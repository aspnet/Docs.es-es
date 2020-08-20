---
title: Extensibilidad de criptografía básica en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer y el generador de nivel superior.
ms.author: riande
ms.date: 08/11/2017
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
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: 4c802bc4beb1f1fde812e6c3f55fc43b5d569b66
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635325"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a>Extensibilidad de criptografía básica en ASP.NET Core

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> Los tipos que implementan cualquiera de las interfaces siguientes deben ser seguros para subprocesos para varios llamadores.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a>IAuthenticatedEncryptor

La interfaz **IAuthenticatedEncryptor** es el bloque de creación básico del subsistema criptográfico. Por lo general, hay una IAuthenticatedEncryptor por clave y la instancia de IAuthenticatedEncryptor incluye todo el material de clave criptográfica y la información algorítmica necesaria para realizar operaciones criptográficas.

Como su nombre sugiere, el tipo es responsable de proporcionar los servicios de cifrado y descifrado autenticados. Expone las dos API siguientes.

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

El método Encrypt devuelve un BLOB que incluye el texto sin formato cifrado y una etiqueta de autenticación. La etiqueta de autenticación debe abarcar los datos autenticados adicionales (AAD), aunque el propio AAD no debe ser recuperable de la carga final. El método de descifrado valida la etiqueta de autenticación y devuelve la carga descifrada. Todos los errores (excepto ArgumentNullException y similares) se deben homogeneizar en CryptographicException.

> [!NOTE]
> En realidad, la instancia de IAuthenticatedEncryptor no necesita contener el material de clave. Por ejemplo, la implementación podría delegar a un HSM para todas las operaciones.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a>Creación de un IAuthenticatedEncryptor

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

La interfaz **IAuthenticatedEncryptorFactory** representa un tipo que sabe cómo crear una instancia de [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) . Su API es la siguiente.

* CreateEncryptorInstance (clave IKey): IAuthenticatedEncryptor

En el caso de una instancia de IKey determinada, todos los cifrados autenticados creados por su método CreateEncryptorInstance deben considerarse equivalentes, como en el ejemplo de código siguiente.

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

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

La interfaz **IAuthenticatedEncryptorDescriptor** representa un tipo que sabe cómo crear una instancia de [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) . Su API es la siguiente.

* CreateEncryptorInstance() : IAuthenticatedEncryptor

* ExportToXml() : XmlSerializedDescriptorInfo

Al igual que IAuthenticatedEncryptor, se supone que una instancia de IAuthenticatedEncryptorDescriptor encapsula una clave específica. Esto significa que, para una instancia de IAuthenticatedEncryptorDescriptor determinada, todos los cifrados autenticados creados por su método CreateEncryptorInstance deben considerarse equivalentes, como en el ejemplo de código siguiente.

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

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a>IAuthenticatedEncryptorDescriptor (solo ASP.NET Core 2. x)

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

La interfaz **IAuthenticatedEncryptorDescriptor** representa un tipo que sabe cómo exportar a XML. Su API es la siguiente.

* ExportToXml() : XmlSerializedDescriptorInfo

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a>Serialización XML

La principal diferencia entre IAuthenticatedEncryptor y IAuthenticatedEncryptorDescriptor es que el descriptor sabe cómo crear el sistema de cifrado y proporcionarle argumentos válidos. Considere una IAuthenticatedEncryptor cuya implementación se basa en SymmetricAlgorithm y en KeyedHashAlgorithm. El trabajo del sistema de cifrado es consumir estos tipos, pero no necesariamente sabe de dónde proceden estos tipos, por lo que realmente no puede escribir una descripción adecuada de cómo volver a crearlo si la aplicación se reinicia. El descriptor actúa como un nivel superior sobre este. Dado que el descriptor sabe cómo crear la instancia del sistema de cifrado (por ejemplo, sabe cómo crear los algoritmos necesarios), puede serializar ese conocimiento en formato XML para que se pueda volver a crear la instancia del sistema de cifrado después de restablecer la aplicación.

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

El descriptor se puede serializar a través de su rutina ExportToXml. Esta rutina devuelve un XmlSerializedDescriptorInfo que contiene dos propiedades: la representación de XElement del descriptor y el tipo que representa una [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) que se puede usar para restablecer Este descriptor según el XElement correspondiente.

