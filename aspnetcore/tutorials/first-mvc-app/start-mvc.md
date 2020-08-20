---
title: Introducción a ASP.NET Core MVC
author: rick-anderson
description: Obtenga información sobre cómo empezar a usar ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 9d70b292a93a5d19cc25b2fc592ec88ce8262434
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629995"
---
# <a name="get-started-with-aspnet-core-mvc"></a>Introducción a ASP.NET Core MVC

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

En este tutorial se enseñan los conceptos básicos de la compilación de una aplicación web ASP.NET Core MVC.

La aplicación administra una base de datos de títulos de películas. Aprenderá a:

> [!div class="checklist"]
> * Crear una aplicación web.
> * Agregar un modelo y aplicarle scaffolding.
> * Trabajar con una base de datos.
> * Agregar búsqueda y validación.

Al final, tendrá una aplicación que le permitirá administrar y mostrar datos de películas.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>Requisitos previos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a>Creación de una aplicación web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* En Visual Studio, seleccione **Crear un proyecto**.

* Seleccione **Aplicación web ASP.NET Core** y, después, **Siguiente**.

![Nueva aplicación web de ASP.NET Core](start-mvc/_static/np_2.1.png)

* Asigne el nombre **MvcMovie** al proyecto y seleccione **Crear**. Es importante que el proyecto se llame **MvcMovie** para que, al copiar el código, coincida con el espacio de nombres.

  ![Nueva aplicación web de ASP.NET Core](start-mvc/_static/config.png)

* Seleccione **Aplicación web (Modelo-Vista-Controlador)** y, luego, **Crear**.

![Cuadro de diálogo Nuevo proyecto, .NET CORE en el panel izquierdo, Aplicación web ASP.NET Core ](start-mvc/_static/new_project30.png)

Visual Studio ha usado la plantilla predeterminada para el proyecto de MVC que acaba de crear. Si escribe un nombre de proyecto y selecciona algunas opciones, dispondrá de inmediato de una aplicación operativa. Se trata de un proyecto básico de inicio.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Para realizar el tutorial debe estar familiarizado con VS Code. Para más información, vea [Getting started with VS Code](https://code.visualstudio.com/docs) (Introducción a VS Code) y [Visual Studio Code help](#visual-studio-code-help) (Ayuda de Visual Studio Code).

* Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Cambie los directorios (`cd`) a una carpeta que contenga el proyecto.
* Ejecute el siguiente comando:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Se muestra un cuadro de diálogo con el texto **Required assets to build and debug are missing from 'MvcMovie'. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?).  Seleccione **Sí**.

  * `dotnet new mvc -o MvcMovie`: crea un nuevo proyecto de ASP.NET Core MVC en la carpeta *MvcMovie*.
  * `code -r MvcMovie`: carga el archivo de proyecto *MvcMovie.csproj* en Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Seleccione **Archivo** > **Nueva solución**.

  ![macOS: Nueva solución](start-mvc/_static/new_project_vsmac.png)

* En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente**. En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente**.

  ![Selección de plantilla de aplicación web de macOS](start-mvc/_static/web_app_template_vsmac.png)

* En el cuadro de diálogo **Configure your new Web Application** (Configurar la nueva aplicación web):

  * Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.
  * Si se presenta una opción para seleccionar una **plataforma de destino**, seleccione la versión 3.x más reciente.

  Seleccione **Siguiente**.

