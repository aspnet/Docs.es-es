---
title: Publicación de una aplicación ASP.NET Core en IIS
author: rick-anderson
description: Obtenga información sobre cómo hospedar una aplicación ASP.NET Core en un servidor IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
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
uid: tutorials/publish-to-iis
ms.openlocfilehash: 0f70b5f12b9097f8710c9641404b3e085968fc3f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "97753158"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a>Publicación de una aplicación ASP.NET Core en IIS

En este tutorial se muestra cómo hospedar una aplicación ASP.NET Core en un servidor IIS.

En este tutorial se describen los temas siguientes:

> [!div class="checklist"]
> * Instalación del conjunto de hospedaje de .NET Core en Windows Server
> * Creación de un sitio de IIS en el Administrador de IIS
> * Implementación de una aplicación ASP.NET Core

## <a name="prerequisites"></a>Requisitos previos

* El [SDK de .NET Core](/dotnet/core/sdk) instalado en el equipo de desarrollo.
* Windows Server configurado con el rol de servidor **Servidor web (IIS)** . Si el servidor no está configurado para hospedar sitios web con IIS, siga las instrucciones de la sección *Configuración de IIS* del artículo <xref:host-and-deploy/iis/index#iis-configuration> y, después, vuelva a este tutorial.

> [!WARNING]
> **La configuración de IIS y la seguridad de los sitios web implican conceptos que no se describen en este tutorial.** Consulte las instrucciones de IIS en [la documentación de Microsoft IIS](https://www.iis.net/) y el [artículo de ASP.NET Core sobre hospedaje con IIS](xref:host-and-deploy/iis/index) antes de hospedar aplicaciones de producción en IIS.
>
> Entre los escenarios importantes para el hospedaje de IIS que no se describen en este tutorial se incluyen los siguientes:
>
> * [Creación de un subárbol del Registro para la protección de datos de ASP.NET Core](xref:host-and-deploy/iis/advanced#data-protection)
> * [Configuración de la lista de control de acceso (ACL) del grupo de aplicaciones](xref:host-and-deploy/iis/advanced#application-pool-identity)
> * Para centrarse en los conceptos de implementación de IIS, en este tutorial se implementa una aplicación sin seguridad HTTPS configurada en IIS. Para obtener más información sobre cómo hospedar una aplicación habilitada para el protocolo HTTPS, vea los temas de seguridad en la sección [Recursos adicionales](#additional-resources) de este artículo. En el artículo <xref:host-and-deploy/iis/index> se proporciona más información sobre cómo hospedar aplicaciones ASP.NET Core.

## <a name="install-the-net-core-hosting-bundle"></a>Instalación del conjunto de hospedaje de .NET Core

Instale el *conjunto de hospedaje de .NET Core* en el servidor IIS. El lote instala .NET Core Runtime, .NET Core Library y el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module). El módulo permite que las aplicaciones ASP.NET Core se ejecuten detrás de IIS.

Descargue al instalador mediante el vínculo siguiente:

[Instalador del conjunto de hospedaje de .NET Core actual (descarga directa)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. Ejecute el instalador en el servidor IIS.

1. Reinicie el servidor o ejecute `net stop was /y`, seguido de `net start w3svc` en un shell de comandos.

## <a name="create-the-iis-site"></a>Creación del sitio de IIS

1. En el servidor IIS, cree una carpeta para que contenga los archivos y las carpetas publicados de la aplicación. En un paso posterior, la ruta de acceso de la carpeta se proporciona a IIS como la ruta de acceso física a la aplicación. Para obtener más información sobre el diseño de carpetas y archivos de implementación de una aplicación, consulte <xref:host-and-deploy/directory-structure>.

1. En Administrador de IIS, abra el nodo del servidor en el panel **Conexiones**. Haga clic con el botón derecho en la carpeta **Sitios**. Haga clic en **Agregar sitio web** en el menú contextual.

1. Proporcione el **Nombre del sitio** y establezca la **Ruta de acceso física** a la carpeta de implementación de la aplicación que ha creado. Proporcione la configuración de **Enlace** y seleccione **Aceptar** para crear el sitio web.

   > [!WARNING]
   > Los enlaces de carácter comodín de nivel superior (`http://*:80/` y `http://+:80`) **no** se deben usar. Los enlaces de carácter comodín de nivel superior pueden exponer su aplicación a vulnerabilidades de seguridad. Esto se aplica tanto a los caracteres comodín fuertes como a los débiles. Use nombres de host explícitos en lugar de caracteres comodín. Los enlaces de carácter comodín de subdominio (por ejemplo, `*.mysub.com`) no suponen este riesgo de seguridad si se controla todo el dominio primario (a diferencia de `*.com`, que sí es vulnerable). Vea la [sección 5.4 de RFC 7230](https://tools.ietf.org/html/rfc7230#section-5.4) para obtener más información.

1. Confirme que la identidad del modelo de proceso tiene los permisos adecuados.

   Si cambia la identidad predeterminada del grupo de aplicaciones (**Modelo de proceso** >  **Identity** ) de `ApplicationPoolIdentity` a otra identidad, compruebe que la nueva tenga los permisos necesarios para acceder a la carpeta de la aplicación, la base de datos y otros recursos necesarios. Por ejemplo, el grupo de aplicaciones requiere acceso de lectura y escritura a las carpetas donde la aplicación lee y escribe archivos.

## <a name="create-an-aspnet-core-no-locrazor-pages-app"></a>Creación de una aplicación Razor Pages de ASP.NET Core

Siga el tutorial <xref:getting-started> para crear una aplicación Razor Pages.

## <a name="publish-and-deploy-the-app"></a>Publicar e implementar la aplicación

*Publicar una aplicación* significa generar una aplicación compilada que se puede hospedar en un servidor. *Implementar una aplicación* significa trasladar la aplicación publicada a un sistema de hospedaje. El paso de publicación lo controla el [SDK de .NET Core](/dotnet/core/sdk), mientras que el paso de implementación se puede controlar mediante distintos enfoques. En este tutorial se adopta el enfoque de implementación de *carpetas*, donde:
 
* La aplicación se publica en una carpeta.
* El contenido de la carpeta se mueve a la carpeta del sitio de IIS (la **ruta de acceso física** al sitio en el Administrador de IIS).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Desde el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Publicar**.
1. En el cuadro de diálogo **Elegir un destino de publicación**, seleccione la opción de publicación **Carpeta**.
1. Establezca la ruta de acceso **Recurso compartido de archivos o carpeta**.
   * Si ha creado una carpeta para el sitio de IIS que está disponible en el equipo de desarrollo como un recurso compartido de red, proporcione la ruta de acceso al recurso compartido. El usuario actual debe tener acceso de escritura para publicar en el recurso compartido.
   * Si no puede realizar la implementación directamente en la carpeta del sitio de IIS en el servidor IIS, publique en una carpeta de un medio extraíble y mueva físicamente la aplicación publicada a la carpeta del sitio de IIS en el servidor, que es la **ruta de acceso física** del sitio en el Administrador de IIS. Mueva el contenido de la carpeta `bin/Release/{TARGET FRAMEWORK}/publish` a la carpeta del sitio de IIS en el servidor, que es la **ruta de acceso física** del sitio en el Administrador de IIS.
1. Seleccione el botón **Publicar**.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

1. En un shell de comandos, publique la aplicación en Configuración de versión con el comando [dotnet publish](/dotnet/core/tools/dotnet-publish):

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. Mueva el contenido de la carpeta `bin/Release/{TARGET FRAMEWORK}/publish` a la carpeta del sitio de IIS en el servidor, que es la **ruta de acceso física** del sitio en el Administrador de IIS.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Haga clic con el botón derecho en el proyecto en **Solución** y seleccione **Publicar** > **Publicación en carpeta**.
1. Establezca la ruta de acceso **Elegir una carpeta**.
   * Si ha creado una carpeta para el sitio de IIS que está disponible en el equipo de desarrollo como un recurso compartido de red, proporcione la ruta de acceso al recurso compartido. El usuario actual debe tener acceso de escritura para publicar en el recurso compartido.
   * Si no puede realizar la implementación directamente en la carpeta del sitio de IIS en el servidor IIS, publique en una carpeta de un medio extraíble y mueva físicamente la aplicación publicada a la carpeta del sitio de IIS en el servidor, que es la **ruta de acceso física** del sitio en el Administrador de IIS. Mueva el contenido de la carpeta `bin/Release/{TARGET FRAMEWORK}/publish` a la carpeta del sitio de IIS en el servidor, que es la **ruta de acceso física** del sitio en el Administrador de IIS.
1. Seleccione el botón **Publicar**.

---

## <a name="browse-the-website"></a>Examinar el sitio web

Se puede acceder a la aplicación en un explorador después de que reciba la primera solicitud. Realice una solicitud a la aplicación en el enlace de punto de conexión que ha establecido en el Administrador de IIS para el sitio.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha aprendido a:

> [!div class="checklist"]
> * Instalación del conjunto de hospedaje de .NET Core en Windows Server
> * Creación de un sitio de IIS en el Administrador de IIS
> * Implementación de una aplicación ASP.NET Core

Para obtener más información sobre cómo hospedar aplicaciones ASP.NET Core en IIS, vea el artículo Información general de IIS:

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a>Recursos adicionales

### <a name="articles-in-the-aspnet-core-documentation-set"></a>Artículos en el conjunto de documentación de ASP.NET Core

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a>Artículos relacionados con la implementación de aplicaciones ASP.NET Core

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [Publicación de una aplicación web en una carpeta mediante Visual Studio para Mac](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a>Artículos sobre la configuración HTTPS de IIS

* [Configuración de SSL en el Administrador de IIS](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [Procedimientos para configurar SSL en IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a>Artículos sobre IIS y Windows Server

* [Sitio oficial de Microsoft IIS](https://www.iis.net/)
* [Biblioteca de contenido técnico de Windows Server](/windows-server/windows-server)

### <a name="deployment-resources-for-iis-administrators"></a>Recursos de implementación para administradores de IIS

* [Documentación de IIS](/iis)
* [Introducción al Administrador de IIS en IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Implementación de aplicaciones .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

