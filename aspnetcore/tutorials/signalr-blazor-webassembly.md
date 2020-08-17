---
title: Uso de ASP.NET Core SignalR con Blazor WebAssembly
author: guardrex
description: Cree una aplicación de chat que use ASP.NET Core SignalR con Blazor WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 3d7ae49142849c589a1a20c33d30e87747ad1935
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021632"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a>Uso de ASP.NET Core SignalR con Blazor WebAssembly

Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)

En este tutorial se describen los conceptos básicos de la creación de una aplicación en tiempo real mediante SignalR con Blazor WebAssembly. Aprenderá a:

> [!div class="checklist"]
> * Crear un proyecto de aplicación Blazor WebAssembly hospedado
> * Adición de la biblioteca cliente de SignalR
> * Agregar un concentrador de SignalR
> * Agregar servicios de SignalR y un punto de conexión para el concentrador de SignalR
> * Agregar código de componente de Razor para chat

Al final de este tutorial, tendrá una aplicación de chat funcional.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Requisitos previos

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

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a>Creación de un proyecto de aplicación Blazor WebAssembly hospedado

Siga las instrucciones para su elección de herramientas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!NOTE]
> Se requiere Visual Studio 16.6 o posterior y el SDK de .NET Core 3.1.300 o posterior.

1. Cree un nuevo proyecto.

1. Seleccione **Aplicación Blazor** y luego **Siguiente**.

1. Escriba `BlazorSignalRApp` en el campo **Nombre del proyecto**. Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto. Seleccione **Crear**.

1. Elija la plantilla **Aplicación de Blazor WebAssembly** .

1. En **Avanzado**, active la casilla **ASP.NET Core hospedado**.

1. Seleccione **Crear**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. En un shell de comandos, ejecute el siguiente comando:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. En Visual Studio Code, abra la carpeta de proyecto de la aplicación.

1. Cuando aparezca el cuadro de diálogo para agregar recursos para compilar y depurar la aplicación, seleccione **Sí**. Visual Studio Code agrega automáticamente la carpeta `.vscode` con los archivos `launch.json` y `tasks.json` generados.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Instale la versión más reciente de [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) y realice los pasos siguientes:

1. Seleccione **Archivo** > **Nueva solución** o cree un proyecto **Nuevo** desde **Ventana de inicio**.

1. En la barra lateral, seleccione **Web y consola** > **Aplicación**.

1. Elija la plantilla **Aplicación de Blazor WebAssembly** . Seleccione **Siguiente**.

1. Asegúrese de que la **autenticación** esté establecida en **Sin autenticación**. Active la casilla **ASP.NET Core hospedado**. Seleccione **Siguiente**.

1. En el campo **Nombre del proyecto**, asigne un nombre a la aplicación `BlazorSignalRApp`. Seleccione **Crear**.

   Si aparece un mensaje para que confíe en el certificado de desarrollo, hágalo y continúe. Las contraseñas de usuario y de cadena de claves son necesarias para confiar en el certificado.

1. Abra el proyecto; para ello, vaya a la carpeta del proyecto y abra el archivo de solución del proyecto (`.sln`).

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

En un shell de comandos, ejecute el siguiente comando:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a>Adición de la biblioteca cliente de SignalR

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto `BlazorSignalRApp.Client` y seleccione **Administrar paquetes NuGet**.

1. En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que **Origen del paquete** se ha establecido en `nuget.org`.

1. Con **Examinar** seleccionado, escriba `Microsoft.AspNetCore.SignalR.Client` en el cuadro de búsqueda.

1. En los resultados de la búsqueda, seleccione el paquete [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) y, después, seleccione **Instalar**.

1. Si aparece el cuadro de diálogo **Vista previa de los cambios**, seleccione **Aceptar**.

1. Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

En **Terminal integrado** (**Ver** > **Terminal** en la barra de herramientas), ejecute los siguientes comandos:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. En la barra lateral **Solución**, haga clic con el botón derecho en el proyecto `BlazorSignalRApp.Client` y, después, seleccione **Administrar paquetes NuGet**.

