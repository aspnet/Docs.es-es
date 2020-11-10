---
title: Enrutar a acciones de controlador de ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo ASP.NET Core MVC usa el middleware de enrutamiento para encontrar direcciones URL de las solicitudes entrantes y asignarlas a acciones.
ms.author: riande
ms.date: 3/25/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/controllers/routing
ms.openlocfilehash: 59ad373cefaa12370aa7c02a367125c7a94f59a6
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422605"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="7ba60-103">Enrutar a acciones de controlador de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ba60-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="7ba60-104">Por [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5) y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7ba60-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7ba60-105">Los controladores de ASP.NET Core usan el [middleware](xref:fundamentals/middleware/index) de enrutamiento para hacer coincidir las direcciones URL de las solicitudes entrantes y asignarlas a [las acciones](#action).</span><span class="sxs-lookup"><span data-stu-id="7ba60-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="7ba60-106">Plantillas de rutas:</span><span class="sxs-lookup"><span data-stu-id="7ba60-106">Routes templates:</span></span>

* <span data-ttu-id="7ba60-107">Se definen en el código de inicio o en los atributos.</span><span class="sxs-lookup"><span data-stu-id="7ba60-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="7ba60-108">Describa cómo las rutas de dirección URL coinciden con [las acciones](#action).</span><span class="sxs-lookup"><span data-stu-id="7ba60-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="7ba60-109">Se usan para generar direcciones URL para los vínculos.</span><span class="sxs-lookup"><span data-stu-id="7ba60-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="7ba60-110">Los vínculos generados se devuelven normalmente en las respuestas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="7ba60-111">Las acciones se [enrutan convencionalmente](#cr) o se [enrutan mediante atributos](#ar).</span><span class="sxs-lookup"><span data-stu-id="7ba60-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="7ba60-112">La colocación de una ruta en el controlador o la [acción](#action) hace que se enrute el atributo.</span><span class="sxs-lookup"><span data-stu-id="7ba60-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="7ba60-113">Consulte [Enrutamiento mixto](#routing-mixed-ref-label) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="7ba60-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="7ba60-114">Este documento:</span><span class="sxs-lookup"><span data-stu-id="7ba60-114">This document:</span></span>

* <span data-ttu-id="7ba60-115">Explica las interacciones entre MVC y el enrutamiento:</span><span class="sxs-lookup"><span data-stu-id="7ba60-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="7ba60-116">La forma en que las aplicaciones MVC típicas usan las características de enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="7ba60-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="7ba60-117">Abarca ambos:</span><span class="sxs-lookup"><span data-stu-id="7ba60-117">Covers both:</span></span>
    * <span data-ttu-id="7ba60-118">El [enrutamiento convencional](#cr) normalmente se usa con controladores y vistas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-118">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="7ba60-119">*Enrutamiento de atributos* usado con las API de REST.</span><span class="sxs-lookup"><span data-stu-id="7ba60-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="7ba60-120">Si está interesado principalmente en el enrutamiento de las API de REST, vaya a la sección [enrutamiento de atributo para las API de REST](#ar) .</span><span class="sxs-lookup"><span data-stu-id="7ba60-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="7ba60-121">Consulte [enrutamiento](xref:fundamentals/routing) para obtener detalles de enrutamiento avanzados.</span><span class="sxs-lookup"><span data-stu-id="7ba60-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="7ba60-122">Hace referencia al sistema de enrutamiento predeterminado agregado en ASP.NET Core 3,0, denominado enrutamiento de punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="7ba60-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="7ba60-123">Es posible usar controladores con la versión anterior de enrutamiento para fines de compatibilidad.</span><span class="sxs-lookup"><span data-stu-id="7ba60-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="7ba60-124">Consulte la [Guía de migración 2.2-3.0](xref:migration/22-to-30) para obtener instrucciones.</span><span class="sxs-lookup"><span data-stu-id="7ba60-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="7ba60-125">Consulte la [versión 2,2 de este documento](xref:mvc/controllers/routing?view=aspnetcore-2.2) para obtener material de referencia sobre el sistema de enrutamiento heredado.</span><span class="sxs-lookup"><span data-stu-id="7ba60-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="7ba60-126">Configuración de la ruta convencional</span><span class="sxs-lookup"><span data-stu-id="7ba60-126">Set up conventional route</span></span>

<span data-ttu-id="7ba60-127">`Startup.Configure` normalmente tiene código similar al siguiente al usar el [enrutamiento convencional](#crd):</span><span class="sxs-lookup"><span data-stu-id="7ba60-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="7ba60-128">Dentro de la llamada a <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> se usa para crear una única ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> is used to create a single route.</span></span> <span data-ttu-id="7ba60-129">La ruta única se denomina `default` Route.</span><span class="sxs-lookup"><span data-stu-id="7ba60-129">The single route is named `default` route.</span></span> <span data-ttu-id="7ba60-130">La mayoría de las aplicaciones con controladores y vistas usan una plantilla de ruta similar a la `default` ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="7ba60-131">Las API de REST deben usar el [enrutamiento de atributos](#ar).</span><span class="sxs-lookup"><span data-stu-id="7ba60-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="7ba60-132">La plantilla de ruta `"{controller=Home}/{action=Index}/{id?}"` :</span><span class="sxs-lookup"><span data-stu-id="7ba60-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="7ba60-133">Coincide con una ruta de dirección URL como `/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="7ba60-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="7ba60-134">Extrae los valores de ruta `{ controller = Products, action = Details, id = 5 }` mediante el token de la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="7ba60-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="7ba60-135">La extracción de valores de ruta da como resultado una coincidencia si la aplicación tiene un controlador denominado `ProductsController` y una `Details` acción:</span><span class="sxs-lookup"><span data-stu-id="7ba60-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="7ba60-136">`/Products/Details/5` el modelo enlaza el valor de `id = 5` para establecer el `id` parámetro en `5` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-136">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="7ba60-137">Vea [enlace de modelos](xref:mvc/models/model-binding) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="7ba60-137">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="7ba60-138">`{controller=Home}` define `Home` como valor predeterminado `controller` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-138">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="7ba60-139">`{action=Index}` define `Index` como valor predeterminado `action` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-139">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="7ba60-140">El `?` carácter de `{id?}` define `id` como opcional.</span><span class="sxs-lookup"><span data-stu-id="7ba60-140">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="7ba60-141">No es necesario que los parámetros de ruta opcionales y predeterminados estén presentes en la ruta de dirección URL para una coincidencia.</span><span class="sxs-lookup"><span data-stu-id="7ba60-141">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="7ba60-142">Consulte [Referencia de plantilla de ruta](xref:fundamentals/routing#route-template-reference) para obtener una descripción detallada de la sintaxis de la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-142">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="7ba60-143">Coincide con la ruta de acceso de la dirección URL `/` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-143">Matches the URL path `/`.</span></span>
* <span data-ttu-id="7ba60-144">Genera los valores de ruta `{ controller = Home, action = Index }` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-144">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="7ba60-145">Los valores de `controller` y `action` usan los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="7ba60-145">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="7ba60-146">`id` no genera un valor, ya que no hay ningún segmento correspondiente en la ruta de acceso de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-146">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="7ba60-147">`/` solo coincide si existe una `HomeController` acción and `Index` :</span><span class="sxs-lookup"><span data-stu-id="7ba60-147">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="7ba60-148">Con la definición de controlador y la plantilla de ruta anteriores, la `HomeController.Index` acción se ejecuta para las siguientes rutas de acceso URL:</span><span class="sxs-lookup"><span data-stu-id="7ba60-148">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="7ba60-149">La ruta de dirección URL `/` utiliza la acción y los controladores predeterminados de la plantilla de ruta `Home` `Index` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-149">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="7ba60-150">La ruta de dirección URL `/Home` utiliza la acción predeterminada de la plantilla de ruta `Index` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-150">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="7ba60-151">El método de conveniencia <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>:</span><span class="sxs-lookup"><span data-stu-id="7ba60-151">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="7ba60-152">Reemplazo</span><span class="sxs-lookup"><span data-stu-id="7ba60-152">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="7ba60-153">El enrutamiento se configura mediante <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> el <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> middleware de y.</span><span class="sxs-lookup"><span data-stu-id="7ba60-153">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> middleware.</span></span> <span data-ttu-id="7ba60-154">Para usar controladores:</span><span class="sxs-lookup"><span data-stu-id="7ba60-154">To use controllers:</span></span>
>
> * <span data-ttu-id="7ba60-155">Llame <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> `UseEndpoints` a dentro de para asignar los controladores [enrutados de atributo](#ar) .</span><span class="sxs-lookup"><span data-stu-id="7ba60-155">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="7ba60-156">Llame <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> a o a para asignar controladores enrutados por [Convención](#cr) y controladores [enrutados por atributo](#ar) .</span><span class="sxs-lookup"><span data-stu-id="7ba60-156">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A>, to map both [conventionally routed](#cr) controllers and [attribute routed](#ar) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="7ba60-157">Enrutamiento convencional</span><span class="sxs-lookup"><span data-stu-id="7ba60-157">Conventional routing</span></span>

<span data-ttu-id="7ba60-158">El enrutamiento convencional se usa con controladores y vistas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-158">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="7ba60-159">La ruta `default`:</span><span class="sxs-lookup"><span data-stu-id="7ba60-159">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="7ba60-160">es un ejemplo de un *enrutamiento convencional*.</span><span class="sxs-lookup"><span data-stu-id="7ba60-160">is an example of a *conventional routing*.</span></span> <span data-ttu-id="7ba60-161">Se denomina *enrutamiento convencional* porque establece una *Convención* para las rutas de dirección URL:</span><span class="sxs-lookup"><span data-stu-id="7ba60-161">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="7ba60-162">El primer segmento de la ruta de acceso, `{controller=Home}` , se asigna al nombre del controlador.</span><span class="sxs-lookup"><span data-stu-id="7ba60-162">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="7ba60-163">El segundo segmento, `{action=Index}` , se asigna al nombre de la [acción](#action) .</span><span class="sxs-lookup"><span data-stu-id="7ba60-163">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="7ba60-164">El tercer segmento, `{id?}` se usa para un opcional `id` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-164">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="7ba60-165">`?`En `{id?}` hace que sea opcional.</span><span class="sxs-lookup"><span data-stu-id="7ba60-165">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="7ba60-166">`id` se utiliza para asignar a una entidad del modelo.</span><span class="sxs-lookup"><span data-stu-id="7ba60-166">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="7ba60-167">Con esta `default` ruta, la ruta de acceso URL:</span><span class="sxs-lookup"><span data-stu-id="7ba60-167">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="7ba60-168">`/Products/List` asigna a la `ProductsController.List` acción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-168">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="7ba60-169">`/Blog/Article/17` asigna a `BlogController.Article` y normalmente el modelo enlaza el `id` parámetro a 17.</span><span class="sxs-lookup"><span data-stu-id="7ba60-169">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="7ba60-170">Esta asignación:</span><span class="sxs-lookup"><span data-stu-id="7ba60-170">This mapping:</span></span>

* <span data-ttu-id="7ba60-171">**Solo** se basa en los nombres de [acción](#action) y controlador.</span><span class="sxs-lookup"><span data-stu-id="7ba60-171">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="7ba60-172">No se basa en espacios de nombres, ubicaciones de archivos de código fuente o parámetros de método.</span><span class="sxs-lookup"><span data-stu-id="7ba60-172">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="7ba60-173">El uso del enrutamiento convencional con la ruta predeterminada permite crear la aplicación sin tener que presentar un nuevo patrón de dirección URL para cada acción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-173">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="7ba60-174">Para una aplicación con acciones de estilo [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) , que tiene coherencia para las direcciones URL entre controladores:</span><span class="sxs-lookup"><span data-stu-id="7ba60-174">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="7ba60-175">Ayuda a simplificar el código.</span><span class="sxs-lookup"><span data-stu-id="7ba60-175">Helps simplify the code.</span></span>
* <span data-ttu-id="7ba60-176">Hace que la interfaz de usuario sea más predecible.</span><span class="sxs-lookup"><span data-stu-id="7ba60-176">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="7ba60-177">La `id` plantilla de ruta define el en el código anterior como opcional.</span><span class="sxs-lookup"><span data-stu-id="7ba60-177">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="7ba60-178">Las acciones se pueden ejecutar sin el identificador opcional proporcionado como parte de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-178">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="7ba60-179">Generalmente, cuando `id` se omite en la dirección URL:</span><span class="sxs-lookup"><span data-stu-id="7ba60-179">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="7ba60-180">`id` está establecido en `0` por enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="7ba60-180">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="7ba60-181">No se encontró ninguna entidad en la base de datos que coincida con `id == 0` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-181">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="7ba60-182">El [enrutamiento de atributo](#ar) proporciona un control específico para que el identificador sea necesario para algunas acciones y no para otros.</span><span class="sxs-lookup"><span data-stu-id="7ba60-182">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="7ba60-183">Por Convención, la documentación incluye parámetros opcionales como `id` cuando es probable que aparezcan en el uso correcto.</span><span class="sxs-lookup"><span data-stu-id="7ba60-183">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="7ba60-184">La mayoría de las aplicaciones deben elegir un esquema de enrutamiento básico y descriptivo para que las direcciones URL sean legibles y significativas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-184">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="7ba60-185">La ruta convencional predeterminada `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="7ba60-185">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="7ba60-186">Admite un esquema de enrutamiento básico y descriptivo.</span><span class="sxs-lookup"><span data-stu-id="7ba60-186">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="7ba60-187">Se trata de un punto de partida útil para las aplicaciones basadas en la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="7ba60-187">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="7ba60-188">Es la única plantilla de ruta necesaria para muchas aplicaciones de interfaz de usuario Web.</span><span class="sxs-lookup"><span data-stu-id="7ba60-188">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="7ba60-189">En el caso de las aplicaciones de interfaz de usuario Web más grandes, otra ruta que usa [áreas](#areas) suele ser necesaria.</span><span class="sxs-lookup"><span data-stu-id="7ba60-189">For larger web UI apps, another route using [Areas](#areas) is frequently all that's needed.</span></span>

<span data-ttu-id="7ba60-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> y <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> :</span><span class="sxs-lookup"><span data-stu-id="7ba60-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> :</span></span>

* <span data-ttu-id="7ba60-191">Asigne automáticamente un valor de **pedido** a sus puntos de conexión en función del orden en que se invocan.</span><span class="sxs-lookup"><span data-stu-id="7ba60-191">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="7ba60-192">Enrutamiento de puntos de conexión en ASP.NET Core 3.0 y versiones posteriores:</span><span class="sxs-lookup"><span data-stu-id="7ba60-192">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="7ba60-193">No tiene un concepto de rutas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-193">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="7ba60-194">No proporciona garantías de ordenación para la ejecución de la extensibilidad, todos los puntos de conexión se procesan a la vez.</span><span class="sxs-lookup"><span data-stu-id="7ba60-194">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="7ba60-195">Habilite el [registro](xref:fundamentals/logging/index) para ver de qué forma las implementaciones de enrutamiento integradas, como <xref:Microsoft.AspNetCore.Routing.Route>, coinciden con las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="7ba60-195">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="7ba60-196">El [enrutamiento de atributos](#ar) se explica más adelante en este documento.</span><span class="sxs-lookup"><span data-stu-id="7ba60-196">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="7ba60-197">Varias rutas convencionales</span><span class="sxs-lookup"><span data-stu-id="7ba60-197">Multiple conventional routes</span></span>

<span data-ttu-id="7ba60-198">Se pueden agregar varias [rutas convencionales](#cr) en agregando `UseEndpoints` más llamadas a <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> y <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> .</span><span class="sxs-lookup"><span data-stu-id="7ba60-198">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A>.</span></span> <span data-ttu-id="7ba60-199">Esto permite definir varias convenciones o agregar rutas convencionales dedicadas a una [acción](#action)específica, como:</span><span class="sxs-lookup"><span data-stu-id="7ba60-199">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="7ba60-200">La `blog` ruta en el código anterior es una **ruta convencional dedicada**.</span><span class="sxs-lookup"><span data-stu-id="7ba60-200">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="7ba60-201">Se denomina una ruta convencional dedicada porque:</span><span class="sxs-lookup"><span data-stu-id="7ba60-201">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="7ba60-202">Utiliza el [enrutamiento convencional](#cr).</span><span class="sxs-lookup"><span data-stu-id="7ba60-202">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="7ba60-203">Está dedicado a una [acción](#action)específica.</span><span class="sxs-lookup"><span data-stu-id="7ba60-203">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="7ba60-204">Dado `controller` `action` que y no aparecen en la plantilla de ruta `"blog/{*article}"` como parámetros:</span><span class="sxs-lookup"><span data-stu-id="7ba60-204">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="7ba60-205">Solo pueden tener los valores predeterminados `{ controller = "Blog", action = "Article" }` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-205">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="7ba60-206">Esta ruta siempre se asigna a la acción `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-206">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="7ba60-207">`/Blog`, `/Blog/Article` y `/Blog/{any-string}` son las únicas rutas de acceso URL que coinciden con la ruta del blog.</span><span class="sxs-lookup"><span data-stu-id="7ba60-207">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="7ba60-208">Ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="7ba60-208">The preceding example:</span></span>

* <span data-ttu-id="7ba60-209">`blog` la ruta tiene una prioridad más alta para las coincidencias que la `default` ruta porque se agrega en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="7ba60-209">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="7ba60-210">Es un ejemplo de enrutamiento de estilo de [indicaciones](https://developer.mozilla.org/docs/Glossary/Slug) en el que es habitual tener un nombre de artículo como parte de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-210">Is an example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="7ba60-211">En ASP.NET Core 3,0 y versiones posteriores, el enrutamiento no:</span><span class="sxs-lookup"><span data-stu-id="7ba60-211">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="7ba60-212">Defina un concepto denominado *Route*.</span><span class="sxs-lookup"><span data-stu-id="7ba60-212">Define a concept called a *route*.</span></span> <span data-ttu-id="7ba60-213">`UseRouting` agrega coincidencia de rutas a la canalización de middleware.</span><span class="sxs-lookup"><span data-stu-id="7ba60-213">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="7ba60-214">El `UseRouting` middleware examina el conjunto de puntos de conexión definidos en la aplicación y selecciona la mejor coincidencia de punto de conexión en función de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="7ba60-214">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="7ba60-215">Proporcione garantías sobre el orden de ejecución de la extensibilidad, como <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> o <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> .</span><span class="sxs-lookup"><span data-stu-id="7ba60-215">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="7ba60-216">Consulte [enrutamiento](xref:fundamentals/routing) para obtener material de referencia sobre el enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="7ba60-216">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="7ba60-217">Orden de enrutamiento convencional</span><span class="sxs-lookup"><span data-stu-id="7ba60-217">Conventional routing order</span></span>

<span data-ttu-id="7ba60-218">El enrutamiento convencional solo coincide con una combinación de acción y controlador que se define en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7ba60-218">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="7ba60-219">Esto se ha diseñado para simplificar los casos en los que las rutas convencionales se superponen.</span><span class="sxs-lookup"><span data-stu-id="7ba60-219">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="7ba60-220">Agregar rutas mediante <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A> y <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> asigna automáticamente un valor de pedido a sus puntos de conexión en función del orden en el que se invocan.</span><span class="sxs-lookup"><span data-stu-id="7ba60-220">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="7ba60-221">Las coincidencias de una ruta que aparece antes tienen una prioridad más alta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-221">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="7ba60-222">El enrutamiento convencional depende del orden.</span><span class="sxs-lookup"><span data-stu-id="7ba60-222">Conventional routing is order-dependent.</span></span> <span data-ttu-id="7ba60-223">En general, las rutas con áreas deben colocarse anteriormente ya que son más específicas que las rutas sin un área.</span><span class="sxs-lookup"><span data-stu-id="7ba60-223">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="7ba60-224">Las [rutas convencionales dedicadas](#dcr) con parámetros de ruta catch-all como `{*article}` pueden hacer que una ruta sea demasiado [expansiva](xref:fundamentals/routing#greedy), lo que significa que coincide con las direcciones URL que desea que coincidan con otras rutas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-224">[Dedicated conventional routes](#dcr) with catch-all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="7ba60-225">Coloque las rutas expansivas más adelante en la tabla de rutas para evitar coincidencias expansivas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-225">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="7ba60-226">Resolver acciones ambiguas</span><span class="sxs-lookup"><span data-stu-id="7ba60-226">Resolving ambiguous actions</span></span>

<span data-ttu-id="7ba60-227">Cuando dos puntos de conexión coinciden a través del enrutamiento, el enrutamiento debe realizar una de las siguientes acciones:</span><span class="sxs-lookup"><span data-stu-id="7ba60-227">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="7ba60-228">Elija el mejor candidato.</span><span class="sxs-lookup"><span data-stu-id="7ba60-228">Choose the best candidate.</span></span>
* <span data-ttu-id="7ba60-229">Iniciar una excepción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-229">Throw an exception.</span></span>

<span data-ttu-id="7ba60-230">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7ba60-230">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="7ba60-231">El controlador anterior define dos acciones que coinciden con:</span><span class="sxs-lookup"><span data-stu-id="7ba60-231">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="7ba60-232">La ruta de dirección URL `/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="7ba60-232">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="7ba60-233">Datos de ruta `{ controller = Products33, action = Edit, id = 17 }` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-233">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="7ba60-234">Este es un patrón típico para los controladores de MVC:</span><span class="sxs-lookup"><span data-stu-id="7ba60-234">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="7ba60-235">`Edit(int)` muestra un formulario para editar un producto.</span><span class="sxs-lookup"><span data-stu-id="7ba60-235">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="7ba60-236">`Edit(int, Product)` procesa el formulario publicado.</span><span class="sxs-lookup"><span data-stu-id="7ba60-236">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="7ba60-237">Para resolver la ruta correcta:</span><span class="sxs-lookup"><span data-stu-id="7ba60-237">To resolve the correct route:</span></span>

* <span data-ttu-id="7ba60-238">`Edit(int, Product)` se selecciona cuando la solicitud es un HTTP `POST` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-238">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="7ba60-239">`Edit(int)` se selecciona cuando el [verbo http](#verb) es cualquier otra cosa.</span><span class="sxs-lookup"><span data-stu-id="7ba60-239">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="7ba60-240">`Edit(int)` normalmente se llama a través de `GET` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-240">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="7ba60-241">, <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> `[HttpPost]` , Se proporciona al enrutamiento para que pueda elegir según el método HTTP de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="7ba60-241">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="7ba60-242">El `HttpPostAttribute` crea `Edit(int, Product)` una coincidencia mejor que `Edit(int)` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-242">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="7ba60-243">Es importante comprender el rol de los atributos como `HttpPostAttribute` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-243">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="7ba60-244">Se definen atributos similares para otros [verbos http](#verb).</span><span class="sxs-lookup"><span data-stu-id="7ba60-244">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="7ba60-245">En el [enrutamiento convencional](#cr), es habitual que las acciones usen el mismo nombre de acción cuando formen parte de un flujo de trabajo Mostrar formulario, enviar formulario.</span><span class="sxs-lookup"><span data-stu-id="7ba60-245">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="7ba60-246">Por ejemplo, vea [examinar los dos métodos de acción de edición](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span><span class="sxs-lookup"><span data-stu-id="7ba60-246">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="7ba60-247">Si el enrutamiento no puede elegir un mejor candidato, <xref:System.Reflection.AmbiguousMatchException> se produce una enumeración de varios puntos de conexión coincidentes.</span><span class="sxs-lookup"><span data-stu-id="7ba60-247">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="7ba60-248">Nombres de ruta convencionales</span><span class="sxs-lookup"><span data-stu-id="7ba60-248">Conventional route names</span></span>

<span data-ttu-id="7ba60-249">Las cadenas  `"blog"` y `"default"` en los ejemplos siguientes son nombres de ruta convencionales:</span><span class="sxs-lookup"><span data-stu-id="7ba60-249">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="7ba60-250">Los nombres de ruta proporcionan un nombre lógico a la ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-250">The route names give the route a logical name.</span></span> <span data-ttu-id="7ba60-251">La ruta con nombre se puede usar para la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-251">The named route can be used for URL generation.</span></span> <span data-ttu-id="7ba60-252">El uso de una ruta con nombre simplifica la creación de direcciones URL cuando el orden de las rutas podría complicar la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-252">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="7ba60-253">Los nombres de ruta deben ser de gran ancho en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7ba60-253">Route names must be unique application wide.</span></span>

<span data-ttu-id="7ba60-254">Nombres de ruta:</span><span class="sxs-lookup"><span data-stu-id="7ba60-254">Route names:</span></span>

* <span data-ttu-id="7ba60-255">No tiene ningún impacto en la coincidencia de direcciones URL ni en el control de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="7ba60-255">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="7ba60-256">Solo se usan para la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-256">Are used only for URL generation.</span></span>

<span data-ttu-id="7ba60-257">El concepto de nombre de ruta se representa en enrutamiento como [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span><span class="sxs-lookup"><span data-stu-id="7ba60-257">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="7ba60-258">El nombre de **ruta** de los términos y el **nombre del punto de conexión** :</span><span class="sxs-lookup"><span data-stu-id="7ba60-258">The terms **route name** and **endpoint name** :</span></span>

* <span data-ttu-id="7ba60-259">Son intercambiables.</span><span class="sxs-lookup"><span data-stu-id="7ba60-259">Are interchangeable.</span></span>
* <span data-ttu-id="7ba60-260">La que se usa en la documentación y el código depende de la API que se describe.</span><span class="sxs-lookup"><span data-stu-id="7ba60-260">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="7ba60-261">Enrutamiento de atributos para las API de REST</span><span class="sxs-lookup"><span data-stu-id="7ba60-261">Attribute routing for REST APIs</span></span>

<span data-ttu-id="7ba60-262">Las API de REST deben usar el enrutamiento de atributos para modelar la funcionalidad de la aplicación como un conjunto de recursos en el que las operaciones se representan mediante [verbos http](#verb).</span><span class="sxs-lookup"><span data-stu-id="7ba60-262">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="7ba60-263">El enrutamiento mediante atributos utiliza un conjunto de atributos para asignar acciones directamente a las plantillas de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-263">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="7ba60-264">El `StartUp.Configure` código siguiente es típico para una API de REST y se usa en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7ba60-264">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupAPI.cs?name=snippet)]

<span data-ttu-id="7ba60-265">En el código anterior, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> se llama a dentro de `UseEndpoints` para asignar controladores enrutados de atributo.</span><span class="sxs-lookup"><span data-stu-id="7ba60-265">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="7ba60-266">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7ba60-266">In the following example:</span></span>

* <span data-ttu-id="7ba60-267">`Configure`Se usa el método anterior.</span><span class="sxs-lookup"><span data-stu-id="7ba60-267">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="7ba60-268">`HomeController` coincide con un conjunto de direcciones URL similar al que coincide con la ruta convencional predeterminada `{controller=Home}/{action=Index}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-268">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="7ba60-269">La `HomeController.Index` acción se ejecuta para cualquiera de las rutas de dirección URL `/` ,, `/Home` `/Home/Index` o `/Home/Index/3` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-269">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="7ba60-270">Este ejemplo resalta una diferencia de programación clave entre el enrutamiento de atributos y el [enrutamiento convencional](#cr).</span><span class="sxs-lookup"><span data-stu-id="7ba60-270">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="7ba60-271">El enrutamiento de atributos requiere más entradas para especificar una ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-271">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="7ba60-272">La ruta predeterminada convencional controla las rutas de forma más concisa.</span><span class="sxs-lookup"><span data-stu-id="7ba60-272">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="7ba60-273">Sin embargo, el enrutamiento de atributos permite y requiere un control preciso de las plantillas de ruta que se aplican a cada [acción](#action).</span><span class="sxs-lookup"><span data-stu-id="7ba60-273">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="7ba60-274">Con el enrutamiento de atributos, los nombres de acción y controlador no juegan ninguna parte en la que se haga coincidir la acción, a menos que se use el [reemplazo de tokens](#routing-token-replacement-templates-ref-label) .</span><span class="sxs-lookup"><span data-stu-id="7ba60-274">With attribute routing, the controller and action names play no part in which action is matched, unless [token replacement](#routing-token-replacement-templates-ref-label) is used.</span></span> <span data-ttu-id="7ba60-275">El ejemplo siguiente coincide con las mismas direcciones URL que en el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="7ba60-275">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="7ba60-276">En el código siguiente se usa el reemplazo de tokens para `action` y `controller` :</span><span class="sxs-lookup"><span data-stu-id="7ba60-276">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="7ba60-277">El código siguiente se aplica `[Route("[controller]/[action]")]` al controlador:</span><span class="sxs-lookup"><span data-stu-id="7ba60-277">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="7ba60-278">En el código anterior, las `Index` plantillas de método deben anteponer `/` o `~/` a las plantillas de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-278">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="7ba60-279">Las plantillas de ruta aplicadas a una acción que comienzan por `/` o `~/` no se combinan con las plantillas de ruta que se aplican al controlador.</span><span class="sxs-lookup"><span data-stu-id="7ba60-279">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="7ba60-280">Consulte [precedencia](xref:fundamentals/routing#rtp) de la plantilla de ruta para obtener información sobre la selección de plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-280">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="7ba60-281">Nombres de enrutamientos reservados</span><span class="sxs-lookup"><span data-stu-id="7ba60-281">Reserved routing names</span></span>

<span data-ttu-id="7ba60-282">Las siguientes palabras clave son nombres de parámetro de ruta reservados al usar controladores o Razor páginas:</span><span class="sxs-lookup"><span data-stu-id="7ba60-282">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="7ba60-283">`page`El uso de como parámetro de ruta con enrutamiento de atributos es un error común.</span><span class="sxs-lookup"><span data-stu-id="7ba60-283">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="7ba60-284">Esto da como resultado un comportamiento incoherente y confuso con la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-284">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="7ba60-285">La generación de direcciones URL utiliza los nombres de parámetros especiales para determinar si una operación de generación de direcciones URL hace referencia a una Razor página o a un controlador.</span><span class="sxs-lookup"><span data-stu-id="7ba60-285">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="7ba60-286">Plantillas de verbo HTTP</span><span class="sxs-lookup"><span data-stu-id="7ba60-286">HTTP verb templates</span></span>

<span data-ttu-id="7ba60-287">ASP.NET Core tiene las siguientes plantillas de verbo HTTP:</span><span class="sxs-lookup"><span data-stu-id="7ba60-287">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="7ba60-288">[HttpGet](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="7ba60-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="7ba60-289">[HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="7ba60-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="7ba60-290">[HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="7ba60-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="7ba60-291">[HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="7ba60-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="7ba60-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="7ba60-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="7ba60-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="7ba60-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="7ba60-294">Plantillas de ruta</span><span class="sxs-lookup"><span data-stu-id="7ba60-294">Route templates</span></span>

<span data-ttu-id="7ba60-295">ASP.NET Core tiene las siguientes plantillas de ruta:</span><span class="sxs-lookup"><span data-stu-id="7ba60-295">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="7ba60-296">Todas las [plantillas de verbo http](#verb) son plantillas de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-296">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="7ba60-297">[Distribuya](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="7ba60-297">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="7ba60-298">Enrutamiento de atributos con atributos de verbo http</span><span class="sxs-lookup"><span data-stu-id="7ba60-298">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="7ba60-299">Considere el siguiente controlador:</span><span class="sxs-lookup"><span data-stu-id="7ba60-299">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="7ba60-300">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="7ba60-300">In the preceding code:</span></span>

* <span data-ttu-id="7ba60-301">Cada acción contiene el `[HttpGet]` atributo, que limita la coincidencia a las solicitudes HTTP GET únicamente.</span><span class="sxs-lookup"><span data-stu-id="7ba60-301">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="7ba60-302">La `GetProduct` acción incluye la `"{id}"` plantilla, por lo `id` que se anexa a la `"api/[controller]"` plantilla en el controlador.</span><span class="sxs-lookup"><span data-stu-id="7ba60-302">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="7ba60-303">La plantilla de métodos es `"api/[controller]/"{id}""` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-303">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="7ba60-304">Por lo tanto, esta acción solo coincide con las solicitudes GET del formulario `/api/test2/xyz` ,, `/api/test2/123` `/api/test2/{any string}` , etc.</span><span class="sxs-lookup"><span data-stu-id="7ba60-304">Therefore this action only matches GET requests for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="7ba60-305">La `GetIntProduct` acción contiene la `"int/{id:int}")` plantilla.</span><span class="sxs-lookup"><span data-stu-id="7ba60-305">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="7ba60-306">La `:int` parte de la plantilla restringe los `id` valores de ruta a cadenas que se pueden convertir en un entero.</span><span class="sxs-lookup"><span data-stu-id="7ba60-306">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="7ba60-307">Una solicitud GET a `/api/test2/int/abc` :</span><span class="sxs-lookup"><span data-stu-id="7ba60-307">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="7ba60-308">No coincide con esta acción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-308">Doesn't match this action.</span></span>
  * <span data-ttu-id="7ba60-309">Devuelve un error [404 no encontrado](https://developer.mozilla.org/docs/Web/HTTP/Status/404) .</span><span class="sxs-lookup"><span data-stu-id="7ba60-309">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="7ba60-310">La `GetInt2Product` acción contiene `{id}` en la plantilla, pero no `id` se limita a los valores que se pueden convertir en un entero.</span><span class="sxs-lookup"><span data-stu-id="7ba60-310">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="7ba60-311">Una solicitud GET a `/api/test2/int2/abc` :</span><span class="sxs-lookup"><span data-stu-id="7ba60-311">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="7ba60-312">Coincide con esta ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-312">Matches this route.</span></span>
  * <span data-ttu-id="7ba60-313">El enlace de modelos no se puede convertir `abc` en un entero.</span><span class="sxs-lookup"><span data-stu-id="7ba60-313">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="7ba60-314">El `id` parámetro del método es Integer.</span><span class="sxs-lookup"><span data-stu-id="7ba60-314">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="7ba60-315">Devuelve una [Solicitud incorrecta de 400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) porque el enlace de modelos no se pudo convertir `abc` en un entero.</span><span class="sxs-lookup"><span data-stu-id="7ba60-315">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="7ba60-316">El enrutamiento mediante atributos puede utilizar <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> atributos como <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> y <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="7ba60-316">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="7ba60-317">Todos los atributos de [verbo http](#verb) aceptan una plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-317">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="7ba60-318">En el ejemplo siguiente se muestran dos acciones que coinciden con la misma plantilla de ruta:</span><span class="sxs-lookup"><span data-stu-id="7ba60-318">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="7ba60-319">Uso de la ruta de dirección URL `/products3` :</span><span class="sxs-lookup"><span data-stu-id="7ba60-319">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="7ba60-320">La `MyProductsController.ListProducts` acción se ejecuta cuando el [verbo http](#verb) es `GET` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-320">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="7ba60-321">La `MyProductsController.CreateProduct` acción se ejecuta cuando el [verbo http](#verb) es `POST` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-321">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="7ba60-322">Al compilar una API de REST, es poco habitual que deba usar `[Route(...)]` en un método de acción porque la acción acepta todos los métodos http.</span><span class="sxs-lookup"><span data-stu-id="7ba60-322">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="7ba60-323">Es mejor usar el [atributo verbo http](#verb) más específico para ser precisos sobre lo que admite la API.</span><span class="sxs-lookup"><span data-stu-id="7ba60-323">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="7ba60-324">Se espera que los clientes de API de REST sepan qué rutas y verbos HTTP se asignan a determinadas operaciones lógicas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-324">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="7ba60-325">Las API de REST deben usar el enrutamiento de atributos para modelar la funcionalidad de la aplicación como un conjunto de recursos en el que las operaciones se representan mediante verbos HTTP.</span><span class="sxs-lookup"><span data-stu-id="7ba60-325">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="7ba60-326">Esto significa que muchas operaciones, por ejemplo, GET y POST en el mismo recurso lógico, usan la misma dirección URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-326">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="7ba60-327">El enrutamiento mediante atributos proporciona un nivel de control que es necesario para diseñar cuidadosamente un diseño de puntos de conexión públicos de la API.</span><span class="sxs-lookup"><span data-stu-id="7ba60-327">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="7ba60-328">Puesto que una ruta de atributo se aplica a una acción específica, es fácil crear parámetros necesarios como parte de la definición de plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-328">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="7ba60-329">En el ejemplo siguiente, `id` se requiere como parte de la ruta de dirección URL:</span><span class="sxs-lookup"><span data-stu-id="7ba60-329">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="7ba60-330">La `Products2ApiController.GetProduct(int)` acción:</span><span class="sxs-lookup"><span data-stu-id="7ba60-330">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="7ba60-331">Se ejecuta con una ruta de dirección URL como `/products2/3`</span><span class="sxs-lookup"><span data-stu-id="7ba60-331">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="7ba60-332">No se ejecuta con la ruta de dirección URL `/products2` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-332">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="7ba60-333">El atributo [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) permite que una acción limite los tipos de contenido de la solicitud compatibles.</span><span class="sxs-lookup"><span data-stu-id="7ba60-333">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="7ba60-334">Para obtener más información, vea [definir tipos de contenido de solicitud admitidos con el atributo consumes](xref:web-api/index#consumes).</span><span class="sxs-lookup"><span data-stu-id="7ba60-334">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="7ba60-335">Consulte [Enrutamiento](xref:fundamentals/routing) para obtener una descripción completa de las plantillas de ruta y las opciones relacionadas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-335">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="7ba60-336">Para obtener más información sobre `[ApiController]` , vea el [atributo ApiController](xref:web-api/index##apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="7ba60-336">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="7ba60-337">Nombre de ruta</span><span class="sxs-lookup"><span data-stu-id="7ba60-337">Route name</span></span>

<span data-ttu-id="7ba60-338">El código siguiente define un nombre de ruta de `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="7ba60-338">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="7ba60-339">Los nombres de ruta se pueden utilizar para generar una dirección URL basada en una ruta específica.</span><span class="sxs-lookup"><span data-stu-id="7ba60-339">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="7ba60-340">Nombres de ruta:</span><span class="sxs-lookup"><span data-stu-id="7ba60-340">Route names:</span></span>

* <span data-ttu-id="7ba60-341">No tiene ningún impacto en el comportamiento de coincidencia de la dirección URL del enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="7ba60-341">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="7ba60-342">Solo se usan para la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-342">Are only used for URL generation.</span></span>

<span data-ttu-id="7ba60-343">Los nombres de ruta deben ser únicos en toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7ba60-343">Route names must be unique application-wide.</span></span>

<span data-ttu-id="7ba60-344">Compare el código anterior con la ruta predeterminada convencional, que define el `id` parámetro como opcional ( `{id?}` ).</span><span class="sxs-lookup"><span data-stu-id="7ba60-344">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="7ba60-345">La capacidad de especificar con precisión las API tiene ventajas, como permitir `/products` y `/products/5` Enviar a acciones diferentes.</span><span class="sxs-lookup"><span data-stu-id="7ba60-345">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="7ba60-346">Combinar rutas de atributo</span><span class="sxs-lookup"><span data-stu-id="7ba60-346">Combining attribute routes</span></span>

<span data-ttu-id="7ba60-347">Para que el enrutamiento mediante atributos sea menos repetitivo, los atributos de ruta del controlador se combinan con los atributos de ruta de las acciones individuales.</span><span class="sxs-lookup"><span data-stu-id="7ba60-347">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="7ba60-348">Las plantillas de ruta definidas en el controlador se anteponen a las plantillas de ruta de las acciones.</span><span class="sxs-lookup"><span data-stu-id="7ba60-348">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="7ba60-349">La colocación de un atributo de ruta en el controlador hace que **todas** las acciones del controlador usen el enrutamiento mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="7ba60-349">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="7ba60-350">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="7ba60-350">In the preceding example:</span></span>

* <span data-ttu-id="7ba60-351">La ruta de dirección URL `/products` puede coincidir `ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="7ba60-351">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="7ba60-352">La ruta de dirección URL `/products/5` puede coincidir `ProductsApi.GetProduct(int)` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-352">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="7ba60-353">Ambas acciones solo coinciden con HTTP `GET` porque están marcadas con el `[HttpGet]` atributo.</span><span class="sxs-lookup"><span data-stu-id="7ba60-353">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="7ba60-354">Las plantillas de ruta aplicadas a una acción que comienzan por `/` o `~/` no se combinan con las plantillas de ruta que se aplican al controlador.</span><span class="sxs-lookup"><span data-stu-id="7ba60-354">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="7ba60-355">El ejemplo siguiente coincide con un conjunto de rutas de dirección URL similar a la ruta predeterminada.</span><span class="sxs-lookup"><span data-stu-id="7ba60-355">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="7ba60-356">En la tabla siguiente se explican los `[Route]` atributos del código anterior:</span><span class="sxs-lookup"><span data-stu-id="7ba60-356">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="7ba60-357">Atributo</span><span class="sxs-lookup"><span data-stu-id="7ba60-357">Attribute</span></span>               | <span data-ttu-id="7ba60-358">Combina con `[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="7ba60-358">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="7ba60-359">Define la plantilla de ruta</span><span class="sxs-lookup"><span data-stu-id="7ba60-359">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="7ba60-360">Sí</span><span class="sxs-lookup"><span data-stu-id="7ba60-360">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="7ba60-361">Sí</span><span class="sxs-lookup"><span data-stu-id="7ba60-361">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="7ba60-362">**No**</span><span class="sxs-lookup"><span data-stu-id="7ba60-362">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="7ba60-363">Sí</span><span class="sxs-lookup"><span data-stu-id="7ba60-363">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="7ba60-364">Orden de la ruta de atributo</span><span class="sxs-lookup"><span data-stu-id="7ba60-364">Attribute route order</span></span>

<span data-ttu-id="7ba60-365">El enrutamiento crea un árbol y coincide con todos los puntos de conexión simultáneamente:</span><span class="sxs-lookup"><span data-stu-id="7ba60-365">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="7ba60-366">Las entradas de ruta se comportan como si se hubieran colocado en una ordenación ideal.</span><span class="sxs-lookup"><span data-stu-id="7ba60-366">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="7ba60-367">Las rutas más específicas tienen la oportunidad de ejecutarse antes que las rutas más generales.</span><span class="sxs-lookup"><span data-stu-id="7ba60-367">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="7ba60-368">Por ejemplo, una ruta de atributo como `blog/search/{topic}` es más específica que una ruta de atributo como `blog/{*article}` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-368">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="7ba60-369">La `blog/search/{topic}` ruta tiene una prioridad más alta, de forma predeterminada, porque es más específica.</span><span class="sxs-lookup"><span data-stu-id="7ba60-369">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="7ba60-370">Mediante el [enrutamiento convencional](#cr), el desarrollador es responsable de colocar las rutas en el orden deseado.</span><span class="sxs-lookup"><span data-stu-id="7ba60-370">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="7ba60-371">Las rutas de atributo pueden configurar un orden mediante la <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> propiedad.</span><span class="sxs-lookup"><span data-stu-id="7ba60-371">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="7ba60-372">Todos los [atributos de ruta](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) proporcionados por el marco de trabajo `Order` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-372">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="7ba60-373">Las rutas se procesan de acuerdo con el orden ascendente de la propiedad `Order`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-373">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="7ba60-374">El orden predeterminado es `0`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-374">The default order is `0`.</span></span> <span data-ttu-id="7ba60-375">La configuración de una ruta mediante `Order = -1` se ejecuta antes que las rutas que no establecen un orden.</span><span class="sxs-lookup"><span data-stu-id="7ba60-375">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="7ba60-376">Establecer una ruta mediante `Order = 1` ejecuciones después del orden de la ruta predeterminada.</span><span class="sxs-lookup"><span data-stu-id="7ba60-376">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="7ba60-377">**Evite** en función de `Order` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-377">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="7ba60-378">Si el espacio de la dirección URL de una aplicación requiere que los valores de orden explícitos se enruten correctamente, es probable que también sea confuso para los clientes.</span><span class="sxs-lookup"><span data-stu-id="7ba60-378">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="7ba60-379">En general, el enrutamiento de atributos selecciona la ruta correcta con coincidencia de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-379">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="7ba60-380">Si el orden predeterminado usado para la generación de direcciones URL no funciona, el uso de un nombre de ruta como una invalidación suele ser más sencillo que aplicar la `Order` propiedad.</span><span class="sxs-lookup"><span data-stu-id="7ba60-380">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="7ba60-381">Tenga en cuenta los dos controladores siguientes que definen la coincidencia de ruta `/home` :</span><span class="sxs-lookup"><span data-stu-id="7ba60-381">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="7ba60-382">Al solicitar `/home` el código anterior, se produce una excepción similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="7ba60-382">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="7ba60-383">`Order`La adición a uno de los atributos de ruta resuelve la ambigüedad:</span><span class="sxs-lookup"><span data-stu-id="7ba60-383">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="7ba60-384">Con el código anterior, `/home` ejecuta el `HomeController.Index` punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="7ba60-384">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="7ba60-385">Para llegar a la `MyDemoController.MyIndex` solicitud, `/home/MyIndex` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-385">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="7ba60-386">**Nota** :</span><span class="sxs-lookup"><span data-stu-id="7ba60-386">**Note** :</span></span>

* <span data-ttu-id="7ba60-387">El código anterior es un ejemplo o un diseño de enrutamiento deficiente.</span><span class="sxs-lookup"><span data-stu-id="7ba60-387">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="7ba60-388">Se usó para ilustrar la `Order` propiedad.</span><span class="sxs-lookup"><span data-stu-id="7ba60-388">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="7ba60-389">La `Order` propiedad solo resuelve la ambigüedad, no se puede encontrar una coincidencia con esa plantilla.</span><span class="sxs-lookup"><span data-stu-id="7ba60-389">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="7ba60-390">Sería mejor quitar la `[Route("Home")]` plantilla.</span><span class="sxs-lookup"><span data-stu-id="7ba60-390">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="7ba60-391">Vea [ Razor páginas ruta y convenciones de aplicación: orden de ruta](xref:razor-pages/razor-pages-conventions#route-order) para obtener información sobre el orden de las rutas con Razor las páginas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-391">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="7ba60-392">En algunos casos, se devuelve un error HTTP 500 con rutas ambiguas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-392">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="7ba60-393">Utilice el [registro](xref:fundamentals/logging/index) para ver qué extremos produjeron el `AmbiguousMatchException` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-393">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="7ba60-394">Reemplazo de tokens en las plantillas de ruta [Controller], [Action], [Area]</span><span class="sxs-lookup"><span data-stu-id="7ba60-394">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="7ba60-395">Para mayor comodidad, las rutas de atributo admiten el reemplazo de tokens para los parámetros de ruta reservados mediante la inclusión de un token en una de las siguientes opciones:</span><span class="sxs-lookup"><span data-stu-id="7ba60-395">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="7ba60-396">Corchetes: `[]`</span><span class="sxs-lookup"><span data-stu-id="7ba60-396">Square brackets: `[]`</span></span>
* <span data-ttu-id="7ba60-397">Llaves: `{}`</span><span class="sxs-lookup"><span data-stu-id="7ba60-397">Curly braces: `{}`</span></span>

<span data-ttu-id="7ba60-398">Los tokens `[action]` , `[area]` y `[controller]` se reemplazan por los valores del nombre de acción, el nombre del área y el nombre del controlador de la acción en la que se define la ruta:</span><span class="sxs-lookup"><span data-stu-id="7ba60-398">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="7ba60-399">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="7ba60-399">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="7ba60-400">Coincide con `/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="7ba60-400">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="7ba60-401">Coincide con `/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="7ba60-401">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="7ba60-402">El reemplazo del token se produce como último paso de la creación de las rutas de atributo.</span><span class="sxs-lookup"><span data-stu-id="7ba60-402">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="7ba60-403">El ejemplo anterior se comporta igual que el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7ba60-403">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="7ba60-404">Las rutas de atributo también se pueden combinar con la herencia.</span><span class="sxs-lookup"><span data-stu-id="7ba60-404">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="7ba60-405">Esto es eficaz combinado con el reemplazo de tokens.</span><span class="sxs-lookup"><span data-stu-id="7ba60-405">This is powerful combined with token replacement.</span></span> <span data-ttu-id="7ba60-406">El reemplazo de token también se aplica a los nombres de ruta definidos por las rutas de atributo.</span><span class="sxs-lookup"><span data-stu-id="7ba60-406">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="7ba60-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`genera un nombre de ruta único para cada acción:</span><span class="sxs-lookup"><span data-stu-id="7ba60-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="7ba60-408">El reemplazo de token también se aplica a los nombres de ruta definidos por las rutas de atributo.</span><span class="sxs-lookup"><span data-stu-id="7ba60-408">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="7ba60-409"> genera un nombre de ruta único para cada acción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-409">generates a unique route name for each action.</span></span>

<span data-ttu-id="7ba60-410">Para que el delimitador de reemplazo de token `[` o `]` coincida, repita el carácter (`[[` o `]]`) para usarlo como secuencia de escape.</span><span class="sxs-lookup"><span data-stu-id="7ba60-410">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="7ba60-411">Usar un transformador de parámetro para personalizar el reemplazo de tokens</span><span class="sxs-lookup"><span data-stu-id="7ba60-411">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="7ba60-412">El reemplazo de tokens se puede personalizarse mediante un transformador de parámetro.</span><span class="sxs-lookup"><span data-stu-id="7ba60-412">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="7ba60-413">Un transformador de parámetro implementa <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> y transforma el valor de parámetros.</span><span class="sxs-lookup"><span data-stu-id="7ba60-413">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="7ba60-414">Por ejemplo, un `SlugifyParameterTransformer` transformador de parámetros personalizados cambia el `SubscriptionManagement` valor de ruta a `subscription-management` :</span><span class="sxs-lookup"><span data-stu-id="7ba60-414">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="7ba60-415"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> es una convención de modelo de aplicación que:</span><span class="sxs-lookup"><span data-stu-id="7ba60-415">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="7ba60-416">Aplica un transformador de parámetro a todas las rutas de atributo en una aplicación.</span><span class="sxs-lookup"><span data-stu-id="7ba60-416">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="7ba60-417">Personaliza los valores de token de ruta de atributo que se reemplazan.</span><span class="sxs-lookup"><span data-stu-id="7ba60-417">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="7ba60-418">El `ListAll` método anterior coincide con `/subscription-management/list-all` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-418">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="7ba60-419">`RouteTokenTransformerConvention` está registrado como una opción en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-419">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="7ba60-420">Consulte [documentos web de MDN en](https://developer.mozilla.org/docs/Glossary/Slug) el documento para ver la definición de Slug.</span><span class="sxs-lookup"><span data-stu-id="7ba60-420">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="7ba60-421">Múltiples rutas de atributo</span><span class="sxs-lookup"><span data-stu-id="7ba60-421">Multiple attribute routes</span></span>

<span data-ttu-id="7ba60-422">El enrutamiento mediante atributos permite definir varias rutas que llegan a la misma acción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-422">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="7ba60-423">El uso más común de esto es imitar el comportamiento de la ruta convencional predeterminada tal como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7ba60-423">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="7ba60-424">La colocación de varios atributos de ruta en el controlador significa que cada uno de ellos se combina con cada uno de los atributos de ruta de los métodos de acción:</span><span class="sxs-lookup"><span data-stu-id="7ba60-424">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="7ba60-425">Todas las restricciones de ruta de [verbo http](#verb) implementan `IActionConstraint` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-425">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="7ba60-426">Cuando se colocan varios atributos de ruta que implementan <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> en una acción:</span><span class="sxs-lookup"><span data-stu-id="7ba60-426">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="7ba60-427">Cada restricción de acción se combina con la plantilla de ruta que se aplica al controlador.</span><span class="sxs-lookup"><span data-stu-id="7ba60-427">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="7ba60-428">El uso de varias rutas en acciones podría parecer útil y eficaz, es mejor mantener el espacio de la dirección URL de la aplicación de forma básica y bien definida.</span><span class="sxs-lookup"><span data-stu-id="7ba60-428">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="7ba60-429">Use varias rutas en acciones **solo** cuando sea necesario, por ejemplo, para admitir clientes existentes.</span><span class="sxs-lookup"><span data-stu-id="7ba60-429">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="7ba60-430">Especificación de parámetros opcionales de ruta de atributo, valores predeterminados y restricciones</span><span class="sxs-lookup"><span data-stu-id="7ba60-430">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="7ba60-431">Las rutas de atributo admiten la misma sintaxis en línea que las rutas convencionales para especificar parámetros opcionales, valores predeterminados y restricciones.</span><span class="sxs-lookup"><span data-stu-id="7ba60-431">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="7ba60-432">En el código anterior, `[HttpPost("product/{id:int}")]` aplica una restricción de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-432">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="7ba60-433">La `ProductsController.ShowProduct` acción coincide solo en rutas de dirección URL como `/product/3` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-433">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="7ba60-434">La parte de la plantilla de ruta `{id:int}` restringe ese segmento a solo enteros.</span><span class="sxs-lookup"><span data-stu-id="7ba60-434">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

<span data-ttu-id="7ba60-435">Consulte [Referencia de plantilla de ruta](xref:fundamentals/routing#route-template-reference) para obtener una descripción detallada de la sintaxis de la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-435">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="7ba60-436">Atributos de ruta personalizados mediante IRouteTemplateProvider</span><span class="sxs-lookup"><span data-stu-id="7ba60-436">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="7ba60-437">Todos los [atributos de ruta](#rt) implementan <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> .</span><span class="sxs-lookup"><span data-stu-id="7ba60-437">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="7ba60-438">El tiempo de ejecución de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="7ba60-438">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="7ba60-439">Busca atributos en las clases de controlador y métodos de acción cuando se inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7ba60-439">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="7ba60-440">Utiliza los atributos que implementan `IRouteTemplateProvider` para generar el conjunto inicial de rutas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-440">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="7ba60-441">Implemente `IRouteTemplateProvider` para definir atributos de ruta personalizados.</span><span class="sxs-lookup"><span data-stu-id="7ba60-441">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="7ba60-442">Cada `IRouteTemplateProvider` le permite definir una única ruta con una plantilla de ruta, un orden y un nombre personalizados:</span><span class="sxs-lookup"><span data-stu-id="7ba60-442">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="7ba60-443">El `Get` método anterior devuelve `Order = 2, Template = api/MyTestApi` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-443">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="7ba60-444">Usar el modelo de aplicación para personalizar las rutas de atributo</span><span class="sxs-lookup"><span data-stu-id="7ba60-444">Use application model to customize attribute routes</span></span>

<span data-ttu-id="7ba60-445">El modelo de aplicación:</span><span class="sxs-lookup"><span data-stu-id="7ba60-445">The application model:</span></span>

* <span data-ttu-id="7ba60-446">Es un modelo de objetos creado en el inicio.</span><span class="sxs-lookup"><span data-stu-id="7ba60-446">Is an object model created at startup.</span></span>
* <span data-ttu-id="7ba60-447">Contiene todos los metadatos usados por ASP.NET Core para enrutar y ejecutar las acciones en una aplicación.</span><span class="sxs-lookup"><span data-stu-id="7ba60-447">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="7ba60-448">El modelo de aplicación incluye todos los datos recopilados de los atributos de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-448">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="7ba60-449">La implementación proporciona los datos de los atributos de ruta `IRouteTemplateProvider` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-449">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="7ba60-450">Convention</span><span class="sxs-lookup"><span data-stu-id="7ba60-450">Conventions:</span></span>

* <span data-ttu-id="7ba60-451">Se puede escribir para modificar el modelo de aplicación con el fin de personalizar el comportamiento del enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="7ba60-451">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="7ba60-452">Se leen en el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7ba60-452">Are read at app startup.</span></span>

<span data-ttu-id="7ba60-453">En esta sección se muestra un ejemplo básico de la personalización del enrutamiento mediante el modelo de aplicación.</span><span class="sxs-lookup"><span data-stu-id="7ba60-453">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="7ba60-454">El código siguiente hace que las rutas se alinean aproximadamente con la estructura de carpetas del proyecto.</span><span class="sxs-lookup"><span data-stu-id="7ba60-454">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="7ba60-455">El código siguiente impide que la `namespace` Convención se aplique a los controladores que están enrutados por atributo:</span><span class="sxs-lookup"><span data-stu-id="7ba60-455">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="7ba60-456">Por ejemplo, el controlador siguiente no utiliza `NamespaceRoutingConvention` :</span><span class="sxs-lookup"><span data-stu-id="7ba60-456">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="7ba60-457">El método `NamespaceRoutingConvention.Apply` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="7ba60-457">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="7ba60-458">No hace nada si el controlador está enrutando el atributo.</span><span class="sxs-lookup"><span data-stu-id="7ba60-458">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="7ba60-459">Establece la plantilla Controllers basada en `namespace` , con la base `namespace` quitada.</span><span class="sxs-lookup"><span data-stu-id="7ba60-459">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="7ba60-460">`NamespaceRoutingConvention`Se puede aplicar en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7ba60-460">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="7ba60-461">Por ejemplo, considere el siguiente controlador:</span><span class="sxs-lookup"><span data-stu-id="7ba60-461">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="7ba60-462">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="7ba60-462">In the preceding code:</span></span>

* <span data-ttu-id="7ba60-463">La base `namespace` es `My.Application` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-463">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="7ba60-464">El nombre completo del controlador anterior es `My.Application.Admin.Controllers.UsersController` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-464">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="7ba60-465">`NamespaceRoutingConvention`Establece la plantilla Controllers en `Admin/Controllers/Users/[action]/{id?` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-465">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="7ba60-466">`NamespaceRoutingConvention`También se puede aplicar como atributo en un controlador:</span><span class="sxs-lookup"><span data-stu-id="7ba60-466">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="7ba60-467">Enrutamiento mixto: enrutamiento mediante atributos frente a enrutamiento convencional</span><span class="sxs-lookup"><span data-stu-id="7ba60-467">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="7ba60-468">ASP.NET Core aplicaciones pueden mezclar el uso de enrutamiento convencional y enrutamiento de atributos.</span><span class="sxs-lookup"><span data-stu-id="7ba60-468">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="7ba60-469">Es habitual usar las rutas convencionales para controladores que sirven páginas HTML para los exploradores, y el enrutamiento mediante atributos para los controladores que sirven las API de REST.</span><span class="sxs-lookup"><span data-stu-id="7ba60-469">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="7ba60-470">Las acciones se enrutan bien mediante convención o bien mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="7ba60-470">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="7ba60-471">Colocar una ruta en el controlador o la acción hace que se enrute mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="7ba60-471">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="7ba60-472">Las acciones que definen rutas de atributo no se pueden alcanzar a través de las rutas convencionales y viceversa.</span><span class="sxs-lookup"><span data-stu-id="7ba60-472">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="7ba60-473">**Cualquier** atributo de ruta en el controlador hace que **todas** las acciones en el atributo del controlador se enruten.</span><span class="sxs-lookup"><span data-stu-id="7ba60-473">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="7ba60-474">El enrutamiento de atributos y el enrutamiento convencional usan el mismo motor de enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="7ba60-474">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="7ba60-475">Generación de direcciones URL y valores de ambiente</span><span class="sxs-lookup"><span data-stu-id="7ba60-475">URL Generation and ambient values</span></span>

<span data-ttu-id="7ba60-476">Las aplicaciones pueden usar las características de generación de direcciones URL de enrutamiento para generar vínculos URL a acciones.</span><span class="sxs-lookup"><span data-stu-id="7ba60-476">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="7ba60-477">La generación de direcciones URL elimina las direcciones URL de codificar, por lo que el código es más sólido y fácil de mantener.</span><span class="sxs-lookup"><span data-stu-id="7ba60-477">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="7ba60-478">Esta sección se centra en las características de generación de direcciones URL proporcionadas por MVC y solo cubre los aspectos básicos del funcionamiento de la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-478">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="7ba60-479">Consulte [Enrutamiento](xref:fundamentals/routing) para obtener una descripción detallada de la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-479">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="7ba60-480">La <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interfaz es el elemento subyacente de la infraestructura entre MVC y el enrutamiento para la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-480">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="7ba60-481">Una instancia de `IUrlHelper` está disponible a través de la `Url` propiedad en los controladores, las vistas y los componentes de vista.</span><span class="sxs-lookup"><span data-stu-id="7ba60-481">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="7ba60-482">En el ejemplo siguiente, la `IUrlHelper` interfaz se usa a través de la `Controller.Url` propiedad para generar una dirección URL a otra acción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-482">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="7ba60-483">Si la aplicación usa la ruta convencional predeterminada, el valor de la `url` variable es la cadena de ruta de acceso de dirección URL `/UrlGeneration/Destination` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-483">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="7ba60-484">La ruta de acceso de dirección URL se crea mediante el enrutamiento mediante la combinación de:</span><span class="sxs-lookup"><span data-stu-id="7ba60-484">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="7ba60-485">Los valores de ruta de la solicitud actual, que se denominan **valores de ambiente**.</span><span class="sxs-lookup"><span data-stu-id="7ba60-485">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="7ba60-486">Los valores que se pasan a `Url.Action` y sustituyen esos valores en la plantilla de ruta:</span><span class="sxs-lookup"><span data-stu-id="7ba60-486">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="7ba60-487">El valor de cada uno de los parámetros de ruta incluidos en la plantilla de ruta se sustituye por nombres que coincidan con los valores y los valores de ambiente.</span><span class="sxs-lookup"><span data-stu-id="7ba60-487">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="7ba60-488">Un parámetro de ruta que no tiene un valor puede:</span><span class="sxs-lookup"><span data-stu-id="7ba60-488">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="7ba60-489">Use un valor predeterminado si tiene uno.</span><span class="sxs-lookup"><span data-stu-id="7ba60-489">Use a default value if it has one.</span></span>
* <span data-ttu-id="7ba60-490">Se omitirá si es opcional.</span><span class="sxs-lookup"><span data-stu-id="7ba60-490">Be skipped if it's optional.</span></span> <span data-ttu-id="7ba60-491">Por ejemplo, `id` de la plantilla de ruta `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-491">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="7ba60-492">Se produce un error en la generación de direcciones URL si algún parámetro de ruta requerido no tiene un valor correspondiente.</span><span class="sxs-lookup"><span data-stu-id="7ba60-492">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="7ba60-493">Si se produce un error en la generación de direcciones URL para una ruta, se prueba con la ruta siguiente hasta que se hayan probado todas las rutas o se encuentra una coincidencia.</span><span class="sxs-lookup"><span data-stu-id="7ba60-493">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="7ba60-494">En el ejemplo anterior de `Url.Action` se presupone el [enrutamiento convencional](#cr).</span><span class="sxs-lookup"><span data-stu-id="7ba60-494">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="7ba60-495">La generación de direcciones URL funciona de forma similar con el [enrutamiento de atributos](#ar), aunque los conceptos son diferentes.</span><span class="sxs-lookup"><span data-stu-id="7ba60-495">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="7ba60-496">Con enrutamiento convencional:</span><span class="sxs-lookup"><span data-stu-id="7ba60-496">With conventional routing:</span></span>

* <span data-ttu-id="7ba60-497">Los valores de ruta se usan para expandir una plantilla.</span><span class="sxs-lookup"><span data-stu-id="7ba60-497">The route values are used to expand a template.</span></span>
* <span data-ttu-id="7ba60-498">Los valores de ruta de `controller` y `action` suelen aparecer en esa plantilla.</span><span class="sxs-lookup"><span data-stu-id="7ba60-498">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="7ba60-499">Esto funciona porque las direcciones URL que coinciden con el enrutamiento se adhieren a una Convención.</span><span class="sxs-lookup"><span data-stu-id="7ba60-499">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="7ba60-500">En el ejemplo siguiente se utiliza el enrutamiento de atributos:</span><span class="sxs-lookup"><span data-stu-id="7ba60-500">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="7ba60-501">La `Source` acción en el código anterior genera `custom/url/to/destination` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-501">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="7ba60-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> se agregó en ASP.NET Core 3,0 como alternativa a `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="7ba60-503">`LinkGenerator` ofrece una funcionalidad similar pero más flexible.</span><span class="sxs-lookup"><span data-stu-id="7ba60-503">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="7ba60-504">Cada método de `IUrlHelper` tiene también una familia de métodos correspondiente `LinkGenerator` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-504">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="7ba60-505">Generación de direcciones URL por nombre de acción</span><span class="sxs-lookup"><span data-stu-id="7ba60-505">Generating URLs by action name</span></span>

<span data-ttu-id="7ba60-506">[URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)y todas las sobrecargas relacionadas están diseñadas para generar el punto de conexión de destino mediante la especificación de un nombre de controlador y un nombre de acción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-506">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="7ba60-507">Al utilizar `Url.Action` , el tiempo de ejecución proporciona los valores de ruta actuales para `controller` y `action` :</span><span class="sxs-lookup"><span data-stu-id="7ba60-507">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="7ba60-508">El valor de `controller` y `action` forman parte de los valores y valores de [ambiente](#ambient) .</span><span class="sxs-lookup"><span data-stu-id="7ba60-508">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="7ba60-509">El método `Url.Action` siempre usa los valores actuales de `action` y `controller` y genera una ruta de acceso de dirección URL que enruta a la acción actual.</span><span class="sxs-lookup"><span data-stu-id="7ba60-509">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="7ba60-510">El enrutamiento intenta usar los valores de los valores de ambiente para rellenar la información que no se proporcionó al generar una dirección URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-510">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="7ba60-511">Considere una ruta como `{a}/{b}/{c}/{d}` con valores de ambiente `{ a = Alice, b = Bob, c = Carol, d = David }` :</span><span class="sxs-lookup"><span data-stu-id="7ba60-511">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="7ba60-512">El enrutamiento tiene suficiente información para generar una dirección URL sin valores adicionales.</span><span class="sxs-lookup"><span data-stu-id="7ba60-512">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="7ba60-513">El enrutamiento tiene suficiente información porque todos los parámetros de ruta tienen un valor.</span><span class="sxs-lookup"><span data-stu-id="7ba60-513">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="7ba60-514">Si `{ d = Donovan }` se agrega el valor:</span><span class="sxs-lookup"><span data-stu-id="7ba60-514">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="7ba60-515">`{ d = David }`Se omite el valor.</span><span class="sxs-lookup"><span data-stu-id="7ba60-515">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="7ba60-516">La ruta de dirección URL generada es `Alice/Bob/Carol/Donovan` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-516">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="7ba60-517">**ADVERTENCIA** : las rutas de dirección URL son jerárquicas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-517">**Warning** : URL paths are hierarchical.</span></span> <span data-ttu-id="7ba60-518">En el ejemplo anterior, si `{ c = Cheryl }` se agrega el valor:</span><span class="sxs-lookup"><span data-stu-id="7ba60-518">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="7ba60-519">Ambos valores `{ c = Carol, d = David }` se omiten.</span><span class="sxs-lookup"><span data-stu-id="7ba60-519">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="7ba60-520">Ya no hay un valor para `d` y se produce un error en la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-520">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="7ba60-521">Se deben especificar los valores deseados de `c` y `d` para generar una dirección URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-521">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="7ba60-522">Podría esperar que se produzca este problema con la ruta predeterminada `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-522">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="7ba60-523">Este problema es raro en la práctica porque `Url.Action` siempre especifica explícitamente un `controller` valor de y `action` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-523">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="7ba60-524">Varias sobrecargas de [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) toman un objeto de valores de ruta para proporcionar valores para los parámetros de ruta distintos de `controller` y `action` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-524">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="7ba60-525">El objeto Route Values se usa con frecuencia con `id` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-525">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="7ba60-526">Por ejemplo, `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-526">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="7ba60-527">Objeto de valores de ruta:</span><span class="sxs-lookup"><span data-stu-id="7ba60-527">The route values object:</span></span>

* <span data-ttu-id="7ba60-528">Por Convención suele ser un objeto de tipo anónimo.</span><span class="sxs-lookup"><span data-stu-id="7ba60-528">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="7ba60-529">Puede ser un `IDictionary<>` o un [poco](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span><span class="sxs-lookup"><span data-stu-id="7ba60-529">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="7ba60-530">Los valores de ruta adicionales que no coinciden con los parámetros de ruta se colocan en la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-530">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="7ba60-531">El código anterior genera `/Products/Buy/17?color=red` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-531">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="7ba60-532">El código siguiente genera una dirección URL absoluta:</span><span class="sxs-lookup"><span data-stu-id="7ba60-532">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="7ba60-533">Para crear una dirección URL absoluta, use una de las siguientes opciones:</span><span class="sxs-lookup"><span data-stu-id="7ba60-533">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="7ba60-534">Una sobrecarga que acepta un `protocol` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-534">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="7ba60-535">Por ejemplo, el código anterior.</span><span class="sxs-lookup"><span data-stu-id="7ba60-535">For example, the preceding code.</span></span>
* <span data-ttu-id="7ba60-536">[LinkGenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), que genera identificadores URI absolutos de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="7ba60-536">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="7ba60-537">Generar direcciones URL por ruta</span><span class="sxs-lookup"><span data-stu-id="7ba60-537">Generate URLs by route</span></span>

<span data-ttu-id="7ba60-538">En el código anterior se mostró la generación de una dirección URL pasando el controlador y el nombre de la acción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-538">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="7ba60-539">`IUrlHelper` también proporciona la familia de métodos [URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) .</span><span class="sxs-lookup"><span data-stu-id="7ba60-539">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="7ba60-540">Estos métodos son similares a [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), pero no copian los valores actuales de `action` y `controller` en los valores de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-540">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="7ba60-541">El uso más común de `Url.RouteUrl` :</span><span class="sxs-lookup"><span data-stu-id="7ba60-541">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="7ba60-542">Especifica un nombre de ruta para generar la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-542">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="7ba60-543">Por lo general, no especifica un nombre de acción o controlador.</span><span class="sxs-lookup"><span data-stu-id="7ba60-543">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="7ba60-544">El Razor archivo siguiente genera un vínculo HTML a `Destination_Route` :</span><span class="sxs-lookup"><span data-stu-id="7ba60-544">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-no-locrazor"></a><span data-ttu-id="7ba60-545">Generar direcciones URL en HTML y Razor</span><span class="sxs-lookup"><span data-stu-id="7ba60-545">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="7ba60-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> proporciona los <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> métodos [HTML. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) y [HTML. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) para generar `<form>` `<a>` los elementos y, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="7ba60-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="7ba60-547">Estos métodos usan el método [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) para generar una dirección URL y aceptan argumentos similares.</span><span class="sxs-lookup"><span data-stu-id="7ba60-547">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="7ba60-548">Los métodos `Url.RouteUrl` complementarios de `HtmlHelper` son `Html.BeginRouteForm` y `Html.RouteLink`, cuya funcionalidad es similar.</span><span class="sxs-lookup"><span data-stu-id="7ba60-548">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="7ba60-549">Las TagHelper generan direcciones URL a través de la TagHelper `form` y la TagHelper `<a>`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-549">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="7ba60-550">Ambos usan `IUrlHelper` para su implementación.</span><span class="sxs-lookup"><span data-stu-id="7ba60-550">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="7ba60-551">Consulte [aplicaciones auxiliares de etiquetas en formularios](xref:mvc/views/working-with-forms) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="7ba60-551">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="7ba60-552">Dentro de las vistas, `IUrlHelper` está disponible a través de la propiedad `Url` para una generación de direcciones URL ad hoc no cubierta por los pasos anteriores.</span><span class="sxs-lookup"><span data-stu-id="7ba60-552">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="7ba60-553">Generación de direcciones URL en los resultados de la acción</span><span class="sxs-lookup"><span data-stu-id="7ba60-553">URL generation in Action Results</span></span>

<span data-ttu-id="7ba60-554">En los ejemplos anteriores se mostró el uso `IUrlHelper` de en un controlador.</span><span class="sxs-lookup"><span data-stu-id="7ba60-554">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="7ba60-555">El uso más común de un controlador es generar una dirección URL como parte de un resultado de acción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-555">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="7ba60-556">Las clases base <xref:Microsoft.AspNetCore.Mvc.ControllerBase> y <xref:Microsoft.AspNetCore.Mvc.Controller> proporcionan métodos de conveniencia para los resultados de acción que hacen referencia a otra acción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-556">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="7ba60-557">Un uso típico consiste en redirigir después de aceptar la entrada del usuario:</span><span class="sxs-lookup"><span data-stu-id="7ba60-557">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="7ba60-558">Los métodos del generador de resultados de la acción como <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> y <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> siguen un patrón similar a los métodos de `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-558">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="7ba60-559">Caso especial para rutas convencionales dedicadas</span><span class="sxs-lookup"><span data-stu-id="7ba60-559">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="7ba60-560">El [enrutamiento convencional](#cr) puede usar un tipo especial de definición de ruta denominada [ruta convencional dedicada](#dcr).</span><span class="sxs-lookup"><span data-stu-id="7ba60-560">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="7ba60-561">En el ejemplo siguiente, la ruta denominada `blog` es una ruta convencional dedicada:</span><span class="sxs-lookup"><span data-stu-id="7ba60-561">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="7ba60-562">Con las definiciones de ruta anteriores, `Url.Action("Index", "Home")` genera la ruta de acceso de la dirección URL `/` mediante la `default` ruta, pero ¿por qué?</span><span class="sxs-lookup"><span data-stu-id="7ba60-562">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="7ba60-563">Se puede suponer que los valores de ruta `{ controller = Home, action = Index }` son suficientes para generar una dirección URL utilizando `blog`, con el resultado `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-563">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="7ba60-564">Las [rutas convencionales dedicadas](#dcr) dependen de un comportamiento especial de los valores predeterminados que no tienen un parámetro de ruta correspondiente que impide que la ruta sea demasiado [expansiva](xref:fundamentals/routing#greedy) con generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-564">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="7ba60-565">En este caso, los valores predeterminados son `{ controller = Blog, action = Article }`, y ni `controller` ni `action` aparecen como un parámetro de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-565">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="7ba60-566">Cuando el enrutamiento realiza la generación de direcciones URL, los valores proporcionados deben coincidir con los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="7ba60-566">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="7ba60-567">`blog`Se produce un error en la generación de direcciones URL mediante los valores `{ controller = Home, action = Index }` no coinciden `{ controller = Blog, action = Article }` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-567">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="7ba60-568">Después, el enrutamiento vuelve para probar `default`, operación que se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="7ba60-568">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="7ba60-569">Áreas</span><span class="sxs-lookup"><span data-stu-id="7ba60-569">Areas</span></span>

<span data-ttu-id="7ba60-570">Las [áreas](xref:mvc/controllers/areas) son una característica de MVC que se usa para organizar la funcionalidad relacionada en un grupo como independiente:</span><span class="sxs-lookup"><span data-stu-id="7ba60-570">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="7ba60-571">Espacio de nombres de enrutamiento para las acciones de controlador.</span><span class="sxs-lookup"><span data-stu-id="7ba60-571">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="7ba60-572">Estructura de carpetas para las vistas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-572">Folder structure for views.</span></span>

<span data-ttu-id="7ba60-573">El uso de áreas permite que una aplicación tenga varios controladores con el mismo nombre, siempre y cuando tengan áreas diferentes.</span><span class="sxs-lookup"><span data-stu-id="7ba60-573">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="7ba60-574">El uso de áreas crea una jerarquía para el enrutamiento mediante la adición de otro parámetro de ruta, `area`, a `controller` y `action`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-574">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="7ba60-575">En esta sección se describe cómo interactúa el enrutamiento con las áreas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-575">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="7ba60-576">Vea [áreas](xref:mvc/controllers/areas) para obtener más información sobre cómo se usan las áreas con las vistas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-576">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="7ba60-577">En el ejemplo siguiente se configura MVC para usar la ruta convencional predeterminada y una `area` ruta para un `area` denominado `Blog` :</span><span class="sxs-lookup"><span data-stu-id="7ba60-577">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="7ba60-578">En el código anterior, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> se llama a para crear el `"blog_route"` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-578">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="7ba60-579">El segundo parámetro, `"Blog"` , es el nombre del área.</span><span class="sxs-lookup"><span data-stu-id="7ba60-579">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="7ba60-580">Al buscar una coincidencia con una ruta de dirección URL como `/Manage/Users/AddUser` , la `"blog_route"` ruta genera los valores de ruta `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-580">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="7ba60-581">El `area` valor de ruta se genera mediante un valor predeterminado para `area` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-581">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="7ba60-582">La ruta creada por `MapAreaControllerRoute` es equivalente a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="7ba60-582">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="7ba60-583">`MapAreaControllerRoute` utiliza el nombre de área proporcionado, que en este caso es `Blog`, para crear una ruta con un valor predeterminado y una restricción para `area`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-583">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="7ba60-584">El valor predeterminado garantiza que la ruta siempre produce `{ area = Blog, ... }`; la restricción requiere el valor `{ area = Blog, ... }` para la generación de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-584">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="7ba60-585">El enrutamiento convencional depende del orden.</span><span class="sxs-lookup"><span data-stu-id="7ba60-585">Conventional routing is order-dependent.</span></span> <span data-ttu-id="7ba60-586">En general, las rutas con áreas deben colocarse anteriormente ya que son más específicas que las rutas sin un área.</span><span class="sxs-lookup"><span data-stu-id="7ba60-586">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="7ba60-587">Con el ejemplo anterior, los valores de ruta `{ area = Blog, controller = Users, action = AddUser }` coinciden con la siguiente acción:</span><span class="sxs-lookup"><span data-stu-id="7ba60-587">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="7ba60-588">El atributo [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) es lo que denota un controlador como parte de un área.</span><span class="sxs-lookup"><span data-stu-id="7ba60-588">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="7ba60-589">Este controlador está en el `Blog` área.</span><span class="sxs-lookup"><span data-stu-id="7ba60-589">This controller is in the `Blog` area.</span></span> <span data-ttu-id="7ba60-590">Los controladores sin un `[Area]` atributo no son miembros de ningún área y no **not** coinciden cuando el `area` enrutamiento proporciona el valor de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-590">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="7ba60-591">En el ejemplo siguiente, solo el primer controlador enumerado puede coincidir con los valores de ruta `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-591">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="7ba60-592">Aquí se muestra el espacio de nombres de cada controlador por integridad.</span><span class="sxs-lookup"><span data-stu-id="7ba60-592">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="7ba60-593">Si los controladores anteriores usan el mismo espacio de nombres, se generaría un error del compilador.</span><span class="sxs-lookup"><span data-stu-id="7ba60-593">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="7ba60-594">Los espacios de nombres de clase no tienen ningún efecto en el enrutamiento de MVC.</span><span class="sxs-lookup"><span data-stu-id="7ba60-594">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="7ba60-595">Los dos primeros controladores son miembros de las áreas y solo coinciden cuando el valor de ruta `area` proporciona su respectivo nombre de área.</span><span class="sxs-lookup"><span data-stu-id="7ba60-595">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="7ba60-596">El tercer controlador no es miembro de ningún área y solo puede coincidir cuando el enrutamiento no proporciona ningún valor para `area`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-596">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="7ba60-597">En términos de búsqueda de coincidencias de *ningún valor* , la ausencia del valor `area` es igual que si el valor de `area` fuese null o una cadena vacía.</span><span class="sxs-lookup"><span data-stu-id="7ba60-597">In terms of matching *no value* , the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="7ba60-598">Al ejecutar una acción dentro de un área, el valor de ruta de `area` está disponible como [valor ambiente](#ambient) para el enrutamiento que se va a usar para la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-598">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="7ba60-599">Esto significa que, de forma predeterminada, las áreas actúan de forma *adhesiva* para la generación de direcciones URL, tal como se muestra en el ejemplo siguiente.</span><span class="sxs-lookup"><span data-stu-id="7ba60-599">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="7ba60-600">El código siguiente genera una dirección URL para `/Zebra/Users/AddUser` :</span><span class="sxs-lookup"><span data-stu-id="7ba60-600">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="7ba60-601">Definición de la acción</span><span class="sxs-lookup"><span data-stu-id="7ba60-601">Action definition</span></span>

<span data-ttu-id="7ba60-602">Los métodos públicos de un controlador, excepto aquellos con el atributo no [Action](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) , son acciones.</span><span class="sxs-lookup"><span data-stu-id="7ba60-602">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="7ba60-603">Código de ejemplo</span><span class="sxs-lookup"><span data-stu-id="7ba60-603">Sample code</span></span>

* [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]
* <span data-ttu-id="7ba60-604">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7ba60-604">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7ba60-605">ASP.NET Core MVC utiliza el [middleware](xref:fundamentals/middleware/index) de enrutamiento para buscar las direcciones URL de las solicitudes entrantes y asignarlas a acciones.</span><span class="sxs-lookup"><span data-stu-id="7ba60-605">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="7ba60-606">Las rutas se definen en el código de inicio o los atributos.</span><span class="sxs-lookup"><span data-stu-id="7ba60-606">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="7ba60-607">Las rutas describen cómo se deben asociar las rutas de dirección URL a las acciones.</span><span class="sxs-lookup"><span data-stu-id="7ba60-607">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="7ba60-608">Las rutas también se usan para generar direcciones URL (para vínculos) enviadas en las respuestas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-608">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="7ba60-609">Las acciones se enrutan bien mediante convención o bien mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="7ba60-609">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="7ba60-610">Colocar una ruta en el controlador o la acción hace que se enrute mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="7ba60-610">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="7ba60-611">Consulte [Enrutamiento mixto](#routing-mixed-ref-label) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="7ba60-611">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="7ba60-612">Este documento explica las interacciones entre MVC y el enrutamiento, así como el uso que las aplicaciones MVC suelen hacer de las características de enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="7ba60-612">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="7ba60-613">Consulte [Enrutamiento](xref:fundamentals/routing) para obtener más información sobre enrutamiento avanzado.</span><span class="sxs-lookup"><span data-stu-id="7ba60-613">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="7ba60-614">Configurar el middleware de enrutamiento</span><span class="sxs-lookup"><span data-stu-id="7ba60-614">Setting up Routing Middleware</span></span>

<span data-ttu-id="7ba60-615">En su método *Configure* puede ver código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="7ba60-615">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7ba60-616">Dentro de la llamada a `UseMvc`, `MapRoute` se utiliza para crear una ruta única, a la que nos referiremos como la ruta `default`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-616">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="7ba60-617">La mayoría de las aplicaciones MVC usarán una ruta con una plantilla similar a la ruta `default`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-617">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="7ba60-618">La plantilla de ruta `"{controller=Home}/{action=Index}/{id?}"` puede coincidir con una ruta de dirección URL como `/Products/Details/5` y extraerá los valores de ruta `{ controller = Products, action = Details, id = 5 }` mediante la conversión de la ruta en tokens.</span><span class="sxs-lookup"><span data-stu-id="7ba60-618">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="7ba60-619">MVC intentará encontrar un controlador denominado `ProductsController` y ejecutar la acción `Details`:</span><span class="sxs-lookup"><span data-stu-id="7ba60-619">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="7ba60-620">Tenga en cuenta que, en este ejemplo, el enlace de modelos usaría el valor de `id = 5` para establecer el parámetro `id` en `5` al invocar esta acción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-620">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="7ba60-621">Consulte [Enlace de modelos](../models/model-binding.md) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="7ba60-621">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="7ba60-622">Utilizando la ruta `default`:</span><span class="sxs-lookup"><span data-stu-id="7ba60-622">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="7ba60-623">La plantilla de ruta:</span><span class="sxs-lookup"><span data-stu-id="7ba60-623">The route template:</span></span>

* <span data-ttu-id="7ba60-624">`{controller=Home}` define `Home` como el valor `controller` predeterminado</span><span class="sxs-lookup"><span data-stu-id="7ba60-624">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="7ba60-625">`{action=Index}` define `Index` como el valor `action` predeterminado</span><span class="sxs-lookup"><span data-stu-id="7ba60-625">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="7ba60-626">`{id?}` define `id` como opcional</span><span class="sxs-lookup"><span data-stu-id="7ba60-626">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="7ba60-627">No es necesario que los parámetros de ruta opcionales y predeterminados estén presentes en la ruta de dirección URL para una coincidencia.</span><span class="sxs-lookup"><span data-stu-id="7ba60-627">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="7ba60-628">Consulte [Referencia de plantilla de ruta](xref:fundamentals/routing#route-template-reference) para obtener una descripción detallada de la sintaxis de la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-628">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="7ba60-629">`"{controller=Home}/{action=Index}/{id?}"` puede coincidir con la ruta de dirección URL `/` y generará los valores de ruta `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-629">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="7ba60-630">Los valores de `controller` y `action` utilizan los valores predeterminados, `id` no genera un valor porque no hay ningún segmento correspondiente en la ruta de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-630">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="7ba60-631">MVC utilizaría estos valores de ruta para seleccionar `HomeController` y la acción `Index`:</span><span class="sxs-lookup"><span data-stu-id="7ba60-631">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="7ba60-632">Usando esta definición de controlador y la plantilla de ruta, la acción `HomeController.Index` se ejecutaría para cualquiera de las rutas de dirección URL siguientes:</span><span class="sxs-lookup"><span data-stu-id="7ba60-632">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="7ba60-633">El método de conveniencia `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="7ba60-633">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="7ba60-634">Se puede usar para reemplazar:</span><span class="sxs-lookup"><span data-stu-id="7ba60-634">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7ba60-635">`UseMvc` y `UseMvcWithDefaultRoute` agregan una instancia de `RouterMiddleware` a la canalización de middleware.</span><span class="sxs-lookup"><span data-stu-id="7ba60-635">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="7ba60-636">MVC no interactúa directamente con middleware y usa el enrutamiento para controlar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="7ba60-636">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="7ba60-637">MVC está conectado a las rutas a través de una instancia de `MvcRouteHandler`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-637">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="7ba60-638">El código dentro de `UseMvc` es similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="7ba60-638">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="7ba60-639">`UseMvc` no define directamente ninguna ruta, sino que agrega un marcador de posición a la colección de rutas para la ruta `attribute`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-639">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="7ba60-640">La sobrecarga `UseMvc(Action<IRouteBuilder>)` le permite agregar sus propias rutas y también admite el enrutamiento mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="7ba60-640">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="7ba60-641">`UseMvc` y todas sus variaciones agregan un marcador de posición para la ruta de atributo (el enrutamiento mediante atributos siempre está disponible, independientemente de cómo configure `UseMvc`).</span><span class="sxs-lookup"><span data-stu-id="7ba60-641">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="7ba60-642">`UseMvcWithDefaultRoute` define una ruta predeterminada y admite el enrutamiento mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="7ba60-642">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="7ba60-643">La sección [Enrutamiento mediante atributos](#attribute-routing-ref-label) incluye más detalles sobre este tipo de enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="7ba60-643">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="7ba60-644">Enrutamiento convencional</span><span class="sxs-lookup"><span data-stu-id="7ba60-644">Conventional routing</span></span>

<span data-ttu-id="7ba60-645">La ruta `default`:</span><span class="sxs-lookup"><span data-stu-id="7ba60-645">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="7ba60-646">El código anterior es un ejemplo de enrutamiento convencional.</span><span class="sxs-lookup"><span data-stu-id="7ba60-646">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="7ba60-647">Este estilo se denomina enrutamiento convencional porque establece una *Convención* para las rutas de dirección URL:</span><span class="sxs-lookup"><span data-stu-id="7ba60-647">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="7ba60-648">El primer segmento de la ruta de acceso se asigna al nombre del controlador.</span><span class="sxs-lookup"><span data-stu-id="7ba60-648">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="7ba60-649">El segundo se asigna al nombre de la acción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-649">The second maps to the action name.</span></span>
* <span data-ttu-id="7ba60-650">El tercer segmento se utiliza para un opcional `id` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-650">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="7ba60-651">`id` asigna a una entidad del modelo.</span><span class="sxs-lookup"><span data-stu-id="7ba60-651">`id` maps to a model entity.</span></span>

<span data-ttu-id="7ba60-652">Mediante esta ruta `default`, la ruta de dirección URL `/Products/List` se asigna a la acción `ProductsController.List`, y `/Blog/Article/17` se asigna a `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-652">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="7ba60-653">Esta asignación **solo** se basa en los nombres de acción y controlador, y no en espacios de nombres, ubicaciones de archivos de origen ni parámetros de método.</span><span class="sxs-lookup"><span data-stu-id="7ba60-653">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="7ba60-654">Usar el enrutamiento convencional con la ruta predeterminada permite generar rápidamente la aplicación sin necesidad de elaborar un nuevo patrón de dirección URL para cada acción que se define.</span><span class="sxs-lookup"><span data-stu-id="7ba60-654">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="7ba60-655">Para una aplicación con acciones de estilo CRUD, la coherencia en las direcciones URL en todos los controladores puede ayudar a simplificar el código y hacer que la interfaz de usuario sea más predecible.</span><span class="sxs-lookup"><span data-stu-id="7ba60-655">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="7ba60-656">`id` se define como opcional mediante la plantilla de ruta, lo que significa que las acciones se pueden ejecutar sin el identificador proporcionado como parte de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-656">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="7ba60-657">Normalmente lo que ocurrirá si `id` se omite de la dirección URL es que el enlace de modelos establecerá en `0` su valor y, como resultado, no se encontrará ninguna entidad en la base de datos que coincida con `id == 0`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-657">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="7ba60-658">El enrutamiento mediante atributos proporciona un mayor control que permite requerir el identificador para algunas acciones y para otras no.</span><span class="sxs-lookup"><span data-stu-id="7ba60-658">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="7ba60-659">Por convención, la documentación incluirá parámetros opcionales como `id` cuando sea más probable que su uso sea correcto.</span><span class="sxs-lookup"><span data-stu-id="7ba60-659">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="7ba60-660">Varias rutas</span><span class="sxs-lookup"><span data-stu-id="7ba60-660">Multiple routes</span></span>

<span data-ttu-id="7ba60-661">Para agregar varias rutas dentro de `UseMvc`, agregue más llamadas a `MapRoute`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-661">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="7ba60-662">Hacerlo le permite definir varias convenciones o agregar rutas convencionales que se dedican a una acción específica, como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7ba60-662">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7ba60-663">Aquí, la ruta `blog` es una *ruta convencional dedicada* , lo que significa que utiliza el sistema de enrutamiento convencional, pero que está dedicada a una acción específica.</span><span class="sxs-lookup"><span data-stu-id="7ba60-663">The `blog` route here is a *dedicated conventional route* , meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="7ba60-664">Puesto que `controller` y `action` no aparecen en la plantilla de ruta como parámetros, solo pueden tener los valores predeterminados y, por tanto, esta ruta siempre se asignará a la acción `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-664">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="7ba60-665">Las rutas de la colección de rutas están ordenadas y se procesan en el orden en que se hayan agregado.</span><span class="sxs-lookup"><span data-stu-id="7ba60-665">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="7ba60-666">Por tanto, la ruta `blog` de este ejemplo se intentará antes que la ruta `default`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-666">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="7ba60-667">Las *rutas convencionales dedicadas* suelen usar parámetros de ruta **catch-all** como `{*article}` para capturar la parte restante de la ruta de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-667">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="7ba60-668">Esto puede hacer que la ruta sea "demasiado expansiva", lo que significa que coincidirá con direcciones URL que se pretendía que coincidieran con otras rutas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-668">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="7ba60-669">Coloque las rutas "expansivas" más adelante en la tabla de rutas para resolver este problema.</span><span class="sxs-lookup"><span data-stu-id="7ba60-669">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="7ba60-670">Reserva</span><span class="sxs-lookup"><span data-stu-id="7ba60-670">Fallback</span></span>

<span data-ttu-id="7ba60-671">Como parte del procesamiento de la solicitud, MVC comprobará que los valores de ruta se pueden usar para buscar un controlador y la acción en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7ba60-671">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="7ba60-672">Si los valores de ruta no coinciden con una acción, entonces la ruta no se considera una coincidencia y se intentará la ruta siguiente.</span><span class="sxs-lookup"><span data-stu-id="7ba60-672">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="7ba60-673">Esto se denomina *reserva* , y se ha diseñado para simplificar los casos donde se superponen rutas convencionales.</span><span class="sxs-lookup"><span data-stu-id="7ba60-673">This is called *fallback* , and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="7ba60-674">Eliminar la ambigüedad de acciones</span><span class="sxs-lookup"><span data-stu-id="7ba60-674">Disambiguating actions</span></span>

<span data-ttu-id="7ba60-675">Cuando dos acciones coinciden en el enrutamiento, MVC debe eliminar la ambigüedad para elegir el candidato "recomendado", o de lo contrario se produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-675">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="7ba60-676">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7ba60-676">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="7ba60-677">Este controlador define dos acciones que coincidirían con la ruta de dirección URL `/Products/Edit/17` y los datos de ruta `{ controller = Products, action = Edit, id = 17 }`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-677">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="7ba60-678">Se trata de un patrón típico para controladores MVC donde `Edit(int)` muestra un formulario para editar un producto, y `Edit(int, Product)` procesa el formulario expuesto.</span><span class="sxs-lookup"><span data-stu-id="7ba60-678">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="7ba60-679">Para hacer esto posible, MVC necesita seleccionar `Edit(int, Product)` cuando la solicitud es HTTP `POST` y `Edit(int)` cuando el verbo HTTP es cualquier otra cosa.</span><span class="sxs-lookup"><span data-stu-id="7ba60-679">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="7ba60-680">`HttpPostAttribute` ( `[HttpPost]` ) es una implementación de `IActionConstraint` que solo permitirá que la acción se seleccione cuando el verbo HTTP sea `POST`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-680">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="7ba60-681">La presencia de `IActionConstraint` hace que `Edit(int, Product)` sea una mejor coincidencia que `Edit(int)`, por lo que `Edit(int, Product)` se intentará en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="7ba60-681">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="7ba60-682">Solo necesitará escribir implementaciones de `IActionConstraint` personalizadas en escenarios especializados, pero es importante comprender el rol de atributos como `HttpPostAttribute` (para otros verbos HTTP se definen atributos similares).</span><span class="sxs-lookup"><span data-stu-id="7ba60-682">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="7ba60-683">En el enrutamiento convencional es común que las acciones utilicen el mismo nombre de acción cuando son parte de un flujo de trabajo `show form -> submit form`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-683">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="7ba60-684">La comodidad de este patrón será más evidente después de revisar la sección [Descripción de IActionConstraint](#understanding-iactionconstraint).</span><span class="sxs-lookup"><span data-stu-id="7ba60-684">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="7ba60-685">Si coinciden varias rutas y MVC no encuentra una ruta "recomendada", se produce una excepción `AmbiguousActionException`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-685">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="7ba60-686">Nombres de ruta</span><span class="sxs-lookup"><span data-stu-id="7ba60-686">Route names</span></span>

<span data-ttu-id="7ba60-687">Las cadenas `"blog"` y `"default"` de los ejemplos siguientes son nombres de ruta:</span><span class="sxs-lookup"><span data-stu-id="7ba60-687">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7ba60-688">Los nombres de ruta proporcionan un nombre lógico a la ruta para que la ruta con nombre pueda utilizarse en la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-688">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="7ba60-689">Esto simplifica en gran medida la creación de direcciones URL en los casos en que el orden de las rutas podría complicar la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-689">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="7ba60-690">Los nombres de ruta deben ser únicos en toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7ba60-690">Route names must be unique application-wide.</span></span>

<span data-ttu-id="7ba60-691">Los nombres de ruta no tienen ningún impacto en la coincidencia de direcciones URL ni en el control de las solicitudes; se utilizan únicamente para la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-691">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="7ba60-692">En [Enrutamiento](xref:fundamentals/routing) se incluye información más detallada sobre la generación de direcciones URL, como la generación de direcciones URL en asistentes específicos de MVC.</span><span class="sxs-lookup"><span data-stu-id="7ba60-692">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="7ba60-693">Enrutamiento mediante atributos</span><span class="sxs-lookup"><span data-stu-id="7ba60-693">Attribute routing</span></span>

<span data-ttu-id="7ba60-694">El enrutamiento mediante atributos utiliza un conjunto de atributos para asignar acciones directamente a las plantillas de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-694">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="7ba60-695">En el ejemplo siguiente, `app.UseMvc();` se utiliza en el método `Configure` y no se pasa ninguna ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-695">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="7ba60-696">`HomeController` coincidirá con un conjunto de direcciones URL similares a las que coincidirían con la ruta predeterminada `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="7ba60-696">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="7ba60-697">La acción `HomeController.Index()` se ejecutará para cualquiera de las rutas de dirección URL `/`, `/Home` o `/Home/Index`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-697">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="7ba60-698">Este ejemplo resalta una diferencia clave de programación entre el enrutamiento mediante atributos y el enrutamiento convencional.</span><span class="sxs-lookup"><span data-stu-id="7ba60-698">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="7ba60-699">El enrutamiento mediante atributos requiere más entradas para especificar una ruta; la ruta predeterminada convencional controla las rutas de manera más sucinta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-699">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="7ba60-700">Pero el enrutamiento mediante atributos permite (y requiere) un control preciso de las plantillas de ruta que se aplicarán a cada acción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-700">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="7ba60-701">Con el enrutamiento mediante atributos, el nombre del controlador y los nombres de acción **no** juegan ningún papel en la selección de las acciones.</span><span class="sxs-lookup"><span data-stu-id="7ba60-701">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="7ba60-702">Este ejemplo coincidirá con las mismas direcciones URL que el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="7ba60-702">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="7ba60-703">Las plantillas de ruta anteriores no definen parámetros de ruta para `action`, `area` y `controller`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-703">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="7ba60-704">De hecho, estos parámetros de ruta no se permiten en rutas de atributo.</span><span class="sxs-lookup"><span data-stu-id="7ba60-704">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="7ba60-705">Puesto que la plantilla de ruta ya está asociada a una acción, no tendría sentido analizar el nombre de acción de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-705">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="7ba60-706">Enrutamiento mediante atributos con atributos Http[Verb]</span><span class="sxs-lookup"><span data-stu-id="7ba60-706">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="7ba60-707">El enrutamiento mediante atributos también puede hacer uso de los atributos `Http[Verb]` como `HttpPostAttribute`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-707">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="7ba60-708">Todos estos atributos pueden aceptar una plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-708">All of these attributes can accept a route template.</span></span> <span data-ttu-id="7ba60-709">Este ejemplo muestra dos acciones que coinciden con la misma plantilla de ruta:</span><span class="sxs-lookup"><span data-stu-id="7ba60-709">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="7ba60-710">Para una ruta de dirección URL como `/products`, la acción `ProductsApi.ListProducts` se ejecutará cuando el verbo HTTP sea `GET` y `ProductsApi.CreateProduct` se ejecutará cuando el verbo HTTP sea `POST`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-710">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="7ba60-711">El enrutamiento mediante atributos primero busca una coincidencia de la dirección URL en el conjunto de plantillas de ruta que se definen mediante atributos de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-711">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="7ba60-712">Una vez que se encuentra una plantilla de ruta que coincide, las restricciones `IActionConstraint` se aplican para determinar qué acciones se pueden ejecutar.</span><span class="sxs-lookup"><span data-stu-id="7ba60-712">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="7ba60-713">Cuando se compila una API de REST, es poco frecuente que se quiera usar `[Route(...)]` en un método de acción, ya que la acción aceptará todos los métodos HTTP.</span><span class="sxs-lookup"><span data-stu-id="7ba60-713">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="7ba60-714">Es mejor usar `Http*Verb*Attributes` más específicos para precisar lo que es compatible con la API.</span><span class="sxs-lookup"><span data-stu-id="7ba60-714">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="7ba60-715">Se espera que los clientes de API de REST sepan qué rutas y verbos HTTP se asignan a determinadas operaciones lógicas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-715">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="7ba60-716">Puesto que una ruta de atributo se aplica a una acción específica, es fácil crear parámetros necesarios como parte de la definición de plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-716">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="7ba60-717">En este ejemplo, se requiere `id` como parte de la ruta de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-717">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="7ba60-718">La acción `ProductsApi.GetProduct(int)` se ejecutará para una ruta de dirección URL como `/products/3`, pero no para una ruta de dirección URL como `/products`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-718">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="7ba60-719">Consulte [Enrutamiento](xref:fundamentals/routing) para obtener una descripción completa de las plantillas de ruta y las opciones relacionadas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-719">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="7ba60-720">Nombre de ruta</span><span class="sxs-lookup"><span data-stu-id="7ba60-720">Route Name</span></span>

<span data-ttu-id="7ba60-721">El código siguiente define un *nombre de ruta* de `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="7ba60-721">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="7ba60-722">Los nombres de ruta se pueden utilizar para generar una dirección URL basada en una ruta específica.</span><span class="sxs-lookup"><span data-stu-id="7ba60-722">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="7ba60-723">Los nombres de ruta no tienen ningún impacto en el comportamiento de coincidencia de direcciones URL del enrutamiento, y solo se usan para la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-723">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="7ba60-724">Los nombres de ruta deben ser únicos en toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7ba60-724">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="7ba60-725">Compare esto con la *ruta predeterminada* convencional, que define el parámetro `id` como opcional (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="7ba60-725">Contrast this with the conventional *default route* , which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="7ba60-726">Esta capacidad de especificar con precisión las API tiene sus ventajas, como permitir que `/products` y `/products/5` se envíen a acciones diferentes.</span><span class="sxs-lookup"><span data-stu-id="7ba60-726">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="7ba60-727">Combinación de rutas</span><span class="sxs-lookup"><span data-stu-id="7ba60-727">Combining routes</span></span>

<span data-ttu-id="7ba60-728">Para que el enrutamiento mediante atributos sea menos repetitivo, los atributos de ruta del controlador se combinan con los atributos de ruta de las acciones individuales.</span><span class="sxs-lookup"><span data-stu-id="7ba60-728">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="7ba60-729">Las plantillas de ruta definidas en el controlador se anteponen a las plantillas de ruta de las acciones.</span><span class="sxs-lookup"><span data-stu-id="7ba60-729">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="7ba60-730">La colocación de un atributo de ruta en el controlador hace que **todas** las acciones del controlador usen el enrutamiento mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="7ba60-730">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="7ba60-731">En este ejemplo, la ruta de dirección URL `/products` puede coincidir con `ProductsApi.ListProducts` y la ruta de dirección URL `/products/5` puede coincidir con `ProductsApi.GetProduct(int)`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-731">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="7ba60-732">Ambas acciones solo coinciden con el método HTTP `GET` porque están marcadas con `HttpGetAttribute`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-732">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="7ba60-733">Las plantillas de ruta aplicadas a una acción que comienzan por `/` o `~/` no se combinan con las plantillas de ruta que se aplican al controlador.</span><span class="sxs-lookup"><span data-stu-id="7ba60-733">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="7ba60-734">En este ejemplo coinciden un conjunto de rutas de dirección URL similares a la *ruta predeterminada*.</span><span class="sxs-lookup"><span data-stu-id="7ba60-734">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="7ba60-735">Ordenación de rutas de atributo</span><span class="sxs-lookup"><span data-stu-id="7ba60-735">Ordering attribute routes</span></span>

<span data-ttu-id="7ba60-736">A diferencia de las rutas convencionales, que se ejecutan en un orden definido, el enrutamiento de atributos genera un árbol y coincide con todas las rutas simultáneamente.</span><span class="sxs-lookup"><span data-stu-id="7ba60-736">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="7ba60-737">Es como si las entradas de ruta se colocasen en un orden ideal; las rutas más específicas tienen la oportunidad de ejecutarse antes que las rutas más generales.</span><span class="sxs-lookup"><span data-stu-id="7ba60-737">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="7ba60-738">Por ejemplo, una ruta como `blog/search/{topic}` es más específica que una ruta como `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-738">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="7ba60-739">Desde un punto de vista lógico, la ruta `blog/search/{topic}` se ejecuta en primer lugar de forma predeterminada, ya que ese es el único orden significativo.</span><span class="sxs-lookup"><span data-stu-id="7ba60-739">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="7ba60-740">En el enrutamiento convencional, el desarrollador es responsable de colocar las rutas en el orden deseado.</span><span class="sxs-lookup"><span data-stu-id="7ba60-740">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="7ba60-741">Las rutas de atributo pueden configurar un orden mediante la propiedad `Order` de todos los atributos de ruta proporcionados por el marco.</span><span class="sxs-lookup"><span data-stu-id="7ba60-741">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="7ba60-742">Las rutas se procesan de acuerdo con el orden ascendente de la propiedad `Order`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-742">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="7ba60-743">El orden predeterminado es `0`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-743">The default order is `0`.</span></span> <span data-ttu-id="7ba60-744">Si una ruta se configura con `Order = -1`, se ejecutará antes que las rutas que establecen un orden.</span><span class="sxs-lookup"><span data-stu-id="7ba60-744">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="7ba60-745">Si una ruta se configura con `Order = 1`, se ejecutará después del orden de rutas predeterminado.</span><span class="sxs-lookup"><span data-stu-id="7ba60-745">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="7ba60-746">Evite depender de `Order`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-746">Avoid depending on `Order`.</span></span> <span data-ttu-id="7ba60-747">Si su espacio de direcciones URL requiere unos valores de orden explícitos para un enrutamiento correcto, es probable que también sea confuso para los clientes.</span><span class="sxs-lookup"><span data-stu-id="7ba60-747">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="7ba60-748">Por lo general, el enrutamiento mediante atributos seleccionará la ruta correcta con la coincidencia de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-748">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="7ba60-749">Si el orden predeterminado que se usa para la generación de direcciones URL no funciona, normalmente es más sencillo utilizar el nombre de ruta como una invalidación que aplicar la propiedad `Order`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-749">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="7ba60-750">El enrutamiento de Razor Pages y el del controlador MVC comparten una implementación.</span><span class="sxs-lookup"><span data-stu-id="7ba60-750">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="7ba60-751">La información sobre el orden de la ruta en los temas de las Razor páginas está disponible en [ Razor las páginas ruta y convenciones de aplicación: orden de ruta](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="7ba60-751">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="7ba60-752">Reemplazo de tokens en plantillas de ruta ([controller], [action], [area])</span><span class="sxs-lookup"><span data-stu-id="7ba60-752">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="7ba60-753">Para mayor comodidad, las rutas de atributo admiten el *reemplazo de tokens* mediante la inclusión de un token entre corchetes ( `[` , `]` ).</span><span class="sxs-lookup"><span data-stu-id="7ba60-753">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="7ba60-754">Los tokens `[action]`, `[area]` y `[controller]` se reemplazan con los valores del nombre de la acción, el nombre del área y el nombre del controlador de la acción donde se define la ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-754">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="7ba60-755">En este ejemplo, las acciones coinciden con las rutas de dirección URL, tal como se describe en los comentarios:</span><span class="sxs-lookup"><span data-stu-id="7ba60-755">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="7ba60-756">El reemplazo del token se produce como último paso de la creación de las rutas de atributo.</span><span class="sxs-lookup"><span data-stu-id="7ba60-756">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="7ba60-757">El ejemplo anterior se comportará igual que el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7ba60-757">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="7ba60-758">Las rutas de atributo también se pueden combinar con la herencia.</span><span class="sxs-lookup"><span data-stu-id="7ba60-758">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="7ba60-759">Esto es especialmente eficaz si se combina con el reemplazo de token.</span><span class="sxs-lookup"><span data-stu-id="7ba60-759">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="7ba60-760">El reemplazo de token también se aplica a los nombres de ruta definidos por las rutas de atributo.</span><span class="sxs-lookup"><span data-stu-id="7ba60-760">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="7ba60-761">`[Route("[controller]/[action]", Name="[controller]_[action]")]` genera un nombre de ruta único para cada acción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-761">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="7ba60-762">Para que el delimitador de reemplazo de token `[` o `]` coincida, repita el carácter (`[[` o `]]`) para usarlo como secuencia de escape.</span><span class="sxs-lookup"><span data-stu-id="7ba60-762">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="7ba60-763">Usar un transformador de parámetro para personalizar el reemplazo de tokens</span><span class="sxs-lookup"><span data-stu-id="7ba60-763">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="7ba60-764">El reemplazo de tokens se puede personalizarse mediante un transformador de parámetro.</span><span class="sxs-lookup"><span data-stu-id="7ba60-764">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="7ba60-765">Un transformador de parámetro implementa `IOutboundParameterTransformer` y transforma el valor de parámetros.</span><span class="sxs-lookup"><span data-stu-id="7ba60-765">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="7ba60-766">Por ejemplo, un transformador de parámetros `SlugifyParameterTransformer` personalizado cambia el valor de ruta `SubscriptionManagement` a `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-766">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="7ba60-767">`RouteTokenTransformerConvention` es una convención de modelo de aplicación que:</span><span class="sxs-lookup"><span data-stu-id="7ba60-767">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="7ba60-768">Aplica un transformador de parámetro a todas las rutas de atributo en una aplicación.</span><span class="sxs-lookup"><span data-stu-id="7ba60-768">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="7ba60-769">Personaliza los valores de token de ruta de atributo que se reemplazan.</span><span class="sxs-lookup"><span data-stu-id="7ba60-769">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="7ba60-770">`RouteTokenTransformerConvention` está registrado como una opción en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-770">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="7ba60-771">Varias rutas</span><span class="sxs-lookup"><span data-stu-id="7ba60-771">Multiple Routes</span></span>

<span data-ttu-id="7ba60-772">El enrutamiento mediante atributos permite definir varias rutas que llegan a la misma acción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-772">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="7ba60-773">El uso más común de esto es imitar el comportamiento de la *ruta convencional predeterminada* tal como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7ba60-773">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="7ba60-774">La colocación de varios atributos de ruta en el controlador significa que cada uno de ellos se combinará con cada uno de los atributos de ruta en los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-774">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="7ba60-775">Cuando en una acción se colocan varios atributos de ruta (que implementan `IActionConstraint`), cada restricción de acción se combina con la plantilla de ruta del atributo que lo define.</span><span class="sxs-lookup"><span data-stu-id="7ba60-775">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="7ba60-776">Si bien el uso de varias rutas en acciones puede parecer eficaz, es mejor que el espacio de direcciones URL de la aplicación sea sencillo y esté bien definido.</span><span class="sxs-lookup"><span data-stu-id="7ba60-776">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="7ba60-777">Utilice varias rutas en acciones solo cuando sea necesario, por ejemplo, para admitir a clientes existentes.</span><span class="sxs-lookup"><span data-stu-id="7ba60-777">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="7ba60-778">Especificación de parámetros opcionales de ruta de atributo, valores predeterminados y restricciones</span><span class="sxs-lookup"><span data-stu-id="7ba60-778">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="7ba60-779">Las rutas de atributo admiten la misma sintaxis en línea que las rutas convencionales para especificar parámetros opcionales, valores predeterminados y restricciones.</span><span class="sxs-lookup"><span data-stu-id="7ba60-779">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="7ba60-780">Consulte [Referencia de plantilla de ruta](xref:fundamentals/routing#route-template-reference) para obtener una descripción detallada de la sintaxis de la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-780">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="7ba60-781">Atributos de ruta personalizados con `IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="7ba60-781">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="7ba60-782">Todos los atributos de ruta proporcionados en el marco (`[Route(...)]`, `[HttpGet(...)]`, etc.) implementan la interfaz `IRouteTemplateProvider`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-782">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="7ba60-783">Al iniciarse la aplicación, MVC busca atributos en las clases de controlador y los métodos de acción, y usa los que implementan `IRouteTemplateProvider` para generar el conjunto inicial de rutas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-783">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="7ba60-784">Implemente `IRouteTemplateProvider` para definir sus propios atributos de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-784">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="7ba60-785">Cada `IRouteTemplateProvider` le permite definir una única ruta con una plantilla de ruta, un orden y un nombre personalizados:</span><span class="sxs-lookup"><span data-stu-id="7ba60-785">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="7ba60-786">El atributo del ejemplo anterior establece automáticamente `Template` en `"api/[controller]"` cuando se aplica `[MyApiController]`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-786">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="7ba60-787">Uso del modelo de aplicación para personalizar las rutas de atributo</span><span class="sxs-lookup"><span data-stu-id="7ba60-787">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="7ba60-788">El *modelo de aplicación* es un modelo de objetos creado durante el inicio con todos los metadatos utilizados por MVC para enrutar y ejecutar las acciones.</span><span class="sxs-lookup"><span data-stu-id="7ba60-788">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="7ba60-789">El *modelo de aplicación* incluye todos los datos recopilados de los atributos de ruta (a través de `IRouteTemplateProvider`).</span><span class="sxs-lookup"><span data-stu-id="7ba60-789">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="7ba60-790">Puede escribir *convenciones* para modificar el modelo de aplicación en el momento del inicio y personalizar el comportamiento del enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="7ba60-790">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="7ba60-791">En esta sección se muestra un ejemplo sencillo de personalización del enrutamiento mediante el modelo de aplicación.</span><span class="sxs-lookup"><span data-stu-id="7ba60-791">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="7ba60-792">Enrutamiento mixto: enrutamiento mediante atributos frente a enrutamiento convencional</span><span class="sxs-lookup"><span data-stu-id="7ba60-792">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="7ba60-793">Las aplicaciones MVC pueden combinar el uso de enrutamiento convencional y enrutamiento mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="7ba60-793">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="7ba60-794">Es habitual usar las rutas convencionales para controladores que sirven páginas HTML para los exploradores, y el enrutamiento mediante atributos para los controladores que sirven las API de REST.</span><span class="sxs-lookup"><span data-stu-id="7ba60-794">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="7ba60-795">Las acciones se enrutan bien mediante convención o bien mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="7ba60-795">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="7ba60-796">Colocar una ruta en el controlador o la acción hace que se enrute mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="7ba60-796">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="7ba60-797">Las acciones que definen rutas de atributo no se pueden alcanzar a través de las rutas convencionales y viceversa.</span><span class="sxs-lookup"><span data-stu-id="7ba60-797">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="7ba60-798">**Cualquier** atributo de ruta en el controlador hace que todas las acciones del controlador se enruten mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="7ba60-798">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="7ba60-799">Lo que distingue los dos tipos de sistemas de enrutamiento es el proceso que se aplica después de que una dirección URL coincida con una plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-799">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="7ba60-800">En el enrutamiento convencional, los valores de ruta de la coincidencia se usan para elegir la acción y el controlador en una tabla de búsqueda de todas las acciones enrutadas convencionales.</span><span class="sxs-lookup"><span data-stu-id="7ba60-800">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="7ba60-801">En el enrutamiento mediante atributos, cada plantilla ya está asociada a una acción y no es necesaria ninguna otra búsqueda.</span><span class="sxs-lookup"><span data-stu-id="7ba60-801">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="7ba60-802">Segmentos complejos</span><span class="sxs-lookup"><span data-stu-id="7ba60-802">Complex segments</span></span>

<span data-ttu-id="7ba60-803">Los segmentos complejos (por ejemplo, `[Route("/dog{token}cat")]`), se procesan buscando coincidencias de literales de derecha a izquierda de un modo no expansivo.</span><span class="sxs-lookup"><span data-stu-id="7ba60-803">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="7ba60-804">Para ver una descripción, eche un vistazo al [código fuente](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296).</span><span class="sxs-lookup"><span data-stu-id="7ba60-804">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="7ba60-805">Para obtener más información, consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="7ba60-805">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="7ba60-806">Generación de direcciones URL</span><span class="sxs-lookup"><span data-stu-id="7ba60-806">URL Generation</span></span>

<span data-ttu-id="7ba60-807">Las aplicaciones MVC pueden usar características de generación de direcciones URL de enrutamiento para generar vínculos URL a las acciones.</span><span class="sxs-lookup"><span data-stu-id="7ba60-807">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="7ba60-808">La generación de direcciones URL elimina las direcciones URL codificadas de forma rígida, por lo que el código es más compacto y fácil de mantener.</span><span class="sxs-lookup"><span data-stu-id="7ba60-808">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="7ba60-809">Esta sección se centra en las características de generación de direcciones URL proporcionadas por MVC y solo aborda los conceptos básicos de su funcionamiento.</span><span class="sxs-lookup"><span data-stu-id="7ba60-809">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="7ba60-810">Consulte [Enrutamiento](xref:fundamentals/routing) para obtener una descripción detallada de la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-810">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="7ba60-811">La interfaz `IUrlHelper` es la pieza subyacente de la infraestructura entre MVC y el enrutamiento para la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-811">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="7ba60-812">Encontrará una instancia de `IUrlHelper` disponible a través de la propiedad `Url` en los controladores, las vistas y los componentes de vista.</span><span class="sxs-lookup"><span data-stu-id="7ba60-812">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="7ba60-813">En este ejemplo, la interfaz `IUrlHelper` se usa a través de la propiedad `Controller.Url` para generar una dirección URL a otra acción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-813">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="7ba60-814">Si la aplicación está usando la ruta convencional predeterminada, el valor de la variable `url` será la cadena de ruta de dirección URL `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-814">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="7ba60-815">Esta ruta de dirección URL se crea por enrutamiento mediante la combinación de los valores de ruta de la solicitud actual (valores de ambiente) con los valores pasados a `Url.Action`, y sustituyendo esos valores en la plantilla de ruta:</span><span class="sxs-lookup"><span data-stu-id="7ba60-815">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="7ba60-816">El valor de cada uno de los parámetros de ruta incluidos en la plantilla de ruta se sustituye por nombres que coincidan con los valores y los valores de ambiente.</span><span class="sxs-lookup"><span data-stu-id="7ba60-816">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="7ba60-817">Si un parámetro de ruta no tiene un valor, puede utilizar un valor predeterminado en caso de tenerlo, o se puede omitir si es opcional (como en el caso de `id` en este ejemplo).</span><span class="sxs-lookup"><span data-stu-id="7ba60-817">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="7ba60-818">La generación de direcciones URL producirá un error si cualquiera de los parámetros de ruta necesarios no tiene un valor correspondiente.</span><span class="sxs-lookup"><span data-stu-id="7ba60-818">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="7ba60-819">Si se produce un error en la generación de direcciones URL para una ruta, se prueba con la ruta siguiente hasta que se hayan probado todas las rutas o se encuentra una coincidencia.</span><span class="sxs-lookup"><span data-stu-id="7ba60-819">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="7ba60-820">En el ejemplo anterior de `Url.Action` se supone que el enrutamiento es convencional, pero la generación de direcciones URL funciona del mismo modo con el enrutamiento mediante atributos, si bien los conceptos son diferentes.</span><span class="sxs-lookup"><span data-stu-id="7ba60-820">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="7ba60-821">En el enrutamiento convencional, los valores de ruta se usan para expandir una plantilla; los valores de ruta de `controller` y `action` suelen aparecer en esa plantilla. Esto es válido porque las direcciones URL que coinciden con el enrutamiento se adhieren a una *convención*.</span><span class="sxs-lookup"><span data-stu-id="7ba60-821">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="7ba60-822">En el enrutamiento mediante atributos, no está permitido que los valores de ruta de `controller` y `action` aparezcan en la plantilla. En vez de eso, se utilizan para buscar la plantilla que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="7ba60-822">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="7ba60-823">Este ejemplo utiliza la enrutamiento mediante atributos:</span><span class="sxs-lookup"><span data-stu-id="7ba60-823">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="7ba60-824">MVC genera una tabla de búsqueda de todas las acciones enrutadas mediante atributos y hará coincidir los valores `controller` y `action` para seleccionar la plantilla de ruta que se usará para la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-824">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="7ba60-825">En el ejemplo anterior se genera `custom/url/to/destination`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-825">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="7ba60-826">Generación de direcciones URL por nombre de acción</span><span class="sxs-lookup"><span data-stu-id="7ba60-826">Generating URLs by action name</span></span>

<span data-ttu-id="7ba60-827">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="7ba60-827">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="7ba60-828">`Action`) y todas las sobrecargas relacionadas se basan en la idea de especificar el destino del vínculo mediante un nombre de controlador y un nombre de acción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-828">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="7ba60-829">Cuando se usa `Url.Action`, se especifican los valores actuales de la ruta para `controller` y `action`. Los valores de `controller` y `action` forman parte tanto de los *valores de ambiente* **y como de los** *valores*.</span><span class="sxs-lookup"><span data-stu-id="7ba60-829">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="7ba60-830">El método `Url.Action` siempre utiliza los valores actuales de `action` y `controller` y genera una ruta de dirección URL que dirige a la acción actual.</span><span class="sxs-lookup"><span data-stu-id="7ba60-830">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="7ba60-831">Intentos de enrutamiento para utilizar los valores en los valores de ambiente para rellenar la información que no se proporcionó al generar una dirección URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-831">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="7ba60-832">Al utilizar una ruta como `{a}/{b}/{c}/{d}` y los valores de ambiente `{ a = Alice, b = Bob, c = Carol, d = David }`, el enrutamiento tiene suficiente información para generar una dirección URL sin ningún valor adicional, puesto que todos los parámetros de ruta tienen un valor.</span><span class="sxs-lookup"><span data-stu-id="7ba60-832">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="7ba60-833">Si se agregó el valor `{ d = Donovan }`, el valor `{ d = David }` se ignorará y la ruta de dirección URL generada será `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-833">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="7ba60-834">Las rutas de dirección URL son jerárquicas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-834">URL paths are hierarchical.</span></span> <span data-ttu-id="7ba60-835">En el ejemplo anterior, si se agrega el valor `{ c = Cheryl }`, ambos valores `{ c = Carol, d = David }` se ignorarán.</span><span class="sxs-lookup"><span data-stu-id="7ba60-835">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="7ba60-836">En este caso ya no tenemos un valor para `d` y se producirá un error en la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-836">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="7ba60-837">Debe especificar el valor deseado de `c` y `d`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-837">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="7ba60-838">Es probable que este problema se produzca con la ruta predeterminada (`{controller}/{action}/{id?}`), pero raramente encontrará este comportamiento en la práctica, dado que `Url.Action` especificará siempre de manera explícita un valor `controller` y `action`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-838">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="7ba60-839">Las sobrecargas más largas de `Url.Action` también toman un objeto de *valores de ruta* adicional para proporcionar valores para parámetros de ruta distintos de `controller` y `action`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-839">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="7ba60-840">Normalmente verá esto utilizado con `id`, como `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-840">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="7ba60-841">Por convención, el objeto de *valores de ruta* normalmente es un objeto de tipo anónimo, pero también puede ser `IDictionary<>` o un *objeto .NET estándar*.</span><span class="sxs-lookup"><span data-stu-id="7ba60-841">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="7ba60-842">Los valores de ruta adicionales que no coinciden con los parámetros de ruta se colocan en la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-842">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="7ba60-843">Para crear una dirección URL absoluta, use una sobrecarga que acepte `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="7ba60-843">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="7ba60-844">Generación de direcciones URL por ruta</span><span class="sxs-lookup"><span data-stu-id="7ba60-844">Generating URLs by route</span></span>

<span data-ttu-id="7ba60-845">En el código anterior se pasó el nombre de acción y de controlador para generar una dirección URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-845">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="7ba60-846">`IUrlHelper` también proporciona la familia de métodos `Url.RouteUrl`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-846">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="7ba60-847">Estos métodos son similares a `Url.Action`, pero no copian los valores actuales de `action` y `controller` en los valores de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-847">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="7ba60-848">Lo más común es especificar un nombre de ruta para utilizar una ruta específica y generar la dirección URL, por lo general *sin* especificar un nombre de acción o controlador.</span><span class="sxs-lookup"><span data-stu-id="7ba60-848">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="7ba60-849">Generación de direcciones URL en HTML</span><span class="sxs-lookup"><span data-stu-id="7ba60-849">Generating URLs in HTML</span></span>

<span data-ttu-id="7ba60-850">`IHtmlHelper` proporciona los métodos de `HtmlHelper``Html.BeginForm` y `Html.ActionLink` para generar elementos `<form>` y `<a>`, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="7ba60-850">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="7ba60-851">Estos métodos utilizan el método `Url.Action` para generar una dirección URL y aceptan argumentos similares.</span><span class="sxs-lookup"><span data-stu-id="7ba60-851">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="7ba60-852">Los métodos `Url.RouteUrl` complementarios de `HtmlHelper` son `Html.BeginRouteForm` y `Html.RouteLink`, cuya funcionalidad es similar.</span><span class="sxs-lookup"><span data-stu-id="7ba60-852">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="7ba60-853">Las TagHelper generan direcciones URL a través de la TagHelper `form` y la TagHelper `<a>`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-853">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="7ba60-854">Ambos usan `IUrlHelper` para su implementación.</span><span class="sxs-lookup"><span data-stu-id="7ba60-854">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="7ba60-855">Consulte [Trabajar con formularios](../views/working-with-forms.md) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="7ba60-855">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="7ba60-856">Dentro de las vistas, `IUrlHelper` está disponible a través de la propiedad `Url` para una generación de direcciones URL ad hoc no cubierta por los pasos anteriores.</span><span class="sxs-lookup"><span data-stu-id="7ba60-856">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="7ba60-857">Generar direcciones URL en los resultados de acción</span><span class="sxs-lookup"><span data-stu-id="7ba60-857">Generating URLS in Action Results</span></span>

<span data-ttu-id="7ba60-858">Los ejemplos anteriores han demostrado el uso de `IUrlHelper` en un controlador, aunque el uso más común de un controlador consiste en generar una dirección URL como parte de un resultado de acción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-858">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="7ba60-859">Las clases base `ControllerBase` y `Controller` proporcionan métodos de conveniencia para los resultados de acción que hacen referencia a otra acción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-859">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="7ba60-860">Un uso típico es redirigir después de aceptar la entrada del usuario.</span><span class="sxs-lookup"><span data-stu-id="7ba60-860">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

<span data-ttu-id="7ba60-861">Los patrones de diseño Factory Method de resultados de acción siguen un patrón similar a los métodos en `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-861">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="7ba60-862">Caso especial para rutas convencionales dedicadas</span><span class="sxs-lookup"><span data-stu-id="7ba60-862">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="7ba60-863">El enrutamiento convencional puede usar un tipo especial de definición de ruta denominada *ruta convencional dedicada*.</span><span class="sxs-lookup"><span data-stu-id="7ba60-863">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="7ba60-864">En el ejemplo siguiente, la ruta llamada `blog` es una ruta convencional dedicada.</span><span class="sxs-lookup"><span data-stu-id="7ba60-864">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="7ba60-865">Con estas definiciones de ruta, `Url.Action("Index", "Home")` generará la ruta de dirección URL `/` con la ruta `default`. Pero, ¿por qué?</span><span class="sxs-lookup"><span data-stu-id="7ba60-865">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="7ba60-866">Se puede suponer que los valores de ruta `{ controller = Home, action = Index }` son suficientes para generar una dirección URL utilizando `blog`, con el resultado `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-866">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="7ba60-867">Las rutas convencionales dedicadas se basan en un comportamiento especial de los valores predeterminados que no tienen un parámetro de ruta correspondiente que impida que la ruta sea "demasiado expansiva" con la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-867">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="7ba60-868">En este caso, los valores predeterminados son `{ controller = Blog, action = Article }`, y ni `controller` ni `action` aparecen como un parámetro de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-868">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="7ba60-869">Cuando el enrutamiento realiza la generación de direcciones URL, los valores proporcionados deben coincidir con los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="7ba60-869">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="7ba60-870">La generación de direcciones URL con `blog` producirá un error porque los valores `{ controller = Home, action = Index }` no coinciden con `{ controller = Blog, action = Article }`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-870">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="7ba60-871">Después, el enrutamiento vuelve para probar `default`, operación que se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="7ba60-871">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="7ba60-872">Áreas</span><span class="sxs-lookup"><span data-stu-id="7ba60-872">Areas</span></span>

<span data-ttu-id="7ba60-873">Las [áreas](areas.md) son una característica de MVC que se usa para organizar funciones relacionadas en un grupo como un espacio de nombres de enrutamiento independiente (para acciones de controlador) y una estructura de carpetas (para las vistas).</span><span class="sxs-lookup"><span data-stu-id="7ba60-873">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="7ba60-874">La utilización de áreas permite que una aplicación tenga varios controladores con el mismo nombre, siempre y cuando tengan *áreas* diferentes.</span><span class="sxs-lookup"><span data-stu-id="7ba60-874">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="7ba60-875">El uso de áreas crea una jerarquía para el enrutamiento mediante la adición de otro parámetro de ruta, `area`, a `controller` y `action`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-875">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="7ba60-876">En esta sección veremos la interacción entre el enrutamiento y las áreas. Consulte [Áreas](areas.md) para obtener más información sobre cómo se utilizan las áreas con las vistas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-876">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="7ba60-877">En el ejemplo siguiente se configura MVC para usar la ruta predeterminada convencional y una *ruta de área* para un área denominada `Blog`:</span><span class="sxs-lookup"><span data-stu-id="7ba60-877">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="7ba60-878">Cuando coincide con una ruta de dirección URL como `/Manage/Users/AddUser`, la primera ruta generará los valores de ruta `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-878">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="7ba60-879">El valor de ruta `area` se genera con un valor predeterminado para `area`. De hecho, la ruta creada por `MapAreaRoute` es equivalente a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="7ba60-879">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="7ba60-880">`MapAreaRoute` utiliza el nombre de área proporcionado, que en este caso es `Blog`, para crear una ruta con un valor predeterminado y una restricción para `area`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-880">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="7ba60-881">El valor predeterminado garantiza que la ruta siempre produce `{ area = Blog, ... }`; la restricción requiere el valor `{ area = Blog, ... }` para la generación de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-881">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="7ba60-882">El enrutamiento convencional depende del orden.</span><span class="sxs-lookup"><span data-stu-id="7ba60-882">Conventional routing is order-dependent.</span></span> <span data-ttu-id="7ba60-883">En general, las rutas con áreas deben colocarse antes en la tabla de rutas, ya que son más específicas que las rutas sin un área.</span><span class="sxs-lookup"><span data-stu-id="7ba60-883">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="7ba60-884">En el ejemplo anterior, los valores de ruta coincidirían con la acción siguiente:</span><span class="sxs-lookup"><span data-stu-id="7ba60-884">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="7ba60-885">`AreaAttribute` es lo que denota un controlador como parte de un área. Se dice que este controlador está en el área `Blog`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-885">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="7ba60-886">Los controladores sin un atributo `[Area]` no son miembros de ningún área y **no** coincidirán cuando el enrutamiento proporcione el valor de ruta `area`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-886">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="7ba60-887">En el ejemplo siguiente, solo el primer controlador enumerado puede coincidir con los valores de ruta `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-887">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="7ba60-888">En aras de la exhaustividad, aquí se muestra el espacio de nombres de cada controlador; en caso contrario, los controladores tendrían un conflicto de nomenclatura y generarían un error del compilador.</span><span class="sxs-lookup"><span data-stu-id="7ba60-888">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="7ba60-889">Los espacios de nombres de clase no tienen ningún efecto en el enrutamiento de MVC.</span><span class="sxs-lookup"><span data-stu-id="7ba60-889">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="7ba60-890">Los dos primeros controladores son miembros de las áreas y solo coinciden cuando el valor de ruta `area` proporciona su respectivo nombre de área.</span><span class="sxs-lookup"><span data-stu-id="7ba60-890">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="7ba60-891">El tercer controlador no es miembro de ningún área y solo puede coincidir cuando el enrutamiento no proporciona ningún valor para `area`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-891">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="7ba60-892">En términos de búsqueda de coincidencias de *ningún valor* , la ausencia del valor `area` es igual que si el valor de `area` fuese null o una cadena vacía.</span><span class="sxs-lookup"><span data-stu-id="7ba60-892">In terms of matching *no value* , the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="7ba60-893">Al ejecutar una acción en un área, el valor de ruta para `area` estará disponible como un *valor de ambiente* para que el enrutamiento pueda usarlo en la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-893">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="7ba60-894">Esto significa que, de forma predeterminada, las áreas actúan de forma *adhesiva* para la generación de direcciones URL, tal como se muestra en el ejemplo siguiente.</span><span class="sxs-lookup"><span data-stu-id="7ba60-894">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="7ba60-895">Descripción de IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="7ba60-895">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="7ba60-896">En esta sección se analiza con mayor profundidad el funcionamiento interno del marco y la elección por parte de MVC de la acción que se va a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="7ba60-896">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="7ba60-897">Una aplicación típica no necesitará un `IActionConstraint` personalizado.</span><span class="sxs-lookup"><span data-stu-id="7ba60-897">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="7ba60-898">Es probable que ya haya usado `IActionConstraint` incluso si no está familiarizado con la interfaz.</span><span class="sxs-lookup"><span data-stu-id="7ba60-898">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="7ba60-899">El atributo `[HttpGet]` y los atributos `[Http-VERB]` similares implementan `IActionConstraint` con el fin de limitar la ejecución de un método de acción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-899">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="7ba60-900">Adoptando la ruta convencional predeterminada, la ruta de dirección URL `/Products/Edit` generaría los valores `{ controller = Products, action = Edit }`, que coincidirían con **ambas** acciones que se muestran aquí.</span><span class="sxs-lookup"><span data-stu-id="7ba60-900">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="7ba60-901">En terminología de `IActionConstraint`, diríamos que ambas acciones se consideran candidatas, puesto que las dos coinciden con los datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-901">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="7ba60-902">Cuando `HttpGetAttribute` se ejecute, dirá que *Edit()* es una coincidencia para *GET* , pero no para cualquier otro verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="7ba60-902">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="7ba60-903">La acción `Edit(...)` no tiene ninguna restricción definida, por lo que coincidirá con cualquier verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="7ba60-903">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="7ba60-904">Con `POST`, solamente `Edit(...)` coincide.</span><span class="sxs-lookup"><span data-stu-id="7ba60-904">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="7ba60-905">Pero con `GET` ambas acciones pueden coincidir. No obstante, una acción con `IActionConstraint` siempre se considera *mejor* que una acción sin dicha restricción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-905">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="7ba60-906">Por tanto, como `Edit()` tiene `[HttpGet]`, se considera más específica y se seleccionará si ambas acciones pueden coincidir.</span><span class="sxs-lookup"><span data-stu-id="7ba60-906">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="7ba60-907">Conceptualmente, `IActionConstraint` es una forma de *sobrecarga* , pero en lugar de sobrecargar métodos con el mismo nombre, la sobrecarga se produce entre acciones que coinciden con la misma dirección URL.</span><span class="sxs-lookup"><span data-stu-id="7ba60-907">Conceptually, `IActionConstraint` is a form of *overloading* , but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="7ba60-908">El enrutamiento mediante atributos también utiliza `IActionConstraint` y puede dar lugar a que acciones de diferentes controladores se consideren candidatas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-908">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="7ba60-909">Implementación de IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="7ba60-909">Implementing IActionConstraint</span></span>

<span data-ttu-id="7ba60-910">La manera más sencilla de implementar `IActionConstraint` consiste en crear una clase derivada de `System.Attribute` y colocarla en las acciones y los controladores.</span><span class="sxs-lookup"><span data-stu-id="7ba60-910">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="7ba60-911">MVC detectará automáticamente cualquier `IActionConstraint` que se aplique como atributo.</span><span class="sxs-lookup"><span data-stu-id="7ba60-911">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="7ba60-912">El modelo de aplicaciones es quizá el enfoque más flexible para la aplicación de restricciones, puesto que permite metaprogramar cómo se aplican.</span><span class="sxs-lookup"><span data-stu-id="7ba60-912">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="7ba60-913">En el ejemplo siguiente, una restricción elige una acción basada en un *código de país* de los datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="7ba60-913">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="7ba60-914">El [ejemplo completo se encuentra en GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="7ba60-914">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="7ba60-915">Usted es responsable de implementar el método `Accept` y elegir un valor para "Order" para que la restricción se ejecute.</span><span class="sxs-lookup"><span data-stu-id="7ba60-915">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="7ba60-916">En este caso, el método `Accept` devuelve `true` para denotar que la acción es una coincidencia cuando el valor de ruta `country` coincide.</span><span class="sxs-lookup"><span data-stu-id="7ba60-916">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="7ba60-917">Esto difiere de `RouteValueAttribute` en que permite volver a una acción sin atributos.</span><span class="sxs-lookup"><span data-stu-id="7ba60-917">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="7ba60-918">El ejemplo muestra que si se define una acción `en-US`, entonces un código de país como `fr-FR` recurriría a un controlador más genérico que no tenga `[CountrySpecific(...)]` aplicado.</span><span class="sxs-lookup"><span data-stu-id="7ba60-918">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="7ba60-919">La propiedad `Order` decide de qué *fase* forma parte la restricción.</span><span class="sxs-lookup"><span data-stu-id="7ba60-919">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="7ba60-920">Restricciones de acciones que se ejecutan en grupos basados en la `Order`.</span><span class="sxs-lookup"><span data-stu-id="7ba60-920">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="7ba60-921">Por ejemplo, todos los atributos del método HTTP proporcionados por el marco utilizan el mismo valor `Order` para que se ejecuten en la misma fase.</span><span class="sxs-lookup"><span data-stu-id="7ba60-921">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="7ba60-922">Puede tener las fases que sean necesarias para implementar las directivas deseadas.</span><span class="sxs-lookup"><span data-stu-id="7ba60-922">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="7ba60-923">Para decidir el valor de `Order`, piense si la restricción se debería o no aplicar antes que los métodos HTTP.</span><span class="sxs-lookup"><span data-stu-id="7ba60-923">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="7ba60-924">Los números más bajos se ejecutan primero.</span><span class="sxs-lookup"><span data-stu-id="7ba60-924">Lower numbers run first.</span></span>

::: moniker-end
