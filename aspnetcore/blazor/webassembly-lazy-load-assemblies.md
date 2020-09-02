---
title: Ensamblados de carga diferida en Blazor WebAssembly de ASP.NET Core
author: guardrex
description: Descubra cómo cargar ensamblados de forma diferida en aplicaciones de Blazor WebAssembly de ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/25/2020
no-loc:
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
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: 46f98080ad40f614f9cb1af2190f263d205c1016
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865159"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a>Ensamblados de carga diferida en Blazor WebAssembly de ASP.NET Core

Por [Safia Abdalla](https://safia.rocks) y [Luke Latham](https://github.com/guardrex)

El rendimiento de inicio de la aplicación Blazor WebAssembly se puede mejorar aplazando la carga de algunos ensamblados de la aplicación hasta que sean necesarios, lo que se denomina *carga diferida*. Por ejemplo, los ensamblados que solo se usan para representar un único componente se pueden configurar para que se carguen solo si el usuario navega a ese componente. Después de la carga, los ensamblados se almacenan en la memoria caché del lado cliente y están disponibles para todas las navegaciones futuras.

La característica de carga diferida de Blazor permite marcar los ensamblados de la aplicación para la carga diferida, que carga los ensamblados durante el tiempo de ejecución cuando el usuario navega a una ruta determinada. La característica consiste en cambios en el archivo del proyecto y en el enrutador de la aplicación.

> [!NOTE]
> La carga diferida de ensamblados no se beneficia de las aplicaciones de Blazor Server porque los ensamblados no se descargan en el cliente en una aplicación Blazor Server.

## <a name="project-file"></a>Archivo del proyecto

Marque los ensamblados para la carga diferida en el archivo del proyecto de la aplicación (`.csproj`) mediante el elemento `BlazorWebAssemblyLazyLoad`. Use el nombre de ensamblado sin la extensión `.dll`. El marco de trabajo de Blazor impide que los ensamblados especificados por este grupo de elementos se carguen al iniciar la aplicación. En el ejemplo siguiente se marca un ensamblado personalizado grande (`GrantImaharaRobotControls.dll`) para la carga diferida. Si un ensamblado que está marcado para la carga diferida tiene dependencias, también se deben marcar para la carga diferida en el archivo del proyecto.

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls" />
</ItemGroup>
```

Solo se pueden cargar de forma diferida los ensamblados utilizados por la aplicación. El enlazador quita los ensamblados que no se usan de la salida publicada.

> [!NOTE]
> En la versión candidata para lanzamiento 1 (RC1) de .NET 5 o una posterior, que se publicará a mediados de septiembre, el nombre de ensamblado requerirá la extensión `.dll`:
>
> ```xml
> <ItemGroup>
>  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls.dll" />
> </ItemGroup>
> ```

## <a name="router-component"></a>Componente de `Router`

El componente `Router` de Blazor designa qué ensamblados busca Blazor para los componentes enrutables. El componente `Router` también es responsable de representar el componente para la ruta donde navega el usuario. El componente `Router` admite una característica `OnNavigateAsync` que se puede usar junto con la carga diferida.

En el componente `Router` de la aplicación (`App.razor`):

* Agregue una devolución de llamada `OnNavigateAsync`. El controlador `OnNavigateAsync` se invoca cuando el usuario:
  * Visita una ruta por primera vez desplazándose hasta ella directamente desde el explorador.
  * Navega a una nueva ruta mediante un vínculo o una invocación de <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType>.
* Si los ensamblados de carga diferida contienen componentes enrutables, agregue un elemento [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (por ejemplo, con el nombre `lazyLoadedAssemblies`) al componente. Los ensamblados se devuelven a la colección <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> en caso de que los ensamblados contengan componentes enrutables. El marco de trabajo busca las rutas en los ensamblados y actualiza la colección de rutas si se encuentran nuevas rutas.

```razor
@using System.Reflection

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
    }
}
```

Si la devolución de llamada de `OnNavigateAsync` produce una excepción no controlada, se invoca la [interfaz de usuario de error Blazor](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development).

### <a name="assembly-load-logic-in-onnavigateasync"></a>Lógica de carga de ensamblado en `OnNavigateAsync`

`OnNavigateAsync` tiene un parámetro `NavigationContext` que proporciona información sobre el evento de navegación asincrónico actual, incluida la ruta de acceso de destino (`Path`) y el token de cancelación (`CancellationToken`):

* La propiedad `Path` es la ruta de acceso de destino del usuario en relación con la ruta de acceso base de la aplicación, como `/robot`.
* `CancellationToken` se puede usar para observar la cancelación de la tarea asincrónica. `OnNavigateAsync` cancela automáticamente la tarea de navegación actualmente en ejecución cuando el usuario navega a una página diferente.

Dentro de `OnNavigateAsync`, implemente la lógica para determinar los ensamblados que se van a cargar. Las opciones incluyen:

* Comprobaciones condicionales dentro del método `OnNavigateAsync`.
* Tabla de búsqueda que asigna rutas a los nombres de ensamblado, insertados en el componente o implementados en el bloque [`@code`](xref:mvc/views/razor#code).

`LazyAssemblyLoader` es un servicio singleton proporcionado por el marco de trabajo para cargar ensamblados. Inserte `LazyAssemblyLoader` en el componente `Router`:

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

`LazyAssemblyLoader` proporciona el método `LoadAssembliesAsync` que:

* Usa la interoperabilidad de JS para capturar ensamblados a través de una llamada de red.
* Carga los ensamblados en el runtime que se ejecuta en WebAssembly en el explorador.

> [!NOTE]
> La implementación de la carga diferida del marco de trabajo admite la representación previa en el servidor. Durante la representación previa, se supone que se cargan todos los ensamblados, incluidos los marcados para la carga diferida.

### <a name="user-interaction-with-navigating-content"></a>Interacción del usuario con el contenido de `<Navigating>`

Mientras se cargan los ensamblados, que pueden tardar varios segundos, el componente `Router` puede indicar al usuario que se está produciendo una transición de página:

* Agregue una directiva [`@using`](xref:mvc/views/razor#using) para el espacio de nombres <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName>.
* Agregue una etiqueta `<Navigating>` al componente con el marcado que se va a mostrar durante los eventos de transición de la página.

```razor
...
@using Microsoft.AspNetCore.Components.Routing
...

