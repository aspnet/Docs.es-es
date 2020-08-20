---
title: Extensibilidad de la administración de claves en ASP.NET Core
author: rick-anderson
description: Más información sobre la extensibilidad de la administración de claves de protección de datos ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
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
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 797df457a5584233043210e9ba2657b7fd7f3893
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631009"
---
# <a name="key-management-extensibility-in-aspnet-core"></a>Extensibilidad de la administración de claves en ASP.NET Core

> [!TIP]
> Lea la sección [Administración de claves](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) antes de leer esta sección, ya que se explican algunos de los conceptos fundamentales que hay detrás de estas API.

> [!WARNING]
> Los tipos que implementan cualquiera de las interfaces siguientes deben ser seguros para subprocesos para varios llamadores.

## <a name="key"></a>Clave

La `IKey` interfaz es la representación básica de una clave en Cryptosystem. La clave term se usa aquí en el sentido abstracto, no en el sentido literal de "material de clave criptográfica". Una clave tiene las siguientes propiedades:

* Fechas de activación, creación y expiración

* Estado de revocación

* Identificador de clave (un GUID)

::: moniker range=">= aspnetcore-2.0"

Además, `IKey` expone un `CreateEncryptor` método que se puede utilizar para crear una instancia de [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) asociada a esta clave.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Además, `IKey` expone un `CreateEncryptorInstance` método que se puede utilizar para crear una instancia de [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) asociada a esta clave.

::: moniker-end

> [!NOTE]
> No hay ninguna API para recuperar el material criptográfico sin formato de una `IKey` instancia de.

## <a name="ikeymanager"></a>IKeyManager

La `IKeyManager` interfaz representa un objeto responsable del almacenamiento, la recuperación y la manipulación de claves generales. Expone tres operaciones de alto nivel:

* Cree una nueva clave y arrástrela al almacenamiento.

* Obtiene todas las claves del almacenamiento.

* Revocar una o más claves y conservar la información de revocación en el almacenamiento.

