---
title: 'Protección de una aplicación hospedada :::no-loc(Blazor WebAssembly)::: de ASP.NET Core con :::no-loc(Identity)::: Server'
author: guardrex
description: 'Obtenga información sobre cómo proteger una aplicación :::no-loc(Blazor WebAssembly)::: hospedada de ASP.NET Core con :::no-loc(Identity)::: Server.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: a6fd005e19f532089ac1a1914756fb03eabb24c4
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690480"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a><span data-ttu-id="c1d86-103">Protección de una aplicación hospedada :::no-loc(Blazor WebAssembly)::: de ASP.NET Core con :::no-loc(Identity)::: Server</span><span class="sxs-lookup"><span data-stu-id="c1d86-103">Secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: hosted app with :::no-loc(Identity)::: Server</span></span>

<span data-ttu-id="c1d86-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c1d86-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c1d86-105">En este artículo se explica cómo crear una [aplicación :::no-loc(Blazor WebAssembly)::: hospedada](xref:blazor/hosting-models#blazor-webassembly) que usa [:::no-loc(Identity):::Server](https://identityserver.io/) para autenticar usuarios y llamadas API.</span><span class="sxs-lookup"><span data-stu-id="c1d86-105">This article explains how to create a [hosted :::no-loc(Blazor WebAssembly)::: app](xref:blazor/hosting-models#blazor-webassembly) that uses [:::no-loc(Identity):::Server](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="c1d86-106">Para configurar una aplicación :::no-loc(Blazor WebAssembly)::: independiente u hospedada para que use una instancia de :::no-loc(Identity)::: Server externa existente, siga las instrucciones de <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span><span class="sxs-lookup"><span data-stu-id="c1d86-106">To configure a standalone or hosted :::no-loc(Blazor WebAssembly)::: app to use an existing, external :::no-loc(Identity)::: Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c1d86-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1d86-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1d86-108">Para crear un nuevo proyecto de :::no-loc(Blazor WebAssembly)::: con un mecanismo de autenticación:</span><span class="sxs-lookup"><span data-stu-id="c1d86-108">To create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism:</span></span>

1. <span data-ttu-id="c1d86-109">Después de elegir la plantilla de aplicación de **:::no-loc(Blazor WebAssembly):::** en el cuadro de diálogo **Crear una aplicación web ASP.NET Core** , seleccione **Cambiar** en **Autenticación** .</span><span class="sxs-lookup"><span data-stu-id="c1d86-109">After choosing the **:::no-loc(Blazor WebAssembly)::: App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication** .</span></span>

1. <span data-ttu-id="c1d86-110">Seleccione **Cuentas de usuario individuales** con la opción **Almacenar cuentas de usuario en aplicación** para almacenar usuarios dentro de la aplicación mediante el sistema [:::no-loc(Identity):::](xref:security/authentication/identity) de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1d86-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [:::no-loc(Identity):::](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="c1d86-111">Active la casilla **ASP.NET Core hosted** (Hospedada en ASP.NET Core) en la sección **Avanzado** .</span><span class="sxs-lookup"><span data-stu-id="c1d86-111">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="c1d86-112">Visual Studio Code y CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="c1d86-112">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="c1d86-113">Para crear un nuevo proyecto de :::no-loc(Blazor WebAssembly)::: con un mecanismo de autenticación en una carpeta vacía, especifique el mecanismo de autenticación `Individual` con la opción `-au|--auth` para almacenar los usuarios dentro de la aplicación con el sistema [:::no-loc(Identity):::](xref:security/authentication/identity) de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="c1d86-113">To create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [:::no-loc(Identity):::](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="c1d86-114">Marcador de posición</span><span class="sxs-lookup"><span data-stu-id="c1d86-114">Placeholder</span></span>  | <span data-ttu-id="c1d86-115">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="c1d86-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `:::no-loc(Blazor):::Sample` |

<span data-ttu-id="c1d86-116">La ubicación de salida especificada con la opción `-o|--output` crea una carpeta de proyecto si no existe y se convierte en parte del nombre de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c1d86-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="c1d86-117">Para más información, consulte el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) de la guía de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1d86-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c1d86-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c1d86-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c1d86-119">Para crear un nuevo proyecto de :::no-loc(Blazor WebAssembly)::: con un mecanismo de autenticación:</span><span class="sxs-lookup"><span data-stu-id="c1d86-119">To create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism:</span></span>

1. <span data-ttu-id="c1d86-120">En el paso **Configure your new :::no-loc(Blazor WebAssembly)::: App** (Configurar la nueva aplicación de Blazor Server), seleccione **Individual Authentication (in-app)** (Autenticación individual [en aplicación]) en la lista desplegable **Authentication** (Autenticación).</span><span class="sxs-lookup"><span data-stu-id="c1d86-120">On the **Configure your new :::no-loc(Blazor WebAssembly)::: App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="c1d86-121">La aplicación se crea para usuarios individuales almacenados en la aplicación con ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="c1d86-121">The app is created for individual users stored in the app with ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="c1d86-122">Active la casilla **ASP.NET Core hosted** (Hospedada en ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="c1d86-122">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="c1d86-123">Configuración de la aplicación *`Server`*</span><span class="sxs-lookup"><span data-stu-id="c1d86-123">*`Server`* app configuration</span></span>

<span data-ttu-id="c1d86-124">En las siguientes secciones se describen algunas adiciones al proyecto cuando se incluye compatibilidad con la autenticación.</span><span class="sxs-lookup"><span data-stu-id="c1d86-124">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="c1d86-125">Clase Startup</span><span class="sxs-lookup"><span data-stu-id="c1d86-125">Startup class</span></span>

<span data-ttu-id="c1d86-126">La clase `Startup` tiene las siguientes adiciones.</span><span class="sxs-lookup"><span data-stu-id="c1d86-126">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="c1d86-127">En `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c1d86-127">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="c1d86-128">:::no-loc(ASP.NET Core Identity)::::</span><span class="sxs-lookup"><span data-stu-id="c1d86-128">:::no-loc(ASP.NET Core Identity)::::</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefault:::no-loc(Identity):::<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="c1d86-129">:::no-loc(Identity):::Server con un método auxiliar <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> adicional que configura convenciones de ASP.NET Core predeterminadas por encima de :::no-loc(Identity):::Server:</span><span class="sxs-lookup"><span data-stu-id="c1d86-129">:::no-loc(Identity):::Server with an additional <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of :::no-loc(Identity):::Server:</span></span>

    ```csharp
    services.Add:::no-loc(Identity):::Server()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="c1d86-130">Autenticación con un método auxiliar <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> adicional que configura la aplicación para validar los tokens JWT generados por :::no-loc(Identity):::Server:</span><span class="sxs-lookup"><span data-stu-id="c1d86-130">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> helper method that configures the app to validate JWT tokens produced by :::no-loc(Identity):::Server:</span></span>

    ```csharp
    services.AddAuthentication()
        .Add:::no-loc(Identity):::ServerJwt();
    ```

* <span data-ttu-id="c1d86-131">En `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="c1d86-131">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="c1d86-132">El middleware :::no-loc(Identity):::Server expone los puntos de conexión de OpenID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="c1d86-132">The :::no-loc(Identity):::Server middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.Use:::no-loc(Identity):::Server();
    ```

  * <span data-ttu-id="c1d86-133">El middleware Authentication es responsable de validar las credenciales de solicitud y de configurar el usuario en el contexto de la solicitud:</span><span class="sxs-lookup"><span data-stu-id="c1d86-133">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="c1d86-134">El middleware Authorization habilita capacidades de autorización:</span><span class="sxs-lookup"><span data-stu-id="c1d86-134">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="c1d86-135">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="c1d86-135">AddApiAuthorization</span></span>

<span data-ttu-id="c1d86-136">El método auxiliar <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> configura [:::no-loc(Identity):::Server](https://identityserver.io/) en escenarios de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1d86-136">The <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [:::no-loc(Identity):::Server](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="c1d86-137">:::no-loc(Identity):::Server es un marco eficaz y extensible que sirve para abordar los problemas de seguridad de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="c1d86-137">:::no-loc(Identity):::Server is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="c1d86-138">:::no-loc(Identity):::Server expone las complejidades innecesarias en los escenarios más comunes.</span><span class="sxs-lookup"><span data-stu-id="c1d86-138">:::no-loc(Identity):::Server exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="c1d86-139">En consecuencia, se proporciona un conjunto de convenciones y opciones de configuración que consideramos un buen punto de partida.</span><span class="sxs-lookup"><span data-stu-id="c1d86-139">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="c1d86-140">Cuando sus necesidades de autenticación cambien, tendrá a su disposición toda la eficacia de :::no-loc(Identity):::Server para personalizar la autenticación para adaptarse a los requisitos de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="c1d86-140">Once your authentication needs change, the full power of :::no-loc(Identity):::Server is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="c1d86-141">Add:::no-loc(Identity):::ServerJwt</span><span class="sxs-lookup"><span data-stu-id="c1d86-141">Add:::no-loc(Identity):::ServerJwt</span></span>

<span data-ttu-id="c1d86-142">El método auxiliar <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> configura un esquema de directiva para la aplicación como el controlador de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="c1d86-142">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="c1d86-143">La directiva está configurada para permitir que :::no-loc(Identity)::: controle todas las solicitudes enrutadas a cualquier subruta en el espacio de dirección URL de :::no-loc(Identity)::: `/:::no-loc(Identity):::`.</span><span class="sxs-lookup"><span data-stu-id="c1d86-143">The policy is configured to allow :::no-loc(Identity)::: to handle all requests routed to any subpath in the :::no-loc(Identity)::: URL space `/:::no-loc(Identity):::`.</span></span> <span data-ttu-id="c1d86-144"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> se encarga de todas las demás solicitudes.</span><span class="sxs-lookup"><span data-stu-id="c1d86-144">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="c1d86-145">Este método también hace lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c1d86-145">Additionally, this method:</span></span>

* <span data-ttu-id="c1d86-146">Registra un recurso de API `{APPLICATION NAME}API` con :::no-loc(Identity):::Server con un ámbito predeterminado de `{APPLICATION NAME}API`.</span><span class="sxs-lookup"><span data-stu-id="c1d86-146">Registers an `{APPLICATION NAME}API` API resource with :::no-loc(Identity):::Server with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="c1d86-147">Configura el middleware de token de portador de JWT para validar los tokens emitidos por :::no-loc(Identity):::Server para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c1d86-147">Configures the JWT Bearer Token Middleware to validate tokens issued by :::no-loc(Identity):::Server for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="c1d86-148">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="c1d86-148">WeatherForecastController</span></span>

<span data-ttu-id="c1d86-149">En `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), el atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) se aplica a la clase.</span><span class="sxs-lookup"><span data-stu-id="c1d86-149">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="c1d86-150">Este atributo señala que, para acceder al recurso, el usuario debe estar autorizado en función de la directiva predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c1d86-150">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="c1d86-151">La directiva de autorización predeterminada está configurada para usar el esquema de autenticación predeterminado, que <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> configura.</span><span class="sxs-lookup"><span data-stu-id="c1d86-151">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A>.</span></span> <span data-ttu-id="c1d86-152">El método auxiliar configura <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> como el controlador predeterminado de las solicitudes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c1d86-152">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="c1d86-153">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="c1d86-153">ApplicationDbContext</span></span>

<span data-ttu-id="c1d86-154">En `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extiende <xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiAuthorizationDbContext%601> para incluir el esquema de :::no-loc(Identity):::Server.</span><span class="sxs-lookup"><span data-stu-id="c1d86-154">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiAuthorizationDbContext%601> to include the schema for :::no-loc(Identity):::Server.</span></span> <span data-ttu-id="c1d86-155">La clase <xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiAuthorizationDbContext%601> se deriva de la clase <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::DbContext>.</span><span class="sxs-lookup"><span data-stu-id="c1d86-155"><xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::DbContext>.</span></span>

<span data-ttu-id="c1d86-156">Para obtener el control total del esquema de la base de datos, herede de una de las clases <xref:Microsoft.EntityFrameworkCore.DbContext> de :::no-loc(Identity)::: disponibles y configure el contexto para incluir el esquema de :::no-loc(Identity)::: llamando a `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` en el método <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="c1d86-156">To gain full control of the database schema, inherit from one of the available :::no-loc(Identity)::: <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the :::no-loc(Identity)::: schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="c1d86-157">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="c1d86-157">OidcConfigurationController</span></span>

<span data-ttu-id="c1d86-158">En `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), el punto de conexión del cliente se aprovisiona para proporcionar parámetros de OIDC.</span><span class="sxs-lookup"><span data-stu-id="c1d86-158">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="c1d86-159">Configuración de la aplicación</span><span class="sxs-lookup"><span data-stu-id="c1d86-159">App settings</span></span>

<span data-ttu-id="c1d86-160">En el archivo de configuración de la aplicación (`appsettings.json`), en la raíz del proyecto, la sección `:::no-loc(Identity):::Server` describe la lista de clientes configurados.</span><span class="sxs-lookup"><span data-stu-id="c1d86-160">In the app settings file (`appsettings.json`) at the project root, the `:::no-loc(Identity):::Server` section describes the list of configured clients.</span></span> <span data-ttu-id="c1d86-161">En el siguiente ejemplo hay un solo cliente,</span><span class="sxs-lookup"><span data-stu-id="c1d86-161">In the following example, there's a single client.</span></span> <span data-ttu-id="c1d86-162">cuyo nombre corresponde al nombre de la aplicación y se asigna por convención al parámetro `ClientId` de OAuth.</span><span class="sxs-lookup"><span data-stu-id="c1d86-162">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="c1d86-163">El perfil señala el tipo de aplicación que se está configurando.</span><span class="sxs-lookup"><span data-stu-id="c1d86-163">The profile indicates the app type being configured.</span></span> <span data-ttu-id="c1d86-164">El perfil se usa internamente para controlar las convenciones que simplifican el proceso de configuración del servidor.</span><span class="sxs-lookup"><span data-stu-id="c1d86-164">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
":::no-loc(Identity):::Server": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": ":::no-loc(Identity):::ServerSPA"
    }
  }
}
```

<span data-ttu-id="c1d86-165">El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `:::no-loc(Blazor):::Sample.Client`).</span><span class="sxs-lookup"><span data-stu-id="c1d86-165">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `:::no-loc(Blazor):::Sample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="c1d86-166">Configuración de la aplicación *`Client`*</span><span class="sxs-lookup"><span data-stu-id="c1d86-166">*`Client`* app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="c1d86-167">Paquete de autenticación</span><span class="sxs-lookup"><span data-stu-id="c1d86-167">Authentication package</span></span>

<span data-ttu-id="c1d86-168">Cuando una aplicación se crea para usar cuentas de usuario individuales (`Individual`), dicha aplicación recibe automáticamente una referencia de paquete del paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) en el archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c1d86-168">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="c1d86-169">El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar usuarios y a obtener tokens para llamar a API protegidas.</span><span class="sxs-lookup"><span data-stu-id="c1d86-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="c1d86-170">Si agrega autenticación a una aplicación, agregue el paquete manualmente al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="c1d86-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="c1d86-171">En el caso del marcador de posición `{VERSION}`, la versión estable más reciente del paquete que coincide con la versión del marco compartida de la aplicación se puede encontrar en el **historial de versiones** del paquete en [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="c1d86-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

### <a name="httpclient-configuration"></a><span data-ttu-id="c1d86-172">Configuración de `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="c1d86-172">`HttpClient` configuration</span></span>

<span data-ttu-id="c1d86-173">En `Program.Main` (`Program.cs`), hay configurada una instancia de <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) con nombre para proporcionar instancias de <xref:System.Net.Http.HttpClient> que incluyen tokens de acceso al realizar solicitudes a la API de servidor:</span><span class="sxs-lookup"><span data-stu-id="c1d86-173">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="c1d86-174">Si va a configurar una aplicación :::no-loc(Blazor WebAssembly)::: para usar una instancia de :::no-loc(Identity)::: Server existente que no forma parte de una solución hospedada por :::no-loc(Blazor):::, cambie el registro de la dirección base <xref:System.Net.Http.HttpClient> de <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) a la dirección URL del punto de conexión de autorización de API de la aplicación de servidor.</span><span class="sxs-lookup"><span data-stu-id="c1d86-174">If you're configuring a :::no-loc(Blazor WebAssembly)::: app to use an existing :::no-loc(Identity)::: Server instance that isn't part of a hosted :::no-loc(Blazor)::: solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="c1d86-175">Compatibilidad con autorización de API</span><span class="sxs-lookup"><span data-stu-id="c1d86-175">API authorization support</span></span>

<span data-ttu-id="c1d86-176">La compatibilidad para autenticar usuarios se incluye en el contenedor de servicios con el método de extensión proporcionado dentro del paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="c1d86-176">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="c1d86-177">Este método configura los servicios necesarios para que la aplicación interactúe con el sistema de autorización existente.</span><span class="sxs-lookup"><span data-stu-id="c1d86-177">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="c1d86-178">La configuración de la aplicación se carga por convención de forma predeterminada desde `_configuration/{client-id}`.</span><span class="sxs-lookup"><span data-stu-id="c1d86-178">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="c1d86-179">Por convención, el identificador de cliente se establece en el nombre de ensamblado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c1d86-179">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="c1d86-180">Esta dirección URL se puede cambiar para que apunte a un punto de conexión aparte llamando a la sobrecarga con opciones.</span><span class="sxs-lookup"><span data-stu-id="c1d86-180">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="c1d86-181">Archivo Imports</span><span class="sxs-lookup"><span data-stu-id="c1d86-181">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="c1d86-182">Página de índice</span><span class="sxs-lookup"><span data-stu-id="c1d86-182">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="c1d86-183">Componente App</span><span class="sxs-lookup"><span data-stu-id="c1d86-183">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="c1d86-184">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="c1d86-184">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="c1d86-185">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="c1d86-185">LoginDisplay component</span></span>

<span data-ttu-id="c1d86-186">El componente `LoginDisplay` (`Shared/LoginDisplay.razor`) se representa en el componente `MainLayout` (`Shared/MainLayout.razor`) y administra los siguientes comportamientos:</span><span class="sxs-lookup"><span data-stu-id="c1d86-186">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="c1d86-187">En el caso de los usuarios autenticados:</span><span class="sxs-lookup"><span data-stu-id="c1d86-187">For authenticated users:</span></span>
  * <span data-ttu-id="c1d86-188">Muestra el nombre de usuario actual.</span><span class="sxs-lookup"><span data-stu-id="c1d86-188">Displays the current user name.</span></span>
  * <span data-ttu-id="c1d86-189">Proporciona un vínculo a la página de perfil de usuario en :::no-loc(ASP.NET Core Identity):::.</span><span class="sxs-lookup"><span data-stu-id="c1d86-189">Offers a link to the user profile page in :::no-loc(ASP.NET Core Identity):::.</span></span>
  * <span data-ttu-id="c1d86-190">Proporciona un botón para cerrar la sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c1d86-190">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="c1d86-191">En el caso de los usuarios anónimos:</span><span class="sxs-lookup"><span data-stu-id="c1d86-191">For anonymous users:</span></span>
  * <span data-ttu-id="c1d86-192">Ofrece la opción de registrarse.</span><span class="sxs-lookup"><span data-stu-id="c1d86-192">Offers the option to register.</span></span>
  * <span data-ttu-id="c1d86-193">Ofrece la opción de iniciar sesión.</span><span class="sxs-lookup"><span data-stu-id="c1d86-193">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.:::no-loc(Identity):::.Name!</a>
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

### <a name="authentication-component"></a><span data-ttu-id="c1d86-194">Componente Authentication</span><span class="sxs-lookup"><span data-stu-id="c1d86-194">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="c1d86-195">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="c1d86-195">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="c1d86-196">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="c1d86-196">Run the app</span></span>

<span data-ttu-id="c1d86-197">Ejecute la aplicación desde el proyecto de servidor.</span><span class="sxs-lookup"><span data-stu-id="c1d86-197">Run the app from the Server project.</span></span> <span data-ttu-id="c1d86-198">Al usar Visual Studio, puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c1d86-198">When using Visual Studio, either:</span></span>

* <span data-ttu-id="c1d86-199">Establezca la lista desplegable **Proyectos de inicio** de la barra de herramientas en la *aplicación de API de servidor* y seleccione el botón **Ejecutar** .</span><span class="sxs-lookup"><span data-stu-id="c1d86-199">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="c1d86-200">Seleccione el proyecto de servidor en el **Explorador de soluciones** y seleccione el botón **Ejecutar** de la barra de herramientas, o bien inicie la aplicación desde el menú **Depurar** .</span><span class="sxs-lookup"><span data-stu-id="c1d86-200">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="c1d86-201">Notificaciones name y role con autorización de API</span><span class="sxs-lookup"><span data-stu-id="c1d86-201">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="c1d86-202">Fábrica de usuario personalizada</span><span class="sxs-lookup"><span data-stu-id="c1d86-202">Custom user factory</span></span>

<span data-ttu-id="c1d86-203">En la aplicación *`Client`* , cree una fábrica de usuario personalizada.</span><span class="sxs-lookup"><span data-stu-id="c1d86-203">In the *`Client`* app, create a custom user factory.</span></span> <span data-ttu-id="c1d86-204">:::no-loc(Identity)::: Server envía varios roles como una matriz JSON en una sola notificación `role`.</span><span class="sxs-lookup"><span data-stu-id="c1d86-204">:::no-loc(Identity)::: Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="c1d86-205">Se envía un único rol como un valor de cadena en la notificación.</span><span class="sxs-lookup"><span data-stu-id="c1d86-205">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="c1d86-206">La fábrica crea una notificación `role` individual por cada uno de los roles del usuario.</span><span class="sxs-lookup"><span data-stu-id="c1d86-206">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="c1d86-207">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="c1d86-207">`CustomUserFactory.cs`:</span></span>

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

        if (user.:::no-loc(Identity):::.IsAuthenticated)
        {
            var identity = (Claims:::no-loc(Identity):::)user.:::no-loc(Identity):::;
            var roleClaims = identity.FindAll(identity.RoleClaimType).ToArray();

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

<span data-ttu-id="c1d86-208">En la aplicación *`Client`* , registre la fábrica en `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="c1d86-208">In the *`Client`* app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="c1d86-209">En la aplicación *`Server`* , llame a <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Builder.AddRoles*> en el generador de :::no-loc(Identity):::, que agrega servicios relativos a roles:</span><span class="sxs-lookup"><span data-stu-id="c1d86-209">In the *`Server`* app, call <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Builder.AddRoles*> on the :::no-loc(Identity)::: builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(Identity):::;

...

services.AddDefault:::no-loc(Identity):::<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<:::no-loc(Identity):::Role>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a><span data-ttu-id="c1d86-210">Configuración de :::no-loc(Identity)::: Server</span><span class="sxs-lookup"><span data-stu-id="c1d86-210">Configure :::no-loc(Identity)::: Server</span></span>

<span data-ttu-id="c1d86-211">Siga **uno** de estos procedimientos:</span><span class="sxs-lookup"><span data-stu-id="c1d86-211">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="c1d86-212">Opciones de autorización de API</span><span class="sxs-lookup"><span data-stu-id="c1d86-212">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="c1d86-213">Servicio de perfil</span><span class="sxs-lookup"><span data-stu-id="c1d86-213">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="c1d86-214">Opciones de autorización de API</span><span class="sxs-lookup"><span data-stu-id="c1d86-214">API authorization options</span></span>

<span data-ttu-id="c1d86-215">En la aplicación *`Server`* :</span><span class="sxs-lookup"><span data-stu-id="c1d86-215">In the *`Server`* app:</span></span>

* <span data-ttu-id="c1d86-216">Configure :::no-loc(Identity)::: Server para colocar las notificaciones `name` y `role` en el token de identificador y el token de acceso.</span><span class="sxs-lookup"><span data-stu-id="c1d86-216">Configure :::no-loc(Identity)::: Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="c1d86-217">Evite la asignación predeterminada de roles en el controlador de token JWT.</span><span class="sxs-lookup"><span data-stu-id="c1d86-217">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.:::no-loc(Identity):::Model.Tokens.Jwt;
using System.Linq;

...

services.Add:::no-loc(Identity):::Server()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.:::no-loc(Identity):::Resources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.:::no-loc(Identity):::Resources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="c1d86-218">Servicio de perfil</span><span class="sxs-lookup"><span data-stu-id="c1d86-218">Profile Service</span></span>

<span data-ttu-id="c1d86-219">En la aplicación *`Server`* , cree una implementación `ProfileService`.</span><span class="sxs-lookup"><span data-stu-id="c1d86-219">In the *`Server`* app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="c1d86-220">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="c1d86-220">`ProfileService.cs`:</span></span>

```csharp
using :::no-loc(Identity):::Model;
using :::no-loc(Identity):::Server4.Models;
using :::no-loc(Identity):::Server4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public async Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        await Task.CompletedTask;
    }

    public async Task IsActiveAsync(IsActiveContext context)
    {
        await Task.CompletedTask;
    }
}
```

<span data-ttu-id="c1d86-221">En la aplicación *`Server`* , registre el servicio de perfil en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c1d86-221">In the *`Server`* app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using :::no-loc(Identity):::Server4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="c1d86-222">Uso de mecanismos de autorización</span><span class="sxs-lookup"><span data-stu-id="c1d86-222">Use authorization mechanisms</span></span>

<span data-ttu-id="c1d86-223">En la aplicación *`Client`* , los métodos de autorización de componentes son funcionales en este momento.</span><span class="sxs-lookup"><span data-stu-id="c1d86-223">In the *`Client`* app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="c1d86-224">Cualquiera de los mecanismos de autorización de los componentes puede usar un rol para autorizar al usuario:</span><span class="sxs-lookup"><span data-stu-id="c1d86-224">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="c1d86-225">[Componente `AuthorizeView`](xref:blazor/security/index#authorizeview-component) (ejemplo: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="c1d86-225">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="c1d86-226">[Directiva de atributo `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (ejemplo: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="c1d86-226">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="c1d86-227">[Lógica de procedimientos](xref:blazor/security/index#procedural-logic) (ejemplo: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="c1d86-227">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="c1d86-228">Se admiten pruebas de rol múltiple:</span><span class="sxs-lookup"><span data-stu-id="c1d86-228">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="c1d86-229">`User.:::no-loc(Identity):::.Name` se rellena en la aplicación *`Client`* con el nombre de usuario del usuario, que suele ser su dirección de correo electrónico de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="c1d86-229">`User.:::no-loc(Identity):::.Name` is populated in the *`Client`* app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain"></a><span data-ttu-id="c1d86-230">Host en Azure App Service con un dominio personalizado</span><span class="sxs-lookup"><span data-stu-id="c1d86-230">Host in Azure App Service with a custom domain</span></span>

<span data-ttu-id="c1d86-231">En la siguiente guía se explica cómo implementar una aplicación hospedada de :::no-loc(Blazor WebAssembly)::: con :::no-loc(Identity)::: Server en [Azure App Service](https://azure.microsoft.com/services/app-service/) con un dominio personalizado.</span><span class="sxs-lookup"><span data-stu-id="c1d86-231">The following guidance explains how to deploy a hosted :::no-loc(Blazor WebAssembly)::: app with :::no-loc(Identity)::: Server to [Azure App Service](https://azure.microsoft.com/services/app-service/) with a custom domain.</span></span>

<span data-ttu-id="c1d86-232">En este escenario de hospedaje, **no** use el mismo certificado para la [clave de firma de tokens de :::no-loc(Identity)::: Server](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) y la comunicación segura HTTPS del sitio con exploradores:</span><span class="sxs-lookup"><span data-stu-id="c1d86-232">For this hosting scenario, do **not** use the same certificate for [:::no-loc(Identity)::: Server's token signing key](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) and the site's HTTPS secure communication with browsers:</span></span>

* <span data-ttu-id="c1d86-233">Usar certificados diferentes para estos dos requisitos es una buena práctica de seguridad porque aísla claves privadas para cada propósito.</span><span class="sxs-lookup"><span data-stu-id="c1d86-233">Using different certificates for these two requirements is a good security practice because it isolates private keys for each purpose.</span></span>
* <span data-ttu-id="c1d86-234">Los certificados TLS para la comunicación con los exploradores se administran de forma independiente sin afectar a la firma de tokens de :::no-loc(Identity)::: Server.</span><span class="sxs-lookup"><span data-stu-id="c1d86-234">TLS certificates for communication with browsers is managed independently without affecting :::no-loc(Identity)::: Server's token signing.</span></span>
* <span data-ttu-id="c1d86-235">Cuando [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) proporciona un certificado a una aplicación de App Service para el enlace de dominio personalizado, :::no-loc(Identity)::: Server no puede obtener el mismo certificado de Azure Key Vault para la firma de tokens.</span><span class="sxs-lookup"><span data-stu-id="c1d86-235">When [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) supplies a certificate to an App Service app for custom domain binding, :::no-loc(Identity)::: Server can't obtain the same certificate from Azure Key Vault for token signing.</span></span> <span data-ttu-id="c1d86-236">Aunque es posible configurar :::no-loc(Identity)::: Server para que use el mismo certificado TLS desde una ruta de acceso física, la colocación de los certificados de seguridad en el control de código fuente es una **práctica inadecuada y debe evitarse en la mayoría de los casos** .</span><span class="sxs-lookup"><span data-stu-id="c1d86-236">Although configuring :::no-loc(Identity)::: Server to use the same TLS certificate from a physical path is possible, placing security certificates into source control is a **poor practice and should be avoided in most scenarios** .</span></span>

<span data-ttu-id="c1d86-237">En la siguiente guía, se crea un certificado autofirmado en Azure Key Vault únicamente para la firma de tokens de :::no-loc(Identity)::: Server.</span><span class="sxs-lookup"><span data-stu-id="c1d86-237">In the following guidance, a self-signed certificate is created in Azure Key Vault solely for :::no-loc(Identity)::: Server token signing.</span></span> <span data-ttu-id="c1d86-238">La configuración de :::no-loc(Identity)::: Server usa el certificado del almacén de claves mediante el almacén de certificados `My` > `CurrentUser` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c1d86-238">The :::no-loc(Identity)::: Server configuration uses the key vault certificate via the app's `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="c1d86-239">Otros certificados que se usan para el tráfico HTTPS con dominios personalizados se crean y se configuran de forma independiente del certificado de firma de :::no-loc(Identity)::: Server.</span><span class="sxs-lookup"><span data-stu-id="c1d86-239">Other certificates used for HTTPS traffic with custom domains are created and configured separately from the :::no-loc(Identity)::: Server signing certificate.</span></span>

<span data-ttu-id="c1d86-240">Para configurar una aplicación, Azure App Service y Azure Key Vault para que hospeden con un dominio personalizado y HTTPS:</span><span class="sxs-lookup"><span data-stu-id="c1d86-240">To configure an app, Azure App Service, and Azure Key Vault to host with a custom domain and HTTPS:</span></span>

1. <span data-ttu-id="c1d86-241">Cree un [plan de App Service](/azure/app-service/overview-hosting-plans) con un nivel de plan de `Basic B1` o superior.</span><span class="sxs-lookup"><span data-stu-id="c1d86-241">Create an [App Service plan](/azure/app-service/overview-hosting-plans) with an plan level of `Basic B1` or higher.</span></span> <span data-ttu-id="c1d86-242">App Service requiere un nivel de servicio `Basic B1` o superior para usar dominios personalizados.</span><span class="sxs-lookup"><span data-stu-id="c1d86-242">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="c1d86-243">Cree un certificado PFX para la comunicación segura del explorador del sitio (protocolo HTTPS) con un nombre común del nombre de dominio completo (FQDN) del sitio que controla la organización (por ejemplo, `www.contoso.com`).</span><span class="sxs-lookup"><span data-stu-id="c1d86-243">Create a PFX certificate for the site's secure browser communication (HTTPS protocol) with a common name of the site's fully qualified domain name (FQDN) that your organization controls (for example, `www.contoso.com`).</span></span> <span data-ttu-id="c1d86-244">Cree el certificado con:</span><span class="sxs-lookup"><span data-stu-id="c1d86-244">Create the certificate with:</span></span>
   * <span data-ttu-id="c1d86-245">Usos de las claves</span><span class="sxs-lookup"><span data-stu-id="c1d86-245">Key uses</span></span>
     * <span data-ttu-id="c1d86-246">Validación de firma digital (`digitalSignature`)</span><span class="sxs-lookup"><span data-stu-id="c1d86-246">Digital signature validation (`digitalSignature`)</span></span>
     * <span data-ttu-id="c1d86-247">Cifrado de claves (`keyEncipherment`)</span><span class="sxs-lookup"><span data-stu-id="c1d86-247">Key encipherment (`keyEncipherment`)</span></span>
   * <span data-ttu-id="c1d86-248">Usos mejorados/extendidos de las claves</span><span class="sxs-lookup"><span data-stu-id="c1d86-248">Enhanced/extended key uses</span></span>
     * <span data-ttu-id="c1d86-249">Autenticación de cliente (1.3.6.1.5.5.7.3.2)</span><span class="sxs-lookup"><span data-stu-id="c1d86-249">Client Authentication (1.3.6.1.5.5.7.3.2)</span></span>
     * <span data-ttu-id="c1d86-250">Autenticación del servidor (1.3.6.1.5.5.7.3.1)</span><span class="sxs-lookup"><span data-stu-id="c1d86-250">Server Authentication (1.3.6.1.5.5.7.3.1)</span></span>

   <span data-ttu-id="c1d86-251">Para crear el certificado, use uno de los métodos siguientes, o cualquier otra herramienta o servicio en línea adecuado:</span><span class="sxs-lookup"><span data-stu-id="c1d86-251">To create the certificate, use one of the following approaches or any other suitable tool or online service:</span></span>

   * [<span data-ttu-id="c1d86-252">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="c1d86-252">Azure Key Vault</span></span>](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [<span data-ttu-id="c1d86-253">MakeCert en Windows</span><span class="sxs-lookup"><span data-stu-id="c1d86-253">MakeCert on Windows</span></span>](/windows/desktop/seccrypto/makecert)
   * [<span data-ttu-id="c1d86-254">OpenSSL</span><span class="sxs-lookup"><span data-stu-id="c1d86-254">OpenSSL</span></span>](https://www.openssl.org)

   <span data-ttu-id="c1d86-255">Anote la contraseña, que se usará más adelante para importar el certificado en Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="c1d86-255">Make note of the password, which is used later to import the certificate into Azure Key Vault.</span></span>

   <span data-ttu-id="c1d86-256">Para más información sobre los certificados de Azure Key Vault, consulte [Azure Key Vault: Certificados](/azure/key-vault/certificates/).</span><span class="sxs-lookup"><span data-stu-id="c1d86-256">For more information on Azure Key Vault certificates, see [Azure Key Vault: Certificates](/azure/key-vault/certificates/).</span></span>
1. <span data-ttu-id="c1d86-257">Cree una nueva instancia de Azure Key Vault o use un almacén de claves existente en su suscripción de Azure.</span><span class="sxs-lookup"><span data-stu-id="c1d86-257">Create a new Azure Key Vault or use an existing key vault in your Azure subscription.</span></span>
1. <span data-ttu-id="c1d86-258">En el área **Certificados** del almacén de claves, importe el certificado del sitio PFX.</span><span class="sxs-lookup"><span data-stu-id="c1d86-258">In the key vault's **Certificates** area, import the PFX site certificate.</span></span> <span data-ttu-id="c1d86-259">Registre la huella digital del certificado, que se usará en la configuración de la aplicación más adelante.</span><span class="sxs-lookup"><span data-stu-id="c1d86-259">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="c1d86-260">En Azure Key Vault, genere un nuevo certificado autofirmado para la firma de tokens de :::no-loc(Identity)::: Server.</span><span class="sxs-lookup"><span data-stu-id="c1d86-260">In Azure Key Vault, generate a new self-signed certificate for :::no-loc(Identity)::: Server token signing.</span></span> <span data-ttu-id="c1d86-261">Asigne al certificado un **Nombre de certificado** y un **Firmante** .</span><span class="sxs-lookup"><span data-stu-id="c1d86-261">Give the certificate a **Certificate Name** and **Subject** .</span></span> <span data-ttu-id="c1d86-262">El **Firmante** se especifica como `CN={COMMON NAME}`, donde el marcador de posición `{COMMON NAME}` es el nombre común del certificado.</span><span class="sxs-lookup"><span data-stu-id="c1d86-262">The **Subject** is specified as `CN={COMMON NAME}`, where the `{COMMON NAME}` placeholder is the certificate's common name.</span></span> <span data-ttu-id="c1d86-263">El nombre común puede ser cualquier cadena alfanumérica.</span><span class="sxs-lookup"><span data-stu-id="c1d86-263">The common name can be any alphanumeric string.</span></span> <span data-ttu-id="c1d86-264">Por ejemplo, `CN=:::no-loc(Identity):::ServerSigning` es un **Firmante** válido de certificado.</span><span class="sxs-lookup"><span data-stu-id="c1d86-264">For example, `CN=:::no-loc(Identity):::ServerSigning` is a valid certificate **Subject** .</span></span> <span data-ttu-id="c1d86-265">Use la configuración predeterminada de la **Configuración avanzada de directivas** .</span><span class="sxs-lookup"><span data-stu-id="c1d86-265">Use the default **Advanced Policy Configuration** settings.</span></span> <span data-ttu-id="c1d86-266">Registre la huella digital del certificado, que se usará en la configuración de la aplicación más adelante.</span><span class="sxs-lookup"><span data-stu-id="c1d86-266">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="c1d86-267">Vaya a Azure App Service en Azure Portal y cree un nuevo App Service con la siguiente configuración:</span><span class="sxs-lookup"><span data-stu-id="c1d86-267">Navigate to Azure App Service in the Azure portal and create a new App Service with the following configuration:</span></span>
   * <span data-ttu-id="c1d86-268">**Publicar** establecido en `Code`.</span><span class="sxs-lookup"><span data-stu-id="c1d86-268">**Publish** set to `Code`.</span></span>
   * <span data-ttu-id="c1d86-269">**Pila en tiempo de ejecución** establecido en el tiempo de ejecución de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c1d86-269">**Runtime stack** set to the app's runtime.</span></span>
   * <span data-ttu-id="c1d86-270">Para **SKU y tamaño** , confirme que el nivel de App Service sea `Basic B1` o superior.</span><span class="sxs-lookup"><span data-stu-id="c1d86-270">For **Sku and size** , confirm that the App Service tier is `Basic B1` or higher.</span></span>  <span data-ttu-id="c1d86-271">App Service requiere un nivel de servicio `Basic B1` o superior para usar dominios personalizados.</span><span class="sxs-lookup"><span data-stu-id="c1d86-271">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="c1d86-272">Después de que Azure cree App Service, abra la **Configuración** de la aplicación y agregue una nueva configuración de aplicación que especifique las huellas digitales del certificado que se registraron anteriormente.</span><span class="sxs-lookup"><span data-stu-id="c1d86-272">After Azure creates the App Service, open the app's **Configuration** and add a new application setting specifying the certificate thumbprints recorded earlier.</span></span> <span data-ttu-id="c1d86-273">La clave de configuración de la aplicación es `WEBSITE_LOAD_CERTIFICATES`.</span><span class="sxs-lookup"><span data-stu-id="c1d86-273">The app setting key is `WEBSITE_LOAD_CERTIFICATES`.</span></span> <span data-ttu-id="c1d86-274">Separe las huellas digitales de certificado en el valor de configuración de la aplicación con una coma, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c1d86-274">Separate the certificate thumbprints in the app setting value with a comma, as the following example shows:</span></span>
   * <span data-ttu-id="c1d86-275">Clave: `WEBSITE_LOAD_CERTIFICATES`</span><span class="sxs-lookup"><span data-stu-id="c1d86-275">Key: `WEBSITE_LOAD_CERTIFICATES`</span></span>
   * <span data-ttu-id="c1d86-276">Valor: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span><span class="sxs-lookup"><span data-stu-id="c1d86-276">Value: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span></span>

   <span data-ttu-id="c1d86-277">En Azure Portal, guardar la configuración de la aplicación es un proceso de dos pasos: Guarde la configuración de clave-valor de `WEBSITE_LOAD_CERTIFICATES` y, después, seleccione el botón **Guardar** , en la parte superior de la hoja.</span><span class="sxs-lookup"><span data-stu-id="c1d86-277">In the Azure portal, saving app settings is a two-step process: Save the `WEBSITE_LOAD_CERTIFICATES` key-value setting, then select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="c1d86-278">Seleccione la **configuración de TLS/SSL** de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c1d86-278">Select the app's **TLS/SSL settings** .</span></span> <span data-ttu-id="c1d86-279">Seleccione **Certificados de clave privada (.pfx)** .</span><span class="sxs-lookup"><span data-stu-id="c1d86-279">Select **Private Key Certificates (.pfx)** .</span></span> <span data-ttu-id="c1d86-280">Use el proceso **Importar certificado de Key Vault** dos veces para importar el certificado del sitio para la comunicación HTTPS y el certificado autofirmado del sitio para la firma de tokens de :::no-loc(Identity)::: Server.</span><span class="sxs-lookup"><span data-stu-id="c1d86-280">Use the **Import Key Vault Certificate** process twice to import both the site's certificate for HTTPS communication and the site's self-signed :::no-loc(Identity)::: Server token signing certificate.</span></span>
1. <span data-ttu-id="c1d86-281">Vaya a la hoja **Dominios personalizados** .</span><span class="sxs-lookup"><span data-stu-id="c1d86-281">Navigate to the **Custom domains** blade.</span></span> <span data-ttu-id="c1d86-282">En el sitio web del registrador de dominios, use la **dirección IP** y el **id. de verificación del dominio personalizado** para configurar el dominio.</span><span class="sxs-lookup"><span data-stu-id="c1d86-282">At your domain registrar's website, use the **IP address** and **Custom Domain Verification ID** to configure the domain.</span></span> <span data-ttu-id="c1d86-283">Una configuración de dominio típica incluye:</span><span class="sxs-lookup"><span data-stu-id="c1d86-283">A typical domain configuration includes:</span></span>
   * <span data-ttu-id="c1d86-284">Un **registro A** con un **host** de `@` y un valor de la dirección IP de Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c1d86-284">An **A Record** with a **Host** of `@` and a value of the IP address from the Azure portal.</span></span>
   * <span data-ttu-id="c1d86-285">Un **registro TXT** con un **host** de `asuid` y el valor del id. de verificación generado por Azure y proporcionado por Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c1d86-285">A **TXT Record** with a **Host** of `asuid` and the value of the verification ID generated by Azure and provided by the Azure portal.</span></span>

   <span data-ttu-id="c1d86-286">Asegúrese de guardar correctamente los cambios en el sitio web del registrador de dominios.</span><span class="sxs-lookup"><span data-stu-id="c1d86-286">Make sure that you save the changes at your domain registrar's website correctly.</span></span> <span data-ttu-id="c1d86-287">Algunos sitios web del registrador requieren un proceso de dos pasos para guardar los registros de dominio: Uno o varios registros se guardan individualmente seguidos de la actualización del registro del dominio con un botón independiente.</span><span class="sxs-lookup"><span data-stu-id="c1d86-287">Some registrar websites require a two-step process to save domain records: One or more records are saved individually followed by updating the domain's registration with a separate button.</span></span>
1. <span data-ttu-id="c1d86-288">Vuelva a la hoja **Dominios personalizados** en Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c1d86-288">Return to the **Custom domains** blade in the Azure portal.</span></span> <span data-ttu-id="c1d86-289">Seleccione **Agregar dominio personalizado** .</span><span class="sxs-lookup"><span data-stu-id="c1d86-289">Select **Add custom domain** .</span></span> <span data-ttu-id="c1d86-290">Seleccione la opción **Registro A** .</span><span class="sxs-lookup"><span data-stu-id="c1d86-290">Select the **A Record** option.</span></span> <span data-ttu-id="c1d86-291">Proporcione el dominio y seleccione **Validar** .</span><span class="sxs-lookup"><span data-stu-id="c1d86-291">Provide the domain and select **Validate** .</span></span> <span data-ttu-id="c1d86-292">Si los registros de dominio son correctos y están propagados por Internet, el portal le permitirá seleccionar el botón **Agregar dominio personalizado** .</span><span class="sxs-lookup"><span data-stu-id="c1d86-292">If the domain records are correct and propagated across the Internet, the portal allows you to select the **Add custom domain** button.</span></span>

   <span data-ttu-id="c1d86-293">Los cambios en el registro de dominio pueden tardar unos días en propagarse entre los servidores de nombres de dominio (DNS) de Internet después de que el registrador de dominios los procese.</span><span class="sxs-lookup"><span data-stu-id="c1d86-293">It can take a few days for domain registration changes to propagate across Internet domain name servers (DNS) after they're processed by your domain registrar.</span></span> <span data-ttu-id="c1d86-294">Si los registros de dominio no se actualizan en un plazo de tres días laborables, confirme que los registros se han establecido correctamente con el registrador de dominios y póngase en contacto con el servicio de soporte técnico al cliente.</span><span class="sxs-lookup"><span data-stu-id="c1d86-294">If domain records aren't updated within three business days, confirm the records are correctly set with the domain registrar and contact their customer support.</span></span>
1. <span data-ttu-id="c1d86-295">En la hoja **Dominios personalizados** , el **ESTADO SSL** del dominio está marcado como `Not Secure`.</span><span class="sxs-lookup"><span data-stu-id="c1d86-295">In the **Custom domains** blade, the **SSL STATE** for the domain is marked `Not Secure`.</span></span> <span data-ttu-id="c1d86-296">Seleccione el vínculo **Agregar enlaces** .</span><span class="sxs-lookup"><span data-stu-id="c1d86-296">Select the **Add binding** link.</span></span> <span data-ttu-id="c1d86-297">Seleccione el certificado HTTPS del sitio en el almacén de claves para el enlace de dominio personalizado.</span><span class="sxs-lookup"><span data-stu-id="c1d86-297">Select the site HTTPS certificate from the key vault for the custom domain binding.</span></span>
1. <span data-ttu-id="c1d86-298">En Visual Studio, abra el archivo de configuración de la aplicación del proyecto *Server* (`appsettings.json` o `appsettings.Production.json`).</span><span class="sxs-lookup"><span data-stu-id="c1d86-298">In Visual Studio, open the *Server* project's app settings file (`appsettings.json` or `appsettings.Production.json`).</span></span> <span data-ttu-id="c1d86-299">En la configuración de :::no-loc(Identity)::: Server, agregue la siguiente sección de `Key`.</span><span class="sxs-lookup"><span data-stu-id="c1d86-299">In the :::no-loc(Identity)::: Server configuration, add the following `Key` section.</span></span> <span data-ttu-id="c1d86-300">Especifique el **Firmante** del certificado autofirmado para la clave `Name`.</span><span class="sxs-lookup"><span data-stu-id="c1d86-300">Specify the self-signed certificate **Subject** for the `Name` key.</span></span> <span data-ttu-id="c1d86-301">En el ejemplo siguiente, el nombre común del certificado asignado en el almacén de claves es `:::no-loc(Identity):::ServerSigning`, lo que produce un **Firmante** de `CN=:::no-loc(Identity):::ServerSigning`:</span><span class="sxs-lookup"><span data-stu-id="c1d86-301">In the following example, the certificate's common name assigned in the key vault is `:::no-loc(Identity):::ServerSigning`, which yields a **Subject** of `CN=:::no-loc(Identity):::ServerSigning`:</span></span>

   ```json
   ":::no-loc(Identity):::Server": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=:::no-loc(Identity):::ServerSigning"
     }
   },
   ```

1. <span data-ttu-id="c1d86-302">En Visual Studio, cree un [perfil de publicación](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) de Azure App Service para el proyecto *Server* .</span><span class="sxs-lookup"><span data-stu-id="c1d86-302">In Visual Studio, create an Azure App Service [publish profile](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) for the *Server* project.</span></span> <span data-ttu-id="c1d86-303">En la barra de menús, seleccione: **Compilar** > **Publicar** > **Nuevo** > **Azure** > **Azure App Service** (Windows o Linux).</span><span class="sxs-lookup"><span data-stu-id="c1d86-303">From the menu bar, select: **Build** > **Publish** > **New** > **Azure** > **Azure App Service** (Windows or Linux).</span></span> <span data-ttu-id="c1d86-304">Cuando Visual Studio se conecta a una suscripción de Azure, puede establecer la **Vista** de recursos de Azure por **Tipo de recurso** .</span><span class="sxs-lookup"><span data-stu-id="c1d86-304">When Visual Studio is connected to an Azure subscription, you can set the **View** of Azure resources by **Resource type** .</span></span> <span data-ttu-id="c1d86-305">Navegue dentro de la lista **Aplicación web** para buscar la instancia de App Service de la aplicación y selecciónela.</span><span class="sxs-lookup"><span data-stu-id="c1d86-305">Navigate within the **Web App** list to find the App Service for the app and select it.</span></span> <span data-ttu-id="c1d86-306">Seleccione **Finalizar** .</span><span class="sxs-lookup"><span data-stu-id="c1d86-306">Select **Finish** .</span></span>
1. <span data-ttu-id="c1d86-307">Cuando Visual Studio vuelve a la ventana **Publicar** , se detectan automáticamente el almacén de claves y las dependencias del servicio de base de datos SQL Server.</span><span class="sxs-lookup"><span data-stu-id="c1d86-307">When Visual Studio returns to the **Publish** window, the key vault and SQL Server database service dependencies are automatically detected.</span></span>

   <span data-ttu-id="c1d86-308">No se requiere ningún cambio de configuración en la configuración predeterminada para el servicio del almacén de claves.</span><span class="sxs-lookup"><span data-stu-id="c1d86-308">No configuration changes to the default settings are required for the key vault service.</span></span>

   <span data-ttu-id="c1d86-309">Con fines de prueba, la base de datos [SQLite](https://www.sqlite.org/index.html) local de una aplicación, que está configurada de forma predeterminada por la plantilla de :::no-loc(Blazor):::, puede implementarse con la aplicación sin configuración adicional.</span><span class="sxs-lookup"><span data-stu-id="c1d86-309">For testing purposes, an app's local [SQLite](https://www.sqlite.org/index.html) database, which is configured by default by the :::no-loc(Blazor)::: template, can be deployed with the app without additional configuration.</span></span> <span data-ttu-id="c1d86-310">La configuración de una base de datos diferente en producción para :::no-loc(Identity)::: Server está fuera del ámbito de este artículo.</span><span class="sxs-lookup"><span data-stu-id="c1d86-310">Configuring a different database for :::no-loc(Identity)::: Server in production is beyond the scope of this article.</span></span> <span data-ttu-id="c1d86-311">Para obtener más información, consulte los recursos de base de datos en los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="c1d86-311">For more information, see the database resources in the following documentation sets:</span></span>
   * [<span data-ttu-id="c1d86-312">App Service</span><span class="sxs-lookup"><span data-stu-id="c1d86-312">App Service</span></span>](/azure/app-service/)
   * [<span data-ttu-id="c1d86-313">Servidor de :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="c1d86-313">:::no-loc(Identity)::: Server</span></span>](https://identityserver4.readthedocs.io/en/latest/)

1. <span data-ttu-id="c1d86-314">Seleccione el enlace **Editar** debajo del nombre del perfil de implementación en la parte superior de la ventana.</span><span class="sxs-lookup"><span data-stu-id="c1d86-314">Select the **Edit** link under the deployment profile name at the top of the window.</span></span> <span data-ttu-id="c1d86-315">Cambie la dirección URL de destino a la dirección URL de dominio personalizada del sitio (por ejemplo, `https://www.contoso.com`).</span><span class="sxs-lookup"><span data-stu-id="c1d86-315">Change the destination URL to the site's custom domain URL (for example, `https://www.contoso.com`).</span></span> <span data-ttu-id="c1d86-316">Guarde la configuración</span><span class="sxs-lookup"><span data-stu-id="c1d86-316">Save the settings.</span></span>
1. <span data-ttu-id="c1d86-317">Publique la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c1d86-317">Publish the app.</span></span> <span data-ttu-id="c1d86-318">Visual Studio abre una ventana del explorador y solicita el sitio en su dominio personalizado.</span><span class="sxs-lookup"><span data-stu-id="c1d86-318">Visual Studio opens a browser window and requests the site at its custom domain.</span></span>

<span data-ttu-id="c1d86-319">La documentación de Azure contiene detalles adicionales sobre el uso de servicios de Azure y de dominios personalizados con enlace de TLS en App Service, incluida información sobre el uso de registros CNAME en lugar de registros A.</span><span class="sxs-lookup"><span data-stu-id="c1d86-319">The Azure documentation contains additional detail on using Azure services and custom domains with TLS binding in App Service, including information on using CNAME records instead of A records.</span></span> <span data-ttu-id="c1d86-320">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="c1d86-320">For more information, see the following resources:</span></span>

* [<span data-ttu-id="c1d86-321">Documentación de App Service</span><span class="sxs-lookup"><span data-stu-id="c1d86-321">App Service documentation</span></span>](/azure/app-service/)
* [<span data-ttu-id="c1d86-322">Tutorial: Asignación de un nombre DNS personalizado existente a Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c1d86-322">Tutorial: Map an existing custom DNS name to Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-custom-domain)
* [<span data-ttu-id="c1d86-323">Protección de un nombre DNS personalizado con un enlace TLS/SSL en Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c1d86-323">Secure a custom DNS name with a TLS/SSL binding in Azure App Service</span></span>](/azure/app-service/configure-ssl-bindings)
* [<span data-ttu-id="c1d86-324">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="c1d86-324">Azure Key Vault</span></span>](/azure/key-vault/)

<span data-ttu-id="c1d86-325">Se recomienda usar una nueva ventana del explorador en privado o en incógnito para cada ejecución de prueba de la aplicación después de un cambio en la aplicación, la configuración de la aplicación o los servicios de Azure en Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c1d86-325">We recommend using a new in-private or incognito browser window for each app test run after a change to the app, app configuration, or Azure services in the Azure portal.</span></span> <span data-ttu-id="c1d86-326">Los elementos :::no-loc(cookie)::: persistentes de una serie de pruebas anterior pueden dar errores de autenticación o autorización al probar el sitio, incluso aunque la configuración del sitio sea correcta.</span><span class="sxs-lookup"><span data-stu-id="c1d86-326">Lingering :::no-loc(cookie):::s from a previous test run can result in failed authentication or authorization when testing the site even when the site's configuration is correct.</span></span> <span data-ttu-id="c1d86-327">Para obtener más información sobre cómo configurar Visual Studio para abrir una nueva ventana de explorador en privado o incógnito para cada serie de pruebas, consulte la sección [:::no-loc(Cookie):::s y datos del sitio](#:::no-loc(cookie):::s-and-site-data).</span><span class="sxs-lookup"><span data-stu-id="c1d86-327">For more information on how to configure Visual Studio to open a new in-private or incognito browser window for each test run, see the [:::no-loc(Cookie):::s and site data](#:::no-loc(cookie):::s-and-site-data) section.</span></span>

<span data-ttu-id="c1d86-328">Cuando se cambia la configuración de App Service en Azure Portal, las actualizaciones suelen surtir efecto rápidamente, pero no son instantáneas.</span><span class="sxs-lookup"><span data-stu-id="c1d86-328">When App Service configuration is changed in the Azure portal, the updates generally take effect quickly but aren't instant.</span></span> <span data-ttu-id="c1d86-329">A veces, debe esperar un breve período para que App Service se reinicie y se aplique un cambio de configuración.</span><span class="sxs-lookup"><span data-stu-id="c1d86-329">Sometimes, you must wait a short period for an App Service to restart in order for a configuration change to take effect.</span></span>

<span data-ttu-id="c1d86-330">Si va a solucionar un problema de carga de certificados, ejecute el siguiente comando en un shell de comandos [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) de PowerShell en Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c1d86-330">If troubleshooting a certificate loading problem, execute the following command in an Azure portal [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell command shell.</span></span> <span data-ttu-id="c1d86-331">El comando proporciona una lista de certificados a los que la aplicación puede tener acceso desde el almacén de certificados de `My` > `CurrentUser`.</span><span class="sxs-lookup"><span data-stu-id="c1d86-331">The command provides a list of certificates that the app can access from the `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="c1d86-332">La salida incluye firmantes de certificados y huellas digitales útiles al depurar una aplicación:</span><span class="sxs-lookup"><span data-stu-id="c1d86-332">The output includes certificate subjects and thumbprints useful when debugging an app:</span></span>

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="c1d86-333">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c1d86-333">Additional resources</span></span>

* [<span data-ttu-id="c1d86-334">Implementación en Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c1d86-334">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="c1d86-335">Importación de un certificado de Key Vault (documentación de Azure)</span><span class="sxs-lookup"><span data-stu-id="c1d86-335">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="c1d86-336">Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro</span><span class="sxs-lookup"><span data-stu-id="c1d86-336">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
