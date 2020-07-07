---
title: Uso de Grunt en ASP.NET Core
author: rick-anderson
description: Uso de Grunt en ASP.NET Core
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/using-grunt
ms.openlocfilehash: fc871e22f9bd5a9c137008f1d87019542c45b5d2
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401771"
---
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="749e8-103">Uso de Grunt en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="749e8-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="749e8-104">Grunt es ejecutor de tareas de JavaScript que automatiza la minificación de scripts, la compilación de TypeScript, las herramientas de "lint" de calidad del código, los preprocesadores de CSS y las tareas repetitivas que se necesitan para admitir el desarrollo del cliente.</span><span class="sxs-lookup"><span data-stu-id="749e8-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="749e8-105">Grunt es totalmente compatible con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="749e8-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="749e8-106">En este ejemplo se usa un proyecto de ASP.NET Core vacío como punto inicial para mostrar cómo automatizar el proceso de compilación del cliente desde cero.</span><span class="sxs-lookup"><span data-stu-id="749e8-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="749e8-107">El ejemplo finalizado limpia el directorio de implementación de destino, combina los archivos JavaScript, comprueba la calidad del código, condensa el contenido del archivo JavaScript y se implementa en la raíz de la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="749e8-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="749e8-108">Usaremos los siguientes paquetes:</span><span class="sxs-lookup"><span data-stu-id="749e8-108">We will use the following packages:</span></span>

* <span data-ttu-id="749e8-109">**grunt**: El paquete del ejecutor de tareas de Grunt.</span><span class="sxs-lookup"><span data-stu-id="749e8-109">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="749e8-110">**grunt-contrib-clean**: Un complemento que quita archivos o directorios.</span><span class="sxs-lookup"><span data-stu-id="749e8-110">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="749e8-111">**grunt-contrib-jshint**: Un complemento que revisa la calidad del código de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="749e8-111">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="749e8-112">**grunt-contrib-concat**: Un complemento que une archivos en un único archivo.</span><span class="sxs-lookup"><span data-stu-id="749e8-112">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="749e8-113">**grunt-contrib-uglify**: Un complemento que minifica JavaScript para reducir el tamaño.</span><span class="sxs-lookup"><span data-stu-id="749e8-113">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="749e8-114">**grunt-contrib-watch**: Un complemento que inspecciona la actividad de archivo.</span><span class="sxs-lookup"><span data-stu-id="749e8-114">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="749e8-115">Preparar la aplicación</span><span class="sxs-lookup"><span data-stu-id="749e8-115">Preparing the application</span></span>

