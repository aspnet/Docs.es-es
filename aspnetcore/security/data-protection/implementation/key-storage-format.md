---
title: Formato de almacenamiento de claves en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre los detalles de implementación del formato de almacenamiento de la clave de protección de datos ASP.NET Core.
ms.author: riande
ms.date: 04/08/2020
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
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: daf86d3e3357d42ddad74d5e2f06e00e0e24db07
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631997"
---
# <a name="key-storage-format-in-aspnet-core"></a>Formato de almacenamiento de claves en ASP.NET Core

<a name="data-protection-implementation-key-storage-format"></a>

Los objetos se almacenan en reposo en representación XML. El directorio predeterminado para el almacenamiento de claves es:

* Windows: *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*
* macOS/Linux: *$Home/.Aspnet/dataprotection-Keys*

## <a name="the-key-element"></a>El elemento \<key>

Existen claves como objetos de nivel superior en el repositorio de claves. Por las claves de Convención tienen la clave de nombre de archivo **-{GUID}. XML**, donde {GUID} es el identificador de la clave. Cada archivo de este tipo contiene una sola clave. El formato del archivo es el siguiente.

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

El \<key> elemento contiene los atributos y elementos secundarios siguientes:

* Identificador de clave. Este valor se trata como autoritativo; el nombre de archivo es simplemente un nicety para la legibilidad humana.

* Versión del \<key> elemento, fijada actualmente en 1.

* Las fechas de creación, activación y expiración de la clave.

* Un \<descriptor> elemento, que contiene información sobre la implementación de cifrado autenticada incluida en esta clave.

En el ejemplo anterior, el identificador de la clave es {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, se creó y se activó el 19 de marzo de 2015 y tiene una duración de 90 días. (En ocasiones, la fecha de activación puede ser ligeramente anterior a la fecha de creación como en este ejemplo. Esto se debe a una prueba de cómo funcionan las API y es inofensivo en la práctica).

## <a name="the-descriptor-element"></a>El elemento \<descriptor>

El \<descriptor> elemento exterior contiene un atributo deserializerType, que es el nombre calificado con el ensamblado de un tipo que implementa IAuthenticatedEncryptorDescriptorDeserializer. Este tipo es responsable de leer el \<descriptor> elemento interno y de analizar la información contenida en.

El formato determinado del \<descriptor> elemento depende de la implementación del sistema de cifrado autenticado encapsulada por la clave, y cada tipo de deserializador espera un formato ligeramente diferente para esto. Sin embargo, en general, este elemento contendrá información algorítmica (nombres, tipos, OID o similares) y material de clave secreta. En el ejemplo anterior, el descriptor especifica que esta clave encapsula el cifrado AES-256-CBC + HMACSHA256.

## <a name="the-encryptedsecret-element"></a>El elemento \<encryptedSecret>

Un elemento ** &lt; encryptedSecret &gt; ** que contiene el formato cifrado del material de clave secreta puede estar presente si [está habilitado el cifrado de secretos en reposo](xref:security/data-protection/implementation/key-encryption-at-rest). El atributo `decryptorType` es el nombre calificado con el ensamblado de un tipo que implementa [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor). Este tipo es responsable de leer el elemento ** &lt; encryptedKey &gt; ** interno y descifrarlo para recuperar el texto sin formato original.

Al igual que con `<descriptor>` , el formato determinado del `<encryptedSecret>` elemento depende del mecanismo de cifrado en reposo en uso. En el ejemplo anterior, la clave maestra se cifra mediante la DPAPI de Windows según el comentario.

## <a name="the-revocation-element"></a>El elemento \<revocation>

Las revocación existen como objetos de nivel superior en el repositorio de claves. Las revocaciones de Convención tienen la **revocación del nombre de archivo-{timestamp}. XML** (para revocar todas las claves antes de una fecha concreta) o **revocación-{GUID}. XML** (para revocar una clave específica). Cada archivo contiene un solo \<revocation> elemento.

En el caso de las revocación de claves individuales, el contenido del archivo será como el que se indica a continuación.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

En este caso, solo se revoca la clave especificada. Sin embargo, si el identificador de clave es "*", como en el ejemplo siguiente, se revocan todas las claves cuya fecha de creación sea anterior a la fecha de revocación especificada.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

El \<reason> sistema nunca lee el elemento. Es simplemente un lugar cómodo para almacenar un motivo legible para la revocación.
