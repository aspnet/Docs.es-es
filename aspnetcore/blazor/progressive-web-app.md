---
title: Creación de aplicaciones web progresivas con ASP.NET Core Blazor WebAssembly
author: guardrex
description: Obtenga información sobre cómo compilar aplicaciones web progresivas (PWA) basadas en Blazor que usan características de exploradores modernos para comportarse como aplicaciones de escritorio.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/11/2021
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
uid: blazor/progressive-web-app
ms.openlocfilehash: fcf06295deb41f304b92caa82535a1197c909898
ms.sourcegitcommit: 75db2f684a9302b0be7925eab586aa091c6bd19f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2021
ms.locfileid: "99238236"
---
# <a name="build-progressive-web-applications-with-aspnet-core-blazor-webassembly"></a>Creación de aplicaciones web progresivas con ASP.NET Core Blazor WebAssembly

Por [Steve Sanderson](https://github.com/SteveSandersonMS)

Una aplicación web progresiva suele ser una aplicación de página única que usa las API y funciones de exploradores modernos para comportarse como una aplicación de escritorio. Blazor WebAssembly es una plataforma de aplicaciones web del lado cliente basada en estándares, por lo que puede usar cualquier API de explorador, incluidas las de PWA necesarias para las siguientes funcionalidades:

* Trabajo sin conexión y carga instantánea, con independencia de la velocidad de la red.
* Ejecución en una ventana de aplicación propia, no solo en una ventana del explorador.
* Apertura desde el menú de inicio del sistema operativo host o la pantalla principal.
* Recepción de notificaciones push desde un servidor back-end, incluso cuando el usuario no utiliza la aplicación.
* Actualización automática en segundo plano.

La palabra *progresiva* se usa para describir estas aplicaciones porque:

* Es posible que un usuario detecte primero la aplicación y la use en su explorador web como cualquier otra SPA.
* Más adelante, el usuario pasa a instalarla en su sistema operativo y a habilitar las notificaciones push.

## <a name="create-a-project-from-the-pwa-template"></a>Creación de un proyecto a partir de la plantilla de PWA

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Al crear una **aplicación Blazor WebAssembly** en el cuadro de diálogo **Crear un nuevo proyecto**, active la casilla **Aplicación web progresiva**:

![La casilla "Aplicación web progresiva" está activada en el cuadro de diálogo Nuevo proyecto de Visual Studio.](progressive-web-app/_static/image1.png)

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

-->

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code y CLI de .NET Core](#tab/visual-studio-code+netcore-cli)

Use el comando siguiente para crear un proyecto de PWA en un shell de comandos con el modificador `--pwa`:

```dotnetcli
dotnet new blazorwasm -o MyBlazorPwa --pwa
```

En el comando anterior, la opción `-o|--output` crea una carpeta para la aplicación denominada `MyBlazorPwa`.

---

Opcionalmente, PWA se puede configurar para una aplicación creada a partir de la plantilla ASP.NET Core hospedado. El escenario de PWA es independiente del modelo de hospedaje.

## <a name="convert-an-existing-blazor-webassembly-app-into-a-pwa"></a>Conversión de una aplicación de Blazor WebAssembly existente en un PWA

Siga las instrucciones de esta sección para convertir una aplicación de Blazor WebAssembly existente en una PWA.

En el archivo de proyecto de la aplicación:

* Agregue la siguiente propiedad `ServiceWorkerAssetsManifest` a un objeto `PropertyGroup`:

  ```xml
    ...
    <ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
  </PropertyGroup>
   ```

* Agregue el siguiente elemento `ServiceWorker` a un objeto `ItemGroup`:

  ```xml
  <ItemGroup>
    <ServiceWorker Include="wwwroot\service-worker.js" 
      PublishedContent="wwwroot\service-worker.published.js" />
  </ItemGroup>
  ```

Para obtener recursos estáticos, use **uno** de los enfoques siguientes:

::: moniker range=">= aspnetcore-5.0"

* Cree un proyecto de PWA independiente con el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) en un shell de comandos:

  ```dotnetcli
  dotnet new blazorwasm -o MyBlazorPwa --pwa
  ```
  
  En el comando anterior, la opción `-o|--output` crea una carpeta para la aplicación denominada `MyBlazorPwa`.
  
  Si no va a convertir una aplicación para la versión más reciente, pase la opción `-f|--framework`. En el ejemplo siguiente se crea la aplicación para la versión 3.1 de ASP.NET Core:
  
  ```dotnetcli
  dotnet new blazorwasm -o MyBlazorPwa --pwa -f netcoreapp3.1
  ```

