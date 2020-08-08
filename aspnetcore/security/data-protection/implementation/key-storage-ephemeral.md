---
title: Proveedores de protección de datos efímeros en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre los detalles de implementación de los ASP.NET Core proveedores de protección de datos efímeros.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: f51553385d9481a1e96fe3e1a14e51e470b0e735
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018265"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a><span data-ttu-id="bc92f-103">Proveedores de protección de datos efímeros en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bc92f-103">Ephemeral data protection providers in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-ephemeral"></a>

<span data-ttu-id="bc92f-104">Hay escenarios en los que una aplicación necesita un throwaway `IDataProtectionProvider` .</span><span class="sxs-lookup"><span data-stu-id="bc92f-104">There are scenarios where an application needs a throwaway `IDataProtectionProvider`.</span></span> <span data-ttu-id="bc92f-105">Por ejemplo, el desarrollador podría estar experimentando en una aplicación de consola única o la propia aplicación es transitoria (se trata de un proyecto de prueba unitaria o de script).</span><span class="sxs-lookup"><span data-stu-id="bc92f-105">For example, the developer might just be experimenting in a one-off console application, or the application itself is transient (it's scripted or a unit test project).</span></span> <span data-ttu-id="bc92f-106">Para admitir estos escenarios, el paquete [Microsoft. AspNetCore. PROTECCION](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) incluye un tipo `EphemeralDataProtectionProvider` .</span><span class="sxs-lookup"><span data-stu-id="bc92f-106">To support these scenarios the [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) package includes a type `EphemeralDataProtectionProvider`.</span></span> <span data-ttu-id="bc92f-107">Este tipo proporciona una implementación básica de `IDataProtectionProvider` cuyo repositorio de claves se mantiene únicamente en memoria y no se escribe en ningún almacén de respaldo.</span><span class="sxs-lookup"><span data-stu-id="bc92f-107">This type provides a basic implementation of `IDataProtectionProvider` whose key repository is held solely in-memory and isn't written out to any backing store.</span></span>

<span data-ttu-id="bc92f-108">Cada instancia de `EphemeralDataProtectionProvider` utiliza su propia clave maestra única.</span><span class="sxs-lookup"><span data-stu-id="bc92f-108">Each instance of `EphemeralDataProtectionProvider` uses its own unique master key.</span></span> <span data-ttu-id="bc92f-109">Por lo tanto, si un objeto `IDataProtector` cuya raíz se encuentra en `EphemeralDataProtectionProvider` genera una carga protegida, esa carga solo puede desprotegerse mediante un equivalente `IDataProtector` (dado la misma cadena de [propósito](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) ) que se ha modificado en la misma `EphemeralDataProtectionProvider` instancia.</span><span class="sxs-lookup"><span data-stu-id="bc92f-109">Therefore, if an `IDataProtector` rooted at an `EphemeralDataProtectionProvider` generates a protected payload, that payload can only be unprotected by an equivalent `IDataProtector` (given the same [purpose](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) chain) rooted at the same `EphemeralDataProtectionProvider` instance.</span></span>

<span data-ttu-id="bc92f-110">En el ejemplo siguiente se muestra cómo crear una instancia de `EphemeralDataProtectionProvider` y utilizarla para proteger y desproteger los datos.</span><span class="sxs-lookup"><span data-stu-id="bc92f-110">The following sample demonstrates instantiating an `EphemeralDataProtectionProvider` and using it to protect and unprotect data.</span></span>

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
