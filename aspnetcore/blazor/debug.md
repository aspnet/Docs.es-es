---
title: Depuración de Blazor WebAssembly en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo depurar aplicaciones Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/debug
ms.openlocfilehash: 990882c03ddc14c664aa8da0518fb36087199aca
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550522"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a>Depuración de Blazor WebAssembly en ASP.NET Core

[Daniel Roth](https://github.com/danroth27)

Las aplicaciones Blazor WebAssembly se pueden depurar con las herramientas de desarrollo del explorador en exploradores basados en Chromium (Edge/Chrome). También puede depurar la aplicación con los siguientes entornos de desarrollo integrado (IDE):

* Visual Studio
* Visual Studio para Mac
* Visual Studio Code

Entre los escenarios disponibles se incluyen los siguientes:

* Establecimiento y eliminación de puntos de interrupción.
* Ejecute la aplicación con compatibilidad de depuración en IDE.
* Examine el código en un solo paso.
* Reanude la ejecución de código con un método abreviado de teclado en IDE.
* En la ventana *Variables locales*, observe los valores de las variables locales.
* Vea la pila de llamadas, incluidas las cadenas de llamadas entre JavaScript y .NET.

Por ahora, *no puede*:

* Interrumpir las operaciones ante excepciones no controladas.
* Alcanzar puntos de interrupción durante el inicio de la aplicación antes de que se ejecute el proxy de depuración. Esto incluye los puntos de interrupción de `Program.Main` (`Program.cs`) y los de los [métodos `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) de los componentes que se cargan en la primera página solicitada desde la aplicación.
* Depure en escenarios no locales; por ejemplo, el [Subsistema de Windows para Linux (WSL)](/windows/wsl/) o [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline).

## <a name="prerequisites"></a>Requisitos previos

La depuración requiere cualquiera de los exploradores siguientes:

* Google Chrome (versión 70 o posterior) (predeterminado)
* Microsoft Edge (versión 80 o posterior)

Asegúrese de que los firewalls o servidores proxy no bloquean la comunicación con el proxy de depuración (proceso `NodeJS`). Para más información, vea la sección [Configuración de firewall](#firewall-configuration).

Visual Studio para Mac requiere la versión 8.8 (compilación 1532) o una posterior:

1. Instale la versión más reciente de Visual Studio para Mac seleccionando el botón **Descargar Visual Studio para Mac** en [Microsoft: Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).
1. Seleccione el canal *Vista preliminar* en Visual Studio. Para obtener más información, vea [Instalación de la versión más reciente de Visual Studio para Mac](/visualstudio/mac/install-preview).

> [!NOTE]
> Apple Safari en macOS no se admite actualmente.

## <a name="enable-debugging"></a>Habilitar la depuración

Para habilitar la depuración de una aplicación Blazor WebAssembly, actualice el archivo `launchSettings.json` del proyecto de inicio para incluir la propiedad `inspectUri` siguiente en cada perfil de inicio:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Una vez que lo actualice, el archivo `launchSettings.json` debe ser similar a este ejemplo:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

La propiedad `inspectUri`:

* Permite que el IDE detecte que la aplicación es una aplicación Blazor WebAssembly.
* Indica a la infraestructura de depuración de scripts que se conecte al explorador a través del proxy de depuración de Blazor.

El marco de trabajo proporciona los valores de marcador de posición para el protocolo WebSocket (`wsProtocol`), el host (`url.hostname`), el puerto (`url.port`) y el identificador URI del inspector en el explorador iniciado (`browserInspectUri`).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Para depurar una aplicación Blazor WebAssembly en Visual Studio:

1. Cree una aplicación hospedada Blazor WebAssembly de ASP.NET Core.
1. Presione <kbd>F5</kbd> para ejecutar la aplicación en el depurador.

   > [!NOTE]
   > No admite **Iniciar sin depuración** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>). Cuando la aplicación se ejecuta en la configuración de depuración, la sobrecarga de depuración siempre genera una pequeña reducción del rendimiento.

1. En la aplicación `*Client*`, establezca un punto de interrupción en la línea `currentCount++;` en `Pages/Counter.razor`.
1. En el explorador, vaya a la página `Counter` y seleccione el botón **Hacer clic aquí** para alcanzar el punto de interrupción.
1. En Visual Studio, revise el valor del campo `currentCount` en la ventana **Variables locales**.
1. Presione <kbd>F5</kbd> para continuar la ejecución.

Al depurar una aplicación de Blazor WebAssembly, también puede depurar el código del servidor:

1. Establezca un punto de interrupción en la página `Pages/FetchData.razor` de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.
1. Establezca un punto de interrupción en `WeatherForecastController` en el método de acción `Get`.
1. Vaya a la página `Fetch Data` para alcanzar el primer punto de interrupción en el componente `FetchData` justo antes de que emita una solicitud HTTP al servidor.
1. Presione <kbd>F5</kbd> para continuar la ejecución y, luego, alcance el punto de interrupción en el servidor en `WeatherForecastController`.
1. Vuelva a presionar <kbd>F5</kbd> para permitir que la ejecución continúe y ver la tabla de pronóstico meteorológico representada en el explorador.

> [!NOTE]
> Los puntos de interrupción **no** se alcanzan durante el inicio de la aplicación antes de que se ejecute el proxy de depuración. Esto incluye los puntos de interrupción de `Program.Main` (`Program.cs`) y los de los [métodos `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) de los componentes que se cargan en la primera página solicitada desde la aplicación.

Si la aplicación se hospeda en una [ruta de acceso base de la aplicación](xref:blazor/host-and-deploy/index#app-base-path) distinta de `/`, actualice las propiedades siguientes en `Properties/launchSettings.json` para reflejar la ruta de acceso base de la aplicación:

* `applicationUrl`:

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* `inspectUri` de cada perfil:

  ```json
  "profiles": {
    ...
    "{PROFILE 1, 2, ... N}": {
      ...
      "inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/{APP BASE PATH}/_framework/debug/ws-proxy?browser={browserInspectUri}",
      ...
    }
  }
  ```

Los marcadores de posición en la configuración anterior:

* `{INSECURE PORT}`: puerto no seguro. De forma predeterminada, se proporciona un valor aleatorio, pero también se permite un puerto personalizado.
* `{APP BASE PATH}`: ruta de acceso base de la aplicación.
* `{SECURE PORT}`: puerto seguro. De forma predeterminada, se proporciona un valor aleatorio, pero también se permite un puerto personalizado.
* `{PROFILE 1, 2, ... N}`: perfiles de configuración de inicio. Normalmente, una aplicación especifica más de un perfil de forma predeterminada (por ejemplo, un perfil para IIS Express y un perfil de proyecto, que se usa en el servidor de Kestrel).

En los ejemplos siguientes, la aplicación se hospeda en `/OAT` con una ruta de acceso base de la aplicación configurada en `wwwroot/index.html` como `<base href="/OAT/">`:

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

Para obtener información sobre el uso de una ruta de acceso base de la aplicación personalizada para las aplicaciones de Blazor WebAssembly, vea <xref:blazor/host-and-deploy/index#app-base-path>.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<h2 id="vscode">Depuración de una aplicación independiente Blazor WebAssembly</h2>

1. Abra la aplicación independiente Blazor WebAssembly en VS Code.

   Puede recibir una notificación que indica que se requiere más información para habilitar la depuración:

   > Se necesita una configuración adicional para depurar aplicaciones Blazor WebAssembly.

   Si recibe la notificación:

   * Confirme que está instalada la [extensión de C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) más reciente. Para inspeccionar las extensiones instaladas, abra **Ver** > **Extensiones** en la barra de menús o seleccione el icono **Extensiones** en la barra lateral **Actividad**.
   * Confirme que está habilitada la depuración de JavaScript en versión preliminar. Abra la configuración en la barra de menús (**Archivo** > **Preferencias** > **Configuración**). Busque con las palabras clave `debug preview`. En los resultados de la búsqueda, confirme que la casilla **Depurar > JavaScript: Usar vista previa** está activada. Si la opción para habilitar la depuración de vista previa no está presente, actualice a la versión más reciente de VS Code o instale la [extensión del depurador de JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code 1.46 o versiones anteriores).
   * Recargue la ventana.

1. Para iniciar la depuración, use el método abreviado de teclado <kbd>F5</kbd> o el elemento de menú.

   > [!NOTE]
   > No admite **Iniciar sin depuración** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>). Cuando la aplicación se ejecuta en la configuración de depuración, la sobrecarga de depuración siempre genera una pequeña reducción del rendimiento.

1. Cuando se le pida, seleccione la opción **Blazor WebAssembly Debug** (Depuración de Blazor WebAssembly) para iniciar la depuración.

1. Se inicia la aplicación independiente y se abre un explorador de depuración.

1. En la aplicación `*Client*`, establezca un punto de interrupción en la línea `currentCount++;` en `Pages/Counter.razor`.

1. En el explorador, vaya a la página `Counter` y seleccione el botón **Hacer clic aquí** para alcanzar el punto de interrupción.

> [!NOTE]
> Los puntos de interrupción **no** se alcanzan durante el inicio de la aplicación antes de que se ejecute el proxy de depuración. Esto incluye los puntos de interrupción de `Program.Main` (`Program.cs`) y los de los [métodos `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) de los componentes que se cargan en la primera página solicitada desde la aplicación.

## <a name="debug-hosted-no-locblazor-webassembly"></a>Depuración de una aplicación hospedada Blazor WebAssembly

1. Abra la carpeta de la solución de la aplicación Blazor WebAssembly hospedada en VS Code.

1. Si no hay ninguna configuración de inicio establecida para el proyecto, aparece la notificación siguiente. Seleccione **Sí**.

   > Faltan los recursos necesarios para compilar y depurar desde "{NOMBRE DE LA APLICACIÓN}". Add them?"

1. En la paleta de comandos de la parte superior de la ventana, seleccione el proyecto *Server* en la solución hospedada.

Se genera un archivo `launch.json` con la configuración de inicio para iniciar el depurador.

## <a name="attach-to-an-existing-debugging-session"></a>Adjuntar a una sesión de depuración existente

Para adjuntar a una aplicación Blazor en ejecución, cree un archivo `launch.json` con la configuración siguiente:

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> Solo se permite adjuntar a una sesión de depuración en el caso de las aplicaciones independientes. Para usar la depuración de pila completa, debe iniciar la aplicación desde VS Code.

## <a name="launch-configuration-options"></a>Opciones de configuración de inicio

Estas opciones de configuración de inicio son compatibles con el tipo de depuración `blazorwasm` (`.vscode/launch.json`).

| Opción    | Descripción |
| --------- | ----------- |
| `request` | Use `launch` para iniciar y adjuntar una sesión de depuración a una aplicación Blazor WebAssembly o `attach` para adjuntar una sesión de depuración a una aplicación que ya está en ejecución. |
| `url`     | La dirección URL que se va a abrir en el explorador durante la depuración. Tiene como valor predeterminado `https://localhost:5001`. |
| `browser` | El explorador que se va a iniciar para la sesión de depuración. Se establece en `edge` o `chrome`. Tiene como valor predeterminado `chrome`. |
| `trace`   | Se usa para generar registros desde el depurador de JS. Se establece en `true` para generar registros. |
| `hosted`  | Se debe establecer en `true` si se inicia y depura una aplicación hospedada Blazor WebAssembly. |
| `webRoot` | Especifica la ruta de acceso absoluta del servidor web. Se debe establecer si una aplicación se envía desde una subruta. |
| `timeout` | El número de milisegundos que se esperarán para adjuntar la sesión de depuración. El valor predeterminado es 30 000 milisegundos (30 segundos). |
| `program` | Una referencia al archivo ejecutable para ejecutar el servidor de la aplicación hospedada. Se debe establecer si `hosted` es `true`. |
| `cwd`     | El directorio de trabajo en el que se va a iniciar la aplicación. Se debe establecer si `hosted` es `true`. |
| `env`     | Las variables de entorno que se van a proporcionar al proceso iniciado. Solo se aplica si `hosted` está establecido en `true`. |

## <a name="example-launch-configurations"></a>Configuraciones de inicio de ejemplo

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a>Inicio y depuración de una aplicación independiente Blazor WebAssembly

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a>Adjuntar a una aplicación en ejecución en una dirección URL especificada

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a>Inicio y depuración de una aplicación Blazor WebAssembly hospedada con Microsoft Edge

La configuración predeterminada del explorador es Google Chrome. Al usar Microsoft Edge para la depuración, establezca `browser` en `edge`. Para usar Google Chrome, no establezca la opción `browser` ni establezca el valor de la opción en `chrome`.

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

En el ejemplo anterior, `MyHostedApp.Server.dll` es el ensamblado de la aplicación *Server*. La carpeta `.vscode` se encuentra en la carpeta de la solución junto a las carpetas `Client`, `Server` y `Shared`.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Para depurar una aplicación Blazor WebAssembly en Visual Studio para Mac, haga lo siguiente:

1. Cree una aplicación hospedada Blazor WebAssembly de ASP.NET Core.
1. Presione <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> para ejecutar la aplicación en el depurador.

   > [!NOTE]
   > No se admite la opción **Iniciar sin depurar** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>). Cuando la aplicación se ejecuta en la configuración de depuración, la sobrecarga de depuración siempre genera una pequeña reducción del rendimiento.

   > [!IMPORTANT]
   > El explorador seleccionado para la sesión de depuración debe ser Google Chrome o Microsoft Edge.

1. En la aplicación `*Client*`, establezca un punto de interrupción en la línea `currentCount++;` en `Pages/Counter.razor`.
1. En el explorador, vaya a la página `Counter` y seleccione el botón **Hacer clic aquí** para alcanzar el punto de interrupción:
1. En Visual Studio, revise el valor del campo `currentCount` en la ventana **Variables locales**.
1. Presione <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> para continuar la ejecución.

Al depurar una aplicación de Blazor WebAssembly, también puede depurar el código del servidor:

1. Establezca un punto de interrupción en la página `Pages/FetchData.razor` de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.
1. Establezca un punto de interrupción en `WeatherForecastController` en el método de acción `Get`.
1. Vaya a la página `Fetch Data` para alcanzar el primer punto de interrupción en el componente `FetchData` justo antes de que emita una solicitud HTTP al servidor.
1. Presione <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> para continuar la ejecución y, luego, alcance el punto de interrupción en el servidor en `WeatherForecastController`.
1. Vuelva a presionar <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> para permitir que la ejecución continúe y ver la tabla de pronóstico meteorológico representada en el explorador.

> [!NOTE]
> Los puntos de interrupción **no** se alcanzan durante el inicio de la aplicación antes de que se ejecute el proxy de depuración. Esto incluye los puntos de interrupción de `Program.Main` (`Program.cs`) y los de los [métodos `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) de los componentes que se cargan en la primera página solicitada desde la aplicación.

Para obtener más información, vea [Depuración con Visual Studio para Mac](/visualstudio/mac/debugging).

---

## <a name="debug-in-the-browser"></a>Depuración en el explorador

*La guía de esta sección se aplica a Google Chrome y Microsoft Edge en Windows.*

1. Ejecute una compilación de depuración de la aplicación en el entorno de desarrollo.

1. Inicie un explorador y vaya a la dirección URL de la aplicación (por ejemplo, `https://localhost:5001`).

1. En el explorador, intente iniciar la depuración remota; para ello, presione <kbd>Mayús</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.

   El explorador debe ejecutarse con la depuración remota habilitada, lo cual no es el valor predeterminado. Si la depuración remota está deshabilitada, aparece una página de error que informa de que **no se puede encontrar la pestaña del explorador depurable** con instrucciones para iniciar el explorador con el puerto de depuración abierto. Siga las instrucciones del explorador, que abre una nueva ventana del explorador. Cierre la ventana anterior del explorador.

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. Una vez que el explorador se ejecute con la depuración remota habilitada, el método abreviado de teclado de depuración del paso anterior abre una nueva pestaña del depurador.

1. Tras unos instantes, en la pestaña **Orígenes** se mostrará una lista de los ensamblados .NET de la aplicación en el nodo `file://`.

1. En el código de componente (archivos `.razor`) y los archivos de código de C# (`.cs`), se alcanzan los puntos de interrupción establecidos cuando se ejecuta el código. Una vez que se haya alcanzado un punto de interrupción, examine el código una vez (<kbd>F10</kbd>) o reanude (<kbd>F8</kbd>) la ejecución del código con normalidad.

Blazor proporciona un proxy de depuración que implementa el [protocolo Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) y aumenta el protocolo con información específica de .NET. Cuando se presiona el método abreviado de teclado de depuración, Blazor apunta a Chrome DevTools en el proxy. El proxy se conecta a la ventana del explorador que se quiere depurar (de ahí la necesidad de habilitar la depuración remota).

## <a name="browser-source-maps"></a>Mapas de origen del explorador

Los mapas de origen del explorador permiten al explorador volver a asignar los archivos compilados a sus archivos de código fuente originales y se suelen usar para la depuración del lado cliente. Sin embargo, en la actualidad Blazor no asigna C# directamente a JavaScript o WASM. En su lugar, Blazor realiza la interpretación de IL en el explorador, por lo que los mapas de origen no son pertinentes.

## <a name="firewall-configuration"></a>Configuración de firewall

Si un firewall bloquea la comunicación con el proxy de depuración, cree una regla de excepción de firewall que permita la comunicación entre el explorador y el proceso `NodeJS`.

> [!WARNING]
> La modificación de una configuración de firewall debe realizarse con cuidado para evitar la creación de vulnerabilidades de seguridad. Aplique con cuidado las instrucciones de seguridad, siga las recomendaciones de seguridad y respete las advertencias realizadas por el fabricante del firewall.
>
> Permitir la comunicación abierta con el proceso `NodeJS`:
>
> * Abre el servidor de Node en cualquier conexión, en función de la configuración y las funcionalidades del firewall.
> * Podría ser arriesgado en función de la red.
> * **Solo se recomienda en los equipos de los desarrolladores**.
>
> Si es posible, permita solo la comunicación abierta con el proceso `NodeJS` **en redes privadas o de confianza**.

Para obtener instrucciones de configuración del [Firewall de Windows](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security), vea [Crear una regla de programa o servicio de entrada](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule). Para más información, vea [Firewall de Windows Defender con seguridad avanzada](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) y los artículos relacionados en la serie de documentos sobre el Firewall de Windows.

## <a name="troubleshoot"></a>Solucionar problemas

Si se encuentra con errores, las sugerencias siguientes pueden ser útiles:

* En la pestaña **Depurador**, abra las herramientas para desarrolladores en el explorador. En la consola, ejecute `localStorage.clear()` para quitar los puntos de interrupción.
* Confirme que instaló el certificado de desarrollo HTTPS de ASP.NET Core y que es de confianza. Para obtener más información, vea <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
* Visual Studio requiere la opción **Habilitar depuración de JavaScript para ASP.NET (IE, Edge y Chrome)** , en **Herramientas** > **Opciones** > **Depuración** > **General**. Es el valor predeterminado para Visual Studio. Si la depuración no funciona, confirme que la opción está seleccionada.
* Si su entorno usa un proxy HTTP, asegúrese de que `localhost` esté incluido en la configuración de omisión del proxy. Esto puede hacerse al establecer la variable de entorno `NO_PROXY` en alguna de las siguientes opciones:
  * Archivo `launchSettings.json` del proyecto.
  * En el nivel de usuario o de entorno del sistema para que se aplique a todas las aplicaciones. Si usa una variable de entorno, reinicie Visual Studio para que el cambio surta efecto.
* Asegúrese de que los firewalls o servidores proxy no bloquean la comunicación con el proxy de depuración (proceso `NodeJS`). Para más información, vea la sección [Configuración de firewall](#firewall-configuration).

### <a name="breakpoints-in-oninitializedasync-not-hit"></a>Puntos de interrupción de `OnInitialized{Async}` no ejecutados

El proxy de depuración del marco de trabajo de Blazor tarda poco tiempo en iniciarse, por lo que es posible que no se ejecuten los puntos de interrupción en el [método de ciclo de vida `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods). Se recomienda agregar un retraso al principio del cuerpo del método para dar al proxy de depuración un tiempo para que se inicie antes de que se ejecute el punto de interrupción. Puede incluir el retraso según una [directiva de compilador `if`](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) para asegurarse de que el retraso no está presente en una compilación de versión de la aplicación.

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a>Tiempo de espera de Visual Studio (Windows)

Si Visual Studio inicia una excepción relacionada con que el adaptador de depuración no ha podido iniciarse en la que se menciona que se ha alcanzado el tiempo de espera, puede ajustar el tiempo de espera con una configuración del Registro:

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

El marcador de posición `{TIMEOUT}` del comando anterior se expresa en milisegundos. Por ejemplo, un minuto se asigna como `60000`.
