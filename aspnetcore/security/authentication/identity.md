---
title: 'Introducción a :::no-loc(Identity)::: en ASP.net Core'
author: rick-anderson
description: 'Se usa :::no-loc(Identity)::: con una aplicación ASP.net Core. Obtenga información sobre cómo establecer los requisitos de contraseña (RequireDigit, RequiredLength, RequiredUniqueChars, etc.).'
ms.author: riande
ms.date: 7/15/2020
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/identity
ms.openlocfilehash: 25070e90050db9dca8b003ae782662811096526a
ms.sourcegitcommit: 1b89fc58114a251926abadfd5c69c120f1ba12d8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2020
ms.locfileid: "87160311"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a>Introducción a :::no-loc(Identity)::: en ASP.net Core

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

:::no-loc(Identity)::: de ASP.NET Core:

* Es una API que admite la funcionalidad de inicio de sesión de la interfaz de usuario.
* Administra usuarios, contraseñas, datos de perfil, roles, notificaciones, tokens, confirmación de correo electrónico, etc.

Los usuarios pueden crear una cuenta con la información de inicio de sesión almacenada en :::no-loc(Identity)::: o pueden utilizar un proveedor de inicio de sesión externo. Entre los proveedores de inicio de sesión externos admitidos se incluyen [Facebook, Google, cuenta de Microsoft y Twitter](xref:security/authentication/social/index).

[!INCLUDE[](~/includes/requireAuth.md)]

El [ :::no-loc(Identity)::: código fuente](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(Identity):::) está disponible en github. [Scaffolding :::no-loc(Identity)::: ](xref:security/authentication/scaffold-identity) y vea los archivos generados para revisar la interacción de la plantilla con :::no-loc(Identity)::: .

:::no-loc(Identity):::normalmente se configura mediante una base de datos de SQL Server para almacenar nombres de usuario, contraseñas y datos de perfil. Como alternativa, se puede usar otro almacén persistente, por ejemplo, Azure Table Storage.

