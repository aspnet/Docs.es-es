---
title: Introducción a la autenticación para aplicaciones de una sola página en ASP.NET Core
author: javiercn
description: Se usa Identity con una aplicación de una sola página hospedada dentro de una aplicación ASP.net Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity/spa
ms.openlocfilehash: c06f1d4bf772d7726d19163fcdee8c92d4006cd2
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819118"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="0d1ba-103">Autenticación y autorización para spa</span><span class="sxs-lookup"><span data-stu-id="0d1ba-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="0d1ba-104">ASP.NET Core 3,0 o posterior ofrece autenticación en aplicaciones de una sola página (Spa) mediante la compatibilidad con la autorización de la API.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="0d1ba-105">ASP.NET Core Identity para la autenticación y el almacenamiento de usuarios se combinan con el [ Identity servidor](https://identityserver.io/) para implementar OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="0d1ba-106">Se ha agregado un parámetro de autenticación a las plantillas de proyecto **angular** y **reAct** que es similar al parámetro de autenticación en las plantillas de proyecto **aplicación web (controlador de vista de modelos)** (MVC) y **aplicación web** ( Razor páginas).</span><span class="sxs-lookup"><span data-stu-id="0d1ba-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="0d1ba-107">Los valores de parámetro permitidos son **None** y **individual**.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="0d1ba-108">La plantilla de proyecto **React.js y Redux** no admite el parámetro de autenticación en este momento.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="0d1ba-109">Creación de una aplicación con compatibilidad con la autorización de API</span><span class="sxs-lookup"><span data-stu-id="0d1ba-109">Create an app with API authorization support</span></span>

<span data-ttu-id="0d1ba-110">Se puede usar la autenticación y autorización de usuario con angular y reAct Spa.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="0d1ba-111">Abra un shell de comandos y ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="0d1ba-112">**Angular**:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="0d1ba-113">**ReAct**:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="0d1ba-114">El comando anterior crea una aplicación ASP.NET Core con un directorio *ClientApp* que contiene el Spa.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="0d1ba-115">Descripción general de los componentes de ASP.NET Core de la aplicación</span><span class="sxs-lookup"><span data-stu-id="0d1ba-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="0d1ba-116">En las secciones siguientes se describen las adiciones al proyecto cuando se incluye compatibilidad con la autenticación:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="0d1ba-117">Clase Startup</span><span class="sxs-lookup"><span data-stu-id="0d1ba-117">Startup class</span></span>

<span data-ttu-id="0d1ba-118">Los ejemplos de código siguientes se basan en [Microsoft. AspNetCore. ApiAuthorization. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer)Paquete NuGet de servidor.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-118">The following code examples rely on the [Microsoft.AspNetCore.ApiAuthorization.IdentityServer](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet package.</span></span> <span data-ttu-id="0d1ba-119">En los ejemplos se configura la autenticación y autorización de API mediante los <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> métodos de extensión y.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-119">The examples configure API authentication and authorization using the <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> and <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> extension methods.</span></span> <span data-ttu-id="0d1ba-120">Los proyectos que usan las plantillas de proyecto reAct o angular SPA con autenticación incluyen una referencia a este paquete.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-120">Projects using the React or Angular SPA project templates with authentication include a reference to this package.</span></span>

<span data-ttu-id="0d1ba-121">La `Startup` clase tiene las siguientes adiciones:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-121">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="0d1ba-122">Dentro del `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-122">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="0d1ba-123">Identitycon la interfaz de usuario predeterminada:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-123">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="0d1ba-124">IdentityServidor con un `AddApiAuthorization` método auxiliar adicional que configura algunas convenciones de ASP.net Core predeterminadas en la parte superior del Identity servidor:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-124">IdentityServer with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="0d1ba-125">Autenticación con un método auxiliar `AddIdentityServerJwt` adicional que configura la aplicación para validar los tokens JWT generados por IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-125">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="0d1ba-126">Dentro del `Startup.Configure` método:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-126">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="0d1ba-127">El middleware de autenticación responsable de validar las credenciales de solicitud y establecer el usuario en el contexto de la solicitud:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-127">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="0d1ba-128">El Identity middleware de servidor que expone los puntos de conexión de OpenID Connect:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-128">The IdentityServer middleware that exposes the OpenID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="0d1ba-129">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="0d1ba-129">AddApiAuthorization</span></span>

<span data-ttu-id="0d1ba-130">Este método auxiliar configura el Identity servidor para usar la configuración admitida.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-130">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="0d1ba-131">IdentityServer es un marco eficaz y extensible que sirve para abordar los problemas de seguridad de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-131">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="0d1ba-132">Al mismo tiempo, esto expone una complejidad innecesaria para los escenarios más comunes.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-132">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="0d1ba-133">Por lo tanto, se le proporciona un conjunto de convenciones y opciones de configuración que se consideran un buen punto de partida.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-133">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="0d1ba-134">Una vez que cambien las necesidades de autenticación, todo el potencial del Identity servidor sigue estando disponible para personalizar la autenticación para satisfacer sus necesidades.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-134">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="0d1ba-135">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="0d1ba-135">AddIdentityServerJwt</span></span>

<span data-ttu-id="0d1ba-136">Este método auxiliar configura un esquema de directiva para la aplicación como el controlador de autenticación predeterminado.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-136">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="0d1ba-137">La Directiva está configurada para permitir que Identity todas las solicitudes se enruten a cualquier subruta en el espacio de la Identity dirección URL "/ Identity ".</span><span class="sxs-lookup"><span data-stu-id="0d1ba-137">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="0d1ba-138">`JwtBearerHandler` se encarga de todas las demás solicitudes.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-138">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="0d1ba-139">Además, este método registra un `<<ApplicationName>>API` recurso de API con Identity el servidor con un ámbito predeterminado de `<<ApplicationName>>API` y configura el middleware del token de portador de JWT para validar los tokens emitidos por Identity el servidor para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-139">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="0d1ba-140">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="0d1ba-140">WeatherForecastController</span></span>

<span data-ttu-id="0d1ba-141">En el archivo *Controllers\WeatherForecastController.CS* , observe el `[Authorize]` atributo que se aplica a la clase, que indica que el usuario debe ser autorizado en función de la directiva predeterminada para tener acceso al recurso.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-141">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="0d1ba-142">La Directiva de autorización predeterminada está configurada para usar el esquema de autenticación predeterminado, que se configura por `AddIdentityServerJwt` el esquema de directivas mencionado anteriormente, `JwtBearerHandler` configurando el método de este auxiliar como el controlador predeterminado para las solicitudes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-142">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="0d1ba-143">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="0d1ba-143">ApplicationDbContext</span></span>

<span data-ttu-id="0d1ba-144">En el archivo *Data\ApplicationDbContext.CS* , observe lo mismo `DbContext` que se usa en Identity con la excepción de que extiende `ApiAuthorizationDbContext` (una clase más derivada de `IdentityDbContext` ) para incluir el esquema del Identity servidor.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-144">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="0d1ba-145">Para obtener el control total del esquema de la base de datos, herede de una de las Identity `DbContext` clases disponibles y configure el contexto para incluir el Identity esquema mediante una llamada a `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` en el `OnModelCreating` método.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-145">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="0d1ba-146">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="0d1ba-146">OidcConfigurationController</span></span>

<span data-ttu-id="0d1ba-147">En el archivo *Controllers\OidcConfigurationController.CS* , observe el punto de conexión aprovisionado para atender los parámetros OIDC que el cliente necesita usar.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-147">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="0d1ba-148">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="0d1ba-148">appsettings.json</span></span>

<span data-ttu-id="0d1ba-149">En el *appsettings.jsen* el archivo de la raíz del proyecto, hay una nueva `IdentityServer` sección en la que se describe la lista de clientes configurados.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-149">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="0d1ba-150">En el siguiente ejemplo hay un solo cliente,</span><span class="sxs-lookup"><span data-stu-id="0d1ba-150">In the following example, there's a single client.</span></span> <span data-ttu-id="0d1ba-151">cuyo nombre corresponde al nombre de la aplicación y se asigna por convención al parámetro `ClientId` de OAuth.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-151">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="0d1ba-152">El perfil señala el tipo de aplicación que se está configurando.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-152">The profile indicates the app type being configured.</span></span> <span data-ttu-id="0d1ba-153">Se usa internamente para controlar las convenciones que simplifican el proceso de configuración del servidor.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-153">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="0d1ba-154">Hay varios perfiles disponibles, como se explica en la sección [perfiles de aplicación](#application-profiles) .</span><span class="sxs-lookup"><span data-stu-id="0d1ba-154">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="0d1ba-155">appsettings.Development.jsen</span><span class="sxs-lookup"><span data-stu-id="0d1ba-155">appsettings.Development.json</span></span>

<span data-ttu-id="0d1ba-156">En el *appsettings.Development.jsen* el archivo de la raíz del proyecto, hay una `IdentityServer` sección que describe la clave que se usa para firmar los tokens.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-156">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="0d1ba-157">Al realizar la implementación en producción, es necesario aprovisionar e implementar una clave junto con la aplicación, como se explica en la sección [implementación en producción](#deploy-to-production) .</span><span class="sxs-lookup"><span data-stu-id="0d1ba-157">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="0d1ba-158">Descripción general de la aplicación angular</span><span class="sxs-lookup"><span data-stu-id="0d1ba-158">General description of the Angular app</span></span>

<span data-ttu-id="0d1ba-159">La compatibilidad con la autenticación y la autorización de API en la plantilla de angular reside en su propio módulo angular en el directorio *ClientApp\src\api-Authorization*</span><span class="sxs-lookup"><span data-stu-id="0d1ba-159">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="0d1ba-160">El módulo se compone de los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-160">The module is composed of the following elements:</span></span>

* <span data-ttu-id="0d1ba-161">3 componentes:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-161">3 components:</span></span>
  * <span data-ttu-id="0d1ba-162">*login. Component. ts*: controla el flujo de inicio de sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-162">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="0d1ba-163">*logout. Component. ts*: controla el flujo de cierre de sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-163">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="0d1ba-164">*login-menu. Component. ts*: un widget que muestra uno de los siguientes conjuntos de vínculos:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-164">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="0d1ba-165">Los vínculos de administración de perfiles de usuario y cierre de sesión cuando el usuario está autenticado.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-165">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="0d1ba-166">Los vínculos registro e inicio de sesión cuando el usuario no está autenticado.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-166">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="0d1ba-167">Una protección de ruta `AuthorizeGuard` que se puede Agregar a las rutas y requiere que un usuario se autentique antes de visitar la ruta.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-167">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="0d1ba-168">Un interceptor HTTP `AuthorizeInterceptor` que asocia el token de acceso a las solicitudes HTTP salientes que se dirigen a la API cuando se autentica el usuario.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-168">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="0d1ba-169">Un servicio `AuthorizeService` que controla los detalles de nivel inferior del proceso de autenticación y expone información sobre el usuario autenticado al resto de la aplicación para su consumo.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-169">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="0d1ba-170">Un módulo angular que define rutas asociadas a las partes de autenticación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-170">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="0d1ba-171">Expone el componente de menú Inicio de sesión, el interceptor, la protección y el servicio para su consumo desde el resto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-171">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="0d1ba-172">Descripción general de la aplicación reAct</span><span class="sxs-lookup"><span data-stu-id="0d1ba-172">General description of the React app</span></span>

<span data-ttu-id="0d1ba-173">La compatibilidad con la autenticación y la autorización de API en la plantilla reAct reside en el directorio *ClientApp\src\components\api-Authorization*</span><span class="sxs-lookup"><span data-stu-id="0d1ba-173">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="0d1ba-174">Se compone de los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-174">It's composed of the following elements:</span></span>

* <span data-ttu-id="0d1ba-175">4 componentes:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-175">4 components:</span></span>
  * <span data-ttu-id="0d1ba-176">*Login.js*: controla el flujo de inicio de sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-176">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="0d1ba-177">*Logout.js*: controla el flujo de cierre de sesión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-177">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="0d1ba-178">*LoginMenu.js*: un widget que muestra uno de los siguientes conjuntos de vínculos:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-178">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="0d1ba-179">Los vínculos de administración de perfiles de usuario y cierre de sesión cuando el usuario está autenticado.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-179">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="0d1ba-180">Los vínculos registro e inicio de sesión cuando el usuario no está autenticado.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-180">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="0d1ba-181">*AuthorizeRoute.js*: componente de ruta que requiere que un usuario se autentique antes de representar el componente indicado en el `Component` parámetro.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-181">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="0d1ba-182">Instancia exportada `authService` de la clase `AuthorizeService` que controla los detalles de nivel inferior del proceso de autenticación y expone información sobre el usuario autenticado al resto de la aplicación para su consumo.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-182">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="0d1ba-183">Ahora que ha visto los componentes principales de la solución, puede profundizar en los escenarios individuales de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-183">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="0d1ba-184">Requerir autorización en una nueva API</span><span class="sxs-lookup"><span data-stu-id="0d1ba-184">Require authorization on a new API</span></span>

<span data-ttu-id="0d1ba-185">De forma predeterminada, el sistema está configurado para requerir fácilmente la autorización para las nuevas API.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-185">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="0d1ba-186">Para ello, cree un nuevo controlador y agregue el `[Authorize]` atributo a la clase de controlador o a cualquier acción dentro del controlador.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-186">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="0d1ba-187">Personalización del controlador de autenticación de API</span><span class="sxs-lookup"><span data-stu-id="0d1ba-187">Customize the API authentication handler</span></span>

<span data-ttu-id="0d1ba-188">Para personalizar la configuración del controlador de JWT de la API, configure su <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instancia:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-188">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

<span data-ttu-id="0d1ba-189">El controlador JWT de la API genera eventos que permiten controlar el proceso de autenticación mediante `JwtBearerEvents` .</span><span class="sxs-lookup"><span data-stu-id="0d1ba-189">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="0d1ba-190">Para proporcionar compatibilidad con la autorización de API, `AddIdentityServerJwt` registra sus propios controladores de eventos.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-190">To provide support for API authorization, `AddIdentityServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="0d1ba-191">Para personalizar el control de un evento, ajuste el controlador de eventos existente con lógica adicional, según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-191">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="0d1ba-192">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-192">For example:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
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

<span data-ttu-id="0d1ba-193">En el código anterior, el `OnTokenValidated` controlador de eventos se reemplaza con una implementación personalizada.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-193">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="0d1ba-194">Esta implementación:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-194">This implementation:</span></span>

1. <span data-ttu-id="0d1ba-195">Llama a la implementación original proporcionada por la compatibilidad con la autorización de la API.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-195">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="0d1ba-196">Ejecute su propia lógica personalizada.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-196">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="0d1ba-197">Protección de una ruta de lado cliente (angular)</span><span class="sxs-lookup"><span data-stu-id="0d1ba-197">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="0d1ba-198">La protección de una ruta del lado cliente se realiza mediante la adición de la protección de autorización a la lista de protecciones que se deben ejecutar al configurar una ruta.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-198">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="0d1ba-199">Por ejemplo, puede ver cómo `fetch-data` se configura la ruta dentro del módulo de angular de la aplicación principal:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-199">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="0d1ba-200">Es importante mencionar que la protección de una ruta no protege el punto de conexión real (lo que requiere que se le `[Authorize]` aplique un atributo), sino que solo impide que el usuario navegue hasta la ruta de la aplicación del lado cliente cuando no se autentica.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-200">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="0d1ba-201">Autenticar solicitudes de API (angular)</span><span class="sxs-lookup"><span data-stu-id="0d1ba-201">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="0d1ba-202">La autenticación de las solicitudes a las API hospedadas junto a la aplicación se realiza automáticamente mediante el uso del interceptor de cliente HTTP definido por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-202">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="0d1ba-203">Proteger una ruta del lado cliente (reAct)</span><span class="sxs-lookup"><span data-stu-id="0d1ba-203">Protect a client-side route (React)</span></span>

<span data-ttu-id="0d1ba-204">Proteja una ruta del lado cliente mediante el `AuthorizeRoute` componente en lugar del componente sin formato `Route` .</span><span class="sxs-lookup"><span data-stu-id="0d1ba-204">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="0d1ba-205">Por ejemplo, observe cómo `fetch-data` se configura la ruta dentro del `App` componente:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-205">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="0d1ba-206">Protección de una ruta:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-206">Protecting a route:</span></span>

* <span data-ttu-id="0d1ba-207">No protege el punto de conexión real (que sigue necesitando un `[Authorize]` atributo aplicado).</span><span class="sxs-lookup"><span data-stu-id="0d1ba-207">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="0d1ba-208">Solo impide que el usuario navegue hasta la ruta del lado cliente determinada cuando no se autentica.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-208">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="0d1ba-209">Autenticar solicitudes de API (reAct)</span><span class="sxs-lookup"><span data-stu-id="0d1ba-209">Authenticate API requests (React)</span></span>

<span data-ttu-id="0d1ba-210">La autenticación de solicitudes con reAct se realiza mediante la importación `authService` en primer lugar de la instancia de `AuthorizeService` .</span><span class="sxs-lookup"><span data-stu-id="0d1ba-210">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="0d1ba-211">El token de acceso se recupera del `authService` y se adjunta a la solicitud, como se muestra a continuación.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-211">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="0d1ba-212">En los componentes de reAct, este trabajo se realiza normalmente en el `componentDidMount` método de ciclo de vida o como resultado de alguna interacción del usuario.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-212">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="0d1ba-213">Importar la authService en el componente</span><span class="sxs-lookup"><span data-stu-id="0d1ba-213">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="0d1ba-214">Recuperar y adjuntar el token de acceso a la respuesta</span><span class="sxs-lookup"><span data-stu-id="0d1ba-214">Retrieve and attach the access token to the response</span></span>

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

## <a name="deploy-to-production"></a><span data-ttu-id="0d1ba-215">Implementación en producción</span><span class="sxs-lookup"><span data-stu-id="0d1ba-215">Deploy to production</span></span>

<span data-ttu-id="0d1ba-216">Para implementar la aplicación en producción, se deben aprovisionar los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-216">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="0d1ba-217">Una base de datos para almacenar las Identity cuentas de usuario y las Identity concesiones de servidor.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-217">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="0d1ba-218">Un certificado de producción que se utilizará para firmar los tokens.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-218">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="0d1ba-219">No hay requisitos específicos para este certificado; puede ser un certificado autofirmado o un certificado aprovisionado a través de una entidad de certificación.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-219">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="0d1ba-220">Se puede generar a través de herramientas estándar como PowerShell o OpenSSL.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-220">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="0d1ba-221">Se puede instalar en el almacén de certificados en los equipos de destino o implementarse como un archivo *. pfx* con una contraseña segura.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-221">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-app-service"></a><span data-ttu-id="0d1ba-222">Ejemplo: implementar en Azure App Service</span><span class="sxs-lookup"><span data-stu-id="0d1ba-222">Example: Deploy to Azure App Service</span></span>

<span data-ttu-id="0d1ba-223">En esta sección se describe la implementación de la aplicación en Azure App Service mediante un certificado almacenado en el almacén de certificados.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-223">This section describes deploying the app to Azure App Service using a certificate stored in the certificate store.</span></span> <span data-ttu-id="0d1ba-224">Para modificar la aplicación para cargar un certificado desde el almacén de certificados, se requiere un plan de servicio de nivel estándar o superior cuando se configura la aplicación en el Azure Portal en un paso posterior.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-224">To modify the app to load a certificate from the certificate store, a Standard tier service plan or better is required when you configure the app in the Azure portal in a later step.</span></span>

<span data-ttu-id="0d1ba-225">En elappsettings.jsde la aplicación *en* el archivo, modifique la `IdentityServer` sección para incluir los detalles de la clave:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-225">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* <span data-ttu-id="0d1ba-226">El nombre del almacén representa el nombre del almacén de certificados donde se almacena el certificado.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-226">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="0d1ba-227">En este caso, apunta al almacén de usuarios personales.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-227">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="0d1ba-228">La ubicación del almacén representa dónde se debe cargar el certificado ( `CurrentUser` o `LocalMachine` ).</span><span class="sxs-lookup"><span data-stu-id="0d1ba-228">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="0d1ba-229">La propiedad Name en el certificado corresponde al sujeto distintivo del certificado.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-229">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="0d1ba-230">Para realizar la implementación en Azure App Service, siga los pasos descritos en [implementación de la aplicación en Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), en el que se explica cómo crear los recursos necesarios de Azure e implementar la aplicación en producción.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-230">To deploy to Azure App Service, follow the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), which explains how to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="0d1ba-231">Después de seguir las instrucciones anteriores, la aplicación se implementa en Azure pero todavía no es funcional.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-231">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="0d1ba-232">El certificado usado por la aplicación debe configurarse en el Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-232">The certificate used by the app must be configured in the Azure portal.</span></span> <span data-ttu-id="0d1ba-233">Busque la huella digital del certificado y siga los pasos descritos en [carga de los certificados](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span><span class="sxs-lookup"><span data-stu-id="0d1ba-233">Locate the thumbprint for the certificate and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="0d1ba-234">Aunque estos pasos mencionan SSL, hay una sección de **certificados privados** en el Azure portal donde puede cargar el certificado aprovisionado para usarlo con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-234">While these steps mention SSL, there's a **Private certificates** section in the Azure portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="0d1ba-235">Después de configurar la aplicación y la configuración de la aplicación en el Azure Portal, reinicie la aplicación en el portal.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-235">After configuring the app and the app's settings in the Azure portal, restart the app in the portal.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="0d1ba-236">Otras opciones de configuración</span><span class="sxs-lookup"><span data-stu-id="0d1ba-236">Other configuration options</span></span>

<span data-ttu-id="0d1ba-237">La compatibilidad con la autorización de API se basa en el Identity servidor con un conjunto de convenciones, valores predeterminados y mejoras para simplificar la experiencia de spa.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-237">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="0d1ba-238">No es necesario decir que toda la capacidad del Identity servidor está disponible en segundo plano si las integraciones de ASP.net Core no cubren su escenario.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-238">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="0d1ba-239">La compatibilidad con ASP.NET Core se centra en las aplicaciones de "primera parte", donde se crean e implementan todas las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-239">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="0d1ba-240">Como tal, no se ofrece soporte técnico para elementos como el consentimiento o la Federación.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-240">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="0d1ba-241">Para esos escenarios, use Identity el servidor y siga su documentación.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-241">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="0d1ba-242">Perfiles de aplicación</span><span class="sxs-lookup"><span data-stu-id="0d1ba-242">Application profiles</span></span>

<span data-ttu-id="0d1ba-243">Los perfiles de aplicación son configuraciones predefinidas para aplicaciones que definen aún más sus parámetros.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-243">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="0d1ba-244">En este momento, se admiten los siguientes perfiles:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-244">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="0d1ba-245">`IdentityServerSPA`: Representa un SPA hospedado junto Identity al servidor como una unidad única.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-245">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="0d1ba-246">El `redirect_uri` valor predeterminado es `/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="0d1ba-246">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="0d1ba-247">El `post_logout_redirect_uri` valor predeterminado es `/authentication/logout-callback` .</span><span class="sxs-lookup"><span data-stu-id="0d1ba-247">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="0d1ba-248">El conjunto de ámbitos incluye `openid` , `profile` y cada ámbito definido para las API de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-248">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="0d1ba-249">El conjunto de tipos de respuesta OIDC permitidos es `id_token token` o cada uno de ellos individualmente ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="0d1ba-249">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="0d1ba-250">El modo de respuesta permitido es `fragment` .</span><span class="sxs-lookup"><span data-stu-id="0d1ba-250">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="0d1ba-251">`SPA`: Representa un SPA que no está hospedado con el Identity servidor.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-251">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="0d1ba-252">El conjunto de ámbitos incluye `openid` , `profile` y cada ámbito definido para las API de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-252">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="0d1ba-253">El conjunto de tipos de respuesta OIDC permitidos es `id_token token` o cada uno de ellos individualmente ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="0d1ba-253">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="0d1ba-254">El modo de respuesta permitido es `fragment` .</span><span class="sxs-lookup"><span data-stu-id="0d1ba-254">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="0d1ba-255">`IdentityServerJwt`: Representa una API que se hospeda junto con el Identity servidor.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-255">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="0d1ba-256">La aplicación está configurada para tener un solo ámbito que tenga como valor predeterminado el nombre de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-256">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="0d1ba-257">`API`: Representa una API que no está hospedada en el Identity servidor.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-257">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="0d1ba-258">La aplicación está configurada para tener un solo ámbito que tenga como valor predeterminado el nombre de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-258">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="0d1ba-259">Configuración a través de AppSettings</span><span class="sxs-lookup"><span data-stu-id="0d1ba-259">Configuration through AppSettings</span></span>

<span data-ttu-id="0d1ba-260">Configure las aplicaciones a través del sistema de configuración agregándolas a la lista de `Clients` o `Resources` .</span><span class="sxs-lookup"><span data-stu-id="0d1ba-260">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="0d1ba-261">Configure cada `redirect_uri` propiedad y del cliente `post_logout_redirect_uri` , tal y como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-261">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

<span data-ttu-id="0d1ba-262">Al configurar recursos, puede configurar los ámbitos para el recurso, tal y como se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="0d1ba-262">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="0d1ba-263">Configuración mediante código</span><span class="sxs-lookup"><span data-stu-id="0d1ba-263">Configuration through code</span></span>

<span data-ttu-id="0d1ba-264">También puede configurar los clientes y los recursos a través del código mediante una sobrecarga de `AddApiAuthorization` que toma una acción para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="0d1ba-264">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="0d1ba-265">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="0d1ba-265">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
