---
title: Identity Personalización del modelo en ASP.NET Core
author: ajcvickers
description: En este artículo se describe cómo personalizar el modelo de datos de Entity Framework Core subyacente para ASP.NET Core Identity .
ms.author: avickers
ms.date: 07/01/2019
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
uid: security/authentication/customize_identity_model
ms.openlocfilehash: 71f532aa00c2afeeb0d6b93c01cb6a1fbd0a686c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634311"
---
# <a name="no-locidentity-model-customization-in-aspnet-core"></a><span data-ttu-id="73c02-103">Identity Personalización del modelo en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="73c02-103">Identity model customization in ASP.NET Core</span></span>

<span data-ttu-id="73c02-104">Por [Arthur Vickers](https://github.com/ajcvickers)</span><span class="sxs-lookup"><span data-stu-id="73c02-104">By [Arthur Vickers](https://github.com/ajcvickers)</span></span>

<span data-ttu-id="73c02-105">ASP.NET Core Identity proporciona un marco para administrar y almacenar cuentas de usuario en ASP.NET Core aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="73c02-105">ASP.NET Core Identity provides a framework for managing and storing user accounts in ASP.NET Core apps.</span></span> <span data-ttu-id="73c02-106">Identity se agrega al proyecto cuando se selecciona **cuentas de usuario individuales** como mecanismo de autenticación.</span><span class="sxs-lookup"><span data-stu-id="73c02-106">Identity is added to your project when **Individual User Accounts** is selected as the authentication mechanism.</span></span> <span data-ttu-id="73c02-107">De forma predeterminada, Identity hace uso de un modelo de datos básico de Entity Framework (EF).</span><span class="sxs-lookup"><span data-stu-id="73c02-107">By default, Identity makes use of an Entity Framework (EF) Core data model.</span></span> <span data-ttu-id="73c02-108">En este artículo se describe cómo personalizar el Identity modelo.</span><span class="sxs-lookup"><span data-stu-id="73c02-108">This article describes how to customize the Identity model.</span></span>

## <a name="no-locidentity-and-ef-core-migrations"></a><span data-ttu-id="73c02-109">Identity y EF Core migraciones</span><span class="sxs-lookup"><span data-stu-id="73c02-109">Identity and EF Core Migrations</span></span>

<span data-ttu-id="73c02-110">Antes de examinar el modelo, es útil entender cómo Identity funciona con migraciones de [EF Core](/ef/core/managing-schemas/migrations/) para crear y actualizar una base de datos.</span><span class="sxs-lookup"><span data-stu-id="73c02-110">Before examining the model, it's useful to understand how Identity works with [EF Core Migrations](/ef/core/managing-schemas/migrations/) to create and update a database.</span></span> <span data-ttu-id="73c02-111">En el nivel superior, el proceso es:</span><span class="sxs-lookup"><span data-stu-id="73c02-111">At the top level, the process is:</span></span>

1. <span data-ttu-id="73c02-112">Definir o actualizar un [modelo de datos en el código](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="73c02-112">Define or update a [data model in code](/ef/core/modeling/).</span></span>
1. <span data-ttu-id="73c02-113">Agregue una migración para traducir este modelo a los cambios que se pueden aplicar a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="73c02-113">Add a Migration to translate this model into changes that can be applied to the database.</span></span>
1. <span data-ttu-id="73c02-114">Compruebe que la migración representa correctamente sus intenciones.</span><span class="sxs-lookup"><span data-stu-id="73c02-114">Check that the Migration correctly represents your intentions.</span></span>
1. <span data-ttu-id="73c02-115">Aplique la migración para actualizar la base de datos y sincronizarla con el modelo.</span><span class="sxs-lookup"><span data-stu-id="73c02-115">Apply the Migration to update the database to be in sync with the model.</span></span>
1. <span data-ttu-id="73c02-116">Repita los pasos del 1 al 4 para refinar aún más el modelo y mantener la base de datos sincronizada.</span><span class="sxs-lookup"><span data-stu-id="73c02-116">Repeat steps 1 through 4 to further refine the model and keep the database in sync.</span></span>

<span data-ttu-id="73c02-117">Use uno de los métodos siguientes para agregar y aplicar migraciones:</span><span class="sxs-lookup"><span data-stu-id="73c02-117">Use one of the following approaches to add and apply Migrations:</span></span>

* <span data-ttu-id="73c02-118">La ventana de la **consola del administrador de paquetes** (PMC) si usa Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="73c02-118">The **Package Manager Console** (PMC) window if using Visual Studio.</span></span> <span data-ttu-id="73c02-119">Para obtener más información, vea [EF Core herramientas PMC](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="73c02-119">For more information, see [EF Core PMC tools](/ef/core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="73c02-120">CLI de .NET Core si se usa la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="73c02-120">The .NET Core CLI if using the command line.</span></span> <span data-ttu-id="73c02-121">Para obtener más información, vea [EF Core herramientas de línea de comandos de .net](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="73c02-121">For more information, see [EF Core .NET command line tools](/ef/core/miscellaneous/cli/dotnet).</span></span>
* <span data-ttu-id="73c02-122">Al hacer clic en el botón **aplicar migraciones** en la página de error cuando se ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="73c02-122">Clicking the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="73c02-123">ASP.NET Core tiene un controlador de página de errores en tiempo de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="73c02-123">ASP.NET Core has a development-time error page handler.</span></span> <span data-ttu-id="73c02-124">El controlador puede aplicar migraciones cuando se ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="73c02-124">The handler can apply migrations when the app is run.</span></span> <span data-ttu-id="73c02-125">Normalmente, las aplicaciones de producción generan scripts SQL desde las migraciones e implementan cambios de base de datos como parte de una implementación controlada de una aplicación y una base de datos.</span><span class="sxs-lookup"><span data-stu-id="73c02-125">Production apps typically generate SQL scripts from the migrations and deploy database changes as part of a controlled app and database deployment.</span></span>

<span data-ttu-id="73c02-126">Cuando se crea una nueva aplicación con Identity , los pasos 1 y 2 anteriores ya se han completado.</span><span class="sxs-lookup"><span data-stu-id="73c02-126">When a new app using Identity is created, steps 1 and 2 above have already been completed.</span></span> <span data-ttu-id="73c02-127">Es decir, el modelo de datos inicial ya existe y la migración inicial se ha agregado al proyecto.</span><span class="sxs-lookup"><span data-stu-id="73c02-127">That is, the initial data model already exists, and the initial migration has been added to the project.</span></span> <span data-ttu-id="73c02-128">Todavía es necesario aplicar la migración inicial a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="73c02-128">The initial migration still needs to be applied to the database.</span></span> <span data-ttu-id="73c02-129">La migración inicial puede aplicarse mediante uno de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="73c02-129">The initial migration can be applied via one of the following approaches:</span></span>

* <span data-ttu-id="73c02-130">Ejecutar `Update-Database` en PMC.</span><span class="sxs-lookup"><span data-stu-id="73c02-130">Run `Update-Database` in PMC.</span></span>
* <span data-ttu-id="73c02-131">Ejecute `dotnet ef database update` en un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="73c02-131">Run `dotnet ef database update` in a command shell.</span></span>
* <span data-ttu-id="73c02-132">Haga clic en el botón **aplicar migraciones** en la página de error cuando se ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="73c02-132">Click the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="73c02-133">Repita los pasos anteriores a medida que se realicen cambios en el modelo.</span><span class="sxs-lookup"><span data-stu-id="73c02-133">Repeat the preceding steps as changes are made to the model.</span></span>

## <a name="the-no-locidentity-model"></a><span data-ttu-id="73c02-134">El Identity modelo</span><span class="sxs-lookup"><span data-stu-id="73c02-134">The Identity model</span></span>

### <a name="entity-types"></a><span data-ttu-id="73c02-135">Tipos de entidades</span><span class="sxs-lookup"><span data-stu-id="73c02-135">Entity types</span></span>

<span data-ttu-id="73c02-136">El Identity modelo consta de los siguientes tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="73c02-136">The Identity model consists of the following entity types.</span></span>

|<span data-ttu-id="73c02-137">Tipo de entidad</span><span class="sxs-lookup"><span data-stu-id="73c02-137">Entity type</span></span>|<span data-ttu-id="73c02-138">Descripción</span><span class="sxs-lookup"><span data-stu-id="73c02-138">Description</span></span>                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |<span data-ttu-id="73c02-139">Representa al usuario.</span><span class="sxs-lookup"><span data-stu-id="73c02-139">Represents the user.</span></span>                                         |
|`Role`     |<span data-ttu-id="73c02-140">Representa un rol.</span><span class="sxs-lookup"><span data-stu-id="73c02-140">Represents a role.</span></span>                                           |
|`UserClaim`|<span data-ttu-id="73c02-141">Representa una demanda que un usuario posee.</span><span class="sxs-lookup"><span data-stu-id="73c02-141">Represents a claim that a user possesses.</span></span>                    |
|`UserToken`|<span data-ttu-id="73c02-142">Representa un token de autenticación para un usuario.</span><span class="sxs-lookup"><span data-stu-id="73c02-142">Represents an authentication token for a user.</span></span>               |
|`UserLogin`|<span data-ttu-id="73c02-143">Asocia un usuario a un inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="73c02-143">Associates a user with a login.</span></span>                              |
|`RoleClaim`|<span data-ttu-id="73c02-144">Representa una demanda que se concede a todos los usuarios de un rol.</span><span class="sxs-lookup"><span data-stu-id="73c02-144">Represents a claim that's granted to all users within a role.</span></span>|
|`UserRole` |<span data-ttu-id="73c02-145">Entidad de combinación que asocia usuarios y roles.</span><span class="sxs-lookup"><span data-stu-id="73c02-145">A join entity that associates users and roles.</span></span>               |

### <a name="entity-type-relationships"></a><span data-ttu-id="73c02-146">Relaciones de tipo de entidad</span><span class="sxs-lookup"><span data-stu-id="73c02-146">Entity type relationships</span></span>

<span data-ttu-id="73c02-147">Los [tipos de entidad](#entity-types) se relacionan entre sí de las siguientes maneras:</span><span class="sxs-lookup"><span data-stu-id="73c02-147">The [entity types](#entity-types) are related to each other in the following ways:</span></span>

* <span data-ttu-id="73c02-148">Cada `User` puede tener muchos `UserClaims` .</span><span class="sxs-lookup"><span data-stu-id="73c02-148">Each `User` can have many `UserClaims`.</span></span>
* <span data-ttu-id="73c02-149">Cada `User` puede tener muchos `UserLogins` .</span><span class="sxs-lookup"><span data-stu-id="73c02-149">Each `User` can have many `UserLogins`.</span></span>
* <span data-ttu-id="73c02-150">Cada `User` puede tener muchos `UserTokens` .</span><span class="sxs-lookup"><span data-stu-id="73c02-150">Each `User` can have many `UserTokens`.</span></span>
* <span data-ttu-id="73c02-151">Cada `Role` puede tener varios asociados `RoleClaims` .</span><span class="sxs-lookup"><span data-stu-id="73c02-151">Each `Role` can have many associated `RoleClaims`.</span></span>
* <span data-ttu-id="73c02-152">Cada `User` puede tener varios asociados `Roles` , y cada uno `Role` de ellos puede asociarse a muchos `Users` .</span><span class="sxs-lookup"><span data-stu-id="73c02-152">Each `User` can have many associated `Roles`, and each `Role` can be associated with many `Users`.</span></span> <span data-ttu-id="73c02-153">Se trata de una relación de varios a varios que requiere una tabla de combinación en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="73c02-153">This is a many-to-many relationship that requires a join table in the database.</span></span> <span data-ttu-id="73c02-154">La entidad representa la tabla de combinación `UserRole` .</span><span class="sxs-lookup"><span data-stu-id="73c02-154">The join table is represented by the `UserRole` entity.</span></span>

### <a name="default-model-configuration"></a><span data-ttu-id="73c02-155">Configuración de modelo predeterminada</span><span class="sxs-lookup"><span data-stu-id="73c02-155">Default model configuration</span></span>

<span data-ttu-id="73c02-156">Identity define muchas *clases de contexto* que heredan de [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) para configurar y usar el modelo.</span><span class="sxs-lookup"><span data-stu-id="73c02-156">Identity defines many *context classes* that inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) to configure and use the model.</span></span> <span data-ttu-id="73c02-157">Esta configuración se realiza mediante el [EF Core Code First API fluida](/ef/core/modeling/) en el método [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) de la clase de contexto.</span><span class="sxs-lookup"><span data-stu-id="73c02-157">This configuration is done using the [EF Core Code First Fluent API](/ef/core/modeling/) in the [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) method of the context class.</span></span> <span data-ttu-id="73c02-158">La configuración predeterminada es la siguiente:</span><span class="sxs-lookup"><span data-stu-id="73c02-158">The default configuration is:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Primary key
    b.HasKey(u => u.Id);

    // Indexes for "normalized" username and email, to allow efficient lookups
    b.HasIndex(u => u.NormalizedUserName).HasName("UserNameIndex").IsUnique();
    b.HasIndex(u => u.NormalizedEmail).HasName("EmailIndex");

    // Maps to the AspNetUsers table
    b.ToTable("AspNetUsers");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(u => u.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.UserName).HasMaxLength(256);
    b.Property(u => u.NormalizedUserName).HasMaxLength(256);
    b.Property(u => u.Email).HasMaxLength(256);
    b.Property(u => u.NormalizedEmail).HasMaxLength(256);

    // The relationships between User and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each User can have many UserClaims
    b.HasMany<TUserClaim>().WithOne().HasForeignKey(uc => uc.UserId).IsRequired();

    // Each User can have many UserLogins
    b.HasMany<TUserLogin>().WithOne().HasForeignKey(ul => ul.UserId).IsRequired();

    // Each User can have many UserTokens
    b.HasMany<TUserToken>().WithOne().HasForeignKey(ut => ut.UserId).IsRequired();

    // Each User can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.UserId).IsRequired();
});

builder.Entity<TUserClaim>(b =>
{
    // Primary key
    b.HasKey(uc => uc.Id);

    // Maps to the AspNetUserClaims table
    b.ToTable("AspNetUserClaims");
});

builder.Entity<TUserLogin>(b =>
{
    // Composite primary key consisting of the LoginProvider and the key to use
    // with that provider
    b.HasKey(l => new { l.LoginProvider, l.ProviderKey });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(l => l.LoginProvider).HasMaxLength(128);
    b.Property(l => l.ProviderKey).HasMaxLength(128);

    // Maps to the AspNetUserLogins table
    b.ToTable("AspNetUserLogins");
});

builder.Entity<TUserToken>(b =>
{
    // Composite primary key consisting of the UserId, LoginProvider and Name
    b.HasKey(t => new { t.UserId, t.LoginProvider, t.Name });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(t => t.LoginProvider).HasMaxLength(maxKeyLength);
    b.Property(t => t.Name).HasMaxLength(maxKeyLength);

    // Maps to the AspNetUserTokens table
    b.ToTable("AspNetUserTokens");
});

builder.Entity<TRole>(b =>
{
    // Primary key
    b.HasKey(r => r.Id);

    // Index for "normalized" role name to allow efficient lookups
    b.HasIndex(r => r.NormalizedName).HasName("RoleNameIndex").IsUnique();

    // Maps to the AspNetRoles table
    b.ToTable("AspNetRoles");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(r => r.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.Name).HasMaxLength(256);
    b.Property(u => u.NormalizedName).HasMaxLength(256);

    // The relationships between Role and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each Role can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.RoleId).IsRequired();

    // Each Role can have many associated RoleClaims
    b.HasMany<TRoleClaim>().WithOne().HasForeignKey(rc => rc.RoleId).IsRequired();
});

builder.Entity<TRoleClaim>(b =>
{
    // Primary key
    b.HasKey(rc => rc.Id);

    // Maps to the AspNetRoleClaims table
    b.ToTable("AspNetRoleClaims");
});

builder.Entity<TUserRole>(b =>
{
    // Primary key
    b.HasKey(r => new { r.UserId, r.RoleId });

    // Maps to the AspNetUserRoles table
    b.ToTable("AspNetUserRoles");
});
```

### <a name="model-generic-types"></a><span data-ttu-id="73c02-159">Tipos genéricos de modelo</span><span class="sxs-lookup"><span data-stu-id="73c02-159">Model generic types</span></span>

<span data-ttu-id="73c02-160">Identity define los tipos de [Common Language Runtime](/dotnet/standard/glossary#clr) (CLR) predeterminados para cada uno de los tipos de entidad enumerados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="73c02-160">Identity defines default [Common Language Runtime](/dotnet/standard/glossary#clr) (CLR) types for each of the entity types listed above.</span></span> <span data-ttu-id="73c02-161">Todos estos tipos tienen el prefijo *Identity* :</span><span class="sxs-lookup"><span data-stu-id="73c02-161">These types are all prefixed with *Identity*:</span></span>

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

<span data-ttu-id="73c02-162">En lugar de usar estos tipos directamente, los tipos se pueden usar como clases base para los propios tipos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="73c02-162">Rather than using these types directly, the types can be used as base classes for the app's own types.</span></span> <span data-ttu-id="73c02-163">Las `DbContext` clases definidas por Identity son genéricas, de modo que se pueden usar diferentes tipos CLR para uno o varios de los tipos de entidad del modelo.</span><span class="sxs-lookup"><span data-stu-id="73c02-163">The `DbContext` classes defined by Identity are generic, such that different CLR types can be used for one or more of the entity types in the model.</span></span> <span data-ttu-id="73c02-164">Estos tipos genéricos también permiten `User` cambiar el tipo de datos de la clave principal (PK).</span><span class="sxs-lookup"><span data-stu-id="73c02-164">These generic types also allow the `User` primary key (PK) data type to be changed.</span></span>

<span data-ttu-id="73c02-165">Cuando Identity se usa con compatibilidad con roles, <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> se debe usar una clase.</span><span class="sxs-lookup"><span data-stu-id="73c02-165">When using Identity with support for roles, an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> class should be used.</span></span> <span data-ttu-id="73c02-166">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="73c02-166">For example:</span></span>

```csharp
// Uses all the built-in Identity types
// Uses `string` as the key type
public class IdentityDbContext
    : IdentityDbContext<IdentityUser, IdentityRole, string>
{
}

// Uses the built-in Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityDbContext<TUser>
    : IdentityDbContext<TUser, IdentityRole, string>
        where TUser : IdentityUser
{
}

// Uses the built-in Identity types except with custom User and Role types
// The key type is defined by TKey
public class IdentityDbContext<TUser, TRole, TKey> : IdentityDbContext<
    TUser, TRole, TKey, IdentityUserClaim<TKey>, IdentityUserRole<TKey>,
    IdentityUserLogin<TKey>, IdentityRoleClaim<TKey>, IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TRole : IdentityRole<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments
// The key type is defined by TKey
public abstract class IdentityDbContext<
    TUser, TRole, TKey, TUserClaim, TUserRole, TUserLogin, TRoleClaim, TUserToken>
    : IdentityUserContext<TUser, TKey, TUserClaim, TUserLogin, TUserToken>
         where TUser : IdentityUser<TKey>
         where TRole : IdentityRole<TKey>
         where TKey : IEquatable<TKey>
         where TUserClaim : IdentityUserClaim<TKey>
         where TUserRole : IdentityUserRole<TKey>
         where TUserLogin : IdentityUserLogin<TKey>
         where TRoleClaim : IdentityRoleClaim<TKey>
         where TUserToken : IdentityUserToken<TKey>
```

<span data-ttu-id="73c02-167">También se puede usar Identity sin roles (solo notificaciones), en cuyo caso <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> se debe usar una clase:</span><span class="sxs-lookup"><span data-stu-id="73c02-167">It's also possible to use Identity without roles (only claims), in which case an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> class should be used:</span></span>

```csharp
// Uses the built-in non-role Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityUserContext<TUser>
    : IdentityUserContext<TUser, string>
        where TUser : IdentityUser
{
}

// Uses the built-in non-role Identity types except with a custom User type
// The key type is defined by TKey
public class IdentityUserContext<TUser, TKey> : IdentityUserContext<
    TUser, TKey, IdentityUserClaim<TKey>, IdentityUserLogin<TKey>,
    IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments, with no roles
// The key type is defined by TKey
public abstract class IdentityUserContext<
    TUser, TKey, TUserClaim, TUserLogin, TUserToken> : DbContext
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
        where TUserClaim : IdentityUserClaim<TKey>
        where TUserLogin : IdentityUserLogin<TKey>
        where TUserToken : IdentityUserToken<TKey>
{
}
```

## <a name="customize-the-model"></a><span data-ttu-id="73c02-168">Personalizar el modelo</span><span class="sxs-lookup"><span data-stu-id="73c02-168">Customize the model</span></span>

<span data-ttu-id="73c02-169">El punto de partida para la personalización del modelo es derivar del tipo de contexto adecuado.</span><span class="sxs-lookup"><span data-stu-id="73c02-169">The starting point for model customization is to derive from the appropriate context type.</span></span> <span data-ttu-id="73c02-170">Vea la sección [tipos genéricos de modelo](#model-generic-types) .</span><span class="sxs-lookup"><span data-stu-id="73c02-170">See the [Model generic types](#model-generic-types) section.</span></span> <span data-ttu-id="73c02-171">Este tipo de contexto se denomina habitualmente `ApplicationDbContext` y lo crean las plantillas de ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="73c02-171">This context type is customarily called `ApplicationDbContext` and is created by the ASP.NET Core templates.</span></span>

<span data-ttu-id="73c02-172">El contexto se utiliza para configurar el modelo de dos maneras:</span><span class="sxs-lookup"><span data-stu-id="73c02-172">The context is used to configure the model in two ways:</span></span>

* <span data-ttu-id="73c02-173">Proporcionar tipos de entidad y clave para los parámetros de tipo genérico.</span><span class="sxs-lookup"><span data-stu-id="73c02-173">Supplying entity and key types for the generic type parameters.</span></span>
* <span data-ttu-id="73c02-174">Reemplazar `OnModelCreating` para modificar la asignación de estos tipos.</span><span class="sxs-lookup"><span data-stu-id="73c02-174">Overriding `OnModelCreating` to modify the mapping of these types.</span></span>

<span data-ttu-id="73c02-175">Al reemplazar `OnModelCreating` , `base.OnModelCreating` se debe llamar primero a; a continuación, se debe llamar a la configuración de reemplazo.</span><span class="sxs-lookup"><span data-stu-id="73c02-175">When overriding `OnModelCreating`, `base.OnModelCreating` should be called first; the overriding configuration should be called next.</span></span> <span data-ttu-id="73c02-176">Por lo general, EF Core tiene una directiva de el último de uno a WINS para la configuración.</span><span class="sxs-lookup"><span data-stu-id="73c02-176">EF Core generally has a last-one-wins policy for configuration.</span></span> <span data-ttu-id="73c02-177">Por ejemplo, si `ToTable` se llama primero al método para un tipo de entidad con un nombre de tabla y después de nuevo con un nombre de tabla diferente, se utiliza el nombre de tabla de la segunda llamada.</span><span class="sxs-lookup"><span data-stu-id="73c02-177">For example, if the `ToTable` method for an entity type is called first with one table name and then again later with a different table name, the table name in the second call is used.</span></span>

### <a name="custom-user-data"></a><span data-ttu-id="73c02-178">Datos de usuario personalizados</span><span class="sxs-lookup"><span data-stu-id="73c02-178">Custom user data</span></span>

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

<span data-ttu-id="73c02-179">Los [datos de usuario personalizados](xref:security/authentication/add-user-data) se admiten heredando de `IdentityUser` .</span><span class="sxs-lookup"><span data-stu-id="73c02-179">[Custom user data](xref:security/authentication/add-user-data) is supported by inheriting from `IdentityUser`.</span></span> <span data-ttu-id="73c02-180">El nombre de este tipo es personalizado `ApplicationUser` :</span><span class="sxs-lookup"><span data-stu-id="73c02-180">It's customary to name this type `ApplicationUser`:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

<span data-ttu-id="73c02-181">Use el `ApplicationUser` tipo como argumento genérico para el contexto:</span><span class="sxs-lookup"><span data-stu-id="73c02-181">Use the `ApplicationUser` type as a generic argument for the context:</span></span>

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);
    }
}
```

<span data-ttu-id="73c02-182">No es necesario invalidar `OnModelCreating` en la `ApplicationDbContext` clase.</span><span class="sxs-lookup"><span data-stu-id="73c02-182">There's no need to override `OnModelCreating` in the `ApplicationDbContext` class.</span></span> <span data-ttu-id="73c02-183">EF Core asigna la `CustomTag` propiedad por Convención.</span><span class="sxs-lookup"><span data-stu-id="73c02-183">EF Core maps the `CustomTag` property by convention.</span></span> <span data-ttu-id="73c02-184">Sin embargo, es necesario actualizar la base de datos para crear una nueva `CustomTag` columna.</span><span class="sxs-lookup"><span data-stu-id="73c02-184">However, the database needs to be updated to create a new `CustomTag` column.</span></span> <span data-ttu-id="73c02-185">Para crear la columna, agregue una migración y, a continuación, actualice la base de datos como se describe en [ Identity y EF Core migraciones](#identity-and-ef-core-migrations).</span><span class="sxs-lookup"><span data-stu-id="73c02-185">To create the column, add a migration, and then update the database as described in [Identity and EF Core Migrations](#identity-and-ef-core-migrations).</span></span>

<span data-ttu-id="73c02-186">Actualice *pages/Shared/_LoginPartial. cshtml* y reemplace `IdentityUser` por `ApplicationUser` :</span><span class="sxs-lookup"><span data-stu-id="73c02-186">Update *Pages/Shared/_LoginPartial.cshtml* and replace `IdentityUser` with `ApplicationUser`:</span></span>

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

<span data-ttu-id="73c02-187">Actualice *areas/ Identity / Identity HostingStartup.CS* o `Startup.ConfigureServices` y reemplace `IdentityUser` por `ApplicationUser` .</span><span class="sxs-lookup"><span data-stu-id="73c02-187">Update *Areas/Identity/IdentityHostingStartup.cs*  or `Startup.ConfigureServices` and replace `IdentityUser` with `ApplicationUser`.</span></span>

```csharp
services.AddIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

<span data-ttu-id="73c02-188">En ASP.NET Core 2,1 o posterior, Identity se proporciona como una Razor biblioteca de clases.</span><span class="sxs-lookup"><span data-stu-id="73c02-188">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="73c02-189">Para más información, consulte <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="73c02-189">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="73c02-190">Por consiguiente, el código anterior requiere una llamada a <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="73c02-190">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="73c02-191">Si el Identity scaffolding se utilizó para agregar Identity archivos al proyecto, quite la llamada a `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="73c02-191">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span> <span data-ttu-id="73c02-192">Para más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="73c02-192">For more information, see:</span></span>

* [<span data-ttu-id="73c02-193">Scaffold Identity</span><span class="sxs-lookup"><span data-stu-id="73c02-193">Scaffold Identity</span></span>](xref:security/authentication/scaffold-identity)
* [<span data-ttu-id="73c02-194">Agregar, descargar y eliminar datos de usuario personalizados a Identity</span><span class="sxs-lookup"><span data-stu-id="73c02-194">Add, download, and delete custom user data to Identity</span></span>](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a><span data-ttu-id="73c02-195">Cambiar el tipo de clave principal</span><span class="sxs-lookup"><span data-stu-id="73c02-195">Change the primary key type</span></span>

<span data-ttu-id="73c02-196">Un cambio en el tipo de datos de la columna PK después de crear la base de datos es problemático en muchos sistemas de base de datos.</span><span class="sxs-lookup"><span data-stu-id="73c02-196">A change to the PK column's data type after the database has been created is problematic on many database systems.</span></span> <span data-ttu-id="73c02-197">Cambiar la PK normalmente implica quitar y volver a crear la tabla.</span><span class="sxs-lookup"><span data-stu-id="73c02-197">Changing the PK typically involves dropping and re-creating the table.</span></span> <span data-ttu-id="73c02-198">Por lo tanto, los tipos de clave se deben especificar en la migración inicial cuando se crea la base de datos.</span><span class="sxs-lookup"><span data-stu-id="73c02-198">Therefore, key types should be specified in the initial migration when the database is created.</span></span>

<span data-ttu-id="73c02-199">Siga estos pasos para cambiar el tipo de PK:</span><span class="sxs-lookup"><span data-stu-id="73c02-199">Follow these steps to change the PK type:</span></span>

1. <span data-ttu-id="73c02-200">Si la base de datos se creó antes del cambio de la PK, ejecute `Drop-Database` (PMC) o `dotnet ef database drop` (CLI de .net Core) para eliminarla.</span><span class="sxs-lookup"><span data-stu-id="73c02-200">If the database was created before the PK change, run `Drop-Database` (PMC) or `dotnet ef database drop` (.NET Core CLI) to delete it.</span></span>
2. <span data-ttu-id="73c02-201">Después de confirmar la eliminación de la base de datos, quite la migración inicial con `Remove-Migration` (PMC) o `dotnet ef migrations remove` (CLI de .net Core).</span><span class="sxs-lookup"><span data-stu-id="73c02-201">After confirming deletion of the database, remove the initial migration with `Remove-Migration` (PMC) or `dotnet ef migrations remove` (.NET Core CLI).</span></span>
3. <span data-ttu-id="73c02-202">Actualice la `ApplicationDbContext` clase para que se derive de <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603> .</span><span class="sxs-lookup"><span data-stu-id="73c02-202">Update the `ApplicationDbContext` class to derive from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603>.</span></span> <span data-ttu-id="73c02-203">Especifique el nuevo tipo de clave para `TKey` .</span><span class="sxs-lookup"><span data-stu-id="73c02-203">Specify the new key type for `TKey`.</span></span> <span data-ttu-id="73c02-204">Por ejemplo, para usar un `Guid` tipo de clave:</span><span class="sxs-lookup"><span data-stu-id="73c02-204">For example, to use a `Guid` key type:</span></span>

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<IdentityUser<Guid>, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    ::: moniker range=">= aspnetcore-2.0"

    <span data-ttu-id="73c02-205">En el código anterior, las clases genéricas <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> y <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> deben especificarse para usar el nuevo tipo de clave.</span><span class="sxs-lookup"><span data-stu-id="73c02-205">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> and <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    <span data-ttu-id="73c02-206">En el código anterior, las clases genéricas <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> y <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> deben especificarse para usar el nuevo tipo de clave.</span><span class="sxs-lookup"><span data-stu-id="73c02-206">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> and <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    <span data-ttu-id="73c02-207">`Startup.ConfigureServices` debe actualizarse para usar el usuario genérico:</span><span class="sxs-lookup"><span data-stu-id="73c02-207">`Startup.ConfigureServices` must be updated to use the generic user:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<IdentityUser<Guid>>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

4. <span data-ttu-id="73c02-208">Si `ApplicationUser` se utiliza una clase personalizada, actualice la clase para que herede de `IdentityUser` .</span><span class="sxs-lookup"><span data-stu-id="73c02-208">If a custom `ApplicationUser` class is being used, update the class to inherit from `IdentityUser`.</span></span> <span data-ttu-id="73c02-209">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="73c02-209">For example:</span></span>

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    <span data-ttu-id="73c02-210">Actualice `ApplicationDbContext` para hacer referencia a la `ApplicationUser` clase personalizada:</span><span class="sxs-lookup"><span data-stu-id="73c02-210">Update `ApplicationDbContext` to reference the custom `ApplicationUser` class:</span></span>

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<ApplicationUser, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    <span data-ttu-id="73c02-211">Registre la clase de contexto de base de datos personalizada al agregar el Identity servicio en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="73c02-211">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="73c02-212">El tipo de datos de la clave principal se deduce mediante el análisis del objeto [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="73c02-212">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="73c02-213">En ASP.NET Core 2,1 o posterior, Identity se proporciona como una Razor biblioteca de clases.</span><span class="sxs-lookup"><span data-stu-id="73c02-213">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="73c02-214">Para más información, consulte <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="73c02-214">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="73c02-215">Por consiguiente, el código anterior requiere una llamada a <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="73c02-215">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="73c02-216">Si el Identity scaffolding se utilizó para agregar Identity archivos al proyecto, quite la llamada a `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="73c02-216">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="73c02-217">El tipo de datos de la clave principal se deduce mediante el análisis del objeto [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="73c02-217">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="73c02-218">El <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> método acepta un `TKey` tipo que indica el tipo de datos de la clave principal.</span><span class="sxs-lookup"><span data-stu-id="73c02-218">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

5. <span data-ttu-id="73c02-219">Si `ApplicationRole` se utiliza una clase personalizada, actualice la clase para que herede de `IdentityRole<TKey>` .</span><span class="sxs-lookup"><span data-stu-id="73c02-219">If a custom `ApplicationRole` class is being used, update the class to inherit from `IdentityRole<TKey>`.</span></span> <span data-ttu-id="73c02-220">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="73c02-220">For example:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    <span data-ttu-id="73c02-221">Actualice `ApplicationDbContext` para hacer referencia a la `ApplicationRole` clase personalizada.</span><span class="sxs-lookup"><span data-stu-id="73c02-221">Update `ApplicationDbContext` to reference the custom `ApplicationRole` class.</span></span> <span data-ttu-id="73c02-222">Por ejemplo, la siguiente clase hace referencia a un personalizado `ApplicationUser` y a un personalizado `ApplicationRole` :</span><span class="sxs-lookup"><span data-stu-id="73c02-222">For example, the following class references a custom `ApplicationUser` and a custom `ApplicationRole`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="73c02-223">Registre la clase de contexto de base de datos personalizada al agregar el Identity servicio en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="73c02-223">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    <span data-ttu-id="73c02-224">El tipo de datos de la clave principal se deduce mediante el análisis del objeto [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="73c02-224">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="73c02-225">En ASP.NET Core 2,1 o posterior, Identity se proporciona como una Razor biblioteca de clases.</span><span class="sxs-lookup"><span data-stu-id="73c02-225">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="73c02-226">Para más información, consulte <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="73c02-226">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="73c02-227">Por consiguiente, el código anterior requiere una llamada a <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="73c02-227">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="73c02-228">Si el Identity scaffolding se utilizó para agregar Identity archivos al proyecto, quite la llamada a `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="73c02-228">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="73c02-229">Registre la clase de contexto de base de datos personalizada al agregar el Identity servicio en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="73c02-229">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="73c02-230">El tipo de datos de la clave principal se deduce mediante el análisis del objeto [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="73c02-230">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="73c02-231">Registre la clase de contexto de base de datos personalizada al agregar el Identity servicio en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="73c02-231">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="73c02-232">El <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> método acepta un `TKey` tipo que indica el tipo de datos de la clave principal.</span><span class="sxs-lookup"><span data-stu-id="73c02-232">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

### <a name="add-navigation-properties"></a><span data-ttu-id="73c02-233">Agregar propiedades de navegación</span><span class="sxs-lookup"><span data-stu-id="73c02-233">Add navigation properties</span></span>

<span data-ttu-id="73c02-234">Cambiar la configuración del modelo para las relaciones puede ser más difícil que realizar otros cambios.</span><span class="sxs-lookup"><span data-stu-id="73c02-234">Changing the model configuration for relationships can be more difficult than making other changes.</span></span> <span data-ttu-id="73c02-235">Se debe tener cuidado para reemplazar las relaciones existentes en lugar de crear nuevas relaciones adicionales.</span><span class="sxs-lookup"><span data-stu-id="73c02-235">Care must be taken to replace the existing relationships rather than create new, additional relationships.</span></span> <span data-ttu-id="73c02-236">En concreto, la relación modificada debe especificar la misma propiedad de clave externa (FK) que la relación existente.</span><span class="sxs-lookup"><span data-stu-id="73c02-236">In particular, the changed relationship must specify the same foreign key (FK) property as the existing relationship.</span></span> <span data-ttu-id="73c02-237">Por ejemplo, la relación entre `Users` y `UserClaims` es, de forma predeterminada, especificada de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="73c02-237">For example, the relationship between `Users` and `UserClaims` is, by default, specified as follows:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Each User can have many UserClaims
    b.HasMany<TUserClaim>()
     .WithOne()
     .HasForeignKey(uc => uc.UserId)
     .IsRequired();
});
```

<span data-ttu-id="73c02-238">El parámetro FK para esta relación se especifica como la `UserClaim.UserId` propiedad.</span><span class="sxs-lookup"><span data-stu-id="73c02-238">The FK for this relationship is specified as the `UserClaim.UserId` property.</span></span> <span data-ttu-id="73c02-239">`HasMany``WithOne`se llama a y sin argumentos para crear la relación sin las propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="73c02-239">`HasMany` and `WithOne` are called without arguments to create the relationship without navigation properties.</span></span>

<span data-ttu-id="73c02-240">Agregue una propiedad de navegación a `ApplicationUser` que permita que `UserClaims` se haga referencia a la clase asociada desde el usuario:</span><span class="sxs-lookup"><span data-stu-id="73c02-240">Add a navigation property to `ApplicationUser` that allows associated `UserClaims` to be referenced from the user:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

<span data-ttu-id="73c02-241">La `TKey` para `IdentityUserClaim<TKey>` es el tipo especificado para la clave principal de los usuarios.</span><span class="sxs-lookup"><span data-stu-id="73c02-241">The `TKey` for `IdentityUserClaim<TKey>` is the type specified for the PK of users.</span></span> <span data-ttu-id="73c02-242">En este caso, `TKey` se `string` debe a que se usan los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="73c02-242">In this case, `TKey` is `string` because the defaults are being used.</span></span> <span data-ttu-id="73c02-243">**No** es el tipo PK del tipo de `UserClaim` entidad.</span><span class="sxs-lookup"><span data-stu-id="73c02-243">It's **not** the PK type for the `UserClaim` entity type.</span></span>

<span data-ttu-id="73c02-244">Ahora que la propiedad de navegación existe, debe configurarse en `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="73c02-244">Now that the navigation property exists, it must be configured in `OnModelCreating`:</span></span>

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();
        });
    }
}
```

<span data-ttu-id="73c02-245">Observe que la relación se configura exactamente como estaba antes, solo con una propiedad de navegación especificada en la llamada a `HasMany` .</span><span class="sxs-lookup"><span data-stu-id="73c02-245">Notice that relationship is configured exactly as it was before, only with a navigation property specified in the call to `HasMany`.</span></span>

<span data-ttu-id="73c02-246">Las propiedades de navegación solo existen en el modelo EF, no en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="73c02-246">The navigation properties only exist in the EF model, not the database.</span></span> <span data-ttu-id="73c02-247">Dado que el FK de la relación no ha cambiado, este tipo de cambio de modelo no requiere que se actualice la base de datos.</span><span class="sxs-lookup"><span data-stu-id="73c02-247">Because the FK for the relationship hasn't changed, this kind of model change doesn't require the database to be updated.</span></span> <span data-ttu-id="73c02-248">Para comprobarlo, agregue una migración después de realizar el cambio.</span><span class="sxs-lookup"><span data-stu-id="73c02-248">This can be checked by adding a migration after making the change.</span></span> <span data-ttu-id="73c02-249">Los `Up` `Down` métodos y están vacíos.</span><span class="sxs-lookup"><span data-stu-id="73c02-249">The `Up` and `Down` methods are empty.</span></span>

### <a name="add-all-user-navigation-properties"></a><span data-ttu-id="73c02-250">Agregar todas las propiedades de navegación de usuario</span><span class="sxs-lookup"><span data-stu-id="73c02-250">Add all User navigation properties</span></span>

<span data-ttu-id="73c02-251">Con la sección anterior como guía, en el ejemplo siguiente se configuran las propiedades de navegación unidireccional para todas las relaciones del usuario:</span><span class="sxs-lookup"><span data-stu-id="73c02-251">Using the section above as guidance, the following example configures unidirectional navigation properties for all relationships on User:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<IdentityUserRole<string>> UserRoles { get; set; }
}
```

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne()
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });
    }
}
```

### <a name="add-user-and-role-navigation-properties"></a><span data-ttu-id="73c02-252">Agregar propiedades de navegación de usuario y rol</span><span class="sxs-lookup"><span data-stu-id="73c02-252">Add User and Role navigation properties</span></span>

<span data-ttu-id="73c02-253">Con la sección anterior como guía, en el ejemplo siguiente se configuran las propiedades de navegación para todas las relaciones en el usuario y el rol:</span><span class="sxs-lookup"><span data-stu-id="73c02-253">Using the section above as guidance, the following example configures navigation properties for all relationships on User and Role:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        IdentityUserClaim<string>, ApplicationUserRole, IdentityUserLogin<string>,
        IdentityRoleClaim<string>, IdentityUserToken<string>>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();
        });

    }
}
```

