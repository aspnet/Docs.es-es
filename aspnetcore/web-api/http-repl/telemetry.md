---
title: Telemetría HTTP REPL
author: scottaddie
description: Obtenga información sobre la telemetría recopilada por el REPL de HTTP.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/10/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 8590959e43c2dda69090acb358e740b271426a44
ms.sourcegitcommit: fb72e9c1ae5b279817f1fb4b46a52170449b6f30
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2020
ms.locfileid: "94502009"
---
# <a name="http-repl-telemetry"></a><span data-ttu-id="96072-103">Telemetría HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="96072-103">HTTP REPL telemetry</span></span>

<span data-ttu-id="96072-104">El [bucle http Read-eval-Print (REPL)](xref:web-api/http-repl) incluye una característica de telemetría que recopila datos de uso.</span><span class="sxs-lookup"><span data-stu-id="96072-104">The [HTTP Read-Eval-Print Loop (REPL)](xref:web-api/http-repl) includes a telemetry feature that collects usage data.</span></span> <span data-ttu-id="96072-105">Es importante que el equipo de REPL de HTTP entienda cómo se usa la herramienta para que se pueda mejorar.</span><span class="sxs-lookup"><span data-stu-id="96072-105">It's important that the HTTP REPL team understands how the tool is used so it can be improved.</span></span>

## <a name="how-to-opt-out"></a><span data-ttu-id="96072-106">Cómo desactivar la característica</span><span class="sxs-lookup"><span data-stu-id="96072-106">How to opt out</span></span>

<span data-ttu-id="96072-107">La característica de telemetría de REPL de HTTP está habilitada de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="96072-107">The HTTP REPL telemetry feature is enabled by default.</span></span> <span data-ttu-id="96072-108">Para desactivar la característica de telemetría, establezca la variable de entorno `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` en `1` o `true`.</span><span class="sxs-lookup"><span data-stu-id="96072-108">To opt out of the telemetry feature, set the `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` environment variable to `1` or `true`.</span></span>

## <a name="disclosure"></a><span data-ttu-id="96072-109">Divulgación</span><span class="sxs-lookup"><span data-stu-id="96072-109">Disclosure</span></span>

<span data-ttu-id="96072-110">HttpRepl muestra texto similar al siguiente cuando se ejecuta por primera vez la herramienta.</span><span class="sxs-lookup"><span data-stu-id="96072-110">HttpRepl displays text similar to the following when you first run the tool.</span></span> <span data-ttu-id="96072-111">El texto puede variar ligeramente en función de la versión de la herramienta que se esté ejecutando.</span><span class="sxs-lookup"><span data-stu-id="96072-111">Text may vary slightly depending on the version of the tool you're running.</span></span> <span data-ttu-id="96072-112">Esta experiencia de "primera vista" es la forma en que Microsoft le notifica sobre la recopilación de datos.</span><span class="sxs-lookup"><span data-stu-id="96072-112">This "first run" experience is how Microsoft notifies you about data collection.</span></span>

