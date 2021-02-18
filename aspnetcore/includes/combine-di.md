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
ms.openlocfilehash: 08d212b48c3f97531bea34b11d5b8c9a9069ae34
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536316"
---
<a name="csc"></a>

<span data-ttu-id="255de-101">Tenga en cuenta el siguiente método `ConfigureServices`, que registra los servicios y configura las opciones:</span><span class="sxs-lookup"><span data-stu-id="255de-101">Consider the following `ConfigureServices` method, which registers services and configures options:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

<span data-ttu-id="255de-102">Los grupos de registros relacionados pueden moverse a un método de extensión para registrar los servicios.</span><span class="sxs-lookup"><span data-stu-id="255de-102">Related groups of registrations can be moved to an extension method to register services.</span></span> <span data-ttu-id="255de-103">Por ejemplo, los servicios de configuración se agregan a la siguiente clase:</span><span class="sxs-lookup"><span data-stu-id="255de-103">For example, the configuration services are added to the following class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfigServiceCollectionExtensions.cs)]

<span data-ttu-id="255de-104">Los servicios restantes se registran en una clase similar.</span><span class="sxs-lookup"><span data-stu-id="255de-104">The remaining services are registered in a similar class.</span></span> <span data-ttu-id="255de-105">El siguiente método `ConfigureServices` usa los nuevos métodos de extensión para registrar los servicios:</span><span class="sxs-lookup"><span data-stu-id="255de-105">The following `ConfigureServices` method uses the new extension methods to register the services:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

<span data-ttu-id="255de-106">**_Nota:_** Cada método de extensión `services.Add{GROUP_NAME}` agrega servicios y potencialmente los configura.</span><span class="sxs-lookup"><span data-stu-id="255de-106">**_Note:_** Each `services.Add{GROUP_NAME}` extension method adds and potentially configures services.</span></span> <span data-ttu-id="255de-107">Por ejemplo, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> agrega los controladores MVC de servicios con las vistas que se requieren y <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> agrega los servicios que Razor Pages requiere.</span><span class="sxs-lookup"><span data-stu-id="255de-107">For example, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> adds the services MVC controllers with views require, and <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> adds the services Razor Pages requires.</span></span> <span data-ttu-id="255de-108">Se recomienda que las aplicaciones sigan esta convención de nomenclatura.</span><span class="sxs-lookup"><span data-stu-id="255de-108">We recommended that apps follow this naming convention.</span></span> <span data-ttu-id="255de-109">Coloque los métodos de extensión en el espacio de nombres <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> para encapsular grupos de registros del servicio.</span><span class="sxs-lookup"><span data-stu-id="255de-109">Place extension methods in the <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> namespace to encapsulate groups of service registrations.</span></span>