* Asigne el nombre **MvcMovie** al proyecto y, después, seleccione **Crear**.

  ![Nombre del proyecto en macOS](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>Ejecutar la aplicación

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Presione **Ctrl-F5** para ejecutar la aplicación en modo de no depuración.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) y ejecuta la aplicación. Tenga en cuenta que en la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`). Esto es así porque `localhost` es el nombre de host estándar del equipo local. Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.
* El inicio de la aplicación con Ctrl+F5 (modo de no depuración) permite realizar cambios en el código, guardar el archivo, actualizar el explorador y ver los cambios de código. Muchos desarrolladores prefieren usar el modo de no depuración para iniciar la aplicación rápidamente y ver los cambios.
* Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el elemento de menú **Depurar**:

  ![Menú Depurar](start-mvc/_static/debug_menu.png)

* Puede depurar la aplicación seleccionando el botón **IIS Express**.

  ![IIS Express](start-mvc/_static/iis_express.png)

  En la imagen siguiente se muestra la aplicación:

  ![Página Inicio o Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Presione Ctrl+F5 para ejecutarla sin el depurador.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code inicia [Kestrel](xref:fundamentals/servers/kestrel) y un explorador, y se desplaza hasta `https://localhost:5001`. En la barra de direcciones aparece `localhost:port:5001` (y no algo como `example.com`). Esto es así porque `localhost` es el nombre de host estándar del equipo local. Localhost solo sirve las solicitudes web del equipo local.

  El inicio de la aplicación con Ctrl+F5 (modo de no depuración) permite realizar cambios en el código, guardar el archivo, actualizar el explorador y ver los cambios de código. Muchos desarrolladores prefieren usar el modo de no depuración para actualizar la página y ver los cambios.

  ![Página Inicio o Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Seleccione **Ejecutar** > **Iniciar sin depurar** para iniciar la aplicación. Visual Studio para Mac inicia el servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia un explorador y navega a `http://localhost:port`, donde *port* es un número de puerto elegido aleatoriamente.

[!INCLUDE[](~/includes/trustCertMac.md)]

* En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`). Esto es así porque `localhost` es el nombre de host estándar del equipo local. Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web. Al ejecutar la aplicación verá otro puerto distinto.
* Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el menú **Ejecutar**.

  En la imagen siguiente se muestra la aplicación:

  ![Página Inicio o Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

En la siguiente sección de este tutorial conocerá MVC y empezará a escribir código.

> [!div class="step-by-step"]
> [Siguiente](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

En este tutorial se enseñan los conceptos básicos de la compilación de una aplicación web ASP.NET Core MVC.

La aplicación administra una base de datos de títulos de películas. Aprenderá a:

> [!div class="checklist"]
> * Crear una aplicación web.
> * Agregar un modelo y aplicarle scaffolding.
> * Trabajar con una base de datos.
> * Agregar búsqueda y validación.

Al final, tendrá una aplicación que le permitirá administrar y mostrar datos de películas.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>Requisitos previos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a>Creación de una aplicación web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* En Visual Studio, seleccione **Crear un proyecto**.

* Seleccione **Aplicación web ASP.NET Core** y, después, **Siguiente**.

![Nueva aplicación web de ASP.NET Core](start-mvc/_static/np_2.1.png)

* Asigne el nombre **MvcMovie** al proyecto y seleccione **Crear**. Es importante que el proyecto se llame **MvcMovie** para que, al copiar el código, coincida con el espacio de nombres.

  ![Nueva aplicación web de ASP.NET Core](start-mvc/_static/config.png)


* Seleccione **Aplicación web (Modelo-Vista-Controlador)** y, luego, **Crear**.

![Cuadro de diálogo Nuevo proyecto, .NET CORE en el panel izquierdo, Aplicación web ASP.NET Core ](start-mvc/_static/new_project22-21.png)

Visual Studio ha usado la plantilla predeterminada para el proyecto de MVC que acaba de crear. Si escribe un nombre de proyecto y selecciona algunas opciones, dispondrá de inmediato de una aplicación operativa. Se trata de un proyecto introductorio básico, pero es un buen punto de partida.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Para realizar el tutorial debe estar familiarizado con VS Code. Para más información, vea [Getting started with VS Code](https://code.visualstudio.com/docs) (Introducción a VS Code) y [Visual Studio Code help](#visual-studio-code-help) (Ayuda de Visual Studio Code).

* Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Cambie los directorios (`cd`) a una carpeta que contenga el proyecto.
* Ejecute el siguiente comando:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Se muestra un cuadro de diálogo con el texto **Required assets to build and debug are missing from 'MvcMovie'. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?).  Seleccione **Sí**.

  * `dotnet new mvc -o MvcMovie`: crea un nuevo proyecto de ASP.NET Core MVC en la carpeta *MvcMovie*.
  * `code -r MvcMovie`: carga el archivo de proyecto *MvcMovie.csproj* en Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Seleccione **Archivo** > **Nueva solución**.

  ![macOS: Nueva solución](./start-mvc/_static/new_project_vsmac.png)

* En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente**. En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente**.

* En el cuadro de diálogo **Configure your new Web Application** (Configurar la nueva aplicación web):

  * Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.
  * Si se presenta una opción para seleccionar una **plataforma de destino**, seleccione la versión 2.x más reciente.

  Seleccione **Siguiente**.

* Asigne el nombre **MvcMovie** al proyecto y, después, seleccione **Crear**.

---

### <a name="run-the-app"></a>Ejecutar la aplicación

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Presione **Ctrl-F5** para ejecutar la aplicación en modo de no depuración.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) y ejecuta la aplicación. Tenga en cuenta que en la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`). Esto es así porque `localhost` es el nombre de host estándar del equipo local. Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.
* El inicio de la aplicación con Ctrl+F5 (modo de no depuración) permite realizar cambios en el código, guardar el archivo, actualizar el explorador y ver los cambios de código. Muchos desarrolladores prefieren usar el modo de no depuración para iniciar la aplicación rápidamente y ver los cambios.
* Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el elemento de menú **Depurar**:

  ![Menú Depurar](start-mvc/_static/debug_menu.png)

