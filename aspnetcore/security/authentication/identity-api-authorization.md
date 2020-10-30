---
title: Introducción a la autenticación para aplicaciones de una sola página en ASP.NET Core
author: javiercn
description: 'Se usa :::no-loc(Identity)::: con una aplicación de una sola página hospedada dentro de una aplicación ASP.net Core.'
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 10/27/2020
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
uid: security/authentication/identity/spa
ms.openlocfilehash: 8acc34c88bf62b3da1b920acc7318c94435c100e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051985"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="1f9c5-103">Autenticación y autorización para spa</span><span class="sxs-lookup"><span data-stu-id="1f9c5-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="1f9c5-104">Las plantillas ASP.NET Core 3,1 y posteriores ofrecen autenticación en las aplicaciones de una sola página (Spa) mediante la compatibilidad con la autorización de la API.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-104">The ASP.NET Core 3.1 and later templates offer authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="1f9c5-105">:::no-loc(ASP.NET Core Identity):::para la autenticación y el almacenamiento de usuarios se combina con el [ :::no-loc(Identity)::: servidor](https://identityserver.io/) para implementar OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-105">:::no-loc(ASP.NET Core Identity)::: for authenticating and storing users is combined with [:::no-loc(Identity):::Server](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="1f9c5-106">Se ha agregado un parámetro de autenticación a las plantillas de proyecto **angular** y **reAct** que es similar al parámetro de autenticación en las plantillas de proyecto **aplicación web (controlador de vista de modelos)** (MVC) y **aplicación web** ( :::no-loc(Razor)::: páginas).</span><span class="sxs-lookup"><span data-stu-id="1f9c5-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (:::no-loc(Razor)::: Pages) project templates.</span></span> <span data-ttu-id="1f9c5-107">Los valores de parámetro permitidos son **None** y **individual** .</span><span class="sxs-lookup"><span data-stu-id="1f9c5-107">The allowed parameter values are **None** and **Individual** .</span></span> <span data-ttu-id="1f9c5-108">La plantilla de proyecto **React.js y Redux** no admite el parámetro de autenticación en este momento.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="1f9c5-109">Creación de una aplicación con compatibilidad con la autorización de API</span><span class="sxs-lookup"><span data-stu-id="1f9c5-109">Create an app with API authorization support</span></span>

<span data-ttu-id="1f9c5-110">Se puede usar la autenticación y autorización de usuario con angular y reAct Spa.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="1f9c5-111">Abra un shell de comandos y ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="1f9c5-112">**Angular** :</span><span class="sxs-lookup"><span data-stu-id="1f9c5-112">**Angular** :</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="1f9c5-113">**ReAct** :</span><span class="sxs-lookup"><span data-stu-id="1f9c5-113">**React** :</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="1f9c5-114">El comando anterior crea una aplicación ASP.NET Core con un directorio *ClientApp* que contiene el Spa.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="1f9c5-115">Descripción general de los componentes de ASP.NET Core de la aplicación</span><span class="sxs-lookup"><span data-stu-id="1f9c5-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="1f9c5-116">En las secciones siguientes se describen las adiciones al proyecto cuando se incluye compatibilidad con la autenticación:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="1f9c5-117">Clase Startup</span><span class="sxs-lookup"><span data-stu-id="1f9c5-117">Startup class</span></span>

<span data-ttu-id="1f9c5-118">Los ejemplos de código siguientes se basan en [Microsoft. AspNetCore. ApiAuthorization. :::no-loc(Identity)::: ](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server) Paquete NuGet de servidor.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-118">The following code examples rely on the [Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server) NuGet package.</span></span> <span data-ttu-id="1f9c5-119">En los ejemplos se configura la autenticación y autorización de API mediante los <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> <xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiResourceCollection.Add:::no-loc(Identity):::ServerJwt%2A> métodos de extensión y.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-119">The examples configure API authentication and authorization using the <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> and <xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiResourceCollection.Add:::no-loc(Identity):::ServerJwt%2A> extension methods.</span></span> <span data-ttu-id="1f9c5-120">Los proyectos que usan las plantillas de proyecto reAct o angular SPA con autenticación incluyen una referencia a este paquete.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-120">Projects using the React or Angular SPA project templates with authentication include a reference to this package.</span></span>

<span data-ttu-id="1f9c5-121">La `Startup` clase tiene las siguientes adiciones:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-121">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="1f9c5-122">Dentro del `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-122">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="1f9c5-123">:::no-loc(Identity)::: con la interfaz de usuario predeterminada:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-123">:::no-loc(Identity)::: with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefault:::no-loc(Identity):::<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="1f9c5-124">:::no-loc(Identity):::Servidor con un `AddApiAuthorization` método auxiliar adicional que configura algunas convenciones de ASP.net Core predeterminadas en la parte superior del :::no-loc(Identity)::: servidor:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-124">:::no-loc(Identity):::Server with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of :::no-loc(Identity):::Server:</span></span>

    ```csharp
    services.Add:::no-loc(Identity):::Server()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="1f9c5-125">Autenticación con un método auxiliar `Add:::no-loc(Identity):::ServerJwt` adicional que configura la aplicación para validar los tokens JWT generados por :::no-loc(Identity):::Server:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-125">Authentication with an additional `Add:::no-loc(Identity):::ServerJwt` helper method that configures the app to validate JWT tokens produced by :::no-loc(Identity):::Server:</span></span>

    ```csharp
    services.AddAuthentication()
        .Add:::no-loc(Identity):::ServerJwt();
    ```

* <span data-ttu-id="1f9c5-126">Dentro del `Startup.Configure` método:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-126">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="1f9c5-127">El middleware de autenticación responsable de validar las credenciales de solicitud y establecer el usuario en el contexto de la solicitud:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-127">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="1f9c5-128">El :::no-loc(Identity)::: middleware de servidor que expone los puntos de conexión de OpenID Connect:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-128">The :::no-loc(Identity):::Server middleware that exposes the OpenID Connect endpoints:</span></span>

    ```csharp
    app.Use:::no-loc(Identity):::Server();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="1f9c5-129">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="1f9c5-129">AddApiAuthorization</span></span>

<span data-ttu-id="1f9c5-130">Este método auxiliar configura el :::no-loc(Identity)::: servidor para usar la configuración admitida.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-130">This helper method configures :::no-loc(Identity):::Server to use our supported configuration.</span></span> <span data-ttu-id="1f9c5-131">:::no-loc(Identity):::Server es un marco eficaz y extensible que sirve para abordar los problemas de seguridad de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-131">:::no-loc(Identity):::Server is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="1f9c5-132">Al mismo tiempo, esto expone una complejidad innecesaria para los escenarios más comunes.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-132">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="1f9c5-133">Por lo tanto, se le proporciona un conjunto de convenciones y opciones de configuración que se consideran un buen punto de partida.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-133">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="1f9c5-134">Una vez que cambien las necesidades de autenticación, todo el potencial del :::no-loc(Identity)::: servidor sigue estando disponible para personalizar la autenticación para satisfacer sus necesidades.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-134">Once your authentication needs change, the full power of :::no-loc(Identity):::Server is still available to customize authentication to suit your needs.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="1f9c5-135">Add:::no-loc(Identity):::ServerJwt</span><span class="sxs-lookup"><span data-stu-id="1f9c5-135">Add:::no-loc(Identity):::ServerJwt</span></span>

<span data-ttu-id="1f9c5-136">Este método auxiliar configura un esquema de directiva para la aplicación como el controlador de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-136">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="1f9c5-137">La Directiva está configurada para permitir que :::no-loc(Identity)::: todas las solicitudes se enruten a cualquier subruta en el espacio de la :::no-loc(Identity)::: dirección URL "/ :::no-loc(Identity)::: ".</span><span class="sxs-lookup"><span data-stu-id="1f9c5-137">The policy is configured to let :::no-loc(Identity)::: handle all requests routed to any subpath in the :::no-loc(Identity)::: URL space "/:::no-loc(Identity):::".</span></span> <span data-ttu-id="1f9c5-138">`JwtBearerHandler` se encarga de todas las demás solicitudes.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-138">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="1f9c5-139">Además, este método registra un `<<ApplicationName>>API` recurso de API con :::no-loc(Identity)::: el servidor con un ámbito predeterminado de `<<ApplicationName>>API` y configura el middleware del token de portador de JWT para validar los tokens emitidos por :::no-loc(Identity)::: el servidor para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-139">Additionally, this method registers an `<<ApplicationName>>API` API resource with :::no-loc(Identity):::Server with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by :::no-loc(Identity):::Server for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="1f9c5-140">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="1f9c5-140">WeatherForecastController</span></span>

<span data-ttu-id="1f9c5-141">En el archivo *Controllers\WeatherForecastController.CS* , observe el `[Authorize]` atributo que se aplica a la clase, que indica que el usuario debe ser autorizado en función de la directiva predeterminada para tener acceso al recurso.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-141">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="1f9c5-142">La Directiva de autorización predeterminada está configurada para usar el esquema de autenticación predeterminado, que se configura por `Add:::no-loc(Identity):::ServerJwt` el esquema de directivas mencionado anteriormente, `JwtBearerHandler` configurando el método de este auxiliar como el controlador predeterminado para las solicitudes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-142">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `Add:::no-loc(Identity):::ServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="1f9c5-143">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="1f9c5-143">ApplicationDbContext</span></span>

<span data-ttu-id="1f9c5-144">En el archivo *Data\ApplicationDbContext.CS* , observe lo mismo `DbContext` que se usa en :::no-loc(Identity)::: con la excepción de que extiende `ApiAuthorizationDbContext` (una clase más derivada de `:::no-loc(Identity):::DbContext` ) para incluir el esquema del :::no-loc(Identity)::: servidor.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-144">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in :::no-loc(Identity)::: with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `:::no-loc(Identity):::DbContext`) to include the schema for :::no-loc(Identity):::Server.</span></span>

<span data-ttu-id="1f9c5-145">Para obtener el control total del esquema de la base de datos, herede de una de las :::no-loc(Identity)::: `DbContext` clases disponibles y configure el contexto para incluir el :::no-loc(Identity)::: esquema mediante una llamada a `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` en el `OnModelCreating` método.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-145">To gain full control of the database schema, inherit from one of the available :::no-loc(Identity)::: `DbContext` classes and configure the context to include the :::no-loc(Identity)::: schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="1f9c5-146">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="1f9c5-146">OidcConfigurationController</span></span>

<span data-ttu-id="1f9c5-147">En el archivo *Controllers\OidcConfigurationController.CS* , observe el punto de conexión aprovisionado para atender los parámetros OIDC que el cliente necesita usar.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-147">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### :::no-loc(appsettings.json):::

<span data-ttu-id="1f9c5-148">En el *:::no-loc(appsettings.json):::* archivo de la raíz del proyecto, hay una nueva `:::no-loc(Identity):::Server` sección en la que se describe la lista de clientes configurados.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-148">In the *:::no-loc(appsettings.json):::* file of the project root, there's a new `:::no-loc(Identity):::Server` section that describes the list of configured clients.</span></span> <span data-ttu-id="1f9c5-149">En el siguiente ejemplo hay un solo cliente,</span><span class="sxs-lookup"><span data-stu-id="1f9c5-149">In the following example, there's a single client.</span></span> <span data-ttu-id="1f9c5-150">cuyo nombre corresponde al nombre de la aplicación y se asigna por convención al parámetro `ClientId` de OAuth.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-150">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="1f9c5-151">El perfil señala el tipo de aplicación que se está configurando.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-151">The profile indicates the app type being configured.</span></span> <span data-ttu-id="1f9c5-152">Se usa internamente para controlar las convenciones que simplifican el proceso de configuración del servidor.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-152">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="1f9c5-153">Hay varios perfiles disponibles, como se explica en la sección [perfiles de aplicación](#application-profiles) .</span><span class="sxs-lookup"><span data-stu-id="1f9c5-153">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
":::no-loc(Identity):::Server": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": ":::no-loc(Identity):::ServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="1f9c5-154">appsettings.Development.jsen</span><span class="sxs-lookup"><span data-stu-id="1f9c5-154">appsettings.Development.json</span></span>

<span data-ttu-id="1f9c5-155">En el *appsettings.Development.jsen* el archivo de la raíz del proyecto, hay una `:::no-loc(Identity):::Server` sección que describe la clave que se usa para firmar los tokens.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-155">In the *appsettings.Development.json* file of the project root, there's an `:::no-loc(Identity):::Server` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="1f9c5-156">Al realizar la implementación en producción, es necesario aprovisionar e implementar una clave junto con la aplicación, como se explica en la sección [implementación en producción](#deploy-to-production) .</span><span class="sxs-lookup"><span data-stu-id="1f9c5-156">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
":::no-loc(Identity):::Server": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="1f9c5-157">Descripción general de la aplicación angular</span><span class="sxs-lookup"><span data-stu-id="1f9c5-157">General description of the Angular app</span></span>

<span data-ttu-id="1f9c5-158">La compatibilidad con la autenticación y la autorización de API en la plantilla de angular reside en su propio módulo angular en el directorio *ClientApp\src\api-Authorization*</span><span class="sxs-lookup"><span data-stu-id="1f9c5-158">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="1f9c5-159">El módulo se compone de los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-159">The module is composed of the following elements:</span></span>

* <span data-ttu-id="1f9c5-160">3 componentes:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-160">3 components:</span></span>
  * <span data-ttu-id="1f9c5-161">*login. Component. ts* : controla el flujo de inicio de sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-161">*login.component.ts* : Handles the app's login flow.</span></span>
  * <span data-ttu-id="1f9c5-162">*logout. Component. ts* : controla el flujo de cierre de sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-162">*logout.component.ts* : Handles the app's logout flow.</span></span>
  * <span data-ttu-id="1f9c5-163">*login-menu. Component. ts* : un widget que muestra uno de los siguientes conjuntos de vínculos:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-163">*login-menu.component.ts* : A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="1f9c5-164">Los vínculos de administración de perfiles de usuario y cierre de sesión cuando el usuario está autenticado.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-164">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="1f9c5-165">Los vínculos registro e inicio de sesión cuando el usuario no está autenticado.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-165">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="1f9c5-166">Una protección de ruta `AuthorizeGuard` que se puede Agregar a las rutas y requiere que un usuario se autentique antes de visitar la ruta.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-166">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="1f9c5-167">Un interceptor HTTP `AuthorizeInterceptor` que asocia el token de acceso a las solicitudes HTTP salientes que se dirigen a la API cuando se autentica el usuario.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-167">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="1f9c5-168">Un servicio `AuthorizeService` que controla los detalles de nivel inferior del proceso de autenticación y expone información sobre el usuario autenticado al resto de la aplicación para su consumo.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-168">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="1f9c5-169">Un módulo angular que define rutas asociadas a las partes de autenticación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-169">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="1f9c5-170">Expone el componente de menú Inicio de sesión, el interceptor, la protección y el servicio para su consumo desde el resto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-170">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="1f9c5-171">Descripción general de la aplicación reAct</span><span class="sxs-lookup"><span data-stu-id="1f9c5-171">General description of the React app</span></span>

<span data-ttu-id="1f9c5-172">La compatibilidad con la autenticación y la autorización de API en la plantilla reAct reside en el directorio *ClientApp\src\components\api-Authorization*</span><span class="sxs-lookup"><span data-stu-id="1f9c5-172">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="1f9c5-173">Se compone de los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-173">It's composed of the following elements:</span></span>

* <span data-ttu-id="1f9c5-174">4 componentes:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-174">4 components:</span></span>
  * <span data-ttu-id="1f9c5-175">*Login.js* : controla el flujo de inicio de sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-175">*Login.js* : Handles the app's login flow.</span></span>
  * <span data-ttu-id="1f9c5-176">*Logout.js* : controla el flujo de cierre de sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-176">*Logout.js* : Handles the app's logout flow.</span></span>
  * <span data-ttu-id="1f9c5-177">*LoginMenu.js* : un widget que muestra uno de los siguientes conjuntos de vínculos:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-177">*LoginMenu.js* : A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="1f9c5-178">Los vínculos de administración de perfiles de usuario y cierre de sesión cuando el usuario está autenticado.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-178">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="1f9c5-179">Los vínculos registro e inicio de sesión cuando el usuario no está autenticado.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-179">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="1f9c5-180">*AuthorizeRoute.js* : componente de ruta que requiere que un usuario se autentique antes de representar el componente indicado en el `Component` parámetro.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-180">*AuthorizeRoute.js* : A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="1f9c5-181">Instancia exportada `authService` de la clase `AuthorizeService` que controla los detalles de nivel inferior del proceso de autenticación y expone información sobre el usuario autenticado al resto de la aplicación para su consumo.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-181">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="1f9c5-182">Ahora que ha visto los componentes principales de la solución, puede profundizar en los escenarios individuales de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-182">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="1f9c5-183">Requerir autorización en una nueva API</span><span class="sxs-lookup"><span data-stu-id="1f9c5-183">Require authorization on a new API</span></span>

<span data-ttu-id="1f9c5-184">De forma predeterminada, el sistema está configurado para requerir fácilmente la autorización para las nuevas API.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-184">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="1f9c5-185">Para ello, cree un nuevo controlador y agregue el `[Authorize]` atributo a la clase de controlador o a cualquier acción dentro del controlador.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-185">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="1f9c5-186">Personalización del controlador de autenticación de API</span><span class="sxs-lookup"><span data-stu-id="1f9c5-186">Customize the API authentication handler</span></span>

<span data-ttu-id="1f9c5-187">Para personalizar la configuración del controlador de JWT de la API, configure su <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instancia:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-187">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .Add:::no-loc(Identity):::ServerJwt();

services.Configure<JwtBearerOptions>(
    :::no-loc(Identity):::ServerJwtConstants.:::no-loc(Identity):::ServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

<span data-ttu-id="1f9c5-188">El controlador JWT de la API genera eventos que permiten controlar el proceso de autenticación mediante `JwtBearerEvents` .</span><span class="sxs-lookup"><span data-stu-id="1f9c5-188">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="1f9c5-189">Para proporcionar compatibilidad con la autorización de API, `Add:::no-loc(Identity):::ServerJwt` registra sus propios controladores de eventos.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-189">To provide support for API authorization, `Add:::no-loc(Identity):::ServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="1f9c5-190">Para personalizar el control de un evento, ajuste el controlador de eventos existente con lógica adicional, según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-190">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="1f9c5-191">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-191">For example:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    :::no-loc(Identity):::ServerJwtConstants.:::no-loc(Identity):::ServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

<span data-ttu-id="1f9c5-192">En el código anterior, el `OnTokenValidated` controlador de eventos se reemplaza con una implementación personalizada.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-192">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="1f9c5-193">Esta implementación:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-193">This implementation:</span></span>

1. <span data-ttu-id="1f9c5-194">Llama a la implementación original proporcionada por la compatibilidad con la autorización de la API.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-194">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="1f9c5-195">Ejecute su propia lógica personalizada.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-195">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="1f9c5-196">Protección de una ruta de lado cliente (angular)</span><span class="sxs-lookup"><span data-stu-id="1f9c5-196">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="1f9c5-197">La protección de una ruta del lado cliente se realiza mediante la adición de la protección de autorización a la lista de protecciones que se deben ejecutar al configurar una ruta.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-197">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="1f9c5-198">Por ejemplo, puede ver cómo `fetch-data` se configura la ruta dentro del módulo de angular de la aplicación principal:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-198">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="1f9c5-199">Es importante mencionar que la protección de una ruta no protege el punto de conexión real (lo que requiere que se le `[Authorize]` aplique un atributo), sino que solo impide que el usuario navegue hasta la ruta de la aplicación del lado cliente cuando no se autentica.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-199">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="1f9c5-200">Autenticar solicitudes de API (angular)</span><span class="sxs-lookup"><span data-stu-id="1f9c5-200">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="1f9c5-201">La autenticación de las solicitudes a las API hospedadas junto a la aplicación se realiza automáticamente mediante el uso del interceptor de cliente HTTP definido por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-201">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="1f9c5-202">Proteger una ruta del lado cliente (reAct)</span><span class="sxs-lookup"><span data-stu-id="1f9c5-202">Protect a client-side route (React)</span></span>

<span data-ttu-id="1f9c5-203">Proteja una ruta del lado cliente mediante el `AuthorizeRoute` componente en lugar del componente sin formato `Route` .</span><span class="sxs-lookup"><span data-stu-id="1f9c5-203">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="1f9c5-204">Por ejemplo, observe cómo `fetch-data` se configura la ruta dentro del `App` componente:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-204">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="1f9c5-205">Protección de una ruta:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-205">Protecting a route:</span></span>

* <span data-ttu-id="1f9c5-206">No protege el punto de conexión real (que sigue necesitando un `[Authorize]` atributo aplicado).</span><span class="sxs-lookup"><span data-stu-id="1f9c5-206">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="1f9c5-207">Solo impide que el usuario navegue hasta la ruta del lado cliente determinada cuando no se autentica.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-207">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="1f9c5-208">Autenticar solicitudes de API (reAct)</span><span class="sxs-lookup"><span data-stu-id="1f9c5-208">Authenticate API requests (React)</span></span>

<span data-ttu-id="1f9c5-209">La autenticación de solicitudes con reAct se realiza mediante la importación `authService` en primer lugar de la instancia de `AuthorizeService` .</span><span class="sxs-lookup"><span data-stu-id="1f9c5-209">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="1f9c5-210">El token de acceso se recupera del `authService` y se adjunta a la solicitud, como se muestra a continuación.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-210">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="1f9c5-211">En los componentes de reAct, este trabajo se realiza normalmente en el `componentDidMount` método de ciclo de vida o como resultado de alguna interacción del usuario.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-211">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="1f9c5-212">Importar la authService en el componente</span><span class="sxs-lookup"><span data-stu-id="1f9c5-212">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="1f9c5-213">Recuperar y adjuntar el token de acceso a la respuesta</span><span class="sxs-lookup"><span data-stu-id="1f9c5-213">Retrieve and attach the access token to the response</span></span>

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a><span data-ttu-id="1f9c5-214">Implementación en producción</span><span class="sxs-lookup"><span data-stu-id="1f9c5-214">Deploy to production</span></span>

<span data-ttu-id="1f9c5-215">Para implementar la aplicación en producción, se deben aprovisionar los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-215">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="1f9c5-216">Una base de datos para almacenar las :::no-loc(Identity)::: cuentas de usuario y las :::no-loc(Identity)::: concesiones de servidor.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-216">A database to store the :::no-loc(Identity)::: user accounts and the :::no-loc(Identity):::Server grants.</span></span>
* <span data-ttu-id="1f9c5-217">Un certificado de producción que se utilizará para firmar los tokens.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-217">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="1f9c5-218">No hay requisitos específicos para este certificado; puede ser un certificado autofirmado o un certificado aprovisionado a través de una entidad de certificación.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-218">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="1f9c5-219">Se puede generar a través de herramientas estándar como PowerShell o OpenSSL.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-219">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="1f9c5-220">Se puede instalar en el almacén de certificados en los equipos de destino o implementarse como un archivo *. pfx* con una contraseña segura.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-220">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-a-non-azure-web-hosting-provider"></a><span data-ttu-id="1f9c5-221">Ejemplo: implementación en un proveedor de hospedaje web que no es de Azure</span><span class="sxs-lookup"><span data-stu-id="1f9c5-221">Example: Deploy to a non-Azure web hosting provider</span></span>

<span data-ttu-id="1f9c5-222">En el panel de hospedaje Web, cree o cargue el certificado.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-222">In your web hosting panel, create or load your certificate.</span></span> <span data-ttu-id="1f9c5-223">Después, en el archivo de la aplicación *:::no-loc(appsettings.json):::* , modifique la `:::no-loc(Identity):::Server` sección para incluir los detalles de la clave.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-223">Then in the app's *:::no-loc(appsettings.json):::* file, modify the `:::no-loc(Identity):::Server` section to include the key details.</span></span> <span data-ttu-id="1f9c5-224">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-224">For example:</span></span>

```json
":::no-loc(Identity):::Server": {
  "Key": {
    "Type": "Store",
    "StoreName": "WebHosting",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

<span data-ttu-id="1f9c5-225">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-225">In the preceding example:</span></span>

* <span data-ttu-id="1f9c5-226">`StoreName` representa el nombre del almacén de certificados donde se almacena el certificado.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-226">`StoreName` represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="1f9c5-227">En este caso, apunta al almacén de hospedaje Web.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-227">In this case, it points to the web hosting store.</span></span>
* <span data-ttu-id="1f9c5-228">`StoreLocation` representa dónde se debe cargar el certificado ( `CurrentUser` en este caso).</span><span class="sxs-lookup"><span data-stu-id="1f9c5-228">`StoreLocation` represents where to load the certificate from (`CurrentUser` in this case).</span></span>
* <span data-ttu-id="1f9c5-229">`Name` corresponde al sujeto distintivo del certificado.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-229">`Name` corresponds with the distinguished subject for the certificate.</span></span>

### <a name="example-deploy-to-azure-app-service"></a><span data-ttu-id="1f9c5-230">Ejemplo: implementar en Azure App Service</span><span class="sxs-lookup"><span data-stu-id="1f9c5-230">Example: Deploy to Azure App Service</span></span>

<span data-ttu-id="1f9c5-231">En esta sección se describe la implementación de la aplicación en Azure App Service mediante un certificado almacenado en el almacén de certificados.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-231">This section describes deploying the app to Azure App Service using a certificate stored in the certificate store.</span></span> <span data-ttu-id="1f9c5-232">Para modificar la aplicación para cargar un certificado desde el almacén de certificados, se requiere un plan de servicio de nivel estándar o superior cuando se configura la aplicación en el Azure Portal en un paso posterior.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-232">To modify the app to load a certificate from the certificate store, a Standard tier service plan or better is required when you configure the app in the Azure portal in a later step.</span></span>

<span data-ttu-id="1f9c5-233">En el archivo de la aplicación *:::no-loc(appsettings.json):::* , modifique la `:::no-loc(Identity):::Server` sección para incluir los detalles de la clave:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-233">In the app's *:::no-loc(appsettings.json):::* file, modify the `:::no-loc(Identity):::Server` section to include the key details:</span></span>

```json
":::no-loc(Identity):::Server": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* <span data-ttu-id="1f9c5-234">El nombre del almacén representa el nombre del almacén de certificados donde se almacena el certificado.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-234">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="1f9c5-235">En este caso, apunta al almacén de usuarios personales.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-235">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="1f9c5-236">La ubicación del almacén representa dónde se debe cargar el certificado ( `CurrentUser` o `LocalMachine` ).</span><span class="sxs-lookup"><span data-stu-id="1f9c5-236">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="1f9c5-237">La propiedad Name en el certificado corresponde al sujeto distintivo del certificado.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-237">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="1f9c5-238">Para realizar la implementación en Azure App Service, siga los pasos descritos en [implementación de la aplicación en Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), en el que se explica cómo crear los recursos necesarios de Azure e implementar la aplicación en producción.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-238">To deploy to Azure App Service, follow the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), which explains how to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="1f9c5-239">Después de seguir las instrucciones anteriores, la aplicación se implementa en Azure pero todavía no es funcional.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-239">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="1f9c5-240">El certificado usado por la aplicación debe configurarse en el Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-240">The certificate used by the app must be configured in the Azure portal.</span></span> <span data-ttu-id="1f9c5-241">Busque la huella digital del certificado y siga los pasos descritos en [carga de los certificados](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span><span class="sxs-lookup"><span data-stu-id="1f9c5-241">Locate the thumbprint for the certificate and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="1f9c5-242">Aunque estos pasos mencionan SSL, hay una sección de **certificados privados** en el Azure portal donde puede cargar el certificado aprovisionado para usarlo con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-242">While these steps mention SSL, there's a **Private certificates** section in the Azure portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="1f9c5-243">Después de configurar la aplicación y la configuración de la aplicación en el Azure Portal, reinicie la aplicación en el portal.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-243">After configuring the app and the app's settings in the Azure portal, restart the app in the portal.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="1f9c5-244">Otras opciones de configuración</span><span class="sxs-lookup"><span data-stu-id="1f9c5-244">Other configuration options</span></span>

<span data-ttu-id="1f9c5-245">La compatibilidad con la autorización de API se basa en el :::no-loc(Identity)::: servidor con un conjunto de convenciones, valores predeterminados y mejoras para simplificar la experiencia de spa.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-245">The support for API authorization builds on top of :::no-loc(Identity):::Server with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="1f9c5-246">No es necesario decir que toda la capacidad del :::no-loc(Identity)::: servidor está disponible en segundo plano si las integraciones de ASP.net Core no cubren su escenario.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-246">Needless to say, the full power of :::no-loc(Identity):::Server is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="1f9c5-247">La compatibilidad con ASP.NET Core se centra en las aplicaciones de "primera parte", donde se crean e implementan todas las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-247">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="1f9c5-248">Como tal, no se ofrece soporte técnico para elementos como el consentimiento o la Federación.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-248">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="1f9c5-249">Para esos escenarios, use :::no-loc(Identity)::: el servidor y siga su documentación.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-249">For those scenarios, use :::no-loc(Identity):::Server and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="1f9c5-250">Perfiles de aplicación</span><span class="sxs-lookup"><span data-stu-id="1f9c5-250">Application profiles</span></span>

<span data-ttu-id="1f9c5-251">Los perfiles de aplicación son configuraciones predefinidas para aplicaciones que definen aún más sus parámetros.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-251">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="1f9c5-252">En este momento, se admiten los siguientes perfiles:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-252">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="1f9c5-253">`:::no-loc(Identity):::ServerSPA`: Representa un SPA hospedado junto :::no-loc(Identity)::: al servidor como una unidad única.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-253">`:::no-loc(Identity):::ServerSPA`: Represents a SPA hosted alongside :::no-loc(Identity):::Server as a single unit.</span></span>
  * <span data-ttu-id="1f9c5-254">El `redirect_uri` valor predeterminado es `/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="1f9c5-254">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="1f9c5-255">El `post_logout_redirect_uri` valor predeterminado es `/authentication/logout-callback` .</span><span class="sxs-lookup"><span data-stu-id="1f9c5-255">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="1f9c5-256">El conjunto de ámbitos incluye `openid` , `profile` y cada ámbito definido para las API de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-256">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="1f9c5-257">El conjunto de tipos de respuesta OIDC permitidos es `id_token token` o cada uno de ellos individualmente ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="1f9c5-257">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="1f9c5-258">El modo de respuesta permitido es `fragment` .</span><span class="sxs-lookup"><span data-stu-id="1f9c5-258">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="1f9c5-259">`SPA`: Representa un SPA que no está hospedado con el :::no-loc(Identity)::: servidor.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-259">`SPA`: Represents a SPA that isn't hosted with :::no-loc(Identity):::Server.</span></span>
  * <span data-ttu-id="1f9c5-260">El conjunto de ámbitos incluye `openid` , `profile` y cada ámbito definido para las API de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-260">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="1f9c5-261">El conjunto de tipos de respuesta OIDC permitidos es `id_token token` o cada uno de ellos individualmente ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="1f9c5-261">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="1f9c5-262">El modo de respuesta permitido es `fragment` .</span><span class="sxs-lookup"><span data-stu-id="1f9c5-262">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="1f9c5-263">`:::no-loc(Identity):::ServerJwt`: Representa una API que se hospeda junto con el :::no-loc(Identity)::: servidor.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-263">`:::no-loc(Identity):::ServerJwt`: Represents an API that is hosted alongside with :::no-loc(Identity):::Server.</span></span>
  * <span data-ttu-id="1f9c5-264">La aplicación está configurada para tener un solo ámbito que tenga como valor predeterminado el nombre de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-264">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="1f9c5-265">`API`: Representa una API que no está hospedada en el :::no-loc(Identity)::: servidor.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-265">`API`: Represents an API that isn't hosted with :::no-loc(Identity):::Server.</span></span>
  * <span data-ttu-id="1f9c5-266">La aplicación está configurada para tener un solo ámbito que tenga como valor predeterminado el nombre de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-266">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="1f9c5-267">Configuración a través de AppSettings</span><span class="sxs-lookup"><span data-stu-id="1f9c5-267">Configuration through AppSettings</span></span>

<span data-ttu-id="1f9c5-268">Configure las aplicaciones a través del sistema de configuración agregándolas a la lista de `Clients` o `Resources` .</span><span class="sxs-lookup"><span data-stu-id="1f9c5-268">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="1f9c5-269">Configure cada `redirect_uri` propiedad y del cliente `post_logout_redirect_uri` , tal y como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-269">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

```json
":::no-loc(Identity):::Server": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

<span data-ttu-id="1f9c5-270">Al configurar recursos, puede configurar los ámbitos para el recurso, tal y como se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="1f9c5-270">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

```json
":::no-loc(Identity):::Server": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="1f9c5-271">Configuración mediante código</span><span class="sxs-lookup"><span data-stu-id="1f9c5-271">Configuration through code</span></span>

<span data-ttu-id="1f9c5-272">También puede configurar los clientes y los recursos a través del código mediante una sobrecarga de `AddApiAuthorization` que toma una acción para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="1f9c5-272">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a><span data-ttu-id="1f9c5-273">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="1f9c5-273">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