En este tema, aprenderá a usar :::no-loc(Identity)::: para registrar, iniciar sesión y cerrar la sesión de un usuario. Nota: las plantillas tratan el nombre de usuario y el correo electrónico como los mismos para los usuarios. Para obtener instrucciones más detalladas sobre la creación de aplicaciones que usan :::no-loc(Identity)::: , consulte [pasos siguientes](#next).

La [plataforma de identidad de Microsoft](/azure/active-directory/develop/) es:

* Evolución de la plataforma de desarrollo de Azure Active Directory (Azure AD).
* No relacionado con ASP.NET Core :::no-loc(Identity)::: .

[!INCLUDE[](~/includes/:::no-loc(Identity):::Server4.md)]

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([Cómo descargar)](xref:index#how-to-download-a-sample)).

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a>Creación de una aplicación web con autenticación

Cree un proyecto de aplicación Web de ASP.NET Core con cuentas de usuario individuales.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Seleccione **archivo** > **nuevo** > **proyecto**.
* Seleccione **Aplicación web de ASP.NET Core**. Asigne al proyecto el nombre **WebApp1** para que tenga el mismo espacio de nombres que la descarga del proyecto. Haga clic en **OK**.
* Seleccione una **aplicación web**de ASP.net Core y, a continuación, seleccione **cambiar autenticación**.
* Seleccione **cuentas de usuario individuales** y haga clic en **Aceptar**.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

El comando anterior crea una :::no-loc(Razor)::: aplicación web con SQLite. Para crear la aplicación web con LocalDB, ejecute el siguiente comando:

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

El proyecto generado proporciona [ASP.net Core :::no-loc(Identity)::: ](xref:security/authentication/identity) como [ :::no-loc(Razor)::: biblioteca de clases](xref:razor-pages/ui-class). La :::no-loc(Identity)::: :::no-loc(Razor)::: biblioteca de clases expone extremos con el `:::no-loc(Identity):::` área. Por ejemplo:

* /:::no-loc(Identity):::/Account/Login
* /:::no-loc(Identity):::/Account/Logout
* /:::no-loc(Identity):::/Account/Manage

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

### <a name="configure-no-locidentity-services"></a>Configurar :::no-loc(Identity)::: servicios

Los servicios se agregan en `ConfigureServices` . El patrón habitual consiste en llamar a todos los métodos `Add{Service}` y, luego, a todos los métodos `services.Configure{Service}`.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

El código resaltado anterior configura :::no-loc(Identity)::: con valores de opción predeterminados. Los servicios se ponen a disposición de la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).

:::no-loc(Identity):::se habilita mediante una llamada a <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> . `UseAuthentication`agrega [middleware](xref:fundamentals/middleware/index) de autenticación a la canalización de solicitudes.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

La aplicación generada por la plantilla no utiliza la [autorización](xref:security/authorization/secure-data). `app.UseAuthorization`se incluye para asegurarse de que se agrega en el orden correcto para que la aplicación agregue autorización. `UseRouting``UseAuthentication` `UseAuthorization` `UseEndpoints` se debe llamar a,, y en el orden mostrado en el código anterior.

Para obtener más información sobre `:::no-loc(Identity):::Options` y `Startup` , vea <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Options> e inicio de la [aplicación](xref:fundamentals/startup).

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a>Scaffold registro, Inicio de sesión, cierre de sesión y RegisterConfirmation

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Agregue los `Register` `Login` archivos,, `LogOut` y `RegisterConfirmation` . Siga la [identidad de scaffolding en un :::no-loc(Razor)::: proyecto con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instrucciones de autorización para generar el código que se muestra en esta sección.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

Si ha creado el proyecto con el nombre **WebApp1**, ejecute los siguientes comandos. De lo contrario, use el espacio de nombres correcto para `ApplicationDbContext` :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

PowerShell usa punto y coma como separador de comandos. Al usar PowerShell, use el carácter de escape en la lista de archivos o ponga la lista de archivos entre comillas dobles, como se muestra en el ejemplo anterior.

Para obtener más información sobre scaffolding :::no-loc(Identity)::: , vea [scaffolding Identity into a :::no-loc(Razor)::: Project with Authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).

---

### <a name="examine-register"></a>Examinar registro

Cuando un usuario hace clic en el botón **registrar** de la `Register` página, `RegisterModel.OnPostAsync` se invoca la acción. El usuario lo crea [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) en el `_userManager` objeto:

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/:::no-loc(Identity):::/UI/src/Areas/:::no-loc(Identity):::/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a>Registro

El formulario de inicio de sesión se muestra cuando:

* Se selecciona el vínculo **iniciar sesión** .
* Un usuario intenta obtener acceso a una página restringida para la que no está autorizado para acceder **o** cuando el sistema no la ha autenticado.

Cuando se envía el formulario de la página de inicio de sesión, `OnPostAsync` se llama a la acción. `PasswordSignInAsync`en el `_signInManager` objeto.

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Para obtener información acerca de cómo tomar decisiones de autorización, vea <xref:security/authorization/introduction> .

### <a name="log-out"></a>Cerrar la sesión

El vínculo de **cierre de sesión** invoca la `LogoutModel.OnPost` acción. 

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs?highlight=36)]

En el código anterior, el código `return RedirectToPage();` debe ser un redireccionamiento para que el explorador realice una nueva solicitud y se actualice la identidad del usuario.

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) borra las notificaciones del usuario almacenadas en una cookie.

Post se especifica en *pages/Shared/_LoginPartial. cshtml*:

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a>Muestre:::no-loc(Identity):::