<span data-ttu-id="749e8-116">Para empezar, configure una nueva aplicación web vacía y agregue los archivos de ejemplo de TypeScript.</span><span class="sxs-lookup"><span data-stu-id="749e8-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="749e8-117">Los archivos TypeScript se compilan automáticamente en JavaScript con la configuración predeterminada de Visual Studio y serán nuestra materia prima para procesar con Grunt.</span><span class="sxs-lookup"><span data-stu-id="749e8-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="749e8-118">En Visual Studio, cree un nuevo `ASP.NET Web Application`.</span><span class="sxs-lookup"><span data-stu-id="749e8-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="749e8-119">En el cuadro de diálogo **Nuevo proyecto ASP.NET**, seleccione la plantilla **Vacía** de ASP.NET Core y haga clic en el botón Aceptar.</span><span class="sxs-lookup"><span data-stu-id="749e8-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="749e8-120">En el Explorador de soluciones, revise la estructura del proyecto.</span><span class="sxs-lookup"><span data-stu-id="749e8-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="749e8-121">La carpeta `\src` incluye nodos `wwwroot` y `Dependencies` vacíos.</span><span class="sxs-lookup"><span data-stu-id="749e8-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![solución web vacía](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="749e8-123">Agregue una nueva carpeta denominada `TypeScript`al directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="749e8-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="749e8-124">Antes de agregar archivos, asegúrese de que Visual Studio tiene activada la opción 'compilar al guardar' para los archivos TypeScript.</span><span class="sxs-lookup"><span data-stu-id="749e8-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="749e8-125">Navegue a **Herramientas** > **Opciones** > **Editor de texto** > **Typescript** > **Proyecto**:</span><span class="sxs-lookup"><span data-stu-id="749e8-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![compilación automática de la configuración de opciones de los archivos TypeScript](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="749e8-127">Haga clic con el botón derecho en el directorio `TypeScript` y seleccione **Agregar > Nuevo elemento** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="749e8-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="749e8-128">Seleccione el elemento **Archivo JavaScript** y asigne al archivo el nombre *Tastes.ts* (tenga en cuenta la extensión \*.ts).</span><span class="sxs-lookup"><span data-stu-id="749e8-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="749e8-129">Copie la línea del siguiente código de TypeScript en el archivo (al guardar, aparecerá un nuevo archivo *Tastes.js* con el origen de JavaScript).</span><span class="sxs-lookup"><span data-stu-id="749e8-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="749e8-130">Agregue un segundo archivo al directorio **TypeScript** y asígnele el nombre `Food.ts`.</span><span class="sxs-lookup"><span data-stu-id="749e8-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="749e8-131">Copie el código que se indica a continuación en el archivo.</span><span class="sxs-lookup"><span data-stu-id="749e8-131">Copy the code below into the file.</span></span>

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }

      private _name: string;
      get Name() {
        return this._name;
      }

      private _calories: number;
      get Calories() {
        return this._calories;
      }

      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a><span data-ttu-id="749e8-132">Configuración de NPM</span><span class="sxs-lookup"><span data-stu-id="749e8-132">Configuring NPM</span></span>

