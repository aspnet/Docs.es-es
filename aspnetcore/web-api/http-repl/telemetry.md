---
title: Telemetría de HttpRepl
author: scottaddie
description: Obtenga información sobre la telemetría recopilada por el HttpRepl.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/11/2020
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
ms.openlocfilehash: 5ff22753f566c494e51dae67c8c4f6371211be78
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550613"
---
# <a name="httprepl-telemetry"></a><span data-ttu-id="4de6d-103">Telemetría de HttpRepl</span><span class="sxs-lookup"><span data-stu-id="4de6d-103">HttpRepl telemetry</span></span>

<span data-ttu-id="4de6d-104">[HttpRepl](xref:web-api/http-repl) incluye una característica de telemetría que recopila datos de uso.</span><span class="sxs-lookup"><span data-stu-id="4de6d-104">The [HttpRepl](xref:web-api/http-repl) includes a telemetry feature that collects usage data.</span></span> <span data-ttu-id="4de6d-105">Es importante que el equipo de HttpRepl comprenda cómo se usa la herramienta para que se pueda mejorar.</span><span class="sxs-lookup"><span data-stu-id="4de6d-105">It's important that the HttpRepl team understands how the tool is used so it can be improved.</span></span>

## <a name="how-to-opt-out"></a><span data-ttu-id="4de6d-106">Cómo desactivar la característica</span><span class="sxs-lookup"><span data-stu-id="4de6d-106">How to opt out</span></span>

<span data-ttu-id="4de6d-107">La característica de telemetría HttpRepl está habilitada de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="4de6d-107">The HttpRepl telemetry feature is enabled by default.</span></span> <span data-ttu-id="4de6d-108">Para desactivar la característica de telemetría, establezca la variable de entorno `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` en `1` o `true`.</span><span class="sxs-lookup"><span data-stu-id="4de6d-108">To opt out of the telemetry feature, set the `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` environment variable to `1` or `true`.</span></span>

## <a name="disclosure"></a><span data-ttu-id="4de6d-109">Divulgación</span><span class="sxs-lookup"><span data-stu-id="4de6d-109">Disclosure</span></span>

<span data-ttu-id="4de6d-110">El HttpRepl muestra texto similar al siguiente cuando se ejecuta por primera vez la herramienta.</span><span class="sxs-lookup"><span data-stu-id="4de6d-110">The HttpRepl displays text similar to the following when you first run the tool.</span></span> <span data-ttu-id="4de6d-111">El texto puede variar ligeramente en función de la versión de la herramienta que se esté ejecutando.</span><span class="sxs-lookup"><span data-stu-id="4de6d-111">Text may vary slightly depending on the version of the tool you're running.</span></span> <span data-ttu-id="4de6d-112">Esta experiencia de "primera vista" es la forma en que Microsoft le notifica sobre la recopilación de datos.</span><span class="sxs-lookup"><span data-stu-id="4de6d-112">This "first run" experience is how Microsoft notifies you about data collection.</span></span>