<span data-ttu-id="73c02-254">Notas:</span><span class="sxs-lookup"><span data-stu-id="73c02-254">Notes:</span></span>

* <span data-ttu-id="73c02-255">En este ejemplo también se incluye la `UserRole` entidad de combinación, que es necesaria para navegar por la relación de varios a varios entre usuarios y roles.</span><span class="sxs-lookup"><span data-stu-id="73c02-255">This example also includes the `UserRole` join entity, which is needed to navigate the many-to-many relationship from Users to Roles.</span></span>
* <span data-ttu-id="73c02-256">Recuerde cambiar los tipos de las propiedades de navegación para reflejar que `Application{...}` ahora se usan tipos en lugar de `Identity{...}` tipos.</span><span class="sxs-lookup"><span data-stu-id="73c02-256">Remember to change the types of the navigation properties to reflect that `Application{...}` types are now being used instead of `Identity{...}` types.</span></span>
* <span data-ttu-id="73c02-257">Recuerde usar `Application{...}` en la `ApplicationContext` definición genérica.</span><span class="sxs-lookup"><span data-stu-id="73c02-257">Remember to use the `Application{...}` in the generic `ApplicationContext` definition.</span></span>

### <a name="add-all-navigation-properties"></a><span data-ttu-id="73c02-258">Agregar todas las propiedades de navegación</span><span class="sxs-lookup"><span data-stu-id="73c02-258">Add all navigation properties</span></span>

