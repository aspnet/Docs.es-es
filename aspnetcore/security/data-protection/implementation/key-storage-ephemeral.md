---
title: Proveedores de protección de datos efímeros en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre los detalles de implementación de los ASP.NET Core proveedores de protección de datos efímeros.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: ed0fca88ecf2b002a4421fb120d90adff1b5b12e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052704"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a>Proveedores de protección de datos efímeros en ASP.NET Core

<a name="data-protection-implementation-key-storage-ephemeral"></a>

Hay escenarios en los que una aplicación necesita un throwaway `IDataProtectionProvider` . Por ejemplo, el desarrollador podría estar experimentando en una aplicación de consola única o la propia aplicación es transitoria (se trata de un proyecto de prueba unitaria o de script). Para admitir estos escenarios, el paquete [Microsoft. AspNetCore. PROTECCION](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) incluye un tipo `EphemeralDataProtectionProvider` . Este tipo proporciona una implementación básica de `IDataProtectionProvider` cuyo repositorio de claves se mantiene únicamente en memoria y no se escribe en ningún almacén de respaldo.

Cada instancia de `EphemeralDataProtectionProvider` utiliza su propia clave maestra única. Por lo tanto, si un objeto `IDataProtector` cuya raíz se encuentra en `EphemeralDataProtectionProvider` genera una carga protegida, esa carga solo puede desprotegerse mediante un equivalente `IDataProtector` (dado la misma cadena de [propósito](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) ) que se ha modificado en la misma `EphemeralDataProtectionProvider` instancia.

En el ejemplo siguiente se muestra cómo crear una instancia de `EphemeralDataProtectionProvider` y utilizarla para proteger y desproteger los datos.

```csharp
using System;
using Microsoft.AspNetCore.DataProtection;

public class Program
{
    public static void Main(string[] args)
    {
        const string purpose = "Ephemeral.App.v1";

        // create an ephemeral provider and demonstrate that it can round-trip a payload
        var provider = new EphemeralDataProtectionProvider();
        var protector = provider.CreateProtector(purpose);
        Console.Write("Enter input: ");
        string input = Console.ReadLine();

        // protect the payload
        string protectedPayload = protector.Protect(input);
        Console.WriteLine($"Protect returned: {protectedPayload}");

        // unprotect the payload
        string unprotectedPayload = protector.Unprotect(protectedPayload);
        Console.WriteLine($"Unprotect returned: {unprotectedPayload}");

        // if I create a new ephemeral provider, it won't be able to unprotect existing
        // payloads, even if I specify the same purpose
        provider = new EphemeralDataProtectionProvider();
        protector = provider.CreateProtector(purpose);
        unprotectedPayload = protector.Unprotect(protectedPayload); // THROWS
    }
}

/*
* SAMPLE OUTPUT
*
* Enter input: Hello!
* Protect returned: CfDJ8AAAAAAAAAAAAAAAAAAAAA...uGoxWLjGKtm1SkNACQ
* Unprotect returned: Hello!
* << throws CryptographicException >>
*/
```
