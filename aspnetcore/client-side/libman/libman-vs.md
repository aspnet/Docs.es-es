---
title: Uso de LibMan con ASP.NET Core en Visual Studio
author: scottaddie
description: Obtenga información sobre cómo usar LibMan en un proyecto de ASP.NET Core con Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
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
uid: client-side/libman/libman-vs
ms.openlocfilehash: ae2bc34edaea2df6f329e47b00c7c02cc59d03bd
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589275"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a>Uso de LibMan con ASP.NET Core en Visual Studio

Por [Scott Addie](https://twitter.com/Scott_Addie)

Visual Studio tiene compatibilidad integrada con [LibMan](xref:client-side/libman/index) en proyectos de ASP.NET Core, incluido lo siguiente:

* Compatibilidad con la configuración y ejecución de operaciones de restauración de LibMan durante la compilación.
* Elementos de menú para desencadenar operaciones de restauración y limpieza de LibMan.
* Cuadro de diálogo de búsqueda para buscar bibliotecas y agregar los archivos a un proyecto.
* Compatibilidad de edición para *libman.json*, el archivo de manifiesto de LibMan.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/client-side/libman/samples/) [(cómo descargarlo)](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Requisitos previos

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **ASP.NET y desarrollo web**

## <a name="add-library-files"></a>Adición de archivos de biblioteca

Los archivos de biblioteca se pueden agregar a un proyecto de ASP.NET Core de dos maneras diferentes:

1. [Usar el cuadro de diálogo Agregar biblioteca de lado del cliente](#use-the-add-client-side-library-dialog)
1. [Configurar manualmente las entradas del archivo de manifiesto de LibMan](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a>Uso del cuadro de diálogo Agregar biblioteca de lado del cliente

Siga estos pasos para instalar una biblioteca del lado cliente:

* En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta del proyecto en la que se deben agregar los archivos. Seleccione **Agregar** > **Biblioteca del lado cliente**. Aparecerá el cuadro de diálogo **Agregar biblioteca de lado del cliente**:

  ![Cuadro de diálogo Agregar biblioteca de lado del cliente](_static/add-library-dialog.png)

* Seleccione el proveedor de biblioteca en la lista desplegable **Proveedor**. CDNJS es el proveedor predeterminado.
* Escriba el nombre de la biblioteca que se va a capturar en el cuadro de texto **Biblioteca**. IntelliSense proporciona una lista de bibliotecas que comienzan por el texto proporcionado.
* Seleccione la biblioteca de la lista de IntelliSense. Observe que el nombre de la biblioteca tiene como sufijo el símbolo `@` y la versión estable más reciente conocida del proveedor seleccionado.
* Decida qué archivos se van a incluir:
  * Seleccione el botón de radio **Incluir todos los archivos de biblioteca** para incluir todos los archivos de la biblioteca.
  * Seleccione el botón de radio **Elegir archivos específicos** para incluir un subconjunto de los archivos de la biblioteca. Al seleccionar el botón de radio, se habilita el árbol del selector de archivos. Marque las casillas situadas a la izquierda de los nombres de archivo que se van a descargar.
* Especifique la carpeta del proyecto para almacenar los archivos en el cuadro de texto **Ubicación de destino**. Como recomendación, almacene cada biblioteca en una carpeta independiente.

  La carpeta **Ubicación de destino** sugerida se basa en la ubicación desde la que se ha iniciado el cuadro de diálogo:

  * Si se ha iniciado desde la raíz del proyecto:
    * Se usa *wwwroot/lib* si existe *wwwroot*.
    * Se usa *lib* si *wwwroot* no existe.
  * Si se ha iniciado desde una carpeta de proyecto, se usa el nombre de carpeta correspondiente.

  La sugerencia de carpeta tiene como sufijo el nombre de la biblioteca. En la tabla siguiente se muestran sugerencias de carpeta al instalar jQuery en un proyecto de Razor Pages.
  
  |Ubicación de inicio                           |Carpeta sugerida      |
  |------------------------------------------|----------------------|
  |Raíz del proyecto (si existe *wwwroot*)        |*wwwroot/lib/jquery/* |
  |Raíz del proyecto (si no existe *wwwroot*) |*lib/jquery/*         |
  |Carpeta *Pages* del proyecto                 |*Pages/jquery/*       |

* Haga clic en el botón **Instalar** para descargar los archivos, según la configuración de *libman.json*.
* Revise la fuente **Administrador de bibliotecas** de la ventana **Salida** para obtener detalles de la instalación. Por ejemplo:

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

### <a name="manually-configure-libman-manifest-file-entries"></a>Configuración manual de las entradas del archivo de manifiesto de LibMan

Todas las operaciones de LibMan en Visual Studio se basan en el contenido del manifiesto de LibMan (*libman.json*) de la raíz del proyecto. Puede editar *libman.json* de forma manual para configurar archivos de biblioteca para el proyecto. Visual Studio restaura todos los archivos de biblioteca una vez que se ha guardado *libman.json*.

Para abrir *libman.json* y editarlo, existen las opciones siguientes:

* Haga doble clic en el archivo *libman.json* en el **Explorador de soluciones**.
* Haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y seleccione **Administrar bibliotecas del lado cliente**. **&#8224;**
* Seleccione **Administrar bibliotecas del lado cliente** en el menú **Proyecto** de Visual Studio. **&#8224;**

**&#8224;** Si en la raíz del proyecto todavía no existe el archivo *libman.json*, se creará con el contenido de la plantilla de elemento predeterminada.

Visual Studio ofrece una amplia compatibilidad para la edición de JSON, como el uso de colores, la aplicación de formato, IntelliSense y la validación de esquemas. El esquema JSON del manifiesto LibMan se encuentra en [https://json.schemastore.org/libman](https://json.schemastore.org/libman).

Con el siguiente archivo de manifiesto, LibMan recupera archivos según la configuración definida en la propiedad `libraries`. A continuación se ofrece una explicación de los literales de objeto definidos en `libraries`:

* Un subconjunto de [jQuery](https://jquery.com/) versión 3.3.1 se recupera del proveedor CDNJS. El subconjunto se define en la propiedad `files`: *jquery.min.js*, *jquery.js* y *jquery.min.map*. Los archivos se colocan en la carpeta *wwwroot/lib/jquery* del proyecto.
* La totalidad de la versión de [arranque](https://getbootstrap.com/) 4.1.3 se recupera y se coloca en una carpeta *wwwroot/lib/bootstrap*. La propiedad `provider` del literal de objeto reemplaza el valor de la propiedad `defaultProvider`. LibMan recupera los archivos de arranque del proveedor unpkg.
* Un órgano rector de la organización ha aprobado un subconjunto de [Lodash](https://lodash.com/). Los archivos *lodash.js* y *lodash.min.js* se recuperan del sistema de archivos local en *C:\\temp\\lodash\\* . Los archivos se copian en la carpeta *wwwroot/lib/lodash* del proyecto.

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> LibMan solo admite una versión de cada biblioteca de cada proveedor. El archivo *libman.json* produce un error en la validación del esquema si contiene dos bibliotecas con el mismo nombre para un proveedor determinado.

## <a name="restore-library-files"></a>Restauración de archivos de biblioteca

Para restaurar archivos de biblioteca desde Visual Studio, debe haber un archivo *libman.json* válido en la raíz del proyecto. Los archivos restaurados se colocan en el proyecto en la ubicación especificada para cada biblioteca.

Hay dos maneras de restaurar los archivos de biblioteca en un proyecto de ASP.NET Core:

1. [Restaurar los archivos durante la compilación](#restore-files-during-build)
1. [Restaurar los archivos de forma manual](#restore-files-manually)

### <a name="restore-files-during-build"></a>Restauración de los archivos durante la compilación

LibMan puede restaurar los archivos de biblioteca definidos como parte del proceso de compilación. De forma predeterminada, el comportamiento de *restauración al realizar la compilación* está deshabilitado.

Para habilitar y probar el comportamiento de la restauración al realizar la compilación:

* Haga clic con el botón derecho en *libman.json*, en el **Explorador de soluciones**, y seleccione **Habilitar la restauración de bibliotecas del lado cliente al compilar** en el menú contextual.
* Haga clic en el botón **Sí** cuando se le pida que instale un paquete NuGet. El paquete NuGet [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) se agrega al proyecto:

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* Compile el proyecto para confirmar que se haya restaurado el archivo de LibMan. El paquete `Microsoft.Web.LibraryManager.Build` inserta un destino de MSBuild que ejecuta LibMan durante la operación de compilación del proyecto.
* Revise la fuente **Compilación** de la ventana **Salida** para obtener un registro de actividad de LibMan:

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

Si el comportamiento de restauración al compilar está habilitado, en el menú contextual de *libman.json* se muestra una opción **Deshabilitar la restauración de bibliotecas del lado cliente al compilar**. Al seleccionar esta opción, se quita la referencia del paquete `Microsoft.Web.LibraryManager.Build` del archivo del proyecto. Por tanto, las bibliotecas del lado cliente ya no se restauran en cada compilación.

Con independencia de la configuración de la restauración al compilar, puede restaurar manualmente en cualquier momento desde el menú contextual de *libman.json*. Para obtener más información, vea [Restauración manual de los archivos](#restore-files-manually).

### <a name="restore-files-manually"></a>Restauración manual de los archivos

Para restaurar los archivos de biblioteca de forma manual:

* Para todos los proyectos de la solución:
  * Haga clic con el botón derecho en el nombre de la solución en el **Explorador de soluciones**.
  * Seleccione la opción **Restaurar bibliotecas del lado cliente**.
* Para un proyecto específico:
  * Haga clic con el botón derecho en el archivo *libman.json* en el **Explorador de soluciones**.
  * Seleccione la opción **Restaurar bibliotecas del lado cliente**.

Mientras se ejecuta la operación de restauración:

* El icono del Centro de estado de tareas (TSC) de la barra de estado de Visual Studio se animará y mostrará *Se inició la operación de restauración*. Al hacer clic en el icono, se abrirá una información en pantalla que enumerará las tareas conocidas en segundo plano.
* Los mensajes se enviarán a la barra de estado y la fuente **Administrador de bibliotecas** de la ventana **Salida**. Por ejemplo:

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

## <a name="delete-library-files"></a>Eliminación de archivos de biblioteca

Para realizar la operación de *limpieza*, que elimina los archivos de biblioteca restaurados anteriormente en Visual Studio:

* Haga clic con el botón derecho en el archivo *libman.json* en el **Explorador de soluciones**.
* Seleccione la opción **Limpiar bibliotecas del lado cliente**.

Para evitar la eliminación accidental de archivos que no sean de biblioteca, la operación de limpieza no eliminará directorios completos. Solo quitará los archivos que se hayan incluido en la restauración anterior.

Mientras se ejecuta la operación de limpieza:

* El icono TSC de la barra de estado de Visual Studio se animará y mostrará un mensaje en el que se indicará que *la operación de bibliotecas cliente se ha iniciado*. Al hacer clic en el icono, se abrirá una información en pantalla que enumerará las tareas conocidas en segundo plano.
* Los mensajes se envían a la barra de estado y la fuente **Administrador de bibliotecas** de la ventana **Salida**. Por ejemplo:

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

La operación de limpieza solo elimina archivos del proyecto. Los archivos de biblioteca permanecen en caché para una recuperación más rápida en futuras operaciones de restauración. Para administrar los archivos de biblioteca almacenados en la caché del equipo local, use la [CLI de LibMan](xref:client-side/libman/libman-cli).

## <a name="uninstall-library-files"></a>Desinstalación de archivos de biblioteca

Para desinstalar archivos de biblioteca:

* Abra *libman.json*.
* Sitúe el cursor dentro del literal de objeto `libraries` correspondiente.
* Haga clic en el icono de bombilla que aparece en el margen izquierdo y seleccione **Desinstalar \<library_name>@\<library_version>** :

  ![Opción Desinstalar biblioteca del menú contextual](_static/uninstall-menu-option.png)

Como alternativa, puede editar y guardar manualmente el manifiesto de LibMan (*libman.json*). La [operación de restauración](#restore-library-files) se ejecuta cuando se guarda el archivo. Los archivos de biblioteca que ya no se definen en *libman.json* se quitan del proyecto.

## <a name="update-library-version"></a>Actualización de la versión de la biblioteca

Para comprobar si hay una versión actualizada de la biblioteca:

* Abra *libman.json*.
* Sitúe el cursor dentro del literal de objeto `libraries` correspondiente.
* Haga clic en el icono de bombilla que aparece en el margen izquierdo. Mantenga el cursor sobre **Buscar actualizaciones**.

LibMan comprueba si hay una versión de la biblioteca más reciente que la versión instalada. Se pueden producir los resultados siguientes:

* Si ya está instalada la versión más reciente, se muestra un mensaje **No se encontraron actualizaciones**.
* Se muestra la versión estable más reciente si no está instalada.

  ![Opción Buscar actualizaciones del menú contextual](_static/update-menu-option.png)

* Si hay disponible una versión preliminar más reciente que la instalada, se muestra la versión preliminar.

Para cambiar a una versión anterior de la biblioteca, edite manualmente el archivo *libman.json*. Al guardar el archivo, la [operación de restauración](#restore-library-files) de LibMan:

* Se quitan los archivos redundantes de la versión anterior.
* Se agregan archivos nuevos y actualizados de la versión nueva.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:client-side/libman/libman-cli>
* [Repositorio de LibMan en GitHub](https://github.com/aspnet/LibraryManager)
