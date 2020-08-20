---
title: Configurar la protección de datos en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo configurar la protección de datos en ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
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
uid: security/data-protection/configuration/overview
ms.openlocfilehash: aa7f6f3c1ff8042bd11bba485a2d7b8aaa6ef88a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626719"
---
# <a name="configure-aspnet-core-data-protection"></a><span data-ttu-id="04975-103">Configurar la protección de datos en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="04975-103">Configure ASP.NET Core Data Protection</span></span>

<span data-ttu-id="04975-104">Cuando se inicializa el sistema de protección de datos, aplica la [configuración predeterminada](xref:security/data-protection/configuration/default-settings) en función del entorno operativo.</span><span class="sxs-lookup"><span data-stu-id="04975-104">When the Data Protection system is initialized, it applies [default settings](xref:security/data-protection/configuration/default-settings) based on the operational environment.</span></span> <span data-ttu-id="04975-105">Esta configuración suele ser adecuada para las aplicaciones que se ejecutan en un solo equipo.</span><span class="sxs-lookup"><span data-stu-id="04975-105">These settings are generally appropriate for apps running on a single machine.</span></span> <span data-ttu-id="04975-106">Hay casos en los que un desarrollador puede querer cambiar la configuración predeterminada:</span><span class="sxs-lookup"><span data-stu-id="04975-106">There are cases where a developer may want to change the default settings:</span></span>

* <span data-ttu-id="04975-107">La aplicación se distribuye entre varias máquinas.</span><span class="sxs-lookup"><span data-stu-id="04975-107">The app is spread across multiple machines.</span></span>
* <span data-ttu-id="04975-108">Por motivos de cumplimiento.</span><span class="sxs-lookup"><span data-stu-id="04975-108">For compliance reasons.</span></span>

<span data-ttu-id="04975-109">En estos casos, el sistema de protección de datos ofrece una API de configuración enriquecida.</span><span class="sxs-lookup"><span data-stu-id="04975-109">For these scenarios, the Data Protection system offers a rich configuration API.</span></span>

