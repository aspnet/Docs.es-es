---
title: Introducción a ASP.NET Core SignalR
author: bradygaster
description: En este tutorial, creará una aplicación de chat en la que se usa ASP.NET Core SignalR.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
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
uid: tutorials/signalr
ms.openlocfilehash: 1dad2264250bf43ec6c1df679b8754a82a5addfd
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "95417674"
---
# <a name="tutorial-get-started-with-aspnet-core-no-locsignalr"></a><span data-ttu-id="4d3a1-103">Tutorial: Introducción a ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="4d3a1-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4d3a1-104">En este tutorial se describen los conceptos básicos de la creación de una aplicación en tiempo real con SignalR.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="4d3a1-105">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4d3a1-106">Cree un proyecto web.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-106">Create a web project.</span></span>
> * <span data-ttu-id="4d3a1-107">Agregar la biblioteca cliente SignalR.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="4d3a1-108">Crear un concentrador de SignalR.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="4d3a1-109">Configurar el proyecto para usar SignalR.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="4d3a1-110">Agregar código que envía mensajes desde cualquier cliente a todos los clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="4d3a1-111">Al final, tendrá una aplicación de chat funcional:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-111">At the end, you'll have a working chat app:</span></span>

![Aplicación de ejemplo SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="4d3a1-113">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="4d3a1-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4d3a1-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d3a1-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4d3a1-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d3a1-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4d3a1-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4d3a1-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="4d3a1-117">Crear un proyecto de aplicación web</span><span class="sxs-lookup"><span data-stu-id="4d3a1-117">Create a web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4d3a1-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d3a1-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="4d3a1-119">En el menú, seleccione **Archivo > Nuevo proyecto**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="4d3a1-120">En el cuadro de diálogo **Crear un proyecto nuevo**, seleccione **Aplicación web ASP.NET Core** y, a continuación, seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="4d3a1-121">En el cuadro de diálogo **Configurar el nuevo proyecto**, asigne al proyecto el nombre *SignalRChat* y, a continuación, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="4d3a1-122">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione **.NET Core** y **ASP.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-122">In the **Create a new ASP.NET Core web Application** dialog, select **.NET Core** and **ASP.NET Core 3.1**.</span></span> 

* <span data-ttu-id="4d3a1-123">Seleccione **Aplicación web** para crear un proyecto en el que se use Razor Pages y luego seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Cuadro de diálogo Nuevo proyecto en Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4d3a1-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d3a1-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="4d3a1-126">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) en la carpeta en la que vaya a crear la del proyecto nuevo.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="4d3a1-127">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   cd SignalRChat
   code -r .
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4d3a1-128">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4d3a1-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4d3a1-129">En el menú, seleccione **Archivo > Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="4d3a1-130">Seleccione **.NET Core > Aplicación > Aplicación web** (no **Aplicación web [Modelo-Vista-Controlador]** ) y, a continuación, **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="4d3a1-131">Asegúrese de que la **plataforma de destino** está establecida en **.NET Core 3.1** y, después, seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-131">Make sure the **Target Framework** is set to **.NET Core 3.1**, and then select **Next**.</span></span>

* <span data-ttu-id="4d3a1-132">Asigne el nombre *SignalRChat* al proyecto y, después, haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="4d3a1-133">Adición de la biblioteca cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="4d3a1-133">Add the SignalR client library</span></span>

<span data-ttu-id="4d3a1-134">La biblioteca de servidor de SignalR se incluye en el marco compartido de ASP.NET Core 3.1.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-134">The SignalR server library is included in the ASP.NET Core 3.1 shared framework.</span></span> <span data-ttu-id="4d3a1-135">La biblioteca cliente de JavaScript no se incluye automáticamente en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="4d3a1-136">En este tutorial, usará el Administrador de bibliotecas (LibMan) para obtener la biblioteca cliente de *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="4d3a1-137">unpkg es una red de entrega de contenido (CDN) que puede entregar todo lo que encuentre en npm, el administrador de paquetes de Node.js.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-137">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4d3a1-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d3a1-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="4d3a1-139">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **Biblioteca del lado cliente**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="4d3a1-140">En el cuadro de diálogo **Add Client-Side Library** (Agregar biblioteca del lado cliente), en **Proveedor**, seleccione **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="4d3a1-141">Para **Biblioteca**, indique `@microsoft/signalr@latest`.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-141">For **Library**, enter `@microsoft/signalr@latest`.</span></span>

