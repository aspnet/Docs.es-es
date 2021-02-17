---
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
ms.openlocfilehash: ca743cdb39423d833902c330f6f0682b600c9833
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551818"
---
::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

> [!WARNING]
> Un parámetro **catch-all** puede relacionar rutas de forma incorrecta debido a un [error](https://github.com/dotnet/aspnetcore/issues/18677) en el enrutamiento. Las aplicaciones afectadas por este error tienen las características siguientes:
>
> * Una ruta catch-all (por ejemplo, `{**slug}"`)
> * La ruta catch-all causa un error al relacionar solicitudes que sí que debería relacionar.
> * Al quitar otras rutas, la ruta catch-all empieza a funcionar.
>
> Para ver casos de ejemplo relacionados con este error, consulte los errores [18677](https://github.com/dotnet/aspnetcore/issues/18677) y [16579](https://github.com/dotnet/aspnetcore/issues/16579) en GitHub.
>
> Se incluye una corrección de participación para este error en el [SDK de .NET Core 3.1.301 y versiones posteriores](https://dotnet.microsoft.com/download/dotnet-core/3.1). En el código que hay a continuación se establece un cambio interno que corrige este error:
>
>```csharp
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```

::: moniker-end