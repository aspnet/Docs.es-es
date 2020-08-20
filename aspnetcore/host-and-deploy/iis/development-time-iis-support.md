---
title: Compatibilidad de IIS de tiempo de desarrollo en Visual Studio para ASP.NET Core
author: rick-anderson
description: Descubra la compatibilidad con la depuración de aplicaciones ASP.NET Core cuando se ejecutan con IIS en Windows Server.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: 5636abef928ed8a8122ec907ef025d55baa65127
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635364"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a>Compatibilidad de IIS de tiempo de desarrollo en Visual Studio para ASP.NET Core

Por [Sourabh Shirhatti](https://twitter.com/sshirhatti)

::: moniker range=">= aspnetcore-3.0"

En este artículo se describe la compatibilidad de [Visual Studio](https://visualstudio.microsoft.com) con la depuración de aplicaciones ASP.NET Core que se ejecutan con IIS en Windows Server. En este tema se explica cómo habilitar este escenario y configurar un proyecto.

## <a name="prerequisites"></a>Requisitos previos

* [Visual Studio para Windows](https://visualstudio.microsoft.com/downloads/).
* Carga de trabajo de **ASP.NET y desarrollo web**
* Carga de trabajo **Desarrollo multiplataforma de .NET Core**
* Certificado de seguridad X.509 (para compatibilidad con HTTPS)

## <a name="enable-iis"></a>Habilitar IIS

1. En Windows, navegue a **Panel de control** > **Programas** > **Programas y características** > **Activar o desactivar las características de Windows** (lado izquierdo de la pantalla).
1. Active la casilla **Internet Information Services**. Seleccione **Aceptar**.

La instalación de IIS puede requerir un reinicio del sistema.

## <a name="configure-iis"></a>Configurar IIS

IIS debe tener un sitio web configurado con lo siguiente:

* **Nombre de host**: por lo general, el **sitio web predeterminado** se usa con un **nombre de host** de `localhost`. Sin embargo, sirve cualquier sitio web de IIS válido con un nombre de host único.
* **Enlace de sitio**
  * Para las aplicaciones que requieran HTTPS, cree un enlace al puerto 443 con un certificado. Por lo general, se usa el **certificado de desarrollo de IIS Express**, pero cualquier certificado válido sirve.
  * Para las aplicaciones que usan HTTP, confirme la existencia de un enlace al puerto 80 o cree un enlace a dicho puerto si se trata de un sitio nuevo.
  * Utilice un enlace único para HTTP o HTTPS. **No se admite el enlace al mismo tiempo a los puertos HTTP y HTTPS.**

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Habilitación de la compatibilidad con IIS en tiempo de desarrollo en Visual Studio

1. Inicie el instalador de Visual Studio.
1. Seleccione **Modificar** para la instalación de Visual Studio que se pretende usar para la compatibilidad con IIS en tiempo de desarrollo.
1. Para la carga de trabajo de **Desarrollo de ASP.NET y web** , busque e instale el componente **Compatibilidad con IIS en tiempo de desarrollo**.

   El componente se enumera en la sección **Opcional**, en **Compatibilidad con IIS en tiempo de desarrollo**, dentro del panel **Detalles de instalación** a la derecha de las cargas de trabajo. El componente instala el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), que es un módulo de IIS nativo necesario para ejecutar aplicaciones de ASP.NET Core con IIS.

## <a name="configure-the-project"></a>Configuración del proyecto

### <a name="https-redirection"></a>Redireccionamiento de HTTPS

Para un nuevo proyecto que requiere HTTPS, seleccione la casilla **Configurar para HTTPS** en la ventana **Crear una aplicación web ASP.NET Core**. Al seleccionar la casilla, se agrega [redireccionamiento HTTPS y middleware HSTS](xref:security/enforcing-ssl) a la aplicación cuando esta se crea.

Para un proyecto existente que requiere HTTPS, use el redireccionamiento HTTPS y el middleware HSTS en `Startup.Configure`. Para obtener más información, vea <xref:security/enforcing-ssl>.

Para un proyecto que usa HTTP, el [redireccionamiento HTTPS y el middleware HSTS](xref:security/enforcing-ssl) no se agregan a la aplicación. No se requiere ninguna configuración de la aplicación.

### <a name="iis-launch-profile"></a>Perfil de inicio de IIS

Cree un nuevo perfil de inicio para agregar la compatibilidad con IIS en tiempo de desarrollo:

1. Haga clic con el botón derecho en el **Explorador de soluciones**. Haga clic en **Propiedades**. Abra la pestaña **Depurar**.
1. En **Perfil**, seleccione el botón **Nuevo**. Asigne el perfil el nombre "IIS" en la ventana emergente. Seleccione **Aceptar** para crear el perfil.
1. En **Iniciar**, seleccione **IIS** en la lista.
1. Active la casilla **Iniciar explorador** y proporcione la dirección URL del punto de conexión.

   Si la aplicación requiere HTTPS, use un punto de conexión HTTPS (`https://`). Para HTTP, use un punto de conexión HTTP (`http://`).

   Proporcione el mismo nombre de host y puerto especificados en la [configuración de IIS establecida en usos anteriores](#configure-iis), normalmente `localhost`.

   Proporcione el nombre de la aplicación al final de la dirección URL.

   Por ejemplo, `https://localhost/WebApplication1` (HTTPS) o `http://localhost/WebApplication1` (HTTP) son direcciones URL de punto de conexión válidas.
1. En la sección **Variables de entorno**, seleccione el botón **Agregar**. Proporcione una variable de entorno con un **Nombre** de `ASPNETCORE_ENVIRONMENT` y un **Valor** de `Development`.
1. En el área **Configuración del servidor web**, defina la **Dirección URL de la aplicación** con el mismo valor utilizado para la dirección URL de punto de conexión de **Iniciar el explorador**.
1. Para la configuración del **Modelo de hospedaje** de Visual Studio 2019 o posterior, seleccione **Predeterminado** para usar el modelo de hospedaje utilizado por el proyecto. Si el proyecto establece la propiedad `<AspNetCoreHostingModel>` en su archivo del proyecto, se usa el valor de la propiedad (`InProcess` o `OutOfProcess`). Si la propiedad no existe, se usa el modelo de hospedaje predeterminado de la aplicación, que está en proceso. Si la aplicación requiere una configuración del modelo de hospedaje distinta a la del modelo de hospedaje habitual de la aplicación, defina el **Modelo de hospedaje** como `In Process` o `Out Of Process`, según proceda.
1. Guarde el perfil.

Si no se usa Visual Studio, agregue manualmente un perfil de inicio al archivo [launchSettings.json](https://json.schemastore.org/launchsettings) en la carpeta *Propiedades*. El siguiente ejemplo configura el perfil para usar el protocolo HTTPS:

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

Confirme que los puntos de conexión `applicationUrl` y `launchUrl` coinciden y use el mismo protocolo que la configuración de enlace de IIS, es decir, HTTP o HTTPS.

## <a name="run-the-project"></a>Ejecución del proyecto

Ejecute Visual Studio como administrador:

* Confirme que la lista desplegable de configuración de compilación está configurada como **Depurar**.
* Establezca el botón [Iniciar depuración](/visualstudio/debugger/debugger-feature-tour) en el perfil de **IIS** y seleccione el botón para iniciar la aplicación.

Visual Studio puede solicitar un reinicio si no se ejecuta como administrador. Si es así, reinicie Visual Studio.

Si se usa un certificado de desarrollo que no es de confianza, el explorador puede pedirle que cree una excepción para un certificado de esta clase.

> [!NOTE]
> La depuración de una configuración de compilación de versión con [Solo mi código](/visualstudio/debugger/just-my-code) y las optimizaciones del compilador degradan el rendimiento. Por ejemplo, no se alcanzan los puntos de interrupción.

## <a name="additional-resources"></a>Recursos adicionales

* [Introducción al Administrador de IIS en IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

En este artículo se describe la compatibilidad de [Visual Studio](https://visualstudio.microsoft.com) con la depuración de aplicaciones ASP.NET Core que se ejecutan con IIS en Windows Server. En este tema se explica cómo habilitar este escenario y configurar un proyecto.

## <a name="prerequisites"></a>Requisitos previos

* [Visual Studio para Windows](https://visualstudio.microsoft.com/downloads/).
* Carga de trabajo de **ASP.NET y desarrollo web**
* Carga de trabajo **Desarrollo multiplataforma de .NET Core**
* Certificado de seguridad X.509 (para compatibilidad con HTTPS)

## <a name="enable-iis"></a>Habilitar IIS

1. En Windows, navegue a **Panel de control** > **Programas** > **Programas y características** > **Activar o desactivar las características de Windows** (lado izquierdo de la pantalla).
1. Active la casilla **Internet Information Services**. Seleccione **Aceptar**.

La instalación de IIS puede requerir un reinicio del sistema.

## <a name="configure-iis"></a>Configurar IIS

IIS debe tener un sitio web configurado con lo siguiente:

* **Nombre de host**: por lo general, el **sitio web predeterminado** se usa con un **nombre de host** de `localhost`. Sin embargo, sirve cualquier sitio web de IIS válido con un nombre de host único.
* **Enlace de sitio**
  * Para las aplicaciones que requieran HTTPS, cree un enlace al puerto 443 con un certificado. Por lo general, se usa el **certificado de desarrollo de IIS Express**, pero cualquier certificado válido sirve.
  * Para las aplicaciones que usan HTTP, confirme la existencia de un enlace al puerto 80 o cree un enlace a dicho puerto si se trata de un sitio nuevo.
  * Utilice un enlace único para HTTP o HTTPS. **No se admite el enlace al mismo tiempo a los puertos HTTP y HTTPS.**

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Habilitación de la compatibilidad con IIS en tiempo de desarrollo en Visual Studio

1. Inicie el instalador de Visual Studio.
1. Seleccione **Modificar** para la instalación de Visual Studio que se pretende usar para la compatibilidad con IIS en tiempo de desarrollo.
1. Para la carga de trabajo de **Desarrollo de ASP.NET y web** , busque e instale el componente **Compatibilidad con IIS en tiempo de desarrollo**.

   El componente se enumera en la sección **Opcional**, en **Compatibilidad con IIS en tiempo de desarrollo**, dentro del panel **Detalles de instalación** a la derecha de las cargas de trabajo. El componente instala el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), que es un módulo de IIS nativo necesario para ejecutar aplicaciones de ASP.NET Core con IIS.

## <a name="configure-the-project"></a>Configuración del proyecto

### <a name="https-redirection"></a>Redireccionamiento de HTTPS

Para un nuevo proyecto que requiere HTTPS, seleccione la casilla **Configurar para HTTPS** en la ventana **Crear una aplicación web ASP.NET Core**. Al seleccionar la casilla, se agrega [redireccionamiento HTTPS y middleware HSTS](xref:security/enforcing-ssl) a la aplicación cuando esta se crea.

Para un proyecto existente que requiere HTTPS, use el redireccionamiento HTTPS y el middleware HSTS en `Startup.Configure`. Para obtener más información, vea <xref:security/enforcing-ssl>.

Para un proyecto que usa HTTP, el [redireccionamiento HTTPS y el middleware HSTS](xref:security/enforcing-ssl) no se agregan a la aplicación. No se requiere ninguna configuración de la aplicación.

### <a name="iis-launch-profile"></a>Perfil de inicio de IIS

Cree un nuevo perfil de inicio para agregar la compatibilidad con IIS en tiempo de desarrollo:

1. Haga clic con el botón derecho en el **Explorador de soluciones**. Haga clic en **Propiedades**. Abra la pestaña **Depurar**.
1. En **Perfil**, seleccione el botón **Nuevo**. Asigne el perfil el nombre "IIS" en la ventana emergente. Seleccione **Aceptar** para crear el perfil.
1. En **Iniciar**, seleccione **IIS** en la lista.
1. Active la casilla **Iniciar explorador** y proporcione la dirección URL del punto de conexión.

   Si la aplicación requiere HTTPS, use un punto de conexión HTTPS (`https://`). Para HTTP, use un punto de conexión HTTP (`http://`).

   Proporcione el mismo nombre de host y puerto especificados en la [configuración de IIS establecida en usos anteriores](#configure-iis), normalmente `localhost`.

   Proporcione el nombre de la aplicación al final de la dirección URL.

   Por ejemplo, `https://localhost/WebApplication1` (HTTPS) o `http://localhost/WebApplication1` (HTTP) son direcciones URL de punto de conexión válidas.
1. En la sección **Variables de entorno**, seleccione el botón **Agregar**. Proporcione una variable de entorno con un **Nombre** de `ASPNETCORE_ENVIRONMENT` y un **Valor** de `Development`.
1. En el área **Configuración del servidor web**, defina la **Dirección URL de la aplicación** con el mismo valor utilizado para la dirección URL de punto de conexión de **Iniciar el explorador**.
1. Para la configuración del **Modelo de hospedaje** de Visual Studio 2019 o posterior, seleccione **Predeterminado** para usar el modelo de hospedaje utilizado por el proyecto. Si el proyecto establece la propiedad `<AspNetCoreHostingModel>` en su archivo del proyecto, se usa el valor de la propiedad (`InProcess` o `OutOfProcess`). Si la propiedad no existe, se usa el modelo de hospedaje predeterminado de la aplicación, que está fuera de proceso. Si la aplicación requiere una configuración del modelo de hospedaje distinta a la del modelo de hospedaje habitual de la aplicación, defina el **Modelo de hospedaje** como `In Process` o `Out Of Process`, según proceda.
1. Guarde el perfil.

Si no se usa Visual Studio, agregue manualmente un perfil de inicio al archivo [launchSettings.json](https://json.schemastore.org/launchsettings) en la carpeta *Propiedades*. El siguiente ejemplo configura el perfil para usar el protocolo HTTPS:

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

Confirme que los puntos de conexión `applicationUrl` y `launchUrl` coinciden y use el mismo protocolo que la configuración de enlace de IIS, es decir, HTTP o HTTPS.

## <a name="run-the-project"></a>Ejecución del proyecto

Ejecute Visual Studio como administrador:

* Confirme que la lista desplegable de configuración de compilación está configurada como **Depurar**.
* Establezca el botón [Iniciar depuración](/visualstudio/debugger/debugger-feature-tour) en el perfil de **IIS** y seleccione el botón para iniciar la aplicación.

Visual Studio puede solicitar un reinicio si no se ejecuta como administrador. Si es así, reinicie Visual Studio.

Si se usa un certificado de desarrollo que no es de confianza, el explorador puede pedirle que cree una excepción para un certificado de esta clase.

> [!NOTE]
> La depuración de una configuración de compilación de versión con [Solo mi código](/visualstudio/debugger/just-my-code) y las optimizaciones del compilador degradan el rendimiento. Por ejemplo, no se alcanzan los puntos de interrupción.

## <a name="additional-resources"></a>Recursos adicionales

* [Introducción al Administrador de IIS en IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
