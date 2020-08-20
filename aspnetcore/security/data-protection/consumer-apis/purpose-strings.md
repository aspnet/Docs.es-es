---
title: Cadenas de propósito en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo se usan las cadenas de propósito en las API de protección de datos de ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 1119c45570338f629a3ab7adbd43361529aa23e7
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626927"
---
# <a name="purpose-strings-in-aspnet-core"></a><span data-ttu-id="2fd66-103">Cadenas de propósito en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2fd66-103">Purpose strings in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-purposes"></a>

<span data-ttu-id="2fd66-104">Los componentes que consumen `IDataProtectionProvider` deben pasar un parámetro de *propósitos* únicos al `CreateProtector` método.</span><span class="sxs-lookup"><span data-stu-id="2fd66-104">Components which consume `IDataProtectionProvider` must pass a unique *purposes* parameter to the `CreateProtector` method.</span></span> <span data-ttu-id="2fd66-105">El *parámetro* propósitos es inherente a la seguridad del sistema de protección de datos, ya que proporciona aislamiento entre los consumidores criptográficos, incluso si las claves criptográficas raíz son las mismas.</span><span class="sxs-lookup"><span data-stu-id="2fd66-105">The purposes *parameter* is inherent to the security of the data protection system, as it provides isolation between cryptographic consumers, even if the root cryptographic keys are the same.</span></span>

<span data-ttu-id="2fd66-106">Cuando un consumidor especifica un propósito, se usa la cadena de propósito junto con las claves criptográficas raíz para derivar las subclaves criptográficas únicas de ese consumidor.</span><span class="sxs-lookup"><span data-stu-id="2fd66-106">When a consumer specifies a purpose, the purpose string is used along with the root cryptographic keys to derive cryptographic subkeys unique to that consumer.</span></span> <span data-ttu-id="2fd66-107">Esto aísla al consumidor de todos los demás consumidores criptográficos de la aplicación: ningún otro componente puede leer sus cargas y no puede leer las cargas de cualquier otro componente.</span><span class="sxs-lookup"><span data-stu-id="2fd66-107">This isolates the consumer from all other cryptographic consumers in the application: no other component can read its payloads, and it cannot read any other component's payloads.</span></span> <span data-ttu-id="2fd66-108">Este aislamiento también representa todas las categorías de ataque inviables contra el componente.</span><span class="sxs-lookup"><span data-stu-id="2fd66-108">This isolation also renders infeasible entire categories of attack against the component.</span></span>

![Ejemplo de diagrama de propósito](purpose-strings/_static/purposes.png)

<span data-ttu-id="2fd66-110">En el diagrama anterior, `IDataProtector` las instancias A y B **no pueden** leer las cargas de los demás, solo las suyas propias.</span><span class="sxs-lookup"><span data-stu-id="2fd66-110">In the diagram above, `IDataProtector` instances A and B **cannot** read each other's payloads, only their own.</span></span>

<span data-ttu-id="2fd66-111">La cadena de propósito no tiene que ser secreta.</span><span class="sxs-lookup"><span data-stu-id="2fd66-111">The purpose string doesn't have to be secret.</span></span> <span data-ttu-id="2fd66-112">Simplemente debe ser único en el sentido de que ningún otro componente con el comportamiento correcto deberá proporcionar la misma cadena de propósito.</span><span class="sxs-lookup"><span data-stu-id="2fd66-112">It should simply be unique in the sense that no other well-behaved component will ever provide the same purpose string.</span></span>

