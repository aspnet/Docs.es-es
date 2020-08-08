---
title: Aprenda a migrar de ASP.NET MVC a ASP.NET Core MVC
author: wadepickett
description: Obtenga información sobre cómo iniciar la migración de un proyecto de ASP.NET MVC a ASP.NET Core MVC.
ms.author: wpickett
ms.date: 06/18/2020
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
uid: migration/mvc
ms.openlocfilehash: 17f2a2532c58c3796835328260231d63f8fb2e40
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015054"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="509d2-103">Migración de ASP.NET MVC a ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="509d2-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="509d2-104">En este artículo se muestra cómo iniciar la migración de un proyecto de ASP.NET MVC a [ASP.net Core MVC](xref:mvc/overview).</span><span class="sxs-lookup"><span data-stu-id="509d2-104">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview).</span></span> <span data-ttu-id="509d2-105">En el proceso, se resaltan los cambios relacionados de ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="509d2-105">In the process, it highlights related changes from ASP.NET MVC.</span></span>

<span data-ttu-id="509d2-106">La migración de ASP.NET MVC es un proceso de varios pasos.</span><span class="sxs-lookup"><span data-stu-id="509d2-106">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="509d2-107">En este artículo se describe:</span><span class="sxs-lookup"><span data-stu-id="509d2-107">This article covers:</span></span>

* <span data-ttu-id="509d2-108">Instalación inicial.</span><span class="sxs-lookup"><span data-stu-id="509d2-108">Initial setup.</span></span>
* <span data-ttu-id="509d2-109">Controladores y vistas básicos.</span><span class="sxs-lookup"><span data-stu-id="509d2-109">Basic controllers and views.</span></span>
* <span data-ttu-id="509d2-110">Contenido estático.</span><span class="sxs-lookup"><span data-stu-id="509d2-110">Static content.</span></span>
* <span data-ttu-id="509d2-111">Dependencias del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="509d2-111">Client-side dependencies.</span></span>

