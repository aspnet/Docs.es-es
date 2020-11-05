---
title: Imágenes de Docker para ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo usar las imágenes de Docker de .NET Core publicadas desde el registro de Docker. Extraiga imágenes y cree las suyas propias.
ms.author: riande
ms.custom: mvc
ms.date: 05/12/2020
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
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 81daa1d4a996519f44e513b4f61c27cdf2b6ef5e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059811"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="80354-104">Imágenes de Docker para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="80354-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="80354-105">En este tutorial se muestra cómo ejecutar una aplicación ASP.NET Core en contenedores de Docker.</span><span class="sxs-lookup"><span data-stu-id="80354-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="80354-106">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="80354-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="80354-107">Obtener información sobre las imágenes de Microsoft .NET Core Docker</span><span class="sxs-lookup"><span data-stu-id="80354-107">Learn about Microsoft .NET Core Docker images</span></span>
> * <span data-ttu-id="80354-108">Descargará una aplicación de ejemplo de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="80354-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="80354-109">Ejecutará la aplicación de ejemplo localmente</span><span class="sxs-lookup"><span data-stu-id="80354-109">Run the sample app locally</span></span>
> * <span data-ttu-id="80354-110">Ejecutará la aplicación de ejemplo en contenedores de Linux</span><span class="sxs-lookup"><span data-stu-id="80354-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="80354-111">Ejecutará la aplicación de ejemplo en contenedores de Windows</span><span class="sxs-lookup"><span data-stu-id="80354-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="80354-112">Realizará compilaciones e implementaciones manualmente</span><span class="sxs-lookup"><span data-stu-id="80354-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="80354-113">Imágenes de Docker para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="80354-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="80354-114">En este tutorial, descargará una aplicación de ejemplo de ASP.NET Core y la ejecutará en contenedores de Docker.</span><span class="sxs-lookup"><span data-stu-id="80354-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="80354-115">El ejemplo funciona con contenedores de Linux y Windows.</span><span class="sxs-lookup"><span data-stu-id="80354-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="80354-116">El Dockerfile de ejemplo usa la [característica de compilación en varias fases de Docker](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) para la compilación y ejecución en distintos contenedores.</span><span class="sxs-lookup"><span data-stu-id="80354-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="80354-117">Los contenedores de compilación y ejecución se crean a partir de imágenes que proporciona Microsoft en Docker Hub:</span><span class="sxs-lookup"><span data-stu-id="80354-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

* `dotnet/core/sdk`

  <span data-ttu-id="80354-118">En el ejemplo se usa esta imagen para compilar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="80354-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="80354-119">La imagen contiene el SDK de .NET Core, que incluye las herramientas de línea de comandos (CLI).</span><span class="sxs-lookup"><span data-stu-id="80354-119">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="80354-120">Esta imagen está optimizada para el desarrollo local, la depuración y las pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="80354-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="80354-121">Las herramientas instaladas para el desarrollo y la compilación hacen que esta sea una imagen relativamente grande.</span><span class="sxs-lookup"><span data-stu-id="80354-121">The tools installed for development and compilation make this a relatively large image.</span></span> 

* `dotnet/core/aspnet`

   <span data-ttu-id="80354-122">En el ejemplo se usa esta imagen para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="80354-122">The sample uses this image for running the app.</span></span> <span data-ttu-id="80354-123">La imagen contiene el entorno de ejecución y las bibliotecas de ASP.NET Core y está optimizada para la ejecución de aplicaciones en producción.</span><span class="sxs-lookup"><span data-stu-id="80354-123">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="80354-124">Diseñada para acelerar la implementación y el inicio de las aplicaciones, la imagen es relativamente pequeña, de forma que se optimiza el rendimiento de la red desde Docker Registry hasta el host de Docker.</span><span class="sxs-lookup"><span data-stu-id="80354-124">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="80354-125">Solo se copian en el contenedor los archivos binarios y el contenido necesario para ejecutar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="80354-125">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="80354-126">El contenido está listo para ejecutarse, lo que permite el tiempo más rápido desde `Docker run` hasta el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="80354-126">The contents are ready to run, enabling the fastest time from `Docker run` to app startup.</span></span> <span data-ttu-id="80354-127">En el modelo de Docker no se necesita compilación de código dinámico.</span><span class="sxs-lookup"><span data-stu-id="80354-127">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="80354-128">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="80354-128">Prerequisites</span></span>
