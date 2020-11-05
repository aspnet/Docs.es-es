---
title: Uso de ASP.NET Core SignalR con TypeScript and Webpack
author: ssougnez
description: En este tutorial, configurará Webpack para agrupar y compilar una aplicación web ASP.NET Core SignalR cuyo cliente está escrito en TypeScript.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
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
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: 949276bf4aae33c3af3fd1b8219a83868095f378
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056847"
---
# <a name="use-aspnet-core-no-locsignalr-with-typescript-and-webpack"></a>Uso de ASP.NET Core SignalR con TypeScript and Webpack

Por [Sébastien Sougnez](https://twitter.com/ssougnez) y [Scott Addie](https://twitter.com/Scott_Addie)

[Webpack](https://webpack.js.org/) permite a los desarrolladores agrupar y compilar los recursos del lado cliente de una aplicación web. En este tutorial se describe el uso de Webpack en una aplicación web ASP.NET Core SignalR cuyo cliente está escrito en [TypeScript](https://www.typescriptlang.org/).

En este tutorial aprenderá a:

> [!div class="checklist"]
> * Agregar scaffold a una aplicación ASP.NET Core SignalR de inicio
> * Configurar el cliente de TypeScript para SignalR
> * Configuración de una canalización de compilación mediante Webpack
> * Configurar el servidor SignalR
> * Habilitar la comunicación entre cliente y servidor

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Requisitos previos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **ASP.NET y desarrollo web**
* [.NET Core SDK 3.0 o posterior](https://dotnet.microsoft.com/download/dotnet-core)
* [Node.js](https://nodejs.org/) con [npm](https://www.npmjs.com/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [.NET Core SDK 3.0 o posterior](https://dotnet.microsoft.com/download/dotnet-core)
* [C# para Visual Studio Code versión 1.17.1 o posterior](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Node.js](https://nodejs.org/) con [npm](https://www.npmjs.com/)

---

## <a name="create-the-aspnet-core-web-app"></a>Creación de la aplicación web ASP.NET Core

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Configure Visual Studio para buscar npm en la variable de entorno *PATH*. De forma predeterminada, Visual Studio usa la versión de npm que se encuentra en su directorio de instalación. Siga estas instrucciones en Visual Studio:

1. Inicie Visual Studio. En la ventana de inicio, seleccione **Continuar sin código**.
1. Vaya a **Herramientas** > **Opciones** > **Proyectos y soluciones** > **Administración de paquetes web** > **Herramientas web externas**.
1. Seleccione la entrada *$(PATH)* en la lista. Haga clic en la flecha arriba para mover la entrada a la segunda posición de la lista y seleccione **Aceptar**.

    ![Configuración de Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

Se ha completado la configuración de Visual Studio.

1. Use la opción de menú **Archivo** > **Nuevo** > **Proyecto** y seleccione la plantilla **Aplicación web ASP.NET Core**. Seleccione **Siguiente**.
1. Asigne el nombre *SignalRWebPack* al proyecto y seleccione **Crear**.
1. Seleccione *.NET Core* en la lista desplegable de plataformas de destino y *ASP.NET Core 3.1* en la lista desplegable del selector de plataformas. Seleccione la plantilla **Vacía** y **Crear**.

Agregue el paquete `Microsoft.TypeScript.MSBuild` al proyecto:

1. En el **Explorador de soluciones** (panel derecho), haga clic con el botón derecho en el nodo del proyecto y seleccione **Administrar paquetes NuGet**. En la pestaña **Examinar** , busque `Microsoft.TypeScript.MSBuild` y luego haga clic en **Instalar** a la derecha para instalar el paquete.

Visual Studio agrega el paquete NuGet en el nodo **Dependencias** del **Explorador de soluciones** , que habilita la compilación de TypeScript en el proyecto.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ejecute el comando siguiente en el **terminal integrado** :

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* El comando `dotnet new` crea una aplicación web ASP.NET Core vacía en el directorio *SignalRWebPack*.
* El comando `code` abre la carpeta *SignalRWebPack* en la instancia actual de Visual Studio Code.

Ejecute el siguiente comando de CLI de .NET Core en el **terminal integrado** :

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

El comando anterior agrega el paquete [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/), que habilita la compilación de TypeScript en el proyecto.

---

## <a name="configure-webpack-and-typescript"></a>Configuración de Webpack y TypeScript

Los pasos siguientes permiten configurar la conversión de TypeScript a JavaScript y la agrupación de los recursos del lado cliente.

1. Ejecute el comando siguiente en la raíz del proyecto para crear un archivo *package.json* :

    ```console
    npm init -y
    ```

1. Agregue la propiedad resaltada al archivo *package.json* y guarde los cambios efectuados en el archivo:

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    Si establece la propiedad `private` en `true`, evitará las advertencias de la instalación de paquetes en el paso siguiente.

1. Instale los paquetes npm necesarios. Ejecute el comando siguiente desde la raíz del proyecto:

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    Algunos detalles del comando para tener en cuenta:

    * En cada nombre de paquete, un número de versión sigue al signo `@`. npm instala esas versiones de paquete específicas.
    * La opción `-E` deshabilita el comportamiento predeterminado de npm de escribir operadores de intervalo de [versionamiento semántico](https://semver.org/) en *package.json*. Por ejemplo, se usa `"webpack": "4.41.5"` en lugar de `"webpack": "^4.41.5"`. Esta opción impide actualizaciones no deseadas a versiones más recientes del paquete.

    Consulte la documentación de [npm-install](https://docs.npmjs.com/cli/install) para obtener más detalles.

1. Reemplace la propiedad `scripts` del archivo *package.json* por el código siguiente:

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Más detalles sobre los scripts:

    * `build`: agrupa los recursos del lado cliente en modo de desarrollo y supervisa los cambios del archivo. El monitor de archivos hace que la agrupación se vuelva a generar cada vez que cambia un archivo del proyecto. La opción `mode` deshabilita las optimizaciones de producción, como la agitación del árbol y la minificación. Use `build` únicamente durante el desarrollo.
    * `release`: agrupa los recursos del lado cliente en modo de producción.
    * `publish`: ejecuta el script `release` para agrupar los recursos del lado cliente en modo de producción. Llama al comando [publish](/dotnet/core/tools/dotnet-publish) de la CLI de .NET Core para publicar la aplicación.

1. Cree un archivo denominado *webpack.config.js* , en la raíz del proyecto, con el código siguiente:

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    El archivo anterior configura la compilación de Webpack. Algunos detalles de configuración para tener en cuenta:

    * La propiedad `output` invalida el valor predeterminado de *dist*. En su lugar, la agrupación se genera en el directorio *wwwroot*.
    * La matriz `resolve.extensions` incluye *.js* para importar el código JavaScript del cliente de SignalR.

1. Cree un *src* directorio en la raíz del proyecto para almacenar los recursos del lado cliente del proyecto.

1. Cree *src/index.html* con el marcado siguiente.

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    El código HTML anterior define el marcado reutilizable de la página principal.

1. Cree un directorio *src/css*. Su objetivo es almacenar los archivos *.css* del proyecto.

1. Cree *src/css/main.css* con la especificación CSS siguiente:

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    El archivo *main.css* anterior aplica estilo a la aplicación.

1. Cree *src/tsconfig.json* con el JSON siguiente:

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    El código anterior configura el compilador de TypeScript para generar JavaScript compatible con [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.

1. Cree *src/index.ts* con el código siguiente:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    El elemento TypeScript anterior recupera las referencias a elementos DOM y adjunta dos controladores de eventos:

    * `keyup`: este evento se desencadena cuando el usuario escribe algo en el cuadro de texto `tbMessage`. La función `send` se llama cuando el usuario presiona la tecla **Entrar**.
    * `click`: este evento se desencadena cuando el usuario hace clic en el botón **Enviar**. Se llama a la función `send`.

## <a name="configure-the-app"></a>Configuración de la aplicación

1. En `Startup.Configure`, agregue llamadas a [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) y [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   El código anterior permite que el servidor busque y entregue el archivo *index.html*.  El archivo se entrega si el usuario escribe su dirección URL completa o la dirección URL raíz de la aplicación web.

1. Al final de `Startup.Configure`, asigne una ruta */hub* al centro `ChatHub`. Reemplace el código que muestra *Hola mundo* por la línea siguiente: 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. En `Startup.ConfigureServices`, llame a [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. Cree un directorio denominado *Hubs* en la raíz del proyecto *SignalRWebPack/* para almacenar el centro SignalR.

1. Cree el concentrador *Hubs/ChatHub.cs* con el código siguiente:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. Agregue la instrucción `using` siguiente en la parte superior del archivo *Startup.cs* para resolver la referencia a `ChatHub`:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>Habilitar la comunicación entre cliente y servidor

La aplicación actualmente muestra un formulario básico para enviar mensajes, pero aún no es funcional. El servidor está escuchando en una ruta específica, pero no hace nada con los mensajes enviados.

1. Ejecute el comando siguiente en la raíz del proyecto:

    ```console
    npm i @microsoft/signalr @types/node
    ```

    El comando anterior instala lo siguiente:

     * El [cliente TypeScript de SignalR](https://www.npmjs.com/package/@microsoft/signalr), que permite al cliente enviar mensajes al servidor.
     * Las definiciones de tipo de TypeScript para Node.js, que habilita la comprobación en tiempo de compilación de los tipos de Node.js.

1. Agregue el código resaltado al archivo *src/index.ts* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    El código anterior admite la recepción de mensajes desde el servidor. La clase `HubConnectionBuilder` crea un generador para configurar la conexión al servidor. La función `withUrl` configura la dirección URL del concentrador.

    SignalR permite el intercambio de mensajes entre un cliente y un servidor. Cada mensaje tiene un nombre específico. Por ejemplo, puede haber mensajes con el nombre `messageReceived` que pueden ejecutar la lógica responsable de mostrar el nuevo mensaje en la zona de mensajes. La escucha a un mensaje concreto se puede realizar mediante la función `on`. Se puede escuchar cualquier número de nombres de mensaje. También se pueden pasar parámetros al mensaje, como el nombre del autor y el contenido del mensaje recibido. Una vez que el cliente recibe un mensaje, se crea un elemento `div` con el nombre del autor y el contenido del mensaje en su atributo `innerHTML`. Se agrega al elemento `div` principal que muestra los mensajes.

1. Ahora que el cliente puede recibir mensajes, debe configurarlo para poder enviarlos. Agregue el código resaltado al archivo *src/index.ts* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    El envío de mensajes a través de la conexión de WebSockets requiere llamar al método `send`. El primer parámetro del método es el nombre del mensaje. Los datos del mensaje se encuentran en los otros parámetros. En este ejemplo, se envía al servidor un mensaje identificado como `newMessage`. El mensaje está formado por el nombre de usuario y la entrada del usuario desde un cuadro de texto. Si el envío funciona, se borra el valor del cuadro de texto.

1. Agregue el método `NewMessage` a la clase `ChatHub`:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    El código anterior difunde los mensajes recibidos a todos los usuarios conectados, una vez que el servidor los recibe. No es necesario tener un método `on` genérico para recibir todos los mensajes. Basta con un método que tenga el nombre del mensaje.

    En este ejemplo, el cliente de TypeScript envía un mensaje que se identifica como `newMessage`. El método `NewMessage` de C# espera los datos enviados por el cliente. Se realiza una llamada a [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) de [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all). Los mensajes recibidos se envían a todos los clientes conectados al concentrador.

## <a name="test-the-app"></a>Prueba de la aplicación

Confirme que la aplicación funciona con los pasos siguientes.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Ejecute Webpack en modo *release*. Desde la ventana **Consola del Administrador de paquetes** , ejecute el comando siguiente en la raíz del proyecto. Si no está en la raíz del proyecto, escriba `cd SignalRWebPack` antes de introducir el comando.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Seleccione **Depurar** > **Iniciar sin depurar** para iniciar la aplicación en un explorador sin adjuntar el depurador. El archivo *wwwroot/index.html* se entrega en `http://localhost:<port_number>`.

   Si obtiene errores de compilación, intente cerrar y volver a abrir la solución. 

1. Abra otra instancia del explorador (sirve cualquiera). Pegue la dirección URL en la barra de direcciones.

1. Elija un explorador, escriba algo en el cuadro de texto **Mensaje** y haga clic en el botón **Enviar**. El nombre de usuario único y el mensaje se muestran en las dos páginas al instante.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Ejecute Webpack en modo *release* mediante la ejecución del comando siguiente en la raíz del proyecto:

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Compile y ejecute la aplicación mediante la ejecución del comando siguiente en la raíz del proyecto:

    ```dotnetcli
    dotnet run
    ```

    El servidor web inicia la aplicación y hace que esté disponible en el host local.

1. Abra un explorador en `http://localhost:<port_number>`. Se entrega el archivo *wwwroot/index.html*. Copie la dirección URL de la barra de direcciones.

1. Abra otra instancia del explorador (sirve cualquiera). Pegue la dirección URL en la barra de direcciones.

1. Elija un explorador, escriba algo en el cuadro de texto **Mensaje** y haga clic en el botón **Enviar**. El nombre de usuario único y el mensaje se muestran en las dos páginas al instante.

---

![mensaje mostrado en las dos ventanas del explorador](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a>Requisitos previos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **ASP.NET y desarrollo web**
* [.NET Core SDK 2.2 o posterior](https://dotnet.microsoft.com/download/dotnet-core)
* [Node.js](https://nodejs.org/) con [npm](https://www.npmjs.com/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [.NET Core SDK 2.2 o posterior](https://dotnet.microsoft.com/download/dotnet-core)
* [C# para Visual Studio Code versión 1.17.1 o posterior](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Node.js](https://nodejs.org/) con [npm](https://www.npmjs.com/)

---

## <a name="create-the-aspnet-core-web-app"></a>Creación de la aplicación web ASP.NET Core

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Configure Visual Studio para buscar npm en la variable de entorno *PATH*. De forma predeterminada, Visual Studio usa la versión de npm que se encuentra en su directorio de instalación. Siga estas instrucciones en Visual Studio:

1. Vaya a **Herramientas** > **Opciones** > **Proyectos y soluciones** > **Administración de paquetes web** > **Herramientas web externas**.
1. Seleccione la entrada *$(PATH)* en la lista. Haga clic en la flecha arriba para mover la entrada a la segunda posición de la lista.

    ![Configuración de Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

Se ha completado la configuración de Visual Studio. Es el momento de crear el proyecto.

1. Use la opción de menú **Archivo** > **Nuevo** > **Proyecto** y seleccione la plantilla **Aplicación web ASP.NET Core**.
1. Asigne el nombre *SignalRWebPack* al proyecto y seleccione **Crear**.
1. Seleccione *.NET Core* en la lista desplegable de plataforma de destino y *ASP.NET Core 2.2* en la lista desplegable del selector de plataforma. Seleccione la plantilla **Vacía** y **Crear**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ejecute el comando siguiente en el **terminal integrado** :

```dotnetcli
dotnet new web -o SignalRWebPack
```

Se crea una aplicación web ASP.NET Core vacía, destinada a .NET Core, en el directorio *SignalRWebPack*.

---

## <a name="configure-webpack-and-typescript"></a>Configuración de Webpack y TypeScript

Los pasos siguientes permiten configurar la conversión de TypeScript a JavaScript y la agrupación de los recursos del lado cliente.

1. Ejecute el comando siguiente en la raíz del proyecto para crear un archivo *package.json* :

    ```console
    npm init -y
    ```

1. Agregue la propiedad resaltada al archivo *package.json* :

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    Si establece la propiedad `private` en `true`, evitará las advertencias de la instalación de paquetes en el paso siguiente.

1. Instale los paquetes npm necesarios. Ejecute el comando siguiente desde la raíz del proyecto:

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    Algunos detalles del comando para tener en cuenta:

    * En cada nombre de paquete, un número de versión sigue al signo `@`. npm instala esas versiones de paquete específicas.
    * La opción `-E` deshabilita el comportamiento predeterminado de npm de escribir operadores de intervalo de [versionamiento semántico](https://semver.org/) en *package.json*. Por ejemplo, se usa `"webpack": "4.29.3"` en lugar de `"webpack": "^4.29.3"`. Esta opción impide actualizaciones no deseadas a versiones más recientes del paquete.

    Consulte la documentación de [npm-install](https://docs.npmjs.com/cli/install) para obtener más detalles.

1. Reemplace la propiedad `scripts` del archivo *package.json* por el código siguiente:

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Más detalles sobre los scripts:

    * `build`: agrupa los recursos del lado cliente en modo de desarrollo y supervisa los cambios del archivo. El monitor de archivos hace que la agrupación se vuelva a generar cada vez que cambia un archivo del proyecto. La opción `mode` deshabilita las optimizaciones de producción, como la agitación del árbol y la minificación. Use `build` únicamente durante el desarrollo.
    * `release`: agrupa los recursos del lado cliente en modo de producción.
    * `publish`: ejecuta el script `release` para agrupar los recursos del lado cliente en modo de producción. Llama al comando [publish](/dotnet/core/tools/dotnet-publish) de la CLI de .NET Core para publicar la aplicación.

1. Cree un archivo denominado *webpack.config.js* , en la raíz del proyecto, con el código siguiente:

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    El archivo anterior configura la compilación de Webpack. Algunos detalles de configuración para tener en cuenta:

    * La propiedad `output` invalida el valor predeterminado de *dist*. En su lugar, la agrupación se genera en el directorio *wwwroot*.
    * La matriz `resolve.extensions` incluye *.js* para importar el código JavaScript del cliente de SignalR.

1. Cree un *src* directorio en la raíz del proyecto para almacenar los recursos del lado cliente del proyecto.

1. Cree *src/index.html* con el marcado siguiente.

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    El código HTML anterior define el marcado reutilizable de la página principal.

1. Cree un directorio *src/css*. Su objetivo es almacenar los archivos *.css* del proyecto.

1. Cree *src/css/main.css* con el marcado siguiente:

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    El archivo *main.css* anterior aplica estilo a la aplicación.

1. Cree *src/tsconfig.json* con el JSON siguiente:

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    El código anterior configura el compilador de TypeScript para generar JavaScript compatible con [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.

1. Cree *src/index.ts* con el código siguiente:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    El elemento TypeScript anterior recupera las referencias a elementos DOM y adjunta dos controladores de eventos:

    * `keyup`: este evento se desencadena cuando el usuario escribe en el cuadro de texto `tbMessage`. La función `send` se llama cuando el usuario presiona la tecla **Entrar**.
    * `click`: este evento se desencadena cuando el usuario hace clic en el botón **Enviar**. Se llama a la función `send`.

## <a name="configure-the-aspnet-core-app"></a>Configuración de la aplicación ASP.NET Core

1. El código proporcionado en el método `Startup.Configure` muestra *Hello World!* . Reemplace la llamada al método `app.Run` por las llamadas a [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) y [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    El código anterior permite que el servidor busque y proporcione el archivo *index.html* , con independencia de que el usuario escriba su dirección URL completa o la dirección URL raíz de la aplicación web.

1. Llame a [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) en `Startup.ConfigureServices`. Esta acción agrega los servicios SignalR al proyecto.

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. Asigne una ruta */hub* al concentrador `ChatHub`. Agregue la línea siguiente al final de `Startup.Configure`:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. Cree un directorio denominado *Hubs* en la raíz del proyecto. Su objetivo es almacenar el concentrador de SignalR, que se crea en el paso siguiente.

1. Cree el concentrador *Hubs/ChatHub.cs* con el código siguiente:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. Agregue el código siguiente en la parte superior del archivo *Startup.cs* para resolver la referencia a `ChatHub`:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>Habilitar la comunicación entre cliente y servidor

Actualmente, en la aplicación se muestra un formulario simple para enviar mensajes. Al intentar hacer algo no sucede nada. El servidor está escuchando en una ruta específica, pero no hace nada con los mensajes enviados.

1. Ejecute el comando siguiente en la raíz del proyecto:

    ```console
    npm install @aspnet/signalr
    ```

    El comando anterior instala el [cliente TypeScript de SignalR](https://www.npmjs.com/package/@microsoft/signalr), que permite al cliente enviar mensajes al servidor.

1. Agregue el código resaltado al archivo *src/index.ts* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    El código anterior admite la recepción de mensajes desde el servidor. La clase `HubConnectionBuilder` crea un generador para configurar la conexión al servidor. La función `withUrl` configura la dirección URL del concentrador.

    SignalR permite el intercambio de mensajes entre un cliente y un servidor. Cada mensaje tiene un nombre específico. Por ejemplo, puede haber mensajes con el nombre `messageReceived` que pueden ejecutar la lógica responsable de mostrar el nuevo mensaje en la zona de mensajes. La escucha a un mensaje concreto se puede realizar mediante la función `on`. Puede escuchar a cualquier número de nombres de mensaje. También se pueden pasar parámetros al mensaje, como el nombre del autor y el contenido del mensaje recibido. Una vez que el cliente recibe un mensaje, se crea un elemento `div` con el nombre del autor y el contenido del mensaje en su atributo `innerHTML`. El nuevo mensaje se agrega al elemento `div` principal que muestra los mensajes.

1. Ahora que el cliente puede recibir mensajes, debe configurarlo para poder enviarlos. Agregue el código resaltado al archivo *src/index.ts* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    El envío de mensajes a través de la conexión de WebSockets requiere llamar al método `send`. El primer parámetro del método es el nombre del mensaje. Los datos del mensaje se encuentran en los otros parámetros. En este ejemplo, se envía al servidor un mensaje identificado como `newMessage`. El mensaje está formado por el nombre de usuario y la entrada del usuario desde un cuadro de texto. Si el envío funciona, se borra el valor del cuadro de texto.

1. Agregue el método `NewMessage` a la clase `ChatHub`:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    El código anterior difunde los mensajes recibidos a todos los usuarios conectados, una vez que el servidor los recibe. No es necesario tener un método `on` genérico para recibir todos los mensajes. Basta con un método que tenga el nombre del mensaje.

    En este ejemplo, el cliente de TypeScript envía un mensaje que se identifica como `newMessage`. El método `NewMessage` de C# espera los datos enviados por el cliente. Se realiza una llamada a [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) de [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all). Los mensajes recibidos se envían a todos los clientes conectados al concentrador.

## <a name="test-the-app"></a>Prueba de la aplicación

Confirme que la aplicación funciona con los pasos siguientes.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Ejecute Webpack en modo *release*. Desde la ventana **Consola del Administrador de paquetes** , ejecute el comando siguiente en la raíz del proyecto. Si no está en la raíz del proyecto, escriba `cd SignalRWebPack` antes de introducir el comando.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Seleccione **Depurar** > **Iniciar sin depurar** para iniciar la aplicación en un explorador sin adjuntar el depurador. El archivo *wwwroot/index.html* se entrega en `http://localhost:<port_number>`.

1. Abra otra instancia del explorador (sirve cualquiera). Pegue la dirección URL en la barra de direcciones.

1. Elija un explorador, escriba algo en el cuadro de texto **Mensaje** y haga clic en el botón **Enviar**. El nombre de usuario único y el mensaje se muestran en las dos páginas al instante.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Ejecute Webpack en modo *release* mediante la ejecución del comando siguiente en la raíz del proyecto:

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Compile y ejecute la aplicación mediante la ejecución del comando siguiente en la raíz del proyecto:

    ```dotnetcli
    dotnet run
    ```

    El servidor web inicia la aplicación y hace que esté disponible en el host local.

1. Abra un explorador en `http://localhost:<port_number>`. Se entrega el archivo *wwwroot/index.html*. Copie la dirección URL de la barra de direcciones.

1. Abra otra instancia del explorador (sirve cualquiera). Pegue la dirección URL en la barra de direcciones.

1. Elija un explorador, escriba algo en el cuadro de texto **Mensaje** y haga clic en el botón **Enviar**. El nombre de usuario único y el mensaje se muestran en las dos páginas al instante.

---

![mensaje mostrado en las dos ventanas del explorador](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