>[!TIP]
> <span data-ttu-id="2fd66-113">Usar el espacio de nombres y el nombre de tipo del componente que consume las API de protección de datos es una buena regla general, como en la práctica, esta información nunca entrará en conflicto.</span><span class="sxs-lookup"><span data-stu-id="2fd66-113">Using the namespace and type name of the component consuming the data protection APIs is a good rule of thumb, as in practice this information will never conflict.</span></span>
>
><span data-ttu-id="2fd66-114">Un componente creado por contoso que sea responsable de la creación de tokens de portador puede usar contoso. Security. BearerToken como su cadena de propósito.</span><span class="sxs-lookup"><span data-stu-id="2fd66-114">A Contoso-authored component which is responsible for minting bearer tokens might use Contoso.Security.BearerToken as its purpose string.</span></span> <span data-ttu-id="2fd66-115">O incluso mejor: podría usar contoso. Security. BearerToken. v1 como su cadena de propósito.</span><span class="sxs-lookup"><span data-stu-id="2fd66-115">Or - even better - it might use Contoso.Security.BearerToken.v1 as its purpose string.</span></span> <span data-ttu-id="2fd66-116">Anexar el número de versión permite que una versión futura use contoso. Security. BearerToken. V2 como su finalidad y las distintas versiones estarán completamente aisladas unas de otras en lo que respecta a las cargas.</span><span class="sxs-lookup"><span data-stu-id="2fd66-116">Appending the version number allows a future version to use Contoso.Security.BearerToken.v2 as its purpose, and the different versions would be completely isolated from one another as far as payloads go.</span></span>

<span data-ttu-id="2fd66-117">Dado que el parámetro propósitos de `CreateProtector` es una matriz de cadena, en su lugar se podría especificar como `[ "Contoso.Security.BearerToken", "v1" ]` .</span><span class="sxs-lookup"><span data-stu-id="2fd66-117">Since the purposes parameter to `CreateProtector` is a string array, the above could've been instead specified as `[ "Contoso.Security.BearerToken", "v1" ]`.</span></span> <span data-ttu-id="2fd66-118">Esto permite establecer una jerarquía de propósitos y abre la posibilidad de escenarios de varios inquilinos con el sistema de protección de datos.</span><span class="sxs-lookup"><span data-stu-id="2fd66-118">This allows establishing a hierarchy of purposes and opens up the possibility of multi-tenancy scenarios with the data protection system.</span></span>

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> <span data-ttu-id="2fd66-119">Los componentes no deben permitir que los datos proporcionados por el usuario que no son de confianza sean el único origen de entrada de la cadena de propósitos.</span><span class="sxs-lookup"><span data-stu-id="2fd66-119">Components shouldn't allow untrusted user input to be the sole source of input for the purposes chain.</span></span>
>
><span data-ttu-id="2fd66-120">Por ejemplo, considere un componente contoso. Messaging. SecureMessage, que es responsable de almacenar los mensajes seguros.</span><span class="sxs-lookup"><span data-stu-id="2fd66-120">For example, consider a component Contoso.Messaging.SecureMessage which is responsible for storing secure messages.</span></span> <span data-ttu-id="2fd66-121">Si el componente de mensajería segura tuviera que llamar a `CreateProtector([ username ])` , un usuario malintencionado podría crear una cuenta con el nombre de usuario "contoso. Security. BearerToken" en un intento de obtener el componente al que llamar `CreateProtector([ "Contoso.Security.BearerToken" ])` , lo que provocaría accidentalmente que el sistema de mensajería seguro pudiera ver las cargas que podrían considerarse como tokens de autenticación.</span><span class="sxs-lookup"><span data-stu-id="2fd66-121">If the secure messaging component were to call `CreateProtector([ username ])`, then a malicious user might create an account with username "Contoso.Security.BearerToken" in an attempt to get the component to call `CreateProtector([ "Contoso.Security.BearerToken" ])`, thus inadvertently causing the secure messaging system to mint payloads that could be perceived as authentication tokens.</span></span>
>
><span data-ttu-id="2fd66-122">Una cadena de propósitos mejor para el componente de mensajería sería `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])` , lo que proporciona un aislamiento adecuado.</span><span class="sxs-lookup"><span data-stu-id="2fd66-122">A better purposes chain for the messaging component would be `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`, which provides proper isolation.</span></span>