<Router ...>
    <Navigating>
        <div style="...">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
</Router>

...
```

### <a name="handle-cancellations-in-onnavigateasync"></a>Control de las cancelaciones en `OnNavigateAsync`

El objeto `NavigationContext` pasado a la devolución de llamada de `OnNavigateAsync` contiene un elemento `CancellationToken` que se establece cuando se produce un nuevo evento de navegación. La devolución de llamada de `OnNavigateAsync` debe iniciarse cuando se establece este token de cancelación para evitar que continúe la ejecución de la devolución de llamada de `OnNavigateAsync` en una navegación no actualizada.

Si un usuario navega a la ruta A e inmediatamente después a la ruta B, la aplicación no debe seguir ejecutando la devolución de llamada de `OnNavigateAsync` para la ruta A:

```razor
@inject HttpClient Http
@inject ProductCatalog Products

<Router AppAssembly="@typeof(Program).Assembly" 
    OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context)
    {
        if (context.Path == "/about") 
        {
            var stats = new Stats = { Page = "/about" };
            await Http.PostAsJsonAsync("api/visited", stats, context.CancellationToken);
        }
        else if (context.Path == "/store")
        {
            var productIds = [345, 789, 135, 689];

            foreach (var productId in productIds) 
            {
                context.CancellationToken.ThrowIfCancellationRequested();
                Products.Prefetch(productId);
            }
        }
    }
}
```

> [!NOTE]
> Cuando no se ejecuta si el token de cancelación de `NavigationContext` se cancela, puede resultar en un comportamiento imprevisto, como la representación de un componente de una navegación anterior.

### <a name="onnavigateasync-events-and-renamed-assembly-files"></a>Eventos y archivos de ensamblado con nombre cambiado `OnNavigateAsync`

El cargador de recursos se basa en los nombres de ensamblado que se definen en el archivo `blazor.boot.json`. Si se [cambia el nombre de los ensamblados](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), los nombres de ensamblado utilizados en los métodos `OnNavigateAsync` y los nombres de ensamblado del archivo `blazor.boot.json` no estarán sincronizados.

Para rectificar esto, haga lo siguiente:

* Compruebe si la aplicación se ejecuta en el entorno de producción al determinar los nombres de ensamblado que se van a usar.
* Almacene los nombres de ensamblado con el nombre cambiado en un archivo independiente y lea desde ese archivo para determinar el nombre de ensamblado que se va a usar en los métodos `LazyLoadAssemblyService` y `OnNavigateAsync`.

### <a name="complete-example"></a>Ejemplo completo

El siguiente componente `Router` completo muestra cómo cargar el ensamblado `GrantImaharaRobotControls.dll` cuando el usuario navega a `/robot`. Durante las transiciones de página, se muestra al usuario un mensaje con estilo.

```razor
@using System.Reflection
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    <Navigating>
        <div style="padding:20px;background-color:blue;color:white">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("/robot"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(
                    new List<string>() { "GrantImaharaRobotControls.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
            }
        }
        catch (Exception ex)
        {
            ...
        }
    }
}
```

## <a name="troubleshoot"></a>Solucionar problemas

* Si se produce una representación inesperada (por ejemplo, se representa un componente de una navegación anterior), confirme que el código se ejecuta si se establece el token de cancelación.
* Si los ensamblados continúan cargándose cuando se inicia la aplicación, compruebe que el ensamblado está marcado como cargado de forma diferida en el archivo del proyecto.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:blazor/webassembly-performance-best-practices>
