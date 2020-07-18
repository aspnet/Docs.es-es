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
# <a name="scaffold-identity-in-aspnet-core-projects"></a>Scaffolding Identity en proyectos de ASP.net Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core proporciona [ASP.net Core Identity ](xref:security/authentication/identity) como una [ Razor biblioteca de clases](xref:razor-pages/ui-class). Las aplicaciones que incluyen Identity pueden aplicar el scaffolding para agregar selectivamente el código fuente incluido en la Identity Razor biblioteca de clases (RCL). Puede que quiera generar código fuente que le permita modificar un código y cambiar el comportamiento; así, por ejemplo, podría indicar al proveedor de scaffolding que generara el código que se usa en el registro. El código generado tiene prioridad sobre el mismo código de la RCL de Identity. Para obtener un control total de la interfaz de usuario y no usar el valor predeterminado de RCL, consulte la sección creación de un [ Identity origen de IU completo](#full).

Las aplicaciones que **no** incluyen autenticación pueden aplicar el scaffolding para agregar el paquete RCL Identity . Tiene la opción de seleccionar el código de Identity que se va a generar.

Aunque el scaffolding genera la mayor parte del código necesario, debe actualizar el proyecto para completar el proceso. En este documento se explican los pasos necesarios para completar una Identity actualización de scaffolding.

Se recomienda usar un sistema de control de código fuente que muestre las diferencias de archivos y le permite deshacer los cambios. Inspeccione los cambios después de ejecutar el Identity scaffolding.

