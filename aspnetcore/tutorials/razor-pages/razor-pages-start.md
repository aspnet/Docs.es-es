---
title: 'Tutorial: Introducción a Razor Pages en ASP.NET Core'
author: rick-anderson
description: En esta serie de tutoriales se muestra cómo usar Razor Pages en ASP.NET Core. Aprenda a crear un modelo, generar código para Razor Pages, usar Entity Framework Core y SQL Server para el acceso a datos, agregar la funcionalidad de búsqueda, agregar validación de entrada y usar migraciones para actualizar el modelo.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 77b230f14b4eef60d771daf8fe09288a9dd3c69c
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212995"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>Tutorial: Introducción a Razor Pages en ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"
Este es el primer tutorial de una serie en la que se enseñan los aspectos básicos de la compilación de una aplicación web de Razor Pages de ASP.NET Core.

[!INCLUDE[](~/includes/advancedRP.md)]

Al final de la serie, tendrá una aplicación que puede administrar una base de datos de películas.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

En este tutorial ha:

> [!div class="checklist"]
> * Creará una aplicación web de Razor Pages.
> * Ejecutar la aplicación.
> * Examinar los archivos de proyecto.

Al final de este tutorial, tendrá una aplicación web de Razor Pages que compilará en los tutoriales posteriores.