>[!WARNING]
> Escribir un `IKeyManager` es una tarea muy avanzada y la mayoría de los desarrolladores no deben intentar hacerlo. En su lugar, la mayoría de los desarrolladores deben aprovechar las ventajas de las instalaciones que ofrece la clase [XmlKeyManager](#xmlkeymanager) .

## <a name="xmlkeymanager"></a>XmlKeyManager

El `XmlKeyManager` tipo es la implementación concreta de `IKeyManager` . Proporciona varios recursos útiles, como el custodia de claves y el cifrado de claves en reposo. Las claves de este sistema se representan como elementos XML (específicamente, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).

`XmlKeyManager` depende de otros componentes en el transcurso de la realización de sus tareas:

::: moniker range=">= aspnetcore-2.0"

* `AlgorithmConfiguration`, que dicta los algoritmos usados por las nuevas claves.

* `IXmlRepository`, que controla dónde se conservan las claves en el almacenamiento.

* `IXmlEncryptor` [Optional], que permite cifrar las claves en reposo.

* `IKeyEscrowSink` [opcional], que proporciona servicios de custodia de claves.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* `IXmlRepository`, que controla dónde se conservan las claves en el almacenamiento.

* `IXmlEncryptor` [Optional], que permite cifrar las claves en reposo.

* `IKeyEscrowSink` [opcional], que proporciona servicios de custodia de claves.

::: moniker-end

A continuación se muestran diagramas de alto nivel que indican el modo en que estos componentes están conectados entre sí `XmlKeyManager` .

::: moniker range=">= aspnetcore-2.0"

![Creación de claves](key-management/_static/keycreation2.png)

*Creación de claves/CreateNewKey*

En la implementación de `CreateNewKey` , el `AlgorithmConfiguration` componente se usa para crear un único `IAuthenticatedEncryptorDescriptor` , que a continuación se serializa como XML. Si hay un receptor de custodia de claves, se proporciona el XML sin formato (sin cifrar) al receptor para el almacenamiento a largo plazo. A continuación, se ejecuta el XML sin cifrar a través `IXmlEncryptor` de un (si es necesario) para generar el documento XML cifrado. Este documento cifrado se conserva en el almacenamiento a largo plazo a través de `IXmlRepository` . (Si no `IXmlEncryptor` se configura ningún, el documento no cifrado se conserva en el `IXmlRepository` ).

![Recuperación de claves](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Creación de claves](key-management/_static/keycreation1.png)

*Creación de claves/CreateNewKey*

En la implementación de `CreateNewKey` , el `IAuthenticatedEncryptorConfiguration` componente se usa para crear un único `IAuthenticatedEncryptorDescriptor` , que a continuación se serializa como XML. Si hay un receptor de custodia de claves, se proporciona el XML sin formato (sin cifrar) al receptor para el almacenamiento a largo plazo. A continuación, se ejecuta el XML sin cifrar a través `IXmlEncryptor` de un (si es necesario) para generar el documento XML cifrado. Este documento cifrado se conserva en el almacenamiento a largo plazo a través de `IXmlRepository` . (Si no `IXmlEncryptor` se configura ningún, el documento no cifrado se conserva en el `IXmlRepository` ).

![Recuperación de claves](key-management/_static/keyretrieval1.png)

::: moniker-end

*Recuperación de clave/GetAllKeys*

En la implementación de `GetAllKeys` , los documentos XML que representan claves y revocación se leen del subyacente `IXmlRepository` . Si estos documentos están cifrados, el sistema los descifrará automáticamente. `XmlKeyManager` crea las `IAuthenticatedEncryptorDescriptorDeserializer` instancias adecuadas para deserializar los documentos en `IAuthenticatedEncryptorDescriptor` instancias de, que después se encapsulan en `IKey` instancias individuales. Esta colección de `IKey` instancias se devuelve al autor de la llamada.

Puede encontrar más información sobre los elementos XML concretos en el [documento formato de almacenamiento de claves](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).

## <a name="ixmlrepository"></a>IXmlRepository

La `IXmlRepository` interfaz representa un tipo que puede conservar XML y recuperar XML de una memoria auxiliar. Expone dos API:

* `GetAllElements` :`IReadOnlyCollection<XElement>`

* `StoreElement(XElement element, string friendlyName)`

Las implementaciones de `IXmlRepository` no necesitan analizar el código XML que pasa por ellos. Deben tratar los documentos XML como opacos y permitir que las capas más altas se preocupen de la generación y el análisis de los documentos.

Hay cuatro tipos concretos integrados que implementan `IXmlRepository` :

::: moniker range=">= aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

Consulte el [documento proveedores de almacenamiento de claves](xref:security/data-protection/implementation/key-storage-providers) para obtener más información.

El registro de un personalizado `IXmlRepository` es adecuado cuando se usa un almacén de copia de seguridad diferente (por ejemplo, Azure Table Storage).

Para cambiar el repositorio predeterminado en toda la aplicación, registre una `IXmlRepository` instancia personalizada:

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlRepository = new MyCustomXmlRepository());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlRepository>(new MyCustomXmlRepository());
```

::: moniker-end

## <a name="ixmlencryptor"></a>IXmlEncryptor

La `IXmlEncryptor` interfaz representa un tipo que puede cifrar un elemento XML de texto sin formato. Expone una única API:

* Encrypt (XElement plaintextElement): EncryptedXmlInfo

Si un serializado `IAuthenticatedEncryptorDescriptor` contiene elementos marcados como "requiere cifrado", `XmlKeyManager` ejecutará esos elementos mediante el `IXmlEncryptor` método de la configuración `Encrypt` y conservará el elemento cifrado en lugar del elemento de texto simple en `IXmlRepository` . La salida del `Encrypt` método es un `EncryptedXmlInfo` objeto. Este objeto es un contenedor que contiene el resultante cifrado `XElement` y el tipo que representa un `IXmlDecryptor` que se puede utilizar para descifrar el elemento correspondiente.

Hay cuatro tipos concretos integrados que implementan `IXmlEncryptor` :

* [CertificateXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [DpapiNGXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [DpapiXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [NullXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

Para obtener más información, consulte el [documento de cifrado de claves en reposo](xref:security/data-protection/implementation/key-encryption-at-rest) .

Para cambiar el mecanismo predeterminado de cifrado en reposo de la aplicación, registre una `IXmlEncryptor` instancia personalizada:

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlEncryptor = new MyCustomXmlEncryptor());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlEncryptor>(new MyCustomXmlEncryptor());
```

