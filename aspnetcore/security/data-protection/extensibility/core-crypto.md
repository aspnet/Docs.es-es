---
title: Extensibilidad de criptografía básica en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer y el generador de nivel superior.
ms.author: riande
ms.date: 08/11/2017
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
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: 99ce283d56a6010ddd846f21e0ca9a9a324d55fc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060786"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a><span data-ttu-id="a15a7-103">Extensibilidad de criptografía básica en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a15a7-103">Core cryptography extensibility in ASP.NET Core</span></span>

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> <span data-ttu-id="a15a7-104">Los tipos que implementan cualquiera de las interfaces siguientes deben ser seguros para subprocesos para varios llamadores.</span><span class="sxs-lookup"><span data-stu-id="a15a7-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a><span data-ttu-id="a15a7-105">IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="a15a7-105">IAuthenticatedEncryptor</span></span>

<span data-ttu-id="a15a7-106">La interfaz **IAuthenticatedEncryptor** es el bloque de creación básico del subsistema criptográfico.</span><span class="sxs-lookup"><span data-stu-id="a15a7-106">The **IAuthenticatedEncryptor** interface is the basic building block of the cryptographic subsystem.</span></span> <span data-ttu-id="a15a7-107">Por lo general, hay una IAuthenticatedEncryptor por clave y la instancia de IAuthenticatedEncryptor incluye todo el material de clave criptográfica y la información algorítmica necesaria para realizar operaciones criptográficas.</span><span class="sxs-lookup"><span data-stu-id="a15a7-107">There's generally one IAuthenticatedEncryptor per key, and the IAuthenticatedEncryptor instance wraps all cryptographic key material and algorithmic information necessary to perform cryptographic operations.</span></span>

<span data-ttu-id="a15a7-108">Como su nombre sugiere, el tipo es responsable de proporcionar los servicios de cifrado y descifrado autenticados.</span><span class="sxs-lookup"><span data-stu-id="a15a7-108">As its name suggests, the type is responsible for providing authenticated encryption and decryption services.</span></span> <span data-ttu-id="a15a7-109">Expone las dos API siguientes.</span><span class="sxs-lookup"><span data-stu-id="a15a7-109">It exposes the following two APIs.</span></span>

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

<span data-ttu-id="a15a7-110">El método Encrypt devuelve un BLOB que incluye el texto sin formato cifrado y una etiqueta de autenticación.</span><span class="sxs-lookup"><span data-stu-id="a15a7-110">The Encrypt method returns a blob that includes the enciphered plaintext and an authentication tag.</span></span> <span data-ttu-id="a15a7-111">La etiqueta de autenticación debe abarcar los datos autenticados adicionales (AAD), aunque el propio AAD no debe ser recuperable de la carga final.</span><span class="sxs-lookup"><span data-stu-id="a15a7-111">The authentication tag must encompass the additional authenticated data (AAD), though the AAD itself need not be recoverable from the final payload.</span></span> <span data-ttu-id="a15a7-112">El método de descifrado valida la etiqueta de autenticación y devuelve la carga descifrada.</span><span class="sxs-lookup"><span data-stu-id="a15a7-112">The Decrypt method validates the authentication tag and returns the deciphered payload.</span></span> <span data-ttu-id="a15a7-113">Todos los errores (excepto ArgumentNullException y similares) se deben homogeneizar en CryptographicException.</span><span class="sxs-lookup"><span data-stu-id="a15a7-113">All failures (except ArgumentNullException and similar) should be homogenized to CryptographicException.</span></span>

> [!NOTE]
> <span data-ttu-id="a15a7-114">En realidad, la instancia de IAuthenticatedEncryptor no necesita contener el material de clave.</span><span class="sxs-lookup"><span data-stu-id="a15a7-114">The IAuthenticatedEncryptor instance itself doesn't actually need to contain the key material.</span></span> <span data-ttu-id="a15a7-115">Por ejemplo, la implementación podría delegar a un HSM para todas las operaciones.</span><span class="sxs-lookup"><span data-stu-id="a15a7-115">For example, the implementation could delegate to an HSM for all operations.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a><span data-ttu-id="a15a7-116">Creación de un IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="a15a7-116">How to create an IAuthenticatedEncryptor</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="a15a7-117">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="a15a7-117">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="a15a7-118">La interfaz **IAuthenticatedEncryptorFactory** representa un tipo que sabe cómo crear una instancia de [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) .</span><span class="sxs-lookup"><span data-stu-id="a15a7-118">The **IAuthenticatedEncryptorFactory** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="a15a7-119">Su API es la siguiente.</span><span class="sxs-lookup"><span data-stu-id="a15a7-119">Its API is as follows.</span></span>

