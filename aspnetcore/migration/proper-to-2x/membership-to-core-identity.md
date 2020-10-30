---
title: 'Migración de la autenticación de pertenencia de ASP.NET a ASP.NET Core 2,0 :::no-loc(Identity):::'
author: isaac2004
description: 'Obtenga información sobre cómo migrar aplicaciones existentes de ASP.NET mediante la autenticación de pertenencia a ASP.NET Core 2,0 :::no-loc(Identity)::: .'
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
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
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: d981c424fd2d6cad95b9164420f093672325c347
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051361"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-no-locidentity"></a><span data-ttu-id="d2b5f-103">Migración de la autenticación de pertenencia de ASP.NET a ASP.NET Core 2,0 :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="d2b5f-103">Migrate from ASP.NET Membership authentication to ASP.NET Core 2.0 :::no-loc(Identity):::</span></span>

<span data-ttu-id="d2b5f-104">Por [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="d2b5f-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="d2b5f-105">En este artículo se muestra cómo migrar el esquema de la base de datos para aplicaciones ASP.NET que usan la autenticación de pertenencia a ASP.NET Core 2,0 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="d2b5f-105">This article demonstrates migrating the database schema for ASP.NET apps using Membership authentication to ASP.NET Core 2.0 :::no-loc(Identity):::.</span></span>

> [!NOTE]
> <span data-ttu-id="d2b5f-106">En este documento se proporcionan los pasos necesarios para migrar el esquema de la base de datos para las aplicaciones basadas en la pertenencia a ASP.NET al esquema de base de datos usado para :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="d2b5f-106">This document provides the steps needed to migrate the database schema for ASP.NET Membership-based apps to the database schema used for :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="d2b5f-107">Para obtener más información sobre la migración de la autenticación basada en pertenencia de ASP.NET a ASP.NET :::no-loc(Identity)::: , vea [migrar una aplicación existente de :::no-loc(Identity)::: pertenencia a SQL a ASP.net ](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span><span class="sxs-lookup"><span data-stu-id="d2b5f-107">For more information about migrating from ASP.NET Membership-based authentication to ASP.NET :::no-loc(Identity):::, see [Migrate an existing app from SQL Membership to ASP.NET :::no-loc(Identity):::](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span></span> <span data-ttu-id="d2b5f-108">Para obtener más información acerca de :::no-loc(ASP.NET Core Identity)::: , consulte [Introducción a :::no-loc(Identity)::: en ASP.net Core](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="d2b5f-108">For more information about :::no-loc(ASP.NET Core Identity):::, see [Introduction to :::no-loc(Identity)::: on ASP.NET Core](xref:security/authentication/identity).</span></span>

## <a name="review-of-membership-schema"></a><span data-ttu-id="d2b5f-109">Revisión del esquema de pertenencia</span><span class="sxs-lookup"><span data-stu-id="d2b5f-109">Review of Membership schema</span></span>

<span data-ttu-id="d2b5f-110">Antes de ASP.NET 2,0, los desarrolladores tenían que crear todo el proceso de autenticación y autorización para sus aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-110">Prior to ASP.NET 2.0, developers were tasked with creating the entire authentication and authorization process for their apps.</span></span> <span data-ttu-id="d2b5f-111">Con ASP.NET 2,0, se presentó la pertenencia, que proporciona una solución reutilizable para controlar la seguridad en las aplicaciones de ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-111">With ASP.NET 2.0, Membership was introduced, providing a boilerplate solution to handling security within ASP.NET apps.</span></span> <span data-ttu-id="d2b5f-112">Los desarrolladores ahora podían arrancar un esquema en una base de datos de SQL Server con el <https://docs.microsoft.com/previous-versions/ms229862(v=vs.140)> comando.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-112">Developers were now able to bootstrap a schema into a SQL Server database with the <https://docs.microsoft.com/previous-versions/ms229862(v=vs.140)> command.</span></span> <span data-ttu-id="d2b5f-113">Después de ejecutar este comando, se crearon las tablas siguientes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-113">After running this command, the following tables were created in the database.</span></span>

  ![Tablas de pertenencia](identity/_static/membership-tables.png)

<span data-ttu-id="d2b5f-115">Para migrar las aplicaciones existentes a ASP.NET Core 2,0 :::no-loc(Identity)::: , los datos de estas tablas deben migrarse a las tablas que utiliza el nuevo :::no-loc(Identity)::: esquema.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-115">To migrate existing apps to ASP.NET Core 2.0 :::no-loc(Identity):::, the data in these tables needs to be migrated to the tables used by the new :::no-loc(Identity)::: schema.</span></span>

## <a name="no-locaspnet-core-identity-20-schema"></a><span data-ttu-id="d2b5f-116">:::no-loc(ASP.NET Core Identity)::: esquema 2,0</span><span class="sxs-lookup"><span data-stu-id="d2b5f-116">:::no-loc(ASP.NET Core Identity)::: 2.0 schema</span></span>

<span data-ttu-id="d2b5f-117">ASP.NET Core 2,0 sigue el [:::no-loc(Identity):::](/aspnet/identity/index) principio incluido en ASP.NET 4,5.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-117">ASP.NET Core 2.0 follows the [:::no-loc(Identity):::](/aspnet/identity/index) principle introduced in ASP.NET 4.5.</span></span> <span data-ttu-id="d2b5f-118">Aunque se comparte el principio, la implementación entre los marcos de trabajo es diferente, incluso entre las versiones de ASP.NET Core (consulte migración de la [autenticación y :::no-loc(Identity)::: a ASP.net Core 2,0](xref:migration/1x-to-2x/index)).</span><span class="sxs-lookup"><span data-stu-id="d2b5f-118">Though the principle is shared, the implementation between the frameworks is different, even between versions of ASP.NET Core (see [Migrate authentication and :::no-loc(Identity)::: to ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span></span>

<span data-ttu-id="d2b5f-119">La manera más rápida de ver el esquema para ASP.NET Core 2,0 :::no-loc(Identity)::: es crear una nueva aplicación de ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-119">The fastest way to view the schema for ASP.NET Core 2.0 :::no-loc(Identity)::: is to create a new ASP.NET Core 2.0 app.</span></span> <span data-ttu-id="d2b5f-120">Siga estos pasos en Visual Studio 2017:</span><span class="sxs-lookup"><span data-stu-id="d2b5f-120">Follow these steps in Visual Studio 2017:</span></span>

1. <span data-ttu-id="d2b5f-121">Seleccione **File (Archivo)**  > **New (Nuevo)**  > **Project (Proyecto)** .</span><span class="sxs-lookup"><span data-stu-id="d2b5f-121">Select **File** > **New** > **Project** .</span></span>
1. <span data-ttu-id="d2b5f-122">Cree un nuevo proyecto de **aplicación Web de ASP.net Core** llamado *Core :::no-loc(Identity)::: Sample* .</span><span class="sxs-lookup"><span data-stu-id="d2b5f-122">Create a new **ASP.NET Core Web Application** project named *Core:::no-loc(Identity):::Sample* .</span></span>
1. <span data-ttu-id="d2b5f-123">Seleccione **ASP.NET Core 2,0** en la lista desplegable y, a continuación, seleccione **aplicación web** .</span><span class="sxs-lookup"><span data-stu-id="d2b5f-123">Select **ASP.NET Core 2.0** in the dropdown and then select **Web Application** .</span></span> <span data-ttu-id="d2b5f-124">Esta plantilla crea una aplicación de [ :::no-loc(Razor)::: páginas](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="d2b5f-124">This template produces a [:::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="d2b5f-125">Antes de hacer clic en **Aceptar** , haga clic en **cambiar autenticación** .</span><span class="sxs-lookup"><span data-stu-id="d2b5f-125">Before clicking **OK** , click **Change Authentication** .</span></span>
1. <span data-ttu-id="d2b5f-126">Elija las **cuentas de usuario individuales** para las :::no-loc(Identity)::: plantillas.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-126">Choose **Individual User Accounts** for the :::no-loc(Identity)::: templates.</span></span> <span data-ttu-id="d2b5f-127">Por último, haga clic en **Aceptar** y en **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="d2b5f-127">Finally, click **OK** , then **OK** .</span></span> <span data-ttu-id="d2b5f-128">Visual Studio crea un proyecto mediante la :::no-loc(ASP.NET Core Identity)::: plantilla.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-128">Visual Studio creates a project using the :::no-loc(ASP.NET Core Identity)::: template.</span></span>
1. <span data-ttu-id="d2b5f-129">Seleccione **herramientas** administrador  >  de **paquetes NuGet**  >  **consola del administrador** de paquetes para abrir la ventana de la consola del administrador de **paquetes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="d2b5f-129">Select **Tools** > **NuGet Package Manager** > **Package Manager Console** to open the **Package Manager Console** (PMC) window.</span></span>
1. <span data-ttu-id="d2b5f-130">Vaya a la raíz del proyecto en PMC y ejecute el comando de [Entity Framework (EF) Core](/ef/core) `Update-Database` .</span><span class="sxs-lookup"><span data-stu-id="d2b5f-130">Navigate to the project root in PMC, and run the [Entity Framework (EF) Core](/ef/core) `Update-Database` command.</span></span>

    <span data-ttu-id="d2b5f-131">ASP.NET Core 2,0 :::no-loc(Identity)::: usa EF Core para interactuar con la base de datos que almacena los datos de autenticación.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-131">ASP.NET Core 2.0 :::no-loc(Identity)::: uses EF Core to interact with the database storing the authentication data.</span></span> <span data-ttu-id="d2b5f-132">Para que la aplicación recién creada funcione, debe haber una base de datos para almacenar estos datos.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-132">In order for the newly created app to work, there needs to be a database to store this data.</span></span> <span data-ttu-id="d2b5f-133">Después de crear una nueva aplicación, la manera más rápida de inspeccionar el esquema en un entorno de base de datos es crear la base de datos mediante [migraciones de EF Core](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="d2b5f-133">After creating a new app, the fastest way to inspect the schema in a database environment is to create the database using [EF Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="d2b5f-134">Este proceso crea una base de datos, ya sea localmente o en cualquier otro lugar, que imite ese esquema.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-134">This process creates a database, either locally or elsewhere, which mimics that schema.</span></span> <span data-ttu-id="d2b5f-135">Revise la documentación anterior para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-135">Review the preceding documentation for more information.</span></span>

    <span data-ttu-id="d2b5f-136">Los comandos de EF Core utilizan la cadena de conexión para la base de datos especificada en *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="d2b5f-136">EF Core commands use the connection string for the database specified in *:::no-loc(appsettings.json):::* .</span></span> <span data-ttu-id="d2b5f-137">La siguiente cadena de conexión tiene como destino una base de datos en *localhost* denominada *asp-net-Core-Identity* .</span><span class="sxs-lookup"><span data-stu-id="d2b5f-137">The following connection string targets a database on *localhost* named *asp-net-core-identity* .</span></span> <span data-ttu-id="d2b5f-138">En esta configuración, EF Core se configura para utilizar la `DefaultConnection` cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-138">In this setting, EF Core is configured to use the `DefaultConnection` connection string.</span></span>

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. <span data-ttu-id="d2b5f-139">Seleccione **Ver**  >  **Explorador de objetos de SQL Server** .</span><span class="sxs-lookup"><span data-stu-id="d2b5f-139">Select **View** > **SQL Server Object Explorer** .</span></span> <span data-ttu-id="d2b5f-140">Expanda el nodo correspondiente al nombre de la base de datos especificado en la `ConnectionStrings:DefaultConnection` propiedad de *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="d2b5f-140">Expand the node corresponding to the database name specified in the `ConnectionStrings:DefaultConnection` property of *:::no-loc(appsettings.json):::* .</span></span>

    <span data-ttu-id="d2b5f-141">El `Update-Database` comando creó la base de datos especificada con el esquema y los datos necesarios para la inicialización de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-141">The `Update-Database` command created the database specified with the schema and any data needed for app initialization.</span></span> <span data-ttu-id="d2b5f-142">En la imagen siguiente se muestra la estructura de tabla que se crea con los pasos anteriores.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-142">The following image depicts the table structure that's created with the preceding steps.</span></span>

    ![::: no-LOC (identidad)::: tablas](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a><span data-ttu-id="d2b5f-144">Migración del esquema</span><span class="sxs-lookup"><span data-stu-id="d2b5f-144">Migrate the schema</span></span>

<span data-ttu-id="d2b5f-145">Existen sutiles diferencias en las estructuras de tabla y los campos de pertenencia y :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="d2b5f-145">There are subtle differences in the table structures and fields for both Membership and :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="d2b5f-146">El patrón ha cambiado substancialmente en lo que respecta a la autenticación y autorización con aplicaciones ASP.NET y ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-146">The pattern has changed substantially for authentication/authorization with ASP.NET and ASP.NET Core apps.</span></span> <span data-ttu-id="d2b5f-147">Los objetos de clave que se siguen usando con :::no-loc(Identity)::: son *usuarios* y *roles* .</span><span class="sxs-lookup"><span data-stu-id="d2b5f-147">The key objects that are still used with :::no-loc(Identity)::: are *Users* and *Roles* .</span></span> <span data-ttu-id="d2b5f-148">Aquí se muestran las tablas de asignación de *usuarios* , *roles* y *UserRoles* .</span><span class="sxs-lookup"><span data-stu-id="d2b5f-148">Here are mapping tables for *Users* , *Roles* , and *UserRoles* .</span></span>

### <a name="users"></a><span data-ttu-id="d2b5f-149">Usuarios</span><span class="sxs-lookup"><span data-stu-id="d2b5f-149">Users</span></span>

|:::no-loc(Identity):::<br><span data-ttu-id="d2b5f-150">`dbo.AspNetUsers`columna ()</span><span class="sxs-lookup"><span data-stu-id="d2b5f-150">(`dbo.AspNetUsers`) column</span></span>  |<span data-ttu-id="d2b5f-151">Tipo</span><span class="sxs-lookup"><span data-stu-id="d2b5f-151">Type</span></span>     |<span data-ttu-id="d2b5f-152">Pertenencia</span><span class="sxs-lookup"><span data-stu-id="d2b5f-152">Membership</span></span><br><span data-ttu-id="d2b5f-153">`dbo.aspnet_Users`  /  `dbo.aspnet_Membership` columna ()</span><span class="sxs-lookup"><span data-stu-id="d2b5f-153">(`dbo.aspnet_Users` / `dbo.aspnet_Membership`) column</span></span>|<span data-ttu-id="d2b5f-154">Tipo</span><span class="sxs-lookup"><span data-stu-id="d2b5f-154">Type</span></span>      |
|-------------------------------------------|-----------------------------------------------------------------------|
| `Id`                            | `string`| `aspnet_Users.UserId`                                      | `string` |
| `UserName`                      | `string`| `aspnet_Users.UserName`                                    | `string` |
| `Email`                         | `string`| `aspnet_Membership.Email`                                  | `string` |
| `NormalizedUserName`            | `string`| `aspnet_Users.LoweredUserName`                             | `string` |
| `NormalizedEmail`               | `string`| `aspnet_Membership.LoweredEmail`                           | `string` |
| `PhoneNumber`                   | `string`| `aspnet_Users.MobileAlias`                                 | `string` |
| `LockoutEnabled`                | `bit`   | `aspnet_Membership.IsLockedOut`                            | `bit`    |

> [!NOTE]
> <span data-ttu-id="d2b5f-155">No todas las asignaciones de campos se parecen a las relaciones uno a uno de la pertenencia a :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="d2b5f-155">Not all the field mappings resemble one-to-one relationships from Membership to :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="d2b5f-156">La tabla anterior toma el esquema de usuario de pertenencia predeterminado y lo asigna al :::no-loc(ASP.NET Core Identity)::: esquema.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-156">The preceding table takes the default Membership User schema and maps it to the :::no-loc(ASP.NET Core Identity)::: schema.</span></span> <span data-ttu-id="d2b5f-157">Cualquier otro campo personalizado que se usara para la pertenencia debe asignarse manualmente.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-157">Any other custom fields that were used for Membership need to be mapped manually.</span></span> <span data-ttu-id="d2b5f-158">En esta asignación, no hay ninguna asignación para las contraseñas, ya que los criterios de contraseña y las sales de contraseña no se migran entre los dos.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-158">In this mapping, there's no map for passwords, as both password criteria and password salts don't migrate between the two.</span></span> <span data-ttu-id="d2b5f-159">**Se recomienda dejar la contraseña como NULL y pedir a los usuarios que restablezcan sus contraseñas.**</span><span class="sxs-lookup"><span data-stu-id="d2b5f-159">**It's recommended to leave the password as null and to ask users to reset their passwords.**</span></span> <span data-ttu-id="d2b5f-160">En :::no-loc(ASP.NET Core Identity)::: , `LockoutEnd` debe establecerse en una fecha futura si el usuario está bloqueado. Esto se muestra en el script de migración.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-160">In :::no-loc(ASP.NET Core Identity):::, `LockoutEnd` should be set to some date in the future if the user is locked out. This is shown in the migration script.</span></span>

### <a name="roles"></a><span data-ttu-id="d2b5f-161">Roles</span><span class="sxs-lookup"><span data-stu-id="d2b5f-161">Roles</span></span>

|:::no-loc(Identity):::<br><span data-ttu-id="d2b5f-162">`dbo.AspNetRoles`columna ()</span><span class="sxs-lookup"><span data-stu-id="d2b5f-162">(`dbo.AspNetRoles`) column</span></span>|<span data-ttu-id="d2b5f-163">Tipo</span><span class="sxs-lookup"><span data-stu-id="d2b5f-163">Type</span></span>|<span data-ttu-id="d2b5f-164">Pertenencia</span><span class="sxs-lookup"><span data-stu-id="d2b5f-164">Membership</span></span><br><span data-ttu-id="d2b5f-165">`dbo.aspnet_Roles`columna ()</span><span class="sxs-lookup"><span data-stu-id="d2b5f-165">(`dbo.aspnet_Roles`) column</span></span>|<span data-ttu-id="d2b5f-166">Tipo</span><span class="sxs-lookup"><span data-stu-id="d2b5f-166">Type</span></span>|
|----------------------------------------|-----------------------------------|
|`Id`                           |`string`|`RoleId`         | `string`        |
|`Name`                         |`string`|`RoleName`       | `string`        |
|`NormalizedName`               |`string`|`LoweredRoleName`| `string`        |

### <a name="user-roles"></a><span data-ttu-id="d2b5f-167">Roles de usuario</span><span class="sxs-lookup"><span data-stu-id="d2b5f-167">User Roles</span></span>

|:::no-loc(Identity):::<br><span data-ttu-id="d2b5f-168">`dbo.AspNetUserRoles`columna ()</span><span class="sxs-lookup"><span data-stu-id="d2b5f-168">(`dbo.AspNetUserRoles`) column</span></span>|<span data-ttu-id="d2b5f-169">Tipo</span><span class="sxs-lookup"><span data-stu-id="d2b5f-169">Type</span></span>|<span data-ttu-id="d2b5f-170">Pertenencia</span><span class="sxs-lookup"><span data-stu-id="d2b5f-170">Membership</span></span><br><span data-ttu-id="d2b5f-171">`dbo.aspnet_UsersInRoles`columna ()</span><span class="sxs-lookup"><span data-stu-id="d2b5f-171">(`dbo.aspnet_UsersInRoles`) column</span></span>|<span data-ttu-id="d2b5f-172">Tipo</span><span class="sxs-lookup"><span data-stu-id="d2b5f-172">Type</span></span>|
|-------------------------|----------|--------------|---------------------------|
|`RoleId`                 |`string`  |`RoleId`      |`string`                   |
|`UserId`                 |`string`  |`UserId`      |`string`                   |

<span data-ttu-id="d2b5f-173">Haga referencia a las tablas de asignación anteriores al crear un script de migración para *usuarios* y *roles* .</span><span class="sxs-lookup"><span data-stu-id="d2b5f-173">Reference the preceding mapping tables when creating a migration script for *Users* and *Roles* .</span></span> <span data-ttu-id="d2b5f-174">En el ejemplo siguiente se da por supuesto que tiene dos bases de datos en un servidor de base de datos.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-174">The following example assumes you have two databases on a database server.</span></span> <span data-ttu-id="d2b5f-175">Una base de datos contiene los datos y el esquema de pertenencia de ASP.NET existente.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-175">One database contains the existing ASP.NET Membership schema and data.</span></span> <span data-ttu-id="d2b5f-176">La otra base de datos de *:::no-loc(Identity)::: ejemplo principal* se creó con los pasos descritos anteriormente.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-176">The other *Core:::no-loc(Identity):::Sample* database was created using steps described earlier.</span></span> <span data-ttu-id="d2b5f-177">Los comentarios se incluyen en línea para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-177">Comments are included inline for more details.</span></span>

```sql
-- THIS SCRIPT NEEDS TO RUN FROM THE CONTEXT OF THE MEMBERSHIP DB
BEGIN TRANSACTION MigrateUsersAndRoles
USE aspnetdb

-- INSERT USERS
INSERT INTO Core:::no-loc(Identity):::Sample.dbo.AspNetUsers
            (Id,
             UserName,
             NormalizedUserName,
             PasswordHash,
             SecurityStamp,
             EmailConfirmed,
             PhoneNumber,
             PhoneNumberConfirmed,
             TwoFactorEnabled,
             LockoutEnd,
             LockoutEnabled,
             AccessFailedCount,
             Email,
             NormalizedEmail)
SELECT aspnet_Users.UserId,
       aspnet_Users.UserName,
       -- The NormalizedUserName value is upper case in :::no-loc(ASP.NET Core Identity):::
       UPPER(aspnet_Users.UserName),
       -- Creates an empty password since passwords don't map between the 2 schemas
       '',
       /*
        The SecurityStamp token is used to verify the state of an account and
        is subject to change at any time. It should be initialized as a new ID.
       */
       NewID(),
       /*
        EmailConfirmed is set when a new user is created and confirmed via email.
        Users must have this set during migration to reset passwords.
       */
       1,
       aspnet_Users.MobileAlias,
       CASE
         WHEN aspnet_Users.MobileAlias IS NULL THEN 0
         ELSE 1
       END,
       -- 2FA likely wasn't setup in Membership for users, so setting as false.
       0,
       CASE
         -- Setting lockout date to time in the future (1,000 years)
         WHEN aspnet_Membership.IsLockedOut = 1 THEN Dateadd(year, 1000,
                                                     Sysutcdatetime())
         ELSE NULL
       END,
       aspnet_Membership.IsLockedOut,
       /*
        AccessFailedAccount is used to track failed logins. This is stored in
        Membership in multiple columns. Setting to 0 arbitrarily.
       */
       0,
       aspnet_Membership.Email,
       -- The NormalizedEmail value is upper case in :::no-loc(ASP.NET Core Identity):::
       UPPER(aspnet_Membership.Email)
FROM   aspnet_Users
       LEFT OUTER JOIN aspnet_Membership
                    ON aspnet_Membership.ApplicationId =
                       aspnet_Users.ApplicationId
                       AND aspnet_Users.UserId = aspnet_Membership.UserId
       LEFT OUTER JOIN Core:::no-loc(Identity):::Sample.dbo.AspNetUsers
                    ON aspnet_Membership.UserId = AspNetUsers.Id
WHERE  AspNetUsers.Id IS NULL

-- INSERT ROLES
INSERT INTO Core:::no-loc(Identity):::Sample.dbo.AspNetRoles(Id, Name)
SELECT RoleId, RoleName
FROM aspnet_Roles;

-- INSERT USER ROLES
INSERT INTO Core:::no-loc(Identity):::Sample.dbo.AspNetUserRoles(UserId, RoleId)
SELECT UserId, RoleId
FROM aspnet_UsersInRoles;

IF @@ERROR <> 0
  BEGIN
    ROLLBACK TRANSACTION MigrateUsersAndRoles
    RETURN
  END

COMMIT TRANSACTION MigrateUsersAndRoles
```

<span data-ttu-id="d2b5f-178">Después de completar el script anterior, la :::no-loc(ASP.NET Core Identity)::: aplicación creada anteriormente se rellena con usuarios de pertenencia.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-178">After completion of the preceding script, the :::no-loc(ASP.NET Core Identity)::: app created earlier is populated with Membership users.</span></span> <span data-ttu-id="d2b5f-179">Los usuarios deben cambiar sus contraseñas antes de iniciar sesión.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-179">Users need to change their passwords before logging in.</span></span>

> [!NOTE]
> <span data-ttu-id="d2b5f-180">Si el sistema de pertenencia tuviera usuarios con nombres de usuario que no coincidían con su dirección de correo electrónico, se requieren cambios en la aplicación que se creó anteriormente para dar cabida a este.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-180">If the Membership system had users with user names that didn't match their email address, changes are required to the app created earlier to accommodate this.</span></span> <span data-ttu-id="d2b5f-181">La plantilla predeterminada espera `UserName` y `Email` para ser la misma.</span><span class="sxs-lookup"><span data-stu-id="d2b5f-181">The default template expects `UserName` and `Email` to be the same.</span></span> <span data-ttu-id="d2b5f-182">En situaciones en las que son diferentes, el proceso de inicio de sesión debe modificarse para usar en `UserName` lugar de `Email` .</span><span class="sxs-lookup"><span data-stu-id="d2b5f-182">For situations in which they're different, the login process needs to be modified to use `UserName` instead of `Email`.</span></span>

<span data-ttu-id="d2b5f-183">En la `PageModel` Página de inicio de sesión, que se encuentra en *Pages\Account\Login.cshtml.CS* , quite el `[EmailAddress]` atributo de la propiedad *email* .</span><span class="sxs-lookup"><span data-stu-id="d2b5f-183">In the `PageModel` of the Login Page, located at *Pages\Account\Login.cshtml.cs* , remove the `[EmailAddress]` attribute from the *Email* property.</span></span> <span data-ttu-id="d2b5f-184">Cambie el nombre por *nombre de usuario* .</span><span class="sxs-lookup"><span data-stu-id="d2b5f-184">Rename it to *UserName* .</span></span> <span data-ttu-id="d2b5f-185">Esto requiere un cambio siempre `EmailAddress` que se mencione, en la *vista* y *PageModel* .</span><span class="sxs-lookup"><span data-stu-id="d2b5f-185">This requires a change wherever `EmailAddress` is mentioned, in the *View* and *PageModel* .</span></span> <span data-ttu-id="d2b5f-186">El resultado será similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="d2b5f-186">The result looks like the following:</span></span>

 ![Inicio de sesión fijo](identity/_static/fixed-login.png)

## <a name="next-steps"></a><span data-ttu-id="d2b5f-188">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="d2b5f-188">Next steps</span></span>

<span data-ttu-id="d2b5f-189">En este tutorial, ha aprendido cómo trasladar a los usuarios de la pertenencia de SQL a ASP.NET Core 2,0 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="d2b5f-189">In this tutorial, you learned how to port users from SQL membership to ASP.NET Core 2.0 :::no-loc(Identity):::.</span></span> <span data-ttu-id="d2b5f-190">Para obtener más información sobre :::no-loc(ASP.NET Core Identity)::: , vea [Introducción :::no-loc(Identity)::: a ](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="d2b5f-190">For more information regarding :::no-loc(ASP.NET Core Identity):::, see [Introduction to :::no-loc(Identity):::](xref:security/authentication/identity).</span></span>
