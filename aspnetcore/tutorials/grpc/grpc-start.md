---
title: Creación de un servidor y un cliente gRPC en ASP.NET Core
author: juntaoluo
description: En este tutorial se le mostrará cómo crear un servicio gRPC y un cliente gRPC en ASP.NET Core. Aprenda a crear un proyecto de servicio gRPC, edite un archivo proto y agregue una llamada de streaming dúplex.
ms.author: johluo
ms.date: 04/08/2020
no-loc:
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: fbfd37b8f796990ff035f7fffeb906e23a8739d4
ms.sourcegitcommit: c06a5bf419541d17595af30e4cf6f2787c21855e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678589"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="26f7e-104">Tutorial: Crear un servidor y un cliente gRPC en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="26f7e-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="26f7e-105">Por [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="26f7e-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="26f7e-106">En este tutorial se muestra cómo crear un cliente [gRPC](https://grpc.io/docs/guides/) de .NET Core y un servidor gRPC de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="26f7e-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="26f7e-107">Al final tendrá un cliente gRPC que se comunica con el servicio Greeter de gRPC.</span><span class="sxs-lookup"><span data-stu-id="26f7e-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="26f7e-108">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="26f7e-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="26f7e-109">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="26f7e-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="26f7e-110">Crear un servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="26f7e-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="26f7e-111">Crear un cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="26f7e-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="26f7e-112">Probar el servicio cliente gRPC con el servicio gRPC Greeter.</span><span class="sxs-lookup"><span data-stu-id="26f7e-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="26f7e-113">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="26f7e-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26f7e-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26f7e-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="26f7e-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="26f7e-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="26f7e-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="26f7e-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="26f7e-117">Visual Studio para Mac, versión 8.7 o posterior</span><span class="sxs-lookup"><span data-stu-id="26f7e-117">Visual Studio for Mac version 8.7 or later</span></span>](/visualstudio/releasenotes/vs2019-mac-relnotes)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]
---

## <a name="create-a-grpc-service"></a><span data-ttu-id="26f7e-118">Crear un servicio gRPC</span><span class="sxs-lookup"><span data-stu-id="26f7e-118">Create a gRPC service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26f7e-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26f7e-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="26f7e-120">Inicie Visual Studio y seleccione **Crear un proyecto**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-120">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="26f7e-121">Alternativamente, en el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-121">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="26f7e-122">En el cuadro de diálogo **Crear un proyecto** , seleccione **Servicio gRPC** y elija **Siguiente** :</span><span class="sxs-lookup"><span data-stu-id="26f7e-122">In the **Create a new project** dialog, select **gRPC Service** and select **Next** :</span></span>

  ![Cuadro de diálogo Crear un proyecto](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="26f7e-124">Llame al proyecto **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-124">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="26f7e-125">Es importante asignarle el nombre *GrpcGreeter* para que los espacios de nombres coincidan al copiar y pegar el código.</span><span class="sxs-lookup"><span data-stu-id="26f7e-125">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="26f7e-126">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-126">Select **Create**.</span></span>
* <span data-ttu-id="26f7e-127">En el cuadro de diálogo **Crear un servicio gRPC** :</span><span class="sxs-lookup"><span data-stu-id="26f7e-127">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="26f7e-128">Se selecciona la plantilla **Servicio gRPC**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-128">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="26f7e-129">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-129">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="26f7e-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="26f7e-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="26f7e-131">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="26f7e-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="26f7e-132">Cambie los directorios (`cd`) a una carpeta que contenga el proyecto.</span><span class="sxs-lookup"><span data-stu-id="26f7e-132">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="26f7e-133">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="26f7e-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="26f7e-134">El comando `dotnet new` crea un nuevo servicio gRPC en la carpeta *GrpcGreeter*.</span><span class="sxs-lookup"><span data-stu-id="26f7e-134">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="26f7e-135">El comando `code` abre la carpeta *GrpcGreeter* en una nueva instancia de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="26f7e-135">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="26f7e-136">Se muestra un cuadro de diálogo con el texto **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?).</span><span class="sxs-lookup"><span data-stu-id="26f7e-136">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="26f7e-137">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-137">Select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="26f7e-138">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="26f7e-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="26f7e-139">Inicie Visual Studio para Mac y seleccione **Crear un proyecto**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-139">Start Visual Studio for Mac and select **Create a new project**.</span></span> <span data-ttu-id="26f7e-140">Alternativamente, en el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-140">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="26f7e-141">En el cuadro de diálogo **Crear un proyecto** , seleccione **Web y consola** > **Aplicación** > **Servicio gRPC** y luego **Siguiente** :</span><span class="sxs-lookup"><span data-stu-id="26f7e-141">In the **Create a new project** dialog, select **Web and Console** > **App** > **gRPC Service** and select **Next** :</span></span>

  ![Cuadro de diálogo Crear un proyecto](~/tutorials/grpc/grpc-start/static/cnp-mac.png)

* <span data-ttu-id="26f7e-143">Seleccione **.NET Core 3.1** como marco de trabajo de destino y haga clic en **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-143">Select **.NET Core 3.1** for the target framework and click **Next**</span></span>
* <span data-ttu-id="26f7e-144">Llame al proyecto **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-144">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="26f7e-145">Es importante asignarle el nombre *GrpcGreeter* para que los espacios de nombres coincidan al copiar y pegar el código.</span><span class="sxs-lookup"><span data-stu-id="26f7e-145">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="26f7e-146">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-146">Select **Create**.</span></span>
---

### <a name="run-the-service"></a><span data-ttu-id="26f7e-147">Ejecutar el servicio</span><span class="sxs-lookup"><span data-stu-id="26f7e-147">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="26f7e-148">Los registros muestran que el servicio está escuchando en `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="26f7e-148">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="26f7e-149">La plantilla gRPC está configurada para usar [Seguridad de la capa de transporte (TLS)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="26f7e-149">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="26f7e-150">Los clientes de gRPC necesitan usar HTTPS para llamar al servidor.</span><span class="sxs-lookup"><span data-stu-id="26f7e-150">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="26f7e-151">macOS no admite gRPC de ASP.NET Core con TLS.</span><span class="sxs-lookup"><span data-stu-id="26f7e-151">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="26f7e-152">Se requiere configuración adicional para ejecutar correctamente servicios gRPC en macOS.</span><span class="sxs-lookup"><span data-stu-id="26f7e-152">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="26f7e-153">Para obtener más información, vea [No se puede iniciar la aplicación gRPC de ASP.NET Core en macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="26f7e-153">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="26f7e-154">Examen de los archivo del proyecto</span><span class="sxs-lookup"><span data-stu-id="26f7e-154">Examine the project files</span></span>

<span data-ttu-id="26f7e-155">Archivos de proyecto de *GrpcGreeter* :</span><span class="sxs-lookup"><span data-stu-id="26f7e-155">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="26f7e-156">*greet.proto* : El archivo *Protos/greet.proto* define el gRPC `Greeter` y se usa para generar los recursos de servidor gRPC.</span><span class="sxs-lookup"><span data-stu-id="26f7e-156">*greet.proto* : The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="26f7e-157">Para obtener más información, vea [Introducción a gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="26f7e-157">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="26f7e-158">Carpeta *Servicios* : contiene la implementación del servicio `Greeter`.</span><span class="sxs-lookup"><span data-stu-id="26f7e-158">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="26f7e-159">*appSettings.json* : contiene datos de configuración, como el protocolo usado por Kestrel.</span><span class="sxs-lookup"><span data-stu-id="26f7e-159">*appSettings.json* : Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="26f7e-160">Para obtener más información, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="26f7e-160">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="26f7e-161">*Program.cs* : contiene el punto de entrada para el servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="26f7e-161">*Program.cs* : Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="26f7e-162">Para obtener más información, vea <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="26f7e-162">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="26f7e-163">*Startup.cs* : Contiene código que configura el comportamiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="26f7e-163">*Startup.cs* : Contains code that configures app behavior.</span></span> <span data-ttu-id="26f7e-164">Para obtener más información, vea [Inicio de la aplicación](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="26f7e-164">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="26f7e-165">Creación del cliente gRPC en una aplicación de consola de .NET</span><span class="sxs-lookup"><span data-stu-id="26f7e-165">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26f7e-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26f7e-166">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="26f7e-167">Abra una segunda instancia de Visual Studio y seleccione **Crear un proyecto**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-167">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="26f7e-168">En el cuadro de diálogo **Crear un proyecto** , seleccione **Aplicación de consola (.NET Core)** y elija **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-168">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="26f7e-169">En el cuadro de texto **Nombre de proyecto** , escriba **GrpcGreeterClient** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-169">In the **Project name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="26f7e-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="26f7e-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="26f7e-171">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="26f7e-171">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="26f7e-172">Cambie los directorios (`cd`) a una carpeta que contenga el proyecto.</span><span class="sxs-lookup"><span data-stu-id="26f7e-172">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="26f7e-173">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="26f7e-173">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="26f7e-174">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="26f7e-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="26f7e-175">Siga las instrucciones de [Creación de una solución completa de .NET Core en macOS con Visual Studio para Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) para crear una aplicación de consola con el nombre *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="26f7e-175">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="26f7e-176">Adición de paquetes necesarios</span><span class="sxs-lookup"><span data-stu-id="26f7e-176">Add required packages</span></span>

<span data-ttu-id="26f7e-177">El proyecto de cliente gRPC requiere los siguientes paquetes:</span><span class="sxs-lookup"><span data-stu-id="26f7e-177">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="26f7e-178">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), que contiene el cliente de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="26f7e-178">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="26f7e-179">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), que contiene API de mensajes protobuf para C#.</span><span class="sxs-lookup"><span data-stu-id="26f7e-179">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="26f7e-180">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), que contiene compatibilidad con herramientas de C# para archivos protobuf.</span><span class="sxs-lookup"><span data-stu-id="26f7e-180">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="26f7e-181">El paquete de herramientas no es necesario en el runtime, de modo que la dependencia se marca con `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="26f7e-181">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26f7e-182">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26f7e-182">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="26f7e-183">Instale los paquetes con la Consola del Administrador de paquetes (PMC) o mediante Administrar paquetes NuGet.</span><span class="sxs-lookup"><span data-stu-id="26f7e-183">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="26f7e-184">Opción de PMC para instalar paquetes</span><span class="sxs-lookup"><span data-stu-id="26f7e-184">PMC option to install packages</span></span>

* <span data-ttu-id="26f7e-185">En Visual Studio, seleccione **Herramientas** > **Administrador de paquetes de NuGet** > **Consola del Administrador de paquetes**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-185">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="26f7e-186">En la ventana **Consola del Administrador de paquetes** , ejecute `cd GrpcGreeterClient` para cambiar los directorios a la carpeta que contiene los archivos *GrpcGreeterClient.csproj*.</span><span class="sxs-lookup"><span data-stu-id="26f7e-186">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="26f7e-187">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="26f7e-187">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="26f7e-188">Administración de la opción Paquetes NuGet para instalar paquetes</span><span class="sxs-lookup"><span data-stu-id="26f7e-188">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="26f7e-189">Haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** > **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-189">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="26f7e-190">Seleccione la pestaña **Examinar**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-190">Select the **Browse** tab.</span></span>
* <span data-ttu-id="26f7e-191">Escriba **Grpc.Net.Client** en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="26f7e-191">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="26f7e-192">Seleccione el paquete **Grpc.Net.Client** en la pestaña **Examinar** y haga clic en **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-192">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="26f7e-193">Repita el proceso para `Google.Protobuf` y `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="26f7e-193">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="26f7e-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="26f7e-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="26f7e-195">Ejecute los siguientes comandos en el **terminal integrado** :</span><span class="sxs-lookup"><span data-stu-id="26f7e-195">Run the following commands from the **Integrated Terminal** :</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="26f7e-196">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="26f7e-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="26f7e-197">Haga clic con el botón derecho en el proyecto **GrpcGreeterClient** en el **Panel de solución** y seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-197">Right-click **GrpcGreeterClient** project in the **Solution Pad** and select **Manage NuGet Packages**</span></span>
* <span data-ttu-id="26f7e-198">Escriba **Grpc.Net.Client** en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="26f7e-198">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="26f7e-199">Seleccione el paquete **Grpc.Net.Client** en el panel de resultados y seleccione **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-199">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="26f7e-200">Haga clic en el botón **Aceptar** del cuadro de diálogo de **aceptación de la licencia**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-200">Click the **Accept** button on the **Accept License** dialog.</span></span>
* <span data-ttu-id="26f7e-201">Repita el proceso para `Google.Protobuf` y `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="26f7e-201">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="26f7e-202">Adición de greet.proto</span><span class="sxs-lookup"><span data-stu-id="26f7e-202">Add greet.proto</span></span>

* <span data-ttu-id="26f7e-203">Cree una carpeta *Protos* en el proyecto de cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="26f7e-203">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="26f7e-204">Copie el archivo *Protos\greet.proto* del servicio gRPC Greeter en el proyecto de cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="26f7e-204">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="26f7e-205">Actualice el espacio de nombres del archivo `greet.proto` al espacio de nombres del proyecto:</span><span class="sxs-lookup"><span data-stu-id="26f7e-205">Update the namespace inside the `greet.proto` file to the project's namespace:</span></span>

  ```
  option csharp_namespace = "GrpcGreeterClient";
  ```

* <span data-ttu-id="26f7e-206">Edite el archivo de proyecto *GrpcGreeterClient.csproj* :</span><span class="sxs-lookup"><span data-stu-id="26f7e-206">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studio"></a>[<span data-ttu-id="26f7e-207">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26f7e-207">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="26f7e-208">Haga clic con el botón derecho en el proyecto y seleccione **Editar archivo del proyecto**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-208">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-code"></a>[<span data-ttu-id="26f7e-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="26f7e-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="26f7e-210">Seleccione el archivo *GrpcGreeterClient.csproj*.</span><span class="sxs-lookup"><span data-stu-id="26f7e-210">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="26f7e-211">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="26f7e-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="26f7e-212">Haga clic con el botón derecho en el proyecto y seleccione **Editar archivo del proyecto**.</span><span class="sxs-lookup"><span data-stu-id="26f7e-212">Right-click the project and select **Edit Project File**.</span></span>

  ---

* <span data-ttu-id="26f7e-213">Agregue un grupo de elementos con un elemento `<Protobuf>` que hace referencia al archivo *greet.proto* :</span><span class="sxs-lookup"><span data-stu-id="26f7e-213">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="26f7e-214">Creación del cliente de Greeter</span><span class="sxs-lookup"><span data-stu-id="26f7e-214">Create the Greeter client</span></span>

<span data-ttu-id="26f7e-215">Compile el proyecto de cliente para crear los tipos en el espacio de nombres `GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="26f7e-215">Build the client project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="26f7e-216">El proceso de compilación genera automáticamente los tipos `GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="26f7e-216">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="26f7e-217">Actualice el archivo *Program.cs* del cliente gRPC con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="26f7e-217">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="26f7e-218">*Program.cs* contiene el punto de entrada y la lógica para el cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="26f7e-218">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="26f7e-219">El cliente de Greeter se crea mediante lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="26f7e-219">The Greeter client is created by:</span></span>

* <span data-ttu-id="26f7e-220">Creación de una instancia de `GrpcChannel` que contiene la información para crear la conexión al servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="26f7e-220">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="26f7e-221">Uso de `GrpcChannel` para construir el cliente de Greeter:</span><span class="sxs-lookup"><span data-stu-id="26f7e-221">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="26f7e-222">El cliente de Greeter realiza una llamada al método `SayHello` asincrónico.</span><span class="sxs-lookup"><span data-stu-id="26f7e-222">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="26f7e-223">Se muestra el resultado de la llamada a `SayHello`:</span><span class="sxs-lookup"><span data-stu-id="26f7e-223">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="26f7e-224">Prueba del cliente gRPC con el servicio gRPC Greeter</span><span class="sxs-lookup"><span data-stu-id="26f7e-224">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26f7e-225">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26f7e-225">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="26f7e-226">En el servicio Greeter, presione `Ctrl+F5` para iniciar el servidor sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="26f7e-226">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="26f7e-227">En el proyecto `GrpcGreeterClient`, presione `Ctrl+F5` para iniciar el cliente sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="26f7e-227">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="26f7e-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="26f7e-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="26f7e-229">Inicie el servicio Greeter.</span><span class="sxs-lookup"><span data-stu-id="26f7e-229">Start the Greeter service.</span></span>
* <span data-ttu-id="26f7e-230">Inicie el cliente.</span><span class="sxs-lookup"><span data-stu-id="26f7e-230">Start the client.</span></span>


# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="26f7e-231">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="26f7e-231">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="26f7e-232">Debido a la [solución del problema de TLS de HTTP/2 en macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos) mencionada anteriormente, tiene que actualizar la dirección del canal del cliente a "http://localhost:5000".</span><span class="sxs-lookup"><span data-stu-id="26f7e-232">Due to the previously mentioned [HTTP/2 TLS issue on macOS workaround](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos), you'll need to update the channel address in the client to "http://localhost:5000".</span></span> <span data-ttu-id="26f7e-233">Actualice la línea 13 de **GrpcGreeterClient/Program.cs** de modo que indique:</span><span class="sxs-lookup"><span data-stu-id="26f7e-233">Update line 13 of **GrpcGreeterClient/Program.cs** to read:</span></span>
  ```csharp
  using var channel = GrpcChannel.ForAddress("http://localhost:5000");
  ``` 
* <span data-ttu-id="26f7e-234">Inicie el servicio Greeter.</span><span class="sxs-lookup"><span data-stu-id="26f7e-234">Start the Greeter service.</span></span>
* <span data-ttu-id="26f7e-235">Inicie el cliente.</span><span class="sxs-lookup"><span data-stu-id="26f7e-235">Start the client.</span></span>

---

<span data-ttu-id="26f7e-236">El cliente envía un saludo al servicio con un mensaje que contiene su nombre *GreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="26f7e-236">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="26f7e-237">El servicio envía el mensaje "Hello GreeterClient" como respuesta.</span><span class="sxs-lookup"><span data-stu-id="26f7e-237">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="26f7e-238">La respuesta "Hello GreeterClient" se muestra en el símbolo del sistema:</span><span class="sxs-lookup"><span data-stu-id="26f7e-238">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="26f7e-239">El servicio gRPC registra los detalles de la llamada correcta en los registros escritos en el símbolo del sistema:</span><span class="sxs-lookup"><span data-stu-id="26f7e-239">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

> [!NOTE]
> <span data-ttu-id="26f7e-240">El código de este artículo requiere el certificado de desarrollo de .NET Core HTTPS para proteger el servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="26f7e-240">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="26f7e-241">Si el cliente gRPC de .NET genera un error con el mensaje `The remote certificate is invalid according to the validation procedure.` o `The SSL connection could not be established.`, el certificado de desarrollo no es de confianza.</span><span class="sxs-lookup"><span data-stu-id="26f7e-241">If the .NET gRPC client fails with the message `The remote certificate is invalid according to the validation procedure.` or `The SSL connection could not be established.`, the development certificate isn't trusted.</span></span> <span data-ttu-id="26f7e-242">Para corregir este problema, consulte [Llamada a un servicio gRPC con un certificado no válido o que no es de confianza](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span><span class="sxs-lookup"><span data-stu-id="26f7e-242">To fix this issue, see [Call a gRPC service with an untrusted/invalid certificate](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="26f7e-243">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="26f7e-243">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