* <span data-ttu-id="a15a7-120">CreateEncryptorInstance (clave IKey): IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="a15a7-120">CreateEncryptorInstance(IKey key) : IAuthenticatedEncryptor</span></span>

<span data-ttu-id="a15a7-121">En el caso de una instancia de IKey determinada, todos los cifrados autenticados creados por su método CreateEncryptorInstance deben considerarse equivalentes, como en el ejemplo de código siguiente.</span><span class="sxs-lookup"><span data-stu-id="a15a7-121">For any given IKey instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="a15a7-122">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a15a7-122">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="a15a7-123">La interfaz **IAuthenticatedEncryptorDescriptor** representa un tipo que sabe cómo crear una instancia de [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) .</span><span class="sxs-lookup"><span data-stu-id="a15a7-123">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="a15a7-124">Su API es la siguiente.</span><span class="sxs-lookup"><span data-stu-id="a15a7-124">Its API is as follows.</span></span>

* <span data-ttu-id="a15a7-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="a15a7-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span></span>

* <span data-ttu-id="a15a7-126">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="a15a7-126">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

<span data-ttu-id="a15a7-127">Al igual que IAuthenticatedEncryptor, se supone que una instancia de IAuthenticatedEncryptorDescriptor encapsula una clave específica.</span><span class="sxs-lookup"><span data-stu-id="a15a7-127">Like IAuthenticatedEncryptor, an instance of IAuthenticatedEncryptorDescriptor is assumed to wrap one specific key.</span></span> <span data-ttu-id="a15a7-128">Esto significa que, para una instancia de IAuthenticatedEncryptorDescriptor determinada, todos los cifrados autenticados creados por su método CreateEncryptorInstance deben considerarse equivalentes, como en el ejemplo de código siguiente.</span><span class="sxs-lookup"><span data-stu-id="a15a7-128">This means that for any given IAuthenticatedEncryptorDescriptor instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a><span data-ttu-id="a15a7-129">IAuthenticatedEncryptorDescriptor (solo ASP.NET Core 2. x)</span><span class="sxs-lookup"><span data-stu-id="a15a7-129">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x only)</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="a15a7-130">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="a15a7-130">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="a15a7-131">La interfaz **IAuthenticatedEncryptorDescriptor** representa un tipo que sabe cómo exportar a XML.</span><span class="sxs-lookup"><span data-stu-id="a15a7-131">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to export itself to XML.</span></span> <span data-ttu-id="a15a7-132">Su API es la siguiente.</span><span class="sxs-lookup"><span data-stu-id="a15a7-132">Its API is as follows.</span></span>

* <span data-ttu-id="a15a7-133">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="a15a7-133">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="a15a7-134">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a15a7-134">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a><span data-ttu-id="a15a7-135">Serialización XML</span><span class="sxs-lookup"><span data-stu-id="a15a7-135">XML Serialization</span></span>

