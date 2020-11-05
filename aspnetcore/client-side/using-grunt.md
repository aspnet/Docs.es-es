---
title: Uso de Grunt en ASP.NET Core
author: rick-anderson
description: Uso de Grunt en ASP.NET Core
ms.author: riande
ms.date: 12/05/2019
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
uid: client-side/using-grunt
ms.openlocfilehash: 374c23f440dcf301b3a1e1e9e6684dd050f218c6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054559"
---
# <a name="use-grunt-in-aspnet-core"></a>Uso de Grunt en ASP.NET Core

Grunt es ejecutor de tareas de JavaScript que automatiza la minificación de scripts, la compilación de TypeScript, las herramientas de "lint" de calidad del código, los preprocesadores de CSS y las tareas repetitivas que se necesitan para admitir el desarrollo del cliente. Grunt es totalmente compatible con Visual Studio.

En este ejemplo se usa un proyecto de ASP.NET Core vacío como punto inicial para mostrar cómo automatizar el proceso de compilación del cliente desde cero.

El ejemplo finalizado limpia el directorio de implementación de destino, combina los archivos JavaScript, comprueba la calidad del código, condensa el contenido del archivo JavaScript y se implementa en la raíz de la aplicación web. Usaremos los siguientes paquetes:

* **grunt** : El paquete del ejecutor de tareas de Grunt.

* **grunt-contrib-clean** : Un complemento que quita archivos o directorios.

* **grunt-contrib-jshint** : Un complemento que revisa la calidad del código de JavaScript.

* **grunt-contrib-concat** : Un complemento que une archivos en un único archivo.

* **grunt-contrib-uglify** : Un complemento que minifica JavaScript para reducir el tamaño.

* **grunt-contrib-watch** : Un complemento que inspecciona la actividad de archivo.

## <a name="preparing-the-application"></a>Preparar la aplicación

Para empezar, configure una nueva aplicación web vacía y agregue los archivos de ejemplo de TypeScript. Los archivos TypeScript se compilan automáticamente en JavaScript con la configuración predeterminada de Visual Studio y serán nuestra materia prima para procesar con Grunt.

1. En Visual Studio, cree un nuevo `ASP.NET Web Application`.

2. En el cuadro de diálogo **Nuevo proyecto ASP.NET** , seleccione la plantilla **Vacía** de ASP.NET Core y haga clic en el botón Aceptar.

3. En el Explorador de soluciones, revise la estructura del proyecto. La carpeta `\src` incluye nodos `wwwroot` y `Dependencies` vacíos.

    ![solución web vacía](using-grunt/_static/grunt-solution-explorer.png)

4. Agregue una nueva carpeta denominada `TypeScript`al directorio del proyecto.

5. Antes de agregar archivos, asegúrese de que Visual Studio tiene activada la opción 'compilar al guardar' para los archivos TypeScript. Navegue a **Herramientas** > **Opciones** > **Editor de texto** > **Typescript** > **Proyecto** :

    ![compilación automática de la configuración de opciones de los archivos TypeScript](using-grunt/_static/typescript-options.png)

6. Haga clic con el botón derecho en el directorio `TypeScript` y seleccione **Agregar > Nuevo elemento** en el menú contextual. Seleccione el elemento **Archivo JavaScript** y asigne al archivo el nombre *Tastes.ts* (tenga en cuenta la extensión \*.ts). Copie la línea del siguiente código de TypeScript en el archivo (al guardar, aparecerá un nuevo archivo *Tastes.js* con el origen de JavaScript).

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. Agregue un segundo archivo al directorio **TypeScript** y asígnele el nombre `Food.ts`. Copie el código que se indica a continuación en el archivo.

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

## <a name="configuring-npm"></a>Configuración de NPM

A continuación, configure NPM para descargar grunt y grunt-tasks.

1. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y, en el menú contextual, seleccione **Agregar > Nuevo elemento**. Seleccione el elemento **archivo de configuración de NPM** , deje el nombre predeterminado *package.json* y haga clic en el botón **Agregar**.