* Puede depurar la aplicación seleccionando el botón **IIS Express**.

  ![IIS Express](start-mvc/_static/iis_express.png)

* Seleccione **Aceptar** para dar su consentimiento al seguimiento. Esta aplicación no lleva un seguimiento de la información personal. El código generado con plantilla incluye activos que sirven para cumplir el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr).

  ![Página Inicio o Índice](start-mvc/_static/privacy.png)

  En la siguiente imagen se muestra la aplicación tras haber aceptado el seguimiento:

  ![Página Inicio o Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Presione Ctrl+F5 para ejecutarla sin el depurador.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code inicia [Kestrel](xref:fundamentals/servers/kestrel) y un explorador, y se desplaza hasta `https://localhost:5001`. En la barra de direcciones aparece `localhost:port:5001` (y no algo como `example.com`). Esto es así porque `localhost` es el nombre de host estándar del equipo local. Localhost solo sirve las solicitudes web del equipo local.

  El inicio de la aplicación con Ctrl+F5 (modo de no depuración) permite realizar cambios en el código, guardar el archivo, actualizar el explorador y ver los cambios de código. Muchos desarrolladores prefieren usar el modo de no depuración para actualizar la página y ver los cambios.

* Seleccione **Aceptar** para dar su consentimiento al seguimiento. Esta aplicación no lleva un seguimiento de la información personal. El código generado con plantilla incluye activos que sirven para cumplir el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr).

  ![Página Inicio o Índice](start-mvc/_static/privacy.png)

  En la siguiente imagen se muestra la aplicación tras haber aceptado el seguimiento:

  ![Página Inicio o Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Seleccione **Ejecutar** > **Iniciar sin depurar** para iniciar la aplicación. Visual Studio para Mac inicia el servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia un explorador y navega a `http://localhost:port`, donde *port* es un número de puerto elegido aleatoriamente.

[!INCLUDE[](~/includes/trustCertMac.md)]

* En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`). Esto es así porque `localhost` es el nombre de host estándar del equipo local. Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web. Al ejecutar la aplicación verá otro puerto distinto.
* Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el menú **Ejecutar**.

* Seleccione **Aceptar** para dar su consentimiento al seguimiento. Esta aplicación no lleva un seguimiento de la información personal. El código generado con plantilla incluye activos que sirven para cumplir el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr).

  ![Página Inicio o Índice](./start-mvc/_static/output_privacy_macos.png)

  En la siguiente imagen se muestra la aplicación tras haber aceptado el seguimiento:

  ![Página Inicio o Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

En la siguiente sección de este tutorial conocerá MVC y empezará a escribir código.

> [!div class="step-by-step"]
> [Siguiente](adding-controller.md)

::: moniker-end
