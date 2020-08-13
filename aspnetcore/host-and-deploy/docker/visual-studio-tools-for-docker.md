---
title: Herramientas de contenedor de Visual Studio con ASP.NET Core
author: spboyer
description: Obtenga información sobre cómo usar las herramientas de Visual Studio y Docker para Windows para incluir en un contenedor una aplicación de ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2018
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
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: 2bdbbcfe4a94e5333ae11c4ab486ee0676d24b31
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015730"
---
# <a name="visual-studio-container-tools-with-aspnet-core"></a>Herramientas de contenedor de Visual Studio con ASP.NET Core

En Visual Studio 2017 y versiones posteriores, se pueden compilar, depurar y ejecutar aplicaciones ASP.NET Core incluidas en un contenedor para .NET Core. Se admiten contenedores de Windows y Linux.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Requisitos previos

* [Docker para Windows](https://docs.docker.com/docker-for-windows/install/)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **Desarrollo multiplataforma de .NET Core**

## <a name="installation-and-setup"></a>Instalación y configuración

Para instalar Docker, primero revise la información de [Docker Desktop for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install) (Docker Desktop para Windows: información previa a la instalación). A continuación, instale [Docker para Windows](https://docs.docker.com/docker-for-windows/install/).

Las **[unidades compartidas](https://docs.docker.com/docker-for-windows/#shared-drives)** de Docker para Windows deben configurarse para admitir la asignación y la depuración de volúmenes. Haga clic con el botón derecho en el icono de Docker de la bandeja del sistema, y seleccione **Configuración** y **Unidades compartidas**. Seleccione la unidad donde los archivos se almacenan en Docker. Haga clic en **Aplicar**.

![Cuadro de diálogo para seleccionar el uso compartido de la unidad C local para los contenedores](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> Las versiones 15.6 y posteriores de Visual Studio 2017 le avisan si las **unidades compartidas** no están configuradas.

## <a name="add-a-project-to-a-docker-container"></a>Agregar un proyecto a un contenedor de Docker

Para incluir un proyecto de ASP.NET Core en un contenedor, el proyecto debe ser para .NET Core. Se admiten contenedores de Linux y Windows.

Al agregar compatibilidad con Docker a un proyecto, elija un contenedor de Linux o Windows. El host de Docker debe ejecutar el mismo tipo de contenedor. Para cambiar el tipo de contenedor en la instancia de Docker en ejecución, haga clic con el botón derecho en el icono de Docker en la bandeja del sistema y elija **Switch to Windows containers...** (Cambiar a contenedores Windows) o **Switch to Linux containers...** (Cambiar a contenedores Linux).

### <a name="new-app"></a>Nueva aplicación

Al crear una nueva aplicación con las plantillas de proyecto **Aplicación web ASP.NET Core**, active la casilla **Enable Docker Support** (Habilitar compatibilidad con Docker):

![Casilla Habilitar compatibilidad con Docker](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

Si la plataforma de destino es .NET Core, la lista desplegable de **SO** permite la selección de un tipo de contenedor.

### <a name="existing-app"></a>Aplicación existente

En los proyectos de ASP.NET Core para .NET Core, hay dos opciones para agregar compatibilidad con Docker mediante las herramientas. Abra el proyecto en Visual Studio y elija una de las siguientes opciones:

* Seleccione **Compatibilidad con Docker** en el menú **Proyecto**.
* Haga clic con el botón derecho en el proyecto, en el **Explorador de soluciones**, y seleccione **Agregar** > **Compatibilidad con Docker**.

Las herramientas de contenedor de Visual Studio no admiten la adición de Docker a un proyecto de ASP.NET Core existente para .NET Framework.

## <a name="dockerfile-overview"></a>Información general sobre Dockerfile

Se agrega un *Dockerfile*, la receta para crear una imagen de Docker final, a la raíz del proyecto. Vea [Dockerfile reference (Referencia de Dockerfile)](https://docs.docker.com/engine/reference/builder/) para obtener una descripción de los comandos que contiene. Este *Dockerfile* en concreto usa una [compilación de varias fases](https://docs.docker.com/engine/userguide/eng-image/multistage-build/), con cuatro fases de compilación distintas y cada una con un nombre asignado:

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

El *Dockerfile* anterior se basa en la imagen [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/). Esta imagen base incluye el entorno de ejecución de ASP.NET Core y los paquetes NuGet. Los paquetes están compilados Just-In-Time (JIT) para mejorar el rendimiento de inicio.

Si la casilla **Configurar para HTTPS** del cuadro de diálogo del nuevo proyecto está marcada, el *Dockerfile* expondrá dos puertos. Uno se utiliza para el tráfico HTTP, mientras que el otro se emplea para HTTPS. Si la casilla no está marcada, se expondrá un único puerto (80) para el tráfico HTTP.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

El *Dockerfile* anterior se basa en la imagen [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/). Esta imagen base incluye los paquetes NuGet de ASP.NET Core, compilados Just-In-Time (JIT) para mejorar el rendimiento de inicio.

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a>Agregar compatibilidad con un orquestador de contenedores a una aplicación

Visual Studio 2017, versiones 15.7 o anteriores, es compatible con [Docker Compose](https://docs.docker.com/compose/overview/) como única solución de orquestación de contenedores. Los artefactos de Docker Compose se agregan mediante **Agregar** > **Compatibilidad con Docker**.

Visual Studio 2017, versiones 15.8 o posteriores, permite agregar una solución de orquestación de forma manual. Haga clic con el botón derecho en el **Explorador de soluciones** y seleccione **Agregar** > **Compatibilidad con el orquestador de contenedores**. Están disponibles las siguientes opciones: 

* [Docker Compose](#docker-compose)
* [Service Fabric](#service-fabric)
* [Kubernetes/Helm](https://helm.sh/)

### <a name="docker-compose"></a>Docker Compose

Las herramientas de contenedor de Visual Studio agregan un proyecto *docker-compose* a la solución con los archivos siguientes:

* *docker-compose.dcproj*: archivo que representa el proyecto. Incluye un elemento `<DockerTargetOS>` en el que se especifica el sistema operativo que se utilizará.
* *.dockerignore*: contiene una lista de los patrones de archivos y directorios que se excluirán al generar un contexto de compilación.
* *docker-compose.yml*: archivo base de [Docker Compose](https://docs.docker.com/compose/overview/) que se utiliza para definir la colección de imágenes compilada y ejecutada con `docker-compose build` y `docker-compose run`, respectivamente.
* *docker-compose.override.yml*: archivo opcional que Docker Compose lee y que contiene las invalidaciones de configuración de los servicios. Visual Studio ejecuta `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` para combinar estos archivos.

El archivo *docker-compose.yml* hace referencia al nombre de la imagen que se crea al ejecutar el proyecto:

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

En el ejemplo anterior, `image: hellodockertools` genera la imagen `hellodockertools:dev` cuando se ejecuta la aplicación en modo de **depuración**. La imagen `hellodockertools:latest` se genera cuando se ejecuta la aplicación en modo de **versión**.

Si tiene previsto colocar la imagen en el Registro, utilice el nombre de usuario de [Docker Hub](https://hub.docker.com/) como prefijo, antes del nombre de imagen, por ejemplo, `dockerhubusername/hellodockertools`. También puede cambiar el nombre de la imagen para incluir la dirección URL del Registro privado (por ejemplo, `privateregistry.domain.com/hellodockertools`) según la configuración.

Si quiere un comportamiento diferente basado en la configuración de compilación (por ejemplo, Debug o Release), agregue archivos *docker-compose* específicos de la configuración. Los nombres de los archivos deben basarse en la configuración de compilación (por ejemplo, *docker-compose.vs.debug.yml* y *docker-compose.vs.release.yml*) y deben colocarse en la misma ubicación que el archivo *docker-compose-override.yml*. 

Con los archivos de invalidación específicos de la configuración, puede especificar distintos valores de configuración (por ejemplo, variables de entorno o puntos de entrada) para las configuraciones de compilación de depuración y lanzamiento.

Para que Docker Compose muestre una opción para su ejecución en Visual Studio, el proyecto de Docker debe ser el proyecto de inicio.

### <a name="service-fabric"></a>Service Fabric

Además de los [requisitos previos](#prerequisites) base, la solución de orquestación de [Service Fabric](/azure/service-fabric/) presenta los siguientes requisitos previos:

* [SDK de Microsoft Azure Service Fabric](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK), versión 2.6 o posterior
* Carga de trabajo **Desarrollo de Azure** de Visual Studio

Service Fabric no admite la ejecución de contenedores de Linux en el clúster de desarrollo local de Windows. Si el proyecto ya utiliza un contenedor de Linux, Visual Studio le solicitará que cambie a los contenedores de Windows.

Las herramientas de contenedor de Visual Studio permiten realizar las siguientes tareas:

* Agrega un proyecto *&lt;nombre_de_proyecto&gt;Aplicación* **Aplicación de Service Fabric** a la solución.
* Agregar un *Dockerfile* y un archivo *.dockerignore* al proyecto de ASP.NET Core. Si el proyecto de ASP.NET Core ya contiene un *Dockerfile*, se le cambiará el nombre a *Dockerfile.original*. A continuación, se creará un nuevo *Dockerfile* similar al siguiente:

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* Agregar un elemento `<IsServiceFabricServiceProject>` al archivo *.csproj* al proyecto de ASP.NET Core:

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* Agregar una carpeta *PackageRoot* al proyecto de ASP.NET Core. La carpeta incluirá un manifiesto de servicio y las opciones de configuración del nuevo servicio.

Para obtener más información, consulte [Implementación de una aplicación .NET de un contenedor de Windows en Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).

## <a name="debug"></a>Depuración

Seleccione **Docker** en la lista desplegable de depuración de la barra de herramientas y empiece a depurar la aplicación. La vista **Docker** de la ventana **Salida** muestra las acciones siguientes en curso:

::: moniker range=">= aspnetcore-2.1"

* Si todavía no está en la caché, se adquirirá la etiqueta *2.1-aspnetcore-runtime* de la imagen del entorno de ejecución *microsoft/dotnet*. La imagen instala los entornos de ejecución de ASP.NET Core y .NET Core, así como las bibliotecas asociadas. Además, está optimizada para ejecutar aplicaciones ASP.NET Core en producción.
* Dentro del contenedor, la variable de entorno `ASPNETCORE_ENVIRONMENT` se establece en `Development`.
* Se exponen dos puertos asignados de forma dinámica: uno para HTTP y otro para HTTPS. El puerto asignado al localhost se puede asignar mediante el comando `docker ps`.
* La aplicación se copia en el contenedor.
* Se inicia el explorador predeterminado con el depurador asociado al contenedor, con el puerto asignado dinámicamente.

Seguidamente, se aplica la etiqueta *dev* a la imagen de Docker resultante de la aplicación. La imagen se basa en la etiqueta *2.1-aspnetcore-runtime* de la imagen base *microsoft/dotnet*. Ejecute el comando `docker images` en la ventana **Consola del Administrador de paquetes** (PMC). Se muestran las imágenes en la máquina:

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* Se adquiere la imagen *microsoft/aspnetcore* en tiempo de ejecución (si todavía no está en la caché).
* Dentro del contenedor, la variable de entorno `ASPNETCORE_ENVIRONMENT` se establece en `Development`.
* Se expone el puerto 80 y se asigna a un puerto asignado dinámicamente para el host local. El puerto viene determinado por el host de Docker y se puede consultar con el comando `docker ps`.
* La aplicación se copia en el contenedor.
* Se inicia el explorador predeterminado con el depurador asociado al contenedor, con el puerto asignado dinámicamente.

Seguidamente, se aplica la etiqueta *dev* a la imagen de Docker resultante de la aplicación. La imagen se basa en la imagen base *microsoft/aspnetcore*. Ejecute el comando `docker images` en la ventana **Consola del Administrador de paquetes** (PMC). Se muestran las imágenes en la máquina:

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> La imagen *dev* carece del contenido de la aplicación, ya que las opciones de configuración de **depuración** utilizan el montaje de volúmenes para proporcionar la experiencia iterativa. Para insertar una imagen, use la configuración de **versión**.

Ejecute el comando `docker ps` en la PMC. Tenga en cuenta que la aplicación se ejecuta mediante el contenedor:

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a>Editar y continuar

Los cambios en archivos estáticos y vistas de Razor se actualizan automáticamente sin necesidad de ningún paso de compilación. Realice el cambio, guarde y actualice el explorador para ver la actualización.

Las modificaciones en los archivos de código requieren compilación y un reinicio del Kestrel dentro del contenedor. Después de realizar la modificación, use `CTRL+F5` para realizar el proceso e iniciar la aplicación dentro del contenedor. El contenedor de Docker no se vuelve a compilar ni se detiene. Ejecute el comando `docker ps` en la PMC. Observe que el contenedor original se está ejecutando desde hace 10 minutos:

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a>Publicar imágenes de Docker

Una vez que se completa el ciclo de desarrollo y depuración de la aplicación, las herramientas de contenedor de Visual Studio ayudan a crear la imagen de producción de la aplicación. Cambie la lista desplegable de configuración a **Versión** y compile la aplicación. Si todavía no está en la caché, las herramientas obtendrán la imagen de compilación o publicación a partir de Docker Hub. Se generará una imagen con la etiqueta *más reciente* que se puede colocar en el Registro privado o Docker Hub.

Para consultar la lista de imágenes, ejecute el comando `docker images` en PMC. Esto genera una salida similar a la siguiente:

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

A partir de .NET Core 2.1, las imágenes `microsoft/aspnetcore-build` y `microsoft/aspnetcore` que figuran en la salida anterior se reemplazan por imágenes `microsoft/dotnet`. Para obtener más información, consulte el [anuncio sobre la migración de los repositorios de Docker](https://github.com/aspnet/Announcements/issues/298).

::: moniker-end

> [!NOTE]
> El comando `docker images` devuelve imágenes de intermediario con los nombres de repositorio y las etiquetas identificados como *\<none>* (no mencionado anteriormente). Estas imágenes sin nombre son creadas por el *Dockerfile* de [compilación de varias fases](https://docs.docker.com/engine/userguide/eng-image/multistage-build/). Mejoran la eficacia de la compilación de la imagen final y solo se vuelven a compilar las capas necesarias cuando se producen cambios. Cuando las imágenes de intermediario ya no sean necesarias, elimínelas mediante el comando [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/).

Podría esperarse que la imagen de producción o versión fuera más pequeña que la imagen *dev*. Debido a la asignación de volumen, el depurador y la aplicación se han ejecutado desde la máquina local y no dentro del contenedor. La imagen *más reciente* ha empaquetado el código de aplicación necesario para ejecutar la aplicación en un equipo host. Por tanto, la diferencia es el tamaño del código de aplicación.

## <a name="additional-resources"></a>Recursos adicionales

* [Desarrollo de contenedores con Visual Studio](/visualstudio/containers)
* [Azure Service Fabric: Preparación del entorno de desarrollo](/azure/service-fabric/service-fabric-get-started)
* [Implementación de una aplicación .NET de un contenedor de Windows en Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [Troubleshoot Visual Studio development with Docker](/azure/vs-azure-tools-docker-troubleshooting-docker-errors) (Solución de problemas de desarrollo de Visual Studio con Docker)
* [Visual Studio Container Tools GitHub repository](https://github.com/Microsoft/DockerTools) (Repositorio de GitHub de herramientas de contenedor de Visual Studio)
* [GC mediante Docker y contenedores pequeños](xref:performance/memory#sc)