> [!WARNING]
> <span data-ttu-id="04975-110">Al igual que los archivos de configuración, el anillo de claves de protección de datos debe protegerse con los permisos adecuados.</span><span class="sxs-lookup"><span data-stu-id="04975-110">Similar to configuration files, the data protection key ring should be protected using appropriate permissions.</span></span> <span data-ttu-id="04975-111">Puede optar por cifrar las claves en reposo, pero esto no impide que los atacantes creen nuevas claves.</span><span class="sxs-lookup"><span data-stu-id="04975-111">You can choose to encrypt keys at rest, but this doesn't prevent attackers from creating new keys.</span></span> <span data-ttu-id="04975-112">Por lo tanto, la seguridad de la aplicación se ve afectada.</span><span class="sxs-lookup"><span data-stu-id="04975-112">Consequently, your app's security is impacted.</span></span> <span data-ttu-id="04975-113">La ubicación de almacenamiento configurada con la protección de datos debe tener su acceso limitado a la propia aplicación, de manera similar a la forma en que se protegen los archivos de configuración.</span><span class="sxs-lookup"><span data-stu-id="04975-113">The storage location configured with Data Protection should have its access limited to the app itself, similar to the way you would protect configuration files.</span></span> <span data-ttu-id="04975-114">Por ejemplo, si decide almacenar el anillo de claves en el disco, use los permisos del sistema de archivos.</span><span class="sxs-lookup"><span data-stu-id="04975-114">For example, if you choose to store your key ring on disk, use file system permissions.</span></span> <span data-ttu-id="04975-115">Asegúrese de que solo la identidad en la que se ejecuta la aplicación web tenga acceso de lectura, escritura y creación a ese directorio.</span><span class="sxs-lookup"><span data-stu-id="04975-115">Ensure only the identity under which your web app runs has read, write, and create access to that directory.</span></span> <span data-ttu-id="04975-116">Si usa Azure Blob Storage, solo la aplicación Web debe tener la capacidad de leer, escribir o crear nuevas entradas en el almacén de blobs, etc.</span><span class="sxs-lookup"><span data-stu-id="04975-116">If you use Azure Blob Storage, only the web app should have the ability to read, write, or create new entries in the blob store, etc.</span></span>
>
> <span data-ttu-id="04975-117">El método de extensión [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) devuelve un [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="04975-117">The extension method [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) returns an [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span></span> <span data-ttu-id="04975-118">`IDataProtectionBuilder` Expone métodos de extensión que se pueden encadenar juntos para configurar las opciones de protección de datos.</span><span class="sxs-lookup"><span data-stu-id="04975-118">`IDataProtectionBuilder` exposes extension methods that you can chain together to configure Data Protection options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="04975-119">Los siguientes paquetes NuGet son necesarios para las extensiones de protección de datos que se usan en este artículo:</span><span class="sxs-lookup"><span data-stu-id="04975-119">The following NuGet packages are required for the Data Protection extensions used in this article:</span></span>

* [<span data-ttu-id="04975-120">Microsoft. AspNetCore. AzureStorage</span><span class="sxs-lookup"><span data-stu-id="04975-120">Microsoft.AspNetCore.DataProtection.AzureStorage</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/)
* [<span data-ttu-id="04975-121">Microsoft. AspNetCore. AzureKeyVault</span><span class="sxs-lookup"><span data-stu-id="04975-121">Microsoft.AspNetCore.DataProtection.AzureKeyVault</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault/)

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a><span data-ttu-id="04975-122">ProtectKeysWithAzureKeyVault</span><span class="sxs-lookup"><span data-stu-id="04975-122">ProtectKeysWithAzureKeyVault</span></span>

<span data-ttu-id="04975-123">Para almacenar claves en [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure el sistema con [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) en la `Startup` clase:</span><span class="sxs-lookup"><span data-stu-id="04975-123">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="04975-124">Establezca la ubicación de almacenamiento del anillo de claves (por ejemplo, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span><span class="sxs-lookup"><span data-stu-id="04975-124">Set the key ring storage location (for example, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span></span> <span data-ttu-id="04975-125">Se debe establecer la ubicación porque la llamada a `ProtectKeysWithAzureKeyVault` implementa un [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) que deshabilita la configuración automática de la protección de datos, incluida la ubicación de almacenamiento del anillo de claves.</span><span class="sxs-lookup"><span data-stu-id="04975-125">The location must be set because calling `ProtectKeysWithAzureKeyVault` implements an [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) that disables automatic data protection settings, including the key ring storage location.</span></span> <span data-ttu-id="04975-126">En el ejemplo anterior se usa Azure Blob Storage para conservar el anillo de claves.</span><span class="sxs-lookup"><span data-stu-id="04975-126">The preceding example uses Azure Blob Storage to persist the key ring.</span></span> <span data-ttu-id="04975-127">Para obtener más información, consulte [proveedores de almacenamiento de claves: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage).</span><span class="sxs-lookup"><span data-stu-id="04975-127">For more information, see [Key storage providers: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage).</span></span> <span data-ttu-id="04975-128">También puede conservar el anillo de claves localmente con [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span><span class="sxs-lookup"><span data-stu-id="04975-128">You can also persist the key ring locally with [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span></span>

<span data-ttu-id="04975-129">`keyIdentifier`Es el identificador de clave del almacén de claves que se usa para el cifrado de claves.</span><span class="sxs-lookup"><span data-stu-id="04975-129">The `keyIdentifier` is the key vault key identifier used for key encryption.</span></span> <span data-ttu-id="04975-130">Por ejemplo, una clave creada en el almacén de claves denominado `dataprotection` en `contosokeyvault` tiene el identificador de clave `https://contosokeyvault.vault.azure.net/keys/dataprotection/` .</span><span class="sxs-lookup"><span data-stu-id="04975-130">For example, a key created in key vault named `dataprotection` in the `contosokeyvault` has the key identifier `https://contosokeyvault.vault.azure.net/keys/dataprotection/`.</span></span> <span data-ttu-id="04975-131">Proporcione la aplicación con los permisos clave de **desencapsulado** y **clave de encapsulado** en el almacén de claves.</span><span class="sxs-lookup"><span data-stu-id="04975-131">Provide the app with **Unwrap Key** and **Wrap Key** permissions to the key vault.</span></span>

<span data-ttu-id="04975-132">`ProtectKeysWithAzureKeyVault` sobrecargas</span><span class="sxs-lookup"><span data-stu-id="04975-132">`ProtectKeysWithAzureKeyVault` overloads:</span></span>

* <span data-ttu-id="04975-133">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) permite el uso de una [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) para permitir que el sistema de protección de datos use el almacén de claves.</span><span class="sxs-lookup"><span data-stu-id="04975-133">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) permits the use of a [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="04975-134">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) permite el uso de `ClientId` y [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) para permitir que el sistema de protección de datos use el almacén de claves.</span><span class="sxs-lookup"><span data-stu-id="04975-134">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) permits the use of a `ClientId` and [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="04975-135">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) permite el uso de `ClientId` y `ClientSecret` para habilitar el sistema de protección de datos para que use el almacén de claves.</span><span class="sxs-lookup"><span data-stu-id="04975-135">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) permits the use of a `ClientId` and `ClientSecret` to enable the data protection system to use the key vault.</span></span>

<span data-ttu-id="04975-136">Cuando se usa una combinación de keyvault y Azure Storage para almacenar y proteger claves, se `System.UriFormatException` producirá una excepción si el BLOB en el que se almacenan las claves no existe todavía.</span><span class="sxs-lookup"><span data-stu-id="04975-136">When using a combination of keyvault and azure storage to store and protect keys, a `System.UriFormatException` will be thrown if the blob to store the keys in does not already exist.</span></span> <span data-ttu-id="04975-137">Esto se puede crear manualmente antes de ejecutar la aplicación o `.ProtectKeysWithAzureKeyVault()` se puede quitar para la primera ejecución para crear el BLOB en su lugar y, después, agregarlo a las ejecuciones posteriores.</span><span class="sxs-lookup"><span data-stu-id="04975-137">This can be manually created ahead of running the application, or `.ProtectKeysWithAzureKeyVault()` can be removed for the first run to create the blob in place, then adding it on for subsequent runs.</span></span> <span data-ttu-id="04975-138">`.ProtectKeysWithAzureKeyVault()`Se recomienda quitar, ya que esto garantizará que el archivo se crea con el esquema y los valores adecuados.</span><span class="sxs-lookup"><span data-stu-id="04975-138">Removing `.ProtectKeysWithAzureKeyVault()` is advised, as this will ensure that the file is created with the proper schema and values in place.</span></span>

```csharp
var storageAccount = CloudStorageAccount.Parse("<storage account connection string">);
var client = storageAccount.CreateCloudBlobClient();
var container = client.GetContainerReference("<key store container name>");

var azureServiceTokenProvider = new AzureServiceTokenProvider();
var keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(
        azureServiceTokenProvider.KeyVaultTokenCallback));

services.AddDataProtection()
    //This blob must already exist before the application is run
    .PersistKeysToAzureBlobStorage(container, "<key store blob name>")
    //Removing this line below for an initial run will ensure the file is created correctly
    .ProtectKeysWithAzureKeyVault(keyVaultClient, "<keyIdentifier>");
```

::: moniker-end

## <a name="persistkeystofilesystem"></a><span data-ttu-id="04975-139">PersistKeysToFileSystem</span><span class="sxs-lookup"><span data-stu-id="04975-139">PersistKeysToFileSystem</span></span>

<span data-ttu-id="04975-140">Para almacenar claves en un recurso compartido UNC en lugar de en la ubicación predeterminada *% LOCALAPPDATA%* , configure el sistema con [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span><span class="sxs-lookup"><span data-stu-id="04975-140">To store keys on a UNC share instead of at the *%LOCALAPPDATA%* default location, configure the system with [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> <span data-ttu-id="04975-141">Si cambia la ubicación de persistencia de claves, el sistema ya no cifrará automáticamente las claves en reposo, ya que no sabrá si DPAPI es un mecanismo de cifrado adecuado.</span><span class="sxs-lookup"><span data-stu-id="04975-141">If you change the key persistence location, the system no longer automatically encrypts keys at rest, since it doesn't know whether DPAPI is an appropriate encryption mechanism.</span></span>

## <a name="protectkeyswith"></a><span data-ttu-id="04975-142">ProtectKeysWith\*</span><span class="sxs-lookup"><span data-stu-id="04975-142">ProtectKeysWith\*</span></span>

<span data-ttu-id="04975-143">Puede configurar el sistema para proteger las claves en reposo mediante una llamada a cualquiera de las API de configuración de [ProtectKeysWith \* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) .</span><span class="sxs-lookup"><span data-stu-id="04975-143">You can configure the system to protect keys at rest by calling any of the [ProtectKeysWith\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) configuration APIs.</span></span> <span data-ttu-id="04975-144">Considere el ejemplo siguiente, que almacena las claves en un recurso compartido UNC y las cifra en reposo con un certificado X. 509 específico:</span><span class="sxs-lookup"><span data-stu-id="04975-144">Consider the example below, which stores keys on a UNC share and encrypts those keys at rest with a specific X.509 certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="04975-145">En ASP.NET Core 2,1 o posterior, puede proporcionar un [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) a [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), como un certificado cargado desde un archivo:</span><span class="sxs-lookup"><span data-stu-id="04975-145">In ASP.NET Core 2.1 or later, you can provide an [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), such as a certificate loaded from a file:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
}
```

::: moniker-end

<span data-ttu-id="04975-146">Consulte [cifrado de claves en reposo](xref:security/data-protection/implementation/key-encryption-at-rest) para obtener más ejemplos y debate sobre los mecanismos de cifrado de claves integrados.</span><span class="sxs-lookup"><span data-stu-id="04975-146">See [Key Encryption At Rest](xref:security/data-protection/implementation/key-encryption-at-rest) for more examples and discussion on the built-in key encryption mechanisms.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a><span data-ttu-id="04975-147">UnprotectKeysWithAnyCertificate</span><span class="sxs-lookup"><span data-stu-id="04975-147">UnprotectKeysWithAnyCertificate</span></span>

<span data-ttu-id="04975-148">En ASP.NET Core 2,1 o posterior, puede rotar los certificados y descifrar las claves en reposo mediante una matriz de certificados [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) con [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span><span class="sxs-lookup"><span data-stu-id="04975-148">In ASP.NET Core 2.1 or later, you can rotate certificates and decrypt keys at rest using an array of [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) certificates with [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
        .UnprotectKeysWithAnyCertificate(
            new X509Certificate2("certificate_old_1.pfx", "password_1"),
            new X509Certificate2("certificate_old_2.pfx", "password_2"));
}
```

::: moniker-end

## <a name="setdefaultkeylifetime"></a><span data-ttu-id="04975-149">SetDefaultKeyLifetime</span><span class="sxs-lookup"><span data-stu-id="04975-149">SetDefaultKeyLifetime</span></span>

<span data-ttu-id="04975-150">Para configurar el sistema de forma que use una vigencia de clave de 14 días en lugar del valor predeterminado de 90 días, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span><span class="sxs-lookup"><span data-stu-id="04975-150">To configure the system to use a key lifetime of 14 days instead of the default 90 days, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a><span data-ttu-id="04975-151">SetApplicationName</span><span class="sxs-lookup"><span data-stu-id="04975-151">SetApplicationName</span></span>

<span data-ttu-id="04975-152">De forma predeterminada, el sistema de protección de datos aísla las aplicaciones de otras en función de sus rutas de acceso [raíz de contenido](xref:fundamentals/index#content-root) , incluso si están compartiendo el mismo repositorio de claves físicas.</span><span class="sxs-lookup"><span data-stu-id="04975-152">By default, the Data Protection system isolates apps from one another based on their [content root](xref:fundamentals/index#content-root) paths, even if they're sharing the same physical key repository.</span></span> <span data-ttu-id="04975-153">Esto evita que las aplicaciones conozcan las cargas protegidas de los demás.</span><span class="sxs-lookup"><span data-stu-id="04975-153">This prevents the apps from understanding each other's protected payloads.</span></span>

<span data-ttu-id="04975-154">Para compartir cargas protegidas entre aplicaciones:</span><span class="sxs-lookup"><span data-stu-id="04975-154">To share protected payloads among apps:</span></span>

* <span data-ttu-id="04975-155">Configure <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> en cada aplicación con el mismo valor.</span><span class="sxs-lookup"><span data-stu-id="04975-155">Configure <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> in each app with the same value.</span></span>
* <span data-ttu-id="04975-156">Use la misma versión de la pila de API de protección de datos en las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="04975-156">Use the same version of the Data Protection API stack across the apps.</span></span> <span data-ttu-id="04975-157">Realice **una** de las siguientes acciones en los archivos de proyecto de las aplicaciones:</span><span class="sxs-lookup"><span data-stu-id="04975-157">Perform **either** of the following in the apps' project files:</span></span>
  * <span data-ttu-id="04975-158">Haga referencia a la misma versión de .NET Framework compartida a través del [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="04975-158">Reference the same shared framework version via the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="04975-159">Haga referencia a la misma versión del [paquete de protección de datos](xref:security/data-protection/introduction#package-layout) .</span><span class="sxs-lookup"><span data-stu-id="04975-159">Reference the same [Data Protection package](xref:security/data-protection/introduction#package-layout) version.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a><span data-ttu-id="04975-160">DisableAutomaticKeyGeneration</span><span class="sxs-lookup"><span data-stu-id="04975-160">DisableAutomaticKeyGeneration</span></span>

<span data-ttu-id="04975-161">Puede tener un escenario en el que no quiera que una aplicación revierta automáticamente las claves (crear nuevas claves) a medida que se acerquen a la expiración.</span><span class="sxs-lookup"><span data-stu-id="04975-161">You may have a scenario where you don't want an app to automatically roll keys (create new keys) as they approach expiration.</span></span> <span data-ttu-id="04975-162">Un ejemplo de esto puede ser que las aplicaciones estén configuradas en una relación de principal o secundaria, donde solo la aplicación principal es responsable de los problemas de administración de claves y las aplicaciones secundarias simplemente tienen una vista de solo lectura del anillo de claves.</span><span class="sxs-lookup"><span data-stu-id="04975-162">One example of this might be apps set up in a primary/secondary relationship, where only the primary app is responsible for key management concerns and secondary apps simply have a read-only view of the key ring.</span></span> <span data-ttu-id="04975-163">Las aplicaciones secundarias se pueden configurar para tratar el anillo de claves como de solo lectura configurando el sistema con <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*> :</span><span class="sxs-lookup"><span data-stu-id="04975-163">The secondary apps can be configured to treat the key ring as read-only by configuring the system with <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a><span data-ttu-id="04975-164">Aislamiento por aplicación</span><span class="sxs-lookup"><span data-stu-id="04975-164">Per-application isolation</span></span>

<span data-ttu-id="04975-165">Cuando un host de ASP.NET Core proporciona el sistema de protección de datos, aísla automáticamente las aplicaciones entre sí, aunque dichas aplicaciones se ejecuten en la misma cuenta de proceso de trabajo y usen el mismo material de creación de claves maestro.</span><span class="sxs-lookup"><span data-stu-id="04975-165">When the Data Protection system is provided by an ASP.NET Core host, it automatically isolates apps from one another, even if those apps are running under the same worker process account and are using the same master keying material.</span></span> <span data-ttu-id="04975-166">Esto es algo similar al modificador IsolateApps del elemento System. Web `<machineKey>` .</span><span class="sxs-lookup"><span data-stu-id="04975-166">This is somewhat similar to the IsolateApps modifier from System.Web's `<machineKey>` element.</span></span>

<span data-ttu-id="04975-167">El mecanismo de aislamiento funciona considerando cada aplicación en el equipo local como un inquilino único, por lo que la <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> raíz de cualquier aplicación determinada incluye automáticamente el identificador de la aplicación como discriminador.</span><span class="sxs-lookup"><span data-stu-id="04975-167">The isolation mechanism works by considering each app on the local machine as a unique tenant, thus the <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> rooted for any given app automatically includes the app ID as a discriminator.</span></span> <span data-ttu-id="04975-168">El identificador único de la aplicación es la ruta de acceso física de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="04975-168">The app's unique ID is the app's physical path:</span></span>

* <span data-ttu-id="04975-169">En el caso de las aplicaciones hospedadas en IIS, el identificador único es la ruta de acceso física de IIS de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="04975-169">For apps hosted in IIS, the unique ID is the IIS physical path of the app.</span></span> <span data-ttu-id="04975-170">Si una aplicación se implementa en un entorno de granja de servidores Web, este valor es estable suponiendo que los entornos de IIS estén configurados de forma similar en todos los equipos de la granja de servidores Web.</span><span class="sxs-lookup"><span data-stu-id="04975-170">If an app is deployed in a web farm environment, this value is stable assuming that the IIS environments are configured similarly across all machines in the web farm.</span></span>
* <span data-ttu-id="04975-171">En el caso de las aplicaciones autohospedadas que se ejecutan en el [servidor de Kestrel](xref:fundamentals/servers/index#kestrel), el identificador único es la ruta de acceso física a la aplicación en el disco.</span><span class="sxs-lookup"><span data-stu-id="04975-171">For self-hosted apps running on the [Kestrel server](xref:fundamentals/servers/index#kestrel), the unique ID is the physical path to the app on disk.</span></span>

<span data-ttu-id="04975-172">El identificador único está diseñado para sobrevivir a los restablecimientos &mdash; de la aplicación individual y del equipo.</span><span class="sxs-lookup"><span data-stu-id="04975-172">The unique identifier is designed to survive resets&mdash;both of the individual app and of the machine itself.</span></span>

<span data-ttu-id="04975-173">Este mecanismo de aislamiento presupone que las aplicaciones no son malintencionadas.</span><span class="sxs-lookup"><span data-stu-id="04975-173">This isolation mechanism assumes that the apps are not malicious.</span></span> <span data-ttu-id="04975-174">Una aplicación malintencionada siempre puede afectar A cualquier otra aplicación que se ejecute en la misma cuenta de proceso de trabajo.</span><span class="sxs-lookup"><span data-stu-id="04975-174">A malicious app can always impact any other app running under the same worker process account.</span></span> <span data-ttu-id="04975-175">En un entorno de hospedaje compartido en el que las aplicaciones no son de confianza, el proveedor de hospedaje debe tomar medidas para garantizar el aislamiento de nivel de sistema operativo entre las aplicaciones, incluida la separación de los repositorios de claves subyacentes de las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="04975-175">In a shared hosting environment where apps are mutually untrusted, the hosting provider should take steps to ensure OS-level isolation between apps, including separating the apps' underlying key repositories.</span></span>

<span data-ttu-id="04975-176">Si un host de ASP.NET Core no proporciona el sistema de protección de datos (por ejemplo, si crea una instancia del mismo mediante el `DataProtectionProvider` tipo concreto) el aislamiento de la aplicación está deshabilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="04975-176">If the Data Protection system isn't provided by an ASP.NET Core host (for example, if you instantiate it via the `DataProtectionProvider` concrete type) app isolation is disabled by default.</span></span> <span data-ttu-id="04975-177">Cuando el aislamiento de la aplicación está deshabilitado, todas las aplicaciones respaldadas por el mismo material de creación de claves pueden compartir cargas, siempre y cuando proporcionen los [propósitos](xref:security/data-protection/consumer-apis/purpose-strings)adecuados.</span><span class="sxs-lookup"><span data-stu-id="04975-177">When app isolation is disabled, all apps backed by the same keying material can share payloads as long as they provide the appropriate [purposes](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="04975-178">Para proporcionar aislamiento de la aplicación en este entorno, llame al método [SetApplicationName](#setapplicationname) en el objeto de configuración y proporcione un nombre único para cada aplicación.</span><span class="sxs-lookup"><span data-stu-id="04975-178">To provide app isolation in this environment, call the [SetApplicationName](#setapplicationname) method on the configuration object and provide a unique name for each app.</span></span>

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a><span data-ttu-id="04975-179">Cambiar algoritmos con UseCryptographicAlgorithms</span><span class="sxs-lookup"><span data-stu-id="04975-179">Changing algorithms with UseCryptographicAlgorithms</span></span>

<span data-ttu-id="04975-180">La pila de protección de datos permite cambiar el algoritmo predeterminado que usan las claves generadas recientemente.</span><span class="sxs-lookup"><span data-stu-id="04975-180">The Data Protection stack allows you to change the default algorithm used by newly-generated keys.</span></span> <span data-ttu-id="04975-181">La manera más sencilla de hacerlo es llamar a [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) desde la devolución de llamada de configuración:</span><span class="sxs-lookup"><span data-stu-id="04975-181">The simplest way to do this is to call [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) from the configuration callback:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

<span data-ttu-id="04975-182">El valor predeterminado de EncryptionAlgorithm es AES-256-CBC y el valor predeterminado de ValidationAlgorithm es HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="04975-182">The default EncryptionAlgorithm is AES-256-CBC, and the default ValidationAlgorithm is HMACSHA256.</span></span> <span data-ttu-id="04975-183">La directiva predeterminada se puede establecer mediante un administrador del sistema a través de una [Directiva de todo el equipo](xref:security/data-protection/configuration/machine-wide-policy), pero una llamada explícita a `UseCryptographicAlgorithms` invalida la directiva predeterminada.</span><span class="sxs-lookup"><span data-stu-id="04975-183">The default policy can be set by a system administrator via a [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy), but an explicit call to `UseCryptographicAlgorithms` overrides the default policy.</span></span>

<span data-ttu-id="04975-184">`UseCryptographicAlgorithms`La llamada le permite especificar el algoritmo deseado a partir de una lista integrada predefinida.</span><span class="sxs-lookup"><span data-stu-id="04975-184">Calling `UseCryptographicAlgorithms` allows you to specify the desired algorithm from a predefined built-in list.</span></span> <span data-ttu-id="04975-185">No es necesario preocuparse por la implementación del algoritmo.</span><span class="sxs-lookup"><span data-stu-id="04975-185">You don't need to worry about the implementation of the algorithm.</span></span> <span data-ttu-id="04975-186">En el escenario anterior, el sistema de protección de datos intenta usar la implementación de CNG de AES si se ejecuta en Windows.</span><span class="sxs-lookup"><span data-stu-id="04975-186">In the scenario above, the Data Protection system attempts to use the CNG implementation of AES if running on Windows.</span></span> <span data-ttu-id="04975-187">De lo contrario, recurre a la clase administrada [System. Security. Cryptography. AES](/dotnet/api/system.security.cryptography.aes) .</span><span class="sxs-lookup"><span data-stu-id="04975-187">Otherwise, it falls back to the managed [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) class.</span></span>

<span data-ttu-id="04975-188">Puede especificar manualmente una implementación a través de una llamada a [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span><span class="sxs-lookup"><span data-stu-id="04975-188">You can manually specify an implementation via a call to [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span></span>

> [!TIP]
> <span data-ttu-id="04975-189">El cambio de algoritmos no afecta a las claves existentes en el anillo de claves.</span><span class="sxs-lookup"><span data-stu-id="04975-189">Changing algorithms doesn't affect existing keys in the key ring.</span></span> <span data-ttu-id="04975-190">Solo afecta a las claves generadas recientemente.</span><span class="sxs-lookup"><span data-stu-id="04975-190">It only affects newly-generated keys.</span></span>

### <a name="specifying-custom-managed-algorithms"></a><span data-ttu-id="04975-191">Especificar algoritmos administrados personalizados</span><span class="sxs-lookup"><span data-stu-id="04975-191">Specifying custom managed algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="04975-192">Para especificar algoritmos administrados personalizados, cree una instancia de [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) que apunte a los tipos de implementación:</span><span class="sxs-lookup"><span data-stu-id="04975-192">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instance that points to the implementation types:</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptorConfiguration()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="04975-193">Para especificar algoritmos administrados personalizados, cree una instancia de [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) que apunte a los tipos de implementación:</span><span class="sxs-lookup"><span data-stu-id="04975-193">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instance that points to the implementation types:</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptionSettings()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

<span data-ttu-id="04975-194">Por lo general, las \* propiedades de tipo deben apuntar a implementaciones concretas y en instancias (a través de un constructor sin parámetros público) de [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) y [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), aunque el sistema especial tiene algunos valores como `typeof(Aes)` para mayor comodidad.</span><span class="sxs-lookup"><span data-stu-id="04975-194">Generally the \*Type properties must point to concrete, instantiable (via a public parameterless ctor) implementations of [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) and [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), though the system special-cases some values like `typeof(Aes)` for convenience.</span></span>

> [!NOTE]
> <span data-ttu-id="04975-195">La SymmetricAlgorithm debe tener una longitud de clave de ≥ 128 bits y un tamaño de bloque de ≥ 64 bits, y debe admitir el cifrado de modo CBC con el relleno de #7 de PKCS.</span><span class="sxs-lookup"><span data-stu-id="04975-195">The SymmetricAlgorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="04975-196">El KeyedHashAlgorithm debe tener un tamaño de síntesis de >= 128 bits y debe admitir claves de longitud igual a la longitud de síntesis del algoritmo hash.</span><span class="sxs-lookup"><span data-stu-id="04975-196">The KeyedHashAlgorithm must have a digest size of >= 128 bits, and it must support keys of length equal to the hash algorithm's digest length.</span></span> <span data-ttu-id="04975-197">No es estrictamente necesario que el KeyedHashAlgorithm sea HMAC.</span><span class="sxs-lookup"><span data-stu-id="04975-197">The KeyedHashAlgorithm isn't strictly required to be HMAC.</span></span>

### <a name="specifying-custom-windows-cng-algorithms"></a><span data-ttu-id="04975-198">Especificar algoritmos CNG de Windows personalizados</span><span class="sxs-lookup"><span data-stu-id="04975-198">Specifying custom Windows CNG algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="04975-199">Para especificar un algoritmo CNG de Windows personalizado mediante el cifrado de modo CBC con validación HMAC, cree una instancia de [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) que contenga la información algorítmica:</span><span class="sxs-lookup"><span data-stu-id="04975-199">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="04975-200">Para especificar un algoritmo CNG de Windows personalizado mediante el cifrado de modo CBC con validación HMAC, cree una instancia de [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) que contenga la información algorítmica:</span><span class="sxs-lookup"><span data-stu-id="04975-200">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="04975-201">El algoritmo de cifrado de bloques simétricos debe tener una longitud de clave de >= 128 bits, un tamaño de bloque de >= 64 bits y debe admitir el cifrado de modo CBC con el relleno de #7 PKCS.</span><span class="sxs-lookup"><span data-stu-id="04975-201">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of >= 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="04975-202">El algoritmo hash debe tener un tamaño de síntesis de >= 128 bits y debe admitir que se abra con \_ la \_ \_ marca de marca HMAC del identificador de ALG de BCRYPT \_ .</span><span class="sxs-lookup"><span data-stu-id="04975-202">The hash algorithm must have a digest size of >= 128 bits and must support being opened with the BCRYPT\_ALG\_HANDLE\_HMAC\_FLAG flag.</span></span> <span data-ttu-id="04975-203">Las \* propiedades del proveedor se pueden establecer en null para usar el proveedor predeterminado para el algoritmo especificado.</span><span class="sxs-lookup"><span data-stu-id="04975-203">The \*Provider properties can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="04975-204">Consulte la documentación de [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="04975-204">See the [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) documentation for more information.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="04975-205">Para especificar un algoritmo CNG de Windows personalizado mediante el cifrado de modo de Galois/contador con validación, cree una instancia de [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) que contenga la información algorítmica:</span><span class="sxs-lookup"><span data-stu-id="04975-205">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="04975-206">Para especificar un algoritmo CNG de Windows personalizado mediante el cifrado de modo de Galois/contador con validación, cree una instancia de [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) que contenga la información algorítmica:</span><span class="sxs-lookup"><span data-stu-id="04975-206">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="04975-207">El algoritmo de cifrado del bloque simétrico debe tener una longitud de clave de >= 128 bits, un tamaño de bloque de exactamente 128 bits y debe admitir el cifrado de GCM.</span><span class="sxs-lookup"><span data-stu-id="04975-207">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of exactly 128 bits, and it must support GCM encryption.</span></span> <span data-ttu-id="04975-208">Puede establecer la propiedad [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) en null para usar el proveedor predeterminado para el algoritmo especificado.</span><span class="sxs-lookup"><span data-stu-id="04975-208">You can set the [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) property to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="04975-209">Consulte la documentación de [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="04975-209">See the [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) documentation for more information.</span></span>

### <a name="specifying-other-custom-algorithms"></a><span data-ttu-id="04975-210">Especificar otros algoritmos personalizados</span><span class="sxs-lookup"><span data-stu-id="04975-210">Specifying other custom algorithms</span></span>

<span data-ttu-id="04975-211">Aunque no se expone como una API de primera clase, el sistema de protección de datos es lo suficientemente extensible como para permitir especificar casi cualquier tipo de algoritmo.</span><span class="sxs-lookup"><span data-stu-id="04975-211">Though not exposed as a first-class API, the Data Protection system is extensible enough to allow specifying almost any kind of algorithm.</span></span> <span data-ttu-id="04975-212">Por ejemplo, es posible mantener todas las claves contenidas en un módulo de seguridad de hardware (HSM) y proporcionar una implementación personalizada de las rutinas de cifrado y descifrado principales.</span><span class="sxs-lookup"><span data-stu-id="04975-212">For example, it's possible to keep all keys contained within a Hardware Security Module (HSM) and to provide a custom implementation of the core encryption and decryption routines.</span></span> <span data-ttu-id="04975-213">Vea [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) en [extensibilidad de criptografía básica](xref:security/data-protection/extensibility/core-crypto) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="04975-213">See [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in [Core cryptography extensibility](xref:security/data-protection/extensibility/core-crypto) for more information.</span></span>

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a><span data-ttu-id="04975-214">Persistencia de claves al hospedar en un contenedor de Docker</span><span class="sxs-lookup"><span data-stu-id="04975-214">Persisting keys when hosting in a Docker container</span></span>

<span data-ttu-id="04975-215">Al hospedar en un contenedor de [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) , las claves deben mantenerse en:</span><span class="sxs-lookup"><span data-stu-id="04975-215">When hosting in a [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) container, keys should be maintained in either:</span></span>

* <span data-ttu-id="04975-216">Una carpeta que es un volumen de Docker que se mantiene más allá de la duración del contenedor, como un volumen compartido o un volumen montado en el host.</span><span class="sxs-lookup"><span data-stu-id="04975-216">A folder that's a Docker volume that persists beyond the container's lifetime, such as a shared volume or a host-mounted volume.</span></span>
* <span data-ttu-id="04975-217">Un proveedor externo, como [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) o [Redis](https://redis.io/).</span><span class="sxs-lookup"><span data-stu-id="04975-217">An external provider, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) or [Redis](https://redis.io/).</span></span>

## <a name="persisting-keys-with-redis"></a><span data-ttu-id="04975-218">Persistencia de claves con Redis</span><span class="sxs-lookup"><span data-stu-id="04975-218">Persisting keys with Redis</span></span>

<span data-ttu-id="04975-219">Solo se deben usar las versiones de Redis compatibles con la [persistencia de datos de Redis](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) para almacenar claves.</span><span class="sxs-lookup"><span data-stu-id="04975-219">Only Redis versions supporting [Redis Data Persistence](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) should be used to store keys.</span></span> <span data-ttu-id="04975-220">[Azure BLOB Storage](/azure/storage/blobs/storage-blobs-introduction) es persistente y se puede usar para almacenar claves.</span><span class="sxs-lookup"><span data-stu-id="04975-220">[Azure Blob storage](/azure/storage/blobs/storage-blobs-introduction) is persistent and can be used to store keys.</span></span> <span data-ttu-id="04975-221">Para más información, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore/issues/13476).</span><span class="sxs-lookup"><span data-stu-id="04975-221">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/13476).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="04975-222">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="04975-222">Additional resources</span></span>

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
