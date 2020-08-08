---
title: Configurar la protección de datos en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo configurar la protección de datos en ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
no-loc:
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
ms.openlocfilehash: 0ff211624b7cf363da393a627c761302d9f3d8ed
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019760"
---
# <a name="configure-aspnet-core-data-protection"></a>Configurar la protección de datos en ASP.NET Core

Cuando se inicializa el sistema de protección de datos, aplica la [configuración predeterminada](xref:security/data-protection/configuration/default-settings) en función del entorno operativo. Esta configuración suele ser adecuada para las aplicaciones que se ejecutan en un solo equipo. Hay casos en los que un desarrollador puede querer cambiar la configuración predeterminada:

* La aplicación se distribuye entre varias máquinas.
* Por motivos de cumplimiento.

En estos casos, el sistema de protección de datos ofrece una API de configuración enriquecida.

> [!WARNING]
> Al igual que los archivos de configuración, el anillo de claves de protección de datos debe protegerse con los permisos adecuados. Puede optar por cifrar las claves en reposo, pero esto no impide que los atacantes creen nuevas claves. Por lo tanto, la seguridad de la aplicación se ve afectada. La ubicación de almacenamiento configurada con la protección de datos debe tener su acceso limitado a la propia aplicación, de manera similar a la forma en que se protegen los archivos de configuración. Por ejemplo, si decide almacenar el anillo de claves en el disco, use los permisos del sistema de archivos. Asegúrese de que solo la identidad en la que se ejecuta la aplicación web tenga acceso de lectura, escritura y creación a ese directorio. Si usa Azure Blob Storage, solo la aplicación Web debe tener la capacidad de leer, escribir o crear nuevas entradas en el almacén de blobs, etc.
>
> El método de extensión [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) devuelve un [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder). `IDataProtectionBuilder`Expone métodos de extensión que se pueden encadenar juntos para configurar las opciones de protección de datos.

::: moniker range=">= aspnetcore-3.0"

Los siguientes paquetes NuGet son necesarios para las extensiones de protección de datos que se usan en este artículo:

* [Microsoft. AspNetCore. AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/)
* [Microsoft. AspNetCore. AzureKeyVault](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault/)

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a>ProtectKeysWithAzureKeyVault

Para almacenar claves en [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure el sistema con [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) en la `Startup` clase:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

Establezca la ubicación de almacenamiento del anillo de claves (por ejemplo, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)). Se debe establecer la ubicación porque la llamada a `ProtectKeysWithAzureKeyVault` implementa un [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) que deshabilita la configuración automática de la protección de datos, incluida la ubicación de almacenamiento del anillo de claves. En el ejemplo anterior se usa Azure Blob Storage para conservar el anillo de claves. Para obtener más información, consulte [proveedores de almacenamiento de claves: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage). También puede conservar el anillo de claves localmente con [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).

`keyIdentifier`Es el identificador de clave del almacén de claves que se usa para el cifrado de claves. Por ejemplo, una clave creada en el almacén de claves denominado `dataprotection` en `contosokeyvault` tiene el identificador de clave `https://contosokeyvault.vault.azure.net/keys/dataprotection/` . Proporcione la aplicación con los permisos clave de **desencapsulado** y **clave de encapsulado** en el almacén de claves.

`ProtectKeysWithAzureKeyVault`sobrecargas

* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) permite el uso de una [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) para permitir que el sistema de protección de datos use el almacén de claves.
* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) permite el uso de `ClientId` y [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) para permitir que el sistema de protección de datos use el almacén de claves.
* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) permite el uso de `ClientId` y `ClientSecret` para habilitar el sistema de protección de datos para que use el almacén de claves.

Cuando se usa una combinación de keyvault y Azure Storage para almacenar y proteger claves, se `System.UriFormatException` producirá una excepción si el BLOB en el que se almacenan las claves no existe todavía. Esto se puede crear manualmente antes de ejecutar la aplicación o `.ProtectKeysWithAzureKeyVault()` se puede quitar para la primera ejecución para crear el BLOB en su lugar y, después, agregarlo a las ejecuciones posteriores. `.ProtectKeysWithAzureKeyVault()`Se recomienda quitar, ya que esto garantizará que el archivo se crea con el esquema y los valores adecuados.

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

## <a name="persistkeystofilesystem"></a>PersistKeysToFileSystem

