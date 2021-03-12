---
title: Hospedaje de imágenes de ASP.NET Core con Docker a través de HTTPS
author: rick-anderson
description: Aprenda a hospedar imágenes de ASP.NET Core con Docker a través de HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
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
uid: security/docker-https
ms.openlocfilehash: 3af2aff477604eb19ac211753f848d08d0c67c72
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588645"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="32bf4-103">Hospedaje de imágenes de ASP.NET Core con Docker a través de HTTPS</span><span class="sxs-lookup"><span data-stu-id="32bf4-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="32bf4-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="32bf4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="32bf4-105">[De forma predeterminada](./enforcing-ssl.md)ASP.net Core usa https.</span><span class="sxs-lookup"><span data-stu-id="32bf4-105">ASP.NET Core uses [HTTPS by default](./enforcing-ssl.md).</span></span> <span data-ttu-id="32bf4-106">[Https](https://en.wikipedia.org/wiki/HTTPS) se basa en los [certificados](https://en.wikipedia.org/wiki/Public_key_certificate) de confianza, identidad y cifrado.</span><span class="sxs-lookup"><span data-stu-id="32bf4-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="32bf4-107">En este documento se explica cómo ejecutar imágenes de contenedor creadas previamente con HTTPS.</span><span class="sxs-lookup"><span data-stu-id="32bf4-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="32bf4-108">Consulte [desarrollo de aplicaciones ASP.net Core con Docker a través de https](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md) para escenarios de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="32bf4-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="32bf4-109">En este ejemplo se necesita la versión [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) o posterior del [cliente Docker](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="32bf4-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="32bf4-110">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="32bf4-110">Prerequisites</span></span>

<span data-ttu-id="32bf4-111">El [SDK de .net Core 2,2](https://dotnet.microsoft.com/download) o posterior es necesario para algunas de las instrucciones de este documento.</span><span class="sxs-lookup"><span data-stu-id="32bf4-111">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="32bf4-112">Certificados</span><span class="sxs-lookup"><span data-stu-id="32bf4-112">Certificates</span></span>

<span data-ttu-id="32bf4-113">Se requiere un certificado de una [entidad de certificación](https://wikipedia.org/wiki/Certificate_authority) para el [hospedaje de producción](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) en un dominio.</span><span class="sxs-lookup"><span data-stu-id="32bf4-113">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="32bf4-114">[Let's Encrypt](https://letsencrypt.org/) es una entidad de certificación que ofrece certificados gratuitos.</span><span class="sxs-lookup"><span data-stu-id="32bf4-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="32bf4-115">En este documento se usan [certificados de desarrollo autofirmados](https://en.wikipedia.org/wiki/Self-signed_certificate) para hospedar imágenes pregeneradas en `localhost` .</span><span class="sxs-lookup"><span data-stu-id="32bf4-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="32bf4-116">Las instrucciones son similares a usar certificados de producción.</span><span class="sxs-lookup"><span data-stu-id="32bf4-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="32bf4-117">Use [dotnet dev-certs](/dotnet/core/additional-tools/self-signed-certificates-guide) para crear certificados autofirmados para desarrollo y pruebas.</span><span class="sxs-lookup"><span data-stu-id="32bf4-117">Use [dotnet dev-certs](/dotnet/core/additional-tools/self-signed-certificates-guide) to create self-signed certificates for development and testing.</span></span>

<span data-ttu-id="32bf4-118">Para los certificados de producción:</span><span class="sxs-lookup"><span data-stu-id="32bf4-118">For production certs:</span></span>

* <span data-ttu-id="32bf4-119">La `dotnet dev-certs` herramienta no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="32bf4-119">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="32bf4-120">No es necesario almacenar los certificados en la ubicación usada en las instrucciones.</span><span class="sxs-lookup"><span data-stu-id="32bf4-120">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="32bf4-121">Cualquier ubicación debería funcionar, aunque no se recomienda almacenar certificados en el directorio del sitio.</span><span class="sxs-lookup"><span data-stu-id="32bf4-121">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="32bf4-122">Las instrucciones contenidas en la sección siguiente desmontan certificados en contenedores mediante la `-v` opción de línea de comandos de Docker.</span><span class="sxs-lookup"><span data-stu-id="32bf4-122">The instructions contained in the following section volume mount certificates into containers using Docker's `-v` command-line option.</span></span> <span data-ttu-id="32bf4-123">Puede agregar certificados a las imágenes de contenedor con un `COPY` comando en un *Dockerfile*, pero no se recomienda.</span><span class="sxs-lookup"><span data-stu-id="32bf4-123">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="32bf4-124">No se recomienda copiar certificados en una imagen por los siguientes motivos:</span><span class="sxs-lookup"><span data-stu-id="32bf4-124">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="32bf4-125">Dificulta el uso de la misma imagen para realizar pruebas con certificados de desarrollador.</span><span class="sxs-lookup"><span data-stu-id="32bf4-125">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="32bf4-126">Dificulta el uso de la misma imagen para hospedar con certificados de producción.</span><span class="sxs-lookup"><span data-stu-id="32bf4-126">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="32bf4-127">Existe un riesgo significativo de divulgación de certificados.</span><span class="sxs-lookup"><span data-stu-id="32bf4-127">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="32bf4-128">Ejecución de imágenes de contenedor pregeneradas con HTTPS</span><span class="sxs-lookup"><span data-stu-id="32bf4-128">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="32bf4-129">Utilice las siguientes instrucciones para la configuración del sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="32bf4-129">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="32bf4-130">Windows con contenedores de Linux</span><span class="sxs-lookup"><span data-stu-id="32bf4-130">Windows using Linux containers</span></span>

<span data-ttu-id="32bf4-131">Generar certificado y configurar equipo local:</span><span class="sxs-lookup"><span data-stu-id="32bf4-131">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="32bf4-132">En los comandos anteriores, reemplace `{ password here }` por una contraseña.</span><span class="sxs-lookup"><span data-stu-id="32bf4-132">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="32bf4-133">Ejecute la imagen de contenedor con ASP.NET Core configurado para HTTPS en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="32bf4-133">Run the container image with ASP.NET Core configured for HTTPS in a command shell:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="32bf4-134">Al usar [PowerShell](/powershell/scripting/overview), reemplace `%USERPROFILE%` por `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="32bf4-134">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="32bf4-135">La contraseña debe coincidir con la contraseña usada para el certificado.</span><span class="sxs-lookup"><span data-stu-id="32bf4-135">The password must match the password used for the certificate.</span></span>


<span data-ttu-id="32bf4-136">Nota: en este caso, el certificado debe ser un `.pfx` archivo.</span><span class="sxs-lookup"><span data-stu-id="32bf4-136">Note: The certificate in this case must be a `.pfx` file.</span></span>  <span data-ttu-id="32bf4-137">El uso de `.crt` un `.key` archivo o con o sin la contraseña no es compatible con el contenedor de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="32bf4-137">Utilizing a `.crt` or `.key` file with or without the password isn't supported with the sample container.</span></span>  <span data-ttu-id="32bf4-138">Por ejemplo, al especificar un `.crt` archivo, el contenedor puede devolver mensajes de error como "el modo de servidor SSL debe usar un certificado con la clave privada asociada".</span><span class="sxs-lookup"><span data-stu-id="32bf4-138">For example, when specifying a `.crt` file, the container may return error messages such as 'The server mode SSL must use a certificate with the associated private key.'.</span></span> <span data-ttu-id="32bf4-139">Al usar [WSL](/windows/wsl/about), valide la ruta de acceso de montaje para asegurarse de que el certificado se carga correctamente.</span><span class="sxs-lookup"><span data-stu-id="32bf4-139">When using [WSL](/windows/wsl/about), validate the mount path to ensure that the certificate loads correctly.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="32bf4-140">macOS o Linux</span><span class="sxs-lookup"><span data-stu-id="32bf4-140">macOS or Linux</span></span>

<span data-ttu-id="32bf4-141">Generar certificado y configurar equipo local:</span><span class="sxs-lookup"><span data-stu-id="32bf4-141">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="32bf4-142">`dotnet dev-certs https --trust` solo se admite en macOS y Windows.</span><span class="sxs-lookup"><span data-stu-id="32bf4-142">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="32bf4-143">Debe confiar en los certificados en Linux de la manera que sea compatible con la distribución.</span><span class="sxs-lookup"><span data-stu-id="32bf4-143">You need to trust certs on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="32bf4-144">Es probable que necesite confiar en el certificado en el explorador.</span><span class="sxs-lookup"><span data-stu-id="32bf4-144">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="32bf4-145">En los comandos anteriores, reemplace `{ password here }` por una contraseña.</span><span class="sxs-lookup"><span data-stu-id="32bf4-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="32bf4-146">Ejecute la imagen de contenedor con ASP.NET Core configurado para HTTPS:</span><span class="sxs-lookup"><span data-stu-id="32bf4-146">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="32bf4-147">La contraseña debe coincidir con la contraseña usada para el certificado.</span><span class="sxs-lookup"><span data-stu-id="32bf4-147">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="32bf4-148">Windows con contenedores de Windows</span><span class="sxs-lookup"><span data-stu-id="32bf4-148">Windows using Windows containers</span></span>

<span data-ttu-id="32bf4-149">Generar certificado y configurar equipo local:</span><span class="sxs-lookup"><span data-stu-id="32bf4-149">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="32bf4-150">En los comandos anteriores, reemplace `{ password here }` por una contraseña.</span><span class="sxs-lookup"><span data-stu-id="32bf4-150">In the preceding commands, replace `{ password here }` with a password.</span></span> <span data-ttu-id="32bf4-151">Al usar [PowerShell](/powershell/scripting/overview), reemplace `%USERPROFILE%` por `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="32bf4-151">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="32bf4-152">Ejecute la imagen de contenedor con ASP.NET Core configurado para HTTPS:</span><span class="sxs-lookup"><span data-stu-id="32bf4-152">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="32bf4-153">La contraseña debe coincidir con la contraseña usada para el certificado.</span><span class="sxs-lookup"><span data-stu-id="32bf4-153">The password must match the password used for the certificate.</span></span> <span data-ttu-id="32bf4-154">Al usar [PowerShell](/powershell/scripting/overview), reemplace `%USERPROFILE%` por `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="32bf4-154">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>