![Página Inicio o Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Requisitos previos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Creación de una aplicación web de Razor Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.
* Cree una nueva aplicación web de ASP.NET Core y seleccione **Siguiente**.
  ![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/np_2.1.png)
* Asigne al proyecto el nombre **RazorPagesMovie**. Es importante asignarle el nombre *RazorPagesMovie* al proyecto para que los espacios de nombres coincidan al copiar y pegar el código.
  ![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/config.png)

* Seleccione **ASP.NET Core 3.1** en la lista desplegable, después **Aplicación web** y, por último, **Crear**.

![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/3/npx.png)

  Se crea el proyecto de inicio siguiente:

  ![Explorador de soluciones](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Cambie al directorio (`cd`) que contiene el proyecto.

* Ejecute los comandos siguientes:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * El comando `dotnet new` crea un proyecto de Razor Pages en la carpeta *RazorPagesMovie*.
  * El comando `code` abre la carpeta *RazorPagesMovie* en la instancia actual de Visual Studio Code.

* Cuando el icono de llama de OmniSharp de la barra de estado se pone en verde, aparece un cuadro de diálogo en el que se le indicará que **faltan los recursos necesarios para compilar y depurar en RazorPagesMovie. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?). Seleccione **Sí**.

  Un directorio *.vscode*, que contiene archivos *launch.json* y *tasks.json*, se agrega al directorio raíz del proyecto.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Seleccione **Archivo** > **Nueva solución**.

  ![macOS: Nueva solución](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web** > **Siguiente**. En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web** > **Siguiente**.

  ![Selección de plantilla de aplicación web de macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* En el cuadro de diálogo **Configure your new Web Application** (Configurar la nueva aplicación web):

  * Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.
  * Si se presenta una opción para seleccionar una **plataforma de destino**, seleccione la versión 3.x más reciente.

  Seleccione **Siguiente**.

* Asigne al proyecto el nombre **RazorPagesMovie** y, luego, seleccione **Crear**.

  ![Nombre del proyecto en macOS](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Ejecutar la aplicación

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>Examen de los archivo del proyecto

He aquí un resumen de las principales carpetas y archivos del proyecto con los que va a trabajar en los próximos tutoriales.

### <a name="pages-folder"></a>Carpeta Pages

Contiene páginas de Razor y archivos auxiliares. Cada página de Razor consta de un par de archivos:

* Un archivo *.cshtml* que contiene marcado HTML con código de C# que usa sintaxis de Razor.
* Archivo *. cshtml.cs* que contiene C# código que controla los eventos de página.

Los archivos auxiliares tienen nombres que comienzan con un carácter de subrayado. Por ejemplo, el archivo *_Layout.cshtml* configura los elementos de la interfaz de usuario comunes a todas las páginas. Este archivo configura el menú de navegación de la parte superior de la página y el aviso de copyright de la parte inferior de la página. Para obtener más información, vea <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Carpeta wwwroot

Contiene los archivos estáticos, como los archivos HTML, los archivos de JavaScript y los archivos CSS. Para obtener más información, vea <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Contiene los datos de configuración, como las cadenas de conexión. Para obtener más información, vea <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Contiene el punto de entrada del programa. Para obtener más información, vea <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Contiene código que configura el comportamiento de la aplicación. Para obtener más información, vea <xref:fundamentals/startup>.

## <a name="next-steps"></a>Pasos siguientes

Pase al siguiente tutorial de la serie:

> [!div class="step-by-step"]
> [Agregar un modelo](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

Este es el primer tutorial de una serie. En la [serie](xref:tutorials/razor-pages/index) se enseñan los conceptos básicos de la compilación de una aplicación web de Razor Pages en ASP.NET Core.

[!INCLUDE[](~/includes/advancedRP.md)]

Al final de la serie, tendrá una aplicación que puede administrar una base de datos de películas.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

En este tutorial ha:

> [!div class="checklist"]
> * Creará una aplicación web de Razor Pages.
> * Ejecutar la aplicación.
> * Examinar los archivos de proyecto.

Al final de este tutorial, tendrá una aplicación web de Razor Pages que compilará en los tutoriales posteriores.

![Página Inicio o Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Requisitos previos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Creación de una aplicación web de Razor Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.

* Cree una nueva aplicación web de ASP.NET Core y seleccione **Siguiente**.

  ![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* Asigne al proyecto el nombre **RazorPagesMovie**. Es importante asignarle el nombre *RazorPagesMovie* al proyecto para que los espacios de nombres coincidan al copiar y pegar el código.

  ![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/config.png)

* Seleccione **ASP.NET Core 2.2** en la lista desplegable, después **Aplicación web** y, por último, **Crear**.

![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  Se crea el proyecto de inicio siguiente:

  ![Explorador de soluciones](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Cambie al directorio (`cd`) que contiene el proyecto.

* Ejecute los comandos siguientes:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * El comando `dotnet new` crea un proyecto de Razor Pages en la carpeta *RazorPagesMovie*.
  * El comando `code` abre la carpeta *RazorPagesMovie* en la instancia actual de Visual Studio Code.

* Cuando el icono de llama de OmniSharp de la barra de estado se pone en verde, aparece un cuadro de diálogo en el que se le indicará que **faltan los recursos necesarios para compilar y depurar en RazorPagesMovie. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?). Seleccione **Sí**.

  Un directorio *.vscode*, que contiene archivos *launch.json* y *tasks.json*, se agrega al directorio raíz del proyecto.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Seleccione **Archivo** > **Nueva solución**.

![macOS: Nueva solución](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* En las versiones de Visual Studio para Mac anteriores a 8.6, seleccione **.NET Core** > **Aplicación** > **Aplicación web** > **Siguiente**. En la versión 8.6 o posteriores, seleccione **Web y consola** > **Aplicación** > **Aplicación web** > **Siguiente**.

* En el cuadro de diálogo **Configure your new Web Application** (Configurar la nueva aplicación web):

  * Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**.
  * Si se presenta una opción para seleccionar una **plataforma de destino**, seleccione la versión 2.x más reciente.

  Seleccione **Siguiente**.

* Asigne al proyecto el nombre **RazorPagesMovie** y, luego, seleccione **Crear**.

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Ejecutar la aplicación

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Presione Ctrl+F5 para ejecutarla sin el depurador.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) y ejecuta la aplicación. En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`). Esto es así porque `localhost` es el nombre de host estándar del equipo local. Localhost solo sirve las solicitudes web del equipo local. Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.

* En la página principal de la aplicación, seleccione **Aceptar** para dar su consentimiento al seguimiento.

  Esta aplicación no realiza un seguimiento de la información personal, pero la plantilla del proyecto incluye la función de consentimiento en caso de que sea necesaria para cumplir con el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr) de la Unión Europea.

  ![Página Inicio o Índice](razor-pages-start/_static/homeGDPR2.2.png)

  En la siguiente imagen se muestra la aplicación tras haber dado su consentimiento al seguimiento:

  ![Página Inicio o Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Presione **Ctrl+F5** para ejecutar sin el depurador.

  Visual Studio Code inicia [Kestrel](xref:fundamentals/servers/kestrel) y un explorador, y se desplaza hasta `http://localhost:5001`. En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`). Esto es así porque `localhost` es el nombre de host estándar del equipo local. Localhost solo sirve las solicitudes web del equipo local.

* En la página principal de la aplicación, seleccione **Aceptar** para dar su consentimiento al seguimiento.

  Esta aplicación no realiza un seguimiento de la información personal, pero la plantilla del proyecto incluye la función de consentimiento en caso de que sea necesaria para cumplir con el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr) de la Unión Europea.

  ![Página Inicio o Índice](razor-pages-start/_static/homeGDPR2.2.png)

  En la siguiente imagen se muestra la aplicación tras haber dado su consentimiento al seguimiento:

  ![Página Inicio o Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Presione **Cmd-Opt-F5** para realizar la ejecución sin el depurador.

  Visual Studio iniciará [Kestrel](xref:fundamentals/servers/kestrel) y un explorador, y se desplazará a `http://localhost:5001`.

* En la página principal de la aplicación, seleccione **Aceptar** para dar su consentimiento al seguimiento.

  Esta aplicación no realiza un seguimiento de la información personal, pero la plantilla del proyecto incluye la función de consentimiento en caso de que sea necesaria para cumplir con el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr) de la Unión Europea.

  ![Página Inicio o Índice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  En la siguiente imagen se muestra la aplicación tras haber dado su consentimiento al seguimiento:

  ![Página Inicio o Índice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Examen de los archivo del proyecto

He aquí un resumen de las principales carpetas y archivos del proyecto con los que va a trabajar en los próximos tutoriales.

### <a name="pages-folder"></a>Carpeta Pages

Contiene páginas de Razor y archivos auxiliares. Cada página de Razor consta de un par de archivos:

* Un archivo *.cshtml* que contiene marcado HTML con código de C# que usa sintaxis de Razor.
* Archivo *. cshtml.cs* que contiene C# código que controla los eventos de página.

Los archivos auxiliares tienen nombres que comienzan con un carácter de subrayado. Por ejemplo, el archivo *_Layout.cshtml* configura los elementos de la interfaz de usuario comunes a todas las páginas. Este archivo configura el menú de navegación de la parte superior de la página y el aviso de copyright de la parte inferior de la página. Para obtener más información, vea <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Carpeta wwwroot

Contiene los archivos estáticos, como los archivos HTML, los archivos de JavaScript y los archivos CSS. Para obtener más información, vea <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Contiene los datos de configuración, como las cadenas de conexión. Para obtener más información, vea <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Contiene el punto de entrada del programa. Para obtener más información, vea <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Contiene código que configura el comportamiento de la aplicación, como, por ejemplo, si se requiere consentimiento para las cookies. Para obtener más información, vea <xref:fundamentals/startup>.

## <a name="additional-resources"></a>Recursos adicionales

* [Versión en YouTube de este tutorial](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>Pasos siguientes

Pase al siguiente tutorial de la serie:

> [!div class="step-by-step"]
> [Agregar un modelo](xref:tutorials/razor-pages/model)

::: moniker-end
