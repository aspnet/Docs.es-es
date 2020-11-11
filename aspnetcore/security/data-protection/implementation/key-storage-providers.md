---
title: Proveedores de almacenamiento de claves en ASP.NET Core
author: rick-anderson
description: Obtenga información acerca de los proveedores de almacenamiento de claves en ASP.NET Core y cómo configurar las ubicaciones de almacenamiento de claves.
ms.author: riande
ms.date: 12/05/2019
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
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 6a70183ce4b1a129ef213300473b233a5ef822f9
ms.sourcegitcommit: fbd5427293d9ecccc388bd5fd305c2eb8ada7281
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94463891"
---
# <a name="key-storage-providers-in-aspnet-core"></a><span data-ttu-id="5a39e-103">Proveedores de almacenamiento de claves en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5a39e-103">Key storage providers in ASP.NET Core</span></span>

<span data-ttu-id="5a39e-104">De forma predeterminada, el sistema de protección de datos [emplea un mecanismo de detección](xref:security/data-protection/configuration/default-settings) para determinar dónde se deben conservar las claves criptográficas.</span><span class="sxs-lookup"><span data-stu-id="5a39e-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine where cryptographic keys should be persisted.</span></span> <span data-ttu-id="5a39e-105">El desarrollador puede invalidar el mecanismo de detección predeterminado y especificar manualmente la ubicación.</span><span class="sxs-lookup"><span data-stu-id="5a39e-105">The developer can override the default discovery mechanism and manually specify the location.</span></span>

> [!WARNING]
> <span data-ttu-id="5a39e-106">Si especifica una ubicación de persistencia de claves explícita, el sistema de protección de datos anula el registro del mecanismo de cifrado de claves predeterminado en reposo, por lo que las claves ya no se cifran en reposo.</span><span class="sxs-lookup"><span data-stu-id="5a39e-106">If you specify an explicit key persistence location, the data protection system deregisters the default key encryption at rest mechanism, so keys are no longer encrypted at rest.</span></span> <span data-ttu-id="5a39e-107">Se recomienda [especificar además un mecanismo de cifrado de claves explícito](xref:security/data-protection/implementation/key-encryption-at-rest) para las implementaciones de producción.</span><span class="sxs-lookup"><span data-stu-id="5a39e-107">It's recommended that you additionally [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span>

## <a name="file-system"></a><span data-ttu-id="5a39e-108">Sistema de archivos</span><span class="sxs-lookup"><span data-stu-id="5a39e-108">File system</span></span>

<span data-ttu-id="5a39e-109">Para configurar un repositorio de claves basado en sistema de archivos, llame a la rutina de configuración [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) como se muestra a continuación.</span><span class="sxs-lookup"><span data-stu-id="5a39e-109">To configure a file system-based key repository, call the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) configuration routine as shown below.</span></span> <span data-ttu-id="5a39e-110">Proporcione un [DirectoryInfo](/dotnet/api/system.io.directoryinfo) que apunte al repositorio donde se deben almacenar las claves:</span><span class="sxs-lookup"><span data-stu-id="5a39e-110">Provide a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) pointing to the repository where keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

