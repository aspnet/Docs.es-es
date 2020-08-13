---
title: Metapaquete Microsoft.AspNetCore.App para ASP.NET Core
author: Rick-Anderson
description: El marco compartido de Microsoft.AspNetCore.App
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/metapackage-app
ms.openlocfilehash: 182d167a1848492846a04c938749a6de30bce377
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017069"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a>Microsoft.AspNetCore.App para ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

 El marco compartido de ASP.NET Core (`Microsoft.AspNetCore.App`) contiene ensamblados desarrollados y admitidos por Microsoft. `Microsoft.AspNetCore.App` se instala cuando se instala el [SDK de .NET Core 3.0 o posterior](https://dotnet.microsoft.com/download/dotnet-core/3.0). El *marco compartido* es el conjunto de ensamblados (archivos *.dll*) que se instalan en la máquina e incluye un componente de entorno de ejecución y un paquete de destino. Para más información, consulte este artículo sobre el [marco de trabajo compartido](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

* Los proyectos que tienen como destino el SDK de `Microsoft.NET.Sdk.Web` hacen referencia implícitamente al marco `Microsoft.AspNetCore.App`.

No se requieren referencias adicionales para estos proyectos:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

El marco compartido de ASP.NET Core:

* No incluye dependencias de terceros.
* Incluye todos los paquetes admitidos por el equipo de ASP.NET Core.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Esta característica requiere ASP.NET Core 2.x con .NET Core 2.x como destino.

El [metapaquete](/dotnet/core/packages#metapackages) [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) para ASP.NET Core:

* No incluye dependencias de terceros excepto [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/) e [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/). Estas dependencias de terceros se consideran necesarias para garantizar el funcionamiento de las características de los principales marcos.
* Incluye todos los paquetes admitidos por el equipo de ASP.NET Core, excepto aquellos que contienen dependencias de terceros (distintos de los mencionados anteriormente).
* Incluye todos los paquetes admitidos por el equipo de Entity Framework Core, excepto aquellos que contienen dependencias de terceros (distintos de los mencionados anteriormente).

Todas las características de ASP.NET Core 2.x y Entity Framework Core 2.x están incluidas en el paquete `Microsoft.AspNetCore.App`. Las plantillas de proyecto predeterminadas que tienen ASP.NET 2 como destino usan este paquete. Se recomienda que las aplicaciones que tengan como destino ASP.NET Core 2.x y Entity Framework Core 2.x usen el paquete `Microsoft.AspNetCore.App`.

El número de versión del metapaquete `Microsoft.AspNetCore.App` representa las versiones mínimas de ASP.NET Core y Entity Framework Core.

Mediante el metapaquete `Microsoft.AspNetCore.App` se proporcionan restricciones de versión que protegen la aplicación:

* Si se incluye un paquete que tiene una dependencia transitiva (no directa) en un paquete en `Microsoft.AspNetCore.App` y los números de versión son distintos, NuGet generará un error.
* Los demás paquetes agregados a la aplicación no pueden cambiar la versión de los paquetes que se incluyen en `Microsoft.AspNetCore.App`.
* La coherencia de versiones garantiza una experiencia fiable. `Microsoft.AspNetCore.App` se ha diseñado para evitar las combinaciones de versiones no probadas de bits relacionados que se usan conjuntamente en la misma aplicación.

Las aplicaciones que usan el metapaquete `Microsoft.AspNetCore.App` pueden aprovechar automáticamente el marco de uso compartido de ASP.NET Core. Al usar el metapaquete `Microsoft.AspNetCore.App`, **ningún** recurso de los paquetes NuGet de ASP.NET Core a los que se hace referencia se implementa con la aplicación: el marco compartido de ASP.NET Core contiene esos recursos. Los recursos del marco de uso compartido se precompilan para mejorar el tiempo de inicio de la aplicación. Para más información, consulte este artículo sobre el [marco de trabajo compartido](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

El archivo de proyecto siguiente hace referencia al metapaquete `Microsoft.AspNetCore.App` de ASP.NET Core y representa una típica plantilla de ASP.NET Core 2.2:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

El marcado anterior representa una plantilla típica de ASP.NET Core 2.x. No especifica ningún número de versión para la referencia del paquete `Microsoft.AspNetCore.App`. Si no se especifica la versión, el SDK define una versión [implícita](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md), es decir, `Microsoft.NET.Sdk.Web`. Es recomendable confiar en la versión implícita especificada por el SDK y no establecer de forma explícita el número de versión en la referencia del paquete. Si tiene alguna pregunta sobre este enfoque, deje un comentario de GitHub en [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430) (Debate sobre la versión implícita Microsoft.AspNetCore.App).

La versión implícita se establece en `major.minor.0` para las aplicaciones portátiles. El mecanismo de puesta al día del marco de uso compartido ejecutará la aplicación en la versión más reciente compatible entre los marcos de uso compartidos instalados. Para garantizar que se use la misma versión en el desarrollo, las pruebas y la producción, asegúrese de que en todos los entornos esté instalada la misma versión del marco de uso compartido. Para las aplicaciones autocontenidas, el número de versión implícita se establece en el valor `major.minor.patch` del marco de uso compartido incluido en el SDK instalado.

El hecho de especificar un número de versión en la referencia de `Microsoft.AspNetCore.App`**no** garantiza que se vaya a elegir la versión del marco de uso compartido. Por ejemplo, suponga que se especifica la versión "2.2.1", pero está instalada la "2.2.3". En ese caso, la aplicación usará el valor "2.2.3". Aunque no se recomienda, puede deshabilitar la puesta al día (revisión o secundaria). Para obtener más información sobre la puesta al día del host de dotnet y cómo configurar su comportamiento, vea [Dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md) (Puesta al día del host de dotnet).

::: moniker-end

::: moniker range="= aspnetcore-2.1"

`<Project Sdk` debe establecerse en `Microsoft.NET.Sdk.Web` para usar la versión implícita `Microsoft.AspNetCore.App`. Cuando `<Project Sdk="Microsoft.NET.Sdk">` (sin la terminación `.Web`) se usa:

* Se genera la siguiente advertencia:

  *Advertencia NU1604: La dependencia de proyecto Microsoft.AspNetCore.App no contiene un límite inferior inclusivo. Incluya un límite inferior en la versión de dependencia para garantizar que los resultados de restauración son coherentes.*

* Se trata de un problema conocido en el SDK de .NET Core 2.1.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a>Actualización de ASP.NET Core

El [metapaquete](/dotnet/core/packages#metapackages) `Microsoft.AspNetCore.App` no es un paquete habitual que se actualice desde NuGet. De forma similar a `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` representa un tiempo de ejecución compartido, con una semántica especial de control de versiones controlada de forma ajena a NuGet. Para obtener más información, vea [Paquetes, metapaquetes y marcos de trabajo](/dotnet/core/packages).

Para actualizar ASP.NET Core:

* En las máquinas de desarrollo y los servidores de compilación: Descargue e instale el [SDK de .NET Core](https://dotnet.microsoft.com/download).
* En los servidores de implementación: Descargue e instale el [entorno de ejecución de .NET Core](https://dotnet.microsoft.com/download).

 Las aplicaciones se pondrán al día con la última versión instalada al reiniciar la aplicación. No es necesario actualizar el número de versión de `Microsoft.AspNetCore.App` en el archivo de proyecto. Para obtener más información, vea [Puesta al día de las aplicaciones dependientes de la plataforma](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).

Si la aplicación ha usado `Microsoft.AspNetCore.All` anteriormente, consulte [Migración desde Microsoft.AspNetCore.All a Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).

::: moniker-end
