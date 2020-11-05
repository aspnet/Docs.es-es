---
title: Vínculo con exploradores en ASP.NET Core
author: ncarandini
description: En este artículo se explica que Vínculo con exploradores es una característica de Visual Studio que vincula el entorno de desarrollo con uno o más exploradores web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
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
uid: client-side/using-browserlink
ms.openlocfilehash: 80f05acab55af973faf08b5db79ea4cbaf896b14
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054494"
---
# <a name="browser-link-in-aspnet-core"></a>Vínculo con exploradores en ASP.NET Core

Por [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson) y [Tom Dykstra](https://github.com/tdykstra)

Vínculo con exploradores es una característica de Visual Studio. Crea un canal de comunicación entre el entorno de desarrollo y uno o varios exploradores web. Puede usar Vínculo con exploradores para actualizar su aplicación web en varios exploradores a la vez, lo que resulta útil a la hora de hacer pruebas entre exploradores.

## <a name="browser-link-setup"></a>Configuración de Vínculo con exploradores

::: moniker range=">= aspnetcore-3.0"

Agregue el paquete [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) a su proyecto. En los proyectos de Razor Pages o MVC de ASP.NET Core, habilite también la compilación en tiempo de ejecución de los archivos de Razor ( *.cshtml* ), como se explica en <xref:mvc/views/view-compilation>. Los cambios en la sintaxis de Razor solo se aplican cuando está habilitada la compilación en tiempo de ejecución.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Al convertir un proyecto de ASP.NET Core 2.0 en ASP.NET Core 2.1 y pasar al [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), instale el paquete [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) para la funcionalidad de Vínculo con exploradores. De forma predeterminada, las plantillas de proyecto de ASP.NET Core 2.1 usan el metapaquete `Microsoft.AspNetCore.App`.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Las plantillas de proyecto **vacío** , **de aplicación web** y **API web** de ASP.NET Core 2.0 usan el [metapaquete Microsoft.AspNetCore.All](xref:fundamentals/metapackage), que contiene una referencia de paquete a [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/). Por lo tanto, el uso del metapaquete `Microsoft.AspNetCore.All` no requiere ninguna acción adicional para que Vínculo con exploradores esté disponible para su uso.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

La plantilla de proyecto de **aplicación web** de ASP.NET Core 1.x tiene una referencia de paquete al paquete [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/). Otros tipos de proyectos requieren que agregue una referencia de paquete a `Microsoft.VisualStudio.Web.BrowserLink`.

::: moniker-end

### <a name="configuration"></a>Configuración

Llame a `UseBrowserLink` en el método `Startup.Configure`:

```csharp
app.UseBrowserLink();
```

Normalmente, la llamada a `UseBrowserLink` se coloca dentro de un bloque `if` que solo habilita Vínculo con exploradores en el entorno de desarrollo. Por ejemplo:

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

Para obtener más información, vea <xref:fundamentals/environments>.

## <a name="how-to-use-browser-link"></a>Uso de Vínculo con exploradores

Si tiene un proyecto de ASP.NET Core abierto, Visual Studio muestra el control de la barra de herramientas de Vínculo con exploradores junto al de **destino de depuración** :

![Menú desplegable de Vínculo con exploradores](using-browserlink/_static/browserLink-dropdown-menu.png)

Desde el control de la barra de herramientas de Vínculo con exploradores, puede realizar las siguientes acciones:

* Actualizar la aplicación web en varios exploradores a la vez.
* Abrir el **panel de Vínculo con exploradores**.
* Habilitar o deshabilitar **Vínculo con exploradores**. Nota: Vínculo con exploradores está deshabilitado de forma predeterminada en Visual Studio.
* Habilitar o deshabilitar la [sincronización automática de CSS](#enable-or-disable-css-auto-sync).

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>Actualización de la aplicación web en varios exploradores a la vez

Para elegir un único explorador web que se inicie al abrir el proyecto, use el menú desplegable del control de la barra de herramientas de **destino de depuración** :

![Menú desplegable F5](using-browserlink/_static/debug-target-dropdown-menu.png)

Para abrir varios exploradores a la vez, elija **Examinar con…** en el mismo menú desplegable. Mantenga presionada la tecla <kbd>Ctrl</kbd> para seleccionar los exploradores que quiera y, a continuación, haga clic en **Examinar** :

![Apertura de muchos exploradores a la vez](using-browserlink/_static/open-many-browsers-at-once.png)

En la siguiente captura de pantalla se muestran Visual Studio en la vista de índice y dos exploradores abiertos:

![Ejemplo de sincronización con dos exploradores](using-browserlink/_static/sync-with-two-browsers-example.png)

Pase el mouse por encima del control de la barra de herramientas de Vínculo con exploradores para ver los exploradores que están conectados al proyecto:

![Sugerencia que se muestra al pasar el mouse](using-browserlink/_static/hoover-tip.png)

Cambie la vista de índice; todos los exploradores conectados se actualizarán cuando haga clic en el botón de actualización de Vínculo con exploradores:

![browsers-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

La característica Vínculo con exploradores también funciona con los exploradores que se inician desde fuera de Visual Studio y navegan a la dirección URL de la aplicación.

### <a name="the-browser-link-dashboard"></a>Panel de Vínculo con exploradores

Abra la ventana **Panel de Vínculo con exploradores** desde el menú desplegable de la característica para administrar la conexión con los exploradores abiertos:

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

Si no hay ningún explorador conectado, puede iniciar una sesión que no sea de depuración seleccionando el vínculo **Ver en el explorador** :

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

De lo contrario, los exploradores conectados aparecen enumerados con la ruta de acceso a la página que se está mostrando en cada explorador:

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

También puede hacer clic en un nombre de explorador para actualizar solo ese explorador.

### <a name="enable-or-disable-browser-link"></a>Habilitación o deshabilitación de Vínculo con exploradores

Si vuelve a habilitar Vínculo con exploradores después de deshabilitarlo, debe actualizar los exploradores para conectarlos de nuevo.

### <a name="enable-or-disable-css-auto-sync"></a>Habilitación o deshabilitación de la sincronización automática de CSS

Si se habilita la sincronización automática de CSS, los exploradores conectados se actualizarán automáticamente al realizar cualquier cambio en los archivos CSS.

## <a name="how-it-works"></a>Funcionamiento

Vínculo con exploradores usa [SignalR](xref:signalr/introduction) para crear un canal de comunicación entre Visual Studio y el explorador. Si Vínculo con exploradores está habilitado, Visual Studio actuará como un servidor de SignalR al que se pueden conectar varios clientes (exploradores). Vínculo con exploradores también registra un componente de middleware en la canalización de solicitudes de ASP.NET Core. Este componente inserta referencias especiales de `<script>` en cada solicitud de página del servidor. Para ver las referencias de script, seleccione **Ver código fuente** en el explorador y desplácese hasta el final del contenido de la etiqueta `<body>`:

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

Los archivos de código fuente no se modifican. El componente de middleware inserta las referencias de script dinámicamente.

Dado que el código del explorador es JavaScript, funciona en todos los exploradores que admite SignalR sin requerir un complemento de explorador.
