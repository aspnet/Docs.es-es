---
title: Introducción a la autenticación para aplicaciones de una sola página en ASP.NET Core
author: javiercn
description: Se usa Identity con una aplicación de una sola página hospedada dentro de una aplicación ASP.net Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
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
uid: security/authentication/identity/spa
ms.openlocfilehash: 913f8f1e43586ce71353c080e72be3b80f4c0573
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634272"
---
# <a name="authentication-and-authorization-for-spas"></a>Autenticación y autorización para spa

ASP.NET Core 3,0 o posterior ofrece autenticación en aplicaciones de una sola página (Spa) mediante la compatibilidad con la autorización de la API. ASP.NET Core Identitypara la autenticación y el almacenamiento de usuarios se combina con el [ Identity servidor](https://identityserver.io/) para implementar OpenID Connect.

Se ha agregado un parámetro de autenticación a las plantillas de proyecto **angular** y **reAct** que es similar al parámetro de autenticación en las plantillas de proyecto **aplicación web (controlador de vista de modelos)** (MVC) y **aplicación web** ( Razor páginas). Los valores de parámetro permitidos son **None** y **individual**. La plantilla de proyecto **React.js y Redux** no admite el parámetro de autenticación en este momento.

## <a name="create-an-app-with-api-authorization-support"></a>Creación de una aplicación con compatibilidad con la autorización de API

Se puede usar la autenticación y autorización de usuario con angular y reAct Spa. Abra un shell de comandos y ejecute el siguiente comando:

**Angular**:

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

**ReAct**:

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

El comando anterior crea una aplicación ASP.NET Core con un directorio *ClientApp* que contiene el Spa.

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>Descripción general de los componentes de ASP.NET Core de la aplicación

En las secciones siguientes se describen las adiciones al proyecto cuando se incluye compatibilidad con la autenticación:

### <a name="startup-class"></a>Clase Startup

Los ejemplos de código siguientes se basan en [Microsoft. AspNetCore. ApiAuthorization. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) Paquete NuGet de servidor. En los ejemplos se configura la autenticación y autorización de API mediante los <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> métodos de extensión y. Los proyectos que usan las plantillas de proyecto reAct o angular SPA con autenticación incluyen una referencia a este paquete.

La `Startup` clase tiene las siguientes adiciones:

* Dentro del `Startup.ConfigureServices` método:
  * Identity con la interfaz de usuario predeterminada:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServidor con un `AddApiAuthorization` método auxiliar adicional que configura algunas convenciones de ASP.net Core predeterminadas en la parte superior del Identity servidor:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Autenticación con un método auxiliar `AddIdentityServerJwt` adicional que configura la aplicación para validar los tokens JWT generados por IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* Dentro del `Startup.Configure` método:
  * El middleware de autenticación responsable de validar las credenciales de solicitud y establecer el usuario en el contexto de la solicitud:

    ```csharp
    app.UseAuthentication();
    ```

  * El Identity middleware de servidor que expone los puntos de conexión de OpenID Connect:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

Este método auxiliar configura el Identity servidor para usar la configuración admitida. IdentityServer es un marco eficaz y extensible que sirve para abordar los problemas de seguridad de las aplicaciones. Al mismo tiempo, esto expone una complejidad innecesaria para los escenarios más comunes. Por lo tanto, se le proporciona un conjunto de convenciones y opciones de configuración que se consideran un buen punto de partida. Una vez que cambien las necesidades de autenticación, todo el potencial del Identity servidor sigue estando disponible para personalizar la autenticación para satisfacer sus necesidades.

### <a name="addno-locidentityserverjwt"></a>AddIdentityServerJwt

Este método auxiliar configura un esquema de directiva para la aplicación como el controlador de autenticación predeterminado. La Directiva está configurada para permitir que Identity todas las solicitudes se enruten a cualquier subruta en el espacio de la Identity dirección URL "/ Identity ". `JwtBearerHandler` se encarga de todas las demás solicitudes. Además, este método registra un `<<ApplicationName>>API` recurso de API con Identity el servidor con un ámbito predeterminado de `<<ApplicationName>>API` y configura el middleware del token de portador de JWT para validar los tokens emitidos por Identity el servidor para la aplicación.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

En el archivo *Controllers\WeatherForecastController.CS* , observe el `[Authorize]` atributo que se aplica a la clase, que indica que el usuario debe ser autorizado en función de la directiva predeterminada para tener acceso al recurso. La Directiva de autorización predeterminada está configurada para usar el esquema de autenticación predeterminado, que se configura por `AddIdentityServerJwt` el esquema de directivas mencionado anteriormente, `JwtBearerHandler` configurando el método de este auxiliar como el controlador predeterminado para las solicitudes a la aplicación.

### <a name="applicationdbcontext"></a>ApplicationDbContext

En el archivo *Data\ApplicationDbContext.CS* , observe lo mismo `DbContext` que se usa en Identity con la excepción de que extiende `ApiAuthorizationDbContext` (una clase más derivada de `IdentityDbContext` ) para incluir el esquema del Identity servidor.

Para obtener el control total del esquema de la base de datos, herede de una de las Identity `DbContext` clases disponibles y configure el contexto para incluir el Identity esquema mediante una llamada a `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` en el `OnModelCreating` método.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

En el archivo *Controllers\OidcConfigurationController.CS* , observe el punto de conexión aprovisionado para atender los parámetros OIDC que el cliente necesita usar.

### <a name="appsettingsjson"></a>appsettings.json

En el *appsettings.jsen* el archivo de la raíz del proyecto, hay una nueva `IdentityServer` sección en la que se describe la lista de clientes configurados. En el siguiente ejemplo hay un solo cliente, cuyo nombre corresponde al nombre de la aplicación y se asigna por convención al parámetro `ClientId` de OAuth. El perfil señala el tipo de aplicación que se está configurando. Se usa internamente para controlar las convenciones que simplifican el proceso de configuración del servidor. Hay varios perfiles disponibles, como se explica en la sección [perfiles de aplicación](#application-profiles) .

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>appsettings.Development.jsen

En el *appsettings.Development.jsen* el archivo de la raíz del proyecto, hay una `IdentityServer` sección que describe la clave que se usa para firmar los tokens. Al realizar la implementación en producción, es necesario aprovisionar e implementar una clave junto con la aplicación, como se explica en la sección [implementación en producción](#deploy-to-production) .

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>Descripción general de la aplicación angular

La compatibilidad con la autenticación y la autorización de API en la plantilla de angular reside en su propio módulo angular en el directorio *ClientApp\src\api-Authorization* El módulo se compone de los siguientes elementos:

* 3 componentes:
  * *login. Component. ts*: controla el flujo de inicio de sesión de la aplicación.
  * *logout. Component. ts*: controla el flujo de cierre de sesión de la aplicación.
  * *login-menu. Component. ts*: un widget que muestra uno de los siguientes conjuntos de vínculos:
    * Los vínculos de administración de perfiles de usuario y cierre de sesión cuando el usuario está autenticado.
    * Los vínculos registro e inicio de sesión cuando el usuario no está autenticado.
* Una protección de ruta `AuthorizeGuard` que se puede Agregar a las rutas y requiere que un usuario se autentique antes de visitar la ruta.
* Un interceptor HTTP `AuthorizeInterceptor` que asocia el token de acceso a las solicitudes HTTP salientes que se dirigen a la API cuando se autentica el usuario.
* Un servicio `AuthorizeService` que controla los detalles de nivel inferior del proceso de autenticación y expone información sobre el usuario autenticado al resto de la aplicación para su consumo.
* Un módulo angular que define rutas asociadas a las partes de autenticación de la aplicación. Expone el componente de menú Inicio de sesión, el interceptor, la protección y el servicio para su consumo desde el resto de la aplicación.

## <a name="general-description-of-the-react-app"></a>Descripción general de la aplicación reAct

La compatibilidad con la autenticación y la autorización de API en la plantilla reAct reside en el directorio *ClientApp\src\components\api-Authorization* Se compone de los siguientes elementos:

* 4 componentes:
  * *Login.js*: controla el flujo de inicio de sesión de la aplicación.
  * *Logout.js*: controla el flujo de cierre de sesión de la aplicación.
  * *LoginMenu.js*: un widget que muestra uno de los siguientes conjuntos de vínculos:
    * Los vínculos de administración de perfiles de usuario y cierre de sesión cuando el usuario está autenticado.
    * Los vínculos registro e inicio de sesión cuando el usuario no está autenticado.
  * *AuthorizeRoute.js*: componente de ruta que requiere que un usuario se autentique antes de representar el componente indicado en el `Component` parámetro.
* Instancia exportada `authService` de la clase `AuthorizeService` que controla los detalles de nivel inferior del proceso de autenticación y expone información sobre el usuario autenticado al resto de la aplicación para su consumo.

Ahora que ha visto los componentes principales de la solución, puede profundizar en los escenarios individuales de la aplicación.

## <a name="require-authorization-on-a-new-api"></a>Requerir autorización en una nueva API

De forma predeterminada, el sistema está configurado para requerir fácilmente la autorización para las nuevas API. Para ello, cree un nuevo controlador y agregue el `[Authorize]` atributo a la clase de controlador o a cualquier acción dentro del controlador.

## <a name="customize-the-api-authentication-handler"></a>Personalización del controlador de autenticación de API

Para personalizar la configuración del controlador de JWT de la API, configure su <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instancia:

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

El controlador JWT de la API genera eventos que permiten controlar el proceso de autenticación mediante `JwtBearerEvents` . Para proporcionar compatibilidad con la autorización de API, `AddIdentityServerJwt` registra sus propios controladores de eventos.

Para personalizar el control de un evento, ajuste el controlador de eventos existente con lógica adicional, según sea necesario. Por ejemplo:

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

En el código anterior, el `OnTokenValidated` controlador de eventos se reemplaza con una implementación personalizada. Esta implementación:

1. Llama a la implementación original proporcionada por la compatibilidad con la autorización de la API.
1. Ejecute su propia lógica personalizada.

## <a name="protect-a-client-side-route-angular"></a>Protección de una ruta de lado cliente (angular)

La protección de una ruta del lado cliente se realiza mediante la adición de la protección de autorización a la lista de protecciones que se deben ejecutar al configurar una ruta. Por ejemplo, puede ver cómo `fetch-data` se configura la ruta dentro del módulo de angular de la aplicación principal:

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

Es importante mencionar que la protección de una ruta no protege el punto de conexión real (lo que requiere que se le `[Authorize]` aplique un atributo), sino que solo impide que el usuario navegue hasta la ruta de la aplicación del lado cliente cuando no se autentica.

## <a name="authenticate-api-requests-angular"></a>Autenticar solicitudes de API (angular)

La autenticación de las solicitudes a las API hospedadas junto a la aplicación se realiza automáticamente mediante el uso del interceptor de cliente HTTP definido por la aplicación.

## <a name="protect-a-client-side-route-react"></a>Proteger una ruta del lado cliente (reAct)

Proteja una ruta del lado cliente mediante el `AuthorizeRoute` componente en lugar del componente sin formato `Route` . Por ejemplo, observe cómo `fetch-data` se configura la ruta dentro del `App` componente:

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

Protección de una ruta:

* No protege el punto de conexión real (que sigue necesitando un `[Authorize]` atributo aplicado).
* Solo impide que el usuario navegue hasta la ruta del lado cliente determinada cuando no se autentica.

## <a name="authenticate-api-requests-react"></a>Autenticar solicitudes de API (reAct)

La autenticación de solicitudes con reAct se realiza mediante la importación `authService` en primer lugar de la instancia de `AuthorizeService` . El token de acceso se recupera del `authService` y se adjunta a la solicitud, como se muestra a continuación. En los componentes de reAct, este trabajo se realiza normalmente en el `componentDidMount` método de ciclo de vida o como resultado de alguna interacción del usuario.

### <a name="import-the-authservice-into-your-component"></a>Importar la authService en el componente

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>Recuperar y adjuntar el token de acceso a la respuesta

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

## <a name="deploy-to-production"></a>Implementación en producción

Para implementar la aplicación en producción, se deben aprovisionar los siguientes recursos:

* Una base de datos para almacenar las Identity cuentas de usuario y las Identity concesiones de servidor.
* Un certificado de producción que se utilizará para firmar los tokens.
  * No hay requisitos específicos para este certificado; puede ser un certificado autofirmado o un certificado aprovisionado a través de una entidad de certificación.
  * Se puede generar a través de herramientas estándar como PowerShell o OpenSSL.
  * Se puede instalar en el almacén de certificados en los equipos de destino o implementarse como un archivo *. pfx* con una contraseña segura.

### <a name="example-deploy-to-azure-app-service"></a>Ejemplo: implementar en Azure App Service

En esta sección se describe la implementación de la aplicación en Azure App Service mediante un certificado almacenado en el almacén de certificados. Para modificar la aplicación para cargar un certificado desde el almacén de certificados, se requiere un plan de servicio de nivel estándar o superior cuando se configura la aplicación en el Azure Portal en un paso posterior.

En elappsettings.jsde la aplicación * en* el archivo, modifique la `IdentityServer` sección para incluir los detalles de la clave:

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

* El nombre del almacén representa el nombre del almacén de certificados donde se almacena el certificado. En este caso, apunta al almacén de usuarios personales.
* La ubicación del almacén representa dónde se debe cargar el certificado ( `CurrentUser` o `LocalMachine` ).
* La propiedad Name en el certificado corresponde al sujeto distintivo del certificado.

Para realizar la implementación en Azure App Service, siga los pasos descritos en [implementación de la aplicación en Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), en el que se explica cómo crear los recursos necesarios de Azure e implementar la aplicación en producción.

Después de seguir las instrucciones anteriores, la aplicación se implementa en Azure pero todavía no es funcional. El certificado usado por la aplicación debe configurarse en el Azure Portal. Busque la huella digital del certificado y siga los pasos descritos en [carga de los certificados](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).

Aunque estos pasos mencionan SSL, hay una sección de **certificados privados** en el Azure portal donde puede cargar el certificado aprovisionado para usarlo con la aplicación.

Después de configurar la aplicación y la configuración de la aplicación en el Azure Portal, reinicie la aplicación en el portal.

## <a name="other-configuration-options"></a>Otras opciones de configuración

La compatibilidad con la autorización de API se basa en el Identity servidor con un conjunto de convenciones, valores predeterminados y mejoras para simplificar la experiencia de spa. No es necesario decir que toda la capacidad del Identity servidor está disponible en segundo plano si las integraciones de ASP.net Core no cubren su escenario. La compatibilidad con ASP.NET Core se centra en las aplicaciones de "primera parte", donde se crean e implementan todas las aplicaciones. Como tal, no se ofrece soporte técnico para elementos como el consentimiento o la Federación. Para esos escenarios, use Identity el servidor y siga su documentación.

### <a name="application-profiles"></a>Perfiles de aplicación

Los perfiles de aplicación son configuraciones predefinidas para aplicaciones que definen aún más sus parámetros. En este momento, se admiten los siguientes perfiles:

* `IdentityServerSPA`: Representa un SPA hospedado junto Identity al servidor como una unidad única.
  * El `redirect_uri` valor predeterminado es `/authentication/login-callback` .
  * El `post_logout_redirect_uri` valor predeterminado es `/authentication/logout-callback` .
  * El conjunto de ámbitos incluye `openid` , `profile` y cada ámbito definido para las API de la aplicación.
  * El conjunto de tipos de respuesta OIDC permitidos es `id_token token` o cada uno de ellos individualmente ( `id_token` , `token` ).
  * El modo de respuesta permitido es `fragment` .
* `SPA`: Representa un SPA que no está hospedado con el Identity servidor.
  * El conjunto de ámbitos incluye `openid` , `profile` y cada ámbito definido para las API de la aplicación.
  * El conjunto de tipos de respuesta OIDC permitidos es `id_token token` o cada uno de ellos individualmente ( `id_token` , `token` ).
  * El modo de respuesta permitido es `fragment` .
* `IdentityServerJwt`: Representa una API que se hospeda junto con el Identity servidor.
  * La aplicación está configurada para tener un solo ámbito que tenga como valor predeterminado el nombre de la aplicación.
* `API`: Representa una API que no está hospedada en el Identity servidor.
  * La aplicación está configurada para tener un solo ámbito que tenga como valor predeterminado el nombre de la aplicación.

### <a name="configuration-through-appsettings"></a>Configuración a través de AppSettings

Configure las aplicaciones a través del sistema de configuración agregándolas a la lista de `Clients` o `Resources` .

Configure cada `redirect_uri` propiedad y del cliente `post_logout_redirect_uri` , tal y como se muestra en el ejemplo siguiente:

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

Al configurar recursos, puede configurar los ámbitos para el recurso, tal y como se muestra a continuación:

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

### <a name="configuration-through-code"></a>Configuración mediante código

También puede configurar los clientes y los recursos a través del código mediante una sobrecarga de `AddApiAuthorization` que toma una acción para configurar las opciones.

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

## <a name="additional-resources"></a>Recursos adicionales

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