<span data-ttu-id="a15a7-136">La principal diferencia entre IAuthenticatedEncryptor y IAuthenticatedEncryptorDescriptor es que el descriptor sabe cómo crear el sistema de cifrado y proporcionarle argumentos válidos.</span><span class="sxs-lookup"><span data-stu-id="a15a7-136">The primary difference between IAuthenticatedEncryptor and IAuthenticatedEncryptorDescriptor is that the descriptor knows how to create the encryptor and supply it with valid arguments.</span></span> <span data-ttu-id="a15a7-137">Considere una IAuthenticatedEncryptor cuya implementación se basa en SymmetricAlgorithm y en KeyedHashAlgorithm.</span><span class="sxs-lookup"><span data-stu-id="a15a7-137">Consider an IAuthenticatedEncryptor whose implementation relies on SymmetricAlgorithm and KeyedHashAlgorithm.</span></span> <span data-ttu-id="a15a7-138">El trabajo del sistema de cifrado es consumir estos tipos, pero no necesariamente sabe de dónde proceden estos tipos, por lo que realmente no puede escribir una descripción adecuada de cómo volver a crearlo si la aplicación se reinicia.</span><span class="sxs-lookup"><span data-stu-id="a15a7-138">The encryptor's job is to consume these types, but it doesn't necessarily know where these types came from, so it can't really write out a proper description of how to recreate itself if the application restarts.</span></span> <span data-ttu-id="a15a7-139">El descriptor actúa como un nivel superior sobre este.</span><span class="sxs-lookup"><span data-stu-id="a15a7-139">The descriptor acts as a higher level on top of this.</span></span> <span data-ttu-id="a15a7-140">Dado que el descriptor sabe cómo crear la instancia del sistema de cifrado (por ejemplo, sabe cómo crear los algoritmos necesarios), puede serializar ese conocimiento en formato XML para que se pueda volver a crear la instancia del sistema de cifrado después de restablecer la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a15a7-140">Since the descriptor knows how to create the encryptor instance (e.g., it knows how to create the required algorithms), it can serialize that knowledge in XML form so that the encryptor instance can be recreated after an application reset.</span></span>

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

<span data-ttu-id="a15a7-141">El descriptor se puede serializar a través de su rutina ExportToXml.</span><span class="sxs-lookup"><span data-stu-id="a15a7-141">The descriptor can be serialized via its ExportToXml routine.</span></span> <span data-ttu-id="a15a7-142">Esta rutina devuelve un XmlSerializedDescriptorInfo que contiene dos propiedades: la representación de XElement del descriptor y el tipo que representa una [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) que se puede usar para restablecer Este descriptor según el XElement correspondiente.</span><span class="sxs-lookup"><span data-stu-id="a15a7-142">This routine returns an XmlSerializedDescriptorInfo which contains two properties: the XElement representation of the descriptor and the Type which represents an [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) which can be used to resurrect this descriptor given the corresponding XElement.</span></span>

<span data-ttu-id="a15a7-143">El descriptor serializado puede contener información confidencial, como material de clave criptográfica.</span><span class="sxs-lookup"><span data-stu-id="a15a7-143">The serialized descriptor may contain sensitive information such as cryptographic key material.</span></span> <span data-ttu-id="a15a7-144">El sistema de protección de datos tiene compatibilidad integrada para cifrar la información antes de que se conserve en el almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="a15a7-144">The data protection system has built-in support for encrypting information before it's persisted to storage.</span></span> <span data-ttu-id="a15a7-145">Para aprovechar esto, el descriptor debe marcar el elemento que contiene información confidencial con el nombre de atributo "requiresEncryption" (xmlns " <http://schemas.asp.net/2015/03/dataProtection> "), el valor "true".</span><span class="sxs-lookup"><span data-stu-id="a15a7-145">To take advantage of this, the descriptor should mark the element which contains sensitive information with the attribute name "requiresEncryption" (xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), value "true".</span></span>

>[!TIP]
> <span data-ttu-id="a15a7-146">Hay una API auxiliar para establecer este atributo.</span><span class="sxs-lookup"><span data-stu-id="a15a7-146">There's a helper API for setting this attribute.</span></span> <span data-ttu-id="a15a7-147">Llame al método de extensión XElement. MarkAsRequiresEncryption () ubicado en el espacio de nombres Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span><span class="sxs-lookup"><span data-stu-id="a15a7-147">Call the extension method XElement.MarkAsRequiresEncryption() located in namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span></span>

