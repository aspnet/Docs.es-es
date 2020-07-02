---
title: Herramientas de contenedor de Visual Studio con ASP.NET Core
author: spboyer
description: Obtenga información sobre cómo usar las herramientas de Visual Studio y Docker para Windows para incluir en un contenedor una aplicación de ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: 2b0b1a869f41bcaed59d9bed89f7fcdb58d0f6c1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407738"
---
# <a name="visual-studio-container-tools-with-aspnet-core"></a><span data-ttu-id="a4e27-103">Herramientas de contenedor de Visual Studio con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a4e27-103">Visual Studio Container Tools with ASP.NET Core</span></span>

<span data-ttu-id="a4e27-104">En Visual Studio 2017 y versiones posteriores, se pueden compilar, depurar y ejecutar aplicaciones ASP.NET Core incluidas en un contenedor para .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a4e27-104">Visual Studio 2017 and later versions support building, debugging, and running containerized ASP.NET Core apps targeting .NET Core.</span></span> <span data-ttu-id="a4e27-105">Se admiten contenedores de Windows y Linux.</span><span class="sxs-lookup"><span data-stu-id="a4e27-105">Both Windows and Linux containers are supported.</span></span>

<span data-ttu-id="a4e27-106">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a4e27-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a4e27-107">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="a4e27-107">Prerequisites</span></span>

* [<span data-ttu-id="a4e27-108">Docker para Windows</span><span class="sxs-lookup"><span data-stu-id="a4e27-108">Docker for Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)
* <span data-ttu-id="a4e27-109">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **Desarrollo multiplataforma de .NET Core**</span><span class="sxs-lookup"><span data-stu-id="a4e27-109">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **.NET Core cross-platform development** workload</span></span>

## <a name="installation-and-setup"></a><span data-ttu-id="a4e27-110">Instalación y configuración</span><span class="sxs-lookup"><span data-stu-id="a4e27-110">Installation and setup</span></span>

