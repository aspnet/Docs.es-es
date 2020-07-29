---
title: Uso de LibMan con ASP.NET Core en Visual Studio
author: scottaddie
description: Obtenga información sobre cómo usar LibMan en un proyecto de ASP.NET Core con Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
no-loc:
- '[Blazor'
- '[Blazor Server'
- '[Blazor WebAssembly'
- '[Identity'
- "[Let's Encrypt"
- '[Razor'
- '[SignalR'
uid: client-side/libman/libman-vs
ms.openlocfilehash: 504c34ccd8813273161b86504700704f8a932538
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403175"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a><span data-ttu-id="a2004-103">Uso de LibMan con ASP.NET Core en Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a2004-103">Use LibMan with ASP.NET Core in Visual Studio</span></span>

<span data-ttu-id="a2004-104">Por [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="a2004-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="a2004-105">Visual Studio tiene compatibilidad integrada con [LibMan](xref:client-side/libman/index) en proyectos de ASP.NET Core, incluido lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="a2004-105">Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span></span>

* <span data-ttu-id="a2004-106">Compatibilidad con la configuración y ejecución de operaciones de restauración de LibMan durante la compilación.</span><span class="sxs-lookup"><span data-stu-id="a2004-106">Support for configuring and running LibMan restore operations on build.</span></span>
* <span data-ttu-id="a2004-107">Elementos de menú para desencadenar operaciones de restauración y limpieza de LibMan.</span><span class="sxs-lookup"><span data-stu-id="a2004-107">Menu items for triggering LibMan restore and clean operations.</span></span>
* <span data-ttu-id="a2004-108">Cuadro de diálogo de búsqueda para buscar bibliotecas y agregar los archivos a un proyecto.</span><span class="sxs-lookup"><span data-stu-id="a2004-108">Search dialog for finding libraries and adding the files to a project.</span></span>
* <span data-ttu-id="a2004-109">Compatibilidad de edición para *libman.json*, el archivo de manifiesto de LibMan.</span><span class="sxs-lookup"><span data-stu-id="a2004-109">Editing support for *libman.json*&mdash;the LibMan manifest file.</span></span>

<span data-ttu-id="a2004-110">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(cómo descargarlo)](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="a2004-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a2004-111">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="a2004-111">Prerequisites</span></span>

* <span data-ttu-id="a2004-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **ASP.NET y desarrollo web**</span><span class="sxs-lookup"><span data-stu-id="a2004-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>

## <a name="add-library-files"></a><span data-ttu-id="a2004-113">Adición de archivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="a2004-113">Add library files</span></span>

<span data-ttu-id="a2004-114">Los archivos de biblioteca se pueden agregar a un proyecto de ASP.NET Core de dos maneras diferentes:</span><span class="sxs-lookup"><span data-stu-id="a2004-114">Library files can be added to an ASP.NET Core project in two different ways:</span></span>

1. [<span data-ttu-id="a2004-115">Usar el cuadro de diálogo Agregar biblioteca de lado del cliente</span><span class="sxs-lookup"><span data-stu-id="a2004-115">Use the Add Client-Side Library dialog</span></span>](#use-the-add-client-side-library-dialog)
1. [<span data-ttu-id="a2004-116">Configurar manualmente las entradas del archivo de manifiesto de LibMan</span><span class="sxs-lookup"><span data-stu-id="a2004-116">Manually configure LibMan manifest file entries</span></span>](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a><span data-ttu-id="a2004-117">Uso del cuadro de diálogo Agregar biblioteca de lado del cliente</span><span class="sxs-lookup"><span data-stu-id="a2004-117">Use the Add Client-Side Library dialog</span></span>

<span data-ttu-id="a2004-118">Siga estos pasos para instalar una biblioteca del lado cliente:</span><span class="sxs-lookup"><span data-stu-id="a2004-118">Follow these steps to install a client-side library:</span></span>

* <span data-ttu-id="a2004-119">En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta del proyecto en la que se deben agregar los archivos.</span><span class="sxs-lookup"><span data-stu-id="a2004-119">In **Solution Explorer**, right-click the project folder in which the files should be added.</span></span> <span data-ttu-id="a2004-120">Seleccione **Agregar** > **Biblioteca del lado cliente**.</span><span class="sxs-lookup"><span data-stu-id="a2004-120">Choose **Add** > **Client-Side Library**.</span></span> <span data-ttu-id="a2004-121">Aparecerá el cuadro de diálogo **Agregar biblioteca de lado del cliente**:</span><span class="sxs-lookup"><span data-stu-id="a2004-121">The **Add Client-Side Library** dialog appears:</span></span>

  ![Cuadro de diálogo Agregar biblioteca de lado del cliente](_static/add-library-dialog.png)

* <span data-ttu-id="a2004-123">Seleccione el proveedor de biblioteca en la lista desplegable **Proveedor**.</span><span class="sxs-lookup"><span data-stu-id="a2004-123">Select the library provider from the **Provider** drop down.</span></span> <span data-ttu-id="a2004-124">CDNJS es el proveedor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="a2004-124">CDNJS is the default provider.</span></span>
* <span data-ttu-id="a2004-125">Escriba el nombre de la biblioteca que se va a capturar en el cuadro de texto **Biblioteca**.</span><span class="sxs-lookup"><span data-stu-id="a2004-125">Type the library name to fetch in the **Library** text box.</span></span> <span data-ttu-id="a2004-126">IntelliSense proporciona una lista de bibliotecas que comienzan por el texto proporcionado.</span><span class="sxs-lookup"><span data-stu-id="a2004-126">IntelliSense provides a list of libraries beginning with the provided text.</span></span>
* <span data-ttu-id="a2004-127">Seleccione la biblioteca de la lista de IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="a2004-127">Select the library from the IntelliSense list.</span></span> <span data-ttu-id="a2004-128">Observe que el nombre de la biblioteca tiene como sufijo el símbolo `@` y la versión estable más reciente conocida del proveedor seleccionado.</span><span class="sxs-lookup"><span data-stu-id="a2004-128">Notice the library name is suffixed with the `@` symbol and the latest stable version known to the selected provider.</span></span>
* <span data-ttu-id="a2004-129">Decida qué archivos se van a incluir:</span><span class="sxs-lookup"><span data-stu-id="a2004-129">Decide which files to include:</span></span>
  * <span data-ttu-id="a2004-130">Seleccione el botón de radio **Incluir todos los archivos de biblioteca** para incluir todos los archivos de la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="a2004-130">Select the **Include all library files** radio button to include all of the library's files.</span></span>
  * <span data-ttu-id="a2004-131">Seleccione el botón de radio **Elegir archivos específicos** para incluir un subconjunto de los archivos de la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="a2004-131">Select the **Choose specific files** radio button to include a subset of the library's files.</span></span> <span data-ttu-id="a2004-132">Al seleccionar el botón de radio, se habilita el árbol del selector de archivos.</span><span class="sxs-lookup"><span data-stu-id="a2004-132">When the radio button is selected, the file selector tree is enabled.</span></span> <span data-ttu-id="a2004-133">Marque las casillas situadas a la izquierda de los nombres de archivo que se van a descargar.</span><span class="sxs-lookup"><span data-stu-id="a2004-133">Check the boxes to the left of the file names to download.</span></span>
* <span data-ttu-id="a2004-134">Especifique la carpeta del proyecto para almacenar los archivos en el cuadro de texto **Ubicación de destino**.</span><span class="sxs-lookup"><span data-stu-id="a2004-134">Specify the project folder for storing the files in the **Target Location** text box.</span></span> <span data-ttu-id="a2004-135">Como recomendación, almacene cada biblioteca en una carpeta independiente.</span><span class="sxs-lookup"><span data-stu-id="a2004-135">As a recommendation, store each library in a separate folder.</span></span>

  <span data-ttu-id="a2004-136">La carpeta **Ubicación de destino** sugerida se basa en la ubicación desde la que se ha iniciado el cuadro de diálogo:</span><span class="sxs-lookup"><span data-stu-id="a2004-136">The suggested **Target Location** folder is based on the location from which the dialog launched:</span></span>

  * <span data-ttu-id="a2004-137">Si se ha iniciado desde la raíz del proyecto:</span><span class="sxs-lookup"><span data-stu-id="a2004-137">If launched from the project root:</span></span>
    * <span data-ttu-id="a2004-138">Se usa *wwwroot/lib* si existe *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="a2004-138">*wwwroot/lib* is used if *wwwroot* exists.</span></span>
    * <span data-ttu-id="a2004-139">Se usa *lib* si *wwwroot* no existe.</span><span class="sxs-lookup"><span data-stu-id="a2004-139">*lib* is used if *wwwroot* doesn't exist.</span></span>
  * <span data-ttu-id="a2004-140">Si se ha iniciado desde una carpeta de proyecto, se usa el nombre de carpeta correspondiente.</span><span class="sxs-lookup"><span data-stu-id="a2004-140">If launched from a project folder, the corresponding folder name is used.</span></span>

  <span data-ttu-id="a2004-141">La sugerencia de carpeta tiene como sufijo el nombre de la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="a2004-141">The folder suggestion is suffixed with the library name.</span></span> <span data-ttu-id="a2004-142">En la tabla siguiente se muestran sugerencias de carpeta al instalar jQuery en un proyecto de [Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="a2004-142">The following table illustrates folder suggestions when installing jQuery in a [Razor Pages project.</span></span>
  
  |<span data-ttu-id="a2004-143">Ubicación de inicio</span><span class="sxs-lookup"><span data-stu-id="a2004-143">Launch location</span></span>                           |<span data-ttu-id="a2004-144">Carpeta sugerida</span><span class="sxs-lookup"><span data-stu-id="a2004-144">Suggested folder</span></span>      |
  |------------------------------------------|----------------------|
  |<span data-ttu-id="a2004-145">Raíz del proyecto (si existe *wwwroot*)</span><span class="sxs-lookup"><span data-stu-id="a2004-145">project root (if *wwwroot* exists)</span></span>        |<span data-ttu-id="a2004-146">*wwwroot/lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="a2004-146">*wwwroot/lib/jquery/*</span></span> |
  |<span data-ttu-id="a2004-147">Raíz del proyecto (si no existe *wwwroot*)</span><span class="sxs-lookup"><span data-stu-id="a2004-147">project root (if *wwwroot* doesn't exist)</span></span> |<span data-ttu-id="a2004-148">*lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="a2004-148">*lib/jquery/*</span></span>         |
  |<span data-ttu-id="a2004-149">Carpeta *Pages* del proyecto</span><span class="sxs-lookup"><span data-stu-id="a2004-149">*Pages* folder in project</span></span>                 |<span data-ttu-id="a2004-150">*Pages/jquery/*</span><span class="sxs-lookup"><span data-stu-id="a2004-150">*Pages/jquery/*</span></span>       |

* <span data-ttu-id="a2004-151">Haga clic en el botón **Instalar** para descargar los archivos, según la configuración de *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="a2004-151">Click the **Install** button to download the files, per the configuration in *libman.json*.</span></span>
* <span data-ttu-id="a2004-152">Revise la fuente **Administrador de bibliotecas** de la ventana **Salida** para obtener detalles de la instalación.</span><span class="sxs-lookup"><span data-stu-id="a2004-152">Review the **Library Manager** feed of the **Output** window for installation details.</span></span> <span data-ttu-id="a2004-153">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a2004-153">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a><span data-ttu-id="a2004-154">Configuración manual de las entradas del archivo de manifiesto de LibMan</span><span class="sxs-lookup"><span data-stu-id="a2004-154">Manually configure LibMan manifest file entries</span></span>

<span data-ttu-id="a2004-155">Todas las operaciones de LibMan en Visual Studio se basan en el contenido del manifiesto de LibMan (*libman.json*) de la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="a2004-155">All LibMan operations in Visual Studio are based on the content of the project root's LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="a2004-156">Puede editar *libman.json* de forma manual para configurar archivos de biblioteca para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="a2004-156">You can manually edit *libman.json* to configure library files for the project.</span></span> <span data-ttu-id="a2004-157">Visual Studio restaura todos los archivos de biblioteca una vez que se ha guardado *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="a2004-157">Visual Studio restores all library files once *libman.json* is saved.</span></span>

<span data-ttu-id="a2004-158">Para abrir *libman.json* y editarlo, existen las opciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="a2004-158">To open *libman.json* for editing, the following options exist:</span></span>

* <span data-ttu-id="a2004-159">Haga doble clic en el archivo *libman.json* en el **Explorador de soluciones**.</span><span class="sxs-lookup"><span data-stu-id="a2004-159">Double-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="a2004-160">Haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y seleccione **Administrar bibliotecas del lado cliente**.</span><span class="sxs-lookup"><span data-stu-id="a2004-160">Right-click the project in **Solution Explorer** and select **Manage Client-Side Libraries**.</span></span> <span data-ttu-id="a2004-161">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="a2004-161">**&#8224;**</span></span>
* <span data-ttu-id="a2004-162">Seleccione **Administrar bibliotecas del lado cliente** en el menú **Proyecto** de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a2004-162">Select **Manage Client-Side Libraries** from the Visual Studio **Project** menu.</span></span> <span data-ttu-id="a2004-163">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="a2004-163">**&#8224;**</span></span>

<span data-ttu-id="a2004-164">**&#8224;** Si en la raíz del proyecto todavía no existe el archivo *libman.json*, se creará con el contenido de la plantilla de elemento predeterminada.</span><span class="sxs-lookup"><span data-stu-id="a2004-164">**&#8224;** If the *libman.json* file doesn't already exist in the project root, it will be created with the default item template content.</span></span>

<span data-ttu-id="a2004-165">Visual Studio ofrece una amplia compatibilidad para la edición de JSON, como el uso de colores, la aplicación de formato, IntelliSense y la validación de esquemas.</span><span class="sxs-lookup"><span data-stu-id="a2004-165">Visual Studio offers rich JSON editing support such as colorization, formatting, IntelliSense, and schema validation.</span></span> <span data-ttu-id="a2004-166">El esquema JSON del manifiesto LibMan se encuentra en [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span><span class="sxs-lookup"><span data-stu-id="a2004-166">The LibMan manifest's JSON schema is found at [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span></span>

<span data-ttu-id="a2004-167">Con el siguiente archivo de manifiesto, LibMan recupera archivos según la configuración definida en la propiedad `libraries`.</span><span class="sxs-lookup"><span data-stu-id="a2004-167">With the following manifest file, LibMan retrieves files per the configuration defined in the `libraries` property.</span></span> <span data-ttu-id="a2004-168">A continuación se ofrece una explicación de los literales de objeto definidos en `libraries`:</span><span class="sxs-lookup"><span data-stu-id="a2004-168">An explanation of the object literals defined within `libraries` follows:</span></span>

* <span data-ttu-id="a2004-169">Un subconjunto de [jQuery](https://jquery.com/) versión 3.3.1 se recupera del proveedor CDNJS.</span><span class="sxs-lookup"><span data-stu-id="a2004-169">A subset of [jQuery](https://jquery.com/) version 3.3.1 is retrieved from the CDNJS provider.</span></span> <span data-ttu-id="a2004-170">El subconjunto se define en la propiedad `files`: *jquery.min.js*, *jquery.js* y *jquery.min.map*.</span><span class="sxs-lookup"><span data-stu-id="a2004-170">The subset is defined in the `files` property&mdash;*jquery.min.js*, *jquery.js*, and *jquery.min.map*.</span></span> <span data-ttu-id="a2004-171">Los archivos se colocan en la carpeta *wwwroot/lib/jquery* del proyecto.</span><span class="sxs-lookup"><span data-stu-id="a2004-171">The files are placed in the project's *wwwroot/lib/jquery* folder.</span></span>
* <span data-ttu-id="a2004-172">La totalidad de la versión de [arranque](https://getbootstrap.com/) 4.1.3 se recupera y se coloca en una carpeta *wwwroot/lib/bootstrap*.</span><span class="sxs-lookup"><span data-stu-id="a2004-172">The entirety of [Bootstrap](https://getbootstrap.com/) version 4.1.3 is retrieved and placed in a *wwwroot/lib/bootstrap* folder.</span></span> <span data-ttu-id="a2004-173">La propiedad `provider` del literal de objeto reemplaza el valor de la propiedad `defaultProvider`.</span><span class="sxs-lookup"><span data-stu-id="a2004-173">The object literal's `provider` property overrides the `defaultProvider` property value.</span></span> <span data-ttu-id="a2004-174">LibMan recupera los archivos de arranque del proveedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="a2004-174">LibMan retrieves the Bootstrap files from the unpkg provider.</span></span>
* <span data-ttu-id="a2004-175">Un órgano rector de la organización ha aprobado un subconjunto de [Lodash](https://lodash.com/).</span><span class="sxs-lookup"><span data-stu-id="a2004-175">A subset of [Lodash](https://lodash.com/) was approved by a governing body within the organization.</span></span> <span data-ttu-id="a2004-176">Los archivos *lodash.js* y *lodash.min.js* se recuperan del sistema de archivos local en *C:\\temp\\lodash\\* .</span><span class="sxs-lookup"><span data-stu-id="a2004-176">The *lodash.js* and *lodash.min.js* files are retrieved from the local file system at *C:\\temp\\lodash\\*.</span></span> <span data-ttu-id="a2004-177">Los archivos se copian en la carpeta *wwwroot/lib/lodash* del proyecto.</span><span class="sxs-lookup"><span data-stu-id="a2004-177">The files are copied to the project's *wwwroot/lib/lodash* folder.</span></span>

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> <span data-ttu-id="a2004-178">LibMan solo admite una versión de cada biblioteca de cada proveedor.</span><span class="sxs-lookup"><span data-stu-id="a2004-178">LibMan only supports one version of each library from each provider.</span></span> <span data-ttu-id="a2004-179">El archivo *libman.json* produce un error en la validación del esquema si contiene dos bibliotecas con el mismo nombre para un proveedor determinado.</span><span class="sxs-lookup"><span data-stu-id="a2004-179">The *libman.json* file fails schema validation if it contains two libraries with the same library name for a given provider.</span></span>

## <a name="restore-library-files"></a><span data-ttu-id="a2004-180">Restauración de archivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="a2004-180">Restore library files</span></span>

<span data-ttu-id="a2004-181">Para restaurar archivos de biblioteca desde Visual Studio, debe haber un archivo *libman.json* válido en la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="a2004-181">To restore library files from within Visual Studio, there must be a valid *libman.json* file in the project root.</span></span> <span data-ttu-id="a2004-182">Los archivos restaurados se colocan en el proyecto en la ubicación especificada para cada biblioteca.</span><span class="sxs-lookup"><span data-stu-id="a2004-182">Restored files are placed in the project at the location specified for each library.</span></span>

<span data-ttu-id="a2004-183">Hay dos maneras de restaurar los archivos de biblioteca en un proyecto de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a2004-183">Library files can be restored in an ASP.NET Core project in two ways:</span></span>

1. [<span data-ttu-id="a2004-184">Restaurar los archivos durante la compilación</span><span class="sxs-lookup"><span data-stu-id="a2004-184">Restore files during build</span></span>](#restore-files-during-build)
1. [<span data-ttu-id="a2004-185">Restaurar los archivos de forma manual</span><span class="sxs-lookup"><span data-stu-id="a2004-185">Restore files manually</span></span>](#restore-files-manually)

### <a name="restore-files-during-build"></a><span data-ttu-id="a2004-186">Restauración de los archivos durante la compilación</span><span class="sxs-lookup"><span data-stu-id="a2004-186">Restore files during build</span></span>

<span data-ttu-id="a2004-187">LibMan puede restaurar los archivos de biblioteca definidos como parte del proceso de compilación.</span><span class="sxs-lookup"><span data-stu-id="a2004-187">LibMan can restore the defined library files as part of the build process.</span></span> <span data-ttu-id="a2004-188">De forma predeterminada, el comportamiento de *restauración al realizar la compilación* está deshabilitado.</span><span class="sxs-lookup"><span data-stu-id="a2004-188">By default, the *restore-on-build* behavior is disabled.</span></span>

<span data-ttu-id="a2004-189">Para habilitar y probar el comportamiento de la restauración al realizar la compilación:</span><span class="sxs-lookup"><span data-stu-id="a2004-189">To enable and test the restore-on-build behavior:</span></span>

* <span data-ttu-id="a2004-190">Haga clic con el botón derecho en *libman.json*, en el **Explorador de soluciones**, y seleccione **Habilitar la restauración de bibliotecas del lado cliente al compilar** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="a2004-190">Right-click *libman.json* in **Solution Explorer** and select **Enable Restore Client-Side Libraries on Build** from the context menu.</span></span>
* <span data-ttu-id="a2004-191">Haga clic en el botón **Sí** cuando se le pida que instale un paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="a2004-191">Click the **Yes** button when prompted to install a NuGet package.</span></span> <span data-ttu-id="a2004-192">El paquete NuGet [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) se agrega al proyecto:</span><span class="sxs-lookup"><span data-stu-id="a2004-192">The [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet package is added to the project:</span></span>

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* <span data-ttu-id="a2004-193">Compile el proyecto para confirmar que se haya restaurado el archivo de LibMan.</span><span class="sxs-lookup"><span data-stu-id="a2004-193">Build the project to confirm LibMan file restoration occurs.</span></span> <span data-ttu-id="a2004-194">El paquete `Microsoft.Web.LibraryManager.Build` inserta un destino de MSBuild que ejecuta LibMan durante la operación de compilación del proyecto.</span><span class="sxs-lookup"><span data-stu-id="a2004-194">The `Microsoft.Web.LibraryManager.Build` package injects an MSBuild target that runs LibMan during the project's build operation.</span></span>
* <span data-ttu-id="a2004-195">Revise la fuente **Compilación** de la ventana **Salida** para obtener un registro de actividad de LibMan:</span><span class="sxs-lookup"><span data-stu-id="a2004-195">Review the **Build** feed of the **Output** window for a LibMan activity log:</span></span>

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

<span data-ttu-id="a2004-196">Si el comportamiento de restauración al compilar está habilitado, en el menú contextual de *libman.json* se muestra una opción **Deshabilitar la restauración de bibliotecas del lado cliente al compilar**.</span><span class="sxs-lookup"><span data-stu-id="a2004-196">When the restore-on-build behavior is enabled, the *libman.json* context menu displays a **Disable Restore Client-Side Libraries on Build** option.</span></span> <span data-ttu-id="a2004-197">Al seleccionar esta opción, se quita la referencia del paquete `Microsoft.Web.LibraryManager.Build` del archivo del proyecto.</span><span class="sxs-lookup"><span data-stu-id="a2004-197">Selecting this option removes the `Microsoft.Web.LibraryManager.Build` package reference from the project file.</span></span> <span data-ttu-id="a2004-198">Por tanto, las bibliotecas del lado cliente ya no se restauran en cada compilación.</span><span class="sxs-lookup"><span data-stu-id="a2004-198">Consequently, the client-side libraries are no longer restored on each build.</span></span>

<span data-ttu-id="a2004-199">Con independencia de la configuración de la restauración al compilar, puede restaurar manualmente en cualquier momento desde el menú contextual de *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="a2004-199">Regardless of the restore-on-build setting, you can manually restore at any time from the *libman.json* context menu.</span></span> <span data-ttu-id="a2004-200">Para obtener más información, vea [Restauración manual de los archivos](#restore-files-manually).</span><span class="sxs-lookup"><span data-stu-id="a2004-200">For more information, see [Restore files manually](#restore-files-manually).</span></span>

### <a name="restore-files-manually"></a><span data-ttu-id="a2004-201">Restauración manual de los archivos</span><span class="sxs-lookup"><span data-stu-id="a2004-201">Restore files manually</span></span>

<span data-ttu-id="a2004-202">Para restaurar los archivos de biblioteca de forma manual:</span><span class="sxs-lookup"><span data-stu-id="a2004-202">To manually restore library files:</span></span>

* <span data-ttu-id="a2004-203">Para todos los proyectos de la solución:</span><span class="sxs-lookup"><span data-stu-id="a2004-203">For all projects in the solution:</span></span>
  * <span data-ttu-id="a2004-204">Haga clic con el botón derecho en el nombre de la solución en el **Explorador de soluciones**.</span><span class="sxs-lookup"><span data-stu-id="a2004-204">Right-click the solution name in **Solution Explorer**.</span></span>
  * <span data-ttu-id="a2004-205">Seleccione la opción **Restaurar bibliotecas del lado cliente**.</span><span class="sxs-lookup"><span data-stu-id="a2004-205">Select the **Restore Client-Side Libraries** option.</span></span>
* <span data-ttu-id="a2004-206">Para un proyecto específico:</span><span class="sxs-lookup"><span data-stu-id="a2004-206">For a specific project:</span></span>
  * <span data-ttu-id="a2004-207">Haga clic con el botón derecho en el archivo *libman.json* en el **Explorador de soluciones**.</span><span class="sxs-lookup"><span data-stu-id="a2004-207">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
  * <span data-ttu-id="a2004-208">Seleccione la opción **Restaurar bibliotecas del lado cliente**.</span><span class="sxs-lookup"><span data-stu-id="a2004-208">Select the **Restore Client-Side Libraries** option.</span></span>

<span data-ttu-id="a2004-209">Mientras se ejecuta la operación de restauración:</span><span class="sxs-lookup"><span data-stu-id="a2004-209">While the restore operation is running:</span></span>

* <span data-ttu-id="a2004-210">El icono del Centro de estado de tareas (TSC) de la barra de estado de Visual Studio se animará y mostrará *Se inició la operación de restauración*.</span><span class="sxs-lookup"><span data-stu-id="a2004-210">The Task Status Center (TSC) icon on the Visual Studio status bar will be animated and will read *Restore operation started*.</span></span> <span data-ttu-id="a2004-211">Al hacer clic en el icono, se abrirá una información en pantalla que enumerará las tareas conocidas en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="a2004-211">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="a2004-212">Los mensajes se enviarán a la barra de estado y la fuente **Administrador de bibliotecas** de la ventana **Salida**.</span><span class="sxs-lookup"><span data-stu-id="a2004-212">Messages will be sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="a2004-213">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a2004-213">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a><span data-ttu-id="a2004-214">Eliminación de archivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="a2004-214">Delete library files</span></span>

<span data-ttu-id="a2004-215">Para realizar la operación de *limpieza*, que elimina los archivos de biblioteca restaurados anteriormente en Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a2004-215">To perform the *clean* operation, which deletes library files previously restored in Visual Studio:</span></span>

* <span data-ttu-id="a2004-216">Haga clic con el botón derecho en el archivo *libman.json* en el **Explorador de soluciones**.</span><span class="sxs-lookup"><span data-stu-id="a2004-216">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="a2004-217">Seleccione la opción **Limpiar bibliotecas del lado cliente**.</span><span class="sxs-lookup"><span data-stu-id="a2004-217">Select the **Clean Client-Side Libraries** option.</span></span>

<span data-ttu-id="a2004-218">Para evitar la eliminación accidental de archivos que no sean de biblioteca, la operación de limpieza no eliminará directorios completos.</span><span class="sxs-lookup"><span data-stu-id="a2004-218">To prevent unintentional removal of non-library files, the clean operation doesn't delete whole directories.</span></span> <span data-ttu-id="a2004-219">Solo quitará los archivos que se hayan incluido en la restauración anterior.</span><span class="sxs-lookup"><span data-stu-id="a2004-219">It only removes files that were included in the previous restore.</span></span>

<span data-ttu-id="a2004-220">Mientras se ejecuta la operación de limpieza:</span><span class="sxs-lookup"><span data-stu-id="a2004-220">While the clean operation is running:</span></span>

* <span data-ttu-id="a2004-221">El icono TSC de la barra de estado de Visual Studio se animará y mostrará un mensaje en el que se indicará que *la operación de bibliotecas cliente se ha iniciado*.</span><span class="sxs-lookup"><span data-stu-id="a2004-221">The TSC icon on the Visual Studio status bar will be animated and will read *Client libraries operation started*.</span></span> <span data-ttu-id="a2004-222">Al hacer clic en el icono, se abrirá una información en pantalla que enumerará las tareas conocidas en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="a2004-222">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="a2004-223">Los mensajes se envían a la barra de estado y la fuente **Administrador de bibliotecas** de la ventana **Salida**.</span><span class="sxs-lookup"><span data-stu-id="a2004-223">Messages are sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="a2004-224">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a2004-224">For example:</span></span>

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

<span data-ttu-id="a2004-225">La operación de limpieza solo elimina archivos del proyecto.</span><span class="sxs-lookup"><span data-stu-id="a2004-225">The clean operation only deletes files from the project.</span></span> <span data-ttu-id="a2004-226">Los archivos de biblioteca permanecen en caché para una recuperación más rápida en futuras operaciones de restauración.</span><span class="sxs-lookup"><span data-stu-id="a2004-226">Library files stay in the cache for faster retrieval on future restore operations.</span></span> <span data-ttu-id="a2004-227">Para administrar los archivos de biblioteca almacenados en la caché del equipo local, use la [CLI de LibMan](xref:client-side/libman/libman-cli).</span><span class="sxs-lookup"><span data-stu-id="a2004-227">To manage library files stored in the local machine's cache, use the [LibMan CLI](xref:client-side/libman/libman-cli).</span></span>

## <a name="uninstall-library-files"></a><span data-ttu-id="a2004-228">Desinstalación de archivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="a2004-228">Uninstall library files</span></span>

<span data-ttu-id="a2004-229">Para desinstalar archivos de biblioteca:</span><span class="sxs-lookup"><span data-stu-id="a2004-229">To uninstall library files:</span></span>

* <span data-ttu-id="a2004-230">Abra *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="a2004-230">Open *libman.json*.</span></span>
* <span data-ttu-id="a2004-231">Sitúe el cursor dentro del literal de objeto `libraries` correspondiente.</span><span class="sxs-lookup"><span data-stu-id="a2004-231">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="a2004-232">Haga clic en el icono de bombilla que aparece en el margen izquierdo y seleccione **Desinstalar \<library_name>@\<library_version>** :</span><span class="sxs-lookup"><span data-stu-id="a2004-232">Click the light bulb icon that appears in the left margin, and select **Uninstall \<library_name>@\<library_version>**:</span></span>

  ![Opción Desinstalar biblioteca del menú contextual](_static/uninstall-menu-option.png)

<span data-ttu-id="a2004-234">Como alternativa, puede editar y guardar manualmente el manifiesto de LibMan (*libman.json*).</span><span class="sxs-lookup"><span data-stu-id="a2004-234">Alternatively, you can manually edit and save the LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="a2004-235">La [operación de restauración](#restore-library-files) se ejecuta cuando se guarda el archivo.</span><span class="sxs-lookup"><span data-stu-id="a2004-235">The [restore operation](#restore-library-files) runs when the file is saved.</span></span> <span data-ttu-id="a2004-236">Los archivos de biblioteca que ya no se definen en *libman.json* se quitan del proyecto.</span><span class="sxs-lookup"><span data-stu-id="a2004-236">Library files that are no longer defined in *libman.json* are removed from the project.</span></span>

## <a name="update-library-version"></a><span data-ttu-id="a2004-237">Actualización de la versión de la biblioteca</span><span class="sxs-lookup"><span data-stu-id="a2004-237">Update library version</span></span>

<span data-ttu-id="a2004-238">Para comprobar si hay una versión actualizada de la biblioteca:</span><span class="sxs-lookup"><span data-stu-id="a2004-238">To check for an updated library version:</span></span>

* <span data-ttu-id="a2004-239">Abra *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="a2004-239">Open *libman.json*.</span></span>
* <span data-ttu-id="a2004-240">Sitúe el cursor dentro del literal de objeto `libraries` correspondiente.</span><span class="sxs-lookup"><span data-stu-id="a2004-240">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="a2004-241">Haga clic en el icono de bombilla que aparece en el margen izquierdo.</span><span class="sxs-lookup"><span data-stu-id="a2004-241">Click the light bulb icon that appears in the left margin.</span></span> <span data-ttu-id="a2004-242">Mantenga el cursor sobre **Buscar actualizaciones**.</span><span class="sxs-lookup"><span data-stu-id="a2004-242">Hover over **Check for updates**.</span></span>

<span data-ttu-id="a2004-243">LibMan comprueba si hay una versión de la biblioteca más reciente que la versión instalada.</span><span class="sxs-lookup"><span data-stu-id="a2004-243">LibMan checks for a library version newer than the version installed.</span></span> <span data-ttu-id="a2004-244">Se pueden producir los resultados siguientes:</span><span class="sxs-lookup"><span data-stu-id="a2004-244">The following outcomes can occur:</span></span>

* <span data-ttu-id="a2004-245">Si ya está instalada la versión más reciente, se muestra un mensaje **No se encontraron actualizaciones**.</span><span class="sxs-lookup"><span data-stu-id="a2004-245">A **No updates found** message is displayed if the latest version is already installed.</span></span>
* <span data-ttu-id="a2004-246">Se muestra la versión estable más reciente si no está instalada.</span><span class="sxs-lookup"><span data-stu-id="a2004-246">The latest stable version is displayed if not already installed.</span></span>

  ![Opción Buscar actualizaciones del menú contextual](_static/update-menu-option.png)

* <span data-ttu-id="a2004-248">Si hay disponible una versión preliminar más reciente que la instalada, se muestra la versión preliminar.</span><span class="sxs-lookup"><span data-stu-id="a2004-248">If a pre-release newer than the installed version is available, the pre-release is displayed.</span></span>

<span data-ttu-id="a2004-249">Para cambiar a una versión anterior de la biblioteca, edite manualmente el archivo *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="a2004-249">To downgrade to an older library version, manually edit the *libman.json* file.</span></span> <span data-ttu-id="a2004-250">Al guardar el archivo, la [operación de restauración](#restore-library-files) de LibMan:</span><span class="sxs-lookup"><span data-stu-id="a2004-250">When the file is saved, the LibMan [restore operation](#restore-library-files):</span></span>

* <span data-ttu-id="a2004-251">Se quitan los archivos redundantes de la versión anterior.</span><span class="sxs-lookup"><span data-stu-id="a2004-251">Removes redundant files from the previous version.</span></span>
* <span data-ttu-id="a2004-252">Se agregan archivos nuevos y actualizados de la versión nueva.</span><span class="sxs-lookup"><span data-stu-id="a2004-252">Adds new and updated files from the new version.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a2004-253">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="a2004-253">Additional resources</span></span>

* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="a2004-254">Repositorio de LibMan en GitHub</span><span class="sxs-lookup"><span data-stu-id="a2004-254">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