```console
Telemetry
---------
The .NET tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

<span data-ttu-id="4de6d-113">Para suprimir el texto de la experiencia "primera ejecución", establezca la `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` variable de entorno en `1` o `true` .</span><span class="sxs-lookup"><span data-stu-id="4de6d-113">To suppress the "first run" experience text, set the `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` environment variable to `1` or `true`.</span></span>

## <a name="data-points"></a><span data-ttu-id="4de6d-114">Puntos de datos</span><span class="sxs-lookup"><span data-stu-id="4de6d-114">Data points</span></span>

<span data-ttu-id="4de6d-115">La característica de telemetría no:</span><span class="sxs-lookup"><span data-stu-id="4de6d-115">The telemetry feature doesn't:</span></span>

* <span data-ttu-id="4de6d-116">Recopile datos personales, como nombres de usuario, direcciones de correo electrónico o direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="4de6d-116">Collect personal data, such as usernames, email addresses, or URLs.</span></span>
* <span data-ttu-id="4de6d-117">Examine las solicitudes o respuestas HTTP.</span><span class="sxs-lookup"><span data-stu-id="4de6d-117">Scan your HTTP requests or responses.</span></span>

<span data-ttu-id="4de6d-118">Los datos se envían de forma segura a los servidores de Microsoft y se conservan con acceso restringido.</span><span class="sxs-lookup"><span data-stu-id="4de6d-118">The data is sent securely to Microsoft servers and held under restricted access.</span></span>

<span data-ttu-id="4de6d-119">La protección de su privacidad es importante para nosotros.</span><span class="sxs-lookup"><span data-stu-id="4de6d-119">Protecting your privacy is important to us.</span></span> <span data-ttu-id="4de6d-120">Si sospecha que la característica de telemetría está recopilando información confidencial o los datos se están administrando de forma no segura o inadecuada, realice una de las siguientes acciones:</span><span class="sxs-lookup"><span data-stu-id="4de6d-120">If you suspect the telemetry feature is collecting sensitive data or the data is being insecurely or inappropriately handled, take one of the following actions:</span></span>

* <span data-ttu-id="4de6d-121">Archivo de un problema en el repositorio [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) .</span><span class="sxs-lookup"><span data-stu-id="4de6d-121">File an issue in the [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) repository.</span></span>
* <span data-ttu-id="4de6d-122">Envíe un correo electrónico a [dotnet@microsoft.com](mailto:dotnet@microsoft.com) para su investigación.</span><span class="sxs-lookup"><span data-stu-id="4de6d-122">Send an email to [dotnet@microsoft.com](mailto:dotnet@microsoft.com) for investigation.</span></span>

<span data-ttu-id="4de6d-123">La característica de telemetría recopila los datos siguientes.</span><span class="sxs-lookup"><span data-stu-id="4de6d-123">The telemetry feature collects the following data.</span></span>

| <span data-ttu-id="4de6d-124">Versiones del SDK de .NET</span><span class="sxs-lookup"><span data-stu-id="4de6d-124">.NET SDK versions</span></span> | <span data-ttu-id="4de6d-125">data</span><span class="sxs-lookup"><span data-stu-id="4de6d-125">Data</span></span> |
|--------------|------|
| <span data-ttu-id="4de6d-126">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="4de6d-126">>=5.0</span></span>        | <span data-ttu-id="4de6d-127">Marca de tiempo de la invocación.</span><span class="sxs-lookup"><span data-stu-id="4de6d-127">Timestamp of invocation.</span></span> |
| <span data-ttu-id="4de6d-128">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="4de6d-128">>=5.0</span></span>        | <span data-ttu-id="4de6d-129">Dirección IP de tres octetos que se usa para determinar la ubicación geográfica.</span><span class="sxs-lookup"><span data-stu-id="4de6d-129">Three-octet IP address used to determine the geographical location.</span></span> |
| <span data-ttu-id="4de6d-130">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="4de6d-130">>=5.0</span></span>        | <span data-ttu-id="4de6d-131">Sistema operativo y versión.</span><span class="sxs-lookup"><span data-stu-id="4de6d-131">Operating system and version.</span></span> |
| <span data-ttu-id="4de6d-132">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="4de6d-132">>=5.0</span></span>        | <span data-ttu-id="4de6d-133">IDENTIFICADOR en tiempo de ejecución (RID) en el que se ejecuta la herramienta.</span><span class="sxs-lookup"><span data-stu-id="4de6d-133">Runtime ID (RID) the tool is running on.</span></span> |
| <span data-ttu-id="4de6d-134">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="4de6d-134">>=5.0</span></span>        | <span data-ttu-id="4de6d-135">Si la herramienta se ejecuta en un contenedor.</span><span class="sxs-lookup"><span data-stu-id="4de6d-135">Whether the tool is running in a container.</span></span> |
| <span data-ttu-id="4de6d-136">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="4de6d-136">>=5.0</span></span>        | <span data-ttu-id="4de6d-137">Dirección de Access Control de medios (MAC) con hash: identificador único y hash criptográficamente (SHA256) de un equipo.</span><span class="sxs-lookup"><span data-stu-id="4de6d-137">Hashed Media Access Control (MAC) address: a cryptographically (SHA256) hashed and unique ID for a machine.</span></span> |
| <span data-ttu-id="4de6d-138">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="4de6d-138">>=5.0</span></span>        | <span data-ttu-id="4de6d-139">Versión de kernel.</span><span class="sxs-lookup"><span data-stu-id="4de6d-139">Kernel version.</span></span> |
| <span data-ttu-id="4de6d-140">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="4de6d-140">>=5.0</span></span>        | <span data-ttu-id="4de6d-141">Versión de HttpRepl.</span><span class="sxs-lookup"><span data-stu-id="4de6d-141">HttpRepl version.</span></span> |
| <span data-ttu-id="4de6d-142">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="4de6d-142">>=5.0</span></span>        | <span data-ttu-id="4de6d-143">Si la herramienta se inició con `help` los `run` argumentos, o `connect` .</span><span class="sxs-lookup"><span data-stu-id="4de6d-143">Whether the tool was started with `help`, `run`, or `connect` arguments.</span></span> <span data-ttu-id="4de6d-144">No se recopilan los valores de argumento reales.</span><span class="sxs-lookup"><span data-stu-id="4de6d-144">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="4de6d-145">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="4de6d-145">>=5.0</span></span>        | <span data-ttu-id="4de6d-146">Comando invocado (por ejemplo, `get` ) y si se ha realizado correctamente.</span><span class="sxs-lookup"><span data-stu-id="4de6d-146">Command invoked (for example, `get`) and whether it succeeded.</span></span> |
| <span data-ttu-id="4de6d-147">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="4de6d-147">>=5.0</span></span>        | <span data-ttu-id="4de6d-148">Para el `connect` comando, si `root` `base` `openapi` se proporcionaron los argumentos, o.</span><span class="sxs-lookup"><span data-stu-id="4de6d-148">For the `connect` command, whether the `root`, `base`, or `openapi` arguments were supplied.</span></span> <span data-ttu-id="4de6d-149">No se recopilan los valores de argumento reales.</span><span class="sxs-lookup"><span data-stu-id="4de6d-149">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="4de6d-150">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="4de6d-150">>=5.0</span></span>        | <span data-ttu-id="4de6d-151">Para el `pref` comando, si `get` `set` se emitió un o y a qué preferencia se obtuvo acceso.</span><span class="sxs-lookup"><span data-stu-id="4de6d-151">For the `pref` command, whether a `get` or `set` was issued and which preference was accessed.</span></span> <span data-ttu-id="4de6d-152">Si no se trata de una preferencia conocida, se aplica un algoritmo hash al nombre.</span><span class="sxs-lookup"><span data-stu-id="4de6d-152">If not a well-known preference, the name is hashed.</span></span> <span data-ttu-id="4de6d-153">El valor no se recopila.</span><span class="sxs-lookup"><span data-stu-id="4de6d-153">The value isn't collected.</span></span> |
| <span data-ttu-id="4de6d-154">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="4de6d-154">>=5.0</span></span>        | <span data-ttu-id="4de6d-155">En el caso del `set header` comando, se establece el nombre del encabezado.</span><span class="sxs-lookup"><span data-stu-id="4de6d-155">For the `set header` command, the header name being set.</span></span> <span data-ttu-id="4de6d-156">Si no es un encabezado conocido, se aplica un algoritmo hash al nombre.</span><span class="sxs-lookup"><span data-stu-id="4de6d-156">If not a well-known header, the name is hashed.</span></span> <span data-ttu-id="4de6d-157">El valor no se recopila.</span><span class="sxs-lookup"><span data-stu-id="4de6d-157">The value isn't collected.</span></span> |
| <span data-ttu-id="4de6d-158">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="4de6d-158">>=5.0</span></span>        | <span data-ttu-id="4de6d-159">Para el `connect` comando, si se usó un caso especial para `dotnet new webapi` y, si se omitió a través de la preferencia.</span><span class="sxs-lookup"><span data-stu-id="4de6d-159">For the `connect` command, whether a special case for `dotnet new webapi` was used and, whether it was bypassed via preference.</span></span> |
| <span data-ttu-id="4de6d-160">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="4de6d-160">>=5.0</span></span>        | <span data-ttu-id="4de6d-161">Para todos los comandos HTTP (por ejemplo, GET, POST, PUT), si se especificó cada una de las opciones.</span><span class="sxs-lookup"><span data-stu-id="4de6d-161">For all HTTP commands (for example, GET, POST, PUT), whether each of the options was specified.</span></span> <span data-ttu-id="4de6d-162">No se recopilan los valores de las opciones.</span><span class="sxs-lookup"><span data-stu-id="4de6d-162">The values of the options aren't collected.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="4de6d-163">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4de6d-163">Additional resources</span></span>

* [<span data-ttu-id="4de6d-164">Telemetría del SDK de .NET Core</span><span class="sxs-lookup"><span data-stu-id="4de6d-164">.NET Core SDK telemetry</span></span>](/dotnet/core/tools/telemetry)
* [<span data-ttu-id="4de6d-165">CLI de .NET Core datos de telemetría</span><span class="sxs-lookup"><span data-stu-id="4de6d-165">.NET Core CLI telemetry data</span></span>](https://dotnet.microsoft.com/platform/telemetry)