<span data-ttu-id="a4e27-111">Para instalar Docker, primero revise la información de [Docker Desktop for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install) (Docker Desktop para Windows: información previa a la instalación).</span><span class="sxs-lookup"><span data-stu-id="a4e27-111">For Docker installation, first review the information at [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).</span></span> <span data-ttu-id="a4e27-112">A continuación, instale [Docker para Windows](https://docs.docker.com/docker-for-windows/install/).</span><span class="sxs-lookup"><span data-stu-id="a4e27-112">Next, install [Docker For Windows](https://docs.docker.com/docker-for-windows/install/).</span></span>

<span data-ttu-id="a4e27-113">Las **[unidades compartidas](https://docs.docker.com/docker-for-windows/#shared-drives)** de Docker para Windows deben configurarse para admitir la asignación y la depuración de volúmenes.</span><span class="sxs-lookup"><span data-stu-id="a4e27-113">**[Shared Drives](https://docs.docker.com/docker-for-windows/#shared-drives)** in Docker for Windows must be configured to support volume mapping and debugging.</span></span> <span data-ttu-id="a4e27-114">Haga clic con el botón derecho en el icono de Docker de la bandeja del sistema, y seleccione **Configuración** y **Unidades compartidas**.</span><span class="sxs-lookup"><span data-stu-id="a4e27-114">Right-click the System Tray's Docker icon, select **Settings**, and select **Shared Drives**.</span></span> <span data-ttu-id="a4e27-115">Seleccione la unidad donde los archivos se almacenan en Docker.</span><span class="sxs-lookup"><span data-stu-id="a4e27-115">Select the drive where Docker stores files.</span></span> <span data-ttu-id="a4e27-116">Haga clic en **Aplicar**.</span><span class="sxs-lookup"><span data-stu-id="a4e27-116">Click **Apply**.</span></span>

![Cuadro de diálogo para seleccionar el uso compartido de la unidad C local para los contenedores](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> <span data-ttu-id="a4e27-118">Las versiones 15.6 y posteriores de Visual Studio 2017 le avisan si las **unidades compartidas** no están configuradas.</span><span class="sxs-lookup"><span data-stu-id="a4e27-118">Visual Studio 2017 versions 15.6 and later prompt when **Shared Drives** aren't configured.</span></span>

## <a name="add-a-project-to-a-docker-container"></a><span data-ttu-id="a4e27-119">Agregar un proyecto a un contenedor de Docker</span><span class="sxs-lookup"><span data-stu-id="a4e27-119">Add a project to a Docker container</span></span>

<span data-ttu-id="a4e27-120">Para incluir un proyecto de ASP.NET Core en un contenedor, el proyecto debe ser para .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a4e27-120">To containerize an ASP.NET Core project, the project must target .NET Core.</span></span> <span data-ttu-id="a4e27-121">Se admiten contenedores de Linux y Windows.</span><span class="sxs-lookup"><span data-stu-id="a4e27-121">Both Linux and Windows containers are supported.</span></span>

<span data-ttu-id="a4e27-122">Al agregar compatibilidad con Docker a un proyecto, elija un contenedor de Linux o Windows.</span><span class="sxs-lookup"><span data-stu-id="a4e27-122">When adding Docker support to a project, choose either a Windows or a Linux container.</span></span> <span data-ttu-id="a4e27-123">El host de Docker debe ejecutar el mismo tipo de contenedor.</span><span class="sxs-lookup"><span data-stu-id="a4e27-123">The Docker host must be running the same container type.</span></span> <span data-ttu-id="a4e27-124">Para cambiar el tipo de contenedor en la instancia de Docker en ejecución, haga clic con el botón derecho en el icono de Docker en la bandeja del sistema y elija **Switch to Windows containers...** (Cambiar a contenedores Windows) o **Switch to Linux containers...** (Cambiar a contenedores Linux).</span><span class="sxs-lookup"><span data-stu-id="a4e27-124">To change the container type in the running Docker instance, right-click the System Tray's Docker icon and choose **Switch to Windows containers...** or **Switch to Linux containers...**.</span></span>

### <a name="new-app"></a><span data-ttu-id="a4e27-125">Nueva aplicación</span><span class="sxs-lookup"><span data-stu-id="a4e27-125">New app</span></span>

<span data-ttu-id="a4e27-126">Al crear una nueva aplicación con las plantillas de proyecto **Aplicación web ASP.NET Core**, active la casilla **Enable Docker Support** (Habilitar compatibilidad con Docker):</span><span class="sxs-lookup"><span data-stu-id="a4e27-126">When creating a new app with the **ASP.NET Core Web Application** project templates, select the **Enable Docker Support** check box:</span></span>

![Casilla Habilitar compatibilidad con Docker](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

<span data-ttu-id="a4e27-128">Si la plataforma de destino es .NET Core, la lista desplegable de **SO** permite la selección de un tipo de contenedor.</span><span class="sxs-lookup"><span data-stu-id="a4e27-128">If the target framework is .NET Core, the **OS** drop-down allows for the selection of a container type.</span></span>

### <a name="existing-app"></a><span data-ttu-id="a4e27-129">Aplicación existente</span><span class="sxs-lookup"><span data-stu-id="a4e27-129">Existing app</span></span>

<span data-ttu-id="a4e27-130">En los proyectos de ASP.NET Core para .NET Core, hay dos opciones para agregar compatibilidad con Docker mediante las herramientas.</span><span class="sxs-lookup"><span data-stu-id="a4e27-130">For ASP.NET Core projects targeting .NET Core, there are two options for adding Docker support via the tooling.</span></span> <span data-ttu-id="a4e27-131">Abra el proyecto en Visual Studio y elija una de las siguientes opciones:</span><span class="sxs-lookup"><span data-stu-id="a4e27-131">Open the project in Visual Studio, and choose one of the following options:</span></span>

* <span data-ttu-id="a4e27-132">Seleccione **Compatibilidad con Docker** en el menú **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="a4e27-132">Select **Docker Support** from the **Project** menu.</span></span>
* <span data-ttu-id="a4e27-133">Haga clic con el botón derecho en el proyecto, en el **Explorador de soluciones**, y seleccione **Agregar** > **Compatibilidad con Docker**.</span><span class="sxs-lookup"><span data-stu-id="a4e27-133">Right-click the project in **Solution Explorer** and select **Add** > **Docker Support**.</span></span>

<span data-ttu-id="a4e27-134">Las herramientas de contenedor de Visual Studio no admiten la adición de Docker a un proyecto de ASP.NET Core existente para .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="a4e27-134">The Visual Studio Container Tools don't support adding Docker to an existing ASP.NET Core project targeting .NET Framework.</span></span>

## <a name="dockerfile-overview"></a><span data-ttu-id="a4e27-135">Información general sobre Dockerfile</span><span class="sxs-lookup"><span data-stu-id="a4e27-135">Dockerfile overview</span></span>

<span data-ttu-id="a4e27-136">Se agrega un *Dockerfile*, la receta para crear una imagen de Docker final, a la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="a4e27-136">A *Dockerfile*, the recipe for creating a final Docker image, is added to the project root.</span></span> <span data-ttu-id="a4e27-137">Vea [Dockerfile reference (Referencia de Dockerfile)](https://docs.docker.com/engine/reference/builder/) para obtener una descripción de los comandos que contiene.</span><span class="sxs-lookup"><span data-stu-id="a4e27-137">Refer to [Dockerfile reference](https://docs.docker.com/engine/reference/builder/) for an understanding of the commands within it.</span></span> <span data-ttu-id="a4e27-138">Este *Dockerfile* en concreto usa una [compilación de varias fases](https://docs.docker.com/engine/userguide/eng-image/multistage-build/), con cuatro fases de compilación distintas y cada una con un nombre asignado:</span><span class="sxs-lookup"><span data-stu-id="a4e27-138">This particular *Dockerfile* uses a [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) with four distinct, named build stages:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

<span data-ttu-id="a4e27-139">El *Dockerfile* anterior se basa en la imagen [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/).</span><span class="sxs-lookup"><span data-stu-id="a4e27-139">The preceding *Dockerfile* is based on the [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/) image.</span></span> <span data-ttu-id="a4e27-140">Esta imagen base incluye el entorno de ejecución de ASP.NET Core y los paquetes NuGet.</span><span class="sxs-lookup"><span data-stu-id="a4e27-140">This base image includes the ASP.NET Core runtime and NuGet packages.</span></span> <span data-ttu-id="a4e27-141">Los paquetes están compilados Just-In-Time (JIT) para mejorar el rendimiento de inicio.</span><span class="sxs-lookup"><span data-stu-id="a4e27-141">The packages are just-in-time (JIT) compiled to improve startup performance.</span></span>

<span data-ttu-id="a4e27-142">Si la casilla **Configurar para HTTPS** del cuadro de diálogo del nuevo proyecto está marcada, el *Dockerfile* expondrá dos puertos.</span><span class="sxs-lookup"><span data-stu-id="a4e27-142">When the new project dialog's **Configure for HTTPS** check box is checked, the *Dockerfile* exposes two ports.</span></span> <span data-ttu-id="a4e27-143">Uno se utiliza para el tráfico HTTP, mientras que el otro se emplea para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a4e27-143">One port is used for HTTP traffic; the other port is used for HTTPS.</span></span> <span data-ttu-id="a4e27-144">Si la casilla no está marcada, se expondrá un único puerto (80) para el tráfico HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4e27-144">If the check box isn't checked, a single port (80) is exposed for HTTP traffic.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

<span data-ttu-id="a4e27-145">El *Dockerfile* anterior se basa en la imagen [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/).</span><span class="sxs-lookup"><span data-stu-id="a4e27-145">The preceding *Dockerfile* is based on the [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) image.</span></span> <span data-ttu-id="a4e27-146">Esta imagen base incluye los paquetes NuGet de ASP.NET Core, compilados Just-In-Time (JIT) para mejorar el rendimiento de inicio.</span><span class="sxs-lookup"><span data-stu-id="a4e27-146">This base image includes the ASP.NET Core NuGet packages, which are just-in-time (JIT) compiled to improve startup performance.</span></span>

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a><span data-ttu-id="a4e27-147">Agregar compatibilidad con un orquestador de contenedores a una aplicación</span><span class="sxs-lookup"><span data-stu-id="a4e27-147">Add container orchestrator support to an app</span></span>

<span data-ttu-id="a4e27-148">Visual Studio 2017, versiones 15.7 o anteriores, es compatible con [Docker Compose](https://docs.docker.com/compose/overview/) como única solución de orquestación de contenedores.</span><span class="sxs-lookup"><span data-stu-id="a4e27-148">Visual Studio 2017 versions 15.7 or earlier support [Docker Compose](https://docs.docker.com/compose/overview/) as the sole container orchestration solution.</span></span> <span data-ttu-id="a4e27-149">Los artefactos de Docker Compose se agregan mediante **Agregar** > **Compatibilidad con Docker**.</span><span class="sxs-lookup"><span data-stu-id="a4e27-149">The Docker Compose artifacts are added via **Add** > **Docker Support**.</span></span>

<span data-ttu-id="a4e27-150">Visual Studio 2017, versiones 15.8 o posteriores, permite agregar una solución de orquestación de forma manual.</span><span class="sxs-lookup"><span data-stu-id="a4e27-150">Visual Studio 2017 versions 15.8 or later add an orchestration solution only when instructed.</span></span> <span data-ttu-id="a4e27-151">Haga clic con el botón derecho en el **Explorador de soluciones** y seleccione **Agregar** > **Compatibilidad con el orquestador de contenedores**.</span><span class="sxs-lookup"><span data-stu-id="a4e27-151">Right-click the project in **Solution Explorer** and select **Add** > **Container Orchestrator Support**.</span></span> <span data-ttu-id="a4e27-152">Están disponibles las siguientes opciones:</span><span class="sxs-lookup"><span data-stu-id="a4e27-152">The following choices are available:</span></span> 

* [<span data-ttu-id="a4e27-153">Docker Compose</span><span class="sxs-lookup"><span data-stu-id="a4e27-153">Docker Compose</span></span>](#docker-compose)
* [<span data-ttu-id="a4e27-154">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="a4e27-154">Service Fabric</span></span>](#service-fabric)
* [<span data-ttu-id="a4e27-155">Kubernetes/Helm</span><span class="sxs-lookup"><span data-stu-id="a4e27-155">Kubernetes/Helm </span></span>](https://helm.sh/)

### <a name="docker-compose"></a><span data-ttu-id="a4e27-156">Docker Compose</span><span class="sxs-lookup"><span data-stu-id="a4e27-156">Docker Compose</span></span>

<span data-ttu-id="a4e27-157">Las herramientas de contenedor de Visual Studio agregan un proyecto *docker-compose* a la solución con los archivos siguientes:</span><span class="sxs-lookup"><span data-stu-id="a4e27-157">The Visual Studio Container Tools add a *docker-compose* project to the solution with the following files:</span></span>

* <span data-ttu-id="a4e27-158">*docker-compose.dcproj*: archivo que representa el proyecto.</span><span class="sxs-lookup"><span data-stu-id="a4e27-158">*docker-compose.dcproj*: The file representing the project.</span></span> <span data-ttu-id="a4e27-159">Incluye un elemento `<DockerTargetOS>` en el que se especifica el sistema operativo que se utilizará.</span><span class="sxs-lookup"><span data-stu-id="a4e27-159">Includes a `<DockerTargetOS>` element specifying the OS to be used.</span></span>
* <span data-ttu-id="a4e27-160">*.dockerignore*: contiene una lista de los patrones de archivos y directorios que se excluirán al generar un contexto de compilación.</span><span class="sxs-lookup"><span data-stu-id="a4e27-160">*.dockerignore*: Lists the file and directory patterns to exclude when generating a build context.</span></span>
* <span data-ttu-id="a4e27-161">*docker-compose.yml*: archivo base de [Docker Compose](https://docs.docker.com/compose/overview/) que se utiliza para definir la colección de imágenes compilada y ejecutada con `docker-compose build` y `docker-compose run`, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="a4e27-161">*docker-compose.yml*: The base [Docker Compose](https://docs.docker.com/compose/overview/) file used to define the collection of images built and run with `docker-compose build` and `docker-compose run`, respectively.</span></span>
* <span data-ttu-id="a4e27-162">*docker-compose.override.yml*: archivo opcional que Docker Compose lee y que contiene las invalidaciones de configuración de los servicios.</span><span class="sxs-lookup"><span data-stu-id="a4e27-162">*docker-compose.override.yml*: An optional file, read by Docker Compose, with configuration overrides for services.</span></span> <span data-ttu-id="a4e27-163">Visual Studio ejecuta `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` para combinar estos archivos.</span><span class="sxs-lookup"><span data-stu-id="a4e27-163">Visual Studio executes `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` to merge these files.</span></span>

<span data-ttu-id="a4e27-164">El archivo *docker-compose.yml* hace referencia al nombre de la imagen que se crea al ejecutar el proyecto:</span><span class="sxs-lookup"><span data-stu-id="a4e27-164">The *docker-compose.yml* file references the name of the image that's created when the project runs:</span></span>

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

<span data-ttu-id="a4e27-165">En el ejemplo anterior, `image: hellodockertools` genera la imagen `hellodockertools:dev` cuando se ejecuta la aplicación en modo de **depuración**.</span><span class="sxs-lookup"><span data-stu-id="a4e27-165">In the preceding example, `image: hellodockertools` generates the image `hellodockertools:dev` when the app runs in **Debug** mode.</span></span> <span data-ttu-id="a4e27-166">La imagen `hellodockertools:latest` se genera cuando se ejecuta la aplicación en modo de **versión**.</span><span class="sxs-lookup"><span data-stu-id="a4e27-166">The `hellodockertools:latest` image is generated when the app runs in **Release** mode.</span></span>

<span data-ttu-id="a4e27-167">Si tiene previsto colocar la imagen en el Registro, utilice el nombre de usuario de [Docker Hub](https://hub.docker.com/) como prefijo, antes del nombre de imagen, por ejemplo, `dockerhubusername/hellodockertools`.</span><span class="sxs-lookup"><span data-stu-id="a4e27-167">Prefix the image name with the [Docker Hub](https://hub.docker.com/) username (for example, `dockerhubusername/hellodockertools`) if the image is pushed to the registry.</span></span> <span data-ttu-id="a4e27-168">También puede cambiar el nombre de la imagen para incluir la dirección URL del Registro privado (por ejemplo, `privateregistry.domain.com/hellodockertools`) según la configuración.</span><span class="sxs-lookup"><span data-stu-id="a4e27-168">Alternatively, change the image name to include the private registry URL (for example, `privateregistry.domain.com/hellodockertools`) depending on the configuration.</span></span>

<span data-ttu-id="a4e27-169">Si quiere un comportamiento diferente basado en la configuración de compilación (por ejemplo, Debug o Release), agregue archivos *docker-compose* específicos de la configuración.</span><span class="sxs-lookup"><span data-stu-id="a4e27-169">If you want different behavior based on the build configuration (for example, Debug or Release), add configuration-specific *docker-compose* files.</span></span> <span data-ttu-id="a4e27-170">Los nombres de los archivos deben basarse en la configuración de compilación (por ejemplo, *docker-compose.vs.debug.yml* y *docker-compose.vs.release.yml*) y deben colocarse en la misma ubicación que el archivo *docker-compose-override.yml*.</span><span class="sxs-lookup"><span data-stu-id="a4e27-170">The files should be named according to the build configuration (for example, *docker-compose.vs.debug.yml* and *docker-compose.vs.release.yml*) and placed in the same location as the *docker-compose-override.yml* file.</span></span> 

<span data-ttu-id="a4e27-171">Con los archivos de invalidación específicos de la configuración, puede especificar distintos valores de configuración (por ejemplo, variables de entorno o puntos de entrada) para las configuraciones de compilación de depuración y lanzamiento.</span><span class="sxs-lookup"><span data-stu-id="a4e27-171">Using the configuration-specific override files, you can specify different configuration settings (such as environment variables or entry points) for Debug and Release build configurations.</span></span>

<span data-ttu-id="a4e27-172">Para que Docker Compose muestre una opción para su ejecución en Visual Studio, el proyecto de Docker debe ser el proyecto de inicio.</span><span class="sxs-lookup"><span data-stu-id="a4e27-172">For Docker Compose to display an option to run in Visual Studio, the docker project must be the startup project.</span></span>

### <a name="service-fabric"></a><span data-ttu-id="a4e27-173">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="a4e27-173">Service Fabric</span></span>

<span data-ttu-id="a4e27-174">Además de los [requisitos previos](#prerequisites) base, la solución de orquestación de [Service Fabric](/azure/service-fabric/) presenta los siguientes requisitos previos:</span><span class="sxs-lookup"><span data-stu-id="a4e27-174">In addition to the base [Prerequisites](#prerequisites), the [Service Fabric](/azure/service-fabric/) orchestration solution demands the following prerequisites:</span></span>

* <span data-ttu-id="a4e27-175">[SDK de Microsoft Azure Service Fabric](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK), versión 2.6 o posterior</span><span class="sxs-lookup"><span data-stu-id="a4e27-175">[Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) version 2.6 or later</span></span>
* <span data-ttu-id="a4e27-176">Carga de trabajo **Desarrollo de Azure** de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4e27-176">Visual Studio's **Azure Development** workload</span></span>

<span data-ttu-id="a4e27-177">Service Fabric no admite la ejecución de contenedores de Linux en el clúster de desarrollo local de Windows.</span><span class="sxs-lookup"><span data-stu-id="a4e27-177">Service Fabric doesn't support running Linux containers in the local development cluster on Windows.</span></span> <span data-ttu-id="a4e27-178">Si el proyecto ya utiliza un contenedor de Linux, Visual Studio le solicitará que cambie a los contenedores de Windows.</span><span class="sxs-lookup"><span data-stu-id="a4e27-178">If the project is already using a Linux container, Visual Studio prompts to switch to Windows containers.</span></span>

<span data-ttu-id="a4e27-179">Las herramientas de contenedor de Visual Studio permiten realizar las siguientes tareas:</span><span class="sxs-lookup"><span data-stu-id="a4e27-179">The Visual Studio Container Tools do the following tasks:</span></span>

* <span data-ttu-id="a4e27-180">Agrega un proyecto *&lt;nombre_de_proyecto&gt;Aplicación* **Aplicación de Service Fabric** a la solución.</span><span class="sxs-lookup"><span data-stu-id="a4e27-180">Adds a *&lt;project_name&gt;Application* **Service Fabric Application** project to the solution.</span></span>
* <span data-ttu-id="a4e27-181">Agregar un *Dockerfile* y un archivo *.dockerignore* al proyecto de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a4e27-181">Adds a *Dockerfile* and a *.dockerignore* file to the ASP.NET Core project.</span></span> <span data-ttu-id="a4e27-182">Si el proyecto de ASP.NET Core ya contiene un *Dockerfile*, se le cambiará el nombre a *Dockerfile.original*.</span><span class="sxs-lookup"><span data-stu-id="a4e27-182">If a *Dockerfile* already exists in the ASP.NET Core project, it's renamed to *Dockerfile.original*.</span></span> <span data-ttu-id="a4e27-183">A continuación, se creará un nuevo *Dockerfile* similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="a4e27-183">A new *Dockerfile*, similar to the following, is created:</span></span>

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* <span data-ttu-id="a4e27-184">Agregar un elemento `<IsServiceFabricServiceProject>` al archivo *.csproj* al proyecto de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a4e27-184">Adds an `<IsServiceFabricServiceProject>` element to the ASP.NET Core project's *.csproj* file:</span></span>

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* <span data-ttu-id="a4e27-185">Agregar una carpeta *PackageRoot* al proyecto de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a4e27-185">Adds a *PackageRoot* folder to the ASP.NET Core project.</span></span> <span data-ttu-id="a4e27-186">La carpeta incluirá un manifiesto de servicio y las opciones de configuración del nuevo servicio.</span><span class="sxs-lookup"><span data-stu-id="a4e27-186">The folder includes the service manifest and settings for the new service.</span></span>

<span data-ttu-id="a4e27-187">Para obtener más información, consulte [Implementación de una aplicación .NET de un contenedor de Windows en Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).</span><span class="sxs-lookup"><span data-stu-id="a4e27-187">For more information, see [Deploy a .NET app in a Windows container to Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).</span></span>

## <a name="debug"></a><span data-ttu-id="a4e27-188">Depuración</span><span class="sxs-lookup"><span data-stu-id="a4e27-188">Debug</span></span>

<span data-ttu-id="a4e27-189">Seleccione **Docker** en la lista desplegable de depuración de la barra de herramientas y empiece a depurar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a4e27-189">Select **Docker** from the debug drop-down in the toolbar, and start debugging the app.</span></span> <span data-ttu-id="a4e27-190">La vista **Docker** de la ventana **Salida** muestra las acciones siguientes en curso:</span><span class="sxs-lookup"><span data-stu-id="a4e27-190">The **Docker** view of the **Output** window shows the following actions taking place:</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="a4e27-191">Si todavía no está en la caché, se adquirirá la etiqueta *2.1-aspnetcore-runtime* de la imagen del entorno de ejecución *microsoft/dotnet*.</span><span class="sxs-lookup"><span data-stu-id="a4e27-191">The *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* runtime image is acquired (if not already in the cache).</span></span> <span data-ttu-id="a4e27-192">La imagen instala los entornos de ejecución de ASP.NET Core y .NET Core, así como las bibliotecas asociadas.</span><span class="sxs-lookup"><span data-stu-id="a4e27-192">The image installs the ASP.NET Core and .NET Core runtimes and associated libraries.</span></span> <span data-ttu-id="a4e27-193">Además, está optimizada para ejecutar aplicaciones ASP.NET Core en producción.</span><span class="sxs-lookup"><span data-stu-id="a4e27-193">It's optimized for running ASP.NET Core apps in production.</span></span>
* <span data-ttu-id="a4e27-194">Dentro del contenedor, la variable de entorno `ASPNETCORE_ENVIRONMENT` se establece en `Development`.</span><span class="sxs-lookup"><span data-stu-id="a4e27-194">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="a4e27-195">Se exponen dos puertos asignados de forma dinámica: uno para HTTP y otro para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a4e27-195">Two dynamically assigned ports are exposed: one for HTTP and one for HTTPS.</span></span> <span data-ttu-id="a4e27-196">El puerto asignado al localhost se puede asignar mediante el comando `docker ps`.</span><span class="sxs-lookup"><span data-stu-id="a4e27-196">The port assigned to localhost can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="a4e27-197">La aplicación se copia en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="a4e27-197">The app is copied to the container.</span></span>
* <span data-ttu-id="a4e27-198">Se inicia el explorador predeterminado con el depurador asociado al contenedor, con el puerto asignado dinámicamente.</span><span class="sxs-lookup"><span data-stu-id="a4e27-198">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="a4e27-199">Seguidamente, se aplica la etiqueta *dev* a la imagen de Docker resultante de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a4e27-199">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="a4e27-200">La imagen se basa en la etiqueta *2.1-aspnetcore-runtime* de la imagen base *microsoft/dotnet*.</span><span class="sxs-lookup"><span data-stu-id="a4e27-200">The image is based on the *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* base image.</span></span> <span data-ttu-id="a4e27-201">Ejecute el comando `docker images` en la ventana **Consola del Administrador de paquetes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="a4e27-201">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="a4e27-202">Se muestran las imágenes en la máquina:</span><span class="sxs-lookup"><span data-stu-id="a4e27-202">The images on the machine are displayed:</span></span>

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* <span data-ttu-id="a4e27-203">Se adquiere la imagen *microsoft/aspnetcore* en tiempo de ejecución (si todavía no está en la caché).</span><span class="sxs-lookup"><span data-stu-id="a4e27-203">The *microsoft/aspnetcore* runtime image is acquired (if not already in the cache).</span></span>
* <span data-ttu-id="a4e27-204">Dentro del contenedor, la variable de entorno `ASPNETCORE_ENVIRONMENT` se establece en `Development`.</span><span class="sxs-lookup"><span data-stu-id="a4e27-204">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="a4e27-205">Se expone el puerto 80 y se asigna a un puerto asignado dinámicamente para el host local.</span><span class="sxs-lookup"><span data-stu-id="a4e27-205">Port 80 is exposed and mapped to a dynamically assigned port for localhost.</span></span> <span data-ttu-id="a4e27-206">El puerto viene determinado por el host de Docker y se puede consultar con el comando `docker ps`.</span><span class="sxs-lookup"><span data-stu-id="a4e27-206">The port is determined by the Docker host and can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="a4e27-207">La aplicación se copia en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="a4e27-207">The app is copied to the container.</span></span>
* <span data-ttu-id="a4e27-208">Se inicia el explorador predeterminado con el depurador asociado al contenedor, con el puerto asignado dinámicamente.</span><span class="sxs-lookup"><span data-stu-id="a4e27-208">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="a4e27-209">Seguidamente, se aplica la etiqueta *dev* a la imagen de Docker resultante de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a4e27-209">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="a4e27-210">La imagen se basa en la imagen base *microsoft/aspnetcore*.</span><span class="sxs-lookup"><span data-stu-id="a4e27-210">The image is based on the *microsoft/aspnetcore* base image.</span></span> <span data-ttu-id="a4e27-211">Ejecute el comando `docker images` en la ventana **Consola del Administrador de paquetes** (PMC).</span><span class="sxs-lookup"><span data-stu-id="a4e27-211">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="a4e27-212">Se muestran las imágenes en la máquina:</span><span class="sxs-lookup"><span data-stu-id="a4e27-212">The images on the machine are displayed:</span></span>

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="a4e27-213">La imagen *dev* carece del contenido de la aplicación, ya que las opciones de configuración de **depuración** utilizan el montaje de volúmenes para proporcionar la experiencia iterativa.</span><span class="sxs-lookup"><span data-stu-id="a4e27-213">The *dev* image lacks the app contents, as **Debug** configurations use volume mounting to provide the iterative experience.</span></span> <span data-ttu-id="a4e27-214">Para insertar una imagen, use la configuración de **versión**.</span><span class="sxs-lookup"><span data-stu-id="a4e27-214">To push an image, use the **Release** configuration.</span></span>

<span data-ttu-id="a4e27-215">Ejecute el comando `docker ps` en la PMC.</span><span class="sxs-lookup"><span data-stu-id="a4e27-215">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="a4e27-216">Tenga en cuenta que la aplicación se ejecuta mediante el contenedor:</span><span class="sxs-lookup"><span data-stu-id="a4e27-216">Notice the app is running using the container:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a><span data-ttu-id="a4e27-217">Editar y continuar</span><span class="sxs-lookup"><span data-stu-id="a4e27-217">Edit and continue</span></span>

<span data-ttu-id="a4e27-218">Los cambios en archivos estáticos y vistas de Razor se actualizan automáticamente sin necesidad de ningún paso de compilación.</span><span class="sxs-lookup"><span data-stu-id="a4e27-218">Changes to static files and Razor views are automatically updated without the need for a compilation step.</span></span> <span data-ttu-id="a4e27-219">Realice el cambio, guarde y actualice el explorador para ver la actualización.</span><span class="sxs-lookup"><span data-stu-id="a4e27-219">Make the change, save, and refresh the browser to view the update.</span></span>

<span data-ttu-id="a4e27-220">Las modificaciones en los archivos de código requieren compilación y un reinicio del Kestrel dentro del contenedor.</span><span class="sxs-lookup"><span data-stu-id="a4e27-220">Code file modifications require compilation and a restart of Kestrel within the container.</span></span> <span data-ttu-id="a4e27-221">Después de realizar la modificación, use `CTRL+F5` para realizar el proceso e iniciar la aplicación dentro del contenedor.</span><span class="sxs-lookup"><span data-stu-id="a4e27-221">After making the change, use `CTRL+F5` to perform the process and start the app within the container.</span></span> <span data-ttu-id="a4e27-222">El contenedor de Docker no se vuelve a compilar ni se detiene.</span><span class="sxs-lookup"><span data-stu-id="a4e27-222">The Docker container isn't rebuilt or stopped.</span></span> <span data-ttu-id="a4e27-223">Ejecute el comando `docker ps` en la PMC.</span><span class="sxs-lookup"><span data-stu-id="a4e27-223">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="a4e27-224">Observe que el contenedor original se está ejecutando desde hace 10 minutos:</span><span class="sxs-lookup"><span data-stu-id="a4e27-224">Notice the original container is still running as of 10 minutes ago:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a><span data-ttu-id="a4e27-225">Publicar imágenes de Docker</span><span class="sxs-lookup"><span data-stu-id="a4e27-225">Publish Docker images</span></span>

<span data-ttu-id="a4e27-226">Una vez que se completa el ciclo de desarrollo y depuración de la aplicación, las herramientas de contenedor de Visual Studio ayudan a crear la imagen de producción de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a4e27-226">Once the develop and debug cycle of the app is completed, the Visual Studio Container Tools assist in creating the production image of the app.</span></span> <span data-ttu-id="a4e27-227">Cambie la lista desplegable de configuración a **Versión** y compile la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a4e27-227">Change the configuration drop-down to **Release** and build the app.</span></span> <span data-ttu-id="a4e27-228">Si todavía no está en la caché, las herramientas obtendrán la imagen de compilación o publicación a partir de Docker Hub.</span><span class="sxs-lookup"><span data-stu-id="a4e27-228">The tooling acquires the compile/publish image from Docker Hub (if not already in the cache).</span></span> <span data-ttu-id="a4e27-229">Se generará una imagen con la etiqueta *más reciente* que se puede colocar en el Registro privado o Docker Hub.</span><span class="sxs-lookup"><span data-stu-id="a4e27-229">An image is produced with the *latest* tag, which can be pushed to the private registry or Docker Hub.</span></span>

<span data-ttu-id="a4e27-230">Para consultar la lista de imágenes, ejecute el comando `docker images` en PMC.</span><span class="sxs-lookup"><span data-stu-id="a4e27-230">Run the `docker images` command in PMC to see the list of images.</span></span> <span data-ttu-id="a4e27-231">Esto genera una salida similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="a4e27-231">Output similar to the following is displayed:</span></span>

::: moniker range=">= aspnetcore-2.1"

```console
REPOSITORY        TAG                     IMAGE ID      CREATED             SIZE
hellodockertools  latest                  e3984a64230c  About a minute ago  258MB
hellodockertools  dev                     d72ce0f1dfe7  4 minutes ago       255MB
microsoft/dotnet  2.1-sdk                 9e243db15f91  6 days ago          1.7GB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago          255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

```console
REPOSITORY                  TAG     IMAGE ID      CREATED         SIZE
hellodockertools            latest  cd28f0d4abbd  12 seconds ago  349MB
hellodockertools            dev     5fafe5d1ad5b  23 minutes ago  347MB
microsoft/aspnetcore-build  2.0     7fed40fbb647  13 days ago     2.02GB
microsoft/aspnetcore        2.0     c69d39472da9  13 days ago     347MB
```

<span data-ttu-id="a4e27-232">A partir de .NET Core 2.1, las imágenes `microsoft/aspnetcore-build` y `microsoft/aspnetcore` que figuran en la salida anterior se reemplazan por imágenes `microsoft/dotnet`.</span><span class="sxs-lookup"><span data-stu-id="a4e27-232">The `microsoft/aspnetcore-build` and `microsoft/aspnetcore` images listed in the preceding output are replaced with `microsoft/dotnet` images as of .NET Core 2.1.</span></span> <span data-ttu-id="a4e27-233">Para obtener más información, consulte el [anuncio sobre la migración de los repositorios de Docker](https://github.com/aspnet/Announcements/issues/298).</span><span class="sxs-lookup"><span data-stu-id="a4e27-233">For more information, see [the Docker repositories migration announcement](https://github.com/aspnet/Announcements/issues/298).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="a4e27-234">El comando `docker images` devuelve imágenes de intermediario con los nombres de repositorio y las etiquetas identificados como *\<none>* (no mencionado anteriormente).</span><span class="sxs-lookup"><span data-stu-id="a4e27-234">The `docker images` command returns intermediary images with repository names and tags identified as *\<none>* (not listed above).</span></span> <span data-ttu-id="a4e27-235">Estas imágenes sin nombre son creadas por el *Dockerfile* de [compilación de varias fases](https://docs.docker.com/engine/userguide/eng-image/multistage-build/).</span><span class="sxs-lookup"><span data-stu-id="a4e27-235">These unnamed images are produced by the [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile*.</span></span> <span data-ttu-id="a4e27-236">Mejoran la eficacia de la compilación de la imagen final y solo se vuelven a compilar las capas necesarias cuando se producen cambios.</span><span class="sxs-lookup"><span data-stu-id="a4e27-236">They improve the efficiency of building the final image&mdash;only the necessary layers are rebuilt when changes occur.</span></span> <span data-ttu-id="a4e27-237">Cuando las imágenes de intermediario ya no sean necesarias, elimínelas mediante el comando [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/).</span><span class="sxs-lookup"><span data-stu-id="a4e27-237">When the intermediary images are no longer needed, delete them using the [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) command.</span></span>

<span data-ttu-id="a4e27-238">Podría esperarse que la imagen de producción o versión fuera más pequeña que la imagen *dev*.</span><span class="sxs-lookup"><span data-stu-id="a4e27-238">There may be an expectation for the production or release image to be smaller in size by comparison to the *dev* image.</span></span> <span data-ttu-id="a4e27-239">Debido a la asignación de volumen, el depurador y la aplicación se han ejecutado desde la máquina local y no dentro del contenedor.</span><span class="sxs-lookup"><span data-stu-id="a4e27-239">Because of the volume mapping, the debugger and app were running from the local machine and not within the container.</span></span> <span data-ttu-id="a4e27-240">La imagen *más reciente* ha empaquetado el código de aplicación necesario para ejecutar la aplicación en un equipo host.</span><span class="sxs-lookup"><span data-stu-id="a4e27-240">The *latest* image has packaged the necessary app code to run the app on a host machine.</span></span> <span data-ttu-id="a4e27-241">Por tanto, la diferencia es el tamaño del código de aplicación.</span><span class="sxs-lookup"><span data-stu-id="a4e27-241">Therefore, the delta is the size of the app code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a4e27-242">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="a4e27-242">Additional resources</span></span>

* [<span data-ttu-id="a4e27-243">Desarrollo de contenedores con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4e27-243">Container development with Visual Studio</span></span>](/visualstudio/containers)
* [<span data-ttu-id="a4e27-244">Azure Service Fabric: Preparación del entorno de desarrollo</span><span class="sxs-lookup"><span data-stu-id="a4e27-244">Azure Service Fabric: Prepare your development environment</span></span>](/azure/service-fabric/service-fabric-get-started)
* [<span data-ttu-id="a4e27-245">Implementación de una aplicación .NET de un contenedor de Windows en Azure Service Fabric</span><span class="sxs-lookup"><span data-stu-id="a4e27-245">Deploy a .NET app in a Windows container to Azure Service Fabric</span></span>](/azure/service-fabric/service-fabric-host-app-in-a-container)
* <span data-ttu-id="a4e27-246">[Troubleshoot Visual Studio development with Docker](/azure/vs-azure-tools-docker-troubleshooting-docker-errors) (Solución de problemas de desarrollo de Visual Studio con Docker)</span><span class="sxs-lookup"><span data-stu-id="a4e27-246">[Troubleshoot Visual Studio development with Docker](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)</span></span>
* <span data-ttu-id="a4e27-247">[Visual Studio Container Tools GitHub repository](https://github.com/Microsoft/DockerTools) (Repositorio de GitHub de herramientas de contenedor de Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="a4e27-247">[Visual Studio Container Tools GitHub repository](https://github.com/Microsoft/DockerTools)</span></span>
* [<span data-ttu-id="a4e27-248">GC mediante Docker y contenedores pequeños</span><span class="sxs-lookup"><span data-stu-id="a4e27-248">GC using Docker and small containers</span></span>](xref:performance/memory#sc)
