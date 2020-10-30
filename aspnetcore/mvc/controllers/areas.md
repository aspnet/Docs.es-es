---
title: Áreas de ASP.NET Core
author: rick-anderson
description: Obtenga información sobre las áreas, una característica de ASP.NET MVC que se usa para organizar funciones relacionadas en un grupo como un espacio de nombres independiente (para el enrutamiento) y una estructura de carpetas (para las vistas).
ms.author: riande
ms.date: 03/21/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/controllers/areas
ms.openlocfilehash: 42eec406813adce4d7edbc1ab66a1f689c4aca0e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053532"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="f58ac-103">Áreas de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f58ac-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="f58ac-104">Por [Dhananjay Kumar](https://twitter.com/debug_mode) y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f58ac-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f58ac-105">Las áreas son una característica ASP.NET que se usa para organizar la funcionalidad relacionada en un grupo como independiente:</span><span class="sxs-lookup"><span data-stu-id="f58ac-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="f58ac-106">Espacio de nombres para el enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="f58ac-106">Namespace for routing.</span></span>
* <span data-ttu-id="f58ac-107">Estructura de carpetas para vistas y :::no-loc(Razor)::: páginas.</span><span class="sxs-lookup"><span data-stu-id="f58ac-107">Folder structure for views and :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="f58ac-108">El uso de áreas crea una jerarquía para el enrutamiento mediante la adición de otro parámetro de ruta, `area` , a `controller` y `action` o a una :::no-loc(Razor)::: página `page` .</span><span class="sxs-lookup"><span data-stu-id="f58ac-108">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a :::no-loc(Razor)::: Page `page`.</span></span>

<span data-ttu-id="f58ac-109">Las áreas proporcionan una manera de particionar una aplicación Web ASP.NET Core en grupos funcionales más pequeños, cada uno con su propio conjunto de :::no-loc(Razor)::: páginas, controladores, vistas y modelos.</span><span class="sxs-lookup"><span data-stu-id="f58ac-109">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of :::no-loc(Razor)::: Pages, controllers, views, and models.</span></span> <span data-ttu-id="f58ac-110">Un área es en realidad una estructura dentro de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="f58ac-110">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="f58ac-111">En un proyecto web ASP.NET Core, los componentes lógicos como Páginas, Modelo, Vista y Controlador se mantienen en carpetas diferentes.</span><span class="sxs-lookup"><span data-stu-id="f58ac-111">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="f58ac-112">El runtime de ASP.NET Core usa convenciones de nomenclatura para crear la relación entre estos componentes.</span><span class="sxs-lookup"><span data-stu-id="f58ac-112">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="f58ac-113">Para una aplicación grande, puede ser conveniente dividir la aplicación en distintas áreas de funciones de alto nivel.</span><span class="sxs-lookup"><span data-stu-id="f58ac-113">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="f58ac-114">Por ejemplo, una aplicación de comercio electrónico con varias unidades de negocio, como las de finalización de la compra, facturación y búsqueda.</span><span class="sxs-lookup"><span data-stu-id="f58ac-114">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="f58ac-115">Cada una de estas unidades tiene su propia área para contener vistas, controladores, :::no-loc(Razor)::: páginas y modelos.</span><span class="sxs-lookup"><span data-stu-id="f58ac-115">Each of these units have their own area to contain views, controllers, :::no-loc(Razor)::: Pages, and models.</span></span>

