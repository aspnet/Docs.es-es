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
> <span data-ttu-id="dee01-101">Un parámetro **catch-all** puede relacionar rutas de forma incorrecta debido a un [error](https://github.com/dotnet/aspnetcore/issues/18677) en el enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="dee01-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="dee01-102">Las aplicaciones afectadas por este error tienen las características siguientes:</span><span class="sxs-lookup"><span data-stu-id="dee01-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="dee01-103">Una ruta catch-all (por ejemplo, `{**slug}"`)</span><span class="sxs-lookup"><span data-stu-id="dee01-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="dee01-104">La ruta catch-all causa un error al relacionar solicitudes que sí que debería relacionar.</span><span class="sxs-lookup"><span data-stu-id="dee01-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="dee01-105">Al quitar otras rutas, la ruta catch-all empieza a funcionar.</span><span class="sxs-lookup"><span data-stu-id="dee01-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="dee01-106">Para ver casos de ejemplo relacionados con este error, consulte los errores [18677](https://github.com/dotnet/aspnetcore/issues/18677) y [16579](https://github.com/dotnet/aspnetcore/issues/16579) en GitHub.</span><span class="sxs-lookup"><span data-stu-id="dee01-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="dee01-107">Se incluye una corrección de participación para este error en el [SDK de .NET Core 3.1.301 y versiones posteriores](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="dee01-107">An opt-in fix for this bug is contained in [.NET Core 3.1.301 SDK and later](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="dee01-108">En el código que hay a continuación se establece un cambio interno que corrige este error:</span><span class="sxs-lookup"><span data-stu-id="dee01-108">The following code sets an internal switch that fixes this bug:</span></span>
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