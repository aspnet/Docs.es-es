---
title: Uso de servicios de JavaScript para crear aplicaciones de página única en ASP.NET Core
author: scottaddie
description: Obtenga información sobre las ventajas de usar los servicios de JavaScript para crear una aplicación de página única (SPA) respaldada por ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017, devx-track-js
ms.date: 09/06/2019
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
uid: client-side/spa-services
ms.openlocfilehash: 1b9f5b4b4e066cdd3dd5fbfa666c7a087949979f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054650"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a>Uso de servicios de JavaScript para crear aplicaciones de página única en ASP.NET Core

Por [Scott Addie](https://github.com/scottaddie) y [Fiyaz Hasan](https://fiyazhasan.me/)

Una aplicación de página única (SPA) es un tipo popular de aplicación web debido a su inherente experiencia de usuario mejorada. La integración de bibliotecas o marcos de SPA del lado cliente, como [Angular](https://angular.io/) o [React](https://facebook.github.io/react/), con marcos del lado servidor como ASP.NET Core puede ser difícil. Los servicios de JavaScript se desarrollaron para reducir la fricción en el proceso de integración. Permite que haya un funcionamiento sin problemas entre las distintas pilas de tecnología de cliente y servidor.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Las características descritas en este artículo están obsoletas a partir de ASP.NET Core 3.0. Un mecanismo de integración de marcos de SPA más sencillo está disponible en el paquete NuGet [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions). Para obtener más información, vea [[Anuncio] Microsoft.AspNetCore.SpaServices y Microsoft.AspNetCore.NodeServices quedan obsoletos](https://github.com/dotnet/AspNetCore/issues/12890).

::: moniker-end

## <a name="what-is-javascript-services"></a>Qué son los servicios de JavaScript

Los servicios de JavaScript son una colección de tecnologías del lado cliente para ASP.NET Core. Su objetivo es colocar ASP.NET Core como la plataforma del lado servidor preferida de los desarrolladores para compilar SPA.

Los servicios de JavaScript constan de dos paquetes NuGet distintos:

* [Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)
* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)

Estos paquetes son útiles en los escenarios siguientes:

* Ejecutar JavaScript en el servidor
* Usar una biblioteca o un marco de SPA
* Compilar recursos del lado cliente con Webpack

Este artículo se centra sobre todo en usar el paquete SpaServices.

## <a name="what-is-spaservices"></a>Qué es SpaServices

SpaServices se ha creado para posicionar ASP.NET Core como la plataforma del lado servidor preferida de los desarrolladores para compilar SPA. SpaServices no es necesario para desarrollar SPA con ASP.NET Core y no obliga a los desarrolladores a usar un marco de cliente determinado.

SpaServices proporciona una infraestructura útil como la siguiente:

* [Representación previa del lado servidor](#server-side-prerendering)
* [Middleware de desarrollo de Webpack](#webpack-dev-middleware)
* [Sustitución del módulo de acceso frecuente](#hot-module-replacement)
* [Aplicaciones auxiliares de enrutamiento](#routing-helpers)

En conjunto, estos componentes de infraestructura mejoran el flujo de trabajo de desarrollo y la experiencia en tiempo de ejecución. Los componentes se pueden adoptar individualmente.

## <a name="prerequisites-for-using-spaservices"></a>Requisitos previos para usar SpaServices

Para trabajar con SpaServices, instale lo siguiente:

* [Node.js](https://nodejs.org/) (versión 6 o posterior) con npm

  * Para comprobar que estos componentes están instalados y se pueden encontrar, ejecute lo siguiente desde la línea de comandos:

    ```console
    node -v && npm -v
    ```

  * Si se implementa en un sitio web de Azure, no se necesita ninguna acción, ya que Node.js está instalado y disponible en los entornos de servidor.

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * En Windows, al usar Visual Studio 2017, el SDK se instala al seleccionar la carga de trabajo **Desarrollo multiplataforma de .NET Core**.

* Paquete NuGet [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/)

## <a name="server-side-prerendering"></a>Representación previa del lado servidor

Una aplicación universal (también conocida como isomorfa) es una aplicación JavaScript capaz de ejecutarse tanto en el servidor como en el cliente. Angular, React y otros marcos populares proporcionan una plataforma universal para este estilo de desarrollo de aplicaciones. La idea es representar primero los componentes del marco en el servidor mediante Node.js y luego delegar la ejecución en el cliente.

Los [Asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) de ASP.NET Core proporcionados por SpaServices simplifican la implementación de la representación previa del lado servidor al invocar las funciones de JavaScript en el servidor.

### <a name="server-side-prerendering-prerequisites"></a>Requisitos previos de la representación previa del lado servidor

Instale el paquete de npm [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering):

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a>Configuración de la representación previa del lado servidor

Los asistentes de etiquetas se pueden descubrir mediante el registro del espacio de nombres en el archivo *_ViewImports.cshtml* del proyecto:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

Estos asistentes de etiquetas abstraen las complejidades de la comunicación directa con las API de bajo nivel al aprovechar una sintaxis similar a HTML dentro de la vista de Razor:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a>Asistente de etiquetas asp-prerender-module

El asistente de etiquetas `asp-prerender-module`, que se usa en el ejemplo de código anterior, ejecuta *ClientApp/dist/main-server.js* en el servidor mediante Node.js. Por motivos de claridad, el archivo *main-server.js* es un artefacto de la tarea de transpilación de TypeScript a JavaScript en el proceso de compilación de [Webpack](https://webpack.github.io/). Webpack define un alias de punto de entrada de `main-server`; y el recorrido del gráfico de dependencias de este alias comienza en el archivo *ClientApp/boot-server.ts* :

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

En el siguiente ejemplo de Angular, el archivo *ClientApp/boot-server.ts* usa la función `createServerRenderer` y el tipo `RenderResult` del paquete `aspnet-prerendering` de npm para configurar la representación del servidor mediante Node.js. El marcado HTML destinado a la representación del lado servidor se pasa a una llamada a la función resolve, que se encapsula en un objeto `Promise` de JavaScript fuertemente tipado. La importancia del objeto `Promise` es que proporciona de forma asincrónica el marcado HTML a la página para que se inserte en el elemento de marcador de posición del DOM.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a>Asistente de etiquetas asp-prerender-data

Cuando se combina con el asistente de etiquetas `asp-prerender-module`, el asistente de etiquetas `asp-prerender-data` se puede usar para pasar información contextual de la vista de Razor al código JavaScript del lado servidor. Por ejemplo, el marcado siguiente pasa los datos de usuario al módulo `main-server`:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

El argumento `UserName` recibido se serializa mediante el serializador JSON integrado y se almacena en el objeto `params.data`. En el siguiente ejemplo de Angular, los datos se usan para construir un saludo personalizado en un elemento `h1`:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

Los nombres de propiedad pasados en los asistentes de etiquetas se representan con la notación **PascalCase**. Compare esto con JavaScript, donde los mismos nombres de propiedad se representan con la notación **camelCase**. La configuración de serialización de JSON predeterminada es responsable de esta diferencia.

Para ampliar el ejemplo de código anterior, los datos se pueden pasar del servidor a la vista mediante la hidratación de la propiedad `globals` proporcionada a la función `resolve`:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

La matriz `postList` definida en el objeto `globals` se adjunta al objeto `window` global del explorador. Esta variable que se eleva al ámbito global elimina la duplicación del esfuerzo, sobre todo en lo que respecta a la carga de los mismos datos una vez en el servidor y de nuevo en el cliente.

![Variable global postList adjuntada al objeto de ventana](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a>Middleware de desarrollo de Webpack

El [middleware de desarrollo de Webpack](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) incorpora un flujo de trabajo de desarrollo simplificado, mediante el que Webpack compila los recursos a petición. El middleware compila y atiende automáticamente los recursos del lado cliente cuando se recarga una página en el explorador. El enfoque alternativo consiste en invocar manualmente a Webpack mediante el script de compilación de npm del proyecto cuando cambia una dependencia de terceros o el código personalizado. En el ejemplo siguiente se muestra un script de compilación de npm en el archivo *package.json* :

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a>Requisitos previos del middleware de desarrollo de Webpack

Instale el paquete de npm [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack):

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a>Configuración del middleware de desarrollo de Webpack

El middleware de desarrollo de Webpack se registra en la canalización de solicitudes HTTP mediante el siguiente código en el método `Configure` del archivo *Startup.cs* :

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

Se debe llamar al método de extensión `UseWebpackDevMiddleware` antes de [registrar el hospedaje de archivos estáticos](xref:fundamentals/static-files) mediante el método de extensión `UseStaticFiles`. Por motivos de seguridad, registre el middleware solo cuando la aplicación se ejecute en modo de desarrollo.

La propiedad `output.publicPath` del archivo *webpack.config.js* indica al middleware que observe si se producen cambios en la carpeta `dist`:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a>Sustitución del módulo de acceso frecuente

Piense en la característica de [sustitución del módulo de acceso frecuente](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) de Webpack como una evolución del [middleware de desarrollo de Webpack](#webpack-dev-middleware). HMR presenta las mismas ventajas, pero optimiza aún más el flujo de trabajo de desarrollo al actualizar automáticamente el contenido de la página después de compilar los cambios. No lo confunda con una actualización del explorador, lo que interferiría con el estado en memoria actual y la sesión de depuración de la SPA. Hay un vínculo activo entre el servicio de middleware de desarrollo de Webpack y el explorador, lo que significa que los cambios se insertan en el explorador.

### <a name="hot-module-replacement-prerequisites"></a>Requisitos previos de la sustitución del módulo de acceso frecuente

Instale el paquete de npm [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware):

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a>Configuración de la sustitución del módulo de acceso frecuente

El componente HMR debe estar registrado en la canalización de solicitudes HTTP de MVC en el método `Configure`:

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

Tal y como pasaba con el [middleware de desarrollo de Webpack](#webpack-dev-middleware), se debe llamar al método de extensión `UseWebpackDevMiddleware` antes del método de extensión `UseStaticFiles`. Por motivos de seguridad, registre el middleware solo cuando la aplicación se ejecute en modo de desarrollo.

El archivo *webpack.config.js* debe definir una matriz `plugins`, aunque se deje vacío:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

Después de cargar la aplicación en el explorador, la pestaña de la consola de las herramientas de desarrollo proporciona la confirmación de la activación de HMR:

![Mensaje conectado de la sustitución del módulo de acceso frecuente](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a>Aplicaciones auxiliares de enrutamiento

En la mayoría de las SPA basadas en ASP.NET Core, a menudo se prefiere el enrutamiento del lado cliente además del enrutamiento del lado servidor. Los sistemas de enrutamiento de SPA y MVC pueden funcionar de forma independiente sin interferencias. Aun así, hay un caso perimetral que plantea desafíos: identificar las respuestas HTTP 404.

Piense en el escenario en el que se usa una ruta sin extensión de `/some/page`. Supongamos que la solicitud no coincide con el patrón de una ruta del lado servidor, pero su patrón coincide con una ruta del lado cliente. Ahora piense en una solicitud entrante de `/images/user-512.png`, que suele esperar encontrar un archivo de imagen en el servidor. Si esa ruta de acceso a recursos solicitada no coincide con ninguna ruta o archivo estático del lado servidor, es poco probable que la aplicación del lado cliente la administre (como regla general, se prefiere devolver un código de estado HTTP 404).

### <a name="routing-helpers-prerequisites"></a>Requisitos previos de las aplicaciones auxiliares de enrutamiento

Instale el paquete de npm de enrutamiento del lado cliente. Se usa Angular como ejemplo:

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a>Configuración de las aplicaciones auxiliares de enrutamiento

En el método `Configure` se usa un método de extensión denominado `MapSpaFallbackRoute`:

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

Las rutas se evalúan en el orden en que están configuradas. Por consiguiente, la ruta `default` del ejemplo de código anterior se usa primero en la coincidencia de patrones.

## <a name="create-a-new-project"></a>Crear un proyecto nuevo

Los servicios de JavaScript proporcionan plantillas de aplicación preconfiguradas. SpaServices se usa en estas plantillas con diferentes marcos y bibliotecas, como Angular, React y Redux.

Estas plantillas se pueden instalar mediante la CLI de .NET Core al ejecutar el siguiente comando:

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

Se muestra una lista de las plantillas de SPA disponibles:

| Plantillas                                 | Nombre corto | Lenguaje | Etiquetas        |
| ------------------------------------------| :--------: | :------: | :---------: |
| MVC ASP.NET Core con Angular             | angular    | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core con React.js            | react      | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core con React.js y Redux  | reactredux | [C#]     | Web/MVC/SPA |

Para crear un proyecto con una de las plantillas de SPA, incluya el **nombre corto** de la plantilla en el comando [dotnet new](/dotnet/core/tools/dotnet-new). El siguiente comando crea una aplicación Angular con el MVC de ASP.NET Core configurado en el lado servidor:

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a>Establecimiento del modo de configuración de ejecución

Hay dos modos de configuración de ejecución principales:

* **Desarrollo** :
  * Incluye mapas de origen para facilitar la depuración.
  * No optimiza el código del lado cliente para el rendimiento.
* **Producción** :
  * Excluye los mapas de origen.
  * Optimiza el código del lado cliente mediante la unión y la minificación.

ASP.NET Core usa una variable de entorno denominada `ASPNETCORE_ENVIRONMENT` para almacenar el modo de configuración. Para obtener más información, consulte [Establecimiento del entorno](xref:fundamentals/environments#set-the-environment).

### <a name="run-with-net-core-cli"></a>Ejecución con la CLI de .NET Core

Restaure los paquetes NuGet y de npm necesarios mediante la ejecución del siguiente comando en la raíz del proyecto:

```dotnetcli
dotnet restore && npm i
```

Compile y ejecute la aplicación:

```dotnetcli
dotnet run
```

La aplicación se inicia en localhost según el [modo de configuración de ejecución](#set-the-runtime-configuration-mode). Al ir a `http://localhost:5000` en el explorador, se muestra la página de aterrizaje.

### <a name="run-with-visual-studio-2017"></a>Ejecución con Visual Studio 2017

Abra el archivo *.csproj* generado por el comando [dotnet new](/dotnet/core/tools/dotnet-new). Los paquetes NuGet y de npm necesarios se restauran automáticamente al abrir el proyecto. Este proceso de restauración puede tardar unos minutos y, cuando se complete, la aplicación estará lista para ejecutarse. Haga clic en el botón verde de ejecución o presione `Ctrl + F5` y el explorador se abrirá en la página de aterrizaje de la aplicación. La aplicación se ejecuta en localhost según el [modo de configuración de ejecución](#set-the-runtime-configuration-mode).

## <a name="test-the-app"></a>Prueba de la aplicación

Las plantillas de SpaServices están preconfiguradas para ejecutar pruebas del lado cliente mediante [Karma](https://karma-runner.github.io/1.0/index.html) y [Jasmine](https://jasmine.github.io/). Jasmine es un conocido marco de pruebas unitarias para JavaScript, mientras que Karma es un ejecutor de pruebas para llevar a cabo estas pruebas. Karma está configurado para funcionar con el [middleware de desarrollo de Webpack](#webpack-dev-middleware) de modo que no es necesario que el desarrollador detenga y ejecute la prueba cada vez que se hagan cambios. La prueba se ejecuta de forma automática, ya sea el código que se ejecuta en el caso de prueba o el caso de prueba en sí.

Si se usa la aplicación Angular como ejemplo, ya se proporcionan dos casos de prueba de Jasmine de `CounterComponent` en el archivo *counter.component.spec.ts* :

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

Abra el símbolo del sistema en el directorio *ClientApp*. Ejecute el siguiente comando:

```console
npm test
```

El script inicia el ejecutor de pruebas de Karma, que lee la configuración definida en el archivo *karma.conf.js*. Entre otras opciones, el archivo *karma.conf.js* identifica los archivos de prueba que se van a ejecutar mediante su matriz `files`:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a>Publicar la aplicación

Consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/12474) para obtener más información sobre cómo publicar en Azure.

Combinar los recursos del lado cliente generados y los artefactos de ASP.NET Core publicados en un paquete listo para implementar puede resultar complicado. Afortunadamente, SpaServices orquesta todo el proceso de publicación con un destino de MSBuild personalizado denominado `RunWebpack`:

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

El destino de MSBuild tiene las siguientes responsabilidades:

1. Restaurar los paquetes de npm
1. Crear una compilación de nivel de producción de los recursos del lado cliente de terceros
1. Crear una compilación de nivel de producción de los recursos del lado cliente personalizados
1. Copiar los recursos generados por Webpack en la carpeta de publicación

El destino de MSBuild se invoca cuando se ejecuta lo siguiente:

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a>Recursos adicionales

* [Documentación de Angular](https://angular.io/docs)