<span data-ttu-id="2fd66-123">El aislamiento proporcionado por y los comportamientos de `IDataProtectionProvider` , `IDataProtector` y son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="2fd66-123">The isolation provided by and behaviors of `IDataProtectionProvider`, `IDataProtector`, and purposes are as follows:</span></span>

* <span data-ttu-id="2fd66-124">Para un `IDataProtectionProvider` objeto determinado, el `CreateProtector` método creará un `IDataProtector` objeto vinculado de forma única al `IDataProtectionProvider` objeto que lo creó y al parámetro propósitos que se pasó al método.</span><span class="sxs-lookup"><span data-stu-id="2fd66-124">For a given `IDataProtectionProvider` object, the `CreateProtector` method will create an `IDataProtector` object uniquely tied to both the `IDataProtectionProvider` object which created it and the purposes parameter which was passed into the method.</span></span>

* <span data-ttu-id="2fd66-125">El parámetro purpose no debe ser null.</span><span class="sxs-lookup"><span data-stu-id="2fd66-125">The purpose parameter must not be null.</span></span> <span data-ttu-id="2fd66-126">(Si se especifica propósitos como una matriz, esto significa que la matriz no debe tener una longitud de cero y que todos los elementos de la matriz no deben ser null). Técnicamente, se permite un propósito de cadena vacía, pero no se recomienda.</span><span class="sxs-lookup"><span data-stu-id="2fd66-126">(If purposes is specified as an array, this means that the array must not be of zero length and all elements of the array must be non-null.) An empty string purpose is technically allowed but is discouraged.</span></span>

* <span data-ttu-id="2fd66-127">Los argumentos de dos propósitos son equivalentes si y solo si contienen las mismas cadenas (mediante un comparador ordinal) en el mismo orden.</span><span class="sxs-lookup"><span data-stu-id="2fd66-127">Two purposes arguments are equivalent if and only if they contain the same strings (using an ordinal comparer) in the same order.</span></span> <span data-ttu-id="2fd66-128">Un único argumento de propósito es equivalente a la matriz de propósitos de un solo elemento correspondiente.</span><span class="sxs-lookup"><span data-stu-id="2fd66-128">A single purpose argument is equivalent to the corresponding single-element purposes array.</span></span>

* <span data-ttu-id="2fd66-129">Dos `IDataProtector` objetos son equivalentes si y solo si se crean a partir de objetos equivalentes `IDataProtectionProvider` con los mismos parámetros de propósitos equivalentes.</span><span class="sxs-lookup"><span data-stu-id="2fd66-129">Two `IDataProtector` objects are equivalent if and only if they're created from equivalent `IDataProtectionProvider` objects with equivalent purposes parameters.</span></span>

* <span data-ttu-id="2fd66-130">Para un `IDataProtector` objeto determinado, una llamada a `Unprotect(protectedData)` devolverá el original `unprotectedData` si y solo si `protectedData := Protect(unprotectedData)` para un `IDataProtector` objeto equivalente.</span><span class="sxs-lookup"><span data-stu-id="2fd66-130">For a given `IDataProtector` object, a call to `Unprotect(protectedData)` will return the original `unprotectedData` if and only if `protectedData := Protect(unprotectedData)` for an equivalent `IDataProtector` object.</span></span>

> [!NOTE]
> <span data-ttu-id="2fd66-131">No estamos pensando en el caso de que algún componente elija intencionadamente una cadena de propósito que se sabe que entra en conflicto con otro componente.</span><span class="sxs-lookup"><span data-stu-id="2fd66-131">We're not considering the case where some component intentionally chooses a purpose string which is known to conflict with another component.</span></span> <span data-ttu-id="2fd66-132">Este componente se consideraría esencialmente malintencionado y este sistema no está diseñado para proporcionar garantías de seguridad en caso de que el código malintencionado ya se esté ejecutando dentro del proceso de trabajo.</span><span class="sxs-lookup"><span data-stu-id="2fd66-132">Such a component would essentially be considered malicious, and this system isn't intended to provide security guarantees in the event that malicious code is already running inside of the worker process.</span></span>
