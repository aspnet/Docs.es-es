---
title: Cifrado de claves en reposo en Windows y Azure con ASP.NET Core
author: rick-anderson
description: Obtenga información sobre la implementación de ASP.NET Core el cifrado de claves de protección de datos en reposo.
ms.author: riande
ms.date: 07/16/2018
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
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: e2841278e8262e6dbbfcf172e0b6599bb19f1d6c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061280"
---
# <a name="key-encryption-at-rest-in-windows-and-azure-using-aspnet-core"></a>Cifrado de claves en reposo en Windows y Azure con ASP.NET Core

De forma predeterminada, el sistema de protección de datos [emplea un mecanismo de detección](xref:security/data-protection/configuration/default-settings) para determinar cómo se deben cifrar las claves criptográficas en reposo. El desarrollador puede invalidar el mecanismo de detección y especificar manualmente cómo se deben cifrar las claves en reposo.

> [!WARNING]
> Si especifica una ubicación de [persistencia de claves](xref:security/data-protection/implementation/key-storage-providers)explícita, el sistema de protección de datos anula el registro del cifrado de claves predeterminado en el mecanismo REST. Por consiguiente, las claves ya no se cifran en reposo. Se recomienda [especificar un mecanismo de cifrado de claves explícito](xref:security/data-protection/implementation/key-encryption-at-rest) para las implementaciones de producción. Las opciones de mecanismo de cifrado en reposo se describen en este tema.

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a>Azure Key Vault

Para almacenar claves en [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure el sistema con [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) en la `Startup` clase:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

Para obtener más información, consulte [configuración de ASP.net Core protección de datos: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).

::: moniker-end

## <a name="windows-dpapi"></a>DPAPI de Windows

**Solo se aplica a las implementaciones de Windows.**

Cuando se usa la DPAPI de Windows, el material de clave se cifra con [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) antes de guardarse en el almacenamiento. DPAPI es un mecanismo de cifrado adecuado para los datos que nunca se leen fuera del equipo actual (aunque es posible hacer una copia de seguridad de estas claves hasta Active Directory; vea [perfiles de DPAPI y itinerancia](https://support.microsoft.com/kb/309408/#6)). Para configurar el cifrado de clave en reposo de DPAPI, llame a uno de los métodos de extensión [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

Si `ProtectKeysWithDpapi` se llama a sin parámetros, solo la cuenta de usuario de Windows actual puede descifrar el anillo de claves guardado. Opcionalmente, puede especificar que cualquier cuenta de usuario en el equipo (no solo la cuenta de usuario actual) pueda descifrar el anillo de claves:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a>Certificado X.509

Si la aplicación se distribuye entre varias máquinas, puede ser conveniente distribuir un certificado X. 509 compartido entre los equipos y configurar las aplicaciones hospedadas para que usen el certificado para el cifrado de claves en reposo:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

Debido a las limitaciones de .NET Framework, solo se admiten certificados con claves privadas de CAPI. Vea el contenido siguiente para ver posibles soluciones alternativas a estas limitaciones.

::: moniker-end

## <a name="windows-dpapi-ng"></a>Windows DPAPI-NG

**Este mecanismo solo está disponible en Windows 8/Windows Server 2012 o posterior.**

A partir de Windows 8, el sistema operativo Windows admite DPAPI-NG (también conocido como DPAPI CNG). Para obtener más información, vea [acerca de la DPAPI de CNG](/windows/desktop/SecCNG/cng-dpapi).

La entidad de seguridad se codifica como una regla de descriptor de protección. En el ejemplo siguiente que llama a [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), solo el usuario unido al dominio con el SID especificado puede descifrar el anillo de claves:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

También hay una sobrecarga sin parámetros de `ProtectKeysWithDpapiNG` . Use este método para especificar la regla "SID = {CURRENT_ACCOUNT_SID}", donde *CURRENT_ACCOUNT_SID* es el SID de la cuenta de usuario de Windows actual:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

En este escenario, el controlador de dominio de AD es responsable de distribuir las claves de cifrado usadas por las operaciones de DPAPI-GN. El usuario de destino puede descifrar la carga cifrada desde cualquier máquina unida a un dominio (siempre que el proceso se ejecute bajo su identidad).

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a>Cifrado basado en certificados con Windows DPAPI-NG

Si la aplicación se ejecuta en Windows 8.1/Windows Server 2012 R2 o posterior, puede usar Windows DPAPI-GN para realizar el cifrado basado en certificados. Use la cadena de descriptor de regla "CERTIFICAte = HashId: THUMBPRINT", donde *huella digital* es la huella digital SHA1 con codificación hexadecimal del certificado:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

Cualquier aplicación indicada en este repositorio debe ejecutarse en Windows 8.1/Windows Server 2012 R2 o posterior para descifrar las claves.

## <a name="custom-key-encryption"></a>Cifrado de claves personalizadas

Si los mecanismos integrados no son adecuados, el desarrollador puede especificar su propio mecanismo de cifrado de claves proporcionando un [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)personalizado.
