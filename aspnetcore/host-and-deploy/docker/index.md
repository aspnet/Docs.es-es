---
title: Hospedar ASP.NET Core en contenedores de Docker
author: rick-anderson
description: Descubra vínculos a recursos para aprender a hospedar aplicaciones de ASP.NET Core en contenedores de Docker.
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
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
uid: host-and-deploy/docker/index
ms.openlocfilehash: 6b4b011314be2481e6e71d7782fff6ee99cedb9a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059785"
---
# <a name="host-aspnet-core-in-docker-containers"></a>Hospedar ASP.NET Core en contenedores de Docker

Tiene a su disposición los siguientes artículos para aprender a hospedar aplicaciones de ASP.NET Core en Docker:

[Introducción a los contenedores y Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
Obtenga información sobre la inclusión en contenedores, un enfoque de desarrollo de software en el que una aplicación o un servicio, sus dependencias y su configuración se empaquetan como una imagen de contenedor. Puede probar la imagen y, después, implementarla en un host.

[¿Qué es Docker?](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
Descubra Docker, un proyecto de código abierto para automatizar la implementación de aplicaciones como contenedores portátiles y autosuficientes que se pueden ejecutar en la nube o localmente.

[Terminología de Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
Conozca los términos y las definiciones de la tecnología de Docker.

[Contenedores, imágenes y registros de Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
Descubra cómo se almacenan las imágenes de contenedor de Docker en un registro de imágenes para la implementación coherente en los entornos.

<xref:host-and-deploy/docker/building-net-docker-images> Obtenga información sobre cómo compilar una aplicación de ASP.NET Core y aplicarle Docker. Explore las imágenes de Docker que mantiene Microsoft y examine los casos de uso.

[Herramientas de contenedor de Visual Studio](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
Descubra la manera en que Visual Studio admite la compilación, la depuración y la ejecución de aplicaciones de ASP.NET Core destinadas a .NET Framework o .NET Core en Docker para Windows. Se admiten contenedores de Windows y Linux.

[Publicación en Azure Container Registry](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
Obtenga información sobre cómo usar la extensión de herramientas de contenedor de Visual Studio para implementar una aplicación de ASP.NET Core en un host de Docker en Azure mediante PowerShell.

[Configurar ASP.NET Core para trabajar con servidores proxy y equilibradores de carga](xref:host-and-deploy/proxy-load-balancer)  
Podría ser necesario realizar una configuración adicional para las aplicaciones hospedadas detrás de servidores proxy y equilibradores de carga. El proceso de pasar solicitudes a través de un proxy suele ocultar información de la solicitud original, como la dirección IP de cliente y el esquema. Podría ser necesario reenviar manualmente a la aplicación cierta información de la solicitud.

[GC mediante Docker y contenedores pequeños](xref:performance/memory#sc) Trata de la selección de GC con contenedores pequeños.