<span data-ttu-id="509d2-112">Para migrar la configuración y el Identity código, vea [migrar la configuración a ASP.net Core](xref:migration/configuration) y [migrar la autenticación y Identity a ASP.net Core](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="509d2-112">For migrating configuration and Identity code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="509d2-113">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="509d2-113">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="509d2-114">Crear el proyecto de MVC ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="509d2-114">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="509d2-115">Cree un proyecto de ASP.NET MVC de ejemplo en Visual Studio para migrar:</span><span class="sxs-lookup"><span data-stu-id="509d2-115">Create an example ASP.NET MVC project in Visual Studio to migrate:</span></span>

1. <span data-ttu-id="509d2-116">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="509d2-116">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="509d2-117">Seleccione **aplicación Web de ASP.net (.NET Framework)** y, a continuación, seleccione **siguiente**.</span><span class="sxs-lookup"><span data-stu-id="509d2-117">Select **ASP.NET Web Application (.NET Framework)** and then select **Next**.</span></span>
1. <span data-ttu-id="509d2-118">Asigne al proyecto el nombre *WebApp1* para que el espacio de nombres coincida con el proyecto de ASP.net Core creado en el paso siguiente.</span><span class="sxs-lookup"><span data-stu-id="509d2-118">Name the project *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span> <span data-ttu-id="509d2-119">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="509d2-119">Select **Create**.</span></span>
1. <span data-ttu-id="509d2-120">Seleccione **MVC**y, a continuación, seleccione **crear**.</span><span class="sxs-lookup"><span data-stu-id="509d2-120">Select **MVC**, and then select **Create**.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="509d2-121">Crear el proyecto de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="509d2-121">Create the ASP.NET Core project</span></span>

<span data-ttu-id="509d2-122">Cree una nueva solución con un nuevo proyecto de ASP.NET Core al que migrar:</span><span class="sxs-lookup"><span data-stu-id="509d2-122">Create a new solution with a new ASP.NET Core project to migrate to:</span></span>

1. <span data-ttu-id="509d2-123">Inicie una segunda instancia de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="509d2-123">Launch a second instance of Visual Studio.</span></span>
1. <span data-ttu-id="509d2-124">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="509d2-124">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="509d2-125">Seleccione **ASP.net web Core Web Application** y, a continuación, seleccione **siguiente**.</span><span class="sxs-lookup"><span data-stu-id="509d2-125">Select **ASP.NET Web Core Web Application** and then select **Next**.</span></span>
1. <span data-ttu-id="509d2-126">En el cuadro de diálogo **configurar el nuevo proyecto** , asigne al proyecto el nombre *WebApp1*.</span><span class="sxs-lookup"><span data-stu-id="509d2-126">In the **Configure your new project** dialog, Name the project *WebApp1*.</span></span>
1. <span data-ttu-id="509d2-127">Establezca la ubicación en un directorio diferente al proyecto anterior para usar el mismo nombre de proyecto.</span><span class="sxs-lookup"><span data-stu-id="509d2-127">Set the location to a different directory than the previous project to use the same project name.</span></span> <span data-ttu-id="509d2-128">El uso del mismo espacio de nombres facilita la copia de código entre los dos proyectos.</span><span class="sxs-lookup"><span data-stu-id="509d2-128">Using the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="509d2-129">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="509d2-129">Select **Create**.</span></span>
1. <span data-ttu-id="509d2-130">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 3.1** estén seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="509d2-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="509d2-131">Seleccione la plantilla de proyecto **aplicación web (controlador de vista de modelos)** y seleccione **crear**.</span><span class="sxs-lookup"><span data-stu-id="509d2-131">Select the **Web Application (Model-View-Controller)** project template, and select **Create**.</span></span>

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a><span data-ttu-id="509d2-132">Configuración del sitio de ASP.NET Core para usar MVC</span><span class="sxs-lookup"><span data-stu-id="509d2-132">Configure the ASP.NET Core site to use MVC</span></span>

<span data-ttu-id="509d2-133">En ASP.NET Core proyectos de 3,0 y versiones posteriores, .NET Framework ya no es una plataforma de destino admitida.</span><span class="sxs-lookup"><span data-stu-id="509d2-133">In ASP.NET Core 3.0 and later projects, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="509d2-134">El proyecto debe tener como destino .NET Core.</span><span class="sxs-lookup"><span data-stu-id="509d2-134">Your project must target .NET Core.</span></span> <span data-ttu-id="509d2-135">La ASP.NET Core marco de trabajo compartido, que incluye MVC, forma parte de la instalación del entorno de ejecución de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="509d2-135">The ASP.NET Core shared framework, which includes MVC, is part of the .NET Core runtime installation.</span></span> <span data-ttu-id="509d2-136">Se hace referencia al marco compartido automáticamente al usar el SDK `Microsoft.NET.Sdk.Web` en el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="509d2-136">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="509d2-137">Para obtener más información, vea [referencia de Framework](xref:migration/22-to-30#framework-reference).</span><span class="sxs-lookup"><span data-stu-id="509d2-137">For more information, see [Framework reference](xref:migration/22-to-30#framework-reference).</span></span>

<span data-ttu-id="509d2-138">En ASP.NET Core, la `Startup` clase:</span><span class="sxs-lookup"><span data-stu-id="509d2-138">In ASP.NET Core, the `Startup` class:</span></span>

* <span data-ttu-id="509d2-139">Reemplaza a *global. asax*.</span><span class="sxs-lookup"><span data-stu-id="509d2-139">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="509d2-140">Controla todas las tareas de inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="509d2-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="509d2-141">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="509d2-141">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="509d2-142">En el proyecto de ASP.NET Core, abra el archivo *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="509d2-142">In the ASP.NET Core project, open the *Startup.cs* file:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

<span data-ttu-id="509d2-143">ASP.NET Core aplicaciones deben participar en las características de .NET Framework con middleware.</span><span class="sxs-lookup"><span data-stu-id="509d2-143">ASP.NET Core apps must opt in to framework features with middleware.</span></span> <span data-ttu-id="509d2-144">El código generado anteriormente por la plantilla agrega los siguientes servicios y middleware:</span><span class="sxs-lookup"><span data-stu-id="509d2-144">The previous template-generated code adds the following services and middleware:</span></span>

* <span data-ttu-id="509d2-145">El <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> método de extensión registra la compatibilidad del servicio MVC con los controladores, las características relacionadas con la API y las vistas.</span><span class="sxs-lookup"><span data-stu-id="509d2-145">The <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> extension method registers MVC service support for controllers, API-related features, and views.</span></span> <span data-ttu-id="509d2-146">Para obtener más información sobre las opciones de registro del servicio MVC, consulte [registro del servicio MVC](xref:migration/22-to-30#mvc-service-registration)</span><span class="sxs-lookup"><span data-stu-id="509d2-146">For more information on MVC service registration options, see [MVC service registration](xref:migration/22-to-30#mvc-service-registration)</span></span>
* <span data-ttu-id="509d2-147">El <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> método de extensión agrega el controlador de archivos estáticos `Microsoft.AspNetCore.StaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="509d2-147">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> extension method adds the static file handler `Microsoft.AspNetCore.StaticFiles`.</span></span> <span data-ttu-id="509d2-148">`UseStaticFiles`Se debe llamar antes al método de extensión `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="509d2-148">The `UseStaticFiles` extension method must be called before `UseRouting`.</span></span> <span data-ttu-id="509d2-149">Para obtener más información, vea <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="509d2-149">For more information, see <xref:fundamentals/static-files>.</span></span>
* <span data-ttu-id="509d2-150">El <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> método de extensión agrega enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="509d2-150">The <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> extension method adds routing.</span></span> <span data-ttu-id="509d2-151">Para obtener más información, vea <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="509d2-151">For more information, see <xref:fundamentals/routing>.</span></span>

<span data-ttu-id="509d2-152">Esta configuración existente incluye lo que se necesita para migrar el proyecto de ejemplo ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="509d2-152">This existing configuration includes what is needed to migrate the example ASP.NET MVC project.</span></span> <span data-ttu-id="509d2-153">Para obtener más información acerca de ASP.NET Core opciones de middleware, vea <xref:fundamentals/startup> .</span><span class="sxs-lookup"><span data-stu-id="509d2-153">For more information on ASP.NET Core middleware options, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-controllers-and-views"></a><span data-ttu-id="509d2-154">Migrar controladores y vistas</span><span class="sxs-lookup"><span data-stu-id="509d2-154">Migrate controllers and views</span></span>

<span data-ttu-id="509d2-155">En el proyecto de ASP.NET Core, se agregaría una nueva clase de controlador vacía y una clase de vista para servir como marcadores de posición usando los mismos nombres que las clases de controlador y de vista de cualquier proyecto de ASP.NET MVC del que migrar.</span><span class="sxs-lookup"><span data-stu-id="509d2-155">In the ASP.NET Core project, a new empty controller class and view class would be added to serve as placeholders using the same names as the controller and view classes in any ASP.NET MVC project to migrate from.</span></span>

<span data-ttu-id="509d2-156">El proyecto de ASP.NET Core *WebApp1* ya incluye un controlador de ejemplo mínimo y una vista con el mismo nombre que el proyecto de MVC de ASP.net.</span><span class="sxs-lookup"><span data-stu-id="509d2-156">The ASP.NET Core *WebApp1* project already includes a minimal example controller and view by the same name as the ASP.NET MVC project.</span></span> <span data-ttu-id="509d2-157">Por lo tanto, estos actuarán como marcadores de posición para el controlador ASP.NET MVC y las vistas que se van a migrar desde el proyecto de ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="509d2-157">So those will serve as placeholders for the ASP.NET MVC controller and views to be migrated from the ASP.NET MVC *WebApp1* project.</span></span>

1. <span data-ttu-id="509d2-158">Copie los métodos de MVC de ASP.NET `HomeController` para reemplazar los nuevos métodos de ASP.net Core `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="509d2-158">Copy the methods from the ASP.NET MVC `HomeController` to replace the new ASP.NET Core `HomeController` methods.</span></span> <span data-ttu-id="509d2-159">No es necesario cambiar el tipo de valor devuelto de los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="509d2-159">There's no need to change the return type of the action methods.</span></span> <span data-ttu-id="509d2-160">El tipo de valor devuelto del método de acción del controlador de la plantilla integrada de ASP.NET MVC es [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx). en ASP.NET Core MVC, los métodos de acción devuelven en `IActionResult` su lugar.</span><span class="sxs-lookup"><span data-stu-id="509d2-160">The ASP.NET MVC built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="509d2-161">`ActionResult` implementa `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="509d2-161">`ActionResult` implements `IActionResult`.</span></span>
1. <span data-ttu-id="509d2-162">En el proyecto de ASP.net Core, haga clic con el botón derecho en el directorio *views/Home* y seleccione **Agregar** > **elemento existente**.</span><span class="sxs-lookup"><span data-stu-id="509d2-162">In the ASP.NET Core project, right-click the *Views/Home* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="509d2-163">En el cuadro de diálogo **Add Existing Item (Agregar elemento existente** ), desplácese hasta el directorio de *inicios/* del proyecto de ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="509d2-163">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Home* directory.</span></span>
1. <span data-ttu-id="509d2-164">Seleccione los archivos *de vista about. cshtml*, *Contact. cshtml*e *index. cshtml* y, Razor a continuación, seleccione **Agregar**y reemplace los archivos existentes.</span><span class="sxs-lookup"><span data-stu-id="509d2-164">Select the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files, then select **Add**, replacing the existing files.</span></span>

<span data-ttu-id="509d2-165">Para obtener más información, vea <xref:mvc/controllers/actions> y <xref:mvc/views/overview>.</span><span class="sxs-lookup"><span data-stu-id="509d2-165">For more information, see <xref:mvc/controllers/actions> and <xref:mvc/views/overview>.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="509d2-166">Prueba de cada método</span><span class="sxs-lookup"><span data-stu-id="509d2-166">Test each method</span></span>

<span data-ttu-id="509d2-167">Los puntos de conexión de cada controlador se pueden probar, sin embargo, el diseño y los estilos se describen más adelante en el documento.</span><span class="sxs-lookup"><span data-stu-id="509d2-167">Each controller endpoint can be tested, however, layout and styles are covered later in the document.</span></span>

1. <span data-ttu-id="509d2-168">Ejecute la aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="509d2-168">Run the ASP.NET Core app.</span></span>
1. <span data-ttu-id="509d2-169">Invoque las vistas representadas desde el explorador en la aplicación ASP.NET Core en ejecución reemplazando el número de puerto actual por el número de Puerto usado en el proyecto de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="509d2-169">Invoke the rendered views from the browser on the running ASP.NET Core app by replacing the current port number with the port number used in the ASP.NET Core project.</span></span> <span data-ttu-id="509d2-170">Por ejemplo: `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="509d2-170">For example, `https://localhost:44375/home/about`.</span></span>

## <a name="migrate-static-content"></a><span data-ttu-id="509d2-171">Migrar contenido estático</span><span class="sxs-lookup"><span data-stu-id="509d2-171">Migrate static content</span></span>

<span data-ttu-id="509d2-172">En ASP.NET MVC 5 y versiones anteriores, el contenido estático se hospedaba en el directorio raíz del proyecto web y se combinaba con los archivos del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="509d2-172">In ASP.NET MVC 5 and earlier, static content was hosted from the web project's root directory and was intermixed with server-side files.</span></span> <span data-ttu-id="509d2-173">En ASP.NET Core, los archivos estáticos se almacenan en el directorio [raíz Web](xref:fundamentals/index#web-root) del proyecto.</span><span class="sxs-lookup"><span data-stu-id="509d2-173">In ASP.NET Core, static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="509d2-174">El directorio predeterminado es *{Content root}/wwwroot*, pero se puede cambiar.</span><span class="sxs-lookup"><span data-stu-id="509d2-174">The default directory is *{content root}/wwwroot*, but it can be changed.</span></span> <span data-ttu-id="509d2-175">Para obtener más información, vea [Archivos estáticos en ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span><span class="sxs-lookup"><span data-stu-id="509d2-175">For more information, see [Static files in ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span></span>

<span data-ttu-id="509d2-176">Copie el contenido estático del proyecto de ASP.NET MVC *WebApp1* en el directorio *wwwroot* del proyecto ASP.net Core *WebApp1* :</span><span class="sxs-lookup"><span data-stu-id="509d2-176">Copy the static content from the ASP.NET MVC *WebApp1* project to the *wwwroot* directory in the ASP.NET Core *WebApp1* project:</span></span>

1. <span data-ttu-id="509d2-177">En el proyecto de ASP.net Core, haga clic con el botón secundario en el directorio *wwwroot* y seleccione **Agregar** > **elemento existente**.</span><span class="sxs-lookup"><span data-stu-id="509d2-177">In the ASP.NET Core project, right-click the *wwwroot* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="509d2-178">En el cuadro de diálogo **Agregar elemento existente** , vaya al proyecto de ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="509d2-178">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project.</span></span>
1. <span data-ttu-id="509d2-179">Seleccione el archivo *favicon. ico* y, a continuación, seleccione **Agregar**, reemplazando el archivo existente.</span><span class="sxs-lookup"><span data-stu-id="509d2-179">Select the *favicon.ico* file, then select **Add**, replacing the existing file.</span></span>

## <a name="migrate-the-layout-files"></a><span data-ttu-id="509d2-180">Migrar los archivos de diseño</span><span class="sxs-lookup"><span data-stu-id="509d2-180">Migrate the layout files</span></span>

<span data-ttu-id="509d2-181">Copie los archivos de diseño del proyecto de ASP.NET MVC en el proyecto ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="509d2-181">Copy the ASP.NET MVC project layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="509d2-182">En el proyecto de ASP.net Core, haga clic con el botón derecho en el directorio *views* y seleccione **Agregar** > **elemento existente**.</span><span class="sxs-lookup"><span data-stu-id="509d2-182">In the ASP.NET Core project, right-click the *Views* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="509d2-183">En el cuadro de diálogo **Agregar elemento existente** , vaya al directorio de *vistas* del proyecto de ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="509d2-183">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views* directory.</span></span>
1. <span data-ttu-id="509d2-184">Seleccione el archivo *_ViewStart. cshtml* y, después, seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="509d2-184">Select the *_ViewStart.cshtml* file then select **Add**.</span></span>

<span data-ttu-id="509d2-185">Copie los archivos de diseño compartidos del proyecto de ASP.NET MVC en el proyecto ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="509d2-185">Copy the ASP.NET MVC project shared layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="509d2-186">En el proyecto de ASP.net Core, haga clic con el botón derecho en el directorio *views/Shared* y seleccione **Agregar** > **elemento existente**.</span><span class="sxs-lookup"><span data-stu-id="509d2-186">In the ASP.NET Core project, right-click the *Views/Shared* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="509d2-187">En el cuadro de diálogo **Agregar elemento existente** , desplácese hasta el directorio *compartido o vistas* del proyecto de ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="509d2-187">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Shared* directory.</span></span>
1. <span data-ttu-id="509d2-188">Seleccione el archivo *_Layout. cshtml* , seleccione **Agregar**y reemplace el archivo existente.</span><span class="sxs-lookup"><span data-stu-id="509d2-188">Select the *_Layout.cshtml* file, then select **Add**, replacing the existing file.</span></span>

<span data-ttu-id="509d2-189">En el proyecto de ASP.NET Core, abra el archivo *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="509d2-189">In the ASP.NET Core project, open the *_Layout.cshtml* file.</span></span> <span data-ttu-id="509d2-190">Realice los siguientes cambios para que coincidan con el código completado que se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="509d2-190">Make the following changes to match the completed code shown below:</span></span>

<span data-ttu-id="509d2-191">Actualice la inclusión de CSS de bootstrap para que coincida con el código completado a continuación:</span><span class="sxs-lookup"><span data-stu-id="509d2-191">Update the Bootstrap CSS inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="509d2-192">Reemplace `@Styles.Render("~/Content/css")` por un `<link>` elemento para cargar *bootstrap. CSS* (consulte a continuación).</span><span class="sxs-lookup"><span data-stu-id="509d2-192">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>
1. <span data-ttu-id="509d2-193">Quite `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="509d2-193">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

<span data-ttu-id="509d2-194">El marcado de reemplazo completado para la inclusión de CSS de bootstrap:</span><span class="sxs-lookup"><span data-stu-id="509d2-194">The completed replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="509d2-195">Actualice la inclusión de jQuery y bootstrap de JavaScript para que coincida con el código completado a continuación:</span><span class="sxs-lookup"><span data-stu-id="509d2-195">Update the jQuery and Bootstrap JavaScript inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="509d2-196">Reemplazar `@Scripts.Render("~/bundles/jquery")` por un `<script>` elemento (vea más abajo).</span><span class="sxs-lookup"><span data-stu-id="509d2-196">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>
1. <span data-ttu-id="509d2-197">Reemplazar `@Scripts.Render("~/bundles/bootstrap")` por un `<script>` elemento (vea más abajo).</span><span class="sxs-lookup"><span data-stu-id="509d2-197">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="509d2-198">El marcado de reemplazo completado para jQuery y bootstrap script de inclusión:</span><span class="sxs-lookup"><span data-stu-id="509d2-198">The completed replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="509d2-199">El archivo *_Layout. cshtml* actualizado se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="509d2-199">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

<span data-ttu-id="509d2-200">Vea el sitio en el explorador.</span><span class="sxs-lookup"><span data-stu-id="509d2-200">View the site in the browser.</span></span> <span data-ttu-id="509d2-201">Debe representarse con los estilos esperados en su lugar.</span><span class="sxs-lookup"><span data-stu-id="509d2-201">It should render with the expected styles in place.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="509d2-202">Configuración de la unión y la minimización</span><span class="sxs-lookup"><span data-stu-id="509d2-202">Configure bundling and minification</span></span>

<span data-ttu-id="509d2-203">ASP.NET Core es compatible con varias soluciones minificación y de empaquetado de código abierto, como [Weboptimizer](https://github.com/ligershark/WebOptimizer) y otras bibliotecas similares.</span><span class="sxs-lookup"><span data-stu-id="509d2-203">ASP.NET Core is compatible with several open-source bundling and minification solutions such as [WebOptimizer](https://github.com/ligershark/WebOptimizer) and other similar libraries.</span></span> <span data-ttu-id="509d2-204">ASP.NET Core no proporciona una solución minificación y de Unión nativa.</span><span class="sxs-lookup"><span data-stu-id="509d2-204">ASP.NET Core doesn't provide a native bundling and minification solution.</span></span> <span data-ttu-id="509d2-205">Para obtener información sobre la configuración de la agrupación y la minificación, consulte [agrupación y minificación](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="509d2-205">For information on configuring bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="509d2-206">Solucionar errores HTTP 500</span><span class="sxs-lookup"><span data-stu-id="509d2-206">Solve HTTP 500 errors</span></span>

<span data-ttu-id="509d2-207">Hay muchos problemas que pueden provocar un mensaje de error HTTP 500 que no contiene información sobre el origen del problema.</span><span class="sxs-lookup"><span data-stu-id="509d2-207">There are many problems that can cause an HTTP 500 error message that contains no information on the source of the problem.</span></span> <span data-ttu-id="509d2-208">Por ejemplo, si el archivo *views/_ViewImports. cshtml* contiene un espacio de nombres que no existe en el proyecto, se genera un error http 500.</span><span class="sxs-lookup"><span data-stu-id="509d2-208">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, an HTTP 500 error is generated.</span></span> <span data-ttu-id="509d2-209">De forma predeterminada, en ASP.NET Core aplicaciones, la `UseDeveloperExceptionPage` extensión se agrega a `IApplicationBuilder` y se ejecuta cuando el entorno se está *desarrollando*.</span><span class="sxs-lookup"><span data-stu-id="509d2-209">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the environment is *Development*.</span></span> <span data-ttu-id="509d2-210">Esto se detalla en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="509d2-210">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

<span data-ttu-id="509d2-211">ASP.NET Core convierte las excepciones no controladas en respuestas de error HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="509d2-211">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="509d2-212">Normalmente, los detalles del error no se incluyen en estas respuestas para evitar la divulgación de información potencialmente confidencial sobre el servidor.</span><span class="sxs-lookup"><span data-stu-id="509d2-212">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="509d2-213">Para obtener más información, consulte la [Página de excepciones para desarrolladores](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="509d2-213">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="next-steps"></a><span data-ttu-id="509d2-214">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="509d2-214">Next steps</span></span>

* <xref:migration/identity>

## <a name="additional-resources"></a><span data-ttu-id="509d2-215">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="509d2-215">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="509d2-216">En este artículo se muestra cómo iniciar la migración de un proyecto de ASP.NET MVC a [ASP.net Core mvc](xref:mvc/overview) 2,2.</span><span class="sxs-lookup"><span data-stu-id="509d2-216">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.2.</span></span> <span data-ttu-id="509d2-217">En el proceso, se resaltan muchas de las cosas que han cambiado de ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="509d2-217">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="509d2-218">La migración de ASP.NET MVC es un proceso de varios pasos.</span><span class="sxs-lookup"><span data-stu-id="509d2-218">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="509d2-219">En este artículo se describe:</span><span class="sxs-lookup"><span data-stu-id="509d2-219">This article covers:</span></span>

* <span data-ttu-id="509d2-220">Instalación inicial</span><span class="sxs-lookup"><span data-stu-id="509d2-220">Initial setup</span></span>
* <span data-ttu-id="509d2-221">Controladores y vistas básicos</span><span class="sxs-lookup"><span data-stu-id="509d2-221">Basic controllers and views</span></span>
* <span data-ttu-id="509d2-222">Contenido estático</span><span class="sxs-lookup"><span data-stu-id="509d2-222">Static content</span></span>
* <span data-ttu-id="509d2-223">Dependencias del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="509d2-223">Client-side dependencies.</span></span>

<span data-ttu-id="509d2-224">Para migrar la configuración y el Identity código, vea <xref:migration/configuration> y <xref:migration/identity> .</span><span class="sxs-lookup"><span data-stu-id="509d2-224">For migrating configuration and Identity code, see <xref:migration/configuration> and <xref:migration/identity>.</span></span>

> [!NOTE]
> <span data-ttu-id="509d2-225">Es posible que los números de versión de los ejemplos no estén actualizados, actualice los proyectos en consecuencia.</span><span class="sxs-lookup"><span data-stu-id="509d2-225">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="509d2-226">Crear el proyecto de MVC ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="509d2-226">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="509d2-227">Para demostrar la actualización, comenzaremos por crear una aplicación ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="509d2-227">To demonstrate the upgrade, we'll start by creating an ASP.NET MVC app.</span></span> <span data-ttu-id="509d2-228">Créelo con el nombre *WebApp1* para que el espacio de nombres coincida con el proyecto de ASP.net Core creado en el paso siguiente.</span><span class="sxs-lookup"><span data-stu-id="509d2-228">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Cuadro de diálogo Nuevo proyecto de Visual Studio](mvc/_static/new-project.png)

![Cuadro de diálogo Nueva aplicación web: plantilla de proyecto de MVC seleccionada en el panel de plantillas de ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="509d2-231">*Opcional:* Cambie el nombre de la solución de *WebApp1* a *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="509d2-231">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="509d2-232">Visual Studio muestra el nuevo nombre de la solución (*Mvc5*), lo que facilita la información de este proyecto desde el proyecto siguiente.</span><span class="sxs-lookup"><span data-stu-id="509d2-232">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="509d2-233">Crear el proyecto de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="509d2-233">Create the ASP.NET Core project</span></span>

<span data-ttu-id="509d2-234">Cree una nueva aplicación Web *vacía* de ASP.net Core con el mismo nombre que el proyecto anterior (*WebApp1*) para que coincidan los espacios de nombres de los dos proyectos.</span><span class="sxs-lookup"><span data-stu-id="509d2-234">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="509d2-235">Tener el mismo espacio de nombres facilita la copia de código entre los dos proyectos.</span><span class="sxs-lookup"><span data-stu-id="509d2-235">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="509d2-236">Cree este proyecto en un directorio diferente al del proyecto anterior para usar el mismo nombre.</span><span class="sxs-lookup"><span data-stu-id="509d2-236">Create this project in a different directory than the previous project to use the same name.</span></span>

![Cuadro de diálogo Nuevo proyecto](mvc/_static/new_core.png)

![Cuadro de diálogo Nueva aplicación Web de ASP.NET: plantilla de proyecto vacía seleccionada en el panel de plantillas de ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="509d2-239">*Opcional:* Cree una nueva aplicación de ASP.NET Core mediante la plantilla de proyecto de *aplicación web* .</span><span class="sxs-lookup"><span data-stu-id="509d2-239">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="509d2-240">Asigne al proyecto el nombre *WebApp1*y seleccione una opción de autenticación de **cuentas de usuario individuales**.</span><span class="sxs-lookup"><span data-stu-id="509d2-240">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="509d2-241">Cambie el nombre de esta aplicación a *FullAspNetCore*.</span><span class="sxs-lookup"><span data-stu-id="509d2-241">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="509d2-242">Al crear este proyecto, se ahorra tiempo en la conversión.</span><span class="sxs-lookup"><span data-stu-id="509d2-242">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="509d2-243">El resultado final se puede ver en el código generado por la plantilla, el código puede copiarse en el proyecto de conversión o compararse con el proyecto generado por una plantilla.</span><span class="sxs-lookup"><span data-stu-id="509d2-243">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="509d2-244">Configurar el sitio para usar MVC</span><span class="sxs-lookup"><span data-stu-id="509d2-244">Configure the site to use MVC</span></span>

* <span data-ttu-id="509d2-245">Cuando el destino es .NET Core, se hace referencia al [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="509d2-245">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="509d2-246">Este paquete contiene los paquetes que usan normalmente las aplicaciones MVC.</span><span class="sxs-lookup"><span data-stu-id="509d2-246">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="509d2-247">Si el destino es .NET Framework, las referencias del paquete deben aparecer de forma individual en el archivo del proyecto.</span><span class="sxs-lookup"><span data-stu-id="509d2-247">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="509d2-248">`Microsoft.AspNetCore.Mvc`es el marco de MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="509d2-248">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="509d2-249">`Microsoft.AspNetCore.StaticFiles`es el controlador de archivos estático.</span><span class="sxs-lookup"><span data-stu-id="509d2-249">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="509d2-250">ASP.NET Core aplicaciones optan explícitamente a un middleware, como para servir archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="509d2-250">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="509d2-251">Para obtener más información, consulte [Archivos estáticos](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="509d2-251">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="509d2-252">Abra el archivo *Startup.CS* y cambie el código para que coincida con lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="509d2-252">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="509d2-253">El <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> método de extensión agrega el controlador de archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="509d2-253">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="509d2-254">Para obtener más información, consulte Inicio y [enrutamiento](xref:fundamentals/routing)de la [aplicación](xref:fundamentals/startup) .</span><span class="sxs-lookup"><span data-stu-id="509d2-254">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="509d2-255">Agregar un controlador y una vista</span><span class="sxs-lookup"><span data-stu-id="509d2-255">Add a controller and view</span></span>

<span data-ttu-id="509d2-256">En esta sección, se agrega un controlador y una vista mínimos para servir como marcadores de posición para el controlador ASP.NET MVC y las vistas migradas en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="509d2-256">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="509d2-257">Agregue un directorio de *Controladores* .</span><span class="sxs-lookup"><span data-stu-id="509d2-257">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="509d2-258">Agregue una **clase de controlador** denominada *HomeController.CS* al directorio *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="509d2-258">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![Cuadro de diálogo Agregar nuevo elemento](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="509d2-260">Agregue un directorio *views* .</span><span class="sxs-lookup"><span data-stu-id="509d2-260">Add a *Views* directory.</span></span>

* <span data-ttu-id="509d2-261">Agregue un directorio de *vistas o inicio* .</span><span class="sxs-lookup"><span data-stu-id="509d2-261">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="509d2-262">Agregue una \*\* Razor vista\*\* denominada *index. cshtml* al directorio *views/Home* .</span><span class="sxs-lookup"><span data-stu-id="509d2-262">Add a **Razor View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![Cuadro de diálogo Agregar nuevo elemento](mvc/_static/view.png)

<span data-ttu-id="509d2-264">La estructura del proyecto se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="509d2-264">The project structure is shown below:</span></span>

![Explorador de soluciones que muestra los archivos y directorios de WebApp1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="509d2-266">Reemplace el contenido del archivo *Views/Home/Index.cshtml* por el marcado siguiente:</span><span class="sxs-lookup"><span data-stu-id="509d2-266">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="509d2-267">Ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="509d2-267">Run the app.</span></span>

![Aplicación web abierta en Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="509d2-269">Para obtener más información, vea [Controladores](xref:mvc/controllers/actions) y [vistas](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="509d2-269">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="509d2-270">La funcionalidad siguiente requiere la migración del proyecto de ejemplo ASP.NET MVC al proyecto ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="509d2-270">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="509d2-271">contenido del lado cliente (CSS, fuentes y scripts)</span><span class="sxs-lookup"><span data-stu-id="509d2-271">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="509d2-272">controllers</span><span class="sxs-lookup"><span data-stu-id="509d2-272">controllers</span></span>

* <span data-ttu-id="509d2-273">views</span><span class="sxs-lookup"><span data-stu-id="509d2-273">views</span></span>

* <span data-ttu-id="509d2-274">modelos</span><span class="sxs-lookup"><span data-stu-id="509d2-274">models</span></span>

* <span data-ttu-id="509d2-275">Unión</span><span class="sxs-lookup"><span data-stu-id="509d2-275">bundling</span></span>

* <span data-ttu-id="509d2-276">filters</span><span class="sxs-lookup"><span data-stu-id="509d2-276">filters</span></span>

* <span data-ttu-id="509d2-277">Iniciar y cerrar sesión Identity (esto se hace en el siguiente tutorial).</span><span class="sxs-lookup"><span data-stu-id="509d2-277">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="509d2-278">Controladores y vistas</span><span class="sxs-lookup"><span data-stu-id="509d2-278">Controllers and views</span></span>

* <span data-ttu-id="509d2-279">Copie cada uno de los métodos de MVC de ASP.NET `HomeController` al nuevo `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="509d2-279">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="509d2-280">En ASP.NET MVC, el tipo de valor devuelto del método de acción del controlador de la plantilla integrada es [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx). en ASP.NET Core MVC, los métodos de acción devuelven en `IActionResult` su lugar.</span><span class="sxs-lookup"><span data-stu-id="509d2-280">In ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="509d2-281">`ActionResult`implementa `IActionResult` , por lo que no es necesario cambiar el tipo de valor devuelto de los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="509d2-281">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="509d2-282">Copie los archivos de vista *About. cshtml*, *Contact. cshtml*e *Index. cshtml* Razor del proyecto ASP.NET MVC en el proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="509d2-282">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="509d2-283">Prueba de cada método</span><span class="sxs-lookup"><span data-stu-id="509d2-283">Test each method</span></span>

<span data-ttu-id="509d2-284">Todavía no se han migrado el archivo de diseño y los estilos, por lo que las vistas representadas solo contienen el contenido de los archivos de vista.</span><span class="sxs-lookup"><span data-stu-id="509d2-284">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="509d2-285">Los vínculos generados por el archivo de diseño para las `About` `Contact` vistas y no estarán disponibles todavía.</span><span class="sxs-lookup"><span data-stu-id="509d2-285">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

<span data-ttu-id="509d2-286">Invoque las vistas representadas desde el explorador en la aplicación ASP.NET Core en ejecución reemplazando el número de puerto actual por el número de Puerto usado en el proyecto de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="509d2-286">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="509d2-287">Por ejemplo: `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="509d2-287">For example: `https://localhost:44375/home/about`.</span></span>

![Página de contacto](mvc/_static/contact-page.png)

<span data-ttu-id="509d2-289">Tenga en cuenta la falta de estilo y elementos de menú.</span><span class="sxs-lookup"><span data-stu-id="509d2-289">Note the lack of styling and menu items.</span></span> <span data-ttu-id="509d2-290">El estilo se corregirá en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="509d2-290">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="509d2-291">Contenido estático</span><span class="sxs-lookup"><span data-stu-id="509d2-291">Static content</span></span>

<span data-ttu-id="509d2-292">En ASP.NET MVC 5 y versiones anteriores, el contenido estático se hospedaba en la raíz del proyecto web y se combinaba con los archivos del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="509d2-292">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="509d2-293">En ASP.NET Core, el contenido estático se hospeda en el directorio *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="509d2-293">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="509d2-294">Copie el contenido estático de la aplicación ASP.NET MVC en el directorio *wwwroot* del proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="509d2-294">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="509d2-295">En esta conversión de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="509d2-295">In this sample conversion:</span></span>

* <span data-ttu-id="509d2-296">Copie el archivo *favicon. ico* del proyecto ASP.NET MVC en el directorio *wwwroot* del proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="509d2-296">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="509d2-297">El proyecto ASP.NET MVC usa [bootstrap](https://getbootstrap.com/) para su estilo y almacena los archivos de arranque en los directorios de *contenido* y *scripts* .</span><span class="sxs-lookup"><span data-stu-id="509d2-297">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="509d2-298">La plantilla, que generó el proyecto ASP.NET MVC, hace referencia a bootstrap en el archivo de diseño (*views/Shared/_Layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="509d2-298">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="509d2-299">Los archivos *bootstrap.js* y *bootstrap. CSS* se pueden copiar del proyecto ASP.NET MVC al directorio *wwwroot* del nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="509d2-299">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="509d2-300">En su lugar, este documento agrega compatibilidad con bootstrap (y otras bibliotecas del lado cliente) mediante redes CDN, en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="509d2-300">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="509d2-301">Migrar el archivo de diseño</span><span class="sxs-lookup"><span data-stu-id="509d2-301">Migrate the layout file</span></span>

* <span data-ttu-id="509d2-302">Copie el archivo *_ViewStart. cshtml* del directorio *views* del proyecto ASP.NET MVC en el directorio *views* del proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="509d2-302">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="509d2-303">El archivo *_ViewStart. cshtml* no ha cambiado en ASP.net Core MVC.</span><span class="sxs-lookup"><span data-stu-id="509d2-303">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="509d2-304">Cree un directorio *views/Shared* .</span><span class="sxs-lookup"><span data-stu-id="509d2-304">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="509d2-305">*Opcional:* Copie *_ViewImports. cshtml* del directorio *views* del proyecto de *FullAspNetCore* MVC en el directorio *views* del proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="509d2-305">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="509d2-306">Quite cualquier declaración de espacio de nombres del archivo *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="509d2-306">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="509d2-307">El archivo *_ViewImports. cshtml* proporciona espacios de nombres para todos los archivos de vista y ofrece [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="509d2-307">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="509d2-308">Las aplicaciones auxiliares de etiquetas se usan en el nuevo archivo de diseño.</span><span class="sxs-lookup"><span data-stu-id="509d2-308">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="509d2-309">El archivo *_ViewImports. cshtml* es nuevo para ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="509d2-309">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="509d2-310">Copie el archivo *_Layout. cshtml* del directorio de *vistas/compartidos* del proyecto de ASP.NET MVC en el directorio de *vistas/compartidos* del proyecto de ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="509d2-310">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="509d2-311">Abra *_Layout archivo. cshtml* y realice los cambios siguientes (el código completo se muestra a continuación):</span><span class="sxs-lookup"><span data-stu-id="509d2-311">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="509d2-312">Reemplace `@Styles.Render("~/Content/css")` por un `<link>` elemento para cargar *bootstrap. CSS* (consulte a continuación).</span><span class="sxs-lookup"><span data-stu-id="509d2-312">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="509d2-313">Quite `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="509d2-313">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="509d2-314">Comente la `@Html.Partial("_LoginPartial")` línea (rodee la línea con `@*...*@` ).</span><span class="sxs-lookup"><span data-stu-id="509d2-314">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="509d2-315">Para obtener más información, vea [migrar la autenticación y Identity ASP.net Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="509d2-315">For more information, see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="509d2-316">Reemplazar `@Scripts.Render("~/bundles/jquery")` por un `<script>` elemento (vea más abajo).</span><span class="sxs-lookup"><span data-stu-id="509d2-316">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="509d2-317">Reemplazar `@Scripts.Render("~/bundles/bootstrap")` por un `<script>` elemento (vea más abajo).</span><span class="sxs-lookup"><span data-stu-id="509d2-317">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="509d2-318">El marcado de reemplazo para la inclusión de CSS de bootstrap:</span><span class="sxs-lookup"><span data-stu-id="509d2-318">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="509d2-319">El marcado de reemplazo para jQuery y bootstrap JavaScript incluirá:</span><span class="sxs-lookup"><span data-stu-id="509d2-319">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="509d2-320">El archivo *_Layout. cshtml* actualizado se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="509d2-320">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="509d2-321">Vea el sitio en el explorador.</span><span class="sxs-lookup"><span data-stu-id="509d2-321">View the site in the browser.</span></span> <span data-ttu-id="509d2-322">Ahora debería cargarse correctamente, con los estilos esperados en su lugar.</span><span class="sxs-lookup"><span data-stu-id="509d2-322">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="509d2-323">*Opcional:* Pruebe a usar el nuevo archivo de diseño.</span><span class="sxs-lookup"><span data-stu-id="509d2-323">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="509d2-324">Copie el archivo de diseño del proyecto *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="509d2-324">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="509d2-325">El nuevo archivo de diseño utiliza [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) y otras mejoras.</span><span class="sxs-lookup"><span data-stu-id="509d2-325">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="509d2-326">Configuración de la unión y la minimización</span><span class="sxs-lookup"><span data-stu-id="509d2-326">Configure bundling and minification</span></span>

<span data-ttu-id="509d2-327">Para obtener información acerca de cómo configurar la agrupación y la minificación, consulte [agrupación y minificación](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="509d2-327">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="509d2-328">Solucionar errores HTTP 500</span><span class="sxs-lookup"><span data-stu-id="509d2-328">Solve HTTP 500 errors</span></span>

<span data-ttu-id="509d2-329">Hay muchos problemas que pueden producir mensajes de error HTTP 500 que no contienen información sobre el origen del problema.</span><span class="sxs-lookup"><span data-stu-id="509d2-329">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="509d2-330">Por ejemplo, si el archivo *views/_ViewImports. cshtml* contiene un espacio de nombres que no existe en el proyecto, se genera un error http 500.</span><span class="sxs-lookup"><span data-stu-id="509d2-330">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="509d2-331">De forma predeterminada, en ASP.NET Core aplicaciones, la `UseDeveloperExceptionPage` extensión se agrega a `IApplicationBuilder` y se ejecuta cuando la configuración es *desarrollo*.</span><span class="sxs-lookup"><span data-stu-id="509d2-331">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="509d2-332">Vea un ejemplo en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="509d2-332">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="509d2-333">ASP.NET Core convierte las excepciones no controladas en respuestas de error HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="509d2-333">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="509d2-334">Normalmente, los detalles del error no se incluyen en estas respuestas para evitar la divulgación de información potencialmente confidencial sobre el servidor.</span><span class="sxs-lookup"><span data-stu-id="509d2-334">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="509d2-335">Para obtener más información, consulte la [Página de excepciones para desarrolladores](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="509d2-335">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="509d2-336">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="509d2-336">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="509d2-337">En este artículo se muestra cómo iniciar la migración de un proyecto de ASP.NET MVC a [ASP.net Core mvc](xref:mvc/overview) 2,1.</span><span class="sxs-lookup"><span data-stu-id="509d2-337">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.1.</span></span> <span data-ttu-id="509d2-338">En el proceso, se resaltan muchas de las cosas que han cambiado de ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="509d2-338">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="509d2-339">La migración de ASP.NET MVC es un proceso de varios pasos.</span><span class="sxs-lookup"><span data-stu-id="509d2-339">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="509d2-340">En este artículo se describe:</span><span class="sxs-lookup"><span data-stu-id="509d2-340">This article covers:</span></span>

* <span data-ttu-id="509d2-341">Instalación inicial</span><span class="sxs-lookup"><span data-stu-id="509d2-341">Initial setup</span></span>
* <span data-ttu-id="509d2-342">Controladores y vistas básicos</span><span class="sxs-lookup"><span data-stu-id="509d2-342">Basic controllers and views</span></span>
* <span data-ttu-id="509d2-343">Contenido estático</span><span class="sxs-lookup"><span data-stu-id="509d2-343">Static content</span></span>
* <span data-ttu-id="509d2-344">Dependencias del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="509d2-344">Client-side dependencies.</span></span>

<span data-ttu-id="509d2-345">Para migrar la configuración y el Identity código, vea [migrar la configuración a ASP.net Core](xref:migration/configuration) y [migrar la autenticación y Identity a ASP.net Core](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="509d2-345">For migrating configuration and Identity code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity).</span></span>

> [!NOTE]
> <span data-ttu-id="509d2-346">Es posible que los números de versión de los ejemplos no estén actualizados, actualice los proyectos en consecuencia.</span><span class="sxs-lookup"><span data-stu-id="509d2-346">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="509d2-347">Crear el proyecto de MVC ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="509d2-347">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="509d2-348">Para demostrar la actualización, comenzaremos por crear una aplicación de ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="509d2-348">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="509d2-349">Créelo con el nombre *WebApp1* para que el espacio de nombres coincida con el proyecto de ASP.net Core creado en el paso siguiente.</span><span class="sxs-lookup"><span data-stu-id="509d2-349">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Cuadro de diálogo Nuevo proyecto de Visual Studio](mvc/_static/new-project.png)

![Cuadro de diálogo Nueva aplicación web: plantilla de proyecto de MVC seleccionada en el panel de plantillas de ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="509d2-352">*Opcional:* Cambie el nombre de la solución de *WebApp1* a *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="509d2-352">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="509d2-353">Visual Studio muestra el nuevo nombre de la solución (*Mvc5*), lo que facilita la información de este proyecto desde el proyecto siguiente.</span><span class="sxs-lookup"><span data-stu-id="509d2-353">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="509d2-354">Crear el proyecto de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="509d2-354">Create the ASP.NET Core project</span></span>

<span data-ttu-id="509d2-355">Cree una nueva aplicación Web *vacía* de ASP.net Core con el mismo nombre que el proyecto anterior (*WebApp1*) para que coincidan los espacios de nombres de los dos proyectos.</span><span class="sxs-lookup"><span data-stu-id="509d2-355">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="509d2-356">Tener el mismo espacio de nombres facilita la copia de código entre los dos proyectos.</span><span class="sxs-lookup"><span data-stu-id="509d2-356">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="509d2-357">Cree este proyecto en un directorio diferente al del proyecto anterior para usar el mismo nombre.</span><span class="sxs-lookup"><span data-stu-id="509d2-357">Create this project in a different directory than the previous project to use the same name.</span></span>

![Cuadro de diálogo Nuevo proyecto](mvc/_static/new_core.png)

![Cuadro de diálogo Nueva aplicación Web de ASP.NET: plantilla de proyecto vacía seleccionada en el panel de plantillas de ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="509d2-360">*Opcional:* Cree una nueva aplicación de ASP.NET Core mediante la plantilla de proyecto de *aplicación web* .</span><span class="sxs-lookup"><span data-stu-id="509d2-360">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="509d2-361">Asigne al proyecto el nombre *WebApp1*y seleccione una opción de autenticación de **cuentas de usuario individuales**.</span><span class="sxs-lookup"><span data-stu-id="509d2-361">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="509d2-362">Cambie el nombre de esta aplicación a *FullAspNetCore*.</span><span class="sxs-lookup"><span data-stu-id="509d2-362">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="509d2-363">Al crear este proyecto, se ahorra tiempo en la conversión.</span><span class="sxs-lookup"><span data-stu-id="509d2-363">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="509d2-364">El resultado final se puede ver en el código generado por la plantilla, el código puede copiarse en el proyecto de conversión o compararse con el proyecto generado por una plantilla.</span><span class="sxs-lookup"><span data-stu-id="509d2-364">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="509d2-365">Configurar el sitio para usar MVC</span><span class="sxs-lookup"><span data-stu-id="509d2-365">Configure the site to use MVC</span></span>

* <span data-ttu-id="509d2-366">Cuando el destino es .NET Core, se hace referencia al [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="509d2-366">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="509d2-367">Este paquete contiene los paquetes que usan normalmente las aplicaciones MVC.</span><span class="sxs-lookup"><span data-stu-id="509d2-367">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="509d2-368">Si el destino es .NET Framework, las referencias del paquete deben aparecer de forma individual en el archivo del proyecto.</span><span class="sxs-lookup"><span data-stu-id="509d2-368">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="509d2-369">`Microsoft.AspNetCore.Mvc`es el marco de MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="509d2-369">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="509d2-370">`Microsoft.AspNetCore.StaticFiles`es el controlador de archivos estático.</span><span class="sxs-lookup"><span data-stu-id="509d2-370">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="509d2-371">ASP.NET Core aplicaciones optan explícitamente a un middleware, como para servir archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="509d2-371">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="509d2-372">Para obtener más información, consulte [Archivos estáticos](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="509d2-372">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="509d2-373">Abra el archivo *Startup.CS* y cambie el código para que coincida con lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="509d2-373">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="509d2-374">El <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> método de extensión agrega el controlador de archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="509d2-374">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="509d2-375">El `UseMvc` método de extensión agrega enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="509d2-375">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="509d2-376">Para obtener más información, consulte Inicio y [enrutamiento](xref:fundamentals/routing)de la [aplicación](xref:fundamentals/startup) .</span><span class="sxs-lookup"><span data-stu-id="509d2-376">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="509d2-377">Agregar un controlador y una vista</span><span class="sxs-lookup"><span data-stu-id="509d2-377">Add a controller and view</span></span>

<span data-ttu-id="509d2-378">En esta sección, se agrega un controlador y una vista mínimos para servir como marcadores de posición para el controlador ASP.NET MVC y las vistas migradas en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="509d2-378">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="509d2-379">Agregue un directorio de *Controladores* .</span><span class="sxs-lookup"><span data-stu-id="509d2-379">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="509d2-380">Agregue una **clase de controlador** denominada *HomeController.CS* al directorio *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="509d2-380">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![Cuadro de diálogo Agregar nuevo elemento](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="509d2-382">Agregue un directorio *views* .</span><span class="sxs-lookup"><span data-stu-id="509d2-382">Add a *Views* directory.</span></span>

* <span data-ttu-id="509d2-383">Agregue un directorio de *vistas o inicio* .</span><span class="sxs-lookup"><span data-stu-id="509d2-383">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="509d2-384">Agregue una \*\* Razor vista\*\* denominada *index. cshtml* al directorio *views/Home* .</span><span class="sxs-lookup"><span data-stu-id="509d2-384">Add a **Razor View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![Cuadro de diálogo Agregar nuevo elemento](mvc/_static/view.png)

<span data-ttu-id="509d2-386">La estructura del proyecto se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="509d2-386">The project structure is shown below:</span></span>

![Explorador de soluciones que muestra los archivos y directorios de WebApp1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="509d2-388">Reemplace el contenido del archivo *Views/Home/Index.cshtml* por el marcado siguiente:</span><span class="sxs-lookup"><span data-stu-id="509d2-388">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="509d2-389">Ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="509d2-389">Run the app.</span></span>

![Aplicación web abierta en Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="509d2-391">Para obtener más información, vea [Controladores](xref:mvc/controllers/actions) y [vistas](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="509d2-391">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="509d2-392">La funcionalidad siguiente requiere la migración del proyecto de ejemplo ASP.NET MVC al proyecto ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="509d2-392">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="509d2-393">contenido del lado cliente (CSS, fuentes y scripts)</span><span class="sxs-lookup"><span data-stu-id="509d2-393">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="509d2-394">controllers</span><span class="sxs-lookup"><span data-stu-id="509d2-394">controllers</span></span>

* <span data-ttu-id="509d2-395">views</span><span class="sxs-lookup"><span data-stu-id="509d2-395">views</span></span>

* <span data-ttu-id="509d2-396">modelos</span><span class="sxs-lookup"><span data-stu-id="509d2-396">models</span></span>

* <span data-ttu-id="509d2-397">Unión</span><span class="sxs-lookup"><span data-stu-id="509d2-397">bundling</span></span>

* <span data-ttu-id="509d2-398">filters</span><span class="sxs-lookup"><span data-stu-id="509d2-398">filters</span></span>

* <span data-ttu-id="509d2-399">Iniciar y cerrar sesión Identity (esto se hace en el siguiente tutorial).</span><span class="sxs-lookup"><span data-stu-id="509d2-399">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="509d2-400">Controladores y vistas</span><span class="sxs-lookup"><span data-stu-id="509d2-400">Controllers and views</span></span>

* <span data-ttu-id="509d2-401">Copie cada uno de los métodos de MVC de ASP.NET `HomeController` al nuevo `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="509d2-401">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="509d2-402">En ASP.NET MVC, el tipo de valor devuelto del método de acción del controlador de la plantilla integrada es [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx). en ASP.NET Core MVC, los métodos de acción devuelven en `IActionResult` su lugar.</span><span class="sxs-lookup"><span data-stu-id="509d2-402">In ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="509d2-403">`ActionResult`implementa `IActionResult` , por lo que no es necesario cambiar el tipo de valor devuelto de los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="509d2-403">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="509d2-404">Copie los archivos de vista *About. cshtml*, *Contact. cshtml*e *Index. cshtml* Razor del proyecto ASP.NET MVC en el proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="509d2-404">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="509d2-405">Prueba de cada método</span><span class="sxs-lookup"><span data-stu-id="509d2-405">Test each method</span></span>

<span data-ttu-id="509d2-406">Todavía no se han migrado el archivo de diseño y los estilos, por lo que las vistas representadas solo contienen el contenido de los archivos de vista.</span><span class="sxs-lookup"><span data-stu-id="509d2-406">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="509d2-407">Los vínculos generados por el archivo de diseño para las `About` `Contact` vistas y no estarán disponibles todavía.</span><span class="sxs-lookup"><span data-stu-id="509d2-407">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

* <span data-ttu-id="509d2-408">Invoque las vistas representadas desde el explorador en la aplicación ASP.NET Core en ejecución reemplazando el número de puerto actual por el número de Puerto usado en el proyecto de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="509d2-408">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="509d2-409">Por ejemplo: `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="509d2-409">For example: `https://localhost:44375/home/about`.</span></span>

![Página de contacto](mvc/_static/contact-page.png)

<span data-ttu-id="509d2-411">Tenga en cuenta la falta de estilo y elementos de menú.</span><span class="sxs-lookup"><span data-stu-id="509d2-411">Note the lack of styling and menu items.</span></span> <span data-ttu-id="509d2-412">El estilo se corregirá en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="509d2-412">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="509d2-413">Contenido estático</span><span class="sxs-lookup"><span data-stu-id="509d2-413">Static content</span></span>

<span data-ttu-id="509d2-414">En ASP.NET MVC 5 y versiones anteriores, el contenido estático se hospedaba en la raíz del proyecto web y se combinaba con los archivos del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="509d2-414">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="509d2-415">En ASP.NET Core, el contenido estático se hospeda en el directorio *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="509d2-415">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="509d2-416">Copie el contenido estático de la aplicación ASP.NET MVC en el directorio *wwwroot* del proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="509d2-416">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="509d2-417">En esta conversión de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="509d2-417">In this sample conversion:</span></span>

* <span data-ttu-id="509d2-418">Copie el archivo *favicon. ico* del proyecto ASP.NET MVC en el directorio *wwwroot* del proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="509d2-418">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="509d2-419">El proyecto ASP.NET MVC usa [bootstrap](https://getbootstrap.com/) para su estilo y almacena los archivos de arranque en los directorios de *contenido* y *scripts* .</span><span class="sxs-lookup"><span data-stu-id="509d2-419">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="509d2-420">La plantilla, que generó el proyecto ASP.NET MVC, hace referencia a bootstrap en el archivo de diseño (*views/Shared/_Layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="509d2-420">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="509d2-421">Los archivos *bootstrap.js* y *bootstrap. CSS* se pueden copiar del proyecto ASP.NET MVC al directorio *wwwroot* del nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="509d2-421">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="509d2-422">En su lugar, este documento agrega compatibilidad con bootstrap (y otras bibliotecas del lado cliente) mediante redes CDN, en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="509d2-422">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="509d2-423">Migrar el archivo de diseño</span><span class="sxs-lookup"><span data-stu-id="509d2-423">Migrate the layout file</span></span>

* <span data-ttu-id="509d2-424">Copie el archivo *_ViewStart. cshtml* del directorio *views* del proyecto ASP.NET MVC en el directorio *views* del proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="509d2-424">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="509d2-425">El archivo *_ViewStart. cshtml* no ha cambiado en ASP.net Core MVC.</span><span class="sxs-lookup"><span data-stu-id="509d2-425">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="509d2-426">Cree un directorio *views/Shared* .</span><span class="sxs-lookup"><span data-stu-id="509d2-426">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="509d2-427">*Opcional:* Copie *_ViewImports. cshtml* del directorio *views* del proyecto de *FullAspNetCore* MVC en el directorio *views* del proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="509d2-427">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="509d2-428">Quite cualquier declaración de espacio de nombres del archivo *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="509d2-428">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="509d2-429">El archivo *_ViewImports. cshtml* proporciona espacios de nombres para todos los archivos de vista y ofrece [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="509d2-429">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="509d2-430">Las aplicaciones auxiliares de etiquetas se usan en el nuevo archivo de diseño.</span><span class="sxs-lookup"><span data-stu-id="509d2-430">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="509d2-431">El archivo *_ViewImports. cshtml* es nuevo para ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="509d2-431">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="509d2-432">Copie el archivo *_Layout. cshtml* del directorio de *vistas/compartidos* del proyecto de ASP.NET MVC en el directorio de *vistas/compartidos* del proyecto de ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="509d2-432">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="509d2-433">Abra *_Layout archivo. cshtml* y realice los cambios siguientes (el código completo se muestra a continuación):</span><span class="sxs-lookup"><span data-stu-id="509d2-433">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="509d2-434">Reemplace `@Styles.Render("~/Content/css")` por un `<link>` elemento para cargar *bootstrap. CSS* (consulte a continuación).</span><span class="sxs-lookup"><span data-stu-id="509d2-434">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="509d2-435">Quite `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="509d2-435">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="509d2-436">Comente la `@Html.Partial("_LoginPartial")` línea (rodee la línea con `@*...*@` ).</span><span class="sxs-lookup"><span data-stu-id="509d2-436">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="509d2-437">Para obtener más información, vea [migrar la autenticación y Identity ASP.net Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="509d2-437">For more information, see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="509d2-438">Reemplazar `@Scripts.Render("~/bundles/jquery")` por un `<script>` elemento (vea más abajo).</span><span class="sxs-lookup"><span data-stu-id="509d2-438">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="509d2-439">Reemplazar `@Scripts.Render("~/bundles/bootstrap")` por un `<script>` elemento (vea más abajo).</span><span class="sxs-lookup"><span data-stu-id="509d2-439">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="509d2-440">El marcado de reemplazo para la inclusión de CSS de bootstrap:</span><span class="sxs-lookup"><span data-stu-id="509d2-440">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="509d2-441">El marcado de reemplazo para jQuery y bootstrap JavaScript incluirá:</span><span class="sxs-lookup"><span data-stu-id="509d2-441">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="509d2-442">El archivo *_Layout. cshtml* actualizado se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="509d2-442">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="509d2-443">Vea el sitio en el explorador.</span><span class="sxs-lookup"><span data-stu-id="509d2-443">View the site in the browser.</span></span> <span data-ttu-id="509d2-444">Ahora debería cargarse correctamente, con los estilos esperados en su lugar.</span><span class="sxs-lookup"><span data-stu-id="509d2-444">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="509d2-445">*Opcional:* Pruebe a usar el nuevo archivo de diseño.</span><span class="sxs-lookup"><span data-stu-id="509d2-445">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="509d2-446">Copie el archivo de diseño del proyecto *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="509d2-446">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="509d2-447">El nuevo archivo de diseño utiliza [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) y otras mejoras.</span><span class="sxs-lookup"><span data-stu-id="509d2-447">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="509d2-448">Configuración de la unión y la minimización</span><span class="sxs-lookup"><span data-stu-id="509d2-448">Configure bundling and minification</span></span>

<span data-ttu-id="509d2-449">Para obtener información acerca de cómo configurar la agrupación y la minificación, consulte [agrupación y minificación](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="509d2-449">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="509d2-450">Solucionar errores HTTP 500</span><span class="sxs-lookup"><span data-stu-id="509d2-450">Solve HTTP 500 errors</span></span>

<span data-ttu-id="509d2-451">Hay muchos problemas que pueden producir mensajes de error HTTP 500 que no contienen información sobre el origen del problema.</span><span class="sxs-lookup"><span data-stu-id="509d2-451">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="509d2-452">Por ejemplo, si el archivo *views/_ViewImports. cshtml* contiene un espacio de nombres que no existe en el proyecto, se genera un error http 500.</span><span class="sxs-lookup"><span data-stu-id="509d2-452">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="509d2-453">De forma predeterminada, en ASP.NET Core aplicaciones, la `UseDeveloperExceptionPage` extensión se agrega a `IApplicationBuilder` y se ejecuta cuando la configuración es *desarrollo*.</span><span class="sxs-lookup"><span data-stu-id="509d2-453">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="509d2-454">Vea un ejemplo en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="509d2-454">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="509d2-455">ASP.NET Core convierte las excepciones no controladas en respuestas de error HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="509d2-455">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="509d2-456">Normalmente, los detalles del error no se incluyen en estas respuestas para evitar la divulgación de información potencialmente confidencial sobre el servidor.</span><span class="sxs-lookup"><span data-stu-id="509d2-456">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="509d2-457">Para obtener más información, consulte la [Página de excepciones para desarrolladores](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="509d2-457">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="509d2-458">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="509d2-458">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