```console
Telemetry
---------
The .NET Core tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

## <a name="data-points"></a><span data-ttu-id="96072-113">Puntos de datos</span><span class="sxs-lookup"><span data-stu-id="96072-113">Data points</span></span>

<span data-ttu-id="96072-114">La característica de telemetría no:</span><span class="sxs-lookup"><span data-stu-id="96072-114">The telemetry feature doesn't:</span></span>

* <span data-ttu-id="96072-115">Recopile datos personales, como nombres de usuario, direcciones de correo electrónico o direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="96072-115">Collect personal data, such as usernames, email addresses, or URLs.</span></span>
* <span data-ttu-id="96072-116">Examine las solicitudes o respuestas HTTP.</span><span class="sxs-lookup"><span data-stu-id="96072-116">Scan your HTTP requests or responses.</span></span>

<span data-ttu-id="96072-117">Los datos se envían de forma segura a los servidores de Microsoft y se conservan con acceso restringido.</span><span class="sxs-lookup"><span data-stu-id="96072-117">The data is sent securely to Microsoft servers and held under restricted access.</span></span>

<span data-ttu-id="96072-118">La protección de su privacidad es importante para nosotros.</span><span class="sxs-lookup"><span data-stu-id="96072-118">Protecting your privacy is important to us.</span></span> <span data-ttu-id="96072-119">Si sospecha que la característica de telemetría está recopilando información confidencial o los datos se están administrando de forma no segura o inadecuada, realice una de las siguientes acciones:</span><span class="sxs-lookup"><span data-stu-id="96072-119">If you suspect the telemetry feature is collecting sensitive data or the data is being insecurely or inappropriately handled, take one of the following actions:</span></span>

* <span data-ttu-id="96072-120">Archivo de un problema en el repositorio [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) .</span><span class="sxs-lookup"><span data-stu-id="96072-120">File an issue in the [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) repository.</span></span>
* <span data-ttu-id="96072-121">Envíe un correo electrónico a [dotnet@microsoft.com](mailto:dotnet@microsoft.com) para su investigación.</span><span class="sxs-lookup"><span data-stu-id="96072-121">Send an email to [dotnet@microsoft.com](mailto:dotnet@microsoft.com) for investigation.</span></span>

<span data-ttu-id="96072-122">La característica de telemetría recopila los datos siguientes.</span><span class="sxs-lookup"><span data-stu-id="96072-122">The telemetry feature collects the following data.</span></span>

| <span data-ttu-id="96072-123">Versiones del SDK de .NET</span><span class="sxs-lookup"><span data-stu-id="96072-123">.NET SDK versions</span></span> | <span data-ttu-id="96072-124">data</span><span class="sxs-lookup"><span data-stu-id="96072-124">Data</span></span> |
|--------------|------|
| <span data-ttu-id="96072-125">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="96072-125">>=5.0</span></span>        | <span data-ttu-id="96072-126">Marca de tiempo de la invocación.</span><span class="sxs-lookup"><span data-stu-id="96072-126">Timestamp of invocation.</span></span> |
| <span data-ttu-id="96072-127">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="96072-127">>=5.0</span></span>        | <span data-ttu-id="96072-128">Dirección IP de tres octetos que se usa para determinar la ubicación geográfica.</span><span class="sxs-lookup"><span data-stu-id="96072-128">Three-octet IP address used to determine the geographical location.</span></span> |
| <span data-ttu-id="96072-129">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="96072-129">>=5.0</span></span>        | <span data-ttu-id="96072-130">Sistema operativo y versión.</span><span class="sxs-lookup"><span data-stu-id="96072-130">Operating system and version.</span></span> |
| <span data-ttu-id="96072-131">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="96072-131">>=5.0</span></span>        | <span data-ttu-id="96072-132">IDENTIFICADOR en tiempo de ejecución (RID) en el que se ejecuta la herramienta.</span><span class="sxs-lookup"><span data-stu-id="96072-132">Runtime ID (RID) the tool is running on.</span></span> |
| <span data-ttu-id="96072-133">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="96072-133">>=5.0</span></span>        | <span data-ttu-id="96072-134">Si la herramienta se ejecuta en un contenedor.</span><span class="sxs-lookup"><span data-stu-id="96072-134">Whether the tool is running in a container.</span></span> |
| <span data-ttu-id="96072-135">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="96072-135">>=5.0</span></span>        | <span data-ttu-id="96072-136">Dirección de Access Control de medios (MAC) con hash: identificador único y hash criptográficamente (SHA256) de un equipo.</span><span class="sxs-lookup"><span data-stu-id="96072-136">Hashed Media Access Control (MAC) address: a cryptographically (SHA256) hashed and unique ID for a machine.</span></span> |
| <span data-ttu-id="96072-137">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="96072-137">>=5.0</span></span>        | <span data-ttu-id="96072-138">Versión de kernel.</span><span class="sxs-lookup"><span data-stu-id="96072-138">Kernel version.</span></span> |
| <span data-ttu-id="96072-139">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="96072-139">>=5.0</span></span>        | <span data-ttu-id="96072-140">Versión de REPL de HTTP.</span><span class="sxs-lookup"><span data-stu-id="96072-140">HTTP REPL version.</span></span> |
| <span data-ttu-id="96072-141">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="96072-141">>=5.0</span></span>        | <span data-ttu-id="96072-142">Si la herramienta se inició con `help` los `run` argumentos, o `connect` .</span><span class="sxs-lookup"><span data-stu-id="96072-142">Whether the tool was started with `help`, `run`, or `connect` arguments.</span></span> <span data-ttu-id="96072-143">No se recopilan los valores de argumento reales.</span><span class="sxs-lookup"><span data-stu-id="96072-143">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="96072-144">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="96072-144">>=5.0</span></span>        | <span data-ttu-id="96072-145">Comando invocado (por ejemplo, `get` ) y si se ha realizado correctamente.</span><span class="sxs-lookup"><span data-stu-id="96072-145">Command invoked (for example, `get`) and whether it succeeded.</span></span> |
| <span data-ttu-id="96072-146">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="96072-146">>=5.0</span></span>        | <span data-ttu-id="96072-147">Para el `connect` comando, si `root` `base` `openapi` se proporcionaron los argumentos, o.</span><span class="sxs-lookup"><span data-stu-id="96072-147">For the `connect` command, whether the `root`, `base`, or `openapi` arguments were supplied.</span></span> <span data-ttu-id="96072-148">No se recopilan los valores de argumento reales.</span><span class="sxs-lookup"><span data-stu-id="96072-148">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="96072-149">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="96072-149">>=5.0</span></span>        | <span data-ttu-id="96072-150">Para el `pref` comando, si `get` `set` se emitió un o y a qué preferencia se obtuvo acceso.</span><span class="sxs-lookup"><span data-stu-id="96072-150">For the `pref` command, whether a `get` or `set` was issued and which preference was accessed.</span></span> <span data-ttu-id="96072-151">Si no se trata de una preferencia conocida, se aplica un algoritmo hash al nombre.</span><span class="sxs-lookup"><span data-stu-id="96072-151">If not a well-known preference, the name is hashed.</span></span> <span data-ttu-id="96072-152">El valor no se recopila.</span><span class="sxs-lookup"><span data-stu-id="96072-152">The value isn't collected.</span></span> |
| <span data-ttu-id="96072-153">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="96072-153">>=5.0</span></span>        | <span data-ttu-id="96072-154">En el caso del `set header` comando, se establece el nombre del encabezado.</span><span class="sxs-lookup"><span data-stu-id="96072-154">For the `set header` command, the header name being set.</span></span> <span data-ttu-id="96072-155">Si no es un encabezado conocido, se aplica un algoritmo hash al nombre.</span><span class="sxs-lookup"><span data-stu-id="96072-155">If not a well-known header, the name is hashed.</span></span> <span data-ttu-id="96072-156">El valor no se recopila.</span><span class="sxs-lookup"><span data-stu-id="96072-156">The value isn't collected.</span></span> |
| <span data-ttu-id="96072-157">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="96072-157">>=5.0</span></span>        | <span data-ttu-id="96072-158">Para el `connect` comando, si se usó un caso especial para `dotnet new webapi` y, si se omitió a través de la preferencia.</span><span class="sxs-lookup"><span data-stu-id="96072-158">For the `connect` command, whether a special case for `dotnet new webapi` was used and, whether it was bypassed via preference.</span></span> |
| <span data-ttu-id="96072-159">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="96072-159">>=5.0</span></span>        | <span data-ttu-id="96072-160">Para todos los comandos HTTP (por ejemplo, GET, POST, PUT), si se especificó cada una de las opciones.</span><span class="sxs-lookup"><span data-stu-id="96072-160">For all HTTP commands (for example, GET, POST, PUT), whether each of the options was specified.</span></span> <span data-ttu-id="96072-161">No se recopilan los valores de las opciones.</span><span class="sxs-lookup"><span data-stu-id="96072-161">The values of the options aren't collected.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="96072-162">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="96072-162">Additional resources</span></span>

* [<span data-ttu-id="96072-163">Telemetría del SDK de .NET Core</span><span class="sxs-lookup"><span data-stu-id="96072-163">.NET Core SDK telemetry</span></span>](/dotnet/core/tools/telemetry)
* [<span data-ttu-id="96072-164">CLI de .NET Core datos de telemetría</span><span class="sxs-lookup"><span data-stu-id="96072-164">.NET Core CLI telemetry data</span></span>](https://dotnet.microsoft.com/platform/telemetry)
