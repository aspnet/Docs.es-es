---
title: Uso de ASP.NET Core SignalR con Blazor
author: guardrex
description: Cree una aplicación de chat que use ASP.NET Core SignalR con Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/25/2021
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
uid: tutorials/signalr-blazor
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: e9bb79eb5bd8f56fe8014981de76e210eb35c2de
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589535"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a>Uso de ASP.NET Core SignalR con Blazor

En este tutorial se describen los conceptos básicos de la creación de una aplicación en tiempo real mediante SignalR con Blazor. Aprenderá a:

> [!div class="checklist"]
> * Crear un proyecto Blazor
> * Adición de la biblioteca cliente de SignalR
> * Agregar un concentrador de SignalR
> * Agregar servicios de SignalR y un punto de conexión para el concentrador de SignalR
> * Agregar código de componente de Razor para chat

Al final de este tutorial, tendrá una aplicación de chat funcional.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/signalr-blazor/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Requisitos previos

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Versión 16.8 o posterior de Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **Desarrollo web y ASP.NET**
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* [Visual Studio para Mac, versión 8.8 o posterior](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Versión 16.6 o posterior de Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **Desarrollo web y ASP.NET**
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* [Visual Studio para Mac, versión 8.6 o posterior](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a>Creación de una aplicación Blazor WebAssembly hospedada

Siga las instrucciones para su elección de herramientas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Se requiere Visual Studio 16.8 o posterior y el SDK de .NET Core 5.0.0 o posterior.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> Se requiere Visual Studio 16.6 o posterior y el SDK de .NET Core 3.1.300 o posterior.

::: moniker-end

1. Cree un nuevo proyecto.

1. Seleccione **Aplicación Blazor** y luego **Siguiente**.

1. Escriba `BlazorWebAssemblySignalRApp` en el campo **Nombre del proyecto**. Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto. Seleccione **Crear**.

1. Elija la plantilla **Aplicación de Blazor WebAssembly** .

1. En **Avanzado**, active la casilla **ASP.NET Core hospedado**.

1. Seleccione **Crear**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. En un shell de comandos, ejecute el siguiente comando:

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   La opción `-ho|--hosted` crea una solución hospedada Blazor WebAssembly. Para obtener información sobre cómo configurar recursos de VS Code en la carpeta `.vscode`, consulte la guía del sistema operativo **Linux** en <xref:blazor/tooling>.

   La opción `-o|--output` crea una carpeta para la solución. Si ha creado una carpeta para la solución y el shell de comandos está abierto en esa carpeta, omita la opción `-o|--output` y el valor para crear la solución.

1. En Visual Studio Code, abra la carpeta de proyecto de la aplicación.

1. Cuando aparezca el cuadro de diálogo para agregar recursos para compilar y depurar la aplicación, seleccione **Sí**. Visual Studio Code agrega automáticamente la carpeta `.vscode` con los archivos `launch.json` y `tasks.json` generados.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Instale la versión más reciente de [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) y realice los pasos siguientes:

1. Seleccione **Archivo** > **Nueva solución** o cree un proyecto **Nuevo** desde **Ventana de inicio**.

1. En la barra lateral, seleccione **Web y consola** > **Aplicación**.

1. Elija la plantilla **Aplicación de Blazor WebAssembly** . Seleccione **Siguiente**.

1. Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**. Active la casilla **ASP.NET Core hospedado**. Seleccione **Siguiente**.

1. En el campo **Nombre del proyecto**, asigne un nombre a la aplicación `BlazorWebAssemblySignalRApp`. Seleccione **Crear**.

   Si aparece un mensaje para que confíe en el certificado de desarrollo, hágalo y continúe. Las contraseñas de usuario y de cadena de claves son necesarias para confiar en el certificado.

1. Abra el proyecto; para ello, vaya a la carpeta del proyecto y abra el archivo de solución del proyecto (`.sln`).

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

En un shell de comandos, ejecute el siguiente comando:

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

La opción `-ho|--hosted` crea una solución hospedada Blazor WebAssembly.

La opción `-o|--output` crea una carpeta para la solución. Si ha creado una carpeta para la solución y el shell de comandos está abierto en esa carpeta, omita la opción `-o|--output` y el valor para crear la solución.

---

## <a name="add-the-signalr-client-library"></a>Adición de la biblioteca cliente de SignalR

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto `BlazorWebAssemblySignalRApp.Client` y seleccione **Administrar paquetes NuGet**.

1. En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que **Origen del paquete** se ha establecido en `nuget.org`.

1. Con **Examinar** seleccionado, escriba `Microsoft.AspNetCore.SignalR.Client` en el cuadro de búsqueda.

1. En los resultados de la búsqueda, seleccione el paquete [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client). Establezca la versión para que coincida con el marco compartido de la aplicación. Seleccione **Instalar**.

1. Si aparece el cuadro de diálogo **Vista previa de los cambios**, seleccione **Aceptar**.

1. Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

En **Terminal integrado** (**Ver** > **Terminal** en la barra de herramientas), ejecute el comando siguiente:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

Para agregar una versión anterior del paquete, proporcione la opción `--version {VERSION}`, donde el marcador de posición `{VERSION}` es la versión del paquete que se va a agregar.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. En la barra lateral **Solución**, haga clic con el botón derecho en el proyecto `BlazorWebAssemblySignalRApp.Client` y, después, seleccione **Administrar paquetes NuGet**.

1. En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que la lista desplegable de origen se ha establecido en `nuget.org`.

1. Con **Examinar** seleccionado, escriba `Microsoft.AspNetCore.SignalR.Client` en el cuadro de búsqueda.

1. En los resultados de la búsqueda, active la casilla situada junto al paquete [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client). Establezca la versión para que coincida con el marco compartido de la aplicación. Seleccione **Agregar paquete**.

1. Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

En un shell de comandos de la carpeta de la solución, ejecute el siguiente comando:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

Para agregar una versión anterior del paquete, proporcione la opción `--version {VERSION}`, donde el marcador de posición `{VERSION}` es la versión del paquete que se va a agregar.

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a>Adición del paquete System.Text.Encodings.Web

*Esta sección solo se aplica a las aplicaciones para ASP.NET Core versión 3.x.*

Debido a un problema de resolución de paquetes al usar [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x en una aplicación ASP.NET Core 3.x, el proyecto `BlazorWebAssemblySignalRApp.Server` requiere una referencia al paquete para [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web). El problema subyacente se resolverá en una versión futura de la revisión de .NET 5. Para más información, vea [System.Text.Json define netcoreapp3.0 sin dependencias (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).

Para agregar [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) al proyecto `BlazorWebAssemblySignalRApp.Server` de la solución Blazor hospedada de ASP.NET Core 3.1, siga las instrucciones para elegir las herramientas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto `BlazorWebAssemblySignalRApp.Server` y seleccione **Administrar paquetes NuGet**.

1. En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que **Origen del paquete** se ha establecido en `nuget.org`.

1. Con **Examinar** seleccionado, escriba `System.Text.Encodings.Web` en el cuadro de búsqueda.

1. En los resultados de la búsqueda, seleccione el paquete [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web). Seleccione la versión del paquete que coincida con el marco compartido en uso. Seleccione **Instalar**.

1. Si aparece el cuadro de diálogo **Vista previa de los cambios**, seleccione **Aceptar**.

1. Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

En **Terminal integrado** (**Ver** > **Terminal** en la barra de herramientas), ejecute los siguientes comandos:

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

Para agregar una versión anterior del paquete, proporcione la opción `--version {VERSION}`, donde el marcador de posición `{VERSION}` es la versión del paquete que se va a agregar.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. En la barra lateral **Solución**, haga clic con el botón derecho en el proyecto `BlazorWebAssemblySignalRApp.Server` y, después, seleccione **Administrar paquetes NuGet**.

1. En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que la lista desplegable de origen se ha establecido en `nuget.org`.

1. Con **Examinar** seleccionado, escriba `System.Text.Encodings.Web` en el cuadro de búsqueda.

1. En los resultados de la búsqueda, active la casilla situada junto al paquete [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web), seleccione la versión correcta del paquete que coincida con el marco compartido en uso y seleccione **Agregar paquete**.

1. Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

En un shell de comandos de la carpeta de la solución, ejecute el siguiente comando:

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

Para agregar una versión anterior del paquete, proporcione la opción `--version {VERSION}`, donde el marcador de posición `{VERSION}` es la versión del paquete que se va a agregar.

---

::: moniker-end

## <a name="add-a-signalr-hub"></a>Agregar un concentrador de SignalR

En el proyecto `BlazorWebAssemblySignalRApp.Server`, cree una carpeta `Hubs` (plural) y agregue la siguiente clase `ChatHub` (`Hubs/ChatHub.cs`):

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>Adición de servicios y de un punto de conexión para el concentrador de SignalR

1. En el proyecto `BlazorWebAssemblySignalRApp.Server`, abra el archivo `Startup.cs`.

1. Agregue el espacio de nombres para la clase `ChatHub` en la parte superior del archivo:

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. Agregue servicios de middleware de compresión de respuesta y SignalR a `Startup.ConfigureServices`:

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. En `Startup.Configure`:

   * Use el middleware de compresión de respuesta de la parte superior de la configuración de la canalización de procesamiento.
   * Entre los puntos de conexión de los controladores y la reserva del lado cliente, agregue un punto de conexión para el concentrador.

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Agregue servicios de middleware de compresión de respuesta y SignalR a `Startup.ConfigureServices`:

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. En `Startup.Configure`:

   * Use el middleware de compresión de respuesta de la parte superior de la configuración de la canalización de procesamiento.
   * Entre los puntos de conexión de los controladores y la reserva del lado cliente, agregue un punto de conexión para el concentrador.

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a>Agregar código de componente de Razor para chat

1. En el proyecto `BlazorWebAssemblySignalRApp.Client`, abra el archivo `Pages/Index.razor`.

::: moniker range=">= aspnetcore-5.0"

1. Reemplace el marcado con el código siguiente:

   [!code-razor[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Reemplace el marcado con el código siguiente:

   [!code-razor[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>Ejecutar la aplicación

Siga las instrucciones para las herramientas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. En el **Explorador de soluciones**, seleccione el proyecto `BlazorWebAssemblySignalRApp.Server`. Presione <kbd>F5</kbd> para ejecutar la aplicación con depuración o <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutarla sin depuración.

1. Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.

1. Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje. El nombre y el mensaje se muestran en ambas páginas al instante:

   ![Aplicación de ejemplo SignalR Blazor abierta en dos ventanas del explorador en las que se muestran mensajes intercambiados.](signalr-blazor/_static/signalr-blazor-finished.png)

   Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Para obtener información sobre cómo configurar recursos de VS Code en la carpeta `.vscode`, consulte la guía del sistema operativo **Linux** en <xref:blazor/tooling>.

1. Presione <kbd>F5</kbd> para ejecutar la aplicación con depuración o <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutarla sin depuración.

1. Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.

1. Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje. El nombre y el mensaje se muestran en ambas páginas al instante:

   ![Aplicación de ejemplo SignalR Blazor abierta en dos ventanas del explorador en las que se muestran mensajes intercambiados.](signalr-blazor/_static/signalr-blazor-finished.png)

   Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. En la barra lateral **Solución**, seleccione el proyecto `BlazorWebAssemblySignalRApp.Server`. Presione <kbd>⌘</kbd>+<kbd>↩</kbd> para ejecutar la aplicación con depuración o <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> para ejecutarla sin depuración.

1. Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.

1. Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje. El nombre y el mensaje se muestran en ambas páginas al instante:

   ![Aplicación de ejemplo SignalR Blazor abierta en dos ventanas del explorador en las que se muestran mensajes intercambiados.](signalr-blazor/_static/signalr-blazor-finished.png)

   Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

1. En un shell de comandos de la carpeta de la solución, ejecute los comandos siguientes:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.

1. Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje. El nombre y el mensaje se muestran en ambas páginas al instante:

   ![Aplicación de ejemplo SignalR Blazor abierta en dos ventanas del explorador en las que se muestran mensajes intercambiados.](signalr-blazor/_static/signalr-blazor-finished.png)

   Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a>Creación de una aplicación Blazor Server

Siga las instrucciones para su elección de herramientas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Se requiere Visual Studio 16.8 o posterior y el SDK de .NET Core 5.0.0 o posterior.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> Se requiere Visual Studio 16.6 o posterior y el SDK de .NET Core 3.1.300 o posterior.

::: moniker-end

1. Cree un nuevo proyecto.

1. Seleccione **Aplicación Blazor** y luego **Siguiente**.

1. Escriba `BlazorServerSignalRApp` en el campo **Nombre del proyecto**. Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto. Seleccione **Crear**.

1. Elija la plantilla **Aplicación de Blazor Server** .

1. Seleccione **Crear**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. En un shell de comandos, ejecute el siguiente comando:

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   La opción `-o|--output` crea una carpeta para el proyecto. Si ha creado una carpeta para el proyecto y el shell de comandos está abierto en esa carpeta, omita la opción `-o|--output` y el valor para crear la solución.

1. En Visual Studio Code, abra la carpeta de proyecto de la aplicación.

1. Cuando aparezca el cuadro de diálogo para agregar recursos para compilar y depurar la aplicación, seleccione **Sí**. Visual Studio Code agrega automáticamente la carpeta `.vscode` con los archivos `launch.json` y `tasks.json` generados. Para obtener información sobre cómo configurar recursos de VS Code en la carpeta `.vscode`, consulte la guía del sistema operativo **Linux** en <xref:blazor/tooling>.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Instale la versión más reciente de [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) y realice los pasos siguientes:

1. Seleccione **Archivo** > **Nueva solución** o cree un proyecto **Nuevo** desde **Ventana de inicio**.

1. En la barra lateral, seleccione **Web y consola** > **Aplicación**.

1. Elija la plantilla **Aplicación de Blazor Server** . Seleccione **Siguiente**.

1. Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**. Seleccione **Siguiente**.

1. En el campo **Nombre del proyecto**, asigne un nombre a la aplicación `BlazorServerSignalRApp`. Seleccione **Crear**.

   Si aparece un mensaje para que confíe en el certificado de desarrollo, hágalo y continúe. Las contraseñas de usuario y de cadena de claves son necesarias para confiar en el certificado.

1. Abra el proyecto; para ello, vaya a la carpeta del proyecto y abra el archivo de solución del proyecto (`.sln`).

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

En un shell de comandos, ejecute el siguiente comando:

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

La opción `-o|--output` crea una carpeta para el proyecto. Si ha creado una carpeta para el proyecto y el shell de comandos está abierto en esa carpeta, omita la opción `-o|--output` y el valor para crear la solución.

---

## <a name="add-the-signalr-client-library"></a>Adición de la biblioteca cliente de SignalR

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto `BlazorServerSignalRApp` y seleccione **Administrar paquetes NuGet**.

1. En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que **Origen del paquete** se ha establecido en `nuget.org`.

1. Con **Examinar** seleccionado, escriba `Microsoft.AspNetCore.SignalR.Client` en el cuadro de búsqueda.

1. En los resultados de la búsqueda, seleccione el paquete [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client). Establezca la versión para que coincida con el marco compartido de la aplicación. Seleccione **Instalar**.

1. Si aparece el cuadro de diálogo **Vista previa de los cambios**, seleccione **Aceptar**.

1. Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

En **Terminal integrado** (**Ver** > **Terminal** en la barra de herramientas), ejecute el comando siguiente:

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

Para agregar una versión anterior del paquete, proporcione la opción `--version {VERSION}`, donde el marcador de posición `{VERSION}` es la versión del paquete que se va a agregar.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. En la barra lateral **Solución**, haga clic con el botón derecho en el proyecto `BlazorServerSignalRApp` y, después, seleccione **Administrar paquetes NuGet**.

1. En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que la lista desplegable de origen se ha establecido en `nuget.org`.

1. Con **Examinar** seleccionado, escriba `Microsoft.AspNetCore.SignalR.Client` en el cuadro de búsqueda.

1. En los resultados de la búsqueda, active la casilla situada junto al paquete [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client). Establezca la versión para que coincida con el marco compartido de la aplicación. Seleccione **Agregar paquete**.

1. Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

En un shell de comandos de la carpeta del proyecto, ejecute el comando siguiente:

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

Para agregar una versión anterior del paquete, proporcione la opción `--version {VERSION}`, donde el marcador de posición `{VERSION}` es la versión del paquete que se va a agregar.

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a>Adición del paquete System.Text.Encodings.Web

*Esta sección solo se aplica a las aplicaciones para ASP.NET Core versión 3.x.*

Debido a un problema de resolución de paquetes al usar [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x en una aplicación ASP.NET Core 3.x, el proyecto requiere una referencia al paquete para [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web). El problema subyacente se resolverá en una versión futura de la revisión de .NET 5. Para más información, vea [System.Text.Json define netcoreapp3.0 sin dependencias (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).

Para agregar [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) al proyecto, siga las instrucciones correspondientes a la herramienta de su elección:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto `BlazorServerSignalRApp` y seleccione **Administrar paquetes NuGet**.

1. En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que **Origen del paquete** se ha establecido en `nuget.org`.

1. Con **Examinar** seleccionado, escriba `System.Text.Encodings.Web` en el cuadro de búsqueda.

1. En los resultados de la búsqueda, seleccione el paquete [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web). Seleccione la versión del paquete que coincida con el marco compartido en uso. Seleccione **Instalar**.

1. Si aparece el cuadro de diálogo **Vista previa de los cambios**, seleccione **Aceptar**.

1. Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

En **Terminal integrado** (**Ver** > **Terminal** en la barra de herramientas), ejecute los siguientes comandos:

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

Para agregar una versión anterior del paquete, proporcione la opción `--version {VERSION}`, donde el marcador de posición `{VERSION}` es la versión del paquete que se va a agregar.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. En la barra lateral **Solución**, haga clic con el botón derecho en el proyecto `BlazorServerSignalRApp` y, después, seleccione **Administrar paquetes NuGet**.

1. En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que la lista desplegable de origen se ha establecido en `nuget.org`.

1. Con **Examinar** seleccionado, escriba `System.Text.Encodings.Web` en el cuadro de búsqueda.

1. En los resultados de la búsqueda, active la casilla situada junto al paquete [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web), seleccione la versión correcta del paquete que coincida con el marco compartido en uso y seleccione **Agregar paquete**.

1. Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

En un shell de comandos de la carpeta del proyecto, ejecute el comando siguiente:

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

Para agregar una versión anterior del paquete, proporcione la opción `--version {VERSION}`, donde el marcador de posición `{VERSION}` es la versión del paquete que se va a agregar.

---

::: moniker-end

## <a name="add-a-signalr-hub"></a>Agregar un concentrador de SignalR

Cree una carpeta `Hubs` (en plural) y agregue la siguiente clase `ChatHub` (`Hubs/ChatHub.cs`):

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>Adición de servicios y de un punto de conexión para el concentrador de SignalR

1. Abra el archivo `Startup.cs` .

1. Agregue los espacios de nombres para <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> y la clase `ChatHub` en la parte superior del archivo:

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. Agregue servicios de middleware de compresión de respuesta a `Startup.ConfigureServices`:

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. En `Startup.Configure`:

   * Use el middleware de compresión de respuesta de la parte superior de la configuración de la canalización de procesamiento.
   * Entre los puntos de conexión para asignar el concentrador Blazor y la reserva del lado cliente, agregue un punto de conexión para el concentrador.

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Agregue servicios de middleware de compresión de respuesta a `Startup.ConfigureServices`:

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. En `Startup.Configure`:

   * Use el middleware de compresión de respuesta de la parte superior de la configuración de la canalización de procesamiento.
   * Entre los puntos de conexión para asignar el concentrador Blazor y la reserva del lado cliente, agregue un punto de conexión para el concentrador.

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a>Agregar código de componente de Razor para chat

1. Abra el archivo `Pages/Index.razor` .

::: moniker range=">= aspnetcore-5.0"

1. Reemplace el marcado con el código siguiente:

   [!code-razor[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Reemplace el marcado con el código siguiente:

   [!code-razor[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>Ejecutar la aplicación

Siga las instrucciones para las herramientas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Presione <kbd>F5</kbd> para ejecutar la aplicación con depuración o <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutarla sin depuración.

1. Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.

1. Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje. El nombre y el mensaje se muestran en ambas páginas al instante:

   ![Aplicación de ejemplo SignalR Blazor abierta en dos ventanas del explorador en las que se muestran mensajes intercambiados.](signalr-blazor/_static/signalr-blazor-finished.png)

   Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Presione <kbd>F5</kbd> para ejecutar la aplicación con depuración o <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutarla sin depuración.

1. Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.

1. Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje. El nombre y el mensaje se muestran en ambas páginas al instante:

   ![Aplicación de ejemplo SignalR Blazor abierta en dos ventanas del explorador en las que se muestran mensajes intercambiados.](signalr-blazor/_static/signalr-blazor-finished.png)

   Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Presione <kbd>⌘</kbd>+<kbd>↩</kbd> para ejecutar la aplicación con depuración o <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> para ejecutarla sin depuración.

1. Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.

1. Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje. El nombre y el mensaje se muestran en ambas páginas al instante:

   ![Aplicación de ejemplo SignalR Blazor abierta en dos ventanas del explorador en las que se muestran mensajes intercambiados.](signalr-blazor/_static/signalr-blazor-finished.png)

   Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

1. En un shell de comandos de la carpeta del proyecto, ejecute los comandos siguientes:

   ```dotnetcli
   dotnet run
   ```

1. Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.

1. Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje. El nombre y el mensaje se muestran en ambas páginas al instante:

   ![Aplicación de ejemplo SignalR Blazor abierta en dos ventanas del explorador en las que se muestran mensajes intercambiados.](signalr-blazor/_static/signalr-blazor-finished.png)

   Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a:

> [!div class="checklist"]
> * Crear un proyecto Blazor
> * Adición de la biblioteca cliente de SignalR
> * Agregar un concentrador de SignalR
> * Agregar servicios de SignalR y un punto de conexión para el concentrador de SignalR
> * Agregar código de componente de Razor para chat

Para más información sobre la creación de aplicaciones de Blazor, consulte la documentación de Blazor:

> [!div class="nextstepaction"]
> <xref:blazor/index>
> [Autenticación de tokens de portador con Identity Server, WebSockets y eventos enviados por el servidor](xref:signalr/authn-and-authz#bearer-token-authentication)

## <a name="additional-resources"></a>Recursos adicionales

* <xref:signalr/introduction>
* [Negociación entre orígenes de SignalR para la autenticación](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