1. En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que la lista desplegable de origen se ha establecido en `nuget.org`.

1. Con **Examinar** seleccionado, escriba `Microsoft.AspNetCore.SignalR.Client` en el cuadro de búsqueda.

1. En los resultados de la búsqueda, active la casilla situada junto al paquete [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) y seleccione **Agregar paquete**.

1. Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

En un shell de comandos, ejecute los siguientes comandos:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a>Agregar un concentrador de SignalR

En el proyecto `BlazorSignalRApp.Server`, cree una carpeta `Hubs` (plural) y agregue la siguiente clase `ChatHub` (`Hubs/ChatHub.cs`):

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a>Adición de servicios y de un punto de conexión para el concentrador de SignalR

1. En el proyecto `BlazorSignalRApp.Server`, abra el archivo `Startup.cs`.

1. Agregue el espacio de nombres para la clase `ChatHub` en la parte superior del archivo:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Agregue servicios de middleware de compresión de respuesta y SignalR a `Startup.ConfigureServices`:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. En `Startup.Configure`:

   * Use el middleware de compresión de respuesta de la parte superior de la configuración de la canalización de procesamiento.
   * Entre los puntos de conexión de los controladores y la reserva del lado cliente, agregue un punto de conexión para el concentrador.

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-no-locrazor-component-code-for-chat"></a>Agregar código de componente de Razor para chat

1. En el proyecto `BlazorSignalRApp.Client`, abra el archivo `Pages/Index.razor`.

1. Reemplace el marcado con el código siguiente:

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>Ejecutar la aplicación

1. Siga las instrucciones para las herramientas:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. En el **Explorador de soluciones**, seleccione el proyecto `BlazorSignalRApp.Server`. Presione <kbd>F5</kbd> para ejecutar la aplicación con depuración o <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutarla sin depuración.

1. Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.

1. Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje. El nombre y el mensaje se muestran en ambas páginas al instante:

   ![Aplicación de ejemplo de SignalR Blazor WebAssembly abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Cuando VS Code ofrece crear un perfil de inicio para la aplicación de servidor (`.vscode/launch.json`), la entrada `program` es similar a la siguiente para apuntar al ensamblado de la aplicación (`{APPLICATION NAME}.Server.dll`):

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. Presione <kbd>F5</kbd> para ejecutar la aplicación con depuración o <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutarla sin depuración.

1. Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.

1. Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje. El nombre y el mensaje se muestran en ambas páginas al instante:

   ![Aplicación de ejemplo de SignalR Blazor WebAssembly abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. En la barra lateral **Solución**, seleccione el proyecto `BlazorSignalRApp.Server`. Presione <kbd>⌘</kbd>+<kbd>↩</kbd> para ejecutar la aplicación con depuración o <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> para ejecutarla sin depuración.

1. Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.

1. Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje. El nombre y el mensaje se muestran en ambas páginas al instante:

   ![Aplicación de ejemplo de SignalR Blazor WebAssembly abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

1. En un shell de comandos, ejecute los siguientes comandos:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.

1. Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje. El nombre y el mensaje se muestran en ambas páginas al instante:

   ![Aplicación de ejemplo de SignalR Blazor WebAssembly abierta en dos ventanas del explorador que muestran mensajes intercambiados.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha aprendido a:

> [!div class="checklist"]
> * Crear un proyecto de aplicación Blazor WebAssembly hospedado
> * Adición de la biblioteca cliente de SignalR
> * Agregar un concentrador de SignalR
> * Agregar servicios de SignalR y un punto de conexión para el concentrador de SignalR
> * Agregar código de componente de Razor para chat

Para más información sobre la creación de aplicaciones de Blazor, consulte la documentación de Blazor:

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>Recursos adicionales

* <xref:signalr/introduction>
* [Negociación entre orígenes de SignalR para la autenticación](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