El descriptor serializado puede contener información confidencial, como material de clave criptográfica. El sistema de protección de datos tiene compatibilidad integrada para cifrar la información antes de que se conserve en el almacenamiento. Para aprovechar esto, el descriptor debe marcar el elemento que contiene información confidencial con el nombre de atributo "requiresEncryption" (xmlns " <http://schemas.asp.net/2015/03/dataProtection> "), el valor "true".

>[!TIP]
> Hay una API auxiliar para establecer este atributo. Llame al método de extensión XElement. MarkAsRequiresEncryption () ubicado en el espacio de nombres Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.

También puede haber casos en los que el descriptor serializado no contenga información confidencial. Considere de nuevo el caso de una clave criptográfica almacenada en un HSM. El descriptor no puede escribir el material de clave al serializarse a sí mismo, ya que el HSM no expondrá el material en formato de texto simple. En su lugar, el descriptor podría escribir la versión con ajuste de clave de la clave (si el HSM permite la exportación de este modo) o el identificador único del HSM para la clave.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a>IAuthenticatedEncryptorDescriptorDeserializer

La interfaz **IAuthenticatedEncryptorDescriptorDeserializer** representa un tipo que sabe cómo deserializar una instancia de IAuthenticatedEncryptorDescriptor desde un XElement. Expone un método único:

* ImportFromXml (elemento XElement): IAuthenticatedEncryptorDescriptor

El método ImportFromXml toma el XElement devuelto por [IAuthenticatedEncryptorDescriptor. ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) y crea un equivalente del IAuthenticatedEncryptorDescriptor original.

Los tipos que implementan IAuthenticatedEncryptorDescriptorDeserializer deben tener uno de los dos constructores públicos siguientes:

* . ctor (IServiceProvider)

* . ctor ()

> [!NOTE]
> El IServiceProvider pasado al constructor puede ser null.

## <a name="the-top-level-factory"></a>El generador de nivel superior

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

La clase **AlgorithmConfiguration** representa un tipo que sabe cómo crear instancias de [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) . Expone una sola API.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

Considere AlgorithmConfiguration como el generador de nivel superior. La configuración sirve como plantilla. Contiene información sobre el algoritmo (por ejemplo, esta configuración produce descriptores con una clave maestra de AES-128-GCM), pero aún no está asociada a una clave específica.

Cuando se llama a CreateNewDescriptor, se crea material de clave nueva únicamente para esta llamada y se genera un nuevo IAuthenticatedEncryptorDescriptor que contiene este material de clave y la información algorítmica necesaria para consumir el material. El material de clave podría crearse en el software (y mantenerse en memoria), se podría crear y mantener dentro de un HSM, etc. El punto fundamental es que dos llamadas a CreateNewDescriptor nunca deben crear instancias de IAuthenticatedEncryptorDescriptor equivalentes.

El tipo AlgorithmConfiguration actúa como punto de entrada para las rutinas de creación de claves como el [desplazamiento automático de claves](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling). Para cambiar la implementación de todas las claves futuras, establezca la propiedad AuthenticatedEncryptorConfiguration en KeyManagementOptions.

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

La interfaz **IAuthenticatedEncryptorConfiguration** representa un tipo que sabe cómo crear instancias de [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) . Expone una sola API.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

Considere IAuthenticatedEncryptorConfiguration como el generador de nivel superior. La configuración sirve como plantilla. Contiene información sobre el algoritmo (por ejemplo, esta configuración produce descriptores con una clave maestra de AES-128-GCM), pero aún no está asociada a una clave específica.

Cuando se llama a CreateNewDescriptor, se crea material de clave nueva únicamente para esta llamada y se genera un nuevo IAuthenticatedEncryptorDescriptor que contiene este material de clave y la información algorítmica necesaria para consumir el material. El material de clave podría crearse en el software (y mantenerse en memoria), se podría crear y mantener dentro de un HSM, etc. El punto fundamental es que dos llamadas a CreateNewDescriptor nunca deben crear instancias de IAuthenticatedEncryptorDescriptor equivalentes.

El tipo IAuthenticatedEncryptorConfiguration actúa como punto de entrada para las rutinas de creación de claves como el [desplazamiento automático de claves](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling). Para cambiar la implementación de todas las claves futuras, registre un IAuthenticatedEncryptorConfiguration Singleton en el contenedor de servicios.

---