<span data-ttu-id="f58ac-116">Considere el uso de áreas en un proyecto en los casos siguientes:</span><span class="sxs-lookup"><span data-stu-id="f58ac-116">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="f58ac-117">La aplicación está formada por varios componentes funcionales generales que se pueden separar de forma lógica.</span><span class="sxs-lookup"><span data-stu-id="f58ac-117">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="f58ac-118">Le interesa dividir la aplicación para que se pueda trabajar en cada área funcional de forma independiente.</span><span class="sxs-lookup"><span data-stu-id="f58ac-118">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="f58ac-119">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f58ac-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="f58ac-120">En el ejemplo de descarga se proporciona una aplicación básica para probar las áreas.</span><span class="sxs-lookup"><span data-stu-id="f58ac-120">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="f58ac-121">Si utiliza :::no-loc(Razor)::: páginas, vea [áreas con :::no-loc(Razor)::: páginas](#areas-with-razor-pages) en este documento.</span><span class="sxs-lookup"><span data-stu-id="f58ac-121">If you're using :::no-loc(Razor)::: Pages, see [Areas with :::no-loc(Razor)::: Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="f58ac-122">Áreas para controladores con vistas</span><span class="sxs-lookup"><span data-stu-id="f58ac-122">Areas for controllers with views</span></span>

<span data-ttu-id="f58ac-123">Una aplicación web ASP.NET Core típica en la que se usen áreas, controladores y vistas contiene lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f58ac-123">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="f58ac-124">Una [estructura de carpetas de área](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="f58ac-124">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="f58ac-125">Controladores con el [`[Area]`](#attribute) atributo para asociar el controlador al área:</span><span class="sxs-lookup"><span data-stu-id="f58ac-125">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="f58ac-126">[Ruta de área agregada al inicio](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="f58ac-126">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="f58ac-127">Estructura de carpetas de área</span><span class="sxs-lookup"><span data-stu-id="f58ac-127">Area folder structure</span></span>

<span data-ttu-id="f58ac-128">Considere una aplicación que tiene dos grupos lógicos, *Productos* y *Servicios* .</span><span class="sxs-lookup"><span data-stu-id="f58ac-128">Consider an app that has two logical groups, *Products* and *Services* .</span></span> <span data-ttu-id="f58ac-129">Mediante las áreas, la estructura de carpetas sería similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="f58ac-129">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="f58ac-130">Nombre de proyecto</span><span class="sxs-lookup"><span data-stu-id="f58ac-130">Project name</span></span>
  * <span data-ttu-id="f58ac-131">Áreas</span><span class="sxs-lookup"><span data-stu-id="f58ac-131">Areas</span></span>
    * <span data-ttu-id="f58ac-132">Productos</span><span class="sxs-lookup"><span data-stu-id="f58ac-132">Products</span></span>
      * <span data-ttu-id="f58ac-133">Controladores</span><span class="sxs-lookup"><span data-stu-id="f58ac-133">Controllers</span></span>
        * <span data-ttu-id="f58ac-134">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="f58ac-134">HomeController.cs</span></span>
        * <span data-ttu-id="f58ac-135">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="f58ac-135">ManageController.cs</span></span>
      * <span data-ttu-id="f58ac-136">Vistas</span><span class="sxs-lookup"><span data-stu-id="f58ac-136">Views</span></span>
        * <span data-ttu-id="f58ac-137">Página principal</span><span class="sxs-lookup"><span data-stu-id="f58ac-137">Home</span></span>
          * <span data-ttu-id="f58ac-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="f58ac-138">Index.cshtml</span></span>
        * <span data-ttu-id="f58ac-139">Administrar</span><span class="sxs-lookup"><span data-stu-id="f58ac-139">Manage</span></span>
          * <span data-ttu-id="f58ac-140">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="f58ac-140">Index.cshtml</span></span>
          * <span data-ttu-id="f58ac-141">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="f58ac-141">About.cshtml</span></span>
    * <span data-ttu-id="f58ac-142">Servicios</span><span class="sxs-lookup"><span data-stu-id="f58ac-142">Services</span></span>
      * <span data-ttu-id="f58ac-143">Controladores</span><span class="sxs-lookup"><span data-stu-id="f58ac-143">Controllers</span></span>
        * <span data-ttu-id="f58ac-144">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="f58ac-144">HomeController.cs</span></span>
      * <span data-ttu-id="f58ac-145">Vistas</span><span class="sxs-lookup"><span data-stu-id="f58ac-145">Views</span></span>
        * <span data-ttu-id="f58ac-146">Página principal</span><span class="sxs-lookup"><span data-stu-id="f58ac-146">Home</span></span>
          * <span data-ttu-id="f58ac-147">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="f58ac-147">Index.cshtml</span></span>

<span data-ttu-id="f58ac-148">Aunque el diseño anterior es típico cuando se usan áreas, solo los archivos de vista tienen que usar esta estructura de carpetas.</span><span class="sxs-lookup"><span data-stu-id="f58ac-148">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="f58ac-149">La detección de vista busca un archivo de vista de área coincidente en el orden siguiente:</span><span class="sxs-lookup"><span data-stu-id="f58ac-149">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="f58ac-150">Asociación del controlador a un área</span><span class="sxs-lookup"><span data-stu-id="f58ac-150">Associate the controller with an Area</span></span>

<span data-ttu-id="f58ac-151">Los controladores de área se designan con el atributo de [ &lbrack; área &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="f58ac-151">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="f58ac-152">Adición de la ruta de área</span><span class="sxs-lookup"><span data-stu-id="f58ac-152">Add Area route</span></span>

<span data-ttu-id="f58ac-153">Las rutas de área suelen usar  [enrutamiento convencional](xref:mvc/controllers/routing#cr) en lugar de [enrutamiento de atributos](xref:mvc/controllers/routing#ar).</span><span class="sxs-lookup"><span data-stu-id="f58ac-153">Area routes typically use  [conventional routing](xref:mvc/controllers/routing#cr) rather than [attribute routing](xref:mvc/controllers/routing#ar).</span></span> <span data-ttu-id="f58ac-154">El enrutamiento convencional depende del orden.</span><span class="sxs-lookup"><span data-stu-id="f58ac-154">Conventional routing is order-dependent.</span></span> <span data-ttu-id="f58ac-155">En general, las rutas con áreas deben colocarse antes en la tabla de rutas, ya que son más específicas que las rutas sin un área.</span><span class="sxs-lookup"><span data-stu-id="f58ac-155">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="f58ac-156">Se puede `{area:...}` usar como un token en las plantillas de ruta, si el espacio de direcciones URL es uniforme en todas las áreas:</span><span class="sxs-lookup"><span data-stu-id="f58ac-156">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

<span data-ttu-id="f58ac-157">En el código anterior, `exists` aplica la restricción de que la ruta debe coincidir con un área.</span><span class="sxs-lookup"><span data-stu-id="f58ac-157">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="f58ac-158">Usar `{area:...}` con `MapControllerRoute` :</span><span class="sxs-lookup"><span data-stu-id="f58ac-158">Using `{area:...}` with `MapControllerRoute`:</span></span>

* <span data-ttu-id="f58ac-159">Es el mecanismo menos complicado para agregar enrutamiento a las áreas.</span><span class="sxs-lookup"><span data-stu-id="f58ac-159">Is the least complicated mechanism to adding routing to areas.</span></span>
* <span data-ttu-id="f58ac-160">Coincide con todos los controladores con el `[Area("Area name")]` atributo.</span><span class="sxs-lookup"><span data-stu-id="f58ac-160">Matches all controllers with the `[Area("Area name")]` attribute.</span></span>

<span data-ttu-id="f58ac-161">En el código siguiente se usa <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> para crear dos rutas de área con nombre:</span><span class="sxs-lookup"><span data-stu-id="f58ac-161">The following code uses <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

<span data-ttu-id="f58ac-162">Para más información, vea [Enrutamiento de áreas](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="f58ac-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="f58ac-163">Generación de vínculos con áreas de MVC</span><span class="sxs-lookup"><span data-stu-id="f58ac-163">Link generation with MVC areas</span></span>

<span data-ttu-id="f58ac-164">En el código siguiente de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) se muestra la generación de vínculos con el área especificada:</span><span class="sxs-lookup"><span data-stu-id="f58ac-164">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="f58ac-165">La descarga de ejemplo incluye una [vista parcial](xref:mvc/views/partial) que contiene:</span><span class="sxs-lookup"><span data-stu-id="f58ac-165">The sample download includes a [partial view](xref:mvc/views/partial) that contains:</span></span>

* <span data-ttu-id="f58ac-166">Los vínculos anteriores.</span><span class="sxs-lookup"><span data-stu-id="f58ac-166">The preceding links.</span></span>
* <span data-ttu-id="f58ac-167">No se especifican vínculos similares a los anteriores, excepto `area` .</span><span class="sxs-lookup"><span data-stu-id="f58ac-167">Links similar to the preceding except `area` is not specified.</span></span>

<span data-ttu-id="f58ac-168">En el [archivo de diseño](xref:mvc/views/layout) se hace referencia a la vista parcial, por lo que en todas las páginas de la aplicación se muestran los vínculos generados.</span><span class="sxs-lookup"><span data-stu-id="f58ac-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="f58ac-169">Los vínculos generados sin especificar el área solo son válidos cuando se les hace referencia desde una página en el mismo área y controlador.</span><span class="sxs-lookup"><span data-stu-id="f58ac-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="f58ac-170">Cuando no se especifica el área o el controlador, el enrutamiento depende de los valores de [ambiente](xref:mvc/controllers/routing#ambient).</span><span class="sxs-lookup"><span data-stu-id="f58ac-170">When the area or controller is not specified, routing depends on the [ambient](xref:mvc/controllers/routing#ambient) values.</span></span> <span data-ttu-id="f58ac-171">Los valores de ruta actuales de la solicitud actual se consideran valores de ambiente para la generación de vínculos.</span><span class="sxs-lookup"><span data-stu-id="f58ac-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="f58ac-172">En muchos casos para la aplicación de ejemplo, el uso de los valores de ambiente genera vínculos incorrectos con el marcado que no especifica el área.</span><span class="sxs-lookup"><span data-stu-id="f58ac-172">In many cases for the sample app, using the ambient values generates incorrect links with the markup that doesn't specify the area.</span></span>

<span data-ttu-id="f58ac-173">Para más información, vea [Enrutar a acciones de controlador](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="f58ac-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="f58ac-174">Diseño Compartido para áreas con el archivo _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="f58ac-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="f58ac-175">Para compartir un diseño común para toda la aplicación, mantenga el *_ViewStart. cshtml* en la [carpeta raíz](#arf)de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f58ac-175">To share a common layout for the entire app, keep the *_ViewStart.cshtml* in the [application root folder](#arf).</span></span> <span data-ttu-id="f58ac-176">Para obtener más información, vea <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="f58ac-176">For more information, see <xref:mvc/views/layout></span></span>

<a name="arf"></a>

### <a name="application-root-folder"></a><span data-ttu-id="f58ac-177">Carpeta raíz de la aplicación</span><span class="sxs-lookup"><span data-stu-id="f58ac-177">Application root folder</span></span>

<span data-ttu-id="f58ac-178">La carpeta raíz de la aplicación es la carpeta que contiene *Startup.CS* en la aplicación Web creada con las plantillas de ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="f58ac-178">The application root folder is the folder containing *Startup.cs* in web app created with the ASP.NET Core templates.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="f58ac-179">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="f58ac-179">_ViewImports.cshtml</span></span>

 <span data-ttu-id="f58ac-180">*/Views/_ViewImports. cshtml* , para MVC y */pages/_ViewImports. cshtml* para :::no-loc(Razor)::: páginas, no se importa a las vistas de las áreas.</span><span class="sxs-lookup"><span data-stu-id="f58ac-180">*/Views/_ViewImports.cshtml* , for MVC, and */Pages/_ViewImports.cshtml* for :::no-loc(Razor)::: Pages, is not imported to views in areas.</span></span> <span data-ttu-id="f58ac-181">Use uno de los métodos siguientes para proporcionar las importaciones de vistas a todas las vistas:</span><span class="sxs-lookup"><span data-stu-id="f58ac-181">Use one of the following approaches to provide view imports to all views:</span></span>

* <span data-ttu-id="f58ac-182">Agregue *_ViewImports. cshtml* a la [carpeta raíz](#arf)de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f58ac-182">Add *_ViewImports.cshtml* to the [application root folder](#arf).</span></span> <span data-ttu-id="f58ac-183">Un *_ViewImports. cshtml* en la carpeta raíz de la aplicación se aplicará a todas las vistas de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f58ac-183">A *_ViewImports.cshtml* in the application root folder will apply to all views in the app.</span></span>
* <span data-ttu-id="f58ac-184">Copie el archivo *_ViewImports. cshtml* en la carpeta vista adecuada en áreas.</span><span class="sxs-lookup"><span data-stu-id="f58ac-184">Copy the *_ViewImports.cshtml* file to the appropriate view folder under areas.</span></span>

<span data-ttu-id="f58ac-185">El archivo *_ViewImports. cshtml* normalmente contiene las importaciones de [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) , `@using` y `@inject` .</span><span class="sxs-lookup"><span data-stu-id="f58ac-185">The *_ViewImports.cshtml* file typically contains [Tag Helpers](xref:mvc/views/tag-helpers/intro) imports, `@using`, and `@inject` statements.</span></span> <span data-ttu-id="f58ac-186">Para obtener más información, vea [importar directivas compartidas](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="f58ac-186">For more information, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="f58ac-187">Cambio de la carpeta de área predeterminada donde se almacenan las vistas</span><span class="sxs-lookup"><span data-stu-id="f58ac-187">Change default area folder where views are stored</span></span>

<span data-ttu-id="f58ac-188">En el código siguiente se cambia la carpeta de área predeterminada de `"Areas"` a `"MyAreas"`:</span><span class="sxs-lookup"><span data-stu-id="f58ac-188">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a><span data-ttu-id="f58ac-189">Áreas con :::no-loc(Razor)::: páginas</span><span class="sxs-lookup"><span data-stu-id="f58ac-189">Areas with :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="f58ac-190">Las áreas con :::no-loc(Razor)::: páginas requieren una `Areas/<area name>/Pages` carpeta en la raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f58ac-190">Areas with :::no-loc(Razor)::: Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="f58ac-191">La siguiente estructura de carpetas se usa con la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span><span class="sxs-lookup"><span data-stu-id="f58ac-191">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span></span>

* <span data-ttu-id="f58ac-192">Nombre de proyecto</span><span class="sxs-lookup"><span data-stu-id="f58ac-192">Project name</span></span>
  * <span data-ttu-id="f58ac-193">Áreas</span><span class="sxs-lookup"><span data-stu-id="f58ac-193">Areas</span></span>
    * <span data-ttu-id="f58ac-194">Productos</span><span class="sxs-lookup"><span data-stu-id="f58ac-194">Products</span></span>
      * <span data-ttu-id="f58ac-195">Páginas</span><span class="sxs-lookup"><span data-stu-id="f58ac-195">Pages</span></span>
        * <span data-ttu-id="f58ac-196">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="f58ac-196">_ViewImports</span></span>
        * <span data-ttu-id="f58ac-197">Acerca de</span><span class="sxs-lookup"><span data-stu-id="f58ac-197">About</span></span>
        * <span data-ttu-id="f58ac-198">Índice</span><span class="sxs-lookup"><span data-stu-id="f58ac-198">Index</span></span>
    * <span data-ttu-id="f58ac-199">Servicios</span><span class="sxs-lookup"><span data-stu-id="f58ac-199">Services</span></span>
      * <span data-ttu-id="f58ac-200">Páginas</span><span class="sxs-lookup"><span data-stu-id="f58ac-200">Pages</span></span>
        * <span data-ttu-id="f58ac-201">Administrar</span><span class="sxs-lookup"><span data-stu-id="f58ac-201">Manage</span></span>
          * <span data-ttu-id="f58ac-202">Acerca de</span><span class="sxs-lookup"><span data-stu-id="f58ac-202">About</span></span>
          * <span data-ttu-id="f58ac-203">Índice</span><span class="sxs-lookup"><span data-stu-id="f58ac-203">Index</span></span>

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a><span data-ttu-id="f58ac-204">Generación de vínculos con :::no-loc(Razor)::: páginas y áreas</span><span class="sxs-lookup"><span data-stu-id="f58ac-204">Link generation with :::no-loc(Razor)::: Pages and areas</span></span>

<span data-ttu-id="f58ac-205">El código siguiente de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) muestra la generación de vínculos con el área especificada (por ejemplo, `asp-area="Products"`):</span><span class="sxs-lookup"><span data-stu-id="f58ac-205">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="f58ac-206">En la descarga de ejemplo se incluye una [vista parcial](xref:mvc/views/partial) que contiene los vínculos anteriores y los mismos vínculos sin especificar el área.</span><span class="sxs-lookup"><span data-stu-id="f58ac-206">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="f58ac-207">En el [archivo de diseño](xref:mvc/views/layout) se hace referencia a la vista parcial, por lo que en todas las páginas de la aplicación se muestran los vínculos generados.</span><span class="sxs-lookup"><span data-stu-id="f58ac-207">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="f58ac-208">Los vínculos generados sin especificar el área solo son válidos cuando se les hace referencia desde una página de la misma área.</span><span class="sxs-lookup"><span data-stu-id="f58ac-208">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="f58ac-209">Cuando no se especifica el área, el enrutamiento depende de los valores *ambient* .</span><span class="sxs-lookup"><span data-stu-id="f58ac-209">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="f58ac-210">Los valores de ruta actuales de la solicitud actual se consideran valores de ambiente para la generación de vínculos.</span><span class="sxs-lookup"><span data-stu-id="f58ac-210">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="f58ac-211">En muchos casos de la aplicación de ejemplo, el uso de valores de ambiente genera vínculos incorrectos.</span><span class="sxs-lookup"><span data-stu-id="f58ac-211">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="f58ac-212">Por ejemplo, tenga en cuenta los vínculos generados desde el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="f58ac-212">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="f58ac-213">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="f58ac-213">For the preceding code:</span></span>

* <span data-ttu-id="f58ac-214">El vínculo generado a partir de `<a asp-page="/Manage/About">` es correcto solo cuando la última solicitud fue de una página del área `Services`.</span><span class="sxs-lookup"><span data-stu-id="f58ac-214">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="f58ac-215">Por ejemplo, `/Services/Manage/`, `/Services/Manage/Index` o `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="f58ac-215">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="f58ac-216">El vínculo generado a partir de `<a asp-page="/About">` es correcto solo cuando la última solicitud fue de una página de `/Home`.</span><span class="sxs-lookup"><span data-stu-id="f58ac-216">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="f58ac-217">El código está tomado de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="f58ac-217">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="f58ac-218">Importación del espacio de nombres y los asistentes de etiquetas con el archivo _ViewImports</span><span class="sxs-lookup"><span data-stu-id="f58ac-218">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="f58ac-219">Se puede Agregar un archivo *_ViewImports. cshtml* a cada carpeta de *páginas* de área para importar las aplicaciones auxiliares de espacio de nombres y etiquetas a cada :::no-loc(Razor)::: Página de la carpeta.</span><span class="sxs-lookup"><span data-stu-id="f58ac-219">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each :::no-loc(Razor)::: Page in the folder.</span></span>

<span data-ttu-id="f58ac-220">Tenga en cuenta el área *Services* del código de ejemplo, que no contiene un archivo *_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f58ac-220">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="f58ac-221">En el marcado siguiente se muestra la página */Services/Manage/about* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="f58ac-221">The following markup shows the */Services/Manage/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="f58ac-222">En el marcado anterior:</span><span class="sxs-lookup"><span data-stu-id="f58ac-222">In the preceding markup:</span></span>

* <span data-ttu-id="f58ac-223">El nombre de dominio completo debe usarse para especificar el modelo (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span><span class="sxs-lookup"><span data-stu-id="f58ac-223">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="f58ac-224">Habilita las [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro)`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="f58ac-224">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="f58ac-225">En la descarga de ejemplo, el área Products contiene el siguiente archivo *_ViewImports.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f58ac-225">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="f58ac-226">En el marcado siguiente se muestra la página */Products/about* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="f58ac-226">The following markup shows the */Products/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="f58ac-227">En el archivo anterior, el espacio de nombres y la directiva `@addTagHelper` se importan en el archivo mediante el archivo *Areas/Products/Pages/_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f58ac-227">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="f58ac-228">Para más información, consulte [Administración del ámbito de los asistentes de etiquetas](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) y [Importar directivas compartidas](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="f58ac-228">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-no-locrazor-pages-areas"></a><span data-ttu-id="f58ac-229">Diseño compartido para :::no-loc(Razor)::: áreas de páginas</span><span class="sxs-lookup"><span data-stu-id="f58ac-229">Shared layout for :::no-loc(Razor)::: Pages Areas</span></span>

<span data-ttu-id="f58ac-230">Para compartir un diseño común para toda la aplicación, mueva el archivo *_ViewStart.cshtml* a la carpeta raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f58ac-230">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="f58ac-231">Publicación de áreas</span><span class="sxs-lookup"><span data-stu-id="f58ac-231">Publishing Areas</span></span>

<span data-ttu-id="f58ac-232">Todos los archivos \*.cshtml y los archivos que formen parte del directorio *wwwroot* se publicarán como resultados si `<Project Sdk="Microsoft.NET.Sdk.Web">` está incluido en el archivo \*.csproj.</span><span class="sxs-lookup"><span data-stu-id="f58ac-232">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f58ac-233">Las áreas son una característica de ASP.NET que se usa para organizar funciones relacionadas en un grupo como un espacio de nombres independiente (para el enrutamiento) y una estructura de carpetas (para las vistas).</span><span class="sxs-lookup"><span data-stu-id="f58ac-233">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="f58ac-234">El uso de áreas crea una jerarquía para el enrutamiento mediante la adición de otro parámetro de ruta, `area` , a `controller` y `action` o a una :::no-loc(Razor)::: página `page` .</span><span class="sxs-lookup"><span data-stu-id="f58ac-234">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a :::no-loc(Razor)::: Page `page`.</span></span>

<span data-ttu-id="f58ac-235">Las áreas proporcionan una manera de particionar una aplicación Web ASP.NET Core en grupos funcionales más pequeños, cada uno con su propio conjunto de :::no-loc(Razor)::: páginas, controladores, vistas y modelos.</span><span class="sxs-lookup"><span data-stu-id="f58ac-235">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of :::no-loc(Razor)::: Pages, controllers, views, and models.</span></span> <span data-ttu-id="f58ac-236">Un área es en realidad una estructura dentro de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="f58ac-236">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="f58ac-237">En un proyecto web ASP.NET Core, los componentes lógicos como Páginas, Modelo, Vista y Controlador se mantienen en carpetas diferentes.</span><span class="sxs-lookup"><span data-stu-id="f58ac-237">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="f58ac-238">El runtime de ASP.NET Core usa convenciones de nomenclatura para crear la relación entre estos componentes.</span><span class="sxs-lookup"><span data-stu-id="f58ac-238">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="f58ac-239">Para una aplicación grande, puede ser conveniente dividir la aplicación en distintas áreas de funciones de alto nivel.</span><span class="sxs-lookup"><span data-stu-id="f58ac-239">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="f58ac-240">Por ejemplo, una aplicación de comercio electrónico con varias unidades de negocio, como las de finalización de la compra, facturación y búsqueda.</span><span class="sxs-lookup"><span data-stu-id="f58ac-240">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="f58ac-241">Cada una de estas unidades tiene su propia área para contener vistas, controladores, :::no-loc(Razor)::: páginas y modelos.</span><span class="sxs-lookup"><span data-stu-id="f58ac-241">Each of these units have their own area to contain views, controllers, :::no-loc(Razor)::: Pages, and models.</span></span>

<span data-ttu-id="f58ac-242">Considere el uso de áreas en un proyecto en los casos siguientes:</span><span class="sxs-lookup"><span data-stu-id="f58ac-242">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="f58ac-243">La aplicación está formada por varios componentes funcionales generales que se pueden separar de forma lógica.</span><span class="sxs-lookup"><span data-stu-id="f58ac-243">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="f58ac-244">Le interesa dividir la aplicación para que se pueda trabajar en cada área funcional de forma independiente.</span><span class="sxs-lookup"><span data-stu-id="f58ac-244">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="f58ac-245">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f58ac-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="f58ac-246">En el ejemplo de descarga se proporciona una aplicación básica para probar las áreas.</span><span class="sxs-lookup"><span data-stu-id="f58ac-246">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="f58ac-247">Si utiliza :::no-loc(Razor)::: páginas, vea [áreas con :::no-loc(Razor)::: páginas](#areas-with-razor-pages) en este documento.</span><span class="sxs-lookup"><span data-stu-id="f58ac-247">If you're using :::no-loc(Razor)::: Pages, see [Areas with :::no-loc(Razor)::: Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="f58ac-248">Áreas para controladores con vistas</span><span class="sxs-lookup"><span data-stu-id="f58ac-248">Areas for controllers with views</span></span>

<span data-ttu-id="f58ac-249">Una aplicación web ASP.NET Core típica en la que se usen áreas, controladores y vistas contiene lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f58ac-249">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="f58ac-250">Una [estructura de carpetas de área](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="f58ac-250">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="f58ac-251">Controladores con el [`[Area]`](#attribute) atributo para asociar el controlador al área:</span><span class="sxs-lookup"><span data-stu-id="f58ac-251">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="f58ac-252">[Ruta de área agregada al inicio](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="f58ac-252">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="f58ac-253">Estructura de carpetas de área</span><span class="sxs-lookup"><span data-stu-id="f58ac-253">Area folder structure</span></span>

<span data-ttu-id="f58ac-254">Considere una aplicación que tiene dos grupos lógicos, *Productos* y *Servicios* .</span><span class="sxs-lookup"><span data-stu-id="f58ac-254">Consider an app that has two logical groups, *Products* and *Services* .</span></span> <span data-ttu-id="f58ac-255">Mediante las áreas, la estructura de carpetas sería similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="f58ac-255">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="f58ac-256">Nombre de proyecto</span><span class="sxs-lookup"><span data-stu-id="f58ac-256">Project name</span></span>
  * <span data-ttu-id="f58ac-257">Áreas</span><span class="sxs-lookup"><span data-stu-id="f58ac-257">Areas</span></span>
    * <span data-ttu-id="f58ac-258">Productos</span><span class="sxs-lookup"><span data-stu-id="f58ac-258">Products</span></span>
      * <span data-ttu-id="f58ac-259">Controladores</span><span class="sxs-lookup"><span data-stu-id="f58ac-259">Controllers</span></span>
        * <span data-ttu-id="f58ac-260">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="f58ac-260">HomeController.cs</span></span>
        * <span data-ttu-id="f58ac-261">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="f58ac-261">ManageController.cs</span></span>
      * <span data-ttu-id="f58ac-262">Vistas</span><span class="sxs-lookup"><span data-stu-id="f58ac-262">Views</span></span>
        * <span data-ttu-id="f58ac-263">Página principal</span><span class="sxs-lookup"><span data-stu-id="f58ac-263">Home</span></span>
          * <span data-ttu-id="f58ac-264">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="f58ac-264">Index.cshtml</span></span>
        * <span data-ttu-id="f58ac-265">Administrar</span><span class="sxs-lookup"><span data-stu-id="f58ac-265">Manage</span></span>
          * <span data-ttu-id="f58ac-266">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="f58ac-266">Index.cshtml</span></span>
          * <span data-ttu-id="f58ac-267">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="f58ac-267">About.cshtml</span></span>
    * <span data-ttu-id="f58ac-268">Servicios</span><span class="sxs-lookup"><span data-stu-id="f58ac-268">Services</span></span>
      * <span data-ttu-id="f58ac-269">Controladores</span><span class="sxs-lookup"><span data-stu-id="f58ac-269">Controllers</span></span>
        * <span data-ttu-id="f58ac-270">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="f58ac-270">HomeController.cs</span></span>
      * <span data-ttu-id="f58ac-271">Vistas</span><span class="sxs-lookup"><span data-stu-id="f58ac-271">Views</span></span>
        * <span data-ttu-id="f58ac-272">Página principal</span><span class="sxs-lookup"><span data-stu-id="f58ac-272">Home</span></span>
          * <span data-ttu-id="f58ac-273">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="f58ac-273">Index.cshtml</span></span>

<span data-ttu-id="f58ac-274">Aunque el diseño anterior es típico cuando se usan áreas, solo los archivos de vista tienen que usar esta estructura de carpetas.</span><span class="sxs-lookup"><span data-stu-id="f58ac-274">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="f58ac-275">La detección de vista busca un archivo de vista de área coincidente en el orden siguiente:</span><span class="sxs-lookup"><span data-stu-id="f58ac-275">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="f58ac-276">Asociación del controlador a un área</span><span class="sxs-lookup"><span data-stu-id="f58ac-276">Associate the controller with an Area</span></span>

<span data-ttu-id="f58ac-277">Los controladores de área se designan con el atributo de [ &lbrack; área &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="f58ac-277">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="f58ac-278">Adición de la ruta de área</span><span class="sxs-lookup"><span data-stu-id="f58ac-278">Add Area route</span></span>

<span data-ttu-id="f58ac-279">Las rutas de área normalmente usan el enrutamiento convencional en lugar del enrutamiento mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="f58ac-279">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="f58ac-280">El enrutamiento convencional depende del orden.</span><span class="sxs-lookup"><span data-stu-id="f58ac-280">Conventional routing is order-dependent.</span></span> <span data-ttu-id="f58ac-281">En general, las rutas con áreas deben colocarse antes en la tabla de rutas, ya que son más específicas que las rutas sin un área.</span><span class="sxs-lookup"><span data-stu-id="f58ac-281">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="f58ac-282">Se puede `{area:...}` usar como un token en las plantillas de ruta, si el espacio de direcciones URL es uniforme en todas las áreas:</span><span class="sxs-lookup"><span data-stu-id="f58ac-282">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="f58ac-283">En el código anterior, `exists` aplica la restricción de que la ruta debe coincidir con un área.</span><span class="sxs-lookup"><span data-stu-id="f58ac-283">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="f58ac-284">El uso de `{area:...}` es el mecanismo menos complicado para agregar enrutamiento a las áreas.</span><span class="sxs-lookup"><span data-stu-id="f58ac-284">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="f58ac-285">En el código siguiente se usa <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> para crear dos rutas de área con nombre:</span><span class="sxs-lookup"><span data-stu-id="f58ac-285">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="f58ac-286">Cuando use `MapAreaRoute` con ASP.NET Core 2.2, vea [este problema de GitHub](https://github.com/dotnet/AspNetCore/issues/7772).</span><span class="sxs-lookup"><span data-stu-id="f58ac-286">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="f58ac-287">Para más información, vea [Enrutamiento de áreas](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="f58ac-287">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="f58ac-288">Generación de vínculos con áreas de MVC</span><span class="sxs-lookup"><span data-stu-id="f58ac-288">Link generation with MVC areas</span></span>

<span data-ttu-id="f58ac-289">En el código siguiente de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) se muestra la generación de vínculos con el área especificada:</span><span class="sxs-lookup"><span data-stu-id="f58ac-289">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="f58ac-290">Los vínculos generados con el código anterior son válidos en cualquier parte de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f58ac-290">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="f58ac-291">En la descarga de ejemplo se incluye una [vista parcial](xref:mvc/views/partial) que contiene los vínculos anteriores y los mismos vínculos sin especificar el área.</span><span class="sxs-lookup"><span data-stu-id="f58ac-291">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="f58ac-292">En el [archivo de diseño](xref:mvc/views/layout) se hace referencia a la vista parcial, por lo que en todas las páginas de la aplicación se muestran los vínculos generados.</span><span class="sxs-lookup"><span data-stu-id="f58ac-292">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="f58ac-293">Los vínculos generados sin especificar el área solo son válidos cuando se les hace referencia desde una página en el mismo área y controlador.</span><span class="sxs-lookup"><span data-stu-id="f58ac-293">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="f58ac-294">Cuando no se especifica el área o el controlador, el enrutamiento depende de los valores de *ambiente* .</span><span class="sxs-lookup"><span data-stu-id="f58ac-294">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="f58ac-295">Los valores de ruta actuales de la solicitud actual se consideran valores de ambiente para la generación de vínculos.</span><span class="sxs-lookup"><span data-stu-id="f58ac-295">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="f58ac-296">En muchos casos de la aplicación de ejemplo, el uso de valores de ambiente genera vínculos incorrectos.</span><span class="sxs-lookup"><span data-stu-id="f58ac-296">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="f58ac-297">Para más información, vea [Enrutar a acciones de controlador](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="f58ac-297">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="f58ac-298">Diseño Compartido para áreas con el archivo _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="f58ac-298">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="f58ac-299">Para compartir un diseño común para toda la aplicación, mueva el archivo *_ViewStart.cshtml* a la carpeta raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f58ac-299">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="f58ac-300">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="f58ac-300">_ViewImports.cshtml</span></span>

<span data-ttu-id="f58ac-301">En su ubicación estándar, */Views/_ViewImports.cshtml* no se aplica a las áreas.</span><span class="sxs-lookup"><span data-stu-id="f58ac-301">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="f58ac-302">Para usar [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro)comunes, `@using` o `@inject` en su área, asegúrese de que se aplica un archivo *_ViewImports. cshtml* adecuado [a las vistas de área](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="f58ac-302">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="f58ac-303">Si quiere el mismo comportamiento en todas las vistas, mueva */Views/_ViewImports.cshtml* a la raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f58ac-303">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="f58ac-304">Cambio de la carpeta de área predeterminada donde se almacenan las vistas</span><span class="sxs-lookup"><span data-stu-id="f58ac-304">Change default area folder where views are stored</span></span>

<span data-ttu-id="f58ac-305">En el código siguiente se cambia la carpeta de área predeterminada de `"Areas"` a `"MyAreas"`:</span><span class="sxs-lookup"><span data-stu-id="f58ac-305">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a><span data-ttu-id="f58ac-306">Áreas con :::no-loc(Razor)::: páginas</span><span class="sxs-lookup"><span data-stu-id="f58ac-306">Areas with :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="f58ac-307">Las áreas con :::no-loc(Razor)::: páginas requieren una `Areas/<area name>/Pages` carpeta en la raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f58ac-307">Areas with :::no-loc(Razor)::: Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="f58ac-308">La siguiente estructura de carpetas se usa con la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span><span class="sxs-lookup"><span data-stu-id="f58ac-308">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="f58ac-309">Nombre de proyecto</span><span class="sxs-lookup"><span data-stu-id="f58ac-309">Project name</span></span>
  * <span data-ttu-id="f58ac-310">Áreas</span><span class="sxs-lookup"><span data-stu-id="f58ac-310">Areas</span></span>
    * <span data-ttu-id="f58ac-311">Productos</span><span class="sxs-lookup"><span data-stu-id="f58ac-311">Products</span></span>
      * <span data-ttu-id="f58ac-312">Páginas</span><span class="sxs-lookup"><span data-stu-id="f58ac-312">Pages</span></span>
        * <span data-ttu-id="f58ac-313">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="f58ac-313">_ViewImports</span></span>
        * <span data-ttu-id="f58ac-314">Acerca de</span><span class="sxs-lookup"><span data-stu-id="f58ac-314">About</span></span>
        * <span data-ttu-id="f58ac-315">Índice</span><span class="sxs-lookup"><span data-stu-id="f58ac-315">Index</span></span>
    * <span data-ttu-id="f58ac-316">Servicios</span><span class="sxs-lookup"><span data-stu-id="f58ac-316">Services</span></span>
      * <span data-ttu-id="f58ac-317">Páginas</span><span class="sxs-lookup"><span data-stu-id="f58ac-317">Pages</span></span>
        * <span data-ttu-id="f58ac-318">Administrar</span><span class="sxs-lookup"><span data-stu-id="f58ac-318">Manage</span></span>
          * <span data-ttu-id="f58ac-319">Acerca de</span><span class="sxs-lookup"><span data-stu-id="f58ac-319">About</span></span>
          * <span data-ttu-id="f58ac-320">Índice</span><span class="sxs-lookup"><span data-stu-id="f58ac-320">Index</span></span>

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a><span data-ttu-id="f58ac-321">Generación de vínculos con :::no-loc(Razor)::: páginas y áreas</span><span class="sxs-lookup"><span data-stu-id="f58ac-321">Link generation with :::no-loc(Razor)::: Pages and areas</span></span>

<span data-ttu-id="f58ac-322">El código siguiente de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) muestra la generación de vínculos con el área especificada (por ejemplo, `asp-area="Products"`):</span><span class="sxs-lookup"><span data-stu-id="f58ac-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="f58ac-323">Los vínculos generados con el código anterior son válidos en cualquier parte de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f58ac-323">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="f58ac-324">En la descarga de ejemplo se incluye una [vista parcial](xref:mvc/views/partial) que contiene los vínculos anteriores y los mismos vínculos sin especificar el área.</span><span class="sxs-lookup"><span data-stu-id="f58ac-324">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="f58ac-325">En el [archivo de diseño](xref:mvc/views/layout) se hace referencia a la vista parcial, por lo que en todas las páginas de la aplicación se muestran los vínculos generados.</span><span class="sxs-lookup"><span data-stu-id="f58ac-325">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="f58ac-326">Los vínculos generados sin especificar el área solo son válidos cuando se les hace referencia desde una página de la misma área.</span><span class="sxs-lookup"><span data-stu-id="f58ac-326">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="f58ac-327">Cuando no se especifica el área, el enrutamiento depende de los valores *ambient* .</span><span class="sxs-lookup"><span data-stu-id="f58ac-327">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="f58ac-328">Los valores de ruta actuales de la solicitud actual se consideran valores de ambiente para la generación de vínculos.</span><span class="sxs-lookup"><span data-stu-id="f58ac-328">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="f58ac-329">En muchos casos de la aplicación de ejemplo, el uso de valores de ambiente genera vínculos incorrectos.</span><span class="sxs-lookup"><span data-stu-id="f58ac-329">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="f58ac-330">Por ejemplo, tenga en cuenta los vínculos generados desde el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="f58ac-330">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="f58ac-331">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="f58ac-331">For the preceding code:</span></span>

* <span data-ttu-id="f58ac-332">El vínculo generado a partir de `<a asp-page="/Manage/About">` es correcto solo cuando la última solicitud fue de una página del área `Services`.</span><span class="sxs-lookup"><span data-stu-id="f58ac-332">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="f58ac-333">Por ejemplo, `/Services/Manage/`, `/Services/Manage/Index` o `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="f58ac-333">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="f58ac-334">El vínculo generado a partir de `<a asp-page="/About">` es correcto solo cuando la última solicitud fue de una página de `/Home`.</span><span class="sxs-lookup"><span data-stu-id="f58ac-334">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="f58ac-335">El código está tomado de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="f58ac-335">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="f58ac-336">Importación del espacio de nombres y los asistentes de etiquetas con el archivo _ViewImports</span><span class="sxs-lookup"><span data-stu-id="f58ac-336">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="f58ac-337">Se puede Agregar un archivo *_ViewImports. cshtml* a cada carpeta de *páginas* de área para importar las aplicaciones auxiliares de espacio de nombres y etiquetas a cada :::no-loc(Razor)::: Página de la carpeta.</span><span class="sxs-lookup"><span data-stu-id="f58ac-337">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each :::no-loc(Razor)::: Page in the folder.</span></span>

<span data-ttu-id="f58ac-338">Tenga en cuenta el área *Services* del código de ejemplo, que no contiene un archivo *_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f58ac-338">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="f58ac-339">En el marcado siguiente se muestra la página */Services/Manage/about* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="f58ac-339">The following markup shows the */Services/Manage/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="f58ac-340">En el marcado anterior:</span><span class="sxs-lookup"><span data-stu-id="f58ac-340">In the preceding markup:</span></span>

* <span data-ttu-id="f58ac-341">El nombre de dominio completo debe usarse para especificar el modelo (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span><span class="sxs-lookup"><span data-stu-id="f58ac-341">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="f58ac-342">Habilita las [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro)`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="f58ac-342">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="f58ac-343">En la descarga de ejemplo, el área Products contiene el siguiente archivo *_ViewImports.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f58ac-343">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="f58ac-344">En el marcado siguiente se muestra la página */Products/about* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="f58ac-344">The following markup shows the */Products/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="f58ac-345">En el archivo anterior, el espacio de nombres y la directiva `@addTagHelper` se importan en el archivo mediante el archivo *Areas/Products/Pages/_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f58ac-345">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="f58ac-346">Para más información, consulte [Administración del ámbito de los asistentes de etiquetas](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) y [Importar directivas compartidas](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="f58ac-346">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-no-locrazor-pages-areas"></a><span data-ttu-id="f58ac-347">Diseño compartido para :::no-loc(Razor)::: áreas de páginas</span><span class="sxs-lookup"><span data-stu-id="f58ac-347">Shared layout for :::no-loc(Razor)::: Pages Areas</span></span>

<span data-ttu-id="f58ac-348">Para compartir un diseño común para toda la aplicación, mueva el archivo *_ViewStart.cshtml* a la carpeta raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f58ac-348">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="f58ac-349">Publicación de áreas</span><span class="sxs-lookup"><span data-stu-id="f58ac-349">Publishing Areas</span></span>

<span data-ttu-id="f58ac-350">Todos los archivos \*.cshtml y los archivos que formen parte del directorio *wwwroot* se publicarán como resultados si `<Project Sdk="Microsoft.NET.Sdk.Web">` está incluido en el archivo \*.csproj.</span><span class="sxs-lookup"><span data-stu-id="f58ac-350">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end