2. En el archivo *package.json* , dentro de las llaves del objeto `devDependencies` y escriba "grunt". Seleccione `grunt` en la lista de IntelliSense y presione la tecla Entrar. Visual Studio citará el nombre del paquete de grunt y agregará un signo de dos puntos. A la derecha de los dos puntos, seleccione la versión estable más reciente del paquete en la parte superior de la lista de IntelliSense (presione `Ctrl-Space` si IntelliSense no aparece).

    ![IntelliSense de grunt](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > NPM usa el [versionamiento semántico](https://semver.org/) para organizar las dependencias. El versionamiento semántico, también conocido como SemVer, identifica paquetes con el esquema de numeración \<major>.\<minor>.\<patch>. IntelliSense simplifica el versionamiento semántico mostrando únicamente algunas opciones comunes. El elemento superior de la lista de IntelliSense (0.4.5 en el ejemplo anterior) se considera la versión estable más reciente del paquete. El símbolo de intercalación (^) coincide con la versión principal más reciente y la tilde de la ñ (~) coincide con la versión secundaria más reciente. Vea la [referencia del analizador de versiones SemVer de NPM](https://www.npmjs.com/package/semver) como guía para el expresividad completa que SemVer proporciona.

3. Agregue más dependencias para cargar paquetes grunt-contrib-\* para *clean* , *jshint* , *concat* , *uglify* y *watch* , tal como se muestra en el siguiente ejemplo. No es necesario que las versiones coincidan con el ejemplo.

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

4. Guarde el archivo *package.json*.

Se descargarán los paquetes de cada elemento `devDependencies`, junto con los archivos que requiera cada paquete. Puede buscar los archivos de paquete en el directorio *node_modules* habilitando el botón **Mostrar todos los archivos** en **Explorador de soluciones**.

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> Si es necesario, puede restaurar manualmente las dependencias en **Explorador de soluciones** haciendo clic con el botón derecho en `Dependencies\NPM` y seleccionando la opción del menú **Restaurar paquetes**.

![restaurar paquetes](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Configuración de Grunt

Grunt está configurado con un manifiesto denominado *Gruntfile.js* que define, carga y registra las tareas que se pueden ejecutar manualmente o configurar para que se ejecuten automáticamente en función de los eventos de Visual Studio.

1. Haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **Nuevo elemento**. Seleccione la plantilla de elemento **Archivo JavaScript** , cambie el nombre a *Gruntfile.js* y haga clic en el botón **Agregar**.

1. Agregue el código siguiente a *Gruntfile.js*. La función `initConfig` establece las opciones para cada paquete y el resto del módulo carga y registra las tareas.

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. Dentro de la función `initConfig`, agregue opciones para la tarea `clean`, tal como se muestra en el ejemplo *Gruntfile.js* a continuación. La tarea `clean` acepta una matriz de cadenas de directorio. Esta tarea quita archivos de *wwwroot/lib* y quita todo el directorio */temp*.

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. Debajo de la función `initConfig`, agregue una llamada a `grunt.loadNpmTasks`. Esto hará que la tarea se pueda ejecutar desde Visual Studio.

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. Guarde *Gruntfile.js*. El archivo debe tener un aspecto similar al de la siguiente captura de pantalla.

    ![initial gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. Haga clic con el botón derecho en  *Gruntfile.js* y seleccione **Explorador del Ejecutor de tareas** en el menú contextual. Se abrirá la ventana **Explorador del Ejecutor de tareas**.

    ![menú del explorador del ejecutor de tareas](using-grunt/_static/task-runner-explorer-menu.png)

1. Compruebe que `clean` aparezca en **Tareas** en el **Explorador del Ejecutor de tareas**.

    ![lista de tareas del explorador del ejecutor de tareas](using-grunt/_static/task-runner-explorer-tasks.png)

1. Haga clic con el botón derecho en la tarea de limpieza y seleccione **Ejecutar** en el menú contextual. Una ventana de comandos muestra el progreso de la tarea.

    ![tarea de limpieza para ejecutar el explorador del ejecutor de tareas](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > No hay archivos o directorios para limpiar todavía. Si lo desea, puede crearlos manualmente en el Explorador de soluciones y, a continuación, ejecutar la tarea de limpieza como prueba.

1. En la función `initConfig`, agregue una entrada para `concat` mediante el código siguiente.

    La matriz de propiedades `src` enumera los archivos que se van a combinar, en el orden en que deben combinarse. La propiedad `dest` asigna la ruta de acceso al archivo combinado que se genera.

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > La propiedad `all` en el código anterior es el nombre de un destino. Los destinos se usan en algunas tareas de Grunt para permitir varios entornos de compilación. Puede ver los destinos integrados mediante IntelliSense o asignarse el suyo propio.

1. Agregue la tarea `jshint` mediante el código siguiente.

    La utilidad `code-quality` de jshint se ejecuta en cada archivo JavaScript que se encuentra en el directorio *temp*.

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > La opción "-W069" es un error producido por jshint cuando JavaScript usa la sintaxis de corchetes para asignar una propiedad en lugar de la notación de puntos, es decir, `Tastes["Sweet"]` en lugar de `Tastes.Sweet`. La opción desactiva la advertencia para permitir que continúe el resto del proceso.

1. Agregue la tarea `uglify` mediante el código siguiente.

    La tarea minifica el archivo *combined.js* que se encuentra en el directorio temporal y crea el archivo de resultados en wwwroot/lib siguiendo la convención de nomenclatura estándar *\<file name\>.min.js*.

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. En la llamada a `grunt.loadNpmTasks` que carga `grunt-contrib-clean`, incluya la misma llamada para jshint, concat y uglify con el código siguiente.

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. Guarde *Gruntfile.js*. El archivo debe tener un aspecto similar al del siguiente ejemplo.

    ![ejemplo de archivo grunt completo](using-grunt/_static/gruntfile-js-complete.png)

1. Tenga en cuenta que la lista de tareas del **explorador del ejecutor de tareas** incluye tareas `clean`, `concat`, `jshint` y `uglify`. Ejecute cada tarea en orden y observe los resultados en **Explorador de soluciones**. Cada tarea se debe ejecutar sin errores.

    ![ejecutor de tareas explorador ejecutar cada tarea](using-grunt/_static/task-runner-explorer-run-each-task.png)

    La tarea concat crea un nuevo archivo *combined.js* y lo coloca en el directorio temporal. La tarea `jshint` simplemente se ejecuta y no genera resultados. La tarea `uglify` crea un nuevo archivo *combined.min.js* y lo coloca en *wwwroot/lib*. Al finalizar, la solución debe tener un aspecto similar al de la siguiente captura de pantalla:

    ![explorador de soluciones después de todas las tareas](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > Para obtener más información sobre las opciones de cada paquete, visite [https://www.npmjs.com/](https://www.npmjs.com/) y busque el nombre del paquete en el cuadro de búsqueda de la página principal. Por ejemplo, puede buscar el paquete grunt-contrib-clean para obtener un vínculo de documentación que explique todos sus parámetros.

### <a name="all-together-now"></a>Todo junto

Utilice el método `registerTask()` de Grunt para ejecutar una serie de tareas en una secuencia determinada. Por ejemplo, para ejecutar los pasos de ejemplo anteriores en el orden clean-> concat-> jshint-> uglify, agregue el código siguiente al módulo. El código se debe agregar al mismo nivel que las llamadas a loadNpmTasks(), fuera de initConfig.

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

La nueva tarea aparece en el explorador del ejecutor de tareas en Tareas de alias. Puede hacer clic con el botón derecho y ejecutarlo como lo haría con otras tareas. La tarea `all` ejecutará `clean`, `concat`, `jshint` y `uglify`, en orden.

![tareas grunt alias](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>Observación de cambios

Una tarea `watch` controla los archivos y directorios. La inspección desencadena tareas automáticamente si detecta cambios. Agregue el código siguiente a initConfig para ver los cambios en los archivos \*.js en el directorio TypeScript. Si se cambia un archivo JavaScript, `watch` ejecutará la tarea `all`.

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

Agregue una llamada a `loadNpmTasks()` para mostrar la tarea `watch` en el explorador del ejecutor de tareas.

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

Haga clic con el botón derecho en la tarea de inspección del explorador del ejecutor de tareas y seleccione Ejecutar en el menú contextual. La ventana Comandos que muestra la tarea de inspección en ejecución mostrará un mensaje "Esperando…". Abra uno de los archivos TypeScript, agregue un espacio y, a continuación, guarde el archivo. Esto desencadenará la tarea de inspección y desencadenará las demás tareas para que se ejecuten en orden. La siguiente captura de pantalla muestra una ejecución de ejemplo.

![resultados de tareas en ejecución](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Enlace a eventos de Visual Studio

A menos que desee iniciar manualmente las tareas cada vez que trabaje en Visual Studio, enlace tareas a los eventos **Antes de la compilación** , **Después de la compilación** , **Limpiar** y **Apertura de proyecto**.

Enlace `watch` para que se ejecute cada vez que se abra Visual Studio. En el explorador del ejecutor de tareas, haga clic con el botón derecho en la tarea de inspección y seleccione **Enlaces** > **Apertura de proyecto** en el menú contextual.

![enlazar una tarea a la apertura del proyecto](using-grunt/_static/bindings-project-open.png)

Descargue y vuelva a cargar el proyecto. Cuando el proyecto se vuelva a cargar, la tarea de inspección comienza a ejecutarse automáticamente.

## <a name="summary"></a>Resumen

Grunt es un ejecutor de tareas eficaz que se puede usar para automatizar la mayoría de las tareas de compilación de cliente. Grunt aprovecha NPM para ofrecer sus paquetes y ofrece integración de herramientas con Visual Studio. El explorador del ejecutor de tareas de Visual Studio detecta cambios en los archivos de configuración y proporciona una interfaz adecuada para ejecutar tareas, ver tareas en ejecución y enlazar tareas a eventos de Visual Studio.