<span data-ttu-id="749e8-133">A continuación, configure NPM para descargar grunt y grunt-tasks.</span><span class="sxs-lookup"><span data-stu-id="749e8-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="749e8-134">En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y, en el menú contextual, seleccione **Agregar > Nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="749e8-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="749e8-135">Seleccione el elemento **archivo de configuración de NPM**, deje el nombre predeterminado *package.json* y haga clic en el botón **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="749e8-135">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="749e8-136">En el archivo *package.json*, dentro de las llaves del objeto `devDependencies` y escriba "grunt".</span><span class="sxs-lookup"><span data-stu-id="749e8-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="749e8-137">Seleccione `grunt` en la lista de IntelliSense y presione la tecla Entrar.</span><span class="sxs-lookup"><span data-stu-id="749e8-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="749e8-138">Visual Studio citará el nombre del paquete de grunt y agregará un signo de dos puntos.</span><span class="sxs-lookup"><span data-stu-id="749e8-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="749e8-139">A la derecha de los dos puntos, seleccione la versión estable más reciente del paquete en la parte superior de la lista de IntelliSense (presione `Ctrl-Space` si IntelliSense no aparece).</span><span class="sxs-lookup"><span data-stu-id="749e8-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![IntelliSense de grunt](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="749e8-141">NPM usa el [versionamiento semántico](https://semver.org/) para organizar las dependencias.</span><span class="sxs-lookup"><span data-stu-id="749e8-141">NPM uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="749e8-142">El versionamiento semántico, también conocido como SemVer, identifica paquetes con el esquema de numeración \<major>.\<minor>.\<patch>. IntelliSense simplifica el versionamiento semántico mostrando únicamente algunas opciones comunes.</span><span class="sxs-lookup"><span data-stu-id="749e8-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>. Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="749e8-143">El elemento superior de la lista de IntelliSense (0.4.5 en el ejemplo anterior) se considera la versión estable más reciente del paquete.</span><span class="sxs-lookup"><span data-stu-id="749e8-143">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="749e8-144">El símbolo de intercalación (^) coincide con la versión principal más reciente y la tilde de la ñ (~) coincide con la versión secundaria más reciente.</span><span class="sxs-lookup"><span data-stu-id="749e8-144">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="749e8-145">Vea la [referencia del analizador de versiones SemVer de NPM](https://www.npmjs.com/package/semver) como guía para el expresividad completa que SemVer proporciona.</span><span class="sxs-lookup"><span data-stu-id="749e8-145">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="749e8-146">Agregue más dependencias para cargar paquetes grunt-contrib-\* para *clean*, *jshint*, *concat*, *uglify* y *watch*, tal como se muestra en el siguiente ejemplo.</span><span class="sxs-lookup"><span data-stu-id="749e8-146">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="749e8-147">No es necesario que las versiones coincidan con el ejemplo.</span><span class="sxs-lookup"><span data-stu-id="749e8-147">The versions don't need to match the example.</span></span>

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. <span data-ttu-id="749e8-148">Guarde el archivo *package.json*.</span><span class="sxs-lookup"><span data-stu-id="749e8-148">Save the *package.json* file.</span></span>

<span data-ttu-id="749e8-149">Se descargarán los paquetes de cada elemento `devDependencies`, junto con los archivos que requiera cada paquete.</span><span class="sxs-lookup"><span data-stu-id="749e8-149">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="749e8-150">Puede buscar los archivos de paquete en el directorio *node_modules* habilitando el botón **Mostrar todos los archivos** en **Explorador de soluciones**.</span><span class="sxs-lookup"><span data-stu-id="749e8-150">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="749e8-152">Si es necesario, puede restaurar manualmente las dependencias en **Explorador de soluciones** haciendo clic con el botón derecho en `Dependencies\NPM` y seleccionando la opción del menú **Restaurar paquetes**.</span><span class="sxs-lookup"><span data-stu-id="749e8-152">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![restaurar paquetes](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="749e8-154">Configuración de Grunt</span><span class="sxs-lookup"><span data-stu-id="749e8-154">Configuring Grunt</span></span>

<span data-ttu-id="749e8-155">Grunt está configurado con un manifiesto denominado *Gruntfile.js* que define, carga y registra las tareas que se pueden ejecutar manualmente o configurar para que se ejecuten automáticamente en función de los eventos de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="749e8-155">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="749e8-156">Haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **Nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="749e8-156">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="749e8-157">Seleccione la plantilla de elemento **Archivo JavaScript**, cambie el nombre a *Gruntfile.js* y haga clic en el botón **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="749e8-157">Select the **JavaScript File** item template, change the name to *Gruntfile.js*, and click the **Add** button.</span></span>

1. <span data-ttu-id="749e8-158">Agregue el código siguiente a *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="749e8-158">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="749e8-159">La función `initConfig` establece las opciones para cada paquete y el resto del módulo carga y registra las tareas.</span><span class="sxs-lookup"><span data-stu-id="749e8-159">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="749e8-160">Dentro de la función `initConfig`, agregue opciones para la tarea `clean`, tal como se muestra en el ejemplo *Gruntfile.js* a continuación.</span><span class="sxs-lookup"><span data-stu-id="749e8-160">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="749e8-161">La tarea `clean` acepta una matriz de cadenas de directorio.</span><span class="sxs-lookup"><span data-stu-id="749e8-161">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="749e8-162">Esta tarea quita archivos de *wwwroot/lib* y quita todo el directorio */temp*.</span><span class="sxs-lookup"><span data-stu-id="749e8-162">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="749e8-163">Debajo de la función `initConfig`, agregue una llamada a `grunt.loadNpmTasks`.</span><span class="sxs-lookup"><span data-stu-id="749e8-163">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="749e8-164">Esto hará que la tarea se pueda ejecutar desde Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="749e8-164">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="749e8-165">Guarde *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="749e8-165">Save *Gruntfile.js*.</span></span> <span data-ttu-id="749e8-166">El archivo debe tener un aspecto similar al de la siguiente captura de pantalla.</span><span class="sxs-lookup"><span data-stu-id="749e8-166">The file should look something like the screenshot below.</span></span>

    ![initial gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="749e8-168">Haga clic con el botón derecho en  *Gruntfile.js* y seleccione **Explorador del Ejecutor de tareas** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="749e8-168">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="749e8-169">Se abrirá la ventana **Explorador del Ejecutor de tareas**.</span><span class="sxs-lookup"><span data-stu-id="749e8-169">The **Task Runner Explorer** window will open.</span></span>

    ![menú del explorador del ejecutor de tareas](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="749e8-171">Compruebe que `clean` aparezca en **Tareas** en el **Explorador del Ejecutor de tareas**.</span><span class="sxs-lookup"><span data-stu-id="749e8-171">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![lista de tareas del explorador del ejecutor de tareas](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="749e8-173">Haga clic con el botón derecho en la tarea de limpieza y seleccione **Ejecutar** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="749e8-173">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="749e8-174">Una ventana de comandos muestra el progreso de la tarea.</span><span class="sxs-lookup"><span data-stu-id="749e8-174">A command window displays progress of the task.</span></span>

    ![tarea de limpieza para ejecutar el explorador del ejecutor de tareas](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="749e8-176">No hay archivos o directorios para limpiar todavía.</span><span class="sxs-lookup"><span data-stu-id="749e8-176">There are no files or directories to clean yet.</span></span> <span data-ttu-id="749e8-177">Si lo desea, puede crearlos manualmente en el Explorador de soluciones y, a continuación, ejecutar la tarea de limpieza como prueba.</span><span class="sxs-lookup"><span data-stu-id="749e8-177">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="749e8-178">En la función `initConfig`, agregue una entrada para `concat` mediante el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="749e8-178">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="749e8-179">La matriz de propiedades `src` enumera los archivos que se van a combinar, en el orden en que deben combinarse.</span><span class="sxs-lookup"><span data-stu-id="749e8-179">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="749e8-180">La propiedad `dest` asigna la ruta de acceso al archivo combinado que se genera.</span><span class="sxs-lookup"><span data-stu-id="749e8-180">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="749e8-181">La propiedad `all` en el código anterior es el nombre de un destino.</span><span class="sxs-lookup"><span data-stu-id="749e8-181">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="749e8-182">Los destinos se usan en algunas tareas de Grunt para permitir varios entornos de compilación.</span><span class="sxs-lookup"><span data-stu-id="749e8-182">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="749e8-183">Puede ver los destinos integrados mediante IntelliSense o asignarse el suyo propio.</span><span class="sxs-lookup"><span data-stu-id="749e8-183">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="749e8-184">Agregue la tarea `jshint` mediante el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="749e8-184">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="749e8-185">La utilidad `code-quality` de jshint se ejecuta en cada archivo JavaScript que se encuentra en el directorio *temp*.</span><span class="sxs-lookup"><span data-stu-id="749e8-185">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="749e8-186">La opción "-W069" es un error producido por jshint cuando JavaScript usa la sintaxis de corchetes para asignar una propiedad en lugar de la notación de puntos, es decir, `Tastes["Sweet"]` en lugar de `Tastes.Sweet`.</span><span class="sxs-lookup"><span data-stu-id="749e8-186">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="749e8-187">La opción desactiva la advertencia para permitir que continúe el resto del proceso.</span><span class="sxs-lookup"><span data-stu-id="749e8-187">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="749e8-188">Agregue la tarea `uglify` mediante el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="749e8-188">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="749e8-189">La tarea minifica el archivo *combined.js* que se encuentra en el directorio temporal y crea el archivo de resultados en wwwroot/lib siguiendo la convención de nomenclatura estándar *\<file name\>.min.js*.</span><span class="sxs-lookup"><span data-stu-id="749e8-189">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="749e8-190">En la llamada a `grunt.loadNpmTasks` que carga `grunt-contrib-clean`, incluya la misma llamada para jshint, concat y uglify con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="749e8-190">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="749e8-191">Guarde *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="749e8-191">Save *Gruntfile.js*.</span></span> <span data-ttu-id="749e8-192">El archivo debe tener un aspecto similar al del siguiente ejemplo.</span><span class="sxs-lookup"><span data-stu-id="749e8-192">The file should look something like the example below.</span></span>

    ![ejemplo de archivo grunt completo](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="749e8-194">Tenga en cuenta que la lista de tareas del **explorador del ejecutor de tareas** incluye tareas `clean`, `concat`, `jshint` y `uglify`.</span><span class="sxs-lookup"><span data-stu-id="749e8-194">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="749e8-195">Ejecute cada tarea en orden y observe los resultados en **Explorador de soluciones**.</span><span class="sxs-lookup"><span data-stu-id="749e8-195">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="749e8-196">Cada tarea se debe ejecutar sin errores.</span><span class="sxs-lookup"><span data-stu-id="749e8-196">Each task should run without errors.</span></span>

    ![ejecutor de tareas explorador ejecutar cada tarea](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="749e8-198">La tarea concat crea un nuevo archivo *combined.js* y lo coloca en el directorio temporal.</span><span class="sxs-lookup"><span data-stu-id="749e8-198">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="749e8-199">La tarea `jshint` simplemente se ejecuta y no genera resultados.</span><span class="sxs-lookup"><span data-stu-id="749e8-199">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="749e8-200">La tarea `uglify` crea un nuevo archivo *combined.min.js* y lo coloca en *wwwroot/lib*.</span><span class="sxs-lookup"><span data-stu-id="749e8-200">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="749e8-201">Al finalizar, la solución debe tener un aspecto similar al de la siguiente captura de pantalla:</span><span class="sxs-lookup"><span data-stu-id="749e8-201">On completion, the solution should look something like the screenshot below:</span></span>

    ![explorador de soluciones después de todas las tareas](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="749e8-203">Para obtener más información sobre las opciones de cada paquete, visite [https://www.npmjs.com/](https://www.npmjs.com/) y busque el nombre del paquete en el cuadro de búsqueda de la página principal.</span><span class="sxs-lookup"><span data-stu-id="749e8-203">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="749e8-204">Por ejemplo, puede buscar el paquete grunt-contrib-clean para obtener un vínculo de documentación que explique todos sus parámetros.</span><span class="sxs-lookup"><span data-stu-id="749e8-204">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="749e8-205">Todo junto</span><span class="sxs-lookup"><span data-stu-id="749e8-205">All together now</span></span>

<span data-ttu-id="749e8-206">Utilice el método `registerTask()` de Grunt para ejecutar una serie de tareas en una secuencia determinada.</span><span class="sxs-lookup"><span data-stu-id="749e8-206">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="749e8-207">Por ejemplo, para ejecutar los pasos de ejemplo anteriores en el orden clean-> concat-> jshint-> uglify, agregue el código siguiente al módulo.</span><span class="sxs-lookup"><span data-stu-id="749e8-207">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="749e8-208">El código se debe agregar al mismo nivel que las llamadas a loadNpmTasks(), fuera de initConfig.</span><span class="sxs-lookup"><span data-stu-id="749e8-208">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="749e8-209">La nueva tarea aparece en el explorador del ejecutor de tareas en Tareas de alias.</span><span class="sxs-lookup"><span data-stu-id="749e8-209">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="749e8-210">Puede hacer clic con el botón derecho y ejecutarlo como lo haría con otras tareas.</span><span class="sxs-lookup"><span data-stu-id="749e8-210">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="749e8-211">La tarea `all` ejecutará `clean`, `concat`, `jshint` y `uglify`, en orden.</span><span class="sxs-lookup"><span data-stu-id="749e8-211">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![tareas grunt alias](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="749e8-213">Observación de cambios</span><span class="sxs-lookup"><span data-stu-id="749e8-213">Watching for changes</span></span>

<span data-ttu-id="749e8-214">Una tarea `watch` controla los archivos y directorios.</span><span class="sxs-lookup"><span data-stu-id="749e8-214">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="749e8-215">La inspección desencadena tareas automáticamente si detecta cambios.</span><span class="sxs-lookup"><span data-stu-id="749e8-215">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="749e8-216">Agregue el código siguiente a initConfig para ver los cambios en los archivos \*.js en el directorio TypeScript.</span><span class="sxs-lookup"><span data-stu-id="749e8-216">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="749e8-217">Si se cambia un archivo JavaScript, `watch` ejecutará la tarea `all`.</span><span class="sxs-lookup"><span data-stu-id="749e8-217">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="749e8-218">Agregue una llamada a `loadNpmTasks()` para mostrar la tarea `watch` en el explorador del ejecutor de tareas.</span><span class="sxs-lookup"><span data-stu-id="749e8-218">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="749e8-219">Haga clic con el botón derecho en la tarea de inspección del explorador del ejecutor de tareas y seleccione Ejecutar en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="749e8-219">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="749e8-220">La ventana Comandos que muestra la tarea de inspección en ejecución mostrará un mensaje "Esperando…".</span><span class="sxs-lookup"><span data-stu-id="749e8-220">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="749e8-221">Abra uno de los archivos TypeScript, agregue un espacio y, a continuación, guarde el archivo.</span><span class="sxs-lookup"><span data-stu-id="749e8-221">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="749e8-222">Esto desencadenará la tarea de inspección y desencadenará las demás tareas para que se ejecuten en orden.</span><span class="sxs-lookup"><span data-stu-id="749e8-222">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="749e8-223">La siguiente captura de pantalla muestra una ejecución de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="749e8-223">The screenshot below shows a sample run.</span></span>

![resultados de tareas en ejecución](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="749e8-225">Enlace a eventos de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="749e8-225">Binding to Visual Studio events</span></span>

<span data-ttu-id="749e8-226">A menos que desee iniciar manualmente las tareas cada vez que trabaje en Visual Studio, enlace tareas a los eventos **Antes de la compilación**, **Después de la compilación**, **Limpiar** y **Apertura de proyecto**.</span><span class="sxs-lookup"><span data-stu-id="749e8-226">Unless you want to manually start your tasks every time you work in Visual Studio, bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="749e8-227">Enlace `watch` para que se ejecute cada vez que se abra Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="749e8-227">Bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="749e8-228">En el explorador del ejecutor de tareas, haga clic con el botón derecho en la tarea de inspección y seleccione **Enlaces** > **Apertura de proyecto** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="749e8-228">In Task Runner Explorer, right-click the watch task and select **Bindings** > **Project Open** from the context menu.</span></span>

![enlazar una tarea a la apertura del proyecto](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="749e8-230">Descargue y vuelva a cargar el proyecto.</span><span class="sxs-lookup"><span data-stu-id="749e8-230">Unload and reload the project.</span></span> <span data-ttu-id="749e8-231">Cuando el proyecto se vuelva a cargar, la tarea de inspección comienza a ejecutarse automáticamente.</span><span class="sxs-lookup"><span data-stu-id="749e8-231">When the project loads again, the watch task starts running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="749e8-232">Resumen</span><span class="sxs-lookup"><span data-stu-id="749e8-232">Summary</span></span>

<span data-ttu-id="749e8-233">Grunt es un ejecutor de tareas eficaz que se puede usar para automatizar la mayoría de las tareas de compilación de cliente.</span><span class="sxs-lookup"><span data-stu-id="749e8-233">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="749e8-234">Grunt aprovecha NPM para ofrecer sus paquetes y ofrece integración de herramientas con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="749e8-234">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="749e8-235">El explorador del ejecutor de tareas de Visual Studio detecta cambios en los archivos de configuración y proporciona una interfaz adecuada para ejecutar tareas, ver tareas en ejecución y enlazar tareas a eventos de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="749e8-235">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>
