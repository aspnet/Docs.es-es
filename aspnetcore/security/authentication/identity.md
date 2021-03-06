---
title: Introducción a Identity en ASP.net Core
author: rick-anderson
description: Se usa Identity con una aplicación ASP.net Core. Obtenga información sobre cómo establecer los requisitos de contraseña (RequireDigit, RequiredLength, RequiredUniqueChars, etc.).
ms.author: riande
ms.date: 7/15/2020
no-loc:
- appsettings.json
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
uid: security/authentication/identity
ms.openlocfilehash: 4fa49f795b78b88e00bd32d04f74acd8689383b2
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394478"
---
# <a name="introduction-to-identity-on-aspnet-core"></a>Introducción a Identity en ASP.net Core

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core Identity:

* Es una API que admite la funcionalidad de inicio de sesión de la interfaz de usuario.
* Administra usuarios, contraseñas, datos de perfil, roles, notificaciones, tokens, confirmación de correo electrónico, etc.

Los usuarios pueden crear una cuenta con la información de inicio de sesión almacenada en Identity o pueden utilizar un proveedor de inicio de sesión externo. Entre los proveedores de inicio de sesión externos admitidos se incluyen [Facebook, Google, cuenta de Microsoft y Twitter](xref:security/authentication/social/index).

[!INCLUDE[](~/includes/requireAuth.md)]

El [ Identity código fuente](https://github.com/dotnet/AspNetCore/tree/main/src/Identity) está disponible en github. [Scaffolding Identity ](xref:security/authentication/scaffold-identity) y vea los archivos generados para revisar la interacción de la plantilla con Identity .

Identity normalmente se configura mediante una base de datos de SQL Server para almacenar nombres de usuario, contraseñas y datos de perfil. Como alternativa, se puede usar otro almacén persistente, por ejemplo, Azure Table Storage.

En este tema, aprenderá a usar Identity para registrar, iniciar sesión y cerrar la sesión de un usuario. Nota: las plantillas tratan el nombre de usuario y el correo electrónico como los mismos para los usuarios. Para obtener instrucciones más detalladas sobre la creación de aplicaciones que usan Identity , consulte [pasos siguientes](#next).

La [plataforma de identidad de Microsoft](/azure/active-directory/develop/) es:

* Evolución de la plataforma de desarrollo de Azure Active Directory (Azure AD).
* No relacionado con ASP.NET Core Identity .

[!INCLUDE[](~/includes/IdentityServer4.md)]

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([Cómo descargarlo](xref:index#how-to-download-a-sample)).

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a>Creación de una aplicación web con autenticación

Cree un proyecto de aplicación Web de ASP.NET Core con cuentas de usuario individuales.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Seleccione **archivo** > **nuevo** > **proyecto**.
* Seleccione **Aplicación web de ASP.NET Core**. Asigne al proyecto el nombre **WebApp1** para que tenga el mismo espacio de nombres que la descarga del proyecto. Haga clic en **OK**.
* Seleccione una **aplicación web** de ASP.net Core y, a continuación, seleccione **cambiar autenticación**.
* Seleccione **cuentas de usuario individuales** y haga clic en **Aceptar**.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

El comando anterior crea una Razor aplicación web con SQLite. Para crear la aplicación web con LocalDB, ejecute el siguiente comando:

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

El proyecto generado proporciona [ASP.NET Core Identity](xref:security/authentication/identity) como una [ Razor biblioteca de clases](xref:razor-pages/ui-class). La Identity Razor biblioteca de clases expone extremos con el `Identity` área. Por ejemplo:

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>Aplicación de migraciones

Aplique las migraciones para inicializar la base de datos.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Ejecute el siguiente comando en la consola del administrador de paquetes (PMC):

`PM> Update-Database`

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

No es necesario realizar migraciones en este paso al usar SQLite.

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Para LocalDB, ejecute el siguiente comando:

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Probar registro e inicio de sesión

Ejecute la aplicación y registre un usuario. En función del tamaño de la pantalla, es posible que tenga que seleccionar el botón de alternancia de navegación para ver los vínculos de **Inicio de sesión** y **registro** .

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Configurar Identity servicios

Los servicios se agregan en `ConfigureServices` . El patrón habitual consiste en llamar a todos los métodos `Add{Service}` y, luego, a todos los métodos `services.Configure{Service}`.

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

El código resaltado anterior configura Identity con valores de opción predeterminados. Los servicios se ponen a disposición de la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).

Identity se habilita mediante una llamada a <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> . `UseAuthentication` agrega [middleware](xref:fundamentals/middleware/index) de autenticación a la canalización de solicitudes.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](identity/sample/WebApp5x/Startup.cs?name=snippet_configureservices&highlight=12-99)]

El código anterior configura Identity con valores de opción predeterminados. Los servicios se ponen a disposición de la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).