* Vaya al repositorio de ASP.NET Core en GitHub, disponible en la siguiente dirección URL, que se vincula al origen y los recursos de referencia de la versión 5.0. Si no va a convertir una aplicación para la versión 5.0, seleccione la versión con la que trabaja en la lista desplegable **Switch branches or tags** (Cambiar ramas o etiquetas) correspondiente a la aplicación.

  [Carpeta `wwwroot` de la plantilla de proyecto Blazor WebAssembly dotnet/aspnetcore (versión 5.0)](https://github.com/dotnet/aspnetcore/tree/release/5.0/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/wwwroot)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Cree un proyecto de PWA independiente con el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) en un shell de comandos. Pase la opción `-f|--framework` para seleccionar la versión. En el ejemplo siguiente se crea la aplicación para la versión 3.1 de ASP.NET Core:
  
  ```dotnetcli
  dotnet new blazorwasm -o MyBlazorPwa --pwa -f netcoreapp3.1
  ```
  
  En el comando anterior, la opción `-o|--output` crea una carpeta para la aplicación denominada `MyBlazorPwa`.

* Vaya al repositorio de ASP.NET Core en GitHub, disponible en la siguiente dirección URL, que se vincula al origen y los recursos de referencia de la versión 3.1:

  [Carpeta `wwwroot` de la plantilla de proyecto Blazor WebAssembly dotnet/aspnetcore (versión 3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/ProjectTemplates/ComponentsWebAssembly.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/wwwroot)

  > [!NOTE]
  > La dirección URL de la plantilla de proyecto Blazor WebAssembly ha cambiado después de la publicación de ASP.NET Core 3.1. Los recursos de referencia para la versión 5.0 o posterior están disponibles en la siguiente dirección URL:
  >
  > [Carpeta `wwwroot` de la plantilla de proyecto Blazor WebAssembly dotnet/aspnetcore (versión 5.0)](https://github.com/dotnet/aspnetcore/tree/release/5.0/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/wwwroot)

::: moniker-end

Desde la carpeta `wwwroot` de origen en la aplicación que ha creado o desde los recursos de referencia del repositorio `dotnet/aspnetcore` de GitHub, copie los archivos siguientes en la carpeta `wwwroot` de la aplicación:

* `icon-512.png`
* `manifest.json`
* `service-worker.js`
* `service-worker.published.js`

En el archivo `wwwroot/index.html` de la aplicación:

* Agregue elementos `<link>` para el manifiesto y el icono de la aplicación:

  ```html
  <link href="manifest.json" rel="manifest" />
  <link rel="apple-touch-icon" sizes="512x512" href="icon-512.png" />
  ```

* Agregue la etiqueta `<script>` siguiente dentro de la etiqueta `</body>` de cierre inmediatamente después de la etiqueta de script `blazor.webassembly.js`:

  ```html
      ...
      <script>navigator.serviceWorker.register('service-worker.js');</script>
  </body>
  ```

## <a name="installation-and-app-manifest"></a>Instalación y manifiesto de la aplicación

Al visitar una aplicación creada con la plantilla PWA, los usuarios tienen la opción de instalar la aplicación en el menú de inicio o la pantalla principal de su sistema operativo, o bien mediante el acoplamiento. La forma en la que se presenta esta opción depende del explorador del usuario. Al usar exploradores basados en Chromium de escritorio, como Edge o Chrome, aparece un botón **Agregar** en la barra de direcciones URL. Después de que el usuario seleccione el botón **Agregar**, recibirá un cuadro de diálogo de confirmación:

![El cuadro de diálogo de confirmación en Google Chrome presenta al usuario un botón Instalar para la aplicación "MyBlazorPwa".](progressive-web-app/_static/image2.png)

En iOS, los visitantes pueden instalar la PWA mediante el botón **Compartir** de Safari y la opción que permite la **adición a la pantalla de inicio**. En Chrome para Android, los usuarios deben seleccionar el botón **Menú** en la esquina superior derecha y, después, **Agregar a la pantalla principal**.

Una vez instalada, la aplicación aparece en una ventana propia, sin ninguna barra de direcciones:

![La aplicación "MyBlazorPwa" se ejecuta en Google Chrome sin una barra de direcciones.](progressive-web-app/_static/image3.png)

Para personalizar el título, la combinación de colores, el icono u otros detalles de la ventana, vea el archivo `manifest.json` del directorio `wwwroot` del proyecto. El esquema de este archivo se define mediante los estándares web. Para obtener más información, vea [Documentación web de MDN: Manifiesto de aplicación web](https://developer.mozilla.org/docs/Web/Manifest).

## <a name="offline-support"></a>Compatibilidad sin conexión

De forma predeterminada, las aplicaciones creadas mediante la plantilla PWA tienen compatibilidad para ejecutarse sin conexión. Un usuario debe visitar primero la aplicación mientras está en línea. El explorador descarga automáticamente y almacena en caché todos los recursos necesarios para trabajar sin conexión.

> [!IMPORTANT]
> La compatibilidad con el desarrollo interferiría con el ciclo de desarrollo habitual de realización y prueba de los cambios. Por tanto, la compatibilidad sin conexión solo está habilitada para las aplicaciones *publicadas*. 

> [!WARNING]
> Si tiene previsto distribuir una PWA habilitada para su uso sin conexión, hay [varias advertencias importantes](#caveats-for-offline-pwas). Estos escenarios son propios de la PWA sin conexión y no específicos de Blazor. Asegúrese de leer y comprender estas advertencias antes de realizar suposiciones sobre cómo funcionará la aplicación habilitada para su uso sin conexión.

Para ver cómo funciona la compatibilidad sin conexión:

1. Publique la aplicación. Para obtener más información, vea <xref:blazor/host-and-deploy/index#publish-the-app>.
1. Implemente la aplicación en un servidor que admita HTTPS y acceda a la aplicación en un explorador mediante su dirección HTTPS segura.
1. Abra las herramientas de desarrollo del explorador y, en la pestaña **Aplicación**, compruebe que se ha registrado un *trabajo de servicio* para el host:

   ![En la pestaña "Aplicación" de las herramientas para desarrolladores de Google Chrome se muestra un trabajo de servicio activado y en ejecución.](progressive-web-app/_static/image4.png)

1. Vuelva a cargar la página y examine la pestaña **Red**. **Trabajo de servicio** o **caché de memoria** se muestran como orígenes de todos los recursos de la página:

   ![Pestaña "Red" de las herramientas para desarrolladores de Google Chrome en la que se muestran los orígenes de todos los recursos de la página.](progressive-web-app/_static/image5.png)

1. Para comprobar que el explorador no depende del acceso a la red para cargar la aplicación, puede:

   * Apagar el servidor web y ver cómo la aplicación continúa funcionando con normalidad, lo que incluye las recargas de páginas. Del mismo modo, la aplicación sigue funcionando con normalidad cuando hay una conexión de red lenta.
   * Indicar al explorador que simule el modo sin conexión en la pestaña **Red**:

   ![Pestaña "Red" de las herramientas para desarrolladores de Google Chrome con el cambio de "En línea" a "Sin conexión" en la lista desplegable del modo de explorador.](progressive-web-app/_static/image6.png)

La compatibilidad sin conexión mediante un trabajo de servicio es un estándar web, no específico de Blazor. Para obtener más información sobre los trabajo de servicio, vea [Documentación web de MDN: API de trabajo de servicio](https://developer.mozilla.org/docs/Web/API/Service_Worker_API). Para obtener más información sobre los patrones de uso comunes de los trabajos de servicio, vea [Google Web: El ciclo de vida del trabajo de servicio](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle).

La plantilla de PWA de Blazor genera dos archivos de trabajo de servicio:

* `wwwroot/service-worker.js`, que se usa durante el desarrollo.
* `wwwroot/service-worker.published.js`, que se usa después de publicar la aplicación.

Para compartir la lógica entre los dos archivos de trabajo de servicio, tenga en cuenta el enfoque siguiente:

* Agregue un tercer archivo de JavaScript para contener la lógica común.
* Use [`self.importScripts`](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) para cargar la lógica común en los dos archivos de trabajo de servicio.

### <a name="cache-first-fetch-strategy"></a>Estrategia de captura desde la caché

El servicio de trabajo integrado `service-worker.published.js` resuelve las solicitudes mediante una estrategia *desde la caché*. Esto significa que el trabajo de servicio prefiere devolver el contenido almacenado en caché, con independencia de si el usuario tiene acceso de red o de si hay contenido más reciente disponible en el servidor.

La estrategia de almacenar primero en caché es valiosa porque:

* **Garantiza la confiabilidad.** El acceso a la red no es un estado booleano. Un usuario no está simplemente en línea o sin conexión:

  * El dispositivo del usuario puede asumir que está en línea, pero es posible que la red sea tan lenta que no sea factible esperar.
  * Es posible que la red devuelva resultados no válidos para ciertas direcciones URL, como cuando hay un portal Wi-Fi cautivo que bloquea o redirige determinadas solicitudes.
  
  Por este motivo la API `navigator.onLine` del explorador no es confiable y no se debe depender de ella.

* **Garantiza la corrección.** Al compilar una memoria caché de recursos sin conexión, el trabajo de servicio usa el hash de contenido para garantizar que ha capturado una instantánea completa y autocoherente de los recursos en un solo instante en el tiempo. Después, esta caché se utiliza como unidad atómica. No tiene sentido solicitar a la red recursos más recientes, ya que las únicas versiones necesarias son las que ya se han almacenado en caché. Todo lo demás implica riesgos de incoherencia e incompatibilidad (por ejemplo, intentar usar versiones de ensamblados .NET que no se han compilado de forma conjunta).

### <a name="background-updates"></a>Actualizaciones en segundo plano

Como modelo mental, puede pensar que una PWA primero sin conexión se comporta como una aplicación móvil que se puede instalar. La aplicación se inicia inmediatamente, con independencia de la conectividad de la red, pero la lógica de la aplicación instalada procede de una instantánea de un momento dado que podría no ser la versión más reciente.

La plantilla de PWA Blazor genera aplicaciones que intentan actualizarse de forma automática en segundo plano cada vez que el usuario realiza una visita y tiene una conexión de red operativa. Así es como funciona:

* Durante la compilación, el proyecto genera un *manifiesto de recursos de trabajo de servicio*. De forma predeterminada, se denomina `service-worker-assets.js`. En el manifiesto se enumeran todos los recursos estáticos que la aplicación necesita para funcionar sin conexión, como los ensamblados .NET, los archivos JavaScript y CSS, incluidos sus hash de contenido. Esta lista la carga el trabajo de servicio para saber qué recursos almacenar en la caché.
* Cada vez que el usuario visita la aplicación, el explorador vuelve a solicitar `service-worker.js` y `service-worker-assets.js` en segundo plano. Los archivos se comparan byte a byte con el trabajo de servicio instalado existente. Si el servidor devuelve contenido cambiado para cualquiera de estos archivos, el trabajo de servicio intenta instalar una nueva versión de sí mismo.
* Al instalar una versión nueva de sí mismo, el trabajo de servicio crea una caché independiente para los recursos sin conexión y comienza a rellenarla con los recursos enumerados en `service-worker-assets.js`. Esta lógica se implementa en la función `onInstall` dentro de `service-worker.published.js`.
* El proceso se completa correctamente cuando todos los recursos se cargan sin errores y todos los hashes de contenido coinciden. Si se realiza correctamente, el nuevo trabajo de servicio entra en un estado de *espera de la activación*. En cuanto el usuario cierra la aplicación (no quedan pestañas de aplicación ni ventanas), el nuevo trabajo de servicio se *activa* y se usa para las posteriores visitas a la aplicación. El trabajo de servicio anterior y su memoria caché se eliminan.
* Si el proceso no se completa correctamente, se descarta la nueva instancia del trabajo de servicio. El proceso de actualización se vuelve a intentar en la próxima visita del usuario; con suerte, tendrá una mejor conexión de red que permita completar las solicitudes.

Para personalizar este proceso, modifique la lógica del trabajo de servicio. Ninguno de los comportamientos anteriores es específico de Blazor; simplemente es la experiencia predeterminada proporcionada por la opción de plantilla de PWA. Para obtener más información, vea [Documentación web de MDN: API de trabajo de servicio](https://developer.mozilla.org/docs/Web/API/Service_Worker_API).

### <a name="how-requests-are-resolved"></a>Procedimientos para resolver las solicitudes

Como se ha descrito en la sección [Estrategia de captura desde la caché](#cache-first-fetch-strategy), el trabajo de servicio predeterminado usa una estrategia *desde la caché*, lo que significa que intenta servir contenido almacenado en caché cuando esté disponible. Si no hay contenido en caché para una dirección URL determinada (por ejemplo, cuando se solicitan datos de una API de back-end), el trabajo de servicio recurre a una solicitud de red normal. La solicitud de red se realiza correctamente si se puede acceder al servidor. Esta lógica se implementa dentro de la función `onFetch` en `service-worker.published.js`.

Si los componentes de Razor de la aplicación dependen de la solicitud de datos a las API de back-end y quiere proporcionar una experiencia de usuario sencilla para las solicitudes erróneas por falta de disponibilidad de la red, implemente lógica en los componentes de la aplicación. Por ejemplo, use solicitudes `try/catch` en torno a <xref:System.Net.Http.HttpClient>.

### <a name="support-server-rendered-pages"></a>Compatibilidad de las páginas representadas por el servidor

Tenga en cuenta lo que sucede cuando el usuario navega por primera vez a una dirección URL como `/counter` o cualquier otro vínculo profundo de la aplicación. En estos casos, no le interesa devolver el contenido en caché como `/counter`, sino que necesita que el explorador cargue el contenido en caché como `/index.html` para iniciar la aplicación Blazor WebAssembly. Estas solicitudes iniciales se conocen como solicitudes de *navegación*, en lugar de:

* Solicitudes de `subresource` para imágenes, hojas de estilo u otros archivos.
* Solicitudes de `fetch/XHR` para datos de API.

El trabajo de servicio predeterminado contiene lógica de casos especiales para las solicitudes de navegación. Para resolver las solicitudes, el trabajo de servicio devuelve el contenido en caché para `/index.html`, independientemente de la dirección URL solicitada. Esta lógica se implementa en la función `onFetch` dentro de `service-worker.published.js`.

Si la aplicación tiene determinadas direcciones URL que deben devolver el código HTML representado por el servidor (y no servir `/index.html` desde la caché), tendrá que editar la lógica en el trabajo de servicio. Si todas las direcciones URL que contienen `/Identity/` se deben controlar como solicitudes normales solo en línea en el servidor, modifique la lógica `onFetch` de `service-worker.published.js`. Busque el código siguiente:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate';
```

Cambie el código por lo siguiente:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
  && !event.request.url.includes('/Identity/');
```

Si no lo hace, independientemente de la conectividad de red, el trabajo de servicio intercepta las solicitudes de esas direcciones URL y las resuelve mediante `/index.html`.

Agregue puntos de conexión adicionales para los proveedores de autenticación externos a la comprobación. En el ejemplo siguiente, se agrega `/signin-google` para la autenticación de Google a la comprobación:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
  && !event.request.url.includes('/Identity/')
  && !event.request.url.includes('/signin-google');
```

No es necesario realizar ninguna acción para el entorno de desarrollo, donde el contenido siempre se captura desde la red.

### <a name="control-asset-caching"></a>Control del almacenamiento en caché de recursos

Si el proyecto define la propiedad `ServiceWorkerAssetsManifest` de MSBuild, las herramientas de compilación de Blazor generan un manifiesto de recursos de trabajo de servicio con el nombre especificado. La plantilla de PWA predeterminada produce un archivo de proyecto que contiene la propiedad siguiente:

```xml
<ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
```

El archivo se coloca en el directorio de salida `wwwroot`, por lo que el explorador puede recuperar este archivo solicitando `/service-worker-assets.js`. Para ver el contenido de este archivo, abra `/bin/Debug/{TARGET FRAMEWORK}/wwwroot/service-worker-assets.js` en un editor de texto. Pero no edite el archivo, ya que se vuelve a generar en cada compilación.

De forma predeterminada, este manifiesto muestra:

* Los recursos administrados por Blazor, como los ensamblados .NET y los archivos de runtime de WebAssembly .NET necesarios para funcionar sin conexión.
* Todos los recursos para publicar contenido en el directorio `wwwroot` de la aplicación, como imágenes, hojas de estilos y archivos JavaScript, incluidos los recursos web estáticos proporcionados por los proyectos externos y los paquetes NuGet.

Puede controlar cuál de estos recursos captura y almacena en caché el trabajo de servicio mediante la edición de la lógica `onInstall` en `service-worker.published.js`. De forma predeterminada, el trabajo de servicio captura y almacena en caché los archivos que coinciden con las extensiones de nombre de archivo web típicas, como `.html`, `.css`, `.js` y `.wasm`, además de tipos de archivo específicos de Blazor WebAssembly (`.dll`, `.pdb`).

Para incluir recursos adicionales que no están presentes en el directorio `wwwroot` de la aplicación, defina entradas `ItemGroup` de MSBuild adicionales, como se muestra en el ejemplo siguiente:

```xml
<ItemGroup>
  <ServiceWorkerAssetsManifestItem Include="MyDirectory\AnotherFile.json"
    RelativePath="MyDirectory\AnotherFile.json" AssetUrl="files/AnotherFile.json" />
</ItemGroup>
```

Los metadatos de `AssetUrl` especifican la dirección URL de base relativa que debe usar el explorador al capturar el recurso en la memoria caché. Puede ser independiente del nombre del archivo de origen en el disco.

> [!IMPORTANT]
> Agregar un elemento `ServiceWorkerAssetsManifestItem` no hace que el archivo se publique en el directorio `wwwroot` de la aplicación. La salida de la publicación se debe controlar por separado. El elemento `ServiceWorkerAssetsManifestItem` solo hace que aparezca una entrada adicional en el manifiesto de recursos de trabajo de servicio.

## <a name="push-notifications"></a>Notificaciones de inserción

Al igual que cualquier otra PWA, una PWA Blazor WebAssembly puede recibir notificaciones de inserción de un servidor back-end. El servidor puede enviar notificaciones push en cualquier momento, incluso cuando el usuario no utiliza la aplicación de forma activa. Por ejemplo, se pueden enviar notificaciones push cuando otro usuario realiza una acción pertinente.

El mecanismo para enviar una notificación de inserción es totalmente independiente de Blazor WebAssembly, ya que lo implementa el servidor back-end, que puede usar cualquier tecnología. Si quiere enviar notificaciones push desde un servidor de ASP.NET Core, considere la posibilidad de [usar una técnica similar al enfoque adoptado en el taller de Blazing Pizza](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications).

El mecanismo para recibir y mostrar una notificación de inserción en el cliente también es independiente de Blazor WebAssembly, ya que se implementa en el archivo JavaScript del trabajo de servicio. Para obtener un ejemplo, vea el [enfoque que se usa en el taller de Blazing Pizza](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications).

## <a name="caveats-for-offline-pwas"></a>Advertencias de PWA sin conexión

No todas las aplicaciones deben intentar admitir el uso sin conexión. La compatibilidad sin conexión agrega una complejidad significativa, aunque no siempre es pertinente para los casos de uso necesarios.

La compatibilidad sin conexión normalmente solo es pertinente:

* Si el almacén de datos principal es local para el explorador. Por ejemplo, el enfoque es relevante en una aplicación con una interfaz de usuario para un dispositivo [IoT](https://en.wikipedia.org/wiki/Internet_of_things) que almacena datos en `localStorage` o [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API).
* Si la aplicación realiza un trabajo significativo para capturar y almacenar en caché los datos de la API de back-end pertinentes para cada usuario, para que puedan desplazarse por ellos sin conexión. Si la aplicación admite la edición, se tendrá que crear un sistema para realizar el seguimiento de los cambios y sincronizar datos con el back-end.
* Si el objetivo es garantizar que la aplicación se cargue de forma inmediata, independientemente de las condiciones de la red. Implemente una experiencia de usuario adecuada en torno a las solicitudes de API de back-end para mostrar el progreso de las solicitudes y comportarse correctamente cuando se produzca un error en las solicitudes debido a la falta de disponibilidad de la red.

Además, las PWA con capacidad sin conexión deben tratar con una gran variedad de complicaciones adicionales. Los desarrolladores se deben familiarizar cuidadosamente con las advertencias de las secciones siguientes.

### <a name="offline-support-only-when-published"></a>Compatibilidad sin conexión solo al realizar la publicación

Durante el desarrollo, normalmente le interesará ver cada cambio reflejado inmediatamente en el explorador, sin pasar por un proceso de actualización en segundo plano. Por tanto, la plantilla de PWA de Blazor solo habilita la compatibilidad sin conexión cuando se publica.

Al compilar una aplicación compatible sin conexión, no basta con probarla en el entorno de desarrollo. Tendrá que probar la aplicación en su estado publicado para entender cómo responde a otras condiciones de red.

### <a name="update-completion-after-user-navigation-away-from-app"></a>Finalización de la actualización tras la navegación del usuario fuera de la aplicación

Las actualizaciones no se completan hasta que el usuario haya navegado fuera de la aplicación en todas las pestañas. Como se ha explicado en la sección [Actualizaciones en segundo plano](#background-updates), después de implementar una actualización en la aplicación, el explorador captura los archivos de trabajo de servicio actualizados para comenzar el proceso de actualización.

Lo que sorprende a muchos desarrolladores es que, incluso cuando se completa esta actualización, **no** surtirá efecto hasta que el usuario haya navegado fuera de todas las pestañas. **No** basta con actualizar la pestaña en la que se muestra la aplicación, incluso si es la única. Hasta que la aplicación se cierre por completo, el nuevo trabajo de servicio permanece en el estado *en espera de activación*. **Esto no es específico de Blazor, sino que es un comportamiento estándar de la plataforma web.**

Habitualmente, esto trae problemas a los desarrolladores que intentan probar las actualizaciones de su trabajo de servicio o de los recursos almacenados en la caché sin conexión. Si inserta en el repositorio las herramientas para desarrolladores del explorador, puede ver algo parecido a lo siguiente:

![La pestaña "Aplicación" de Google Chrome en la que se muestra que el trabajo de servicio de la aplicación está en "espera de activarse".](progressive-web-app/_static/image7.png)

Mientras la lista de "clientes" (las pestañas o ventanas en las que se muestra la aplicación) no esté vacía, el trabajo sigue en espera. Los trabajos de servicio hacen esto para garantizar la coherencia. La coherencia significa que todos los recursos se capturan de la misma caché atómica.

Al probar los cambios, puede que le resulte conveniente hacer clic en el vínculo "skipWaiting", como se muestra en la captura de pantalla anterior, y después volver a cargar la página. Puede automatizar este proceso para todos los usuarios si codifica el trabajo de servicio para [omitir la fase de "espera" y activarlo inmediatamente al actualizar](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase). Si lo hace, ya no tendrá la garantía de que los recursos siempre se capturen de forma coherente desde la misma instancia de la caché.

### <a name="users-may-run-any-historical-version-of-the-app"></a>Posibilidad para los usuarios de ejecutar cualquier versión histórica de la aplicación

Normalmente, los desarrolladores web esperan que los usuarios solo ejecuten la última versión implementada de su aplicación web, ya que es lo habitual en el modelo de distribución web tradicional. Pero una PWA sin conexión es más similar a una aplicación móvil nativa, cuya versión ejecutada por los usuarios no siempre es la más reciente.

Como se ha explicado en la sección [Actualizaciones en segundo plano](#background-updates), después de implementar una actualización en la aplicación, **cada usuario existente sigue utilizando una versión anterior durante, al menos, una visita adicional**, porque la actualización se produce en segundo plano y no se activa hasta que el usuario se desplaza fuera de la aplicación. Además, la versión anterior que se usa no es necesariamente la anterior que se ha implementado. La versión anterior puede ser *cualquier* versión histórica, en función de la última vez que el usuario haya completado una actualización.

Esto puede ser un problema si los elementos de front-end y back-end de la aplicación requieren un acuerdo sobre el esquema para las solicitudes de API. No debe implementar los cambios de esquema de API incompatibles con versiones anteriores hasta que se asegure de que todos los usuarios hayan realizado la actualización. Como alternativa, impida que los usuarios utilicen versiones anteriores incompatibles de la aplicación. Este requisito de escenario es el mismo que para las aplicaciones móviles nativas. Si implementa un cambio importante en las API de servidor, la aplicación cliente se interrumpe para los usuarios que todavía no hayan realizado la actualización.

Si es posible, no implemente cambios importantes en las API de back-end. Si tiene que hacerlo, considere la posibilidad de usar [API de trabajo de servicio estándar como ServiceWorkerRegistration](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration) para determinar si la aplicación está actualizada y, en caso contrario, impedir que se use.

### <a name="interference-with-server-rendered-pages"></a>Interferencias con páginas representadas por el servidor

Como se ha descrito en la sección [Compatibilidad de las páginas representadas por el servidor](#support-server-rendered-pages), si quiere omitir el comportamiento del trabajo de servicio de devolver contenido de `/index.html` para todas las solicitudes de navegación, modifique la lógica en el trabajo de servicio.

### <a name="all-service-worker-asset-manifest-contents-are-cached-by-default"></a>Almacenamiento en caché de todos los contenidos del manifiesto de recursos del trabajo de servicio de forma predeterminada

Como se ha descrito en la sección [Control del almacenamiento en caché de recursos](#control-asset-caching), el archivo `service-worker-assets.js` se genera durante la compilación y enumera todos los recursos que el trabajo de servicio debe capturar y almacenar en caché.

Como en esta lista se incluye de forma predeterminada todo lo que se emite para `wwwroot` (incluido el contenido proporcionado por paquetes y proyectos externos), debe tener cuidado de no incluir demasiado contenido en él. Si el directorio `wwwroot` contiene millones de imágenes, el trabajo de servicio intenta recuperarlas y almacenarlas en caché, lo que consume un ancho de banda excesivo y probablemente no se complete de forma correcta.

Implemente lógica arbitraria para controlar qué subconjunto del contenido del manifiesto se debe capturar y almacenar en caché mediante la edición de la función `onInstall` de `service-worker.published.js`.

### <a name="interaction-with-authentication"></a>Interacción con la autenticación

La plantilla de PWA se puede usar junto con la autenticación. Una PWA que puede ejecutarse sin conexión también puede admitir la autenticación cuando el usuario tiene conectividad de red.

Cuando un usuario no tiene conectividad de red, no puede autenticarse ni obtener tokens de acceso. De forma predeterminada, si se intenta visitar la página de inicio de sesión sin acceso a la red, se genera un mensaje de "error de red". Debe diseñar un flujo de interfaz de usuario que permita al usuario realizar tareas útiles mientras está sin conexión sin tener que autenticarse u obtener tokens de acceso. Como alternativa, puede diseñar la aplicación para que genere un error leve cuando la red no esté disponible. Si la aplicación no se puede diseñar para dar cabida a estos escenarios, probablemente no convenga habilitar la compatibilidad para ejecutarse sin conexión.

Cuando una aplicación que está diseñada para usarse tanto con conexión como sin ella vuelve a conectarse:

* Puede que la aplicación necesite aprovisionar un nuevo token de acceso.
* La aplicación debe detectar si un usuario diferente ha iniciado sesión en el servicio para poder realizar las operaciones en la cuenta del usuario que se efectuaron mientras estaba sin conexión.

Para crear una aplicación PWA sin conexión que interactúe con la autenticación:

* Reemplace <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> por una fábrica que almacene el último usuario que inició sesión y use el usuario almacenado cuando la aplicación esté sin conexión.
* Ponga en cola las operaciones mientras la aplicación está sin conexión y realícelas cuando la aplicación vuelva a estar en línea.
* Al cerrar sesión, borre el usuario almacenado.

La aplicación de ejemplo [`CarChecker`](https://github.com/SteveSandersonMS/CarChecker) demuestra los métodos anteriores. Eche un vistazo a las siguientes partes de la aplicación:

* `OfflineAccountClaimsPrincipalFactory` (`Client/Data/OfflineAccountClaimsPrincipalFactory.cs`)
* `LocalVehiclesStore` (`Client/Data/LocalVehiclesStore.cs`)
* Componente `LoginStatus` (`Client/Shared/LoginStatus.razor`)

## <a name="additional-resources"></a>Recursos adicionales

* [Script de PowerShell para solucionar problemas de integridad](xref:blazor/host-and-deploy/webassembly#troubleshoot-integrity-powershell-script)
* [Negociación entre orígenes de SignalR para la autenticación](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