<span data-ttu-id="a15a7-148">También puede haber casos en los que el descriptor serializado no contenga información confidencial.</span><span class="sxs-lookup"><span data-stu-id="a15a7-148">There can also be cases where the serialized descriptor doesn't contain sensitive information.</span></span> <span data-ttu-id="a15a7-149">Considere de nuevo el caso de una clave criptográfica almacenada en un HSM.</span><span class="sxs-lookup"><span data-stu-id="a15a7-149">Consider again the case of a cryptographic key stored in an HSM.</span></span> <span data-ttu-id="a15a7-150">El descriptor no puede escribir el material de clave al serializarse a sí mismo, ya que el HSM no expondrá el material en formato de texto simple.</span><span class="sxs-lookup"><span data-stu-id="a15a7-150">The descriptor cannot write out the key material when serializing itself since the HSM won't expose the material in plaintext form.</span></span> <span data-ttu-id="a15a7-151">En su lugar, el descriptor podría escribir la versión con ajuste de clave de la clave (si el HSM permite la exportación de este modo) o el identificador único del HSM para la clave.</span><span class="sxs-lookup"><span data-stu-id="a15a7-151">Instead, the descriptor might write out the key-wrapped version of the key (if the HSM allows export in this fashion) or the HSM's own unique identifier for the key.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a><span data-ttu-id="a15a7-152">IAuthenticatedEncryptorDescriptorDeserializer</span><span class="sxs-lookup"><span data-stu-id="a15a7-152">IAuthenticatedEncryptorDescriptorDeserializer</span></span>

<span data-ttu-id="a15a7-153">La interfaz **IAuthenticatedEncryptorDescriptorDeserializer** representa un tipo que sabe cómo deserializar una instancia de IAuthenticatedEncryptorDescriptor desde un XElement.</span><span class="sxs-lookup"><span data-stu-id="a15a7-153">The **IAuthenticatedEncryptorDescriptorDeserializer** interface represents a type that knows how to deserialize an IAuthenticatedEncryptorDescriptor instance from an XElement.</span></span> <span data-ttu-id="a15a7-154">Expone un método único:</span><span class="sxs-lookup"><span data-stu-id="a15a7-154">It exposes a single method:</span></span>

* <span data-ttu-id="a15a7-155">ImportFromXml (elemento XElement): IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="a15a7-155">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="a15a7-156">El método ImportFromXml toma el XElement devuelto por [IAuthenticatedEncryptorDescriptor. ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) y crea un equivalente del IAuthenticatedEncryptorDescriptor original.</span><span class="sxs-lookup"><span data-stu-id="a15a7-156">The ImportFromXml method takes the XElement that was returned by [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) and creates an equivalent of the original IAuthenticatedEncryptorDescriptor.</span></span>

<span data-ttu-id="a15a7-157">Los tipos que implementan IAuthenticatedEncryptorDescriptorDeserializer deben tener uno de los dos constructores públicos siguientes:</span><span class="sxs-lookup"><span data-stu-id="a15a7-157">Types which implement IAuthenticatedEncryptorDescriptorDeserializer should have one of the following two public constructors:</span></span>