Identity se habilita llamando a [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_). `UseAuthentication` agrega [middleware](xref:fundamentals/middleware/index) de autenticación a la canalización de solicitudes.

[!code-csharp[](identity/sample/WebApp5x/Startup.cs?name=snippet_configure&highlight=19)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

La aplicación generada por la plantilla no utiliza la [autorización](xref:security/authorization/secure-data). `app.UseAuthorization` se incluye para asegurarse de que se agrega en el orden correcto para que la aplicación agregue autorización. `UseRouting``UseAuthentication` `UseAuthorization` `UseEndpoints` se debe llamar a,, y en el orden mostrado en el código anterior.

Para obtener más información sobre `IdentityOptions` y `Startup` , vea <xref:Microsoft.AspNetCore.Identity.IdentityOptions> e inicio de la [aplicación](xref:fundamentals/startup).

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a>Scaffold registro, Inicio de sesión, cierre de sesión y RegisterConfirmation

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Agregue los `Register` `Login` archivos,, `LogOut` y `RegisterConfirmation` . Siga la [identidad de scaffolding en un Razor proyecto con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instrucciones de autorización para generar el código que se muestra en esta sección.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

Si ha creado el proyecto con el nombre **WebApp1**, ejecute los siguientes comandos. De lo contrario, use el espacio de nombres correcto para `ApplicationDbContext` :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

PowerShell usa punto y coma como separador de comandos. Al usar PowerShell, use el carácter de escape en la lista de archivos o ponga la lista de archivos entre comillas dobles, como se muestra en el ejemplo anterior.

Para obtener más información sobre scaffolding Identity , vea [scaffolding Identity into a Razor Project with Authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).

---

### <a name="examine-register"></a>Examinar registro

Cuando un usuario hace clic en el botón **registrar** de la `Register` página, `RegisterModel.OnPostAsync` se invoca la acción. El usuario lo crea [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) en el `_userManager` objeto:

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a>Registro

El formulario de inicio de sesión se muestra cuando:

* Se selecciona el vínculo **iniciar sesión** .
* Un usuario intenta obtener acceso a una página restringida para la que no está autorizado para acceder **o** cuando el sistema no la ha autenticado.

Cuando se envía el formulario de la página de inicio de sesión, `OnPostAsync` se llama a la acción. `PasswordSignInAsync` en el `_signInManager` objeto.

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Para obtener información acerca de cómo tomar decisiones de autorización, vea <xref:security/authorization/introduction> .

### <a name="log-out"></a>Cerrar la sesión

El vínculo de **cierre de sesión** invoca la `LogoutModel.OnPost` acción. 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

En el código anterior, el código `return RedirectToPage();` debe ser un redireccionamiento para que el explorador realice una nueva solicitud y se actualice la identidad del usuario.

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) borra las notificaciones del usuario almacenadas en un cookie .

Post se especifica en *pages/Shared/_LoginPartial. cshtml*:

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a>Muestre Identity

Las plantillas predeterminadas del proyecto web permiten el acceso anónimo a las páginas principales. Para probar Identity , agregue [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

Si ha iniciado sesión, cierre la sesión. Ejecute la aplicación y seleccione el vínculo de **privacidad** . Se le redirige a la página de inicio de sesión.

### <a name="explore-identity"></a>Visite Identity

Para explorar Identity con más detalle:

* [Crear origen de la interfaz de usuario de identidad completa](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Examine el origen de cada página y recorra el depurador.

## <a name="identity-components"></a>Identity Componentes

Todos los Identity paquetes de NuGet dependientes de se incluyen en el [ASP.net Core marco de trabajo compartido](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).

El paquete principal de Identity es [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/). Este paquete contiene el conjunto principal de interfaces para ASP.NET Core Identity y lo incluye `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .

## <a name="migrating-to-aspnet-core-identity"></a>Migrar a ASP.NET Core Identity

Para obtener más información e instrucciones sobre cómo migrar el Identity almacén existente, vea [migrar Identity la autenticación y ](xref:migration/identity).

## <a name="setting-password-strength"></a>Establecer la seguridad de la contraseña

Consulte [configuración](#pw) para obtener un ejemplo que establece los requisitos mínimos de contraseña.

## <a name="adddefaultidentity-and-addidentity"></a>AddDefault Identity y agregarIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> se presentó en ASP.NET Core 2,1. Llamar `AddDefaultIdentity` a es similar a llamar a lo siguiente:

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

Vea [ Identity origen de AddDefault](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obtener más información.

## <a name="prevent-publish-of-static-identity-assets"></a>Impedir la publicación de Identity recursos estáticos

Para evitar la publicación de recursos estáticos Identity (hojas de estilos y archivos JavaScript para Identity la interfaz de usuario) en la raíz Web, agregue la siguiente `ResolveStaticWebAssetsInputsDependsOn` propiedad y `RemoveIdentityAssets` destino al archivo de proyecto de la aplicación:

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a>Pasos siguientes

* [Código fuente ASP.NET Core Identity](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)
* Consulte [este problema de github](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obtener información sobre cómo configurar el Identity uso de SQLite.
* [Configurar Identity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core Identity es un sistema de pertenencia que agrega funcionalidad de inicio de sesión a ASP.NET Core aplicaciones. Los usuarios pueden crear una cuenta con la información de inicio de sesión almacenada en Identity o pueden utilizar un proveedor de inicio de sesión externo. Entre los proveedores de inicio de sesión externos admitidos se incluyen [Facebook, Google, cuenta de Microsoft y Twitter](xref:security/authentication/social/index).

Identity se puede configurar mediante una base de datos de SQL Server para almacenar nombres de usuario, contraseñas y datos de perfil. Como alternativa, se puede usar otro almacén persistente, por ejemplo, Azure Table Storage.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([Cómo descargarlo](xref:index#how-to-download-a-sample)).

En este tema, aprenderá a usar Identity para registrar, iniciar sesión y cerrar la sesión de un usuario. Para obtener instrucciones más detalladas sobre la creación de aplicaciones que usan Identity , consulte la sección pasos siguientes al final de este artículo.

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a>AddDefault Identity y agregarIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> se presentó en ASP.NET Core 2,1. Llamar `AddDefaultIdentity` a es similar a llamar a lo siguiente:

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

Vea [ Identity origen de AddDefault](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obtener más información.

## <a name="create-a-web-app-with-authentication"></a>Creación de una aplicación web con autenticación

Cree un proyecto de aplicación Web de ASP.NET Core con cuentas de usuario individuales.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Seleccione **archivo** > **nuevo** > **proyecto**.
* Seleccione **Aplicación web de ASP.NET Core**. Asigne al proyecto el nombre **WebApp1** para que tenga el mismo espacio de nombres que la descarga del proyecto. Haga clic en **OK**.
* Seleccione una **aplicación web** de ASP.net Core y, a continuación, seleccione **cambiar autenticación**.
* Seleccione **cuentas de usuario individuales** y haga clic en **Aceptar**.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

El proyecto generado proporciona [ASP.NET Core Identity](xref:security/authentication/identity) como una [ Razor biblioteca de clases](xref:razor-pages/ui-class). La Identity Razor biblioteca de clases expone extremos con el `Identity` área. Por ejemplo:

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>Aplicación de migraciones

Aplique las migraciones para inicializar la base de datos.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Ejecute el siguiente comando en la consola del administrador de paquetes (PMC):

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Probar registro e inicio de sesión

Ejecute la aplicación y registre un usuario. En función del tamaño de la pantalla, es posible que tenga que seleccionar el botón de alternancia de navegación para ver los vínculos de **Inicio de sesión** y **registro** .

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Configurar Identity servicios

Los servicios se agregan en `ConfigureServices` . El patrón habitual consiste en llamar a todos los métodos `Add{Service}` y, luego, a todos los métodos `services.Configure{Service}`.

El código anterior configura Identity con valores de opción predeterminados. Los servicios se ponen a disposición de la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).

Identity se habilita llamando a [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_). `UseAuthentication` agrega [middleware](xref:fundamentals/middleware/index) de autenticación a la canalización de solicitudes.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

Para obtener más información, vea la [ Identity clase de opciones](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) y el inicio de la [aplicación](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Registro de scaffolding, Inicio de sesión y cierre de sesión

Siga la [identidad de scaffolding en un Razor proyecto con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instrucciones de autorización para generar el código que se muestra en esta sección.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Agregue los archivos de registro, Inicio de sesión y cierre de sesión.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

Si ha creado el proyecto con el nombre **WebApp1**, ejecute los siguientes comandos. De lo contrario, use el espacio de nombres correcto para `ApplicationDbContext` :

Al usar SQLite, `--useSqLite` se debe especificar:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout" --useSqLite
```

Con SQL Express, use los comandos siguientes:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell usa punto y coma como separador de comandos. Al usar PowerShell, use el carácter de escape en la lista de archivos o ponga la lista de archivos entre comillas dobles, como se muestra en el ejemplo anterior.

---

### <a name="examine-register"></a>Examinar registro

Cuando un usuario hace clic en el vínculo de **registro** , `RegisterModel.OnPostAsync` se invoca la acción. El usuario lo crea [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) en el `_userManager` objeto:

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

Si el usuario se creó correctamente, el usuario inicia sesión en la llamada a `_signInManager.SignInAsync` .

**Nota:** Consulte [confirmación](xref:security/authentication/accconfirm#prevent-login-at-registration) de la cuenta para conocer los pasos para evitar el inicio de sesión inmediato en el registro.

### <a name="log-in"></a>Registro

El formulario de inicio de sesión se muestra cuando:

* Se selecciona el vínculo **iniciar sesión** .
* Un usuario intenta obtener acceso a una página restringida para la que no está autorizado para acceder **o** cuando el sistema no la ha autenticado.

Cuando se envía el formulario de la página de inicio de sesión, `OnPostAsync` se llama a la acción. `PasswordSignInAsync` en el `_signInManager` objeto.

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Para obtener información acerca de cómo tomar decisiones de autorización, vea <xref:security/authorization/introduction> .

### <a name="log-out"></a>Cerrar la sesión

El vínculo de **cierre de sesión** invoca la `LogoutModel.OnPost` acción. 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) borra las notificaciones del usuario almacenadas en un cookie .

Post se especifica en *pages/Shared/_LoginPartial. cshtml*:

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a>Muestre Identity

Las plantillas predeterminadas del proyecto web permiten el acceso anónimo a las páginas principales. Para probar Identity , agregue [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) a la página de privacidad.

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

Si ha iniciado sesión, cierre la sesión. Ejecute la aplicación y seleccione el vínculo de **privacidad** . Se le redirige a la página de inicio de sesión.

### <a name="explore-identity"></a>Visite Identity

Para explorar Identity con más detalle:

* [Crear origen de la interfaz de usuario de identidad completa](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Examine el origen de cada página y recorra el depurador.

## <a name="identity-components"></a>Identity Componentes

Todos los Identity paquetes de NuGet dependientes se incluyen en el [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).

El paquete principal de Identity es [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/). Este paquete contiene el conjunto principal de interfaces para ASP.NET Core Identity y lo incluye `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .

## <a name="migrating-to-aspnet-core-identity"></a>Migrar a ASP.NET Core Identity

Para obtener más información e instrucciones sobre cómo migrar el Identity almacén existente, vea [migrar Identity la autenticación y ](xref:migration/identity).

## <a name="setting-password-strength"></a>Establecer la seguridad de la contraseña

Consulte [configuración](#pw) para obtener un ejemplo que establece los requisitos mínimos de contraseña.

## <a name="next-steps"></a>Pasos siguientes

* Consulte [este problema de github](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obtener información sobre cómo configurar el Identity uso de SQLite.
* [Configurar Identity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
