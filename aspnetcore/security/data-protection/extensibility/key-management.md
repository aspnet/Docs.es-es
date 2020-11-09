---
title: Extensibilidad de la administración de claves en ASP.NET Core
author: rick-anderson
description: Más información sobre la extensibilidad de la administración de claves de protección de datos ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 08ec018238d18cd253e793267c5b146df8319485
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060773"
---
# <a name="key-management-extensibility-in-aspnet-core"></a><span data-ttu-id="1bdce-103">Extensibilidad de la administración de claves en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1bdce-103">Key management extensibility in ASP.NET Core</span></span>

<span data-ttu-id="1bdce-104">Lea la sección [Administración de claves](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) antes de leer esta sección, ya que se explican algunos de los conceptos fundamentales que hay detrás de estas API.</span><span class="sxs-lookup"><span data-stu-id="1bdce-104">Read the [key management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

<span data-ttu-id="1bdce-105">**ADVERTENCIA** : los tipos que implementan cualquiera de las siguientes interfaces deben ser seguros para subprocesos para varios llamadores.</span><span class="sxs-lookup"><span data-stu-id="1bdce-105">**Warning** : Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="1bdce-106">Clave</span><span class="sxs-lookup"><span data-stu-id="1bdce-106">Key</span></span>

<span data-ttu-id="1bdce-107">La `IKey` interfaz es la representación básica de una clave en Cryptosystem.</span><span class="sxs-lookup"><span data-stu-id="1bdce-107">The `IKey` interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="1bdce-108">La clave term se usa aquí en el sentido abstracto, no en el sentido literal de "material de clave criptográfica".</span><span class="sxs-lookup"><span data-stu-id="1bdce-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="1bdce-109">Una clave tiene las siguientes propiedades:</span><span class="sxs-lookup"><span data-stu-id="1bdce-109">A key has the following properties:</span></span>

* <span data-ttu-id="1bdce-110">Fechas de activación, creación y expiración</span><span class="sxs-lookup"><span data-stu-id="1bdce-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="1bdce-111">Estado de revocación</span><span class="sxs-lookup"><span data-stu-id="1bdce-111">Revocation status</span></span>

* <span data-ttu-id="1bdce-112">Identificador de clave (un GUID)</span><span class="sxs-lookup"><span data-stu-id="1bdce-112">Key identifier (a GUID)</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="1bdce-113">Además, `IKey` expone un `CreateEncryptor` método que se puede utilizar para crear una instancia de [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) asociada a esta clave.</span><span class="sxs-lookup"><span data-stu-id="1bdce-113">Additionally, `IKey` exposes a `CreateEncryptor` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="1bdce-114">Además, `IKey` expone un `CreateEncryptorInstance` método que se puede utilizar para crear una instancia de [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) asociada a esta clave.</span><span class="sxs-lookup"><span data-stu-id="1bdce-114">Additionally, `IKey` exposes a `CreateEncryptorInstance` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="1bdce-115">No hay ninguna API para recuperar el material criptográfico sin formato de una `IKey` instancia de.</span><span class="sxs-lookup"><span data-stu-id="1bdce-115">There's no API to retrieve the raw cryptographic material from an `IKey` instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="1bdce-116">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="1bdce-116">IKeyManager</span></span>

<span data-ttu-id="1bdce-117">La `IKeyManager` interfaz representa un objeto responsable del almacenamiento, la recuperación y la manipulación de claves generales.</span><span class="sxs-lookup"><span data-stu-id="1bdce-117">The `IKeyManager` interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="1bdce-118">Expone tres operaciones de alto nivel:</span><span class="sxs-lookup"><span data-stu-id="1bdce-118">It exposes three high-level operations:</span></span>

* <span data-ttu-id="1bdce-119">Cree una nueva clave y arrástrela al almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="1bdce-119">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="1bdce-120">Obtiene todas las claves del almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="1bdce-120">Get all keys from storage.</span></span>

* <span data-ttu-id="1bdce-121">Revocar una o más claves y conservar la información de revocación en el almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="1bdce-121">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="1bdce-122">Escribir un `IKeyManager` es una tarea muy avanzada y la mayoría de los desarrolladores no deben intentar hacerlo.</span><span class="sxs-lookup"><span data-stu-id="1bdce-122">Writing an `IKeyManager` is a very advanced task, and the majority of developers shouldn't attempt it.</span></span> <span data-ttu-id="1bdce-123">En su lugar, la mayoría de los desarrolladores deben aprovechar las ventajas de las instalaciones que ofrece la clase [XmlKeyManager](#xmlkeymanager) .</span><span class="sxs-lookup"><span data-stu-id="1bdce-123">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](#xmlkeymanager) class.</span></span>

## <a name="xmlkeymanager"></a><span data-ttu-id="1bdce-124">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="1bdce-124">XmlKeyManager</span></span>

<span data-ttu-id="1bdce-125">El `XmlKeyManager` tipo es la implementación concreta de `IKeyManager` .</span><span class="sxs-lookup"><span data-stu-id="1bdce-125">The `XmlKeyManager` type is the in-box concrete implementation of `IKeyManager`.</span></span> <span data-ttu-id="1bdce-126">Proporciona varios recursos útiles, como el custodia de claves y el cifrado de claves en reposo.</span><span class="sxs-lookup"><span data-stu-id="1bdce-126">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="1bdce-127">Las claves de este sistema se representan como elementos XML (específicamente, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span><span class="sxs-lookup"><span data-stu-id="1bdce-127">Keys in this system are represented as XML elements (specifically, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span></span>

<span data-ttu-id="1bdce-128">`XmlKeyManager` depende de otros componentes en el transcurso de la realización de sus tareas:</span><span class="sxs-lookup"><span data-stu-id="1bdce-128">`XmlKeyManager` depends on several other components in the course of fulfilling its tasks:</span></span>

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="1bdce-129">`AlgorithmConfiguration`, que dicta los algoritmos usados por las nuevas claves.</span><span class="sxs-lookup"><span data-stu-id="1bdce-129">`AlgorithmConfiguration`, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="1bdce-130">`IXmlRepository`, que controla dónde se conservan las claves en el almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="1bdce-130">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="1bdce-131">`IXmlEncryptor` [Optional], que permite cifrar las claves en reposo.</span><span class="sxs-lookup"><span data-stu-id="1bdce-131">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="1bdce-132">`IKeyEscrowSink` [opcional], que proporciona servicios de custodia de claves.</span><span class="sxs-lookup"><span data-stu-id="1bdce-132">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="1bdce-133">`IXmlRepository`, que controla dónde se conservan las claves en el almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="1bdce-133">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="1bdce-134">`IXmlEncryptor` [Optional], que permite cifrar las claves en reposo.</span><span class="sxs-lookup"><span data-stu-id="1bdce-134">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="1bdce-135">`IKeyEscrowSink` [opcional], que proporciona servicios de custodia de claves.</span><span class="sxs-lookup"><span data-stu-id="1bdce-135">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

<span data-ttu-id="1bdce-136">A continuación se muestran diagramas de alto nivel que indican el modo en que estos componentes están conectados entre sí `XmlKeyManager` .</span><span class="sxs-lookup"><span data-stu-id="1bdce-136">Below are high-level diagrams which indicate how these components are wired together within `XmlKeyManager`.</span></span>

::: moniker range=">= aspnetcore-2.0"

![Creación de claves](key-management/_static/keycreation2.png)

<span data-ttu-id="1bdce-138">*Creación de claves/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="1bdce-138">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="1bdce-139">En la implementación de `CreateNewKey` , el `AlgorithmConfiguration` componente se usa para crear un único `IAuthenticatedEncryptorDescriptor` , que a continuación se serializa como XML.</span><span class="sxs-lookup"><span data-stu-id="1bdce-139">In the implementation of `CreateNewKey`, the `AlgorithmConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="1bdce-140">Si hay un receptor de custodia de claves, se proporciona el XML sin formato (sin cifrar) al receptor para el almacenamiento a largo plazo.</span><span class="sxs-lookup"><span data-stu-id="1bdce-140">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="1bdce-141">A continuación, se ejecuta el XML sin cifrar a través `IXmlEncryptor` de un (si es necesario) para generar el documento XML cifrado.</span><span class="sxs-lookup"><span data-stu-id="1bdce-141">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="1bdce-142">Este documento cifrado se conserva en el almacenamiento a largo plazo a través de `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="1bdce-142">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="1bdce-143">(Si no `IXmlEncryptor` se configura ningún, el documento no cifrado se conserva en el `IXmlRepository` ).</span><span class="sxs-lookup"><span data-stu-id="1bdce-143">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Recuperación de claves](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Creación de claves](key-management/_static/keycreation1.png)

<span data-ttu-id="1bdce-146">*Creación de claves/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="1bdce-146">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="1bdce-147">En la implementación de `CreateNewKey` , el `IAuthenticatedEncryptorConfiguration` componente se usa para crear un único `IAuthenticatedEncryptorDescriptor` , que a continuación se serializa como XML.</span><span class="sxs-lookup"><span data-stu-id="1bdce-147">In the implementation of `CreateNewKey`, the `IAuthenticatedEncryptorConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="1bdce-148">Si hay un receptor de custodia de claves, se proporciona el XML sin formato (sin cifrar) al receptor para el almacenamiento a largo plazo.</span><span class="sxs-lookup"><span data-stu-id="1bdce-148">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="1bdce-149">A continuación, se ejecuta el XML sin cifrar a través `IXmlEncryptor` de un (si es necesario) para generar el documento XML cifrado.</span><span class="sxs-lookup"><span data-stu-id="1bdce-149">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="1bdce-150">Este documento cifrado se conserva en el almacenamiento a largo plazo a través de `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="1bdce-150">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="1bdce-151">(Si no `IXmlEncryptor` se configura ningún, el documento no cifrado se conserva en el `IXmlRepository` ).</span><span class="sxs-lookup"><span data-stu-id="1bdce-151">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Recuperación de claves](key-management/_static/keyretrieval1.png)

::: moniker-end

<span data-ttu-id="1bdce-153">*Recuperación de clave/GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="1bdce-153">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="1bdce-154">En la implementación de `GetAllKeys` , los documentos XML que representan claves y revocación se leen del subyacente `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="1bdce-154">In the implementation of `GetAllKeys`, the XML documents representing keys and revocations are read from the underlying `IXmlRepository`.</span></span> <span data-ttu-id="1bdce-155">Si estos documentos están cifrados, el sistema los descifrará automáticamente.</span><span class="sxs-lookup"><span data-stu-id="1bdce-155">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="1bdce-156">`XmlKeyManager` crea las `IAuthenticatedEncryptorDescriptorDeserializer` instancias adecuadas para deserializar los documentos en `IAuthenticatedEncryptorDescriptor` instancias de, que después se encapsulan en `IKey` instancias individuales.</span><span class="sxs-lookup"><span data-stu-id="1bdce-156">`XmlKeyManager` creates the appropriate `IAuthenticatedEncryptorDescriptorDeserializer` instances to deserialize the documents back into `IAuthenticatedEncryptorDescriptor` instances, which are then wrapped in individual `IKey` instances.</span></span> <span data-ttu-id="1bdce-157">Esta colección de `IKey` instancias se devuelve al autor de la llamada.</span><span class="sxs-lookup"><span data-stu-id="1bdce-157">This collection of `IKey` instances is returned to the caller.</span></span>

<span data-ttu-id="1bdce-158">Puede encontrar más información sobre los elementos XML concretos en el [documento formato de almacenamiento de claves](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span><span class="sxs-lookup"><span data-stu-id="1bdce-158">Further information on the particular XML elements can be found in the [key storage format document](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="1bdce-159">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="1bdce-159">IXmlRepository</span></span>

<span data-ttu-id="1bdce-160">La `IXmlRepository` interfaz representa un tipo que puede conservar XML y recuperar XML de una memoria auxiliar.</span><span class="sxs-lookup"><span data-stu-id="1bdce-160">The `IXmlRepository` interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="1bdce-161">Expone dos API:</span><span class="sxs-lookup"><span data-stu-id="1bdce-161">It exposes two APIs:</span></span>

* <span data-ttu-id="1bdce-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span><span class="sxs-lookup"><span data-stu-id="1bdce-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span></span>

* `StoreElement(XElement element, string friendlyName)`

<span data-ttu-id="1bdce-163">Las implementaciones de `IXmlRepository` no necesitan analizar el código XML que pasa por ellos.</span><span class="sxs-lookup"><span data-stu-id="1bdce-163">Implementations of `IXmlRepository` don't need to parse the XML passing through them.</span></span> <span data-ttu-id="1bdce-164">Deben tratar los documentos XML como opacos y permitir que las capas más altas se preocupen de la generación y el análisis de los documentos.</span><span class="sxs-lookup"><span data-stu-id="1bdce-164">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="1bdce-165">Hay cuatro tipos concretos integrados que implementan `IXmlRepository` :</span><span class="sxs-lookup"><span data-stu-id="1bdce-165">There are four built-in concrete types which implement `IXmlRepository`:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="1bdce-166">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="1bdce-166">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="1bdce-167">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="1bdce-167">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="1bdce-168">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="1bdce-168">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="1bdce-169">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="1bdce-169">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="1bdce-170">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="1bdce-170">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="1bdce-171">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="1bdce-171">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="1bdce-172">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="1bdce-172">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="1bdce-173">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="1bdce-173">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

<span data-ttu-id="1bdce-174">Consulte el [documento proveedores de almacenamiento de claves](xref:security/data-protection/implementation/key-storage-providers) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="1bdce-174">See the [key storage providers document](xref:security/data-protection/implementation/key-storage-providers) for more information.</span></span>

<span data-ttu-id="1bdce-175">El registro de un personalizado `IXmlRepository` es adecuado cuando se usa un almacén de copia de seguridad diferente (por ejemplo, Azure Table Storage).</span><span class="sxs-lookup"><span data-stu-id="1bdce-175">Registering a custom `IXmlRepository` is appropriate when using a different backing store (for example, Azure Table Storage).</span></span>

<span data-ttu-id="1bdce-176">Para cambiar el repositorio predeterminado en toda la aplicación, registre una `IXmlRepository` instancia personalizada:</span><span class="sxs-lookup"><span data-stu-id="1bdce-176">To change the default repository application-wide, register a custom `IXmlRepository` instance:</span></span>

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

## <a name="ixmlencryptor"></a><span data-ttu-id="1bdce-177">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="1bdce-177">IXmlEncryptor</span></span>

<span data-ttu-id="1bdce-178">La `IXmlEncryptor` interfaz representa un tipo que puede cifrar un elemento XML de texto sin formato.</span><span class="sxs-lookup"><span data-stu-id="1bdce-178">The `IXmlEncryptor` interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="1bdce-179">Expone una única API:</span><span class="sxs-lookup"><span data-stu-id="1bdce-179">It exposes a single API:</span></span>

* <span data-ttu-id="1bdce-180">Encrypt (XElement plaintextElement): EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="1bdce-180">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="1bdce-181">Si un serializado `IAuthenticatedEncryptorDescriptor` contiene elementos marcados como "requiere cifrado", `XmlKeyManager` ejecutará esos elementos mediante el `IXmlEncryptor` método de la configuración `Encrypt` y conservará el elemento cifrado en lugar del elemento de texto simple en `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="1bdce-181">If a serialized `IAuthenticatedEncryptorDescriptor` contains any elements marked as "requires encryption", then `XmlKeyManager` will run those elements through the configured `IXmlEncryptor`'s `Encrypt` method, and it will persist the enciphered element rather than the plaintext element to the `IXmlRepository`.</span></span> <span data-ttu-id="1bdce-182">La salida del `Encrypt` método es un `EncryptedXmlInfo` objeto.</span><span class="sxs-lookup"><span data-stu-id="1bdce-182">The output of the `Encrypt` method is an `EncryptedXmlInfo` object.</span></span> <span data-ttu-id="1bdce-183">Este objeto es un contenedor que contiene el resultante cifrado `XElement` y el tipo que representa un `IXmlDecryptor` que se puede utilizar para descifrar el elemento correspondiente.</span><span class="sxs-lookup"><span data-stu-id="1bdce-183">This object is a wrapper which contains both the resultant enciphered `XElement` and the Type which represents an `IXmlDecryptor` which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="1bdce-184">Hay cuatro tipos concretos integrados que implementan `IXmlEncryptor` :</span><span class="sxs-lookup"><span data-stu-id="1bdce-184">There are four built-in concrete types which implement `IXmlEncryptor`:</span></span>

* [<span data-ttu-id="1bdce-185">CertificateXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="1bdce-185">CertificateXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [<span data-ttu-id="1bdce-186">DpapiNGXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="1bdce-186">DpapiNGXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [<span data-ttu-id="1bdce-187">DpapiXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="1bdce-187">DpapiXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [<span data-ttu-id="1bdce-188">NullXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="1bdce-188">NullXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

<span data-ttu-id="1bdce-189">Para obtener más información, consulte el [documento de cifrado de claves en reposo](xref:security/data-protection/implementation/key-encryption-at-rest) .</span><span class="sxs-lookup"><span data-stu-id="1bdce-189">See the [key encryption at rest document](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

<span data-ttu-id="1bdce-190">Para cambiar el mecanismo predeterminado de cifrado en reposo de la aplicación, registre una `IXmlEncryptor` instancia personalizada:</span><span class="sxs-lookup"><span data-stu-id="1bdce-190">To change the default key-encryption-at-rest mechanism application-wide, register a custom `IXmlEncryptor` instance:</span></span>

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

## <a name="ixmldecryptor"></a><span data-ttu-id="1bdce-191">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="1bdce-191">IXmlDecryptor</span></span>

<span data-ttu-id="1bdce-192">La `IXmlDecryptor` interfaz representa un tipo que sabe cómo descifrar un `XElement` que se ha cifrado mediante `IXmlEncryptor` .</span><span class="sxs-lookup"><span data-stu-id="1bdce-192">The `IXmlDecryptor` interface represents a type that knows how to decrypt an `XElement` that was enciphered via an `IXmlEncryptor`.</span></span> <span data-ttu-id="1bdce-193">Expone una única API:</span><span class="sxs-lookup"><span data-stu-id="1bdce-193">It exposes a single API:</span></span>

* <span data-ttu-id="1bdce-194">Descifrado (XElement encryptedElement): XElement</span><span class="sxs-lookup"><span data-stu-id="1bdce-194">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="1bdce-195">El `Decrypt` método deshace el cifrado realizado por `IXmlEncryptor.Encrypt` .</span><span class="sxs-lookup"><span data-stu-id="1bdce-195">The `Decrypt` method undoes the encryption performed by `IXmlEncryptor.Encrypt`.</span></span> <span data-ttu-id="1bdce-196">Por lo general, cada implementación concreta tendrá `IXmlEncryptor` una implementación concreta correspondiente `IXmlDecryptor` .</span><span class="sxs-lookup"><span data-stu-id="1bdce-196">Generally, each concrete `IXmlEncryptor` implementation will have a corresponding concrete `IXmlDecryptor` implementation.</span></span>

<span data-ttu-id="1bdce-197">Los tipos que implementan `IXmlDecryptor` deben tener uno de los dos constructores públicos siguientes:</span><span class="sxs-lookup"><span data-stu-id="1bdce-197">Types which implement `IXmlDecryptor` should have one of the following two public constructors:</span></span>

* <span data-ttu-id="1bdce-198">. ctor (IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="1bdce-198">.ctor(IServiceProvider)</span></span>
* <span data-ttu-id="1bdce-199">. ctor ()</span><span class="sxs-lookup"><span data-stu-id="1bdce-199">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="1bdce-200">El argumento `IServiceProvider` pasado al constructor puede ser null.</span><span class="sxs-lookup"><span data-stu-id="1bdce-200">The `IServiceProvider` passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="1bdce-201">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="1bdce-201">IKeyEscrowSink</span></span>

<span data-ttu-id="1bdce-202">La `IKeyEscrowSink` interfaz representa un tipo que puede realizar custodia de información confidencial.</span><span class="sxs-lookup"><span data-stu-id="1bdce-202">The `IKeyEscrowSink` interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="1bdce-203">Recuerde que los descriptores serializados pueden contener información confidencial (por ejemplo, material criptográfico) y esto es lo que condujo a la introducción del tipo [IXmlEncryptor](#ixmlencryptor) en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="1bdce-203">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](#ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="1bdce-204">Sin embargo, se producen accidentes y los anillos clave se pueden eliminar o dañar.</span><span class="sxs-lookup"><span data-stu-id="1bdce-204">However, accidents happen, and key rings can be deleted or become corrupted.</span></span>

<span data-ttu-id="1bdce-205">La interfaz de custodia proporciona un sombreado de escape de emergencia, lo que permite el acceso al XML serializado sin formato antes de que lo transforme cualquier [IXmlEncryptor](#ixmlencryptor)configurada.</span><span class="sxs-lookup"><span data-stu-id="1bdce-205">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it's transformed by any configured [IXmlEncryptor](#ixmlencryptor).</span></span> <span data-ttu-id="1bdce-206">La interfaz expone una única API:</span><span class="sxs-lookup"><span data-stu-id="1bdce-206">The interface exposes a single API:</span></span>

* <span data-ttu-id="1bdce-207">Almacén (GUID de GUID, elemento XElement)</span><span class="sxs-lookup"><span data-stu-id="1bdce-207">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="1bdce-208">Depende de la `IKeyEscrowSink` implementación controlar el elemento proporcionado de forma segura coherente con la Directiva empresarial.</span><span class="sxs-lookup"><span data-stu-id="1bdce-208">It's up to the `IKeyEscrowSink` implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="1bdce-209">Una posible implementación podría ser que el receptor de custodia Cifre el elemento XML con un certificado X. 509 corporativo conocido en el que se haya custodiado la clave privada del certificado; el `CertificateXmlEncryptor` tipo puede ayudar con esto.</span><span class="sxs-lookup"><span data-stu-id="1bdce-209">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the `CertificateXmlEncryptor` type can assist with this.</span></span> <span data-ttu-id="1bdce-210">La `IKeyEscrowSink` implementación de también es responsable de conservar el elemento proporcionado de manera adecuada.</span><span class="sxs-lookup"><span data-stu-id="1bdce-210">The `IKeyEscrowSink` implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="1bdce-211">De forma predeterminada, no hay ningún mecanismo de custodia habilitado, aunque los administradores del servidor pueden [configurar esto globalmente](xref:security/data-protection/configuration/machine-wide-policy).</span><span class="sxs-lookup"><span data-stu-id="1bdce-211">By default no escrow mechanism is enabled, though server administrators can [configure this globally](xref:security/data-protection/configuration/machine-wide-policy).</span></span> <span data-ttu-id="1bdce-212">También se puede configurar mediante programación a través del `IDataProtectionBuilder.AddKeyEscrowSink` método como se muestra en el ejemplo siguiente.</span><span class="sxs-lookup"><span data-stu-id="1bdce-212">It can also be configured programmatically via the `IDataProtectionBuilder.AddKeyEscrowSink` method as shown in the sample below.</span></span> <span data-ttu-id="1bdce-213">El `AddKeyEscrowSink` método sobrecarga las `IServiceCollection.AddSingleton` `IServiceCollection.AddInstance` sobrecargas y, ya que `IKeyEscrowSink` las instancias están diseñadas para ser singleton.</span><span class="sxs-lookup"><span data-stu-id="1bdce-213">The `AddKeyEscrowSink` method overloads mirror the `IServiceCollection.AddSingleton` and `IServiceCollection.AddInstance` overloads, as `IKeyEscrowSink` instances are intended to be singletons.</span></span> <span data-ttu-id="1bdce-214">Si `IKeyEscrowSink` se registran varias instancias, se llamará a cada una de ellas durante la generación de claves, por lo que las claves se pueden enviar por custodia a varios mecanismos simultáneamente.</span><span class="sxs-lookup"><span data-stu-id="1bdce-214">If multiple `IKeyEscrowSink` instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="1bdce-215">No hay ninguna API para leer material de una `IKeyEscrowSink` instancia de.</span><span class="sxs-lookup"><span data-stu-id="1bdce-215">There's no API to read material from an `IKeyEscrowSink` instance.</span></span> <span data-ttu-id="1bdce-216">Esto es coherente con la teoría de diseño del mecanismo de custodia: está diseñado para que el material clave sea accesible a una entidad de confianza y, puesto que la aplicación no es una entidad de confianza, no debe tener acceso a su propio material con custodia.</span><span class="sxs-lookup"><span data-stu-id="1bdce-216">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="1bdce-217">En el código de ejemplo siguiente se muestra cómo crear y registrar una `IKeyEscrowSink` clave WHERE con custodia, de modo que solo los miembros de "CONTOSODomain Admins" puedan recuperarlas.</span><span class="sxs-lookup"><span data-stu-id="1bdce-217">The following sample code demonstrates creating and registering an `IKeyEscrowSink` where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="1bdce-218">Para ejecutar este ejemplo, debe estar en un equipo con Windows 8/Windows Server 2012 unido a un dominio y el controlador de dominio debe ser Windows Server 2012 o posterior.</span><span class="sxs-lookup"><span data-stu-id="1bdce-218">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
