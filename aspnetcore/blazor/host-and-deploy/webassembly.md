---
title: Hospedaje e implementación de ASP.NET Core Blazor WebAssembly
author: guardrex
description: Aprenda a hospedar e implementar una aplicación Blazor con ASP.NET Core, redes de entrega de contenido (CDN), servidores de archivos y páginas de GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/09/2020
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
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 0912b3fbcd0b891deb4985eaa18841c22f4f3264
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055755"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a>Hospedaje e implementación de ASP.NET Core Blazor WebAssembly

Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams) y [Safia Abdalla](https://safia.rocks)

Con el [modelo de hospedaje de Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly):

* La aplicación Blazor, sus dependencias y el entorno de ejecución de .NET se descargan en el explorador en paralelo.
* La aplicación se ejecuta directamente en el subproceso de interfaz de usuario del explorador.

Se admiten las estrategias de implementación siguientes:

* Una aplicación ASP.NET Core suministra la aplicación Blazor. Esta estrategia se trata en la sección [Implementación hospedada con ASP.NET Core](#hosted-deployment-with-aspnet-core).
* La aplicación Blazor se coloca en un servicio o servidor web de hospedaje estático, donde no se usa .NET para suministrar la aplicación Blazor. Esta estrategia se trata en la sección [Implementación independiente](#standalone-deployment), que incluye información sobre cómo hospedar una aplicación Blazor WebAssembly como una subaplicación de IIS.

## <a name="compression"></a>Compresión

Cuando se publica una aplicación Blazor WebAssembly, la salida se comprime estáticamente durante la publicación para reducir el tamaño de la aplicación y acabar con la necesidad de compresión en tiempo de ejecución. Se usan los algoritmos de compresión siguientes:

* [Brotli](https://tools.ietf.org/html/rfc7932) (el nivel más alto)
* [Gzip](https://tools.ietf.org/html/rfc1952)

Blazor se basa en el host para proporcionar los archivos comprimidos adecuados. Cuando se usa un proyecto hospedado de ASP.NET Core, el proyecto host tiene capacidad para realizar la negociación de contenido y proporcionar los archivos comprimidos estáticamente. Al hospedar una aplicación independiente Blazor WebAssembly, puede que sea necesario realizar trabajo adicional para garantizar que se proporcionan los archivos comprimidos estáticamente:

* Para ver la configuración de compresión de `web.config` de IIS, vea la sección [IIS: compresión Brotli y Gzip](#brotli-and-gzip-compression). 
* Al hospedar en soluciones de hospedaje estáticas que no admiten la negociación de contenido de archivos comprimidos estáticamente (como es el caso de las páginas de GitHub), considere la posibilidad de configurar la aplicación para capturar y descodificar archivos comprimidos Brotli:

  * Obtenga el descodificador Brotli de JavaScript del [repositorio de GitHub google/brotli](https://github.com/google/brotli). A partir de septiembre de 2020, el archivo del descodificador se llama `decode.js` y se encuentra en la [carpeta `js`](https://github.com/google/brotli/tree/master/js) del repositorio.
  
    > [!NOTE]
    > En la versión reducida del script `decode.js` (`decode.min.js`) en el [repositorio de GitHub google/brotli](https://github.com/google/brotli) se encuentra una regresión. Minimice el script por su cuenta o use el [paquete npm](https://www.npmjs.com/package/brotli) hasta que se resuelva la incidencia [Window.BrotliDecode no se ha establecido en decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844). En el código de ejemplo de esta sección se usa la versión **no reducida** del script.

  * Actualice la aplicación para que use el descodificador. Cambie el marcado de la etiqueta `<body>` de cierre en `wwwroot/index.html` por lo siguiente:
  
    ```html
    <script src="decode.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
      Blazor.start({
        loadBootResource: function (type, name, defaultUri, integrity) {
          if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
            return (async function () {
              const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
              if (!response.ok) {
                throw new Error(response.statusText);
              }
              const originalResponseBuffer = await response.arrayBuffer();
              const originalResponseArray = new Int8Array(originalResponseBuffer);
              const decompressedResponseArray = BrotliDecode(originalResponseArray);
              const contentType = type === 
                'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
              return new Response(decompressedResponseArray, 
                { headers: { 'content-type': contentType } });
            })();
          }
        }
      });
    </script>
    ```
 
Para deshabilitar la compresión, agregue la propiedad `BlazorEnableCompression` de MSBuild al archivo del proyecto de la aplicación y establezca el valor en `false`:

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

La propiedad `BlazorEnableCompression` se puede pasar al comando [`dotnet publish`](/dotnet/core/tools/dotnet-publish) con la sintaxis siguiente en un shell de comandos:

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a>Reescritura de las URL para conseguir un enrutamiento correcto

Enrutar las solicitudes de los componentes de página de una aplicación Blazor WebAssembly no es tan sencillo como enrutar las solicitudes de una aplicación Blazor Server hospedada. Imagine que tiene una aplicación Blazor WebAssembly con dos componentes:

* `Main.razor`: se carga en la raíz de la aplicación y contiene un vínculo al componente `About` (`href="About"`).
* `About.razor`: componente `About`.

Cuando se solicita el documento predeterminado de la aplicación mediante la barra de direcciones del explorador (por ejemplo, `https://www.contoso.com/`):

1. El explorador realiza una solicitud.
1. Se devuelve la página predeterminada, que suele ser `index.html`.
1. `index.html` arranca la aplicación.
1. Se carga el enrutador de Blazor y se representa el componente `Main` de Razor.

En la página principal, la selección del vínculo al componente `About` funciona en el cliente porque el enrutador de Blazor impide que el explorador haga una solicitud en Internet a `www.contoso.com` sobre `About` y presenta el propio componente `About` representado. Todas las solicitudes de puntos de conexión internos *dentro de la aplicación Blazor WebAssembly* funcionan del mismo modo: Las solicitudes no desencadenan solicitudes basadas en el explorador a recursos hospedados en el servidor en Internet. El enrutador controla las solicitudes de forma interna.

Si se realiza una solicitud mediante la barra de direcciones del explorador para `www.contoso.com/About`, se produce un error. Este recurso no existe en el host de Internet de la aplicación, por lo que se devuelve una respuesta *404 No encontrado*.

Dado que los exploradores solicitan páginas del lado cliente a hosts basados en Internet, los servidores web y los servicios de hospedaje deben reescribir todas las solicitudes de recursos que no estén físicamente en el servidor a la página `index.html`. Cuando se devuelve `index.html`, el enrutador Blazor de la aplicación se hace cargo y responde con el recurso correcto.

Al implementar en un servidor IIS, puede usar el módulo URL Rewrite con el archivo `web.config` publicado de la aplicación. Para más información, consulte la sección sobre [IIS](#iis).

## <a name="hosted-deployment-with-aspnet-core"></a>Implementación hospedada con ASP.NET Core

Una *implementación hospedada* se encarga de suministrar la aplicación Blazor WebAssembly a los exploradores desde una [aplicación ASP.NET Core](xref:index) que se ejecuta en un servidor web.

La aplicación cliente de Blazor WebAssembly se publica en la carpeta `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` de la aplicación de servidor, junto con cualquier otro recurso web estático de la aplicación de servidor. Las dos aplicaciones se implementan juntas. Se requiere un servidor web que pueda hospedar una aplicación ASP.NET Core. En el caso de una implementación hospedada, Visual Studio incluye la plantilla de proyecto **Aplicación de Blazor WebAssembly** (plantilla `blazorwasm` si se usa el comando [`dotnet new`](/dotnet/core/tools/dotnet-new)) con la opción **`Hosted`** seleccionada (`-ho|--hosted` si se usa el comando `dotnet new`).

Para obtener más información sobre la implementación y el hospedaje de aplicaciones de ASP.NET Core, consulte <xref:host-and-deploy/index>.

Para obtener información sobre cómo implementar en Azure App Service, vea <xref:tutorials/publish-to-azure-webapp-using-vs>.

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a>Implementación hospedada con varias aplicaciones Blazor WebAssembly

### <a name="app-configuration"></a>Configuración de la aplicación

Para configurar una solución de Blazor hospedada para atender a varias aplicaciones Blazor WebAssembly:

* Use una solución de Blazor hospedada existente o cree una solución a partir de la plantilla de proyecto hospedada de Blazor.

* En el archivo de proyecto de la aplicación cliente, agregue una propiedad `<StaticWebAssetBasePath>` a `<PropertyGroup>` con un valor de `FirstApp` para establecer la ruta de acceso base para los recursos estáticos del proyecto:

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* Agregue una segunda aplicación cliente a la solución:

  * Agregue una carpeta denominada `SecondClient` a la carpeta de la solución.
  * Cree una aplicación Blazor WebAssembly denominada `SecondBlazorApp.Client` en la carpeta `SecondClient` de la plantilla de proyecto de Blazor WebAssembly.
  * En el archivo de proyecto de la aplicación:

    * Agregue una propiedad `<StaticWebAssetBasePath>` a `<PropertyGroup>` con un valor de `SecondApp`:

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * Agregue una referencia de proyecto al proyecto `Shared`:

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      El marcador de posición `{SOLUTION NAME}` es el nombre de la solución.

* En el archivo del proyecto de la aplicación de servidor, cree una referencia de proyecto para la aplicación cliente agregada:

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* En el archivo `Properties/launchSettings.json` de la aplicación de servidor, configure el elemento `applicationUrl` del perfil de Kestrel (`{SOLUTION NAME}.Server`) para acceder a las aplicaciones cliente en los puertos 5001 y 5002:

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* En el método `Startup.Configure` de la aplicación de servidor (`Startup.cs`), quite las líneas siguientes, que aparecen después de la llamada a <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:

  ```csharp
  app.UseBlazorFrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.MapRazorPages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  Agregue middleware que asigna solicitudes a las aplicaciones cliente. En el ejemplo siguiente se configura el middleware para que se ejecute cuando:

  * El puerto de solicitud es 5001 para la aplicación cliente original o 5002 para la aplicación cliente agregada.
  * El host de solicitud es `firstapp.com` para la aplicación cliente original o `secondapp.com` para la aplicación cliente agregada.

    > [!NOTE]
    > El ejemplo que se muestra en esta sección requiere una configuración adicional para:
    >
    > * Acceder a las aplicaciones en los dominios de host de ejemplo, `firstapp.com` y `secondapp.com`.
    > * Certificados que permitan a las aplicaciones cliente habilitar la seguridad TLS (HTTPS).
    >
    > La configuración necesaria queda fuera del ámbito de este artículo y depende de cómo se hospede la solución. Para más información, vea los [artículos sobre hospedaje e implementación](xref:host-and-deploy/index).

  Coloque el código siguiente donde se quitaron las líneas anteriormente:

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.UseBlazorFrameworkFiles("/FirstApp");
      first.UseStaticFiles();
      first.UseStaticFiles("/FirstApp");
      first.UseRouting();

      first.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/FirstApp/{*path:nonfile}", 
              "FirstApp/index.html");
      });
  });
  
  app.MapWhen(ctx => ctx.Request.Host.Port == 5002 || 
      ctx.Request.Host.Equals("secondapp.com"), second =>
  {
      second.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/SecondApp" + ctx.Request.Path;
          return nxt();
      });

      second.UseBlazorFrameworkFiles("/SecondApp");
      second.UseStaticFiles();
      second.UseStaticFiles("/SecondApp");
      second.UseRouting();

      second.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/SecondApp/{*path:nonfile}", 
              "SecondApp/index.html");
      });
  });
  ```

* En el controlador de pronóstico meteorológico (`Controllers/WeatherForecastController.cs`) de la aplicación de servidor, reemplace la ruta existente (`[Route("[controller]")]`) a `WeatherForecastController` por las rutas siguientes:

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  El middleware agregado al método `Startup.Configure` de la aplicación de servidor anteriormente modifica las solicitudes entrantes a `/WeatherForecast` por `/FirstApp/WeatherForecast` o `/SecondApp/WeatherForecast` en función del puerto (5001/5002) o el dominio (`firstapp.com`/`secondapp.com`). Las rutas de controlador anteriores son necesarias para devolver datos meteorológicos de la aplicación de servidor a las aplicaciones cliente.

### <a name="static-assets-and-class-libraries"></a>Recursos estáticos y bibliotecas de clases

Use los métodos siguientes para los recursos estáticos:

* Cuando el recurso se encuentra en la carpeta `wwwroot` de la aplicación cliente, proporcione sus rutas de acceso de la forma habitual:

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* Cuando el recurso se encuentra en la carpeta `wwwroot` de una [biblioteca de clases de Razor (RCL)](xref:blazor/components/class-libraries), haga referencia al recurso estático de la aplicación cliente según las instrucciones del [artículo de RCL](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

Components provided to a client app by a class library are referenced normally. If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:

* The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.
* The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.

The preceding approaches are demonstrated in the following examples.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

Se hace referencia de la forma habitual a los componentes que una biblioteca de clases proporciona a una aplicación cliente. Si algún componente requiere hojas de estilo o archivos JavaScript, el archivo `wwwroot/index.html` de la aplicación cliente debe incluir los vínculos de recursos estáticos correctos. Estos enfoques se demuestran en los ejemplos siguientes.

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

Agregue el siguiente componente `Jeep` a una de las aplicaciones cliente. El componente `Jeep` usa:

* Una imagen de la carpeta `wwwroot` de la aplicación cliente (`jeep-cj.png`).
* Una imagen de una carpeta `wwwroot` de la [biblioteca de componentes de Razor agregada](xref:blazor/components/class-libraries) (`JeepImage`) (`jeep-yj.png`).
* La plantilla del proyecto de RCL crea automáticamente el componente de ejemplo (`Component1`) cuando se agrega la biblioteca `JeepImage` a la solución.

```razor
@page "/Jeep"

<h1>1979 Jeep CJ-5&trade;</h1>

<p>
    <img alt="1979 Jeep CJ-5&trade;" src="/jeep-cj.png" />
</p>

<h1>1991 Jeep YJ&trade;</h1>

<p>
    <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
</p>

<p>
    <em>Jeep CJ-5</em> and <em>Jeep YJ</em> are a trademarks of 
    <a href="https://www.fcagroup.com">Fiat Chrysler Automobiles</a>.
</p>

<JeepImage.Component1 />
```

> [!WARNING]
> **No** publique imágenes de vehículos a menos que sean de su titularidad. De lo contrario, corre el riesgo de infringir los derechos de autor.

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`). To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file. This approach makes the stylesheet available to all of the components in the client app:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

También se puede agregar la imagen `jeep-yj.png` de la biblioteca al componente `Component1` de la biblioteca (`Component1.razor`):

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

El archivo `wwwroot/index.html` de la aplicación cliente solicita la hoja de estilo de la biblioteca con la siguiente etiqueta `<link>` agregada:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

Agregue la navegación al componente `Jeep` en el componente `NavMenu` de la aplicación cliente (`Shared/NavMenu.razor`):

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

Para obtener más información sobre RCL, vea:

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a>Implementación independiente

Una *implementación independiente* suministra la aplicación Blazor WebAssembly como un conjunto de archivos estáticos que los clientes solicitan directamente. Cualquier servidor de archivos estático es capaz de suministrar la aplicación Blazor.

Los recursos de implementación independientes se publican en la carpeta `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot`.

### <a name="azure-app-service"></a>Azure App Service

Las aplicaciones Blazor WebAssembly se pueden implementar en Azure App Services en Windows, que hospeda la aplicación en [IIS](#iis).

Actualmente no se admite la implementación de una aplicación Blazor WebAssembly independiente en Azure App Service para Linux. En este momento no hay disponible una imagen de servidor de Linux para hospedar la aplicación. Se está trabajando para habilitar este escenario.

### <a name="iis"></a>IIS

IIS es un servidor de archivos estáticos compatible con las aplicaciones Blazor. Para configurar IIS para hospedar Blazor, consulte [Compilación de un sitio web estático en IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Los recursos publicados se crean en la carpeta `/bin/Release/{TARGET FRAMEWORK}/publish`. Hospede el contenido de la carpeta `publish` en el servidor web o el servicio de hospedaje.

#### <a name="webconfig"></a>web.config

Cuando se publica un proyecto de Blazor, se crea un archivo `web.config` con la siguiente configuración de IIS:

* Se establecen los tipos MIME de las siguientes extensiones de archivo:
  * `.dll`: `application/octet-stream`
  * `.json`: `application/json`
  * `.wasm`: `application/wasm`
  * `.woff`: `application/font-woff`
  * `.woff2`: `application/font-woff`
* Se habilita la compresión HTTP de los siguientes tipos MIME:
  * `application/octet-stream`
  * `application/wasm`
* Se establecen reglas del módulo URL Rewrite:
  * Proporcione el subdirectorio donde residen los recursos estáticos de la aplicación (`wwwroot/{PATH REQUESTED}`).
  * Cree el enrutamiento de reserva de SPA para que las solicitudes de recursos que no sean archivos se redirijan al documento predeterminado de la aplicación en su carpeta de recursos estáticos (`wwwroot/index.html`).
  
#### <a name="use-a-custom-webconfig"></a>Uso de un archivo web.config personalizado

Para usar un archivo `web.config` personalizado, coloque el archivo `web.config` personalizado en la raíz de la carpeta del proyecto. Configure el proyecto para publicar recursos específicos de IIS mediante `PublishIISAssets` en el archivo de proyecto de la aplicación y publique el proyecto:

```xml
<PropertyGroup>
  <PublishIISAssets>true</PublishIISAssets>
</PropertyGroup>
```

#### <a name="install-the-url-rewrite-module"></a>Instalación del módulo URL Rewrite

El [módulo URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite) es necesario para reescribir las URL. El módulo no se instala de forma predeterminada y no está disponible para instalar como una característica de servicio de rol del servidor web (IIS). El módulo se debe descargar desde el sitio web de IIS. Use el instalador de plataforma web para instalar el módulo:

1. De forma local, vaya a la [página de descargas del módulo URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). En el caso de la versión en inglés, seleccione **WebPI** para descargar el instalador de WebPI. En el caso de otros idiomas, seleccione la arquitectura adecuada del servidor (x86/x64) para descargar el instalador.
1. Copie el instalador en el servidor. Ejecute el instalador. Haga clic en el botón **Instalar** y acepte los términos de licencia. No es necesario reiniciar el servidor al finalizar la instalación.

#### <a name="configure-the-website"></a>Configuración del sitio web

Configure la **ruta de acceso física** del sitio web a la carpeta de la aplicación. La carpeta contiene:

* El archivo `web.config` que usa IIS para configurar el sitio web, incluidas las reglas de redireccionamiento y los tipos de contenido de archivo necesarios.
* La carpeta de recursos estáticos de la aplicación.

#### <a name="host-as-an-iis-sub-app"></a>Hospedaje como subaplicación de IIS

Si una aplicación independiente se hospeda como una subaplicación de IIS, realice una de las siguientes acciones:

* Deshabilite el controlador del módulo de ASP.NET Core heredado.

  Para quitar el controlador del archivo `web.config` publicado de la aplicación Blazor, agregue una sección `<handlers>` al archivo:

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* Deshabilite la herencia de la sección `<system.webServer>` de la aplicación raíz (principal) mediante un elemento `<location>` con `inheritInChildApplications` establecido en `false`:

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

Además de [configurarse la ruta de acceso base de la aplicación](xref:blazor/host-and-deploy/index#app-base-path), se quita el controlador o se deshabilita la herencia. En el archivo `index.html` de la aplicación, establezca la ruta de acceso base de la aplicación en el alias de IIS que ha usado al configurar la subaplicación en IIS.

#### <a name="brotli-and-gzip-compression"></a>Compresión Brotli y Gzip

IIS se puede configurar a través de `web.config` para dar servicio a recursos de Blazor comprimidos con Brotli o Gzip. Para ver una configuración de ejemplo, consulte [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).

#### <a name="troubleshooting"></a>Solución de problemas

Si se recibe un error *500 Error interno del servidor* y el administrador de IIS produce errores al intentar acceder a la configuración del sitio web, confirme que el módulo URL Rewrite está instalado. Si no lo está, IIS no puede analizar el archivo `web.config`. Esto impide que el Administrador de IIS cargue la configuración del sitio web y que el sitio web suministre los archivos estáticos de Blazor.

Para obtener más información sobre cómo solucionar problemas de las implementaciones en IIS, vea <xref:test/troubleshoot-azure-iis>.

### <a name="azure-storage"></a>Almacenamiento de Azure

El hospedaje de archivos estáticos de [Azure Storage](/azure/storage/) permite el hospedaje de aplicaciones Blazor sin servidor. Se admiten nombres de dominio personalizados, Azure Content Delivery Network (CDN) y HTTPS.

Cuando el servicio de blob está habilitado para el hospedaje de sitios web estáticos en una cuenta de almacenamiento:

* Establece el **nombre de documento de índice** en `index.html`.
* Establece la **ruta de acceso del documento de error** en `index.html`. Los componentes Razor y otros puntos de conexión que no son de archivo no residen en las rutas de acceso físicas del contenido estático almacenado por el servicio de blob. Cuando se recibe una solicitud de uno de estos recursos que debe controlar el enrutador de Blazor, el error *404 - No encontrado* generado por el servicio de blob enruta la solicitud a la **ruta de acceso del documento de error**. Se devuelve el blob `index.html`, y el enrutador de Blazor carga y procesa la ruta de acceso.

Si los archivos no se cargan en tiempo de ejecución debido a tipos MIME inadecuados en los encabezados `Content-Type` de los archivos, haga algunas de las acciones siguientes:

* Configure las herramientas para establecer los tipos MIME correctos (encabezados `Content-Type`) cuando se implementen los archivos.
* Cambie los tipos MIME (encabezados `Content-Type`) de los archivos una vez que se implementa la aplicación.

  En cada archivo del Explorador de Storage (Azure Portal) haga lo siguiente:
  
  1. Haga clic con el botón derecho en el archivo y seleccione **Propiedades**.
  1. Establezca el valor de **ContentType** y seleccione el botón **Guardar**.

Para más información, consulte [Hospedaje de sitios web estáticos en Azure Storage](/azure/storage/blobs/storage-blob-static-website).

### <a name="nginx"></a>Nginx

El siguiente archivo `nginx.conf` se ha simplificado para mostrar cómo hay que configurar Nginx para enviar el archivo `index.html` siempre que no pueda encontrar un archivo correspondiente en el disco.

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

Al establecer el [límite de velocidad de ráfaga de NGINX](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) con [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), las aplicaciones de Blazor WebAssembly pueden requerir un valor de parámetro `burst` grande para acomodar el número relativamente elevado de solicitudes realizadas por una aplicación. Inicialmente, establezca el valor en al menos 60:

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

Aumente el valor si las herramientas de desarrollo del explorador o la herramienta de tráfico de red indican que las solicitudes reciben un código de estado *503: Servicio no disponible*.

Para obtener más información sobre la configuración del servidor web de producción de Nginx, consulte [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Creación de archivos de configuración de NGINX y NGINX Plus).

### <a name="nginx-in-docker"></a>Nginx en Docker

Para hospedar Blazor en Docker mediante Nginx, configure Dockerfile para usar la imagen de Nginx basada en Alpine. Actualice el Dockerfile para copiar el archivo `nginx.config` en el contenedor.

Agregue una línea al Dockerfile, como se muestra en el ejemplo siguiente:

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

Para implementar una aplicación Blazor WebAssembly en CentOS 7 o posterior:

1. Cree el archivo de configuración de Apache. El siguiente ejemplo es un archivo de configuración simplificado (`blazorapp.config`):

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. Coloque el archivo de configuración de Apache en el directorio `/etc/httpd/conf.d/`, que es el directorio de configuración de Apache predeterminado en CentOS 7.

1. Coloque los archivos de la aplicación en el directorio `/var/www/blazorapp` (la ubicación especificada para `DocumentRoot` en el archivo de configuración).

1. Reinicie el servicio de Apache.

Para más información, vea [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) y [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).

### <a name="github-pages"></a>GitHub Pages

Para controlar las reescrituras de URL, agregue un archivo `wwwroot/404.html` con un script que controle el redireccionamiento de la solicitud a la página `index.html`. Para obtener un ejemplo, vea el [repositorio de GitHub SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* [Sitio activo](https://stevesandersonms.github.io/BlazorOnGitHubPages/)

Si usa un sitio de proyecto en lugar de un sitio de la organización, actualice la etiqueta `<base>` en `wwwroot/index.html`. Defina el valor del atributo `href` con el nombre del repositorio de GitHub con una barra diagonal final (por ejemplo, `/my-repository/`). En el [repositorio de GitHub SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), el elemento `href` base se actualiza cuando el [archivo de configuración `.github/workflows/main.yml`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml) realiza la publicación.

> [!NOTE]
> El [repositorio de GitHub SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) no es propiedad de .NET Foundation o Microsoft, ni tampoco se encargan de su mantenimiento ni es compatible con ellos.

## <a name="host-configuration-values"></a>Valores de configuración de host

Las [aplicaciones Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) pueden aceptar los siguientes valores de configuración de host como argumentos de línea de comandos en tiempo de ejecución en el entorno de desarrollo.

### <a name="content-root"></a>Raíz del contenido

El argumento `--contentroot` establece la ruta de acceso absoluta al directorio que incluye los archivos de contenido de la aplicación ([raíz del contenido](xref:fundamentals/index#content-root)). En los ejemplos siguientes, `/content-root-path` es la ruta de acceso raíz del contenido de la aplicación.

* Pase el argumento al ejecutar la aplicación de forma local en un símbolo del sistema. En el directorio de la aplicación, ejecute lo siguiente:

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* Agregue una entrada al archivo `launchSettings.json` de la aplicación en el perfil **IIS Express**. Esta configuración se utiliza cuando se ejecuta la aplicación mediante el depurador de Visual Studio y desde un símbolo del sistema con `dotnet run`.

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* En Visual Studio, especifique el argumento en **Propiedades** > **Depuración** > **Argumentos de la aplicación**. Al establecer el argumento en la página de propiedades de Visual Studio, se agrega el argumento al archivo `launchSettings.json`.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Ruta de acceso base

El argumento `--pathbase` establece la ruta de acceso base de la aplicación para una aplicación que se ejecuta localmente con una ruta de acceso de URL relativa que no es raíz (el valor `href` de la etiqueta `<base>` se establece en una ruta de acceso que no sea `/` para ensayo y producción). En los ejemplos siguientes, `/relative-URL-path` es la ruta de acceso base de la aplicación. Para obtener más información, vea [Ruta de acceso base de la aplicación](xref:blazor/host-and-deploy/index#app-base-path).

> [!IMPORTANT]
> A diferencia de la ruta de acceso proporcionada al valor `href` de la etiqueta `<base>`, no incluya una barra diagonal final (`/`) al pasar el valor del argumento `--pathbase`. Si se proporciona la ruta de acceso base de la aplicación en la etiqueta `<base>` como `<base href="/CoolApp/">` (se incluye una barra diagonal final), se pasa el valor del argumento de línea de comandos como `--pathbase=/CoolApp` (sin barra diagonal final).

* Pase el argumento al ejecutar la aplicación de forma local en un símbolo del sistema. En el directorio de la aplicación, ejecute lo siguiente:

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* Agregue una entrada al archivo `launchSettings.json` de la aplicación en el perfil **IIS Express**. Esta configuración se utiliza cuando se ejecuta la aplicación mediante el depurador de Visual Studio y desde un símbolo del sistema con `dotnet run`.

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* En Visual Studio, especifique el argumento en **Propiedades** > **Depuración** > **Argumentos de la aplicación**. Al establecer el argumento en la página de propiedades de Visual Studio, se agrega el argumento al archivo `launchSettings.json`.

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>Direcciones URL

El argumento `--urls` establece las direcciones IP o las direcciones de host con los puertos y protocolos en los que escuchar las solicitudes.

* Pase el argumento al ejecutar la aplicación de forma local en un símbolo del sistema. En el directorio de la aplicación, ejecute lo siguiente:

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* Agregue una entrada al archivo `launchSettings.json` de la aplicación en el perfil **IIS Express**. Esta configuración se utiliza cuando se ejecuta la aplicación mediante el depurador de Visual Studio y desde un símbolo del sistema con `dotnet run`.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* En Visual Studio, especifique el argumento en **Propiedades** > **Depuración** > **Argumentos de la aplicación**. Al establecer el argumento en la página de propiedades de Visual Studio, se agrega el argumento al archivo `launchSettings.json`.

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a>Configurar el recortador

Blazor realiza el recorte de lenguaje intermedio (IL) en cada compilación de lanzamiento para quitar el IL innecesario de los ensamblados de salida. Para obtener más información, vea <xref:blazor/host-and-deploy/configure-trimmer>.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a>Configurar el enlazador

Blazor realiza la vinculación de lenguaje intermedio (IL) en cada compilación de lanzamiento para quitar el IL innecesario de los ensamblados de salida. Para obtener más información, vea <xref:blazor/host-and-deploy/configure-linker>.

::: moniker-end

## <a name="custom-boot-resource-loading"></a>Carga de recursos de arranque personalizados

Una aplicación Blazor WebAssembly se puede inicializar con la función `loadBootResource` para invalidar el mecanismo de carga de recursos de arranque integrado. Use `loadBootResource` en los siguientes escenarios:

* Para permitir a los usuarios cargar recursos estáticos, como datos de zona horaria o `dotnet.wasm`, desde una red CDN.
* Para cargar los ensamblados comprimidos mediante una solicitud HTTP y descomprimirlos en el cliente para aquellos hosts que no admiten la captura de contenido comprimido del servidor.
* Para asignar otro alias a los recursos mediante el redireccionamiento de cada solicitud `fetch` a un nuevo nombre.

Los parámetros `loadBootResource` aparecen en la tabla siguiente.

| Parámetro    | Descripción |
| ------------ | ----------- |
| `type`       | Tipo de recurso. Tipos permitidos: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata` |
| `name`       | Nombre del recurso. |
| `defaultUri` | URI relativo o absoluto del recurso. |
| `integrity`  | Cadena de integridad que representa el contenido esperado de la respuesta. |

`loadBootResource` devuelve cualquiera de los siguientes elementos para invalidar el proceso de carga:

* Cadena URI. En el ejemplo siguiente (`wwwroot/index.html`), los siguientes archivos se sirven desde una red CDN en `https://my-awesome-cdn.com/`:

  * `dotnet.*.js`
  * `dotnet.wasm`
  * Datos de zona horaria

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* `Promise<Response>`. Pase el parámetro `integrity` en un encabezado para conservar el comportamiento de comprobación de integridad predeterminado.

  En el ejemplo siguiente (`wwwroot/index.html`), se agrega un encabezado HTTP personalizado a las solicitudes salientes y se pasa el parámetro `integrity` a través de la llamada a `fetch`:
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* `null`/`undefined`, que da lugar al comportamiento de carga predeterminado.

Los orígenes externos deben devolver los encabezados CORS necesarios para que los exploradores permitan la carga de recursos entre orígenes. Normalmente, las redes CDN proporcionan los encabezados necesarios de forma predeterminada.

Solo tiene que especificar tipos para los comportamientos personalizados. Los tipos no especificados en `loadBootResource` se cargan mediante el marco de trabajo según sus comportamientos de carga predeterminados.

## <a name="change-the-filename-extension-of-dll-files"></a>Cambio de la extensión de nombre de archivo de los archivos DLL

En caso de que necesite cambiar las extensiones de nombre de los archivos `.dll` publicados de la aplicación, siga las instrucciones de esta sección.

Después de publicar la aplicación, use un script de shell o una canalización de compilación de DevOps para cambiar el nombre de los archivos `.dll` a fin de usar otra extensión de archivo. Establezca como destino los archivos `.dll` que están en el directorio `wwwroot` de la salida publicada de la aplicación (por ejemplo, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).

En los ejemplos siguientes se cambia el nombre de los archivos `.dll` para que usen la extensión de archivo `.bin`.

En Windows:

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

Si los recursos de trabajo de servicio también están en uso, agregue el siguiente comando:

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

En Linux o macOS:

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

Si los recursos de trabajo de servicio también están en uso, agregue el siguiente comando:

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
Para usar una extensión de archivo diferente a `.bin`, reemplace `.bin` en los comandos anteriores.

Para dirigirse a los archivos comprimidos `blazor.boot.json.gz` y `blazor.boot.json.br`, adopte uno de los métodos siguientes:

* Quite los archivos comprimidos `blazor.boot.json.gz` y `blazor.boot.json.br`. Con este enfoque, la compresión está deshabilitada.
* Vuelva a comprimir el archivo `blazor.boot.json` actualizado.

Las instrucciones anteriores también se aplican cuando se usan recursos de trabajo de servicio. Quite o vuelva a comprimir `wwwroot/service-worker-assets.js.br` y `wwwroot/service-worker-assets.js.gz`. De lo contrario, las comprobaciones de integridad de los archivos producirán errores en el explorador.

En el siguiente ejemplo de Windows se usa un script de PowerShell colocado en la raíz del proyecto.

`ChangeDLLExtensions.ps1:`:

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

Si los recursos de trabajo de servicio también están en uso, agregue el siguiente comando:

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

En el archivo del proyecto, el script se ejecuta después de publicar la aplicación:

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> Al cambiar el nombre y la carga diferida de los mismos ensamblados, vea la guía de <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.

## <a name="resolve-integrity-check-failures"></a>Resolución de errores de comprobación de integridad

Cuando Blazor WebAssembly descarga los archivos de inicio de una aplicación, le indica al navegador que realice comprobaciones de integridad en las respuestas. Usa la información del archivo `blazor.boot.json` para especificar los valores hash de SHA-256 esperados para `.dll`, `.wasm` y otros archivos. Esto es beneficioso por los siguientes motivos:

* Garantiza que no se corre el riesgo de cargar un conjunto de archivos incoherentes, por ejemplo, si se aplica una nueva implementación al servidor web mientras el usuario se encuentra en el proceso de descarga de los archivos de aplicación. Los archivos incoherentes pueden provocar un comportamiento indefinido.
* Garantiza que el explorador del usuario nunca almacene en caché las respuestas incoherentes o no válidas, lo que podría impedir que se inicie la aplicación aunque actualice manualmente la página.
* Hace que sea seguro almacenar en caché las respuestas y ni siquiera comprobar los cambios en el servidor hasta que cambien los algoritmos hash SHA-256 esperados, por lo que las cargas de páginas posteriores implican menos solicitudes y se completan mucho más rápido.

Si el servidor web devuelve respuestas que no coinciden con los algoritmos hash SHA-256 esperados, verá un error similar al siguiente en la consola del desarrollador del explorador:

```
Failed to find a valid digest in the 'integrity' attribute for resource 'https://myapp.example.com/_framework/MyBlazorApp.dll' with computed SHA-256 integrity 'IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY='. The resource has been blocked.
```

En la mayoría de los casos, esto *no* supone un problema con la propia comprobación de la integridad. Significa en realidad que hay algún otro problema del cual le está advirtiendo la comprobación de la integridad.

### <a name="diagnosing-integrity-problems"></a>Diagnóstico de problemas de integridad

Cuando se compila una aplicación, el manifiesto de `blazor.boot.json` generado describe los algoritmos hash SHA-256 de los recursos de arranque (por ejemplo, `.dll`, `.wasm` y otros archivos) en el momento en que se genera el resultado de la compilación. La comprobación de integridad se supera siempre que los algoritmos hash SHA-256 de `blazor.boot.json` coincidan con los archivos entregados al explorador.

Los motivos comunes por los que se produce un error son:

 * La respuesta del servidor web es un error (por ejemplo, *404 No encontrado* o *500 Error interno del servidor* ) en lugar del archivo solicitado por el explorador. El explorador lo detecta como un error de comprobación de integridad y no como un error de respuesta.
 * Algo ha cambiado el contenido de los archivos entre la compilación y la entrega de los archivos al explorador. Esto puede ocurrir:
   * Si el usuario o las herramientas de compilación modifican manualmente la salida de compilación.
   * Si algún aspecto del proceso de implementación ha modificado los archivos. Por ejemplo, si usa un mecanismo de implementación basado en Git, tenga en cuenta que Git convierte de forma transparente los finales de línea de estilo Windows en finales de línea de estilo Unix si confirma archivos en Windows y los comprueba en Linux. El cambio de los finales de línea de archivo cambia los algoritmos hash SHA-256. Para evitar este problema, considere la posibilidad de [usar `.gitattributes` para tratar los artefactos de compilación como archivos `binary`](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes).
   * El servidor web modifica el contenido del archivo como parte del servicio. Por ejemplo, algunas redes de distribución de contenido (CDN) intentan automáticamente [minimizar](xref:client-side/bundling-and-minification#minification) HTML, con lo que se modifica. Es posible que tenga que deshabilitar estas características.

Para diagnosticar cuál de ellas se aplica en su caso:

 1. Lea el mensaje de error para darse cuenta de qué archivo está desencadenando el error.
 1. Abra las herramientas de desarrollo del explorador y mire en la pestaña *Red*. Si es necesario, vuelva a cargar la página para ver la lista de solicitudes y respuestas. Busque el archivo que desencadena el error en esa lista.
 1. Compruebe el código de estado HTTP en la respuesta. Si el servidor devuelve un valor distinto de *200 - Correcto* (u otro código de estado 2XX), tiene un problema de servidor por diagnosticar. Por ejemplo, el código de estado 403 significa que hay un problema de autorización, mientras que el código de estado 500 significa que el servidor está dando error de una manera no especificada. Consulte los registros del servidor para diagnosticar y corregir la aplicación.
 1. Si el código de estado es *200 - Correcto* para el recurso, examine el contenido de la respuesta en las herramientas de desarrollo del explorador y compruebe que el contenido coincida con los datos esperados. Por ejemplo, un problema común es configurar erróneamente el enrutamiento de modo que las solicitudes devuelvan los datos de `index.html` incluso para otros archivos. Asegúrese de que las respuestas a las solicitudes de `.wasm` son archivos binarios de WebAssembly y que las respuestas a las solicitudes de `.dll` son archivos binarios de ensamblado de .NET. Si no es así, tiene un problema de enrutamiento del lado servidor por diagnosticar.

Si confirma que el servidor está devolviendo datos plausiblemente correctos, debe haber algo más que modifique el contenido entre la compilación y la entrega del archivo. Para investigarlo:

 * Examine la cadena de herramientas de compilación y el mecanismo de implementación en caso de que estén modificando archivos después de compilarlos. Un ejemplo de esto es cuando GIT transforma los finales de línea de los archivos, tal y como se ha descrito anteriormente.
 * Examine el servidor web o la configuración de CDN en caso de que estén configurados para modificar las respuestas de forma dinámica (por ejemplo, intentando minimizar HTML). Está adecuado que el servidor web implemente la compresión HTTP (por ejemplo, devolviendo `content-encoding: br` o `content-encoding: gzip`), ya que esto no afecta al resultado después de la descompresión. Sin embargo, *no* es adecuado que el servidor web modifique los datos sin comprimir.

### <a name="disable-integrity-checking-for-non-pwa-apps"></a>Deshabilitación de la comprobación de integridad para aplicaciones que no son de PWA

En la mayoría de los casos, no deshabilite la comprobación de integridad. Al deshabilitar la comprobación de integridad, no se soluciona el problema subyacente que ha causado las respuestas inesperadas y se pierden las ventajas mencionadas anteriormente.

Puede haber casos en los que no se pueda confiar en el servidor web para que devuelva respuestas coherentes, y solo le queda la opción de deshabilitar las comprobaciones de integridad. Para deshabilitar las comprobaciones de integridad, agregue lo siguiente a un grupo de propiedades en el archivo de `.csproj` del proyecto de Blazor WebAssembly:

```xml
<BlazorCacheBootResources>false</BlazorCacheBootResources>
```

`BlazorCacheBootResources` también deshabilita el comportamiento predeterminado de Blazorde almacenar en caché `.dll`, `.wasm` y otros archivos según sus algoritmos hash SHA-256, ya que la propiedad indica que no se puede confiar en la exactitud de los algoritmos hash SHA-256. Incluso con esta configuración, es posible que la memoria caché HTTP normal del explorador siga almacenando en caché esos archivos, pero que esto suceda o no depende de la configuración del servidor web y de los encabezados de `cache-control` a los que sirve.

> [!NOTE]
> La propiedad `BlazorCacheBootResources` no deshabilita las comprobaciones de integridad de las [aplicaciones web progresivas (PWA)](xref:blazor/progressive-web-app). Para obtener instrucciones relativas a las PWA, consulte la sección [Deshabilitación de la comprobación de integridad para aplicaciones PWA](#disable-integrity-checking-for-pwas).

### <a name="disable-integrity-checking-for-pwas"></a>Deshabilitación de la comprobación de integridad para aplicaciones PWA

La plantilla de aplicación web progresiva (PWA) de Blazor contiene un archivo `service-worker.published.js` sugerido que es responsable de capturar y almacenar archivos de la aplicación para su uso sin conexión. Se trata de un proceso independiente del mecanismo de inicio de la aplicación normal y tiene su propia lógica de comprobación de integridad independiente.

Dentro del archivo `service-worker.published.js`, está presente la línea siguiente:

```javascript
.map(asset => new Request(asset.url, { integrity: asset.hash }));
```

Para deshabilitar la comprobación de la integridad, quite el parámetro `integrity` cambiando la línea por lo siguiente:

```javascript
.map(asset => new Request(asset.url));
```

De nuevo, deshabilitar la comprobación de la integridad significa que se pierden las garantías de seguridad que ofrece este servicio. Por ejemplo, existe el riesgo de que si el explorador del usuario almacena en caché la aplicación en el momento exacto en que implementa una nueva versión, podría almacenar en caché algunos archivos de la implementación anterior y otros de la implementación nueva. Si eso sucede, la aplicación se bloquea en un estado interrumpido hasta que implemente una actualización adicional.
