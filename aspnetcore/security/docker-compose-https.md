---
title: Hospedaje de ASP.NET Core imagen en el contenedor mediante Docker Compose con HTTPS
author: ravipal
description: Obtenga información acerca de cómo hospedar imágenes de ASP.NET Core con Docker Compose a través de HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
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
uid: security/docker-compose-https
ms.openlocfilehash: c3b627cdc74f1b40611d84bc3419e678e2dfbba4
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022464"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a><span data-ttu-id="4833c-103">Hospedaje de imágenes de ASP.NET Core con Docker Compose a través de HTTPS</span><span class="sxs-lookup"><span data-stu-id="4833c-103">Hosting ASP.NET Core images with Docker Compose over HTTPS</span></span>


<span data-ttu-id="4833c-104">[De forma predeterminada](/aspnet/core/security/enforcing-ssl)ASP.net Core usa https.</span><span class="sxs-lookup"><span data-stu-id="4833c-104">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="4833c-105">[Https](https://en.wikipedia.org/wiki/HTTPS) se basa en los [certificados](https://en.wikipedia.org/wiki/Public_key_certificate) de confianza, identidad y cifrado.</span><span class="sxs-lookup"><span data-stu-id="4833c-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="4833c-106">En este documento se explica cómo ejecutar imágenes de contenedor creadas previamente con HTTPS.</span><span class="sxs-lookup"><span data-stu-id="4833c-106">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="4833c-107">Consulte [desarrollo de aplicaciones ASP.net Core con Docker a través de https](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) para escenarios de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="4833c-107">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="4833c-108">Este ejemplo requiere [docker 17,06](https://docs.docker.com/release-notes/docker-ce) o posterior del [cliente de Docker](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="4833c-108">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4833c-109">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="4833c-109">Prerequisites</span></span>

<span data-ttu-id="4833c-110">El [SDK de .net Core 2,2](https://dotnet.microsoft.com/download) o posterior es necesario para algunas de las instrucciones de este documento.</span><span class="sxs-lookup"><span data-stu-id="4833c-110">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="4833c-111">Certificados</span><span class="sxs-lookup"><span data-stu-id="4833c-111">Certificates</span></span>

<span data-ttu-id="4833c-112">Se requiere un certificado de una [entidad de certificación](https://wikipedia.org/wiki/Certificate_authority) para el [hospedaje de producción](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) en un dominio.</span><span class="sxs-lookup"><span data-stu-id="4833c-112">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="4833c-113">[Let's Encrypt](https://letsencrypt.org/)es una entidad de certificación que ofrece certificados gratuitos.</span><span class="sxs-lookup"><span data-stu-id="4833c-113">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="4833c-114">En este documento se usan [certificados de desarrollo autofirmados](https://wikipedia.org/wiki/Self-signed_certificate) para hospedar imágenes pregeneradas en `localhost` .</span><span class="sxs-lookup"><span data-stu-id="4833c-114">This document uses [self-signed development certificates](https://wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="4833c-115">Las instrucciones son similares a usar certificados de producción.</span><span class="sxs-lookup"><span data-stu-id="4833c-115">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="4833c-116">Para los certificados de producción:</span><span class="sxs-lookup"><span data-stu-id="4833c-116">For production certificates:</span></span>

* <span data-ttu-id="4833c-117">La `dotnet dev-certs` herramienta no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="4833c-117">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="4833c-118">Los certificados no tienen que almacenarse en la ubicación usada en las instrucciones.</span><span class="sxs-lookup"><span data-stu-id="4833c-118">Certificates don't need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="4833c-119">Almacene los certificados en cualquier ubicación fuera del directorio del sitio.</span><span class="sxs-lookup"><span data-stu-id="4833c-119">Store the certificates in any location outside the site directory.</span></span>

<span data-ttu-id="4833c-120">Las instrucciones contenidas en la sección siguiente desmontan certificados en contenedores mediante la `volumes` propiedad de *Docker-Compose. yml.*</span><span class="sxs-lookup"><span data-stu-id="4833c-120">The instructions contained in the following section volume mount certificates into containers using the `volumes` property in *docker-compose.yml.*</span></span> <span data-ttu-id="4833c-121">Puede agregar certificados a las imágenes de contenedor con un `COPY` comando en un *Dockerfile*, pero no se recomienda.</span><span class="sxs-lookup"><span data-stu-id="4833c-121">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="4833c-122">No se recomienda copiar certificados en una imagen por los siguientes motivos:</span><span class="sxs-lookup"><span data-stu-id="4833c-122">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="4833c-123">Dificulta el uso de la misma imagen para realizar pruebas con certificados de desarrollador.</span><span class="sxs-lookup"><span data-stu-id="4833c-123">It makes it difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="4833c-124">Dificulta el uso de la misma imagen para hospedar con certificados de producción.</span><span class="sxs-lookup"><span data-stu-id="4833c-124">It makes it difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="4833c-125">Existe un riesgo significativo de divulgación de certificados.</span><span class="sxs-lookup"><span data-stu-id="4833c-125">There is significant risk of certificate disclosure.</span></span>

## <a name="starting-a-container-with-https-support-using-docker-compose"></a><span data-ttu-id="4833c-126">Inicio de un contenedor con compatibilidad con https mediante Docker Compose</span><span class="sxs-lookup"><span data-stu-id="4833c-126">Starting a container with https support using docker compose</span></span>

<span data-ttu-id="4833c-127">Utilice las siguientes instrucciones para la configuración del sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="4833c-127">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="4833c-128">Windows con contenedores de Linux</span><span class="sxs-lookup"><span data-stu-id="4833c-128">Windows using Linux containers</span></span>

<span data-ttu-id="4833c-129">Generar certificado y configurar equipo local:</span><span class="sxs-lookup"><span data-stu-id="4833c-129">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="4833c-130">En los comandos anteriores, reemplace `{ password here }` por una contraseña.</span><span class="sxs-lookup"><span data-stu-id="4833c-130">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="4833c-131">Cree un archivo _Docker-Compose. Debug. yml_ con el siguiente contenido:</span><span class="sxs-lookup"><span data-stu-id="4833c-131">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="4833c-132">La contraseña especificada en el archivo de Docker Compose debe coincidir con la contraseña usada para el certificado.</span><span class="sxs-lookup"><span data-stu-id="4833c-132">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="4833c-133">Inicie el contenedor con ASP.NET Core configurado para HTTPS:</span><span class="sxs-lookup"><span data-stu-id="4833c-133">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a><span data-ttu-id="4833c-134">macOS o Linux</span><span class="sxs-lookup"><span data-stu-id="4833c-134">macOS or Linux</span></span>

<span data-ttu-id="4833c-135">Generar certificado y configurar equipo local:</span><span class="sxs-lookup"><span data-stu-id="4833c-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="4833c-136">`dotnet dev-certs https --trust`solo se admite en macOS y Windows.</span><span class="sxs-lookup"><span data-stu-id="4833c-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="4833c-137">Debe confiar en los certificados en Linux de la manera que sea compatible con la distribución.</span><span class="sxs-lookup"><span data-stu-id="4833c-137">You need to trust certificates on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="4833c-138">Es probable que necesite confiar en el certificado en el explorador.</span><span class="sxs-lookup"><span data-stu-id="4833c-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="4833c-139">En los comandos anteriores, reemplace `{ password here }` por una contraseña.</span><span class="sxs-lookup"><span data-stu-id="4833c-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="4833c-140">Cree un archivo _Docker-Compose. Debug. yml_ con el siguiente contenido:</span><span class="sxs-lookup"><span data-stu-id="4833c-140">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="4833c-141">La contraseña especificada en el archivo de Docker Compose debe coincidir con la contraseña usada para el certificado.</span><span class="sxs-lookup"><span data-stu-id="4833c-141">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="4833c-142">Inicie el contenedor con ASP.NET Core configurado para HTTPS:</span><span class="sxs-lookup"><span data-stu-id="4833c-142">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a><span data-ttu-id="4833c-143">Windows con contenedores de Windows</span><span class="sxs-lookup"><span data-stu-id="4833c-143">Windows using Windows containers</span></span>

<span data-ttu-id="4833c-144">Generar certificado y configurar equipo local:</span><span class="sxs-lookup"><span data-stu-id="4833c-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="4833c-145">En los comandos anteriores, reemplace `{ password here }` por una contraseña.</span><span class="sxs-lookup"><span data-stu-id="4833c-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="4833c-146">Cree un archivo _Docker-Compose. Debug. yml_ con el siguiente contenido:</span><span class="sxs-lookup"><span data-stu-id="4833c-146">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=C:\https\aspnetapp.pfx
    volumes:
      - ${USERPROFILE}\.aspnet\https:C:\https:ro
```
<span data-ttu-id="4833c-147">La contraseña especificada en el archivo de Docker Compose debe coincidir con la contraseña usada para el certificado.</span><span class="sxs-lookup"><span data-stu-id="4833c-147">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="4833c-148">Inicie el contenedor con ASP.NET Core configurado para HTTPS:</span><span class="sxs-lookup"><span data-stu-id="4833c-148">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