Las plantillas predeterminadas del proyecto web permiten el acceso anónimo a las páginas principales. Para probar :::no-loc(Identity)::: , agregue [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

Si ha iniciado sesión, cierre la sesión. Ejecute la aplicación y seleccione el vínculo de **privacidad** . Se le redirige a la página de inicio de sesión.

### <a name="explore-no-locidentity"></a>Visite:::no-loc(Identity):::

Para explorar :::no-loc(Identity)::: con más detalle:

* [Crear origen de la interfaz de usuario de identidad completa](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Examine el origen de cada página y recorra el depurador.

## <a name="no-locidentity-components"></a>:::no-loc(Identity):::Componentes

Todos los :::no-loc(Identity)::: paquetes de NuGet dependientes de se incluyen en el [ASP.net Core marco de trabajo compartido](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).

El paquete principal de :::no-loc(Identity)::: es [Microsoft. AspNetCore. :::no-loc(Identity)::: ](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/). Este paquete contiene el conjunto principal de interfaces para ASP.NET Core :::no-loc(Identity)::: y se incluye en `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .

## <a name="migrating-to-aspnet-core-no-locidentity"></a>Migrar a ASP.NET Core:::no-loc(Identity):::

Para obtener más información e instrucciones sobre cómo migrar el :::no-loc(Identity)::: almacén existente, vea [migrar :::no-loc(Identity)::: la autenticación y ](xref:migration/identity).

## <a name="setting-password-strength"></a>Establecer la seguridad de la contraseña

Consulte [configuración](#pw) para obtener un ejemplo que establece los requisitos mínimos de contraseña.

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a>AddDefault :::no-loc(Identity)::: y agregar:::no-loc(Identity):::

<xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*>se presentó en ASP.NET Core 2,1. Llamar `AddDefault:::no-loc(Identity):::` a es similar a llamar a lo siguiente:

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

Vea [ :::no-loc(Identity)::: origen de AddDefault](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) para obtener más información.

## <a name="prevent-publish-of-static-no-locidentity-assets"></a>Impedir la publicación de :::no-loc(Identity)::: recursos estáticos

Para evitar la publicación de recursos estáticos :::no-loc(Identity)::: (hojas de estilos y archivos JavaScript para :::no-loc(Identity)::: la interfaz de usuario) en la raíz Web, agregue la siguiente `ResolveStaticWebAssetsInputsDependsOn` propiedad y `Remove:::no-loc(Identity):::Assets` destino al archivo de proyecto de la aplicación:

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>Remove:::no-loc(Identity):::Assets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="Remove:::no-loc(Identity):::Assets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.:::no-loc(Identity):::.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a>Pasos siguientes

* [:::no-loc(Identity):::Código fuente de ASP.net Core](https://github.com/dotnet/aspnetcore/tree/master/src/:::no-loc(Identity):::)
* Consulte [este problema de github](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obtener información sobre cómo configurar el :::no-loc(Identity)::: uso de SQLite.
* [Configurar :::no-loc(Identity):::](xref:security/authentication/identity-configuration)
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

ASP.NET Core :::no-loc(Identity)::: es un sistema de pertenencia que agrega funcionalidad de inicio de sesión a ASP.net Core aplicaciones. Los usuarios pueden crear una cuenta con la información de inicio de sesión almacenada en :::no-loc(Identity)::: o pueden utilizar un proveedor de inicio de sesión externo. Entre los proveedores de inicio de sesión externos admitidos se incluyen [Facebook, Google, cuenta de Microsoft y Twitter](xref:security/authentication/social/index).

:::no-loc(Identity):::se puede configurar mediante una base de datos de SQL Server para almacenar nombres de usuario, contraseñas y datos de perfil. Como alternativa, se puede usar otro almacén persistente, por ejemplo, Azure Table Storage.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-:::no-loc(Identity):::DemoComplete/) ([Cómo descargar)](xref:index#how-to-download-a-sample)).

En este tema, aprenderá a usar :::no-loc(Identity)::: para registrar, iniciar sesión y cerrar la sesión de un usuario. Para obtener instrucciones más detalladas sobre la creación de aplicaciones que usan :::no-loc(Identity)::: , consulte la sección pasos siguientes al final de este artículo.

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a>AddDefault :::no-loc(Identity)::: y agregar:::no-loc(Identity):::

<xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*>se presentó en ASP.NET Core 2,1. Llamar `AddDefault:::no-loc(Identity):::` a es similar a llamar a lo siguiente:

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

Vea [ :::no-loc(Identity)::: origen de AddDefault](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) para obtener más información.

## <a name="create-a-web-app-with-authentication"></a>Creación de una aplicación web con autenticación

Cree un proyecto de aplicación Web de ASP.NET Core con cuentas de usuario individuales.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Seleccione **archivo** > **nuevo** > **proyecto**.
* Seleccione **Aplicación web de ASP.NET Core**. Asigne al proyecto el nombre **WebApp1** para que tenga el mismo espacio de nombres que la descarga del proyecto. Haga clic en **OK**.
* Seleccione una **aplicación web**de ASP.net Core y, a continuación, seleccione **cambiar autenticación**.
* Seleccione **cuentas de usuario individuales** y haga clic en **Aceptar**.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

El proyecto generado proporciona [ASP.net Core :::no-loc(Identity)::: ](xref:security/authentication/identity) como [ :::no-loc(Razor)::: biblioteca de clases](xref:razor-pages/ui-class). La :::no-loc(Identity)::: :::no-loc(Razor)::: biblioteca de clases expone extremos con el `:::no-loc(Identity):::` área. Por ejemplo:

* /:::no-loc(Identity):::/Account/Login
* /:::no-loc(Identity):::/Account/Logout
* /:::no-loc(Identity):::/Account/Manage

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

### <a name="configure-no-locidentity-services"></a>Configurar :::no-loc(Identity)::: servicios

Los servicios se agregan en `ConfigureServices` . El patrón habitual consiste en llamar a todos los métodos `Add{Service}` y, luego, a todos los métodos `services.Configure{Service}`.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

El código anterior configura :::no-loc(Identity)::: con valores de opción predeterminados. Los servicios se ponen a disposición de la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).

:::no-loc(Identity):::se habilita llamando a [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_). `UseAuthentication`agrega [middleware](xref:fundamentals/middleware/index) de autenticación a la canalización de solicitudes.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

Para obtener más información, vea la [ :::no-loc(Identity)::: clase de opciones](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) y el inicio de la [aplicación](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Registro de scaffolding, Inicio de sesión y cierre de sesión

Siga la [identidad de scaffolding en un :::no-loc(Razor)::: proyecto con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instrucciones de autorización para generar el código que se muestra en esta sección.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Agregue los archivos de registro, Inicio de sesión y cierre de sesión.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

Si ha creado el proyecto con el nombre **WebApp1**, ejecute los siguientes comandos. De lo contrario, use el espacio de nombres correcto para `ApplicationDbContext` :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell usa punto y coma como separador de comandos. Al usar PowerShell, use el carácter de escape en la lista de archivos o ponga la lista de archivos entre comillas dobles, como se muestra en el ejemplo anterior.

---

### <a name="examine-register"></a>Examinar registro

Cuando un usuario hace clic en el vínculo de **registro** , `RegisterModel.OnPostAsync` se invoca la acción. El usuario lo crea [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) en el `_userManager` objeto:

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

Si el usuario se creó correctamente, el usuario inicia sesión en la llamada a `_signInManager.SignInAsync` .

**Nota:** Consulte [confirmación](xref:security/authentication/accconfirm#prevent-login-at-registration) de la cuenta para conocer los pasos para evitar el inicio de sesión inmediato en el registro.

### <a name="log-in"></a>Registro

El formulario de inicio de sesión se muestra cuando:

* Se selecciona el vínculo **iniciar sesión** .
* Un usuario intenta obtener acceso a una página restringida para la que no está autorizado para acceder **o** cuando el sistema no la ha autenticado.

Cuando se envía el formulario de la página de inicio de sesión, `OnPostAsync` se llama a la acción. `PasswordSignInAsync`en el `_signInManager` objeto.

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Para obtener información acerca de cómo tomar decisiones de autorización, vea <xref:security/authorization/introduction> .

### <a name="log-out"></a>Cerrar la sesión

El vínculo de **cierre de sesión** invoca la `LogoutModel.OnPost` acción. 

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) borra las notificaciones del usuario almacenadas en una cookie.

Post se especifica en *pages/Shared/_LoginPartial. cshtml*:

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a>Muestre:::no-loc(Identity):::

Las plantillas predeterminadas del proyecto web permiten el acceso anónimo a las páginas principales. Para probar :::no-loc(Identity)::: , agregue [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) a la página de privacidad.

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

Si ha iniciado sesión, cierre la sesión. Ejecute la aplicación y seleccione el vínculo de **privacidad** . Se le redirige a la página de inicio de sesión.

### <a name="explore-no-locidentity"></a>Visite:::no-loc(Identity):::

Para explorar :::no-loc(Identity)::: con más detalle:

* [Crear origen de la interfaz de usuario de identidad completa](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Examine el origen de cada página y recorra el depurador.

## <a name="no-locidentity-components"></a>:::no-loc(Identity):::Componentes

Todos los :::no-loc(Identity)::: paquetes de NuGet dependientes se incluyen en el [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).

El paquete principal de :::no-loc(Identity)::: es [Microsoft. AspNetCore. :::no-loc(Identity)::: ](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/). Este paquete contiene el conjunto principal de interfaces para ASP.NET Core :::no-loc(Identity)::: y se incluye en `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .

## <a name="migrating-to-aspnet-core-no-locidentity"></a>Migrar a ASP.NET Core:::no-loc(Identity):::

Para obtener más información e instrucciones sobre cómo migrar el :::no-loc(Identity)::: almacén existente, vea [migrar :::no-loc(Identity)::: la autenticación y ](xref:migration/identity).

## <a name="setting-password-strength"></a>Establecer la seguridad de la contraseña

Consulte [configuración](#pw) para obtener un ejemplo que establece los requisitos mínimos de contraseña.

## <a name="next-steps"></a>Pasos siguientes

* Consulte [este problema de github](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obtener información sobre cómo configurar el :::no-loc(Identity)::: uso de SQLite.
* [Configurar :::no-loc(Identity):::](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