<span data-ttu-id="5a39e-111">`DirectoryInfo`Puede apuntar a un directorio del equipo local o a una carpeta de un recurso compartido de red.</span><span class="sxs-lookup"><span data-stu-id="5a39e-111">The `DirectoryInfo` can point to a directory on the local machine, or it can point to a folder on a network share.</span></span> <span data-ttu-id="5a39e-112">Si apunta a un directorio en el equipo local (y el escenario es que solo las aplicaciones del equipo local requieren acceso para usar este repositorio), considere la posibilidad de usar [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (en Windows) para cifrar las claves en reposo.</span><span class="sxs-lookup"><span data-stu-id="5a39e-112">If pointing to a directory on the local machine (and the scenario is that only apps on the local machine require access to use this repository), consider using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (on Windows) to encrypt the keys at rest.</span></span> <span data-ttu-id="5a39e-113">En caso contrario, considere la posibilidad de utilizar un [certificado X. 509](xref:security/data-protection/implementation/key-encryption-at-rest) para cifrar las claves en reposo.</span><span class="sxs-lookup"><span data-stu-id="5a39e-113">Otherwise, consider using an [X.509 certificate](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt keys at rest.</span></span>

## <a name="azure-storage"></a><span data-ttu-id="5a39e-114">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="5a39e-114">Azure Storage</span></span>

<span data-ttu-id="5a39e-115">El paquete [Azure. Extensions. AspNetCore. bioprotection. blobs](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) permite almacenar las claves de protección de datos en Azure BLOB Storage.</span><span class="sxs-lookup"><span data-stu-id="5a39e-115">The [Azure.Extensions.AspNetCore.DataProtection.Blobs](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) package allows storing data protection keys in Azure Blob Storage.</span></span> <span data-ttu-id="5a39e-116">Las claves se pueden compartir entre varias instancias de una aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="5a39e-116">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="5a39e-117">Las aplicaciones pueden compartir la :::no-loc(cookie)::: protección s o CSRF entre varios servidores.</span><span class="sxs-lookup"><span data-stu-id="5a39e-117">Apps can share authentication :::no-loc(cookie):::s or CSRF protection across multiple servers.</span></span>

<span data-ttu-id="5a39e-118">Para configurar el proveedor de Azure Blob Storage, llame a una de las sobrecargas de [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) .</span><span class="sxs-lookup"><span data-stu-id="5a39e-118">To configure the Azure Blob Storage provider, call one of the [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) overloads.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

<span data-ttu-id="5a39e-119">Si la aplicación web se ejecuta como un servicio de Azure, se puede usar la cadena de conexión para autenticarse en Azure Storage mediante [Azure. Storage. blobs](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobcontainerclient).</span><span class="sxs-lookup"><span data-stu-id="5a39e-119">If the web app is running as an Azure service, connection string can be used to authenticate to Azure storage by using [Azure.Storage.Blobs](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobcontainerclient).</span></span>

```csharp
string connectionString = "<connection_string>";
string containerName = "my-key-container";
BlobContainerClient container = new BlobContainerClient(connectionString, containerName);

// optional - provision the container automatically
await container.CreateIfNotExistsAsync();

services.AddDataProtection()
    .PersistKeysToAzureBlobStorage(container, "keys.xml");
```

> [!NOTE]
> <span data-ttu-id="5a39e-120">La cadena de conexión a la cuenta de almacenamiento se puede encontrar en Azure portal en la sección "claves de acceso" o mediante la ejecución del siguiente comando de la CLI:</span><span class="sxs-lookup"><span data-stu-id="5a39e-120">The connection string to your storage account can be found in the Azure Portal under the "Access Keys" section or by running the following CLI command:</span></span> 
> ```bash
> az storage account show-connection-string --name <account_name> --resource-group <resource_group>
> ```

## <a name="redis"></a><span data-ttu-id="5a39e-121">Redis</span><span class="sxs-lookup"><span data-stu-id="5a39e-121">Redis</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="5a39e-122">El paquete [Microsoft. AspNetCore. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) permite almacenar las claves de protección de datos en una caché en Redis.</span><span class="sxs-lookup"><span data-stu-id="5a39e-122">The [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="5a39e-123">Las claves se pueden compartir entre varias instancias de una aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="5a39e-123">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="5a39e-124">Las aplicaciones pueden compartir la :::no-loc(cookie)::: protección s o CSRF entre varios servidores.</span><span class="sxs-lookup"><span data-stu-id="5a39e-124">Apps can share authentication :::no-loc(cookie):::s or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="5a39e-125">El paquete [Microsoft. AspNetCore. desproteger. Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) permite almacenar las claves de protección de datos en una caché en Redis.</span><span class="sxs-lookup"><span data-stu-id="5a39e-125">The [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="5a39e-126">Las claves se pueden compartir entre varias instancias de una aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="5a39e-126">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="5a39e-127">Las aplicaciones pueden compartir la :::no-loc(cookie)::: protección s o CSRF entre varios servidores.</span><span class="sxs-lookup"><span data-stu-id="5a39e-127">Apps can share authentication :::no-loc(cookie):::s or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="5a39e-128">Para configurar en Redis, llame a una de las sobrecargas de [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) :</span><span class="sxs-lookup"><span data-stu-id="5a39e-128">To configure on Redis, call one of the [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToStackExchangeRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="5a39e-129">Para configurar en Redis, llame a una de las sobrecargas de [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) :</span><span class="sxs-lookup"><span data-stu-id="5a39e-129">To configure on Redis, call one of the [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

<span data-ttu-id="5a39e-130">Para obtener más información, vea los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="5a39e-130">For more information, see the following topics:</span></span>

* [<span data-ttu-id="5a39e-131">StackExchange. Redis ConnectionMultiplexer</span><span class="sxs-lookup"><span data-stu-id="5a39e-131">StackExchange.Redis ConnectionMultiplexer</span></span>](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [<span data-ttu-id="5a39e-132">Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="5a39e-132">Azure Redis Cache</span></span>](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [<span data-ttu-id="5a39e-133">ASP.NET Core ejemplos de protección de los mismos</span><span class="sxs-lookup"><span data-stu-id="5a39e-133">ASP.NET Core DataProtection samples</span></span>](https://github.com/dotnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a><span data-ttu-id="5a39e-134">Registro</span><span class="sxs-lookup"><span data-stu-id="5a39e-134">Registry</span></span>

<span data-ttu-id="5a39e-135">**Solo se aplica a las implementaciones de Windows.**</span><span class="sxs-lookup"><span data-stu-id="5a39e-135">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="5a39e-136">En ocasiones, es posible que la aplicación no tenga acceso de escritura al sistema de archivos.</span><span class="sxs-lookup"><span data-stu-id="5a39e-136">Sometimes the app might not have write access to the file system.</span></span> <span data-ttu-id="5a39e-137">Considere un escenario en el que una aplicación se ejecuta como una cuenta de servicio virtual (como la identidad del grupo de aplicaciones de *w3wp.exe* ).</span><span class="sxs-lookup"><span data-stu-id="5a39e-137">Consider a scenario where an app is running as a virtual service account (such as *w3wp.exe* 's app pool identity).</span></span> <span data-ttu-id="5a39e-138">En estos casos, el administrador puede aprovisionar una clave del registro a la que se pueda tener acceso mediante la identidad de la cuenta de servicio.</span><span class="sxs-lookup"><span data-stu-id="5a39e-138">In these cases, the administrator can provision a registry key that's accessible by the service account identity.</span></span> <span data-ttu-id="5a39e-139">Llame al método de extensión [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) como se muestra a continuación.</span><span class="sxs-lookup"><span data-stu-id="5a39e-139">Call the [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) extension method as shown below.</span></span> <span data-ttu-id="5a39e-140">Proporcione una [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) que apunte a la ubicación donde se deben almacenar las claves criptográficas:</span><span class="sxs-lookup"><span data-stu-id="5a39e-140">Provide a [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) pointing to the location where cryptographic keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> <span data-ttu-id="5a39e-141">Se recomienda usar [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) para cifrar las claves en reposo.</span><span class="sxs-lookup"><span data-stu-id="5a39e-141">We recommend using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt the keys at rest.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a><span data-ttu-id="5a39e-142">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="5a39e-142">Entity Framework Core</span></span>

<span data-ttu-id="5a39e-143">El paquete [Microsoft. AspNetCore. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) proporciona un mecanismo para almacenar las claves de protección de datos en una base de datos mediante Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="5a39e-143">The [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) package provides a mechanism for storing data protection keys to a database using Entity Framework Core.</span></span> <span data-ttu-id="5a39e-144">El `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` paquete NuGet debe agregarse al archivo de proyecto, no es parte del [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="5a39e-144">The `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet package must be added to the project file, it's not part of the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="5a39e-145">Con este paquete, las claves se pueden compartir entre varias instancias de una aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="5a39e-145">With this package, keys can be shared across multiple instances of a web app.</span></span>

<span data-ttu-id="5a39e-146">Para configurar el proveedor de EF Core, llame al método [PersistKeysToDbContext \<TContext> ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) :</span><span class="sxs-lookup"><span data-stu-id="5a39e-146">To configure the EF Core provider, call the [PersistKeysToDbContext\<TContext>](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) method:</span></span>

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="5a39e-147">El parámetro genérico, `TContext` , debe heredar de [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) e implementar [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span><span class="sxs-lookup"><span data-stu-id="5a39e-147">The generic parameter, `TContext`, must inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and implement [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span></span>

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

<span data-ttu-id="5a39e-148">Cree la tabla `DataProtectionKeys`.</span><span class="sxs-lookup"><span data-stu-id="5a39e-148">Create the `DataProtectionKeys` table.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5a39e-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a39e-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5a39e-150">Ejecute los siguientes comandos en la ventana de la **consola del administrador de paquetes** (PMC):</span><span class="sxs-lookup"><span data-stu-id="5a39e-150">Execute the following commands in the **Package Manager Console** (PMC) window:</span></span>

```powershell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-cli"></a>[<span data-ttu-id="5a39e-151">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="5a39e-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5a39e-152">Ejecute los siguientes comandos en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="5a39e-152">Execute the following commands in a command shell:</span></span>

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

<span data-ttu-id="5a39e-153">`MyKeysContext` es el `DbContext` definido en el ejemplo de código anterior.</span><span class="sxs-lookup"><span data-stu-id="5a39e-153">`MyKeysContext` is the `DbContext` defined in the preceding code sample.</span></span> <span data-ttu-id="5a39e-154">Si utiliza un `DbContext` con un nombre diferente, sustituya el `DbContext` nombre por `MyKeysContext` .</span><span class="sxs-lookup"><span data-stu-id="5a39e-154">If you're using a `DbContext` with a different name, substitute your `DbContext` name for `MyKeysContext`.</span></span>

<span data-ttu-id="5a39e-155">La `DataProtectionKeys` clase o entidad adopta la estructura que se muestra en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="5a39e-155">The `DataProtectionKeys` class/entity adopts the structure shown in the following table.</span></span>

| <span data-ttu-id="5a39e-156">Propiedad o campo</span><span class="sxs-lookup"><span data-stu-id="5a39e-156">Property/Field</span></span> | <span data-ttu-id="5a39e-157">Tipo CLR</span><span class="sxs-lookup"><span data-stu-id="5a39e-157">CLR Type</span></span> | <span data-ttu-id="5a39e-158">Tipo de SQL</span><span class="sxs-lookup"><span data-stu-id="5a39e-158">SQL Type</span></span>              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | <span data-ttu-id="5a39e-159">`int`, PK, `IDENTITY(1,1)` , not null</span><span class="sxs-lookup"><span data-stu-id="5a39e-159">`int`, PK, `IDENTITY(1,1)`, not null</span></span>   |
| `FriendlyName` | `string` | <span data-ttu-id="5a39e-160">`nvarchar(MAX)`, null</span><span class="sxs-lookup"><span data-stu-id="5a39e-160">`nvarchar(MAX)`, null</span></span> |
| `Xml`          | `string` | <span data-ttu-id="5a39e-161">`nvarchar(MAX)`, null</span><span class="sxs-lookup"><span data-stu-id="5a39e-161">`nvarchar(MAX)`, null</span></span> |

::: moniker-end

## <a name="custom-key-repository"></a><span data-ttu-id="5a39e-162">Repositorio de claves personalizadas</span><span class="sxs-lookup"><span data-stu-id="5a39e-162">Custom key repository</span></span>

<span data-ttu-id="5a39e-163">Si los mecanismos integrados no son adecuados, el desarrollador puede especificar su propio mecanismo de persistencia de claves proporcionando un [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository)personalizado.</span><span class="sxs-lookup"><span data-stu-id="5a39e-163">If the in-box mechanisms aren't appropriate, the developer can specify their own key persistence mechanism by providing a custom [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span></span>