<span data-ttu-id="73c02-259">Con la sección anterior como guía, en el ejemplo siguiente se configuran las propiedades de navegación para todas las relaciones en todos los tipos de entidad:</span><span class="sxs-lookup"><span data-stu-id="73c02-259">Using the section above as guidance, the following example configures navigation properties for all relationships on all entity types:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<ApplicationUserClaim> Claims { get; set; }
    public virtual ICollection<ApplicationUserLogin> Logins { get; set; }
    public virtual ICollection<ApplicationUserToken> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
    public virtual ICollection<ApplicationRoleClaim> RoleClaims { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserClaim : IdentityUserClaim<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationUserLogin : IdentityUserLogin<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationRoleClaim : IdentityRoleClaim<string>
{
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserToken : IdentityUserToken<string>
{
    public virtual ApplicationUser User { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        ApplicationUserClaim, ApplicationUserRole, ApplicationUserLogin,
        ApplicationRoleClaim, ApplicationUserToken>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne(e => e.User)
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne(e => e.User)
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne(e => e.User)
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();

            // Each Role can have many associated RoleClaims
            b.HasMany(e => e.RoleClaims)
                .WithOne(e => e.Role)
                .HasForeignKey(rc => rc.RoleId)
                .IsRequired();
        });
    }
}
```

### <a name="use-composite-keys"></a><span data-ttu-id="73c02-260">Usar claves compuestas</span><span class="sxs-lookup"><span data-stu-id="73c02-260">Use composite keys</span></span>

<span data-ttu-id="73c02-261">En las secciones anteriores se mostró cómo cambiar el tipo de clave utilizado en el Identity modelo.</span><span class="sxs-lookup"><span data-stu-id="73c02-261">The preceding sections demonstrated changing the type of key used in the Identity model.</span></span> <span data-ttu-id="73c02-262">IdentityNo se admite ni se recomienda cambiar el modelo de clave para utilizar claves compuestas.</span><span class="sxs-lookup"><span data-stu-id="73c02-262">Changing the Identity key model to use composite keys isn't supported or recommended.</span></span> <span data-ttu-id="73c02-263">El uso de una clave compuesta con Identity implica cambiar el modo en que el Identity código del administrador interactúa con el modelo.</span><span class="sxs-lookup"><span data-stu-id="73c02-263">Using a composite key with Identity involves changing how the Identity manager code interacts with the model.</span></span> <span data-ttu-id="73c02-264">Esta personalización está fuera del ámbito de este documento.</span><span class="sxs-lookup"><span data-stu-id="73c02-264">This customization is beyond the scope of this document.</span></span>

### <a name="change-tablecolumn-names-and-facets"></a><span data-ttu-id="73c02-265">Cambiar los nombres de tabla/columna y las caras</span><span class="sxs-lookup"><span data-stu-id="73c02-265">Change table/column names and facets</span></span>

<span data-ttu-id="73c02-266">Para cambiar los nombres de tablas y columnas, llame a `base.OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="73c02-266">To change the names of tables and columns, call `base.OnModelCreating`.</span></span> <span data-ttu-id="73c02-267">A continuación, agregue la configuración para invalidar cualquiera de los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="73c02-267">Then, add configuration to override any of the defaults.</span></span> <span data-ttu-id="73c02-268">Por ejemplo, para cambiar el nombre de todas las Identity tablas:</span><span class="sxs-lookup"><span data-stu-id="73c02-268">For example, to change the name of all the Identity tables:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.ToTable("MyUsers");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.ToTable("MyUserClaims");
    });

    modelBuilder.Entity<IdentityUserLogin<string>>(b =>
    {
        b.ToTable("MyUserLogins");
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.ToTable("MyUserTokens");
    });

    modelBuilder.Entity<IdentityRole>(b =>
    {
        b.ToTable("MyRoles");
    });

    modelBuilder.Entity<IdentityRoleClaim<string>>(b =>
    {
        b.ToTable("MyRoleClaims");
    });

    modelBuilder.Entity<IdentityUserRole<string>>(b =>
    {
        b.ToTable("MyUserRoles");
    });
}
```

<span data-ttu-id="73c02-269">En estos ejemplos se usan los tipos predeterminados Identity .</span><span class="sxs-lookup"><span data-stu-id="73c02-269">These examples use the default Identity types.</span></span> <span data-ttu-id="73c02-270">Si usa un tipo de aplicación como `ApplicationUser` , configure ese tipo en lugar del tipo predeterminado.</span><span class="sxs-lookup"><span data-stu-id="73c02-270">If using an app type such as `ApplicationUser`, configure that type instead of the default type.</span></span>

<span data-ttu-id="73c02-271">En el ejemplo siguiente se cambian algunos nombres de columna:</span><span class="sxs-lookup"><span data-stu-id="73c02-271">The following example changes some column names:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(e => e.Email).HasColumnName("EMail");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.Property(e => e.ClaimType).HasColumnName("CType");
        b.Property(e => e.ClaimValue).HasColumnName("CValue");
    });
}
```