::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="80354-129">SDK de .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="80354-129">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="80354-130">SDK de .NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="80354-130">.NET Core SDK 3.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

* <span data-ttu-id="80354-131">Cliente de Docker 18.03 o posterior</span><span class="sxs-lookup"><span data-stu-id="80354-131">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="80354-132">Distribuciones de Linux</span><span class="sxs-lookup"><span data-stu-id="80354-132">Linux distributions</span></span>
    * [<span data-ttu-id="80354-133">CentOS</span><span class="sxs-lookup"><span data-stu-id="80354-133">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="80354-134">Debian</span><span class="sxs-lookup"><span data-stu-id="80354-134">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="80354-135">Fedora</span><span class="sxs-lookup"><span data-stu-id="80354-135">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="80354-136">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="80354-136">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="80354-137">macOS</span><span class="sxs-lookup"><span data-stu-id="80354-137">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="80354-138">Windows</span><span class="sxs-lookup"><span data-stu-id="80354-138">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="80354-139">Git</span><span class="sxs-lookup"><span data-stu-id="80354-139">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="80354-140">Descarga de la aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="80354-140">Download the sample app</span></span>

* <span data-ttu-id="80354-141">Para descargar el ejemplo, clone el [repositorio de Docker de .NET Core](https://github.com/dotnet/dotnet-docker):</span><span class="sxs-lookup"><span data-stu-id="80354-141">Download the sample by cloning the [.NET Core Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="80354-142">Probar la aplicación localmente</span><span class="sxs-lookup"><span data-stu-id="80354-142">Run the app locally</span></span>

* <span data-ttu-id="80354-143">Vaya a la carpeta de proyecto en *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="80354-143">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="80354-144">Ejecute el siguiente comando para compilar y ejecutar localmente la aplicación:</span><span class="sxs-lookup"><span data-stu-id="80354-144">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="80354-145">Vaya a `http://localhost:5000` en un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="80354-145">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="80354-146">Presione Ctrl+C en el símbolo del sistema para detener la aplicación.</span><span class="sxs-lookup"><span data-stu-id="80354-146">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="80354-147">Ejecución en un contenedor de Linux</span><span class="sxs-lookup"><span data-stu-id="80354-147">Run in a Linux container</span></span>

* <span data-ttu-id="80354-148">En el cliente de Docker, [cambie a contenedores de Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span><span class="sxs-lookup"><span data-stu-id="80354-148">In the Docker client, [switch to Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

* <span data-ttu-id="80354-149">Vaya a la carpeta de Dockerfile en *dotnet-docker/samples/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="80354-149">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="80354-150">Ejecute los siguientes comandos para compilar y ejecutar el ejemplo en Docker:</span><span class="sxs-lookup"><span data-stu-id="80354-150">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="80354-151">Los argumentos del comando `build`:</span><span class="sxs-lookup"><span data-stu-id="80354-151">The `build` command arguments:</span></span>
  * <span data-ttu-id="80354-152">Asigne a la imagen el nombre aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="80354-152">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="80354-153">Busque el archivo Dockerfile en la carpeta actual (el punto al final).</span><span class="sxs-lookup"><span data-stu-id="80354-153">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="80354-154">Los argumentos del comando de ejecución:</span><span class="sxs-lookup"><span data-stu-id="80354-154">The run command arguments:</span></span>
  * <span data-ttu-id="80354-155">Asigne un pseudo-TTY y manténgalo abierto aunque no esté asociado.</span><span class="sxs-lookup"><span data-stu-id="80354-155">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="80354-156">(El mismo efecto que `--interactive --tty`).</span><span class="sxs-lookup"><span data-stu-id="80354-156">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="80354-157">Quite automáticamente el contenedor cuando se cierre.</span><span class="sxs-lookup"><span data-stu-id="80354-157">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="80354-158">Asigne al puerto 5000 de la máquina local el puerto 80 en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="80354-158">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="80354-159">Asigne al contenedor el nombre aspnetcore_sample.</span><span class="sxs-lookup"><span data-stu-id="80354-159">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="80354-160">Especifique la imagen aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="80354-160">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="80354-161">Vaya a `http://localhost:5000` en un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="80354-161">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="80354-162">Ejecución en un contenedor de Windows</span><span class="sxs-lookup"><span data-stu-id="80354-162">Run in a Windows container</span></span>

* <span data-ttu-id="80354-163">En el cliente de Docker, [cambie a los contenedores de Windows](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span><span class="sxs-lookup"><span data-stu-id="80354-163">In the Docker client, [switch to Windows containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

<span data-ttu-id="80354-164">Vaya a la carpeta de archivos de Docker en `dotnet-docker/samples/aspnetapp`.</span><span class="sxs-lookup"><span data-stu-id="80354-164">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="80354-165">Ejecute los siguientes comandos para compilar y ejecutar el ejemplo en Docker:</span><span class="sxs-lookup"><span data-stu-id="80354-165">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="80354-166">Para contenedores de Windows, necesita la dirección IP del contenedor (no sirve ir a `http://localhost:5000`):</span><span class="sxs-lookup"><span data-stu-id="80354-166">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="80354-167">Abra otro símbolo del sistema.</span><span class="sxs-lookup"><span data-stu-id="80354-167">Open up another command prompt.</span></span>
  * <span data-ttu-id="80354-168">Ejecute `docker ps` para ver los contenedores en ejecución.</span><span class="sxs-lookup"><span data-stu-id="80354-168">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="80354-169">Compruebe que aparece el contenedor "aspnetcore_sample".</span><span class="sxs-lookup"><span data-stu-id="80354-169">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="80354-170">Ejecute `docker exec aspnetcore_sample ipconfig` para mostrar la dirección IP del contenedor.</span><span class="sxs-lookup"><span data-stu-id="80354-170">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="80354-171">La salida del comando es similar a este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="80354-171">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="80354-172">Copie la dirección IPv4 (por ejemplo, 172.29.245.43) del contenedor y péguela en la barra de direcciones del explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="80354-172">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="80354-173">Compilaciones e implementaciones manuales</span><span class="sxs-lookup"><span data-stu-id="80354-173">Build and deploy manually</span></span>

<span data-ttu-id="80354-174">En algunos escenarios, puede que quiera implementar una aplicación en un contenedor mediante la copia de los archivos de aplicación que son necesarios en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="80354-174">In some scenarios, you might want to deploy an app to a container by copying to it the application files that are needed at run time.</span></span> <span data-ttu-id="80354-175">En esta sección se muestra cómo realizar una implementación manual.</span><span class="sxs-lookup"><span data-stu-id="80354-175">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="80354-176">Vaya a la carpeta de proyecto en *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="80354-176">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="80354-177">Ejecute el comando [dotnet publish](/dotnet/core/tools/dotnet-publish):</span><span class="sxs-lookup"><span data-stu-id="80354-177">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="80354-178">Los argumentos del comando:</span><span class="sxs-lookup"><span data-stu-id="80354-178">The command arguments:</span></span>
  * <span data-ttu-id="80354-179">Compile la aplicación en modo de versión (el valor predeterminado es modo de depuración).</span><span class="sxs-lookup"><span data-stu-id="80354-179">Build the application in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="80354-180">Cree los archivos en la carpeta *publicada*.</span><span class="sxs-lookup"><span data-stu-id="80354-180">Create the files in the *published* folder.</span></span>

* <span data-ttu-id="80354-181">Ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="80354-181">Run the application.</span></span>

  * <span data-ttu-id="80354-182">Windows:</span><span class="sxs-lookup"><span data-stu-id="80354-182">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="80354-183">Linux:</span><span class="sxs-lookup"><span data-stu-id="80354-183">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="80354-184">Vaya a `http://localhost:5000` para ver la página principal.</span><span class="sxs-lookup"><span data-stu-id="80354-184">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="80354-185">Para usar la aplicación publicada manualmente en un contenedor de Docker, cree un nuevo Dockerfile y use el comando `docker build .` para compilar el contenedor.</span><span class="sxs-lookup"><span data-stu-id="80354-185">To use the manually published application within a Docker container, create a new Dockerfile and use the `docker build .` command to build the container.</span></span>

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="80354-186">El archivo Dockerfile</span><span class="sxs-lookup"><span data-stu-id="80354-186">The Dockerfile</span></span>

<span data-ttu-id="80354-187">Aquí el archivo *Dockerfile* se usa con el comando `docker build` que ejecutó anteriormente.</span><span class="sxs-lookup"><span data-stu-id="80354-187">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="80354-188">Se usa `dotnet publish` de la misma manera que en esta sección para realizar compilaciones e implementaciones.</span><span class="sxs-lookup"><span data-stu-id="80354-188">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="80354-189">El archivo Dockerfile</span><span class="sxs-lookup"><span data-stu-id="80354-189">The Dockerfile</span></span>

<span data-ttu-id="80354-190">Aquí el archivo *Dockerfile* se usa con el comando `docker build` que ejecutó anteriormente.</span><span class="sxs-lookup"><span data-stu-id="80354-190">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="80354-191">Se usa `dotnet publish` de la misma manera que en esta sección para realizar compilaciones e implementaciones.</span><span class="sxs-lookup"><span data-stu-id="80354-191">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.0 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="80354-192">Como se indicó en el archivo Dockerfile anterior, los archivos `*.csproj` se copian y se restauran como *capas* distintas.</span><span class="sxs-lookup"><span data-stu-id="80354-192">As noted in the preceding Dockerfile, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="80354-193">Cuando el comando `docker build` compila una imagen, usa una caché integrada.</span><span class="sxs-lookup"><span data-stu-id="80354-193">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="80354-194">Si los archivos `*.csproj` no han cambiado desde la última vez que se ejecutó el comando `docker build`, no es necesario volver a ejecutar el comando `dotnet restore`.</span><span class="sxs-lookup"><span data-stu-id="80354-194">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="80354-195">En su lugar, se vuelve a usar la caché integrada para la capa `dotnet restore` correspondiente.</span><span class="sxs-lookup"><span data-stu-id="80354-195">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="80354-196">Para obtener más información, vea [Procedimientos recomendados para escribir archivos Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span><span class="sxs-lookup"><span data-stu-id="80354-196">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="80354-197">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="80354-197">Additional resources</span></span>

* [<span data-ttu-id="80354-198">Comando de compilación de Docker</span><span class="sxs-lookup"><span data-stu-id="80354-198">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="80354-199">Comando de ejecución de Docker</span><span class="sxs-lookup"><span data-stu-id="80354-199">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="80354-200">[Ejemplo de Docker de ASP.NET Core](https://github.com/dotnet/dotnet-docker) (el usado en este tutorial).</span><span class="sxs-lookup"><span data-stu-id="80354-200">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="80354-201">Configurar ASP.NET Core para trabajar con servidores proxy y equilibradores de carga</span><span class="sxs-lookup"><span data-stu-id="80354-201">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](../proxy-load-balancer.md)
* <span data-ttu-id="80354-202">[Working with Visual Studio Docker Tools](./visual-studio-tools-for-docker.md) (Trabajo con Visual Studio Docker Tools)</span><span class="sxs-lookup"><span data-stu-id="80354-202">[Working with Visual Studio Docker Tools](./visual-studio-tools-for-docker.md)</span></span>
* [<span data-ttu-id="80354-203">Depuración con Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="80354-203">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [<span data-ttu-id="80354-204">GC mediante Docker y contenedores pequeños</span><span class="sxs-lookup"><span data-stu-id="80354-204">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

## <a name="next-steps"></a><span data-ttu-id="80354-205">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="80354-205">Next steps</span></span>

<span data-ttu-id="80354-206">El repositorio de Git que contiene la aplicación de ejemplo también incluye documentación.</span><span class="sxs-lookup"><span data-stu-id="80354-206">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="80354-207">Para información general de los recursos disponibles en el repositorio, consulte [el archivo Léame](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span><span class="sxs-lookup"><span data-stu-id="80354-207">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="80354-208">En concreto, vea cómo implementar HTTPS:</span><span class="sxs-lookup"><span data-stu-id="80354-208">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="80354-209">[Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) (Desarrollo de aplicaciones ASP.NET Core con Docker a través de HTTPS)</span><span class="sxs-lookup"><span data-stu-id="80354-209">[Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)</span></span>
