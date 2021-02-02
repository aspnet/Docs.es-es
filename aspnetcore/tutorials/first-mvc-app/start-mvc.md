---
title: Introducción a ASP.NET Core MVC
author: rick-anderson
description: Obtenga información sobre cómo empezar a usar ASP.NET Core MVC.
ms.author: riande
ms.date: 01/20/2021
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
uid: tutorials/first-mvc-app/start-mvc
ms.custom: contperf-fy21q3
ms.openlocfilehash: aaf930eee351ed757be60f648bce88b182d52799
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710795"
---
# <a name="get-started-with-aspnet-core-mvc"></a>Introducción a ASP.NET Core MVC

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Este es el primer tutorial de una serie que muestra el desarrollo web de ASP.NET Core MVC con controladores y vistas.

Al final de la serie, tendrá una aplicación que administra y muestra datos de películas. Aprenderá a:

> [!div class="checklist"]
> * Crear una aplicación web.
> * Agregar un modelo y aplicarle scaffolding.
> * Trabajar con una base de datos.
> * Agregar búsqueda y validación.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).

## <a name="prerequisites"></a>Requisitos previos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a>Creación de una aplicación web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Inicie Visual Studio y seleccione **Crear un proyecto**.
* En el cuadro de diálogo **Crear un proyecto**, seleccione **Aplicación web ASP.NET Core** > **Siguiente**.
* En el cuadro de diálogo **Configurar su nuevo proyecto**, escriba `MvcMovie` en **Nombre del proyecto**. Es importante asignar al proyecto el nombre *MvcMovie*. Las mayúsculas tienen que coincidir con cada correspondencia de `namespace` cuando se copia el código.
* Seleccione **Crear**.
* En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione:
  * **.NET Core** y **ASP.NET Core 5.0** en los menús desplegables.
  * **Aplicación web de ASP.NET Core (Modelo-Vista-Controlador**).
  * **Crear**.

![Creación de una aplicación web de ASP.NET Core ](start-mvc/_static/mvcVS19v16.9.png)

Para obtener enfoques alternativos para crear el proyecto, vea [Creación de un proyecto nuevo en Visual Studio](/visualstudio/ide/create-new-project).

Visual Studio usó la plantilla de proyecto predeterminada para el proyecto de MVC creado. El proyecto creado:

* Es una aplicación de trabajo.
* Es un proyecto básico de inicio.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Para realizar el tutorial debe estar familiarizado con VS Code. Para más información, vea [Introducción a VS Code](https://code.visualstudio.com/docs) y [Ayuda de Visual Studio Code](#visual-studio-code-help).

* Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Cambie al directorio (`cd`) que contendrá el proyecto.
* Ejecute el siguiente comando:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Se muestra un cuadro de diálogo con el texto **Required assets to build and debug are missing from 'MvcMovie'. Add them?** (Faltan los activos necesarios para compilar y depurar en "MvcMovie". ¿Desea agregarlos?), seleccione **Sí**.

  * `dotnet new mvc -o MvcMovie`; Crea un nuevo proyecto de ASP.NET Core MVC en la carpeta *MvcMovie*.
  * `code -r MvcMovie`: carga el archivo de proyecto *MvcMovie.csproj* en Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Seleccione **Archivo** > **Nueva solución**.

  ![macOS: Nueva solución](start-mvc/_static/new_project_vsmac.png)

* En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente**. En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente**.

  ![Selección de plantilla de aplicación web de macOS](start-mvc/_static/web_app_template_vsmac.png)

* En el cuadro de diálogo **Configure your new Web Application** (Configurar la nueva aplicación web):

  * Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.
  * Si se presenta una opción para seleccionar una **plataforma de destino**, seleccione la versión 5.x más reciente.
  * Seleccione **Siguiente**.

* Asigne el nombre **MvcMovie** al proyecto y, después, seleccione **Crear**.

  ![Nombre del proyecto en macOS](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>Ejecutar la aplicación

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Seleccione Ctrl + F5 para ejecutar la aplicación sin el depurador.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio:

  * Inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).
  * Ejecuta la aplicación.

  En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`). El nombre de host estándar del equipo local es `localhost`. Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.

El inicio de la aplicación sin depuración seleccionando Ctrl + F5 le permite:

* Modifique el código.
* Guarde el archivo.
* Actualizar rápidamente el explorador y ver los cambios en el código.

Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el elemento de menú **Depurar**:

![Menú Depurar](start-mvc/_static/debug_menu50.png)

Puede depurar la aplicación seleccionando el botón **IIS Express**.

![IIS Express](start-mvc/_static/iis_express50.png)

En la imagen siguiente se muestra la aplicación:

![Página Inicio o Índice](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Seleccione Ctrl+F5 para ejecutarla sin el depurador.

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code:

  * Inicia [Kestrel](xref:fundamentals/servers/kestrel).
  * Inicia un explorador.
  * Navega a `https://localhost:5001`.

  En la barra de direcciones aparece `localhost:port:5001` (y no algo como `example.com`). El nombre de host estándar del equipo local es `localhost`. Localhost solo sirve las solicitudes web del equipo local.

El inicio de la aplicación sin depuración seleccionando Ctrl + F5 le permite:

* Modifique el código.
* Guarde el archivo.
* Actualizar rápidamente el explorador y ver los cambios en el código.

  ![Página Inicio o Índice](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Seleccione **Ejecutar** > **Iniciar sin depurar** para iniciar la aplicación.

  Visual Studio para Mac:

  * Inicia el servidor de [Kestrel](xref:fundamentals/servers/index#kestrel).
  * Inicia un explorador.
  * Navega a `http://localhost:port`, donde *port* es un número de puerto elegido aleatoriamente.

  [!INCLUDE[](~/includes/trustCertMac.md)]

  En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`). El nombre de host estándar del equipo local es `localhost`. Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.

Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el menú **Ejecutar**.

En la imagen siguiente se muestra la aplicación:

![Página Inicio o Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

En la siguiente sección de este tutorial conocerá MVC y empezará a escribir código.

> [!div class="step-by-step"]
> [Siguiente: Incorporación de un controlador](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Este es el primer tutorial de una serie que muestra el desarrollo web de ASP.NET Core MVC con controladores y vistas.

Al final de la serie, tendrá una aplicación que administra y muestra datos de películas. Aprenderá a:

> [!div class="checklist"]
> * Crear una aplicación web.
> * Agregar un modelo y aplicarle scaffolding.
> * Trabajar con una base de datos.
> * Agregar búsqueda y validación.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).

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

* En Visual Studio, seleccione **Crear un proyecto**.

* Seleccione **Aplicación web de ASP.NET Core** > **Siguiente**.

  ![Creación de un proyecto de aplicación web de ASP.NET Core](start-mvc/_static/np_2.1.png)

* Asigne el nombre **MvcMovie** al proyecto y seleccione **Crear**. Es importante que el proyecto se llame **MvcMovie** para que, al copiar el código, coincida con el espacio de nombres.

  ![Configuración del nuevo proyecto](start-mvc/_static/config.png)

* Seleccione **Aplicación web (Modelo-Vista-Controlador)** . En las listas desplegables, seleccione **.NET Core** y **ASP.NET Core 3.1** y, después, **Crear**.

  ![Cuadro de diálogo Nuevo proyecto, .NET CORE en el panel izquierdo, Aplicación web ASP.NET Core ](start-mvc/_static/new_project30.png)

Visual Studio usó la plantilla de proyecto predeterminada para el proyecto de MVC creado. El proyecto creado:

* Es una aplicación de trabajo.
* Es un proyecto básico de inicio.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Para realizar el tutorial debe estar familiarizado con VS Code. Para más información, vea [Introducción a VS Code](https://code.visualstudio.com/docs) y [Ayuda de Visual Studio Code](#visual-studio-code-help).

* Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Cambie los directorios (`cd`) a una carpeta que contenga el proyecto.
* Ejecute el siguiente comando:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Se muestra un cuadro de diálogo con el texto **Required assets to build and debug are missing from 'MvcMovie'. Add them?** (Faltan los activos necesarios para compilar y depurar en "MvcMovie". ¿Desea agregarlos?), seleccione **Sí**.

  * `dotnet new mvc -o MvcMovie`; Crea un nuevo proyecto de ASP.NET Core MVC en la carpeta *MvcMovie*.
  * `code -r MvcMovie`: carga el archivo de proyecto *MvcMovie.csproj* en Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Seleccione **Archivo** > **Nueva solución**.

  ![macOS: Nueva solución](start-mvc/_static/new_project_vsmac.png)

* En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente**. En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web (controlador de vista de modelos)**  > **Siguiente**.

  ![Selección de plantilla de aplicación web de macOS](start-mvc/_static/web_app_template_vsmac.png)

* En el cuadro de diálogo **Configure your new Web Application** (Configurar la nueva aplicación web):

  * Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.
  * Si se presenta una opción para seleccionar una **plataforma de destino**, seleccione la versión 3.x más reciente.
  * Seleccione **Siguiente**.

* Asigne el nombre **MvcMovie** al proyecto y, después, seleccione **Crear**.

  ![Nombre del proyecto en macOS](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>Ejecutar la aplicación

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Seleccione CTRL+F5 para ejecutar la aplicación sin depurar.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio:

  * Inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).
  * Ejecuta la aplicación.

  En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`). El nombre de host estándar del equipo local es `localhost`. Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.

El inicio de la aplicación sin depuración seleccionando Ctrl + F5 le permite:

* Modifique el código.
* Guarde el archivo.
* Actualizar rápidamente el explorador y ver los cambios en el código.

Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el elemento de menú **Depurar**:

![Menú Depurar](start-mvc/_static/debug_menu.png)

Puede depurar la aplicación seleccionando el botón **IIS Express**.

![IIS Express](start-mvc/_static/iis_express.png)

En la imagen siguiente se muestra la aplicación:

![Página Inicio o Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Seleccione CTRL+F5 para ejecutar la aplicación sin depurar.

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code:

  * Inicia [Kestrel](xref:fundamentals/servers/kestrel).
  * Inicia un explorador.
  * Navega a `https://localhost:5001`.

  En la barra de direcciones aparece `localhost:port:5001` (y no algo como `example.com`). El nombre de host estándar del equipo local es `localhost`. Localhost solo sirve las solicitudes web del equipo local.

El inicio de la aplicación sin depuración seleccionando Ctrl + F5 le permite:

* Modifique el código.
* Guarde el archivo.
* Actualizar rápidamente el explorador y ver los cambios en el código.

  ![Página Inicio o Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Seleccione **Ejecutar** > **Iniciar sin depurar** para iniciar la aplicación.

  Visual Studio para Mac: inicia el servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia un explorador y navega a `http://localhost:port`, donde *port* es un número de puerto elegido aleatoriamente.

[!INCLUDE[](~/includes/trustCertMac.md)]

En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`). El nombre de host estándar del equipo local es `localhost`. Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web. Al ejecutar la aplicación verá otro puerto distinto.

Puede iniciar la aplicación en modo de depuración o en modo de no depuración desde el menú **Ejecutar**.

En la imagen siguiente se muestra la aplicación:

![Página Inicio o Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

En la siguiente sección de este tutorial conocerá MVC y empezará a escribir código.

> [!div class="step-by-step"]
> [Siguiente](adding-controller.md)

::: moniker-end
