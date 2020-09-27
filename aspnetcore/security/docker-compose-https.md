---
title: Hospedaje de ASP.NET Core imagen en el contenedor mediante Docker Compose con HTTPS
author: ravipal
description: Obtenga información acerca de cómo hospedar imágenes de ASP.NET Core con Docker Compose a través de HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
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
uid: security/docker-compose-https
ms.openlocfilehash: cd46fdcbe10dc0b7829fbe7eaef821889f395df4
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393709"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a>Hospedaje de imágenes de ASP.NET Core con Docker Compose a través de HTTPS


[De forma predeterminada](./enforcing-ssl.md)ASP.net Core usa https. [Https](https://en.wikipedia.org/wiki/HTTPS) se basa en los [certificados](https://en.wikipedia.org/wiki/Public_key_certificate) de confianza, identidad y cifrado.

En este documento se explica cómo ejecutar imágenes de contenedor creadas previamente con HTTPS.

Consulte [desarrollo de aplicaciones ASP.net Core con Docker a través de https](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) para escenarios de desarrollo.

Este ejemplo requiere [docker 17,06](https://docs.docker.com/release-notes/docker-ce) o posterior del [cliente de Docker](https://www.docker.com/products/docker).

## <a name="prerequisites"></a>Requisitos previos

El [SDK de .net Core 2,2](https://dotnet.microsoft.com/download) o posterior es necesario para algunas de las instrucciones de este documento.

## <a name="certificates"></a>Certificados

Se requiere un certificado de una [entidad de certificación](https://wikipedia.org/wiki/Certificate_authority) para el [hospedaje de producción](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) en un dominio. [Let's Encrypt](https://letsencrypt.org/) es una entidad de certificación que ofrece certificados gratuitos.

En este documento se usan [certificados de desarrollo autofirmados](https://wikipedia.org/wiki/Self-signed_certificate) para hospedar imágenes pregeneradas en `localhost` . Las instrucciones son similares a usar certificados de producción.

Para los certificados de producción:

* La `dotnet dev-certs` herramienta no es necesaria.
* Los certificados no tienen que almacenarse en la ubicación usada en las instrucciones. Almacene los certificados en cualquier ubicación fuera del directorio del sitio.

Las instrucciones contenidas en la sección siguiente desmontan certificados en contenedores mediante la `volumes` propiedad de *Docker-Compose. yml.* Puede agregar certificados a las imágenes de contenedor con un `COPY` comando en un *Dockerfile*, pero no se recomienda. No se recomienda copiar certificados en una imagen por los siguientes motivos:

* Dificulta el uso de la misma imagen para realizar pruebas con certificados de desarrollador.
* Dificulta el uso de la misma imagen para hospedar con certificados de producción.
* Existe un riesgo significativo de divulgación de certificados.

## <a name="starting-a-container-with-https-support-using-docker-compose"></a>Inicio de un contenedor con compatibilidad con https mediante Docker Compose

Utilice las siguientes instrucciones para la configuración del sistema operativo.

### <a name="windows-using-linux-containers"></a>Windows con contenedores de Linux

Generar certificado y configurar equipo local:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

En los comandos anteriores, reemplace `{ password here }` por una contraseña.

Cree un archivo _Docker-Compose. Debug. yml_ con el siguiente contenido:

```yaml
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
La contraseña especificada en el archivo de Docker Compose debe coincidir con la contraseña usada para el certificado.

Inicie el contenedor con ASP.NET Core configurado para HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a>macOS o Linux

Generar certificado y configurar equipo local:

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust` solo se admite en macOS y Windows. Debe confiar en los certificados en Linux de la manera que sea compatible con la distribución. Es probable que necesite confiar en el certificado en el explorador.

En los comandos anteriores, reemplace `{ password here }` por una contraseña.

Cree un archivo _Docker-Compose. Debug. yml_ con el siguiente contenido:

```yaml
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
La contraseña especificada en el archivo de Docker Compose debe coincidir con la contraseña usada para el certificado.

Inicie el contenedor con ASP.NET Core configurado para HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a>Windows con contenedores de Windows

Generar certificado y configurar equipo local:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

En los comandos anteriores, reemplace `{ password here }` por una contraseña.

Cree un archivo _Docker-Compose. Debug. yml_ con el siguiente contenido:

```yaml
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
La contraseña especificada en el archivo de Docker Compose debe coincidir con la contraseña usada para el certificado.

Inicie el contenedor con ASP.NET Core configurado para HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
