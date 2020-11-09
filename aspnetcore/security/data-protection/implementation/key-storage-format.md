---
title: Formato de almacenamiento de claves en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre los detalles de implementación del formato de almacenamiento de la clave de protección de datos ASP.NET Core.
ms.author: riande
ms.date: 04/08/2020
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
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 4a8503964c98d1828dc9d02640a7621b370e679c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060149"
---
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="35ec5-103">Formato de almacenamiento de claves en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="35ec5-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="35ec5-104">Los objetos se almacenan en reposo en representación XML.</span><span class="sxs-lookup"><span data-stu-id="35ec5-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="35ec5-105">El directorio predeterminado para el almacenamiento de claves es:</span><span class="sxs-lookup"><span data-stu-id="35ec5-105">The default directory for key storage is:</span></span>

* <span data-ttu-id="35ec5-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span><span class="sxs-lookup"><span data-stu-id="35ec5-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span></span>
* <span data-ttu-id="35ec5-107">macOS/Linux: *$Home/.Aspnet/dataprotection-Keys*</span><span class="sxs-lookup"><span data-stu-id="35ec5-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span></span>

## <a name="the-key-element"></a><span data-ttu-id="35ec5-108">El elemento \<key></span><span class="sxs-lookup"><span data-stu-id="35ec5-108">The \<key> element</span></span>

<span data-ttu-id="35ec5-109">Existen claves como objetos de nivel superior en el repositorio de claves.</span><span class="sxs-lookup"><span data-stu-id="35ec5-109">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="35ec5-110">Por las claves de Convención tienen la clave de nombre de archivo **-{GUID}. XML** , donde {GUID} es el identificador de la clave.</span><span class="sxs-lookup"><span data-stu-id="35ec5-110">By convention keys have the filename **key-{guid}.xml** , where {guid} is the id of the key.</span></span> <span data-ttu-id="35ec5-111">Cada archivo de este tipo contiene una sola clave.</span><span class="sxs-lookup"><span data-stu-id="35ec5-111">Each such file contains a single key.</span></span> <span data-ttu-id="35ec5-112">El formato del archivo es el siguiente.</span><span class="sxs-lookup"><span data-stu-id="35ec5-112">The format of the file is as follows.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