Para almacenar claves en un recurso compartido UNC en lugar de en la ubicación predeterminada *% LOCALAPPDATA%* , configure el sistema con [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> Si cambia la ubicación de persistencia de claves, el sistema ya no cifrará automáticamente las claves en reposo, ya que no sabrá si DPAPI es un mecanismo de cifrado adecuado.

## <a name="protectkeyswith"></a>ProtectKeysWith\*

Puede configurar el sistema para proteger las claves en reposo mediante una llamada a cualquiera de las API de configuración de [ProtectKeysWith \* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) . Considere el ejemplo siguiente, que almacena las claves en un recurso compartido UNC y las cifra en reposo con un certificado X. 509 específico:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

En ASP.NET Core 2,1 o posterior, puede proporcionar un [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) a [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), como un certificado cargado desde un archivo:

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

Consulte [cifrado de claves en reposo](xref:security/data-protection/implementation/key-encryption-at-rest) para obtener más ejemplos y debate sobre los mecanismos de cifrado de claves integrados.

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a>UnprotectKeysWithAnyCertificate

En ASP.NET Core 2,1 o posterior, puede rotar los certificados y descifrar las claves en reposo mediante una matriz de certificados [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) con [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):

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

## <a name="setdefaultkeylifetime"></a>SetDefaultKeyLifetime

Para configurar el sistema de forma que use una vigencia de clave de 14 días en lugar del valor predeterminado de 90 días, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a>SetApplicationName

De forma predeterminada, el sistema de protección de datos aísla las aplicaciones de otras en función de sus rutas de acceso [raíz de contenido](xref:fundamentals/index#content-root) , incluso si están compartiendo el mismo repositorio de claves físicas. Esto evita que las aplicaciones conozcan las cargas protegidas de los demás.

Para compartir cargas protegidas entre aplicaciones:

* Configure <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> en cada aplicación con el mismo valor.
* Use la misma versión de la pila de API de protección de datos en las aplicaciones. Realice **una** de las siguientes acciones en los archivos de proyecto de las aplicaciones:
  * Haga referencia a la misma versión de .NET Framework compartida a través del [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).
  * Haga referencia a la misma versión del [paquete de protección de datos](xref:security/data-protection/introduction#package-layout) .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a>DisableAutomaticKeyGeneration

Puede tener un escenario en el que no quiera que una aplicación revierta automáticamente las claves (crear nuevas claves) a medida que se acerquen a la expiración. Un ejemplo de esto puede ser que las aplicaciones estén configuradas en una relación de principal o secundaria, donde solo la aplicación principal es responsable de los problemas de administración de claves y las aplicaciones secundarias simplemente tienen una vista de solo lectura del anillo de claves. Las aplicaciones secundarias se pueden configurar para tratar el anillo de claves como de solo lectura configurando el sistema con <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*> :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a>Aislamiento por aplicación

Cuando un host de ASP.NET Core proporciona el sistema de protección de datos, aísla automáticamente las aplicaciones entre sí, aunque dichas aplicaciones se ejecuten en la misma cuenta de proceso de trabajo y usen el mismo material de creación de claves maestro. Esto es algo similar al modificador IsolateApps del elemento System. Web `<machineKey>` .

El mecanismo de aislamiento funciona considerando cada aplicación en el equipo local como un inquilino único, por lo que la <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> raíz de cualquier aplicación determinada incluye automáticamente el identificador de la aplicación como discriminador. El identificador único de la aplicación es la ruta de acceso física de la aplicación:

* En el caso de las aplicaciones hospedadas en IIS, el identificador único es la ruta de acceso física de IIS de la aplicación. Si una aplicación se implementa en un entorno de granja de servidores Web, este valor es estable suponiendo que los entornos de IIS estén configurados de forma similar en todos los equipos de la granja de servidores Web.
* En el caso de las aplicaciones autohospedadas que se ejecutan en el [servidor de Kestrel](xref:fundamentals/servers/index#kestrel), el identificador único es la ruta de acceso física a la aplicación en el disco.

El identificador único está diseñado para sobrevivir a los restablecimientos &mdash; de la aplicación individual y del equipo.

Este mecanismo de aislamiento presupone que las aplicaciones no son malintencionadas. Una aplicación malintencionada siempre puede afectar A cualquier otra aplicación que se ejecute en la misma cuenta de proceso de trabajo. En un entorno de hospedaje compartido en el que las aplicaciones no son de confianza, el proveedor de hospedaje debe tomar medidas para garantizar el aislamiento de nivel de sistema operativo entre las aplicaciones, incluida la separación de los repositorios de claves subyacentes de las aplicaciones.

Si un host de ASP.NET Core no proporciona el sistema de protección de datos (por ejemplo, si crea una instancia del mismo mediante el `DataProtectionProvider` tipo concreto) el aislamiento de la aplicación está deshabilitado de forma predeterminada. Cuando el aislamiento de la aplicación está deshabilitado, todas las aplicaciones respaldadas por el mismo material de creación de claves pueden compartir cargas, siempre y cuando proporcionen los [propósitos](xref:security/data-protection/consumer-apis/purpose-strings)adecuados. Para proporcionar aislamiento de la aplicación en este entorno, llame al método [SetApplicationName](#setapplicationname) en el objeto de configuración y proporcione un nombre único para cada aplicación.

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a>Cambiar algoritmos con UseCryptographicAlgorithms

La pila de protección de datos permite cambiar el algoritmo predeterminado que usan las claves generadas recientemente. La manera más sencilla de hacerlo es llamar a [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) desde la devolución de llamada de configuración:

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

El valor predeterminado de EncryptionAlgorithm es AES-256-CBC y el valor predeterminado de ValidationAlgorithm es HMACSHA256. La directiva predeterminada se puede establecer mediante un administrador del sistema a través de una [Directiva de todo el equipo](xref:security/data-protection/configuration/machine-wide-policy), pero una llamada explícita a `UseCryptographicAlgorithms` invalida la directiva predeterminada.

`UseCryptographicAlgorithms`La llamada le permite especificar el algoritmo deseado a partir de una lista integrada predefinida. No es necesario preocuparse por la implementación del algoritmo. En el escenario anterior, el sistema de protección de datos intenta usar la implementación de CNG de AES si se ejecuta en Windows. De lo contrario, recurre a la clase administrada [System. Security. Cryptography. AES](/dotnet/api/system.security.cryptography.aes) .

Puede especificar manualmente una implementación a través de una llamada a [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).

> [!TIP]
> El cambio de algoritmos no afecta a las claves existentes en el anillo de claves. Solo afecta a las claves generadas recientemente.

### <a name="specifying-custom-managed-algorithms"></a>Especificar algoritmos administrados personalizados

::: moniker range=">= aspnetcore-2.0"

Para especificar algoritmos administrados personalizados, cree una instancia de [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) que apunte a los tipos de implementación:

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

Para especificar algoritmos administrados personalizados, cree una instancia de [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) que apunte a los tipos de implementación:

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

Por lo general, las \* propiedades de tipo deben apuntar a implementaciones concretas y en instancias (a través de un constructor sin parámetros público) de [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) y [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), aunque el sistema especial tiene algunos valores como `typeof(Aes)` para mayor comodidad.

> [!NOTE]
> La SymmetricAlgorithm debe tener una longitud de clave de ≥ 128 bits y un tamaño de bloque de ≥ 64 bits, y debe admitir el cifrado de modo CBC con el relleno de #7 de PKCS. El KeyedHashAlgorithm debe tener un tamaño de síntesis de >= 128 bits y debe admitir claves de longitud igual a la longitud de síntesis del algoritmo hash. No es estrictamente necesario que el KeyedHashAlgorithm sea HMAC.

### <a name="specifying-custom-windows-cng-algorithms"></a>Especificar algoritmos CNG de Windows personalizados

::: moniker range=">= aspnetcore-2.0"

Para especificar un algoritmo CNG de Windows personalizado mediante el cifrado de modo CBC con validación HMAC, cree una instancia de [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) que contenga la información algorítmica:

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

Para especificar un algoritmo CNG de Windows personalizado mediante el cifrado de modo CBC con validación HMAC, cree una instancia de [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) que contenga la información algorítmica:

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
> El algoritmo de cifrado de bloques simétricos debe tener una longitud de clave de >= 128 bits, un tamaño de bloque de >= 64 bits y debe admitir el cifrado de modo CBC con el relleno de #7 PKCS. El algoritmo hash debe tener un tamaño de síntesis de >= 128 bits y debe admitir que se abra con \_ la \_ \_ marca de marca HMAC del identificador de ALG de BCRYPT \_ . Las \* propiedades del proveedor se pueden establecer en null para usar el proveedor predeterminado para el algoritmo especificado. Consulte la documentación de [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) para obtener más información.

::: moniker range=">= aspnetcore-2.0"

Para especificar un algoritmo CNG de Windows personalizado mediante el cifrado de modo de Galois/contador con validación, cree una instancia de [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) que contenga la información algorítmica:

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

Para especificar un algoritmo CNG de Windows personalizado mediante el cifrado de modo de Galois/contador con validación, cree una instancia de [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) que contenga la información algorítmica:

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
> El algoritmo de cifrado del bloque simétrico debe tener una longitud de clave de >= 128 bits, un tamaño de bloque de exactamente 128 bits y debe admitir el cifrado de GCM. Puede establecer la propiedad [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) en null para usar el proveedor predeterminado para el algoritmo especificado. Consulte la documentación de [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) para obtener más información.

### <a name="specifying-other-custom-algorithms"></a>Especificar otros algoritmos personalizados

Aunque no se expone como una API de primera clase, el sistema de protección de datos es lo suficientemente extensible como para permitir especificar casi cualquier tipo de algoritmo. Por ejemplo, es posible mantener todas las claves contenidas en un módulo de seguridad de hardware (HSM) y proporcionar una implementación personalizada de las rutinas de cifrado y descifrado principales. Vea [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) en [extensibilidad de criptografía básica](xref:security/data-protection/extensibility/core-crypto) para obtener más información.

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a>Persistencia de claves al hospedar en un contenedor de Docker

Al hospedar en un contenedor de [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) , las claves deben mantenerse en:

* Una carpeta que es un volumen de Docker que se mantiene más allá de la duración del contenedor, como un volumen compartido o un volumen montado en el host.
* Un proveedor externo, como [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) o [Redis](https://redis.io/).

## <a name="persisting-keys-with-redis"></a>Persistencia de claves con Redis

Solo se deben usar las versiones de Redis compatibles con la [persistencia de datos de Redis](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) para almacenar claves. [Azure BLOB Storage](/azure/storage/blobs/storage-blobs-introduction) es persistente y se puede usar para almacenar claves. Para más información, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore/issues/13476).

## <a name="additional-resources"></a>Recursos adicionales

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