* <span data-ttu-id="4d3a1-142">Seleccione **Choose specific files** (Elegir archivos específicos), expanda la carpeta *dist/browser* y seleccione *signalr.js* y *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="4d3a1-143">Establezca **Ubicación de destino** en *wwwroot/js/signalr/* y seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-143">Set **Target Location** to *wwwroot/js/signalr/*, and select **Install**.</span></span>

  ![Cuadro de diálogo Add Client-Side Library (Agregar biblioteca del lado cliente): selección de la biblioteca](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="4d3a1-145">LibMan crea una carpeta *wwwroot/js/signalr* y copia en ella los archivos seleccionados.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4d3a1-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d3a1-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="4d3a1-147">En el terminal integrado, ejecute el comando siguiente para instalar LibMan.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="4d3a1-148">Ejecute el comando siguiente para obtener la biblioteca cliente de SignalR con LibMan.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="4d3a1-149">Puede que deba esperar unos segundos antes de ver la salida.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="4d3a1-150">Los parámetros especifican las opciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="4d3a1-151">Use el proveedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="4d3a1-152">Copie los archivos en el destino *wwwroot/js/signalr*.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="4d3a1-153">Copie solo los archivos especificados.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-153">Copy only the specified files.</span></span>

  <span data-ttu-id="4d3a1-154">La salida se parece al ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4d3a1-155">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4d3a1-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4d3a1-156">En el **terminal**, ejecute el siguiente comando para instalar LibMan.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="4d3a1-157">Vaya a la carpeta de proyecto (la que contiene el archivo *SignalRChat.csproj*).</span><span class="sxs-lookup"><span data-stu-id="4d3a1-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="4d3a1-158">Ejecute el comando siguiente para obtener la biblioteca cliente de SignalR con LibMan.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="4d3a1-159">Los parámetros especifican las opciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="4d3a1-160">Use el proveedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="4d3a1-161">Copie los archivos en el destino *wwwroot/js/signalr*.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="4d3a1-162">Copie solo los archivos especificados.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-162">Copy only the specified files.</span></span>

  <span data-ttu-id="4d3a1-163">La salida se parece al ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-no-locsignalr-hub"></a><span data-ttu-id="4d3a1-164">Creación de un concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="4d3a1-164">Create a SignalR hub</span></span>

<span data-ttu-id="4d3a1-165">Un *concentrador* es una clase que actúa como una canalización general que controla la comunicación entre el cliente y el servidor.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="4d3a1-166">En la carpeta del proyecto SignalRChat, cree una carpeta *Hubs*.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="4d3a1-167">En la carpeta *Hubs*, cree un archivo *ChatHub.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="4d3a1-168">La clase `ChatHub` hereda de la clase `Hub` de SignalR.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="4d3a1-169">La clase `Hub` administra las conexiones, los grupos y la mensajería.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="4d3a1-170">Puede llamarse al método `SendMessage` mediante un cliente conectado para enviar un mensaje a todos los clientes.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="4d3a1-171">El código de cliente de JavaScript que llama al método se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="4d3a1-172">El código de SignalR es asincrónico para proporcionar la máxima escalabilidad.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-no-locsignalr"></a><span data-ttu-id="4d3a1-173">Configuración de SignalR</span><span class="sxs-lookup"><span data-stu-id="4d3a1-173">Configure SignalR</span></span>

<span data-ttu-id="4d3a1-174">El servidor de SignalR debe estar configurado para pasar solicitudes de SignalR a SignalR.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="4d3a1-175">Agregue el código resaltado siguiente al archivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  <span data-ttu-id="4d3a1-176">Estos cambios agregan SignalR a los sistemas de inserción de dependencias y enrutamiento de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-no-locsignalr-client-code"></a><span data-ttu-id="4d3a1-177">Adición del código del cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="4d3a1-177">Add SignalR client code</span></span>

* <span data-ttu-id="4d3a1-178">Reemplace el contenido de *Pages/Index.cshtml* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="4d3a1-179">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-179">The preceding code:</span></span>

  * <span data-ttu-id="4d3a1-180">Crea cuadros de texto para el nombre y el mensaje de texto, y un botón de envío.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="4d3a1-181">Crea una lista con `id="messagesList"` para mostrar los mensajes que se reciben desde el concentrador de SignalR.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="4d3a1-182">Incluye las referencias de script a SignalR y el código de aplicación de *chat.js* que se va a crear en el paso siguiente.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="4d3a1-183">En la carpeta *wwwroot/js*, cree un archivo *chat.js* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="4d3a1-184">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-184">The preceding code:</span></span>

  * <span data-ttu-id="4d3a1-185">Crea e inicia una conexión.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="4d3a1-186">Agrega al botón de envío un controlador que envía mensajes al concentrador.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="4d3a1-187">Agrega al objeto de conexión un controlador que recibe mensajes desde el concentrador y los agrega a la lista.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="4d3a1-188">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="4d3a1-188">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4d3a1-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d3a1-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4d3a1-190">Presione **CTRL+F5** para ejecutar la aplicación sin depurar.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4d3a1-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d3a1-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4d3a1-192">En el terminal integrado, ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4d3a1-193">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4d3a1-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4d3a1-194">En el menú, seleccione **Ejecutar > Iniciar sin depurar**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="4d3a1-195">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="4d3a1-196">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón **Enviar mensaje**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="4d3a1-197">El nombre y el mensaje se muestran en ambas páginas al instante.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-197">The name and message are displayed on both pages instantly.</span></span>

  ![Aplicación de ejemplo SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="4d3a1-199">Si la aplicación no funciona, abra las herramientas para desarrolladores del explorador (F12) y vaya a la consola.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="4d3a1-200">Es posible que vea errores relacionados con el código HTML y JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="4d3a1-201">Por ejemplo, suponga que coloca *signalr.js* en una carpeta distinta a la indicada.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="4d3a1-202">En ese caso, la referencia a ese archivo no funcionará y verá un error 404 en la consola.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="4d3a1-203">![Error: signalr.js no encontrado](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="4d3a1-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="4d3a1-204">Si se produce el error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY en Chrome, ejecute estos comandos para actualizar el certificado de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4d3a1-205">En este tutorial se describen los conceptos básicos de la creación de una aplicación en tiempo real con SignalR.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-205">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="4d3a1-206">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-206">You learn how to:</span></span> 

> [!div class="checklist"]  
> * <span data-ttu-id="4d3a1-207">Cree un proyecto web.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-207">Create a web project.</span></span>   
> * <span data-ttu-id="4d3a1-208">Agregar la biblioteca cliente SignalR.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-208">Add the SignalR client library.</span></span>   
> * <span data-ttu-id="4d3a1-209">Crear un concentrador de SignalR.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-209">Create a SignalR hub.</span></span> 
> * <span data-ttu-id="4d3a1-210">Configurar el proyecto para usar SignalR.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-210">Configure the project to use SignalR.</span></span> 
> * <span data-ttu-id="4d3a1-211">Agregar código que envía mensajes desde cualquier cliente a todos los clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-211">Add code that sends messages from any client to all connected clients.</span></span>  
<span data-ttu-id="4d3a1-212">Al final, tendrá una aplicación de chat en funcionamiento: ![aplicación de ejemplo SignalR](signalr/_static/2.x/signalr-get-started-finished.png).</span><span class="sxs-lookup"><span data-stu-id="4d3a1-212">At the end, you'll have a working chat app: ![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span>   

## <a name="prerequisites"></a><span data-ttu-id="4d3a1-213">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="4d3a1-213">Prerequisites</span></span>    

# <a name="visual-studio"></a>[<span data-ttu-id="4d3a1-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d3a1-214">Visual Studio</span></span>](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[<span data-ttu-id="4d3a1-215">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d3a1-215">Visual Studio Code</span></span>](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4d3a1-216">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4d3a1-216">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a><span data-ttu-id="4d3a1-217">Creación de un proyecto web</span><span class="sxs-lookup"><span data-stu-id="4d3a1-217">Create a web project</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="4d3a1-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d3a1-218">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="4d3a1-219">En el menú, seleccione **Archivo > Nuevo proyecto**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-219">From the menu, select **File > New Project**.</span></span> 

* <span data-ttu-id="4d3a1-220">En el cuadro de diálogo **Nuevo proyecto**, seleccione **Instalado > Visual C# > Web > Aplicación web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-220">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="4d3a1-221">Asigne el nombre *SignalRChat* al proyecto.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-221">Name the project *SignalRChat*.</span></span>   

  ![Cuadro de diálogo Nuevo proyecto en Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* <span data-ttu-id="4d3a1-223">Seleccione **Aplicación web** para crear un proyecto en el que se use Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-223">Select **Web Application** to create a project that uses Razor Pages.</span></span>   

* <span data-ttu-id="4d3a1-224">Seleccione una plataforma de destino de **.NET Core**, seleccione **ASP.NET Core 2.2** y haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-224">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>    

  ![Cuadro de diálogo Nuevo proyecto en Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[<span data-ttu-id="4d3a1-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d3a1-226">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="4d3a1-227">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) en la carpeta en la que vaya a crear la del proyecto nuevo.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-227">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>  

* <span data-ttu-id="4d3a1-228">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-228">Run the following commands:</span></span>   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat   
   code -r SignalRChat    
   ```  

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4d3a1-229">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4d3a1-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="4d3a1-230">En el menú, seleccione **Archivo > Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-230">From the menu, select **File > New Solution**.</span></span>    

* <span data-ttu-id="4d3a1-231">Seleccione **.NET Core > Aplicación > Aplicación web ASP.NET Core** (no seleccione **Aplicación web de ASP.NET Core (MVC)** ).</span><span class="sxs-lookup"><span data-stu-id="4d3a1-231">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>  

* <span data-ttu-id="4d3a1-232">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-232">Select **Next**.</span></span>  

* <span data-ttu-id="4d3a1-233">Asigne el nombre *SignalRChat* al proyecto y, después, haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-233">Name the project *SignalRChat*, and then select **Create**.</span></span> 

--- 

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="4d3a1-234">Adición de la biblioteca cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="4d3a1-234">Add the SignalR client library</span></span> 

<span data-ttu-id="4d3a1-235">La biblioteca de servidor de SignalR se incluye en el metapaquete `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-235">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="4d3a1-236">La biblioteca cliente de JavaScript no se incluye automáticamente en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-236">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="4d3a1-237">En este tutorial, usará el Administrador de bibliotecas (LibMan) para obtener la biblioteca cliente de *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-237">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="4d3a1-238">unpkg es una red de entrega de contenido (CDN) que puede entregar todo lo que encuentre en npm, el administrador de paquetes de Node.js.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-238">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>   

# <a name="visual-studio"></a>[<span data-ttu-id="4d3a1-239">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d3a1-239">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="4d3a1-240">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **Biblioteca del lado cliente**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-240">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>  

* <span data-ttu-id="4d3a1-241">En el cuadro de diálogo **Add Client-Side Library** (Agregar biblioteca del lado cliente), en **Proveedor**, seleccione **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-241">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span> 

* <span data-ttu-id="4d3a1-242">En **Biblioteca**, escriba `@microsoft/signalr@3` y seleccione la versión más reciente que no sea una versión preliminar.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-242">For **Library**, enter `@microsoft/signalr@3`, and select the latest version that isn't preview.</span></span>  

  ![Cuadro de diálogo Add Client-Side Library (Agregar biblioteca del lado cliente): selección de la biblioteca](signalr/_static/2.x/libman1.png)   

* <span data-ttu-id="4d3a1-244">Seleccione **Choose specific files** (Elegir archivos específicos), expanda la carpeta *dist/browser* y seleccione *signalr.js* y *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-244">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span> 

* <span data-ttu-id="4d3a1-245">Establezca **Ubicación de destino** en *wwwroot/lib/signalr/* y seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-245">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>    

  ![Cuadro de diálogo Add Client-Side Library (Agregar biblioteca del lado cliente): selección de archivos y destino](signalr/_static/2.x/libman2.png) 

  <span data-ttu-id="4d3a1-247">LibMan crea una carpeta *wwwroot/lib/signalr* y copia en ella los archivos seleccionados.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-247">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>    

# <a name="visual-studio-code"></a>[<span data-ttu-id="4d3a1-248">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d3a1-248">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="4d3a1-249">En el terminal integrado, ejecute el comando siguiente para instalar LibMan.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-249">In the integrated terminal, run the following command to install LibMan.</span></span>  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="4d3a1-250">Ejecute el comando siguiente para obtener la biblioteca cliente de SignalR con LibMan.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-250">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="4d3a1-251">Puede que deba esperar unos segundos antes de ver la salida.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-251">You might have to wait a few seconds before seeing output.</span></span> 

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="4d3a1-252">Los parámetros especifican las opciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-252">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="4d3a1-253">Use el proveedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-253">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="4d3a1-254">Copie los archivos en el destino *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-254">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="4d3a1-255">Copie solo los archivos especificados.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-255">Copy only the specified files.</span></span>  

  <span data-ttu-id="4d3a1-256">La salida se parece al ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-256">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4d3a1-257">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4d3a1-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="4d3a1-258">En el **terminal**, ejecute el siguiente comando para instalar LibMan.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-258">In the **Terminal**, run the following command to install LibMan.</span></span> 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="4d3a1-259">Vaya a la carpeta de proyecto (la que contiene el archivo *SignalRChat.csproj*).</span><span class="sxs-lookup"><span data-stu-id="4d3a1-259">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>   

* <span data-ttu-id="4d3a1-260">Ejecute el comando siguiente para obtener la biblioteca cliente de SignalR con LibMan.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-260">Run the following command to get the SignalR client library by using LibMan.</span></span>    

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="4d3a1-261">Los parámetros especifican las opciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-261">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="4d3a1-262">Use el proveedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-262">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="4d3a1-263">Copie los archivos en el destino *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-263">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="4d3a1-264">Copie solo los archivos especificados.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-264">Copy only the specified files.</span></span>  

  <span data-ttu-id="4d3a1-265">La salida se parece al ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-265">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-no-locsignalr-hub"></a><span data-ttu-id="4d3a1-266">Creación de un concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="4d3a1-266">Create a SignalR hub</span></span>   

<span data-ttu-id="4d3a1-267">Un *concentrador* es una clase que actúa como una canalización general que controla la comunicación entre el cliente y el servidor.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-267">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>   

* <span data-ttu-id="4d3a1-268">En la carpeta del proyecto SignalRChat, cree una carpeta *Hubs*.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-268">In the SignalRChat project folder, create a *Hubs* folder.</span></span>  

* <span data-ttu-id="4d3a1-269">En la carpeta *Hubs*, cree un archivo *ChatHub.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-269">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span> 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  <span data-ttu-id="4d3a1-270">La clase `ChatHub` hereda de la clase `Hub` de SignalR.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-270">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="4d3a1-271">La clase `Hub` administra las conexiones, los grupos y la mensajería.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-271">The `Hub` class manages connections, groups, and messaging.</span></span>  

  <span data-ttu-id="4d3a1-272">Puede llamarse al método `SendMessage` mediante un cliente conectado para enviar un mensaje a todos los clientes.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-272">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="4d3a1-273">El código de cliente de JavaScript que llama al método se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-273">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="4d3a1-274">El código de SignalR es asincrónico para proporcionar la máxima escalabilidad.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-274">SignalR code is asynchronous to provide maximum scalability.</span></span>    

## <a name="configure-no-locsignalr"></a><span data-ttu-id="4d3a1-275">Configuración de SignalR</span><span class="sxs-lookup"><span data-stu-id="4d3a1-275">Configure SignalR</span></span>  

<span data-ttu-id="4d3a1-276">El servidor de SignalR debe estar configurado para pasar solicitudes de SignalR a SignalR.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-276">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>    

* <span data-ttu-id="4d3a1-277">Agregue el código resaltado siguiente al archivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-277">Add the following highlighted code to the *Startup.cs* file.</span></span>  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  <span data-ttu-id="4d3a1-278">Estos cambios agregan SignalR al sistema de inserción de dependencias de ASP.NET Core y a la canalización de software intermedio.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-278">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>  

## <a name="add-no-locsignalr-client-code"></a><span data-ttu-id="4d3a1-279">Adición del código del cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="4d3a1-279">Add SignalR client code</span></span>    

* <span data-ttu-id="4d3a1-280">Reemplace el contenido de *Pages/Index.cshtml* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-280">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  <span data-ttu-id="4d3a1-281">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-281">The preceding code:</span></span>   

  * <span data-ttu-id="4d3a1-282">Crea cuadros de texto para el nombre y el mensaje de texto, y un botón de envío.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-282">Creates text boxes for name and message text, and a submit button.</span></span>  
  * <span data-ttu-id="4d3a1-283">Crea una lista con `id="messagesList"` para mostrar los mensajes que se reciben desde el concentrador de SignalR.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-283">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>   
  * <span data-ttu-id="4d3a1-284">Incluye las referencias de script a SignalR y el código de aplicación de *chat.js* que se va a crear en el paso siguiente.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-284">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>    

* <span data-ttu-id="4d3a1-285">En la carpeta *wwwroot/js*, cree un archivo *chat.js* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-285">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  <span data-ttu-id="4d3a1-286">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-286">The preceding code:</span></span>   

  * <span data-ttu-id="4d3a1-287">Crea e inicia una conexión.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-287">Creates and starts a connection.</span></span>    
  * <span data-ttu-id="4d3a1-288">Agrega al botón de envío un controlador que envía mensajes al concentrador.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-288">Adds to the submit button a handler that sends messages to the hub.</span></span> 
  * <span data-ttu-id="4d3a1-289">Agrega al objeto de conexión un controlador que recibe mensajes desde el concentrador y los agrega a la lista.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-289">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>  

## <a name="run-the-app"></a><span data-ttu-id="4d3a1-290">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="4d3a1-290">Run the app</span></span>  

# <a name="visual-studio"></a>[<span data-ttu-id="4d3a1-291">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d3a1-291">Visual Studio</span></span>](#tab/visual-studio)   

* <span data-ttu-id="4d3a1-292">Presione **CTRL+F5** para ejecutar la aplicación sin depurar.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-292">Press **CTRL+F5** to run the app without debugging.</span></span>   

# <a name="visual-studio-code"></a>[<span data-ttu-id="4d3a1-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d3a1-293">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="4d3a1-294">En el terminal integrado, ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="4d3a1-294">In the integrated terminal, run the following command:</span></span>    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4d3a1-295">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4d3a1-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4d3a1-296">En el menú, seleccione **Ejecutar > Iniciar sin depurar**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-296">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="4d3a1-297">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-297">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="4d3a1-298">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón **Enviar mensaje**.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-298">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>  

  <span data-ttu-id="4d3a1-299">El nombre y el mensaje se muestran en ambas páginas al instante.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-299">The name and message are displayed on both pages instantly.</span></span>   

  ![Aplicación de ejemplo SignalR](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> <span data-ttu-id="4d3a1-301">Si la aplicación no funciona, abra las herramientas para desarrolladores del explorador (F12) y vaya a la consola.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-301">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="4d3a1-302">Es posible que vea errores relacionados con el código HTML y JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-302">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="4d3a1-303">Por ejemplo, suponga que coloca *signalr.js* en una carpeta distinta a la indicada.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-303">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="4d3a1-304">En ese caso, la referencia a ese archivo no funcionará y verá un error 404 en la consola.</span><span class="sxs-lookup"><span data-stu-id="4d3a1-304">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>   
> <span data-ttu-id="4d3a1-305">![Error: signalr.js no encontrado](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="4d3a1-305">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>    
## <a name="additional-resources"></a><span data-ttu-id="4d3a1-306">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4d3a1-306">Additional resources</span></span> 
* [<span data-ttu-id="4d3a1-307">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="4d3a1-307">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
