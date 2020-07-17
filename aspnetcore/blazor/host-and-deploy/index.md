---
title: Hospedaje e implementación de ASP.NET Core Blazor
author: guardrex
description: Descubra cómo hospedar e implementar aplicaciones de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/index
ms.openlocfilehash: 77202cd60d357c27237cdb925e0adc00e66d2e56
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2020
ms.locfileid: "86407715"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Hospedaje e implementación de ASP.NET Core Blazor

Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) y [Daniel Roth](https://github.com/danroth27)

## <a name="publish-the-app"></a>Publicar la aplicación

Las aplicaciones se publican para implementación en la configuración de versión.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Seleccione **Compilar** > **Publicar {aplicación}** en la barra de navegación.
1. Seleccione el *destino de publicación*. Para publicar localmente, seleccione **Carpeta**.
1. Acepte la ubicación predeterminada del campo **Elegir una carpeta** o especifique una ubicación diferente. Seleccione el botón **`Publish`** .

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Seleccione **Compilar** > **Publicar en carpeta**.
1. Confirme la carpeta para recibir los recursos publicados y seleccione **`Publish`** .

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

Use el comando [`dotnet publish`](/dotnet/core/tools/dotnet-publish) para publicar la aplicación con una configuración de versión:

```dotnetcli
dotnet publish -c Release
```

---

Al publicar la aplicación se desencadena una [restauración](/dotnet/core/tools/dotnet-restore) de las dependencias del proyecto y se [compila](/dotnet/core/tools/dotnet-build) el proyecto antes de crear los recursos para la implementación. Como parte del proceso de compilación, se quitan los ensamblados y métodos que no se usan para reducir los tiempos de carga y el tamaño de descarga de la aplicación.

Ubicaciones de publicación:

* Blazor WebAssembly
  * Independiente: La aplicación se publica en la carpeta `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot`. Para implementar la aplicación como un sitio estático, copie el contenido de la carpeta `wwwroot` en el host del sitio estático.
  * Hospedada: La aplicación cliente de Blazor WebAssembly se publica en la carpeta `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` de la aplicación de servidor, junto con cualquier otro recurso web estático de la aplicación de servidor. Implemente el contenido de la carpeta `publish` en el host.
* Blazor Server: La aplicación se publica en la carpeta `/bin/Release/{TARGET FRAMEWORK}/publish`. Implemente el contenido de la carpeta `publish` en el host.

Los recursos de la carpeta se implementan en el servidor web. La implementación puede ser un proceso manual o automatizado, en función de las herramientas de desarrollo que se usen.

## <a name="app-base-path"></a>Ruta de acceso base de la aplicación

La *ruta de acceso base de la aplicación* es la de la dirección URL raíz de la aplicación. Tenga en cuenta la siguiente aplicación de ASP.NET Core y la subaplicación de Blazor:

* La aplicación de ASP.NET Core se denomina `MyApp`:
  * La aplicación reside físicamente en `d:/MyApp`.
  * Las solicitudes se reciben en `https://www.contoso.com/{MYAPP RESOURCE}`.
* Una aplicación de Blazor denominada `CoolApp` es una subaplicación de `MyApp`:
  * La aplicación reside físicamente en `d:/MyApp/CoolApp`.
  * Las solicitudes se reciben en `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.

Sin especificar una configuración adicional para `CoolApp`, la subaplicación de este escenario desconoce dónde reside en el servidor. Por ejemplo, la aplicación no puede construir URL relativas correctas para sus recursos sin saber que reside en la ruta de acceso URL relativa `/CoolApp/`.

Para proporcionar la configuración de la ruta de acceso base de la aplicación de Blazor de `https://www.contoso.com/CoolApp/`, el atributo `<base>` de la etiqueta `href` se establece en la ruta de acceso raíz relativa del archivo `Pages/_Host.cshtml` (Blazor Server) o del archivo `wwwroot/index.html` (Blazor WebAssembly):

```html
<base href="/CoolApp/">
```

Las aplicaciones de Blazor Server establecen además la ruta de acceso base del servidor mediante una llamada a <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> en la canalización de solicitudes de la aplicación de `Startup.Configure`:

```csharp
app.UsePathBase("/CoolApp");
```

Al proporcionar la ruta de acceso URL relativa, un componente que no se encuentre en el directorio raíz puede construir direcciones URL relativas a la ruta de acceso raíz de la aplicación. Los componentes que se encuentran en diferentes niveles de la estructura de directorios pueden compilar vínculos a otros recursos en ubicaciones de toda la aplicación. La ruta de acceso base de la aplicación también se usa para interceptar hipervínculos seleccionados en los que el destino `href` del vínculo está dentro del espacio de URI de la ruta de acceso base de la aplicación. El enrutador de Blazor controla la navegación interna.

En muchos escenarios de hospedaje, la ruta de acceso URL relativa a la aplicación es la raíz de la aplicación. En estos casos, la ruta de acceso base URL relativa de la aplicación es una barra diagonal (`<base href="/" />`), que es la configuración predeterminada para una aplicación de Blazor. En otros escenarios de hospedaje, como las subaplicaciones de IIS y GitHub Pages, la ruta de acceso base de la aplicación debe establecerse en la ruta de acceso URL relativa del servidor de la aplicación.

Para establecer la ruta de acceso base de la aplicación, actualice la etiqueta `<base>` dentro de los elementos de etiqueta `<head>` del archivo `Pages/_Host.cshtml` (Blazor Server) o del archivo `wwwroot/index.html` (Blazor WebAssembly). Establezca el valor del atributo `href` en `/{RELATIVE URL PATH}/` (la barra diagonal final es necesaria), donde `{RELATIVE URL PATH}` es la ruta de acceso URL relativa completa de la aplicación.

En el caso de una aplicación de Blazor WebAssembly con una ruta de acceso URL relativa que no sea raíz (por ejemplo, `<base href="/CoolApp/">`), la aplicación no puede encontrar sus recursos *cuando se ejecuta de forma local*. Para solucionar este problema durante la fase de desarrollo y pruebas local, puede proporcionar un argumento de *ruta de acceso base* que coincida con el valor `href` de la etiqueta `<base>` en tiempo de ejecución. No incluya una barra diagonal final. Para pasar el argumento de ruta de acceso base al ejecutar la aplicación de forma local, ejecute el comando `dotnet run` desde el directorio de la aplicación con la opción `--pathbase`:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

En el caso de una aplicación de Blazor WebAssembly con una ruta de acceso a una URL relativa de `/CoolApp/` (`<base href="/CoolApp/">`), el comando es el siguiente:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

La aplicación de Blazor WebAssembly responde de forma local en `http://localhost:port/CoolApp`.

**Configuración `MapFallbackToPage` de Blazor Server**

Pase la siguiente ruta de acceso a <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> en `Startup.Configure`:

```csharp
endpoints.MapFallbackToPage("/{RELATIVE PATH}/{**path:nonfile}");
```

El marcador de posición `{RELATIVE PATH}` es la ruta de acceso no raíz en el servidor. Por ejemplo, `CoolApp` es el segmento del marcador de posición si la dirección URL no raíz a la aplicación es `https://{HOST}:{PORT}/CoolApp/`:

```csharp
endpoints.MapFallbackToPage("/CoolApp/{**path:nonfile}");
```

## <a name="deployment"></a>Implementación

Para una guía sobre la implementación, consulte los temas siguientes:

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
