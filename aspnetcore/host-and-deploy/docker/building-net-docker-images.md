---
title: Imágenes de Docker para ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo usar las imágenes de Docker de ASP.NET Core publicadas desde el registro de Docker. Extraiga imágenes y cree las suyas propias.
ms.author: riande
ms.custom: mvc
ms.date: 01/04/2021
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
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: b29ce03366e5c0e815de0874f5b96efb9ba5326c
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585960"
---
# <a name="docker-images-for-aspnet-core"></a>Imágenes de Docker para ASP.NET Core

En este tutorial se muestra cómo ejecutar una aplicación ASP.NET Core en contenedores de Docker.

En este tutorial ha:
> [!div class="checklist"]
> * Obtendrá información sobre las Imágenes de Docker para ASP.NET Core
> * Descargará una aplicación de ejemplo de ASP.NET Core
> * Ejecutará la aplicación de ejemplo localmente
> * Ejecutará la aplicación de ejemplo en contenedores de Linux
> * Ejecutará la aplicación de ejemplo en contenedores de Windows
> * Realizará compilaciones e implementaciones manualmente

## <a name="aspnet-core-docker-images"></a>Imágenes de Docker para ASP.NET Core

En este tutorial, descargará una aplicación de ejemplo de ASP.NET Core y la ejecutará en contenedores de Docker. El ejemplo funciona con contenedores de Linux y Windows.

El Dockerfile de ejemplo usa la [característica de compilación en varias fases de Docker](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) para la compilación y ejecución en distintos contenedores. Los contenedores de compilación y ejecución se crean a partir de imágenes que proporciona Microsoft en Docker Hub:

::: moniker range=">= aspnetcore-5.0"

* `dotnet/sdk`

  En el ejemplo se usa esta imagen para compilar la aplicación. La imagen contiene el SDK de .NET, que incluye las herramientas de la línea de comandos (CLI). Esta imagen está optimizada para el desarrollo local, la depuración y las pruebas unitarias. Las herramientas instaladas para el desarrollo y la compilación hacen que la imagen sea relativamente grande.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/sdk`

  En el ejemplo se usa esta imagen para compilar la aplicación. La imagen contiene el SDK de .NET Core, que incluye las herramientas de línea de comandos (CLI). Esta imagen está optimizada para el desarrollo local, la depuración y las pruebas unitarias. Las herramientas instaladas para el desarrollo y la compilación hacen que la imagen sea relativamente grande.

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `dotnet/aspnet`

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/aspnet`

::: moniker-end

   En el ejemplo se usa esta imagen para ejecutar la aplicación. La imagen contiene el entorno de ejecución y las bibliotecas de ASP.NET Core y está optimizada para la ejecución de aplicaciones en producción. Diseñada para acelerar la implementación y el inicio de las aplicaciones, la imagen es relativamente pequeña, de forma que se optimiza el rendimiento de la red desde Docker Registry hasta el host de Docker. Solo se copian en el contenedor los archivos binarios y el contenido necesario para ejecutar una aplicación. El contenido está listo para ejecutarse, lo que permite el tiempo más rápido desde `docker run` hasta el inicio de la aplicación. En el modelo de Docker no se necesita compilación de código dinámico.

## <a name="prerequisites"></a>Requisitos previos

::: moniker range=">= aspnetcore-5.0"