* <span data-ttu-id="a15a7-158">. ctor (IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="a15a7-158">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="a15a7-159">. ctor ()</span><span class="sxs-lookup"><span data-stu-id="a15a7-159">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="a15a7-160">El IServiceProvider pasado al constructor puede ser null.</span><span class="sxs-lookup"><span data-stu-id="a15a7-160">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="the-top-level-factory"></a><span data-ttu-id="a15a7-161">El generador de nivel superior</span><span class="sxs-lookup"><span data-stu-id="a15a7-161">The top-level factory</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="a15a7-162">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="a15a7-162">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="a15a7-163">La clase **AlgorithmConfiguration** representa un tipo que sabe cómo crear instancias de [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) .</span><span class="sxs-lookup"><span data-stu-id="a15a7-163">The **AlgorithmConfiguration** class represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="a15a7-164">Expone una sola API.</span><span class="sxs-lookup"><span data-stu-id="a15a7-164">It exposes a single API.</span></span>

* <span data-ttu-id="a15a7-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="a15a7-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="a15a7-166">Considere AlgorithmConfiguration como el generador de nivel superior.</span><span class="sxs-lookup"><span data-stu-id="a15a7-166">Think of AlgorithmConfiguration as the top-level factory.</span></span> <span data-ttu-id="a15a7-167">La configuración sirve como plantilla.</span><span class="sxs-lookup"><span data-stu-id="a15a7-167">The configuration serves as a template.</span></span> <span data-ttu-id="a15a7-168">Contiene información sobre el algoritmo (por ejemplo, esta configuración produce descriptores con una clave maestra de AES-128-GCM), pero aún no está asociada a una clave específica.</span><span class="sxs-lookup"><span data-stu-id="a15a7-168">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="a15a7-169">Cuando se llama a CreateNewDescriptor, se crea material de clave nueva únicamente para esta llamada y se genera un nuevo IAuthenticatedEncryptorDescriptor que contiene este material de clave y la información algorítmica necesaria para consumir el material.</span><span class="sxs-lookup"><span data-stu-id="a15a7-169">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="a15a7-170">El material de clave podría crearse en el software (y mantenerse en memoria), se podría crear y mantener dentro de un HSM, etc.</span><span class="sxs-lookup"><span data-stu-id="a15a7-170">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="a15a7-171">El punto fundamental es que dos llamadas a CreateNewDescriptor nunca deben crear instancias de IAuthenticatedEncryptorDescriptor equivalentes.</span><span class="sxs-lookup"><span data-stu-id="a15a7-171">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="a15a7-172">El tipo AlgorithmConfiguration actúa como punto de entrada para las rutinas de creación de claves como el [desplazamiento automático de claves](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span><span class="sxs-lookup"><span data-stu-id="a15a7-172">The AlgorithmConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="a15a7-173">Para cambiar la implementación de todas las claves futuras, establezca la propiedad AuthenticatedEncryptorConfiguration en KeyManagementOptions.</span><span class="sxs-lookup"><span data-stu-id="a15a7-173">To change the implementation for all future keys, set the AuthenticatedEncryptorConfiguration property in KeyManagementOptions.</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="a15a7-174">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a15a7-174">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="a15a7-175">La interfaz **IAuthenticatedEncryptorConfiguration** representa un tipo que sabe cómo crear instancias de [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) .</span><span class="sxs-lookup"><span data-stu-id="a15a7-175">The **IAuthenticatedEncryptorConfiguration** interface represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="a15a7-176">Expone una sola API.</span><span class="sxs-lookup"><span data-stu-id="a15a7-176">It exposes a single API.</span></span>

* <span data-ttu-id="a15a7-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="a15a7-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="a15a7-178">Considere IAuthenticatedEncryptorConfiguration como el generador de nivel superior.</span><span class="sxs-lookup"><span data-stu-id="a15a7-178">Think of IAuthenticatedEncryptorConfiguration as the top-level factory.</span></span> <span data-ttu-id="a15a7-179">La configuración sirve como plantilla.</span><span class="sxs-lookup"><span data-stu-id="a15a7-179">The configuration serves as a template.</span></span> <span data-ttu-id="a15a7-180">Contiene información sobre el algoritmo (por ejemplo, esta configuración produce descriptores con una clave maestra de AES-128-GCM), pero aún no está asociada a una clave específica.</span><span class="sxs-lookup"><span data-stu-id="a15a7-180">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="a15a7-181">Cuando se llama a CreateNewDescriptor, se crea material de clave nueva únicamente para esta llamada y se genera un nuevo IAuthenticatedEncryptorDescriptor que contiene este material de clave y la información algorítmica necesaria para consumir el material.</span><span class="sxs-lookup"><span data-stu-id="a15a7-181">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="a15a7-182">El material de clave podría crearse en el software (y mantenerse en memoria), se podría crear y mantener dentro de un HSM, etc.</span><span class="sxs-lookup"><span data-stu-id="a15a7-182">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="a15a7-183">El punto fundamental es que dos llamadas a CreateNewDescriptor nunca deben crear instancias de IAuthenticatedEncryptorDescriptor equivalentes.</span><span class="sxs-lookup"><span data-stu-id="a15a7-183">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="a15a7-184">El tipo IAuthenticatedEncryptorConfiguration actúa como punto de entrada para las rutinas de creación de claves como el [desplazamiento automático de claves](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span><span class="sxs-lookup"><span data-stu-id="a15a7-184">The IAuthenticatedEncryptorConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="a15a7-185">Para cambiar la implementación de todas las claves futuras, registre un IAuthenticatedEncryptorConfiguration Singleton en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="a15a7-185">To change the implementation for all future keys, register a singleton IAuthenticatedEncryptorConfiguration in the service container.</span></span>

---