<span data-ttu-id="73c02-272">Algunos tipos de columnas de base de datos se pueden configurar con ciertas *caras* (por ejemplo, la `string` longitud máxima permitida).</span><span class="sxs-lookup"><span data-stu-id="73c02-272">Some types of database columns can be configured with certain *facets* (for example, the maximum `string` length allowed).</span></span> <span data-ttu-id="73c02-273">En el ejemplo siguiente se establecen las longitudes máximas de columna para varias `string` propiedades del modelo:</span><span class="sxs-lookup"><span data-stu-id="73c02-273">The following example sets column maximum lengths for several `string` properties in the model:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(u => u.UserName).HasMaxLength(128);
        b.Property(u => u.NormalizedUserName).HasMaxLength(128);
        b.Property(u => u.Email).HasMaxLength(128);
        b.Property(u => u.NormalizedEmail).HasMaxLength(128);
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.Property(t => t.LoginProvider).HasMaxLength(128);
        b.Property(t => t.Name).HasMaxLength(128);
    });
}
```

### <a name="map-to-a-different-schema"></a><span data-ttu-id="73c02-274">Asignar a un esquema diferente</span><span class="sxs-lookup"><span data-stu-id="73c02-274">Map to a different schema</span></span>

<span data-ttu-id="73c02-275">Los esquemas pueden comportarse de manera diferente en los proveedores de bases de datos.</span><span class="sxs-lookup"><span data-stu-id="73c02-275">Schemas can behave differently across database providers.</span></span> <span data-ttu-id="73c02-276">Por SQL Server, el valor predeterminado es crear todas las tablas en el esquema *DBO* .</span><span class="sxs-lookup"><span data-stu-id="73c02-276">For SQL Server, the default is to create all tables in the *dbo* schema.</span></span> <span data-ttu-id="73c02-277">Las tablas se pueden crear en un esquema diferente.</span><span class="sxs-lookup"><span data-stu-id="73c02-277">The tables can be created in a different schema.</span></span> <span data-ttu-id="73c02-278">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="73c02-278">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a><span data-ttu-id="73c02-279">Carga diferida</span><span class="sxs-lookup"><span data-stu-id="73c02-279">Lazy loading</span></span>

<span data-ttu-id="73c02-280">En esta sección, se agrega compatibilidad con los proxies de carga diferida en el Identity modelo.</span><span class="sxs-lookup"><span data-stu-id="73c02-280">In this section, support for lazy-loading proxies in the Identity model is added.</span></span> <span data-ttu-id="73c02-281">La carga diferida resulta útil, ya que permite usar las propiedades de navegación sin asegurarse primero de que se cargan.</span><span class="sxs-lookup"><span data-stu-id="73c02-281">Lazy-loading is useful since it allows navigation properties to be used without first ensuring they're loaded.</span></span>

<span data-ttu-id="73c02-282">Los tipos de entidad se pueden hacer adecuados para la carga diferida de varias maneras, como se describe en la [documentación EF Core](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="73c02-282">Entity types can be made suitable for lazy-loading in several ways, as described in the [EF Core documentation](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="73c02-283">Para simplificar, use servidores proxy de carga diferida, que requiere:</span><span class="sxs-lookup"><span data-stu-id="73c02-283">For simplicity, use lazy-loading proxies, which requires:</span></span>

* <span data-ttu-id="73c02-284">Instalación del paquete [Microsoft. EntityFrameworkCore. Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) .</span><span class="sxs-lookup"><span data-stu-id="73c02-284">Installation of the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package.</span></span>
* <span data-ttu-id="73c02-285">Una llamada a <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> dentro [de \<TContext> AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).</span><span class="sxs-lookup"><span data-stu-id="73c02-285">A call to <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> inside [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).</span></span>
* <span data-ttu-id="73c02-286">Tipos de entidad pública con `public virtual` propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="73c02-286">Public entity types with `public virtual` navigation properties.</span></span>

<span data-ttu-id="73c02-287">En el ejemplo siguiente se muestra cómo llamar a `UseLazyLoadingProxies` en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="73c02-287">The following example demonstrates calling `UseLazyLoadingProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

<span data-ttu-id="73c02-288">Consulte los ejemplos anteriores para obtener instrucciones sobre cómo agregar propiedades de navegación a los tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="73c02-288">Refer to the preceding examples for guidance on adding navigation properties to the entity types.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="73c02-289">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="73c02-289">Additional resources</span></span>

* <xref:security/authentication/scaffold-identity>

::: moniker-end