* [.NET SDK 5.0](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* [SDK de .NET Core 3.1](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [SDK de .NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core)

::: moniker-end

* Cliente de Docker 18.03 o posterior

  * Distribuciones de Linux
    * [CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [Debian](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [macOS](https://docs.docker.com/docker-for-mac/install/)
  * [Windows](https://docs.docker.com/docker-for-windows/install/)

* [Git](https://git-scm.com/download)

## <a name="download-the-sample-app"></a>Descarga de la aplicación de ejemplo

* Para descargar el ejemplo, clone el [repositorio de Docker de .NET](https://github.com/dotnet/dotnet-docker): 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a>Probar la aplicación localmente

* Vaya a la carpeta de proyecto en *dotnet-docker/samples/aspnetapp/aspnetapp*.

* Ejecute el siguiente comando para compilar y ejecutar localmente la aplicación:

  ```dotnetcli
  dotnet run
  ```

* Vaya a `http://localhost:5000` en un explorador para probar la aplicación.

* Presione Ctrl+C en el símbolo del sistema para detener la aplicación.

## <a name="run-in-a-linux-container"></a>Ejecución en un contenedor de Linux

* En el cliente de Docker, [cambie a contenedores de Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

* Vaya a la carpeta de Dockerfile en *dotnet-docker/samples/aspnetapp*.

* Ejecute los siguientes comandos para compilar y ejecutar el ejemplo en Docker:

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  Los argumentos del comando `build`:
  * Asigne a la imagen el nombre aspnetapp.
  * Busque el archivo Dockerfile en la carpeta actual (el punto al final).

  Los argumentos del comando de ejecución:
  * Asigne un pseudo-TTY y manténgalo abierto aunque no esté asociado. (El mismo efecto que `--interactive --tty`).
  * Quite automáticamente el contenedor cuando se cierre.
  * Asigne al puerto 5000 de la máquina local el puerto 80 en el contenedor.
  * Asigne al contenedor el nombre aspnetcore_sample.
  * Especifique la imagen aspnetapp.

* Vaya a `http://localhost:5000` en un explorador para probar la aplicación.

## <a name="run-in-a-windows-container"></a>Ejecución en un contenedor de Windows

* En el cliente de Docker, [cambie a los contenedores de Windows](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

Vaya a la carpeta de archivos de Docker en `dotnet-docker/samples/aspnetapp`.

* Ejecute los siguientes comandos para compilar y ejecutar el ejemplo en Docker:

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* Para contenedores de Windows, necesita la dirección IP del contenedor (no sirve ir a `http://localhost:5000`):
  * Abra otro símbolo del sistema.
  * Ejecute `docker ps` para ver los contenedores en ejecución. Compruebe que aparece el contenedor "aspnetcore_sample".
  * Ejecute `docker exec aspnetcore_sample ipconfig` para mostrar la dirección IP del contenedor. La salida del comando es similar a este ejemplo:

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* Copie la dirección IPv4 (por ejemplo, 172.29.245.43) del contenedor y péguela en la barra de direcciones del explorador para probar la aplicación.

## <a name="build-and-deploy-manually"></a>Compilaciones e implementaciones manuales

En algunos escenarios, puede que quiera implementar una aplicación en un contenedor mediante la copia de los recursos que son necesarios en tiempo de ejecución. En esta sección se muestra cómo realizar una implementación manual.

* Vaya a la carpeta de proyecto en *dotnet-docker/samples/aspnetapp/aspnetapp*.

* Ejecute el comando [dotnet publish](/dotnet/core/tools/dotnet-publish):

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  Los argumentos del comando:
  * Compile la aplicación en modo de versión (el valor predeterminado es modo de depuración).
  * Cree los recursos en la carpeta *publicada*.

* Ejecute la aplicación.

  * Windows:

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * Linux:

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* Vaya a `http://localhost:5000` para ver la página principal.

Para usar la aplicación publicada manualmente en un contenedor de Docker, cree un nuevo *Dockerfile* y use el comando `docker build .` para compilar el contenedor.

::: moniker range=">= aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>El archivo Dockerfile

Aquí el archivo *Dockerfile* se usa con el comando `docker build` que ejecutó anteriormente.  Se usa `dotnet publish` de la misma manera que en esta sección para realizar compilaciones e implementaciones.  

```dockerfile
# https://hub.docker.com/_/microsoft-dotnet
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /source

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /source/aspnetapp
RUN dotnet publish -c release -o /app --no-restore

# final stage/image
FROM mcr.microsoft.com/dotnet/aspnet:5.0
WORKDIR /app
COPY --from=build /app ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

En el archivo *Dockerfile* anterior, los archivos `*.csproj` se copian y se restauran como *capas* distintas. Cuando el comando `docker build` compila una imagen, usa una caché integrada. Si los archivos `*.csproj` no han cambiado desde la última vez que se ejecutó el comando `docker build`, no es necesario volver a ejecutar el comando `dotnet restore`. En su lugar, se vuelve a usar la caché integrada para la capa `dotnet restore` correspondiente. Para obtener más información, vea [Procedimientos recomendados para escribir archivos Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>El archivo Dockerfile

Aquí el archivo *Dockerfile* se usa con el comando `docker build` que ejecutó anteriormente.  Se usa `dotnet publish` de la misma manera que en esta sección para realizar compilaciones e implementaciones.  

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

Como se indicó en el archivo Dockerfile anterior, los archivos `*.csproj` se copian y se restauran como *capas* distintas. Cuando el comando `docker build` compila una imagen, usa una caché integrada. Si los archivos `*.csproj` no han cambiado desde la última vez que se ejecutó el comando `docker build`, no es necesario volver a ejecutar el comando `dotnet restore`. En su lugar, se vuelve a usar la caché integrada para la capa `dotnet restore` correspondiente. Para obtener más información, vea [Procedimientos recomendados para escribir archivos Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>El archivo Dockerfile

Aquí el archivo *Dockerfile* se usa con el comando `docker build` que ejecutó anteriormente. Se usa `dotnet publish` de la misma manera que en esta sección para realizar compilaciones e implementaciones.  

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

## <a name="additional-resources"></a>Recursos adicionales

* [Comando de compilación de Docker](https://docs.docker.com/engine/reference/commandline/build)
* [Comando de ejecución de Docker](https://docs.docker.com/engine/reference/commandline/run)
* [Ejemplo de Docker de ASP.NET Core](https://github.com/dotnet/dotnet-docker) (el usado en este tutorial).
* [Configurar ASP.NET Core para trabajar con servidores proxy y equilibradores de carga](../proxy-load-balancer.md)
* [Working with Visual Studio Docker Tools](./visual-studio-tools-for-docker.md) (Trabajo con Visual Studio Docker Tools)
* [Depuración con Visual Studio Code](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [GC mediante Docker y contenedores pequeños](xref:performance/memory#sc)

## <a name="next-steps"></a>Pasos siguientes

El repositorio de Git que contiene la aplicación de ejemplo también incluye documentación. Para información general de los recursos disponibles en el repositorio, consulte [el archivo Léame](https://github.com/dotnet/dotnet-docker/blob/main/samples/aspnetapp/README.md). En concreto, vea cómo implementar HTTPS:

> [!div class="nextstepaction"]
> [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md) (Desarrollo de aplicaciones ASP.NET Core con Docker a través de HTTPS)
