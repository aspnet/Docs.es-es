---
title: Agregar, descargar y eliminar datos de usuario en Identity un proyecto de ASP.net Core
author: rick-anderson
description: Obtenga información sobre cómo agregar datos de usuario personalizados a Identity en un proyecto de ASP.net Core. Elimine los datos por RGPD.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
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
uid: security/authentication/add-user-data
ms.openlocfilehash: a4e1fd780947cfa5f09fb1e03964595fa09f0f18
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061423"
---
# <a name="add-download-and-delete-custom-user-data-to-no-locidentity-in-an-aspnet-core-project"></a>Agregar, descargar y eliminar datos de usuario personalizados Identity en un proyecto de ASP.net Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

En este artículo se muestra cómo:

* Agregue datos de usuario personalizados a una aplicación Web de ASP.NET Core.
* Marque el modelo de datos de usuario personalizado con el <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> atributo para que esté disponible automáticamente para su descarga y eliminación. Hacer que los datos se puedan descargar y eliminar ayuda a cumplir los requisitos de [RGPD](xref:security/gdpr) .

El ejemplo de proyecto se crea a partir de una Razor aplicación Web de páginas, pero las instrucciones son similares para una aplicación web MVC ASP.net Core.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Requisitos previos

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-no-locrazor-web-app"></a>Creación de una Razor aplicación Web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto** . Asigne al proyecto el nombre **WebApp1** si desea que coincida con el espacio de nombres del código de [ejemplo de descarga](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .
* Seleccionar **ASP.net Core aplicación web** > **correcta**
* Seleccione **ASP.NET Core 3,0** en la lista desplegable.
* Seleccionar **aplicación web** > **correcta**
* Compile y ejecute el proyecto.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto** . Asigne al proyecto el nombre **WebApp1** si desea que coincida con el espacio de nombres del código de [ejemplo de descarga](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .
* Seleccionar **ASP.net Core aplicación web** > **correcta**
* Seleccione **ASP.NET Core 2,2** en la lista desplegable.
* Seleccionar **aplicación web** > **correcta**
* Compile y ejecute el proyecto.

::: moniker-end


# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-no-locidentity-scaffolder"></a>Ejecutar el Identity scaffolding

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* En **Explorador de soluciones** , haga clic con el botón derecho en el proyecto > **Agregar**  >  **nuevo elemento con scaffolding** .
* En el panel izquierdo del cuadro de diálogo **Agregar scaffold** , seleccione **Identity**  >  **Agregar** .
* En el cuadro de diálogo **agregar Identity** , las siguientes opciones:
  * Seleccione el archivo de diseño existente  *~/Pages/Shared/_Layout. cshtml*
  * Seleccione los siguientes archivos para invalidar:
    * **Cuenta/registro**
    * **Cuenta/administración/índice**
  * Seleccione el **+** botón para crear una nueva **clase de contexto de datos** . Acepte el tipo ( **WebApp1. Models. WebApp1Context** si el proyecto se denomina **WebApp1** ).
  * Seleccione el **+** botón para crear una nueva **clase de usuario** . Acepte el tipo ( **WebApp1User** si el proyecto se denomina **WebApp1** ) > **Agregar** .
* Seleccione **Agregar** .

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

Si no ha instalado previamente el scaffolding de ASP.NET Core, instálelo ahora:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Agregue una referencia de paquete a [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) al archivo de proyecto (. csproj). Ejecute el siguiente comando en el directorio del proyecto:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Ejecute el siguiente comando para enumerar las Identity Opciones del scaffolding:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

En la carpeta del proyecto, ejecute el Identity scaffolding:

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

Siga las instrucciones de [migraciones, UseAuthentication y diseño](xref:security/authentication/scaffold-identity#efm) para realizar los pasos siguientes:

* Cree una migración y actualice la base de datos.
* Agregue `UseAuthentication` a `Startup.Configure`.
* Agregue `<partial name="_LoginPartial" />` al archivo de diseño.
* Pruebe la aplicación:
  * Registrar un usuario
  * Seleccione el nuevo nombre de usuario (junto al vínculo de **cierre de sesión** ). Es posible que necesite expandir la ventana o seleccionar el icono de la barra de navegación para mostrar el nombre de usuario y otros vínculos.
  * Seleccione la pestaña **datos personales** .
  * Seleccione el botón **Descargar** y examine el *PersonalData.jsen* el archivo.
  * Pruebe el botón **eliminar** , que elimina el usuario que ha iniciado sesión.

## <a name="add-custom-user-data-to-the-no-locidentity-db"></a>Agregar datos de usuario personalizados a la base de datos Identity

Actualice la `IdentityUser` clase derivada con propiedades personalizadas. Si ha llamado al proyecto WebApp1, el archivo se denomina *areas/ Identity /Data/WebApp1User.CS* . Actualice el archivo con el código siguiente:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

Las propiedades con el atributo [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) son:

* Se elimina cuando la página *areas/ Identity /pages/Account/Manage/DeletePersonalData.cshtml* Razor llama a `UserManager.Delete` .
* Incluido en los datos descargados en la página *áreas/ Identity /pages/Account/Manage/DownloadPersonalData.cshtml* Razor .

### <a name="update-the-accountmanageindexcshtml-page"></a>Actualización de la página Account/Manage/index. cshtml

Actualice `InputModel` en *areas/ Identity /pages/Account/Manage/index.cshtml.CS* con el siguiente código resaltado:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

Actualice las *áreas/ Identity /pages/Account/Manage/index.cshtml* con el siguiente marcado resaltado:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

Actualice las *áreas/ Identity /pages/Account/Manage/index.cshtml* con el siguiente marcado resaltado:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>Actualización de la página cuenta/registro. cshtml

Actualice `InputModel` en *areas/ Identity /pages/Account/Register.cshtml.CS* con el siguiente código resaltado:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

Actualice las *áreas/ Identity /pages/Account/Register.cshtml* con el siguiente marcado resaltado:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

Actualice las *áreas/ Identity /pages/Account/Register.cshtml* con el siguiente marcado resaltado:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


Compile el proyecto.

### <a name="add-a-migration-for-the-custom-user-data"></a>Agregar una migración para los datos de usuario personalizados

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

En la consola del **Administrador de paquetes** de Visual Studio:

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a>Prueba crear, ver, descargar y eliminar datos de usuario personalizados

Pruebe la aplicación:

* Registra un nuevo usuario.
* Vea los datos de usuario personalizados en la `/Identity/Account/Manage` página.
* Descargue y vea los datos personales de los usuarios en la `/Identity/Account/Manage/PersonalData` página.

## <a name="add-claims-to-no-locidentity-using-iuserclaimsprincipalfactoryapplicationuser"></a>Adición de notificaciones al Identity uso de IUserClaimsPrincipalFactory<ApplicationUser>

> [!NOTE]
> Esta sección no es una extensión del tutorial anterior. Para aplicar los pasos siguientes a la aplicación compilada con el tutorial, consulte [este problema de github](https://github.com/dotnet/AspNetCore.Docs/issues/18797).

Se pueden agregar notificaciones adicionales a ASP.NET Core Identity mediante la `IUserClaimsPrincipalFactory<T>` interfaz. Esta clase se puede Agregar a la aplicación en el `Startup.ConfigureServices` método. Agregue la implementación personalizada de la clase como se indica a continuación:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

El código de demostración utiliza la `ApplicationUser` clase. Esta clase agrega una `IsAdmin` propiedad que se usa para agregar la demanda adicional.

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

`AdditionalUserClaimsPrincipalFactory` implementa la interfaz `UserClaimsPrincipalFactory`. Se agrega una nueva demanda de rol a `ClaimsPrincipal` .

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

La demanda adicional se puede usar en la aplicación. En una Razor página, la `IAuthorizationService` instancia se puede utilizar para tener acceso al valor de la demanda.

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
