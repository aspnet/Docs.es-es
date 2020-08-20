---
title: Introducción a ASP.NET Core SignalR
author: bradygaster
description: En este tutorial, creará una aplicación de chat en la que se usa ASP.NET Core SignalR.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
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
uid: tutorials/signalr
ms.openlocfilehash: e98cfc5e95233ce4d1001ab1225fc15e5fd23733
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634506"
---
# <a name="tutorial-get-started-with-aspnet-core-no-locsignalr"></a>Tutorial: Introducción a ASP.NET Core SignalR

::: moniker range=">= aspnetcore-3.0"

En este tutorial se describen los conceptos básicos de la creación de una aplicación en tiempo real con SignalR. Aprenderá a:

> [!div class="checklist"]
> * Cree un proyecto web.
> * Agregar la biblioteca cliente SignalR.
> * Crear un concentrador de SignalR.
> * Configurar el proyecto para usar SignalR.
> * Agregar código que envía mensajes desde cualquier cliente a todos los clientes conectados.

Al final, tendrá una aplicación de chat funcional:

![Aplicación de ejemplo SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a>Requisitos previos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a>Crear un proyecto de aplicación web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

* En el menú, seleccione **Archivo > Nuevo proyecto**.

* En el cuadro de diálogo **Crear un proyecto nuevo**, seleccione **Aplicación web ASP.NET Core** y, a continuación, seleccione **Siguiente**.

* En el cuadro de diálogo **Configurar el nuevo proyecto**, asigne al proyecto el nombre *SignalRChat* y, a continuación, seleccione **Crear**.

* En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione **.NET Core** y **ASP.NET Core 3.0**. 

* Seleccione **Aplicación web** para crear un proyecto en el que se use Razor Pages y luego seleccione **Crear**.

  ![Cuadro de diálogo Nuevo proyecto en Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) en la carpeta en la que vaya a crear la del proyecto nuevo.

* Ejecute los comandos siguientes:

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* En el menú, seleccione **Archivo > Nueva solución**.

* Seleccione **.NET Core > Aplicación > Aplicación web** (no **Aplicación web [Modelo-Vista-Controlador]** ) y, a continuación, **Siguiente**.

* Asegúrese de que la **Plataforma de destino** esté establecida en **.NET Core 3.0** y, a continuación, seleccione **Siguiente**.

* Asigne el nombre *SignalRChat* al proyecto y, después, haga clic en **Crear**.

---

## <a name="add-the-no-locsignalr-client-library"></a>Adición de la biblioteca cliente de SignalR

La biblioteca de servidor de SignalR se incluye en el marco compartido de ASP.NET Core 3.0. La biblioteca cliente de JavaScript no se incluye automáticamente en el proyecto. En este tutorial, usará el Administrador de bibliotecas (LibMan) para obtener la biblioteca cliente de *unpkg*. unpkg es una red de entrega de contenido (CDN) que puede entregar todo lo que encuentre en npm, el administrador de paquetes de Node.js.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

* En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **Biblioteca del lado cliente**.

* En el cuadro de diálogo **Add Client-Side Library** (Agregar biblioteca del lado cliente), en **Proveedor**, seleccione **unpkg**.

* Para **Biblioteca**, indique `@microsoft/signalr@latest`.

* Seleccione **Choose specific files** (Elegir archivos específicos), expanda la carpeta *dist/browser* y seleccione *signalr.js* y *signalr.min.js*.

* Establezca **Ubicación de destino** en *wwwroot/js/signalr/* y seleccione **Instalar**.

  ![Cuadro de diálogo Add Client-Side Library (Agregar biblioteca del lado cliente): selección de la biblioteca](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  LibMan crea una carpeta *wwwroot/js/signalr* y copia en ella los archivos seleccionados.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* En el terminal integrado, ejecute el comando siguiente para instalar LibMan.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* Ejecute el comando siguiente para obtener la biblioteca cliente de SignalR con LibMan. Puede que deba esperar unos segundos antes de ver la salida.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  Los parámetros especifican las opciones siguientes:
  * Use el proveedor unpkg.
  * Copie los archivos en el destino *wwwroot/js/signalr*.
  * Copie solo los archivos especificados.

  La salida se parece al ejemplo siguiente:

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* En el **terminal**, ejecute el siguiente comando para instalar LibMan.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* Vaya a la carpeta de proyecto (la que contiene el archivo *SignalRChat.csproj*).

* Ejecute el comando siguiente para obtener la biblioteca cliente de SignalR con LibMan.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  Los parámetros especifican las opciones siguientes:
  * Use el proveedor unpkg.
  * Copie los archivos en el destino *wwwroot/js/signalr*.
  * Copie solo los archivos especificados.

  La salida se parece al ejemplo siguiente:

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-no-locsignalr-hub"></a>Creación de un concentrador de SignalR

Un *concentrador* es una clase que actúa como una canalización general que controla la comunicación entre el cliente y el servidor.

* En la carpeta del proyecto SignalRChat, cree una carpeta *Hubs*.

* En la carpeta *Hubs*, cree un archivo *ChatHub.cs* con el código siguiente:

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  La clase `ChatHub` hereda de la clase `Hub` de SignalR. La clase `Hub` administra las conexiones, los grupos y la mensajería.

  Puede llamarse al método `SendMessage` mediante un cliente conectado para enviar un mensaje a todos los clientes. El código de cliente de JavaScript que llama al método se muestra más adelante en el tutorial. El código de SignalR es asincrónico para proporcionar la máxima escalabilidad.

## <a name="configure-no-locsignalr"></a>Configuración de SignalR

El servidor de SignalR debe estar configurado para pasar solicitudes de SignalR a SignalR.

* Agregue el código resaltado siguiente al archivo *Startup.cs*.

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  Estos cambios agregan SignalR a los sistemas de inserción de dependencias y enrutamiento de ASP.NET Core.

## <a name="add-no-locsignalr-client-code"></a>Adición del código del cliente de SignalR

* Reemplace el contenido de *Pages/Index.cshtml* con el código siguiente:

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  El código anterior:

  * Crea cuadros de texto para el nombre y el mensaje de texto, y un botón de envío.
  * Crea una lista con `id="messagesList"` para mostrar los mensajes que se reciben desde el concentrador de SignalR.
  * Incluye las referencias de script a SignalR y el código de aplicación de *chat.js* que se va a crear en el paso siguiente.

* En la carpeta *wwwroot/js*, cree un archivo *chat.js* con el código siguiente:

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  El código anterior:

  * Crea e inicia una conexión.
  * Agrega al botón de envío un controlador que envía mensajes al concentrador.
  * Agrega al objeto de conexión un controlador que recibe mensajes desde el concentrador y los agrega a la lista.

## <a name="run-the-app"></a>Ejecutar la aplicación

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Presione **CTRL+F5** para ejecutar la aplicación sin depurar.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* En el terminal integrado, ejecute el comando siguiente:

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* En el menú, seleccione **Ejecutar > Iniciar sin depurar**.

---

* Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.

* Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón **Enviar mensaje**.

  El nombre y el mensaje se muestran en ambas páginas al instante.

  ![Aplicación de ejemplo SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * Si la aplicación no funciona, abra las herramientas para desarrolladores del explorador (F12) y vaya a la consola. Es posible que vea errores relacionados con el código HTML y JavaScript. Por ejemplo, suponga que coloca *signalr.js* en una carpeta distinta a la indicada. En ese caso, la referencia a ese archivo no funcionará y verá un error 404 en la consola.
>   ![Error: signalr.js no encontrado](signalr/_static/3.x/f12-console.png)
> * Si se produce el error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY en Chrome, ejecute estos comandos para actualizar el certificado de desarrollo:
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

En este tutorial se describen los conceptos básicos de la creación de una aplicación en tiempo real con SignalR. Aprenderá a: 

> [!div class="checklist"]  
> * Cree un proyecto web.   
> * Agregar la biblioteca cliente SignalR.   
> * Crear un concentrador de SignalR. 
> * Configurar el proyecto para usar SignalR. 
> * Agregar código que envía mensajes desde cualquier cliente a todos los clientes conectados.  
Al final, tendrá una aplicación de chat en funcionamiento: ![aplicación de ejemplo SignalR](signalr/_static/2.x/signalr-get-started-finished.png).   

## <a name="prerequisites"></a>Requisitos previos    

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a>Creación de un proyecto web 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* En el menú, seleccione **Archivo > Nuevo proyecto**. 

* En el cuadro de diálogo **Nuevo proyecto**, seleccione **Instalado > Visual C# > Web > Aplicación web ASP.NET Core**. Asigne el nombre *SignalRChat* al proyecto.   

  ![Cuadro de diálogo Nuevo proyecto en Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* Seleccione **Aplicación web** para crear un proyecto en el que se use Razor Pages.   

* Seleccione una plataforma de destino de **.NET Core**, seleccione **ASP.NET Core 2.2** y haga clic en **Aceptar**.    

  ![Cuadro de diálogo Nuevo proyecto en Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) en la carpeta en la que vaya a crear la del proyecto nuevo.  

* Ejecute los comandos siguientes:   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat   
   code -r SignalRChat    
   ```  

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)   

* En el menú, seleccione **Archivo > Nueva solución**.    

* Seleccione **.NET Core > Aplicación > Aplicación web ASP.NET Core** (no seleccione **Aplicación web de ASP.NET Core (MVC)** ).  

* Seleccione **Siguiente**.  

* Asigne el nombre *SignalRChat* al proyecto y, después, haga clic en **Crear**. 

--- 

## <a name="add-the-no-locsignalr-client-library"></a>Adición de la biblioteca cliente de SignalR 

La biblioteca de servidor de SignalR se incluye en el metapaquete `Microsoft.AspNetCore.App`. La biblioteca cliente de JavaScript no se incluye automáticamente en el proyecto. En este tutorial, usará el Administrador de bibliotecas (LibMan) para obtener la biblioteca cliente de *unpkg*. unpkg es una red de entrega de contenido (CDN) que puede entregar todo lo que encuentre en npm, el administrador de paquetes de Node.js.   

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **Biblioteca del lado cliente**.  

* En el cuadro de diálogo **Add Client-Side Library** (Agregar biblioteca del lado cliente), en **Proveedor**, seleccione **unpkg**. 

* En **Biblioteca**, escriba `@microsoft/signalr@3` y seleccione la versión más reciente que no sea una versión preliminar.  

  ![Cuadro de diálogo Add Client-Side Library (Agregar biblioteca del lado cliente): selección de la biblioteca](signalr/_static/2.x/libman1.png)   

* Seleccione **Choose specific files** (Elegir archivos específicos), expanda la carpeta *dist/browser* y seleccione *signalr.js* y *signalr.min.js*. 

* Establezca **Ubicación de destino** en *wwwroot/lib/signalr/* y seleccione **Instalar**.    

  ![Cuadro de diálogo Add Client-Side Library (Agregar biblioteca del lado cliente): selección de archivos y destino](signalr/_static/2.x/libman2.png) 

  LibMan crea una carpeta *wwwroot/lib/signalr* y copia en ella los archivos seleccionados.    

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* En el terminal integrado, ejecute el comando siguiente para instalar LibMan.  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* Ejecute el comando siguiente para obtener la biblioteca cliente de SignalR con LibMan. Puede que deba esperar unos segundos antes de ver la salida. 

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  Los parámetros especifican las opciones siguientes: 
  * Use el proveedor unpkg. 
  * Copie los archivos en el destino *wwwroot/lib/signalr*.    
  * Copie solo los archivos especificados.  

  La salida se parece al ejemplo siguiente:  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)   

* En el **terminal**, ejecute el siguiente comando para instalar LibMan. 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* Vaya a la carpeta de proyecto (la que contiene el archivo *SignalRChat.csproj*).   

* Ejecute el comando siguiente para obtener la biblioteca cliente de SignalR con LibMan.    

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  Los parámetros especifican las opciones siguientes: 
  * Use el proveedor unpkg. 
  * Copie los archivos en el destino *wwwroot/lib/signalr*.    
  * Copie solo los archivos especificados.  

  La salida se parece al ejemplo siguiente:  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-no-locsignalr-hub"></a>Creación de un concentrador de SignalR   

Un *concentrador* es una clase que actúa como una canalización general que controla la comunicación entre el cliente y el servidor.   

* En la carpeta del proyecto SignalRChat, cree una carpeta *Hubs*.  

* En la carpeta *Hubs*, cree un archivo *ChatHub.cs* con el código siguiente: 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  La clase `ChatHub` hereda de la clase `Hub` de SignalR. La clase `Hub` administra las conexiones, los grupos y la mensajería.  

  Puede llamarse al método `SendMessage` mediante un cliente conectado para enviar un mensaje a todos los clientes. El código de cliente de JavaScript que llama al método se muestra más adelante en el tutorial. El código de SignalR es asincrónico para proporcionar la máxima escalabilidad.    

## <a name="configure-no-locsignalr"></a>Configuración de SignalR  

El servidor de SignalR debe estar configurado para pasar solicitudes de SignalR a SignalR.    

* Agregue el código resaltado siguiente al archivo *Startup.cs*.  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  Estos cambios agregan SignalR al sistema de inserción de dependencias de ASP.NET Core y a la canalización de software intermedio.  

## <a name="add-no-locsignalr-client-code"></a>Adición del código del cliente de SignalR    

* Reemplace el contenido de *Pages/Index.cshtml* con el código siguiente:  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  El código anterior:   

  * Crea cuadros de texto para el nombre y el mensaje de texto, y un botón de envío.  
  * Crea una lista con `id="messagesList"` para mostrar los mensajes que se reciben desde el concentrador de SignalR.   
  * Incluye las referencias de script a SignalR y el código de aplicación de *chat.js* que se va a crear en el paso siguiente.    

* En la carpeta *wwwroot/js*, cree un archivo *chat.js* con el código siguiente:  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  El código anterior:   

  * Crea e inicia una conexión.    
  * Agrega al botón de envío un controlador que envía mensajes al concentrador. 
  * Agrega al objeto de conexión un controlador que recibe mensajes desde el concentrador y los agrega a la lista.  

## <a name="run-the-app"></a>Ejecutar la aplicación  

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

* Presione **CTRL+F5** para ejecutar la aplicación sin depurar.   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* En el terminal integrado, ejecute el comando siguiente:    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* En el menú, seleccione **Ejecutar > Iniciar sin depurar**.

---

* Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.

* Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón **Enviar mensaje**.  

  El nombre y el mensaje se muestran en ambas páginas al instante.   

  ![Aplicación de ejemplo SignalR](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> Si la aplicación no funciona, abra las herramientas para desarrolladores del explorador (F12) y vaya a la consola. Es posible que vea errores relacionados con el código HTML y JavaScript. Por ejemplo, suponga que coloca *signalr.js* en una carpeta distinta a la indicada. En ese caso, la referencia a ese archivo no funcionará y verá un error 404 en la consola.   
> ![Error: signalr.js no encontrado](signalr/_static/2.x/f12-console.png)    
## <a name="additional-resources"></a>Recursos adicionales 
* [Versión en YouTube de este tutorial](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