::: moniker-end

## <a name="ixmldecryptor"></a>IXmlDecryptor

La `IXmlDecryptor` interfaz representa un tipo que sabe cómo descifrar un `XElement` que se ha cifrado mediante `IXmlEncryptor` . Expone una única API:

* Descifrado (XElement encryptedElement): XElement

El `Decrypt` método deshace el cifrado realizado por `IXmlEncryptor.Encrypt` . Por lo general, cada implementación concreta tendrá `IXmlEncryptor` una implementación concreta correspondiente `IXmlDecryptor` .

Los tipos que implementan `IXmlDecryptor` deben tener uno de los dos constructores públicos siguientes:

* . ctor (IServiceProvider)
* . ctor ()

> [!NOTE]
> El argumento `IServiceProvider` pasado al constructor puede ser null.

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

La `IKeyEscrowSink` interfaz representa un tipo que puede realizar custodia de información confidencial. Recuerde que los descriptores serializados pueden contener información confidencial (por ejemplo, material criptográfico) y esto es lo que condujo a la introducción del tipo [IXmlEncryptor](#ixmlencryptor) en primer lugar. Sin embargo, se producen accidentes y los anillos clave se pueden eliminar o dañar.

La interfaz de custodia proporciona un sombreado de escape de emergencia, lo que permite el acceso al XML serializado sin formato antes de que lo transforme cualquier [IXmlEncryptor](#ixmlencryptor)configurada. La interfaz expone una única API:

* Almacén (GUID de GUID, elemento XElement)

Depende de la `IKeyEscrowSink` implementación controlar el elemento proporcionado de forma segura coherente con la Directiva empresarial. Una posible implementación podría ser que el receptor de custodia Cifre el elemento XML con un certificado X. 509 corporativo conocido en el que se haya custodiado la clave privada del certificado; el `CertificateXmlEncryptor` tipo puede ayudar con esto. La `IKeyEscrowSink` implementación de también es responsable de conservar el elemento proporcionado de manera adecuada.

De forma predeterminada, no hay ningún mecanismo de custodia habilitado, aunque los administradores del servidor pueden [configurar esto globalmente](xref:security/data-protection/configuration/machine-wide-policy). También se puede configurar mediante programación a través del `IDataProtectionBuilder.AddKeyEscrowSink` método como se muestra en el ejemplo siguiente. El `AddKeyEscrowSink` método sobrecarga las `IServiceCollection.AddSingleton` `IServiceCollection.AddInstance` sobrecargas y, ya que `IKeyEscrowSink` las instancias están diseñadas para ser singleton. Si `IKeyEscrowSink` se registran varias instancias, se llamará a cada una de ellas durante la generación de claves, por lo que las claves se pueden enviar por custodia a varios mecanismos simultáneamente.

No hay ninguna API para leer material de una `IKeyEscrowSink` instancia de. Esto es coherente con la teoría de diseño del mecanismo de custodia: está diseñado para que el material clave sea accesible a una entidad de confianza y, puesto que la aplicación no es una entidad de confianza, no debe tener acceso a su propio material con custodia.

En el código de ejemplo siguiente se muestra cómo crear y registrar una `IKeyEscrowSink` clave WHERE con custodia, de modo que solo los miembros de "CONTOSODomain Admins" puedan recuperarlas.

> [!NOTE]
> Para ejecutar este ejemplo, debe estar en un equipo con Windows 8/Windows Server 2012 unido a un dominio y el controlador de dominio debe ser Windows Server 2012 o posterior.

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