Los servicios son necesarios cuando se usa la [autenticación en dos fases](xref:security/authentication/identity-enable-qrcodes), la [confirmación de la cuenta y la recuperación de contraseña](xref:security/authentication/accconfirm), y otras características de seguridad con Identity . Los servicios o los códigos auxiliares de servicio no se generan al aplicar scaffolding Identity . Los servicios para habilitar estas características deben agregarse manualmente. Por ejemplo, vea [requerir confirmación de correo electrónico](xref:security/authentication/accconfirm#require-email-confirmation).

Cuando se scaffolding Identity con un nuevo contexto de datos en un proyecto con cuentas individuales existentes:

* En `Startup.ConfigureServices` , quite las llamadas a:
  * `AddDbContext`
  * `AddDefaultIdentity`

Por ejemplo, `AddDbContext` y `AddDefaultIdentity` están comentados en el código siguiente:

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

El código anterior comenta el código que está duplicado en *areas/ Identity / Identity HostingStartup.CS*

Normalmente, las aplicaciones que se crearon con cuentas individuales ***no*** deben crear un nuevo contexto de datos.

## <a name="scaffold-identity-into-an-empty-project"></a>Scaffolding Identity en un proyecto vacío

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Actualice la `Startup` clase con código similar al siguiente:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Scaffolding Identity en un Razor proyecto sin autorización existente

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

Identityestá configurado en *areas/ Identity / Identity HostingStartup.CS*. Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migraciones, UseAuthentication y diseño

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Enable authentication (Habilitar autenticación)

Actualice la `Startup` clase con código similar al siguiente:

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Cambios de diseño

Opcional: agregue el inicio de sesión parcial ( `_LoginPartial` ) al archivo de diseño:

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Scaffolding Identity en un Razor proyecto con autorización

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

Algunas Identity opciones se configuran en *areas/ Identity / Identity HostingStartup.CS*. Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Scaffolding Identity en un proyecto de MVC sin autorización existente

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

Opcional: agregue el inicio de sesión parcial ( `_LoginPartial` ) al archivo *views/Shared/_Layout. cshtml* :

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* Mueva el archivo *pages/Shared/_LoginPartial. cshtml* a *views/Shared/_LoginPartial. cshtml*

Identityestá configurado en *areas/ Identity / Identity HostingStartup.CS*. Para obtener más información, vea IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Actualice la `Startup` clase con código similar al siguiente:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Scaffolding Identity en un proyecto de MVC con autorización

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-identity-into-a-blazor-server-project-without-existing-authorization"></a>Scaffolding Identity en un Blazor Server proyecto sin autorización existente

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identityestá configurado en *areas/ Identity / Identity HostingStartup.CS*. Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

### <a name="migrations"></a>Migraciones

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a>Pasar un token XSRF a la aplicación

Los tokens se pueden pasar a los componentes:

* Cuando los tokens de autenticación se aprovisionan y se guardan en la cookie de autenticación, se pueden pasar a los componentes.
* Razorlos componentes no pueden usar `HttpContext` directamente, por lo que no hay manera de obtener un [token de falsificación de la solicitud (XSRF)](xref:security/anti-request-forgery) para publicar el Identity punto de conexión de cierre de sesión en `/Identity/Account/Logout` . Un token XSRF se puede pasar a los componentes.

Para obtener más información, vea <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.

En el archivo *pages/_Host. cshtml* , establezca el token después de agregarlo a las `InitialApplicationState` `TokenProvider` clases y:

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

Actualice el `App` componente (*app. Razor*) para asignar `InitialState.XsrfToken` :

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

El `TokenProvider` servicio que se muestra en el tema se usa en el `LoginDisplay` componente en la siguiente sección [diseño y cambios de flujo de autenticación](#layout-and-authentication-flow-changes) .

### <a name="enable-authentication"></a>Enable authentication (Habilitar autenticación)

En la `Startup` clase:

* Confirme que Razor se han agregado los servicios de páginas en `Startup.ConfigureServices` .
* Si usa [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), registre el servicio.
* Llame a `UseDatabaseErrorPage` en el generador de aplicaciones en `Startup.Configure` para el entorno de desarrollo.
* Llame a `UseAuthentication` y `UseAuthorization` después de `UseRouting` .
* Agregue un extremo para Razor las páginas.

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a>Cambios en el flujo de la autenticación y el diseño

Agregue un `RedirectToLogin` componente (*RedirectToLogin. Razor*) a la carpeta *compartida* de la aplicación en la raíz del proyecto:

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

Agregue un `LoginDisplay` componente (*LoginDisplay. Razor*) a la carpeta *compartida* de la aplicación. El [servicio TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) proporciona el token XSRF para el formulario HTML que se envía al Identity punto de conexión de cierre de sesión:

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

En el `MainLayout` componente (*Shared/MainLayout. Razor*), agregue el `LoginDisplay` componente al `<div>` contenido del elemento de la fila superior:

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a>Extremos de autenticación de estilo

Dado Blazor Server que utiliza páginas Razor Identity de páginas, el estilo de la interfaz de usuario cambia cuando un visitante navega entre Identity páginas y componentes. Tiene dos opciones para abordar los estilos de Incongruous:

#### <a name="build-identity-components"></a>Componentes de compilación Identity

Un enfoque para utilizar los componentes de en Identity lugar de las páginas es crear Identity componentes de. Dado `SignInManager` `UserManager` que y no se admiten en Razor los componentes, use puntos de conexión de API en la Blazor Server aplicación para procesar las acciones de la cuenta de usuario.

#### <a name="use-a-custom-layout-with-blazor-app-styles"></a>Usar un diseño personalizado con Blazor estilos de aplicación

El Identity diseño y los estilos de las páginas se pueden modificar para generar páginas que utilicen el Blazor tema predeterminado.

> [!NOTE]
> El ejemplo de esta sección es simplemente un punto de partida para la personalización. Es probable que se necesite trabajo adicional para la mejor experiencia de usuario.

Cree un nuevo `NavMenu_IdentityLayout` componente (*Shared/NavMenu_ Identity layout. Razor*). Para el marcado y el código del componente, use el mismo contenido del componente de la aplicación `NavMenu` (*Shared/NavMenu. Razor*). Desseccione cualquier `NavLink` s en componentes que no se puedan alcanzar de forma anónima porque las redirecciones automáticas del `RedirectToLogin` componente no se realizan correctamente para los componentes que requieren autenticación o autorización.

En el archivo *pages/Shared/layout. cshtml* , realice los cambios siguientes:

* Agregue Razor directivas a la parte superior del archivo para usar las aplicaciones auxiliares de etiquetas y los componentes de la aplicación en la carpeta *compartida* :

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  Reemplace `{APPLICATION ASSEMBLY}` con el nombre de ensamblado de la aplicación.

* Agregue una `<base>` etiqueta y una Blazor hoja de estilos `<link>` al `<head>` contenido:

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* Cambie el contenido de la `<body>` etiqueta a lo siguiente:

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

## <a name="scaffold-identity-into-a-blazor-server-project-with-authorization"></a>Scaffolding Identity en un Blazor Server proyecto con autorización

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Algunas Identity opciones se configuran en *areas/ Identity / Identity HostingStartup.CS*. Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Crear Identity origen de IU completo

Para mantener el control total de la Identity interfaz de usuario, ejecute el Identity scaffolding y seleccione **invalidar todos los archivos**.

En el código resaltado siguiente se muestran los cambios para reemplazar la Identity interfaz de usuario predeterminada por Identity en una aplicación web ASP.net Core 2,1. Puede que desee hacer esto para tener un control total de la Identity interfaz de usuario.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

El valor predeterminado Identity se reemplaza en el código siguiente:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

El código siguiente establece los valores de [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)y [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Registre una `IEmailSender` implementación, por ejemplo:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a>Configuración de contraseña

Si <xref:Microsoft.AspNetCore.Identity.PasswordOptions> se configuran en `Startup.ConfigureServices` , la configuración de [ `[StringLength]` atributos](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) podría ser necesaria para la `Password` propiedad en Identity las páginas con scaffolding. `InputModel`las `Password` propiedades se encuentran en los siguientes archivos:

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a>Deshabilitar una página

En esta sección se muestra cómo deshabilitar la página de registro, pero se puede usar el enfoque para deshabilitar cualquier página.

Para deshabilitar el registro de usuario:

* Scaffold Identity . Incluya account. Register, Account. login y account. RegisterConfirmation. Por ejemplo:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Actualice *areas/ Identity /pages/Account/Register.cshtml.CS* para que los usuarios no puedan registrarse desde este punto de conexión:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Actualice *areas/ Identity /pages/Account/Register.cshtml* para que sean coherentes con los cambios anteriores:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Comente o quite el vínculo de registro de las *áreas/ Identity /pages/Account/login.cshtml*

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* Actualice la página *áreas/ Identity /pages/Account/RegisterConfirmation* .

  * Quite el código y los vínculos del archivo cshtml.
  * Quite el código de confirmación de `PageModel` :

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
  
### <a name="use-another-app-to-add-users"></a>Usar otra aplicación para agregar usuarios

Proporcionar un mecanismo para agregar usuarios fuera de la aplicación Web. Entre las opciones para agregar usuarios se incluyen:

* Una aplicación Web de administración dedicada.
* Una aplicación de consola.

En el código siguiente se describe un enfoque para agregar usuarios:

* Una lista de usuarios se lee en la memoria.
* Se genera una contraseña única segura para cada usuario.
* El usuario se agrega a la Identity base de datos.
* Se notifica al usuario y se le indica que cambie la contraseña.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

En el código siguiente se describe cómo agregar un usuario:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Se puede seguir un enfoque similar para escenarios de producción.

## <a name="prevent-publish-of-static-identity-assets"></a>Impedir la publicación de Identity recursos estáticos

Para evitar la publicación de recursos estáticos Identity en la raíz Web, vea <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .

## <a name="additional-resources"></a>Recursos adicionales

* [Cambios en el código de autenticación en ASP.NET Core 2,1 y versiones posteriores](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2,1 y versiones posteriores [proporcionan Identity ASP.net Core](xref:security/authentication/identity) como una [ Razor biblioteca de clases](xref:razor-pages/ui-class). Las aplicaciones que incluyen Identity pueden aplicar el scaffolding para agregar selectivamente el código fuente incluido en la Identity Razor biblioteca de clases (RCL). Puede que quiera generar código fuente que le permita modificar un código y cambiar el comportamiento; así, por ejemplo, podría indicar al proveedor de scaffolding que generara el código que se usa en el registro. El código generado tiene prioridad sobre el mismo código de la RCL de Identity. Para obtener el control total de la interfaz de usuario y no usar el valor predeterminado de RCL, consulte la sección creación de un origen de la [interfaz de usuario de identidad completa](#full).

Las aplicaciones que **no** incluyen autenticación pueden aplicar el scaffolding para agregar el paquete RCL Identity . Tiene la opción de seleccionar el código de Identity que se va a generar.

Aunque el scaffolding genera la mayor parte del código necesario, tendrá que actualizar el proyecto para completar el proceso. En este documento se explican los pasos necesarios para completar una Identity actualización de scaffolding.

Cuando Identity se ejecuta el scaffolding, se crea un archivo de *ScaffoldingReadme.txt* en el directorio del proyecto. El archivo de *ScaffoldingReadme.txt* contiene instrucciones generales sobre lo que se necesita para completar la Identity actualización de scaffolding. Este documento contiene instrucciones más completas que el archivo de *ScaffoldingReadme.txt* .

Se recomienda usar un sistema de control de código fuente que muestre las diferencias de archivos y le permite deshacer los cambios. Inspeccione los cambios después de ejecutar el Identity scaffolding.

> [!NOTE]
> Los servicios son necesarios cuando se usa la [autenticación en dos fases](xref:security/authentication/identity-enable-qrcodes), la [confirmación de la cuenta y la recuperación de contraseña](xref:security/authentication/accconfirm), y otras características de seguridad con Identity . Los servicios o los códigos auxiliares de servicio no se generan al aplicar scaffolding Identity . Los servicios para habilitar estas características deben agregarse manualmente. Por ejemplo, vea [requerir confirmación de correo electrónico](xref:security/authentication/accconfirm#require-email-confirmation).

## <a name="scaffold-identity-into-an-empty-project"></a>Scaffolding Identity en un proyecto vacío

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Agregue las siguientes llamadas resaltadas a la `Startup` clase:

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Scaffolding Identity en un Razor proyecto sin autorización existente

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

Identityestá configurado en *areas/ Identity / Identity HostingStartup.CS*. Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migraciones, UseAuthentication y diseño

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Enable authentication (Habilitar autenticación)

En el `Configure` método de la `Startup` clase, llame a [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) después de `UseStaticFiles` :

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Cambios de diseño

Opcional: agregue el inicio de sesión parcial ( `_LoginPartial` ) al archivo de diseño:

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Scaffolding Identity en un Razor proyecto con autorización

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

Algunas Identity opciones se configuran en *areas/ Identity / Identity HostingStartup.CS*. Para obtener más información, vea [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Scaffolding Identity en un proyecto de MVC sin autorización existente

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

Opcional: agregue el inicio de sesión parcial ( `_LoginPartial` ) al archivo *views/Shared/_Layout. cshtml* :

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* Mueva el archivo *pages/Shared/_LoginPartial. cshtml* a *views/Shared/_LoginPartial. cshtml*

Identityestá configurado en *areas/ Identity / Identity HostingStartup.CS*. Para obtener más información, vea IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Llame a [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) después de `UseStaticFiles` :

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Scaffolding Identity en un proyecto de MVC con autorización

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Elimine las *páginas/carpetas compartidas* y los archivos de esa carpeta.

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Crear Identity origen de IU completo

Para mantener el control total de la Identity interfaz de usuario, ejecute el Identity scaffolding y seleccione **invalidar todos los archivos**.

En el código resaltado siguiente se muestran los cambios para reemplazar la Identity interfaz de usuario predeterminada por Identity en una aplicación web ASP.net Core 2,1. Puede que desee hacer esto para tener un control total de la Identity interfaz de usuario.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

El valor predeterminado Identity se reemplaza en el código siguiente:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

El código siguiente establece los valores de [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)y [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Registre una `IEmailSender` implementación, por ejemplo:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a>Configuración de contraseña

Si <xref:Microsoft.AspNetCore.Identity.PasswordOptions> se configuran en `Startup.ConfigureServices` , la configuración de [ `[StringLength]` atributos](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) podría ser necesaria para la `Password` propiedad en Identity las páginas con scaffolding. `InputModel`las `Password` propiedades se encuentran en los siguientes archivos:

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a>Deshabilitar registro (página)

Para deshabilitar el registro de usuario:

* Scaffold Identity . Incluya account. Register, Account. login y account. RegisterConfirmation. Por ejemplo:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Actualice *areas/ Identity /pages/Account/Register.cshtml.CS* para que los usuarios no puedan registrarse desde este punto de conexión:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Actualice *areas/ Identity /pages/Account/Register.cshtml* para que sean coherentes con los cambios anteriores:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Comente o quite el vínculo de registro de las *áreas/ Identity /pages/Account/login.cshtml*

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Actualice la página *áreas/ Identity /pages/Account/RegisterConfirmation* .

  * Quite el código y los vínculos del archivo cshtml.
  * Quite el código de confirmación de `PageModel` :

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
  
### <a name="use-another-app-to-add-users"></a>Usar otra aplicación para agregar usuarios

Proporcionar un mecanismo para agregar usuarios fuera de la aplicación Web. Entre las opciones para agregar usuarios se incluyen:

* Una aplicación Web de administración dedicada.
* Una aplicación de consola.

En el código siguiente se describe un enfoque para agregar usuarios:

* Una lista de usuarios se lee en la memoria.
* Se genera una contraseña única segura para cada usuario.
* El usuario se agrega a la Identity base de datos.
* Se notifica al usuario y se le indica que cambie la contraseña.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

En el código siguiente se describe cómo agregar un usuario:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Se puede seguir un enfoque similar para escenarios de producción.

## <a name="additional-resources"></a>Recursos adicionales

* [Cambios en el código de autenticación en ASP.NET Core 2,1 y versiones posteriores](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
