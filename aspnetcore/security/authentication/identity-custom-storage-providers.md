---
title: Proveedores de almacenamiento personalizados para ASP.NET Core Identity
author: ardalis
description: Aprenda a configurar proveedores de almacenamiento personalizados para ASP.NET Core Identity .
ms.author: riande
ms.custom: mvc
ms.date: 07/23/2019
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
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: f1c4366e4e4afa3dd86a816a649ad0a8b2ce817b
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588632"
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a>Proveedores de almacenamiento personalizados para ASP.NET Core Identity

Por [Steve Smith](https://ardalis.com/)

ASP.NET Core Identity es un sistema extensible que permite crear un proveedor de almacenamiento personalizado y conectarlo a la aplicación. En este tema se describe cómo crear un proveedor de almacenamiento personalizado para ASP.NET Core Identity . Trata los conceptos importantes para crear su propio proveedor de almacenamiento, pero no es un tutorial paso a paso.

[Vea o descargue el ejemplo de GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample).

## <a name="introduction"></a>Introducción

De forma predeterminada, el ASP.NET Core Identity sistema almacena información de usuario en una base de datos de SQL Server mediante Entity Framework Core. En muchas aplicaciones, este enfoque funciona bien. Sin embargo, es posible que prefiera usar un mecanismo de persistencia o un esquema de datos diferente. Por ejemplo:

* Use [Azure Table Storage](/azure/storage/) u otro almacén de datos.
* Las tablas de base de datos tienen una estructura diferente. 
* Puede que desee usar un enfoque de acceso a datos diferente, como [Dapper](https://github.com/StackExchange/Dapper). 

En cada uno de estos casos, puede escribir un proveedor personalizado para su mecanismo de almacenamiento y conectarlo a la aplicación.

ASP.NET Core Identity se incluye en las plantillas de proyecto de Visual Studio con la opción "cuentas de usuario individuales".

Al usar el CLI de .NET Core, agregue `-au Individual` :

```dotnetcli
dotnet new mvc -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a>La ASP.NET Core Identity arquitectura

ASP.NET Core Identity consta de clases denominadas administradores y almacenes. Los *administradores* son clases de alto nivel que un desarrollador de aplicaciones usa para realizar operaciones, como la creación de un Identity usuario. Los *almacenes* son clases de nivel inferior que especifican cómo se conservan las entidades, como usuarios y roles. Los almacenes siguen el patrón del repositorio y se acoplan estrechamente con el mecanismo de persistencia. Los administradores se desacoplan de las tiendas, lo que significa que puede reemplazar el mecanismo de persistencia sin cambiar el código de aplicación (excepto para la configuración).

En el diagrama siguiente se muestra cómo interactúa una aplicación web con los administradores mientras los almacenes interactúan con la capa de acceso a datos.

![ASP.NET Core aplicaciones funcionan con los administradores (por ejemplo, "UserManager", "RoleManager"). Los administradores trabajan con almacenes (por ejemplo, "UserStore") que se comunican con un origen de datos mediante una biblioteca como Entity Framework Core.](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

Para crear un proveedor de almacenamiento personalizado, cree el origen de datos, la capa de acceso a datos y las clases de almacén que interactúan con esta capa de acceso a datos (los cuadros verde y gris del diagrama anterior). No es necesario personalizar los administradores o el código de aplicación que interactúa con ellos (los cuadros azules anteriores).

Al crear una nueva instancia de `UserManager` o `RoleManager` se proporciona el tipo de la clase de usuario y se pasa una instancia de la clase Store como argumento. Este enfoque le permite conectar las clases personalizadas en ASP.NET Core. 

[Volver a configurar la aplicación para usar el nuevo proveedor de almacenamiento](#reconfigure-app-to-use-a-new-storage-provider) muestra cómo crear una instancia de `UserManager` y `RoleManager` con un almacén personalizado.

## <a name="aspnet-core-identity-stores-data-types"></a>ASP.NET Core Identity almacena tipos de datos

[ASP.NET Core Identity](https://github.com/aspnet/identity) los tipos de datos se detallan en las secciones siguientes:

### <a name="users"></a>Usuarios

Usuarios registrados de su sitio Web. El tipo de [ Identity usuario](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser) puede extenderse o usarse como ejemplo para su propio tipo personalizado. No es necesario heredar de un tipo determinado para implementar su propia solución personalizada de almacenamiento de identidades.

### <a name="user-claims"></a>Notificaciones de usuario

Un conjunto de instrucciones (o [notificaciones](/dotnet/api/system.security.claims.claim)) sobre el usuario que representa la identidad del usuario. Puede habilitar una expresión mayor de la identidad del usuario que se puede lograr a través de los roles.

### <a name="user-logins"></a>Inicios de sesión de usuario

Información sobre el proveedor de autenticación externo (como Facebook o un cuenta Microsoft) que se va a usar al iniciar sesión en un usuario. [Ejemplo](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a>Roles

Grupos de autorización para el sitio. Incluye el identificador de rol y el nombre de rol (por ejemplo, "admin" o "Employee"). [Ejemplo](/dotnet/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a>Capa de acceso a datos

En este tema se da por supuesto que está familiarizado con el mecanismo de persistencia que va a usar y cómo crear entidades para ese mecanismo. En este tema no se proporcionan detalles sobre cómo crear los repositorios o las clases de acceso a datos. proporciona algunas sugerencias sobre las decisiones de diseño al trabajar con ASP.NET Core Identity .

Tiene una gran libertad al diseñar el nivel de acceso a datos para un proveedor de almacenamiento personalizado. Solo tiene que crear mecanismos de persistencia para las características que desea utilizar en la aplicación. Por ejemplo, si no usa roles en la aplicación, no es necesario crear almacenamiento para roles o asociaciones de rol de usuario. La tecnología y la infraestructura existente pueden requerir una estructura muy diferente de la implementación predeterminada de ASP.NET Core Identity . En la capa de acceso a datos, se proporciona la lógica para trabajar con la estructura de la implementación del almacenamiento.

La capa de acceso a datos proporciona la lógica para guardar los datos de ASP.NET Core Identity en un origen de datos. La capa de acceso a datos para el proveedor de almacenamiento personalizado podría incluir las siguientes clases para almacenar información de usuarios y roles.

### <a name="context-class"></a>Context (clase)

Encapsula la información para conectarse al mecanismo de persistencia y ejecutar consultas. Varias clases de datos requieren una instancia de esta clase, que normalmente se proporciona a través de la inserción de dependencias. [Ejemplo](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1).

### <a name="user-storage"></a>Almacenamiento de usuario

Almacena y recupera información de usuario (como el nombre de usuario y el hash de contraseña). [Ejemplo](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a>Almacenamiento de rol

Almacena y recupera información de roles (por ejemplo, el nombre del rol). [Ejemplo](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a>Almacenamiento de UserClaims

Almacena y recupera información de notificaciones de usuario (por ejemplo, el tipo y el valor de la demanda). [Ejemplo](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a>Almacenamiento de UserLogins

Almacena y recupera información de inicio de sesión del usuario (por ejemplo, un proveedor de autenticación externo). [Ejemplo](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a>Almacenamiento de UserRole

Almacena y recupera los roles asignados a los usuarios. [Ejemplo](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

**Sugerencia:** Solo se implementan las clases que se van a usar en la aplicación.

En las clases de acceso a datos, proporcione código para realizar operaciones de datos para el mecanismo de persistencia. Por ejemplo, en un proveedor personalizado, puede que tenga el código siguiente para crear un nuevo usuario en la clase *Store* :

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

La lógica de implementación para crear el usuario se encuentra en el `_usersTable.CreateAsync` método, que se muestra a continuación.

## <a name="customize-the-user-class"></a>Personalización de la clase de usuario

Al implementar un proveedor de almacenamiento, cree una clase de usuario que sea equivalente a la [ Identity clase User](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser).

Como mínimo, la clase de usuario debe incluir un `Id` y una `UserName` propiedad.

La `IdentityUser` clase define las propiedades a las que `UserManager` llama al realizar las operaciones solicitadas. El tipo predeterminado de la `Id` propiedad es una cadena, pero puede heredar de `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` y especificar un tipo diferente. El marco de trabajo espera que la implementación de almacenamiento controle las conversiones de tipos de datos.

## <a name="customize-the-user-store"></a>Personalización del almacén de usuarios

Cree una `UserStore` clase que proporcione los métodos para todas las operaciones de datos del usuario. Esta clase es equivalente a la [clase &lt; UserStore &gt; TUser](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) . En la `UserStore` clase, implemente `IUserStore<TUser>` y las interfaces opcionales necesarias. Seleccione las interfaces opcionales que se implementarán en función de la funcionalidad proporcionada en la aplicación.

### <a name="optional-interfaces"></a>Interfaces opcionales

* [IUserRoleStore](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)
* [IUserClaimStore](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)
* [IUserPasswordStore](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)
* [IUserSecurityStampStore](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)
* [IUserEmailStore](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)
* [IUserPhoneNumberStore](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)
* [IQueryableUserStore](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)
* [IUserLoginStore](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)
* [IUserTwoFactorStore](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)
* [IUserLockoutStore](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)

Las interfaces opcionales heredan de `IUserStore<TUser>` . Puede ver un almacén de usuario de ejemplo implementado parcialmente en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs).

Dentro de la `UserStore` clase, se usan las clases de acceso a datos que se han creado para realizar operaciones. Se pasan a través de la inserción de dependencias. Por ejemplo, en la SQL Server con la implementación de Dapper, la `UserStore` clase tiene el `CreateAsync` método que usa una instancia de `DapperUsersTable` para insertar un nuevo registro:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a>Interfaces que se implementan al personalizar el almacén del usuario

* **IUserStore**  
 La [interfaz &lt; IUserStore &gt; TUser](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1) es la única interfaz que debe implementar en el almacén de usuario. Define métodos para crear, actualizar, eliminar y recuperar usuarios.
* **IUserClaimStore**  
 La [interfaz &lt; IUserClaimStore &gt; TUser](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1) define los métodos que se implementan para habilitar las notificaciones de usuario. Contiene métodos para agregar, quitar y recuperar notificaciones de usuario.
* **IUserLoginStore**  
 [IUserLoginStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1) define los métodos que se implementan para habilitar los proveedores de autenticación externos. Contiene métodos para agregar, quitar y recuperar inicios de sesión de usuario, y un método para recuperar un usuario en función de la información de inicio de sesión.
* **IUserRoleStore**  
 La [interfaz &lt; IUserRoleStore &gt; TUser](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1) define los métodos que se implementan para asignar un usuario a un rol. Contiene métodos para agregar, quitar y recuperar los roles de un usuario, y un método para comprobar si un usuario está asignado a un rol.
* **IUserPasswordStore**  
 La [interfaz &lt; IUserPasswordStore &gt; TUser](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) define los métodos que se implementan para conservar las contraseñas con hash. Contiene métodos para obtener y establecer la contraseña hash y un método que indica si el usuario ha establecido una contraseña.
* **IUserSecurityStampStore**  
 La [interfaz &lt; IUserSecurityStampStore &gt; TUser](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) define los métodos que se implementan para utilizar una marca de seguridad que indica si la información de la cuenta del usuario ha cambiado. Esta marca se actualiza cuando un usuario cambia la contraseña, o agrega o quita inicios de sesión. Contiene métodos para obtener y establecer la marca de seguridad.
* **IUserTwoFactorStore**  
 La [interfaz &lt; IUserTwoFactorStore &gt; TUser](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) define los métodos que se implementan para admitir la autenticación en dos fases. Contiene métodos para obtener y establecer si la autenticación en dos fases está habilitada para un usuario.
* **IUserPhoneNumberStore**  
 La [interfaz &lt; IUserPhoneNumberStore &gt; TUser](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) define los métodos que se implementan para almacenar los números de teléfono del usuario. Contiene métodos para obtener y establecer el número de teléfono y si se ha confirmado el número de teléfono.
* **IUserEmailStore**  
 La [interfaz &lt; IUserEmailStore &gt; TUser](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1) define los métodos que se implementan para almacenar direcciones de correo electrónico de usuario. Contiene métodos para obtener y establecer la dirección de correo electrónico y si se confirma el correo electrónico.
* **IUserLockoutStore**  
 La [interfaz &lt; IUserLockoutStore &gt; TUser](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) define los métodos que se implementan para almacenar información sobre el bloqueo de una cuenta. Contiene métodos para realizar el seguimiento de los intentos de acceso y bloqueos con errores.
* **IQueryableUserStore**  
 La [interfaz &lt; IQueryableUserStore &gt; TUser](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) define los miembros que se implementan para proporcionar un almacén de usuario consultable.

Solo se implementan las interfaces que se necesitan en la aplicación. Por ejemplo:

```csharp
public class UserStore : IUserStore<IdentityUser>,
                         IUserClaimStore<IdentityUser>,
                         IUserLoginStore<IdentityUser>,
                         IUserRoleStore<IdentityUser>,
                         IUserPasswordStore<IdentityUser>,
                         IUserSecurityStampStore<IdentityUser>
{
    // interface implementations not shown
}
```

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a>IdentityUserClaim, Identity UserLogin y Identity UserRole

El `Microsoft.AspNet.Identity.EntityFramework` espacio de nombres contiene implementaciones de las clases [ Identity UserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [ Identity UserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)y [ Identity UserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) . Si usa estas características, es posible que quiera crear sus propias versiones de estas clases y definir las propiedades de la aplicación. Sin embargo, a veces es más eficaz no cargar estas entidades en la memoria al realizar operaciones básicas (como agregar o quitar la demanda de un usuario). En su lugar, las clases de almacén de back-end pueden ejecutar estas operaciones directamente en el origen de datos. Por ejemplo, el `UserStore.GetClaimsAsync` método puede llamar al `userClaimTable.FindByUserId(user.Id)` método para ejecutar una consulta en esa tabla directamente y devolver una lista de notificaciones.

## <a name="customize-the-role-class"></a>Personalización de la clase role

Al implementar un proveedor de almacenamiento de rol, puede crear un tipo de rol personalizado. No es necesario que implemente una interfaz determinada, pero debe tener `Id` y, normalmente, tendrá una `Name` propiedad.

La siguiente es una clase de rol de ejemplo:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a>Personalización del almacén de roles

Puede crear una `RoleStore` clase que proporcione los métodos para todas las operaciones de datos en roles. Esta clase es equivalente a la clase [RoleStore de &lt; Comtrol &gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) . En la `RoleStore` clase, implementa `IRoleStore<TRole>` y, opcionalmente, la `IQueryableRoleStore<TRole>` interfaz.

* **IRoleStore &lt;&gt;**  
 La interfaz de [IRoleStore &lt; trol &gt; ](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1) define los métodos que se van a implementar en la clase de almacén de roles. Contiene métodos para crear, actualizar, eliminar y recuperar roles.
* **RoleStore &lt;&gt;**  
 Para personalizar `RoleStore` , cree una clase que implemente la `IRoleStore<TRole>` interfaz. 

## <a name="reconfigure-app-to-use-a-new-storage-provider"></a>Volver a configurar la aplicación para que use un nuevo proveedor de almacenamiento

Una vez que haya implementado un proveedor de almacenamiento, configure la aplicación para que lo use. Si la aplicación usó el proveedor predeterminado, reemplácelo por el proveedor personalizado.

1. Quite el `Microsoft.AspNetCore.EntityFramework.Identity` paquete NuGet.
1. Si el proveedor de almacenamiento reside en un proyecto o paquete independiente, agregue una referencia a él.
1. Reemplace todas las referencias a `Microsoft.AspNetCore.EntityFramework.Identity` por una instrucción using para el espacio de nombres de su proveedor de almacenamiento.
1. En el `ConfigureServices` método, cambie el `AddIdentity` método para utilizar los tipos personalizados. Puede crear sus propios métodos de extensión para este fin. Vea [ Identity ServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) para obtener un ejemplo.
1. Si está utilizando roles, actualice `RoleManager` para que use la `RoleStore` clase.
1. Actualice la cadena de conexión y las credenciales a la configuración de la aplicación.

Ejemplo:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add identity types
    services.AddIdentity<ApplicationUser, ApplicationRole>()
        .AddDefaultTokenProviders();

    // Identity Services
    services.AddTransient<IUserStore<ApplicationUser>, CustomUserStore>();
    services.AddTransient<IRoleStore<ApplicationRole>, CustomRoleStore>();
    string connectionString = Configuration.GetConnectionString("DefaultConnection");
    services.AddTransient<SqlConnection>(e => new SqlConnection(connectionString));
    services.AddTransient<DapperUsersTable>();

    // additional configuration
}
```

## <a name="references"></a>Referencias

* [Proveedores de almacenamiento personalizados para ASP.NET 4. x Identity](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
* [ASP.NET Core Identity](https://github.com/dotnet/AspNetCore/tree/main/src/Identity): Este repositorio incluye vínculos a proveedores de almacenes de mantenidos por la comunidad.