<span data-ttu-id="35ec5-113">El \<key> elemento contiene los atributos y elementos secundarios siguientes:</span><span class="sxs-lookup"><span data-stu-id="35ec5-113">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="35ec5-114">Identificador de clave. Este valor se trata como autoritativo; el nombre de archivo es simplemente un nicety para la legibilidad humana.</span><span class="sxs-lookup"><span data-stu-id="35ec5-114">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="35ec5-115">Versión del \<key> elemento, fijada actualmente en 1.</span><span class="sxs-lookup"><span data-stu-id="35ec5-115">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="35ec5-116">Las fechas de creación, activación y expiración de la clave.</span><span class="sxs-lookup"><span data-stu-id="35ec5-116">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="35ec5-117">Un \<descriptor> elemento, que contiene información sobre la implementación de cifrado autenticada incluida en esta clave.</span><span class="sxs-lookup"><span data-stu-id="35ec5-117">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="35ec5-118">En el ejemplo anterior, el identificador de la clave es {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, se creó y se activó el 19 de marzo de 2015 y tiene una duración de 90 días.</span><span class="sxs-lookup"><span data-stu-id="35ec5-118">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="35ec5-119">(En ocasiones, la fecha de activación puede ser ligeramente anterior a la fecha de creación como en este ejemplo.</span><span class="sxs-lookup"><span data-stu-id="35ec5-119">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="35ec5-120">Esto se debe a una prueba de cómo funcionan las API y es inofensivo en la práctica).</span><span class="sxs-lookup"><span data-stu-id="35ec5-120">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="35ec5-121">El elemento \<descriptor></span><span class="sxs-lookup"><span data-stu-id="35ec5-121">The \<descriptor> element</span></span>

<span data-ttu-id="35ec5-122">El \<descriptor> elemento exterior contiene un atributo deserializerType, que es el nombre calificado con el ensamblado de un tipo que implementa IAuthenticatedEncryptorDescriptorDeserializer.</span><span class="sxs-lookup"><span data-stu-id="35ec5-122">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="35ec5-123">Este tipo es responsable de leer el \<descriptor> elemento interno y de analizar la información contenida en.</span><span class="sxs-lookup"><span data-stu-id="35ec5-123">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="35ec5-124">El formato determinado del \<descriptor> elemento depende de la implementación del sistema de cifrado autenticado encapsulada por la clave, y cada tipo de deserializador espera un formato ligeramente diferente para esto.</span><span class="sxs-lookup"><span data-stu-id="35ec5-124">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="35ec5-125">Sin embargo, en general, este elemento contendrá información algorítmica (nombres, tipos, OID o similares) y material de clave secreta.</span><span class="sxs-lookup"><span data-stu-id="35ec5-125">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="35ec5-126">En el ejemplo anterior, el descriptor especifica que esta clave encapsula el cifrado AES-256-CBC + HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="35ec5-126">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="35ec5-127">El elemento \<encryptedSecret></span><span class="sxs-lookup"><span data-stu-id="35ec5-127">The \<encryptedSecret> element</span></span>

<span data-ttu-id="35ec5-128">Un elemento **&lt; encryptedSecret &gt;** que contiene el formato cifrado del material de clave secreta puede estar presente si [está habilitado el cifrado de secretos en reposo](xref:security/data-protection/implementation/key-encryption-at-rest).</span><span class="sxs-lookup"><span data-stu-id="35ec5-128">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="35ec5-129">El atributo `decryptorType` es el nombre calificado con el ensamblado de un tipo que implementa [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span><span class="sxs-lookup"><span data-stu-id="35ec5-129">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="35ec5-130">Este tipo es responsable de leer el elemento **&lt; encryptedKey &gt;** interno y descifrarlo para recuperar el texto sin formato original.</span><span class="sxs-lookup"><span data-stu-id="35ec5-130">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="35ec5-131">Al igual que con `<descriptor>` , el formato determinado del `<encryptedSecret>` elemento depende del mecanismo de cifrado en reposo en uso.</span><span class="sxs-lookup"><span data-stu-id="35ec5-131">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="35ec5-132">En el ejemplo anterior, la clave maestra se cifra mediante la DPAPI de Windows según el comentario.</span><span class="sxs-lookup"><span data-stu-id="35ec5-132">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="35ec5-133">El elemento \<revocation></span><span class="sxs-lookup"><span data-stu-id="35ec5-133">The \<revocation> element</span></span>

<span data-ttu-id="35ec5-134">Las revocación existen como objetos de nivel superior en el repositorio de claves.</span><span class="sxs-lookup"><span data-stu-id="35ec5-134">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="35ec5-135">Las revocaciones de Convención tienen la **revocación del nombre de archivo-{timestamp}. XML** (para revocar todas las claves antes de una fecha concreta) o **revocación-{GUID}. XML** (para revocar una clave específica).</span><span class="sxs-lookup"><span data-stu-id="35ec5-135">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="35ec5-136">Cada archivo contiene un solo \<revocation> elemento.</span><span class="sxs-lookup"><span data-stu-id="35ec5-136">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="35ec5-137">En el caso de las revocación de claves individuales, el contenido del archivo será como el que se indica a continuación.</span><span class="sxs-lookup"><span data-stu-id="35ec5-137">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="35ec5-138">En este caso, solo se revoca la clave especificada.</span><span class="sxs-lookup"><span data-stu-id="35ec5-138">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="35ec5-139">Sin embargo, si el identificador de clave es "\*", como en el ejemplo siguiente, se revocan todas las claves cuya fecha de creación sea anterior a la fecha de revocación especificada.</span><span class="sxs-lookup"><span data-stu-id="35ec5-139">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="35ec5-140">El \<reason> sistema nunca lee el elemento.</span><span class="sxs-lookup"><span data-stu-id="35ec5-140">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="35ec5-141">Es simplemente un lugar cómodo para almacenar un motivo legible para la revocación.</span><span class="sxs-lookup"><span data-stu-id="35ec5-141">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
