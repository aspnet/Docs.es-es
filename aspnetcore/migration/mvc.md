---
title: Aprenda a migrar de ASP.NET MVC a ASP.NET Core MVC
author: wadepickett
description: Obtenga información sobre cómo iniciar la migración de un proyecto de ASP.NET MVC a ASP.NET Core MVC.
ms.author: wpickett
ms.date: 06/18/2020
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
uid: migration/mvc
ms.openlocfilehash: 51228e59284b5edf0554e9929b16deafe08ea31e
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326623"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="c897d-103">Migración de ASP.NET MVC a ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="c897d-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c897d-104">En este artículo se muestra cómo iniciar la migración de un proyecto de ASP.NET MVC a [ASP.net Core MVC](xref:mvc/overview).</span><span class="sxs-lookup"><span data-stu-id="c897d-104">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview).</span></span> <span data-ttu-id="c897d-105">En el proceso, se resaltan los cambios relacionados de ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="c897d-105">In the process, it highlights related changes from ASP.NET MVC.</span></span>

<span data-ttu-id="c897d-106">La migración de ASP.NET MVC es un proceso de varios pasos.</span><span class="sxs-lookup"><span data-stu-id="c897d-106">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="c897d-107">En este artículo se describe:</span><span class="sxs-lookup"><span data-stu-id="c897d-107">This article covers:</span></span>

* <span data-ttu-id="c897d-108">Instalación inicial.</span><span class="sxs-lookup"><span data-stu-id="c897d-108">Initial setup.</span></span>
* <span data-ttu-id="c897d-109">Controladores y vistas básicos.</span><span class="sxs-lookup"><span data-stu-id="c897d-109">Basic controllers and views.</span></span>
* <span data-ttu-id="c897d-110">Contenido estático.</span><span class="sxs-lookup"><span data-stu-id="c897d-110">Static content.</span></span>
* <span data-ttu-id="c897d-111">Dependencias del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="c897d-111">Client-side dependencies.</span></span>

<span data-ttu-id="c897d-112">Para migrar la configuración y el Identity código, vea [migrar la configuración a ASP.net Core](xref:migration/configuration) y [migrar la autenticación y Identity a ASP.net Core](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="c897d-112">For migrating configuration and Identity code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c897d-113">Prerrequisitos</span><span class="sxs-lookup"><span data-stu-id="c897d-113">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="c897d-114">Crear el proyecto de MVC ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="c897d-114">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="c897d-115">Cree un proyecto de ASP.NET MVC de ejemplo en Visual Studio para migrar:</span><span class="sxs-lookup"><span data-stu-id="c897d-115">Create an example ASP.NET MVC project in Visual Studio to migrate:</span></span>

1. <span data-ttu-id="c897d-116">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="c897d-116">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="c897d-117">Seleccione **aplicación Web de ASP.net (.NET Framework)** y, a continuación, seleccione **siguiente**.</span><span class="sxs-lookup"><span data-stu-id="c897d-117">Select **ASP.NET Web Application (.NET Framework)** and then select **Next**.</span></span>
1. <span data-ttu-id="c897d-118">Asigne al proyecto el nombre *WebApp1* para que el espacio de nombres coincida con el proyecto de ASP.net Core creado en el paso siguiente.</span><span class="sxs-lookup"><span data-stu-id="c897d-118">Name the project *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span> <span data-ttu-id="c897d-119">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="c897d-119">Select **Create**.</span></span>
1. <span data-ttu-id="c897d-120">Seleccione **MVC**y, a continuación, seleccione **crear**.</span><span class="sxs-lookup"><span data-stu-id="c897d-120">Select **MVC**, and then select **Create**.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="c897d-121">Crear el proyecto de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c897d-121">Create the ASP.NET Core project</span></span>

<span data-ttu-id="c897d-122">Cree una nueva solución con un nuevo proyecto de ASP.NET Core al que migrar:</span><span class="sxs-lookup"><span data-stu-id="c897d-122">Create a new solution with a new ASP.NET Core project to migrate to:</span></span>

1. <span data-ttu-id="c897d-123">Inicie una segunda instancia de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c897d-123">Launch a second instance of Visual Studio.</span></span>
1. <span data-ttu-id="c897d-124">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="c897d-124">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="c897d-125">Seleccione **Aplicación web ASP.NET Core** y, después, **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="c897d-125">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>
1. <span data-ttu-id="c897d-126">En el cuadro de diálogo **configurar el nuevo proyecto** , asigne al proyecto el nombre *WebApp1*.</span><span class="sxs-lookup"><span data-stu-id="c897d-126">In the **Configure your new project** dialog, Name the project *WebApp1*.</span></span>
1. <span data-ttu-id="c897d-127">Establezca la ubicación en un directorio diferente al proyecto anterior para usar el mismo nombre de proyecto.</span><span class="sxs-lookup"><span data-stu-id="c897d-127">Set the location to a different directory than the previous project to use the same project name.</span></span> <span data-ttu-id="c897d-128">El uso del mismo espacio de nombres facilita la copia de código entre los dos proyectos.</span><span class="sxs-lookup"><span data-stu-id="c897d-128">Using the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="c897d-129">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="c897d-129">Select **Create**.</span></span>
1. <span data-ttu-id="c897d-130">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 3.1** estén seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="c897d-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="c897d-131">Seleccione la plantilla de proyecto **aplicación web (controlador de vista de modelos)** y seleccione **crear**.</span><span class="sxs-lookup"><span data-stu-id="c897d-131">Select the **Web Application (Model-View-Controller)** project template, and select **Create**.</span></span>

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a><span data-ttu-id="c897d-132">Configuración del sitio de ASP.NET Core para usar MVC</span><span class="sxs-lookup"><span data-stu-id="c897d-132">Configure the ASP.NET Core site to use MVC</span></span>

<span data-ttu-id="c897d-133">En ASP.NET Core proyectos de 3,0 y versiones posteriores, .NET Framework ya no es una plataforma de destino admitida.</span><span class="sxs-lookup"><span data-stu-id="c897d-133">In ASP.NET Core 3.0 and later projects, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="c897d-134">El proyecto debe tener como destino .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c897d-134">Your project must target .NET Core.</span></span> <span data-ttu-id="c897d-135">La ASP.NET Core marco de trabajo compartido, que incluye MVC, forma parte de la instalación del entorno de ejecución de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c897d-135">The ASP.NET Core shared framework, which includes MVC, is part of the .NET Core runtime installation.</span></span> <span data-ttu-id="c897d-136">Se hace referencia al marco compartido automáticamente al usar el SDK `Microsoft.NET.Sdk.Web` en el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="c897d-136">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="c897d-137">Para obtener más información, vea [referencia de Framework](xref:migration/22-to-30#framework-reference).</span><span class="sxs-lookup"><span data-stu-id="c897d-137">For more information, see [Framework reference](xref:migration/22-to-30#framework-reference).</span></span>

<span data-ttu-id="c897d-138">En ASP.NET Core, la `Startup` clase:</span><span class="sxs-lookup"><span data-stu-id="c897d-138">In ASP.NET Core, the `Startup` class:</span></span>

* <span data-ttu-id="c897d-139">Reemplaza a *global. asax*.</span><span class="sxs-lookup"><span data-stu-id="c897d-139">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="c897d-140">Controla todas las tareas de inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c897d-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="c897d-141">Para más información, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="c897d-141">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="c897d-142">En el proyecto de ASP.NET Core, abra el archivo *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="c897d-142">In the ASP.NET Core project, open the *Startup.cs* file:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

<span data-ttu-id="c897d-143">ASP.NET Core aplicaciones deben participar en las características de .NET Framework con middleware.</span><span class="sxs-lookup"><span data-stu-id="c897d-143">ASP.NET Core apps must opt in to framework features with middleware.</span></span> <span data-ttu-id="c897d-144">El código generado anteriormente por la plantilla agrega los siguientes servicios y middleware:</span><span class="sxs-lookup"><span data-stu-id="c897d-144">The previous template-generated code adds the following services and middleware:</span></span>

* <span data-ttu-id="c897d-145">El <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> método de extensión registra la compatibilidad del servicio MVC con los controladores, las características relacionadas con la API y las vistas.</span><span class="sxs-lookup"><span data-stu-id="c897d-145">The <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> extension method registers MVC service support for controllers, API-related features, and views.</span></span> <span data-ttu-id="c897d-146">Para obtener más información sobre las opciones de registro del servicio MVC, consulte [registro del servicio MVC](xref:migration/22-to-30#mvc-service-registration)</span><span class="sxs-lookup"><span data-stu-id="c897d-146">For more information on MVC service registration options, see [MVC service registration](xref:migration/22-to-30#mvc-service-registration)</span></span>
* <span data-ttu-id="c897d-147">El <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> método de extensión agrega el controlador de archivos estáticos `Microsoft.AspNetCore.StaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="c897d-147">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> extension method adds the static file handler `Microsoft.AspNetCore.StaticFiles`.</span></span> <span data-ttu-id="c897d-148">`UseStaticFiles`Se debe llamar antes al método de extensión `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="c897d-148">The `UseStaticFiles` extension method must be called before `UseRouting`.</span></span> <span data-ttu-id="c897d-149">Para más información, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="c897d-149">For more information, see <xref:fundamentals/static-files>.</span></span>
* <span data-ttu-id="c897d-150">El <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> método de extensión agrega enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="c897d-150">The <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> extension method adds routing.</span></span> <span data-ttu-id="c897d-151">Para más información, consulte <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="c897d-151">For more information, see <xref:fundamentals/routing>.</span></span>

<span data-ttu-id="c897d-152">Esta configuración existente incluye lo que se necesita para migrar el proyecto de ejemplo ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="c897d-152">This existing configuration includes what is needed to migrate the example ASP.NET MVC project.</span></span> <span data-ttu-id="c897d-153">Para obtener más información acerca de ASP.NET Core opciones de middleware, vea <xref:fundamentals/startup> .</span><span class="sxs-lookup"><span data-stu-id="c897d-153">For more information on ASP.NET Core middleware options, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-controllers-and-views"></a><span data-ttu-id="c897d-154">Migrar controladores y vistas</span><span class="sxs-lookup"><span data-stu-id="c897d-154">Migrate controllers and views</span></span>

<span data-ttu-id="c897d-155">En el proyecto de ASP.NET Core, se agregaría una nueva clase de controlador vacía y una clase de vista para servir como marcadores de posición usando los mismos nombres que las clases de controlador y de vista de cualquier proyecto de ASP.NET MVC del que migrar.</span><span class="sxs-lookup"><span data-stu-id="c897d-155">In the ASP.NET Core project, a new empty controller class and view class would be added to serve as placeholders using the same names as the controller and view classes in any ASP.NET MVC project to migrate from.</span></span>

<span data-ttu-id="c897d-156">El proyecto de ASP.NET Core *WebApp1* ya incluye un controlador de ejemplo mínimo y una vista con el mismo nombre que el proyecto de MVC de ASP.net.</span><span class="sxs-lookup"><span data-stu-id="c897d-156">The ASP.NET Core *WebApp1* project already includes a minimal example controller and view by the same name as the ASP.NET MVC project.</span></span> <span data-ttu-id="c897d-157">Por lo tanto, estos actuarán como marcadores de posición para el controlador ASP.NET MVC y las vistas que se van a migrar desde el proyecto de ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="c897d-157">So those will serve as placeholders for the ASP.NET MVC controller and views to be migrated from the ASP.NET MVC *WebApp1* project.</span></span>

1. <span data-ttu-id="c897d-158">Copie los métodos de MVC de ASP.NET `HomeController` para reemplazar los nuevos métodos de ASP.net Core `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="c897d-158">Copy the methods from the ASP.NET MVC `HomeController` to replace the new ASP.NET Core `HomeController` methods.</span></span> <span data-ttu-id="c897d-159">No es necesario cambiar el tipo de valor devuelto de los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="c897d-159">There's no need to change the return type of the action methods.</span></span> <span data-ttu-id="c897d-160">El tipo de valor devuelto del método de acción del controlador de la plantilla integrada de ASP.NET MVC es <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; en ASP.net Core MVC, los métodos de acción devuelven en `IActionResult` su lugar.</span><span class="sxs-lookup"><span data-stu-id="c897d-160">The ASP.NET MVC built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="c897d-161">`ActionResult` implementa `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="c897d-161">`ActionResult` implements `IActionResult`.</span></span>
1. <span data-ttu-id="c897d-162">En el proyecto de ASP.net Core, haga clic con el botón derecho en el directorio *views/Home* y seleccione **Agregar** > **elemento existente**.</span><span class="sxs-lookup"><span data-stu-id="c897d-162">In the ASP.NET Core project, right-click the *Views/Home* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="c897d-163">En el cuadro de diálogo **Add Existing Item (Agregar elemento existente** ), desplácese hasta el directorio de *inicios/* del proyecto de ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="c897d-163">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Home* directory.</span></span>
1. <span data-ttu-id="c897d-164">Seleccione los archivos *de vista about. cshtml*, *Contact. cshtml*e *index. cshtml* y, Razor a continuación, seleccione **Agregar**y reemplace los archivos existentes.</span><span class="sxs-lookup"><span data-stu-id="c897d-164">Select the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files, then select **Add**, replacing the existing files.</span></span>

<span data-ttu-id="c897d-165">Para obtener más información, vea <xref:mvc/controllers/actions> y <xref:mvc/views/overview>.</span><span class="sxs-lookup"><span data-stu-id="c897d-165">For more information, see <xref:mvc/controllers/actions> and <xref:mvc/views/overview>.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="c897d-166">Prueba de cada método</span><span class="sxs-lookup"><span data-stu-id="c897d-166">Test each method</span></span>

<span data-ttu-id="c897d-167">Los puntos de conexión de cada controlador se pueden probar, sin embargo, el diseño y los estilos se describen más adelante en el documento.</span><span class="sxs-lookup"><span data-stu-id="c897d-167">Each controller endpoint can be tested, however, layout and styles are covered later in the document.</span></span>

1. <span data-ttu-id="c897d-168">Ejecute la aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c897d-168">Run the ASP.NET Core app.</span></span>
1. <span data-ttu-id="c897d-169">Invoque las vistas representadas desde el explorador en la aplicación ASP.NET Core en ejecución reemplazando el número de puerto actual por el número de Puerto usado en el proyecto de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c897d-169">Invoke the rendered views from the browser on the running ASP.NET Core app by replacing the current port number with the port number used in the ASP.NET Core project.</span></span> <span data-ttu-id="c897d-170">Por ejemplo, `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="c897d-170">For example, `https://localhost:44375/home/about`.</span></span>

## <a name="migrate-static-content"></a><span data-ttu-id="c897d-171">Migrar contenido estático</span><span class="sxs-lookup"><span data-stu-id="c897d-171">Migrate static content</span></span>

<span data-ttu-id="c897d-172">En ASP.NET MVC 5 y versiones anteriores, el contenido estático se hospedaba en el directorio raíz del proyecto web y se combinaba con los archivos del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="c897d-172">In ASP.NET MVC 5 and earlier, static content was hosted from the web project's root directory and was intermixed with server-side files.</span></span> <span data-ttu-id="c897d-173">En ASP.NET Core, los archivos estáticos se almacenan en el directorio [raíz Web](xref:fundamentals/index#web-root) del proyecto.</span><span class="sxs-lookup"><span data-stu-id="c897d-173">In ASP.NET Core, static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="c897d-174">El directorio predeterminado es *{Content root}/wwwroot*, pero se puede cambiar.</span><span class="sxs-lookup"><span data-stu-id="c897d-174">The default directory is *{content root}/wwwroot*, but it can be changed.</span></span> <span data-ttu-id="c897d-175">Para obtener más información, vea [Archivos estáticos en ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span><span class="sxs-lookup"><span data-stu-id="c897d-175">For more information, see [Static files in ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span></span>

<span data-ttu-id="c897d-176">Copie el contenido estático del proyecto de ASP.NET MVC *WebApp1* en el directorio *wwwroot* del proyecto ASP.net Core *WebApp1* :</span><span class="sxs-lookup"><span data-stu-id="c897d-176">Copy the static content from the ASP.NET MVC *WebApp1* project to the *wwwroot* directory in the ASP.NET Core *WebApp1* project:</span></span>

1. <span data-ttu-id="c897d-177">En el proyecto de ASP.net Core, haga clic con el botón secundario en el directorio *wwwroot* y seleccione **Agregar** > **elemento existente**.</span><span class="sxs-lookup"><span data-stu-id="c897d-177">In the ASP.NET Core project, right-click the *wwwroot* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="c897d-178">En el cuadro de diálogo **Agregar elemento existente** , vaya al proyecto de ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="c897d-178">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project.</span></span>
1. <span data-ttu-id="c897d-179">Seleccione el archivo *favicon. ico* y, a continuación, seleccione **Agregar**, reemplazando el archivo existente.</span><span class="sxs-lookup"><span data-stu-id="c897d-179">Select the *favicon.ico* file, then select **Add**, replacing the existing file.</span></span>

## <a name="migrate-the-layout-files"></a><span data-ttu-id="c897d-180">Migrar los archivos de diseño</span><span class="sxs-lookup"><span data-stu-id="c897d-180">Migrate the layout files</span></span>

<span data-ttu-id="c897d-181">Copie los archivos de diseño del proyecto de ASP.NET MVC en el proyecto ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="c897d-181">Copy the ASP.NET MVC project layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="c897d-182">En el proyecto de ASP.net Core, haga clic con el botón derecho en el directorio *views* y seleccione **Agregar** > **elemento existente**.</span><span class="sxs-lookup"><span data-stu-id="c897d-182">In the ASP.NET Core project, right-click the *Views* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="c897d-183">En el cuadro de diálogo **Agregar elemento existente** , vaya al directorio de *vistas* del proyecto de ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="c897d-183">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views* directory.</span></span>
1. <span data-ttu-id="c897d-184">Seleccione el archivo *_ViewStart. cshtml* y, después, seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="c897d-184">Select the *_ViewStart.cshtml* file then select **Add**.</span></span>

<span data-ttu-id="c897d-185">Copie los archivos de diseño compartidos del proyecto de ASP.NET MVC en el proyecto ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="c897d-185">Copy the ASP.NET MVC project shared layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="c897d-186">En el proyecto de ASP.net Core, haga clic con el botón derecho en el directorio *views/Shared* y seleccione **Agregar** > **elemento existente**.</span><span class="sxs-lookup"><span data-stu-id="c897d-186">In the ASP.NET Core project, right-click the *Views/Shared* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="c897d-187">En el cuadro de diálogo **Agregar elemento existente** , desplácese hasta el directorio *compartido o vistas* del proyecto de ASP.NET MVC *WebApp1* .</span><span class="sxs-lookup"><span data-stu-id="c897d-187">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Shared* directory.</span></span>
1. <span data-ttu-id="c897d-188">Seleccione el archivo *_Layout. cshtml* , seleccione **Agregar**y reemplace el archivo existente.</span><span class="sxs-lookup"><span data-stu-id="c897d-188">Select the *_Layout.cshtml* file, then select **Add**, replacing the existing file.</span></span>

<span data-ttu-id="c897d-189">En el proyecto de ASP.NET Core, abra el archivo *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="c897d-189">In the ASP.NET Core project, open the *_Layout.cshtml* file.</span></span> <span data-ttu-id="c897d-190">Realice los siguientes cambios para que coincidan con el código completado que se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="c897d-190">Make the following changes to match the completed code shown below:</span></span>

<span data-ttu-id="c897d-191">Actualice la inclusión de CSS de bootstrap para que coincida con el código completado a continuación:</span><span class="sxs-lookup"><span data-stu-id="c897d-191">Update the Bootstrap CSS inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="c897d-192">Reemplace `@Styles.Render("~/Content/css")` por un `<link>` elemento para cargar *bootstrap. CSS* (consulte a continuación).</span><span class="sxs-lookup"><span data-stu-id="c897d-192">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>
1. <span data-ttu-id="c897d-193">Quite `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="c897d-193">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

<span data-ttu-id="c897d-194">El marcado de reemplazo completado para la inclusión de CSS de bootstrap:</span><span class="sxs-lookup"><span data-stu-id="c897d-194">The completed replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="c897d-195">Actualice la inclusión de jQuery y bootstrap de JavaScript para que coincida con el código completado a continuación:</span><span class="sxs-lookup"><span data-stu-id="c897d-195">Update the jQuery and Bootstrap JavaScript inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="c897d-196">Reemplazar `@Scripts.Render("~/bundles/jquery")` por un `<script>` elemento (vea más abajo).</span><span class="sxs-lookup"><span data-stu-id="c897d-196">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>
1. <span data-ttu-id="c897d-197">Reemplazar `@Scripts.Render("~/bundles/bootstrap")` por un `<script>` elemento (vea más abajo).</span><span class="sxs-lookup"><span data-stu-id="c897d-197">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="c897d-198">El marcado de reemplazo completado para jQuery y bootstrap script de inclusión:</span><span class="sxs-lookup"><span data-stu-id="c897d-198">The completed replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="c897d-199">El archivo *_Layout. cshtml* actualizado se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="c897d-199">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

<span data-ttu-id="c897d-200">Vea el sitio en el explorador.</span><span class="sxs-lookup"><span data-stu-id="c897d-200">View the site in the browser.</span></span> <span data-ttu-id="c897d-201">Debe representarse con los estilos esperados en su lugar.</span><span class="sxs-lookup"><span data-stu-id="c897d-201">It should render with the expected styles in place.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="c897d-202">Configuración de la unión y la minimización</span><span class="sxs-lookup"><span data-stu-id="c897d-202">Configure bundling and minification</span></span>

<span data-ttu-id="c897d-203">ASP.NET Core es compatible con varias soluciones minificación y de empaquetado de código abierto, como [Weboptimizer](https://github.com/ligershark/WebOptimizer) y otras bibliotecas similares.</span><span class="sxs-lookup"><span data-stu-id="c897d-203">ASP.NET Core is compatible with several open-source bundling and minification solutions such as [WebOptimizer](https://github.com/ligershark/WebOptimizer) and other similar libraries.</span></span> <span data-ttu-id="c897d-204">ASP.NET Core no proporciona una solución minificación y de Unión nativa.</span><span class="sxs-lookup"><span data-stu-id="c897d-204">ASP.NET Core doesn't provide a native bundling and minification solution.</span></span> <span data-ttu-id="c897d-205">Para obtener información sobre la configuración de la agrupación y la minificación, consulte [agrupación y minificación](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="c897d-205">For information on configuring bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="c897d-206">Solucionar errores HTTP 500</span><span class="sxs-lookup"><span data-stu-id="c897d-206">Solve HTTP 500 errors</span></span>

<span data-ttu-id="c897d-207">Hay muchos problemas que pueden provocar un mensaje de error HTTP 500 que no contiene información sobre el origen del problema.</span><span class="sxs-lookup"><span data-stu-id="c897d-207">There are many problems that can cause an HTTP 500 error message that contains no information on the source of the problem.</span></span> <span data-ttu-id="c897d-208">Por ejemplo, si el archivo *views/_ViewImports. cshtml* contiene un espacio de nombres que no existe en el proyecto, se genera un error http 500.</span><span class="sxs-lookup"><span data-stu-id="c897d-208">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, an HTTP 500 error is generated.</span></span> <span data-ttu-id="c897d-209">De forma predeterminada, en ASP.NET Core aplicaciones, la `UseDeveloperExceptionPage` extensión se agrega a `IApplicationBuilder` y se ejecuta cuando el entorno se está *desarrollando*.</span><span class="sxs-lookup"><span data-stu-id="c897d-209">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the environment is *Development*.</span></span> <span data-ttu-id="c897d-210">Esto se detalla en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="c897d-210">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

<span data-ttu-id="c897d-211">ASP.NET Core convierte las excepciones no controladas en respuestas de error HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="c897d-211">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="c897d-212">Normalmente, los detalles del error no se incluyen en estas respuestas para evitar la divulgación de información potencialmente confidencial sobre el servidor.</span><span class="sxs-lookup"><span data-stu-id="c897d-212">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="c897d-213">Para obtener más información, consulte la [Página de excepciones para desarrolladores](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="c897d-213">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="next-steps"></a><span data-ttu-id="c897d-214">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="c897d-214">Next steps</span></span>

* <xref:migration/identity>

## <a name="additional-resources"></a><span data-ttu-id="c897d-215">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c897d-215">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="c897d-216">En este artículo se muestra cómo iniciar la migración de un proyecto de ASP.NET MVC a [ASP.net Core mvc](xref:mvc/overview) 2,2.</span><span class="sxs-lookup"><span data-stu-id="c897d-216">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.2.</span></span> <span data-ttu-id="c897d-217">En el proceso, se resaltan muchas de las cosas que han cambiado de ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="c897d-217">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="c897d-218">La migración de ASP.NET MVC es un proceso de varios pasos.</span><span class="sxs-lookup"><span data-stu-id="c897d-218">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="c897d-219">En este artículo se describe:</span><span class="sxs-lookup"><span data-stu-id="c897d-219">This article covers:</span></span>

* <span data-ttu-id="c897d-220">Instalación inicial</span><span class="sxs-lookup"><span data-stu-id="c897d-220">Initial setup</span></span>
* <span data-ttu-id="c897d-221">Controladores y vistas básicos</span><span class="sxs-lookup"><span data-stu-id="c897d-221">Basic controllers and views</span></span>
* <span data-ttu-id="c897d-222">Contenido estático</span><span class="sxs-lookup"><span data-stu-id="c897d-222">Static content</span></span>
* <span data-ttu-id="c897d-223">Dependencias del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="c897d-223">Client-side dependencies.</span></span>

<span data-ttu-id="c897d-224">Para migrar la configuración y el Identity código, vea <xref:migration/configuration> y <xref:migration/identity> .</span><span class="sxs-lookup"><span data-stu-id="c897d-224">For migrating configuration and Identity code, see <xref:migration/configuration> and <xref:migration/identity>.</span></span>

> [!NOTE]
> <span data-ttu-id="c897d-225">Es posible que los números de versión de los ejemplos no estén actualizados, actualice los proyectos en consecuencia.</span><span class="sxs-lookup"><span data-stu-id="c897d-225">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="c897d-226">Crear el proyecto de MVC ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="c897d-226">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="c897d-227">Para demostrar la actualización, comenzaremos por crear una aplicación ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="c897d-227">To demonstrate the upgrade, we'll start by creating an ASP.NET MVC app.</span></span> <span data-ttu-id="c897d-228">Créelo con el nombre *WebApp1* para que el espacio de nombres coincida con el proyecto de ASP.net Core creado en el paso siguiente.</span><span class="sxs-lookup"><span data-stu-id="c897d-228">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Cuadro de diálogo Nuevo proyecto de Visual Studio](mvc/_static/new-project.png)

![Cuadro de diálogo Nueva aplicación web: plantilla de proyecto de MVC seleccionada en el panel de plantillas de ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="c897d-231">*Opcional:* Cambie el nombre de la solución de *WebApp1* a *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="c897d-231">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="c897d-232">Visual Studio muestra el nuevo nombre de la solución (*Mvc5*), lo que facilita la información de este proyecto desde el proyecto siguiente.</span><span class="sxs-lookup"><span data-stu-id="c897d-232">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="c897d-233">Crear el proyecto de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c897d-233">Create the ASP.NET Core project</span></span>

<span data-ttu-id="c897d-234">Cree una nueva aplicación Web *vacía* de ASP.net Core con el mismo nombre que el proyecto anterior (*WebApp1*) para que coincidan los espacios de nombres de los dos proyectos.</span><span class="sxs-lookup"><span data-stu-id="c897d-234">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="c897d-235">Tener el mismo espacio de nombres facilita la copia de código entre los dos proyectos.</span><span class="sxs-lookup"><span data-stu-id="c897d-235">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="c897d-236">Cree este proyecto en un directorio diferente al del proyecto anterior para usar el mismo nombre.</span><span class="sxs-lookup"><span data-stu-id="c897d-236">Create this project in a different directory than the previous project to use the same name.</span></span>

![Cuadro de diálogo Nuevo proyecto](mvc/_static/new_core.png)

![Cuadro de diálogo Nueva aplicación Web de ASP.NET: plantilla de proyecto vacía seleccionada en el panel de plantillas de ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="c897d-239">*Opcional:* Cree una nueva aplicación de ASP.NET Core mediante la plantilla de proyecto de *aplicación web* .</span><span class="sxs-lookup"><span data-stu-id="c897d-239">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="c897d-240">Asigne al proyecto el nombre *WebApp1*y seleccione una opción de autenticación de **cuentas de usuario individuales**.</span><span class="sxs-lookup"><span data-stu-id="c897d-240">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="c897d-241">Cambie el nombre de esta aplicación a *FullAspNetCore*.</span><span class="sxs-lookup"><span data-stu-id="c897d-241">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="c897d-242">Al crear este proyecto, se ahorra tiempo en la conversión.</span><span class="sxs-lookup"><span data-stu-id="c897d-242">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="c897d-243">El resultado final se puede ver en el código generado por la plantilla, el código puede copiarse en el proyecto de conversión o compararse con el proyecto generado por una plantilla.</span><span class="sxs-lookup"><span data-stu-id="c897d-243">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="c897d-244">Configurar el sitio para usar MVC</span><span class="sxs-lookup"><span data-stu-id="c897d-244">Configure the site to use MVC</span></span>

* <span data-ttu-id="c897d-245">Cuando el destino es .NET Core, se hace referencia al [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c897d-245">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="c897d-246">Este paquete contiene los paquetes que usan normalmente las aplicaciones MVC.</span><span class="sxs-lookup"><span data-stu-id="c897d-246">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="c897d-247">Si el destino es .NET Framework, las referencias del paquete deben aparecer de forma individual en el archivo del proyecto.</span><span class="sxs-lookup"><span data-stu-id="c897d-247">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="c897d-248">`Microsoft.AspNetCore.Mvc` es el marco de MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c897d-248">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="c897d-249">`Microsoft.AspNetCore.StaticFiles` es el controlador de archivos estático.</span><span class="sxs-lookup"><span data-stu-id="c897d-249">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="c897d-250">ASP.NET Core aplicaciones optan explícitamente a un middleware, como para servir archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="c897d-250">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="c897d-251">Para obtener más información, consulte [Archivos estáticos](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="c897d-251">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="c897d-252">Abra el archivo *Startup.CS* y cambie el código para que coincida con lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c897d-252">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="c897d-253">El <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> método de extensión agrega el controlador de archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="c897d-253">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="c897d-254">Para obtener más información, consulte Inicio y [enrutamiento](xref:fundamentals/routing)de la [aplicación](xref:fundamentals/startup) .</span><span class="sxs-lookup"><span data-stu-id="c897d-254">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="c897d-255">Agregar un controlador y una vista</span><span class="sxs-lookup"><span data-stu-id="c897d-255">Add a controller and view</span></span>

<span data-ttu-id="c897d-256">En esta sección, se agrega un controlador y una vista mínimos para servir como marcadores de posición para el controlador ASP.NET MVC y las vistas migradas en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="c897d-256">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="c897d-257">Agregue un directorio de *Controladores* .</span><span class="sxs-lookup"><span data-stu-id="c897d-257">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="c897d-258">Agregue una **clase de controlador** denominada *HomeController.CS* al directorio *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="c897d-258">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![Cuadro de diálogo Agregar nuevo elemento](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="c897d-260">Agregue un directorio *views* .</span><span class="sxs-lookup"><span data-stu-id="c897d-260">Add a *Views* directory.</span></span>

* <span data-ttu-id="c897d-261">Agregue un directorio de *vistas o inicio* .</span><span class="sxs-lookup"><span data-stu-id="c897d-261">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="c897d-262">Agregue una \*\* Razor vista\*\* denominada *index. cshtml* al directorio *views/Home* .</span><span class="sxs-lookup"><span data-stu-id="c897d-262">Add a **Razor View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![Cuadro de diálogo Agregar nuevo elemento](mvc/_static/view.png)

<span data-ttu-id="c897d-264">La estructura del proyecto se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="c897d-264">The project structure is shown below:</span></span>

![Explorador de soluciones que muestra los archivos y directorios de WebApp1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="c897d-266">Reemplace el contenido del archivo *Views/Home/Index.cshtml* por el marcado siguiente:</span><span class="sxs-lookup"><span data-stu-id="c897d-266">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="c897d-267">Ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c897d-267">Run the app.</span></span>

![Aplicación web abierta en Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="c897d-269">Para obtener más información, vea [Controladores](xref:mvc/controllers/actions) y [vistas](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="c897d-269">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="c897d-270">La funcionalidad siguiente requiere la migración del proyecto de ejemplo ASP.NET MVC al proyecto ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="c897d-270">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="c897d-271">contenido del lado cliente (CSS, fuentes y scripts)</span><span class="sxs-lookup"><span data-stu-id="c897d-271">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="c897d-272">controllers</span><span class="sxs-lookup"><span data-stu-id="c897d-272">controllers</span></span>

* <span data-ttu-id="c897d-273">views</span><span class="sxs-lookup"><span data-stu-id="c897d-273">views</span></span>

* <span data-ttu-id="c897d-274">modelos</span><span class="sxs-lookup"><span data-stu-id="c897d-274">models</span></span>

* <span data-ttu-id="c897d-275">Unión</span><span class="sxs-lookup"><span data-stu-id="c897d-275">bundling</span></span>

* <span data-ttu-id="c897d-276">filters</span><span class="sxs-lookup"><span data-stu-id="c897d-276">filters</span></span>

* <span data-ttu-id="c897d-277">Iniciar y cerrar sesión Identity (esto se hace en el siguiente tutorial).</span><span class="sxs-lookup"><span data-stu-id="c897d-277">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="c897d-278">Controladores y vistas</span><span class="sxs-lookup"><span data-stu-id="c897d-278">Controllers and views</span></span>

* <span data-ttu-id="c897d-279">Copie cada uno de los métodos de MVC de ASP.NET `HomeController` al nuevo `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="c897d-279">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="c897d-280">En ASP.NET MVC, el tipo de valor devuelto del método de acción del controlador de la plantilla integrada es <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; en ASP.net Core MVC, los métodos de acción devuelven en `IActionResult` su lugar.</span><span class="sxs-lookup"><span data-stu-id="c897d-280">In ASP.NET MVC, the built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="c897d-281">`ActionResult` implementa `IActionResult` , por lo que no es necesario cambiar el tipo de valor devuelto de los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="c897d-281">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="c897d-282">Copie los archivos de vista *About. cshtml*, *Contact. cshtml*e *Index. cshtml* Razor del proyecto ASP.NET MVC en el proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="c897d-282">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="c897d-283">Prueba de cada método</span><span class="sxs-lookup"><span data-stu-id="c897d-283">Test each method</span></span>

<span data-ttu-id="c897d-284">Todavía no se han migrado el archivo de diseño y los estilos, por lo que las vistas representadas solo contienen el contenido de los archivos de vista.</span><span class="sxs-lookup"><span data-stu-id="c897d-284">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="c897d-285">Los vínculos generados por el archivo de diseño para las `About` `Contact` vistas y no estarán disponibles todavía.</span><span class="sxs-lookup"><span data-stu-id="c897d-285">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

<span data-ttu-id="c897d-286">Invoque las vistas representadas desde el explorador en la aplicación ASP.NET Core en ejecución reemplazando el número de puerto actual por el número de Puerto usado en el proyecto de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c897d-286">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="c897d-287">Por ejemplo: `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="c897d-287">For example: `https://localhost:44375/home/about`.</span></span>

![Página de contacto](mvc/_static/contact-page.png)

<span data-ttu-id="c897d-289">Tenga en cuenta la falta de estilo y elementos de menú.</span><span class="sxs-lookup"><span data-stu-id="c897d-289">Note the lack of styling and menu items.</span></span> <span data-ttu-id="c897d-290">El estilo se corregirá en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="c897d-290">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="c897d-291">Contenido estático</span><span class="sxs-lookup"><span data-stu-id="c897d-291">Static content</span></span>

<span data-ttu-id="c897d-292">En ASP.NET MVC 5 y versiones anteriores, el contenido estático se hospedaba en la raíz del proyecto web y se combinaba con los archivos del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="c897d-292">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="c897d-293">En ASP.NET Core, el contenido estático se hospeda en el directorio *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="c897d-293">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="c897d-294">Copie el contenido estático de la aplicación ASP.NET MVC en el directorio *wwwroot* del proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="c897d-294">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="c897d-295">En esta conversión de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c897d-295">In this sample conversion:</span></span>

* <span data-ttu-id="c897d-296">Copie el archivo *favicon. ico* del proyecto ASP.NET MVC en el directorio *wwwroot* del proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="c897d-296">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="c897d-297">El proyecto ASP.NET MVC usa [bootstrap](https://getbootstrap.com/) para su estilo y almacena los archivos de arranque en los directorios de *contenido* y *scripts* .</span><span class="sxs-lookup"><span data-stu-id="c897d-297">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="c897d-298">La plantilla, que generó el proyecto ASP.NET MVC, hace referencia a bootstrap en el archivo de diseño (*views/Shared/_Layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="c897d-298">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="c897d-299">Los archivos *bootstrap.js* y *bootstrap. CSS* se pueden copiar del proyecto ASP.NET MVC al directorio *wwwroot* del nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="c897d-299">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="c897d-300">En su lugar, este documento agrega compatibilidad con bootstrap (y otras bibliotecas del lado cliente) mediante redes CDN, en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="c897d-300">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="c897d-301">Migrar el archivo de diseño</span><span class="sxs-lookup"><span data-stu-id="c897d-301">Migrate the layout file</span></span>

* <span data-ttu-id="c897d-302">Copie el archivo *_ViewStart. cshtml* del directorio *views* del proyecto ASP.NET MVC en el directorio *views* del proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="c897d-302">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="c897d-303">El archivo *_ViewStart. cshtml* no ha cambiado en ASP.net Core MVC.</span><span class="sxs-lookup"><span data-stu-id="c897d-303">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="c897d-304">Cree un directorio *views/Shared* .</span><span class="sxs-lookup"><span data-stu-id="c897d-304">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="c897d-305">*Opcional:* Copie *_ViewImports. cshtml* del directorio *views* del proyecto de *FullAspNetCore* MVC en el directorio *views* del proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="c897d-305">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="c897d-306">Quite cualquier declaración de espacio de nombres del archivo *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="c897d-306">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="c897d-307">El archivo *_ViewImports. cshtml* proporciona espacios de nombres para todos los archivos de vista y ofrece [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="c897d-307">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="c897d-308">Las aplicaciones auxiliares de etiquetas se usan en el nuevo archivo de diseño.</span><span class="sxs-lookup"><span data-stu-id="c897d-308">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="c897d-309">El archivo *_ViewImports. cshtml* es nuevo para ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="c897d-309">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="c897d-310">Copie el archivo *_Layout. cshtml* del directorio de *vistas/compartidos* del proyecto de ASP.NET MVC en el directorio de *vistas/compartidos* del proyecto de ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="c897d-310">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="c897d-311">Abra *_Layout archivo. cshtml* y realice los cambios siguientes (el código completo se muestra a continuación):</span><span class="sxs-lookup"><span data-stu-id="c897d-311">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="c897d-312">Reemplace `@Styles.Render("~/Content/css")` por un `<link>` elemento para cargar *bootstrap. CSS* (consulte a continuación).</span><span class="sxs-lookup"><span data-stu-id="c897d-312">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="c897d-313">Quite `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="c897d-313">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="c897d-314">Comente la `@Html.Partial("_LoginPartial")` línea (rodee la línea con `@*...*@` ).</span><span class="sxs-lookup"><span data-stu-id="c897d-314">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="c897d-315">Para obtener más información, vea [migrar la autenticación y Identity ASP.net Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="c897d-315">For more information, see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="c897d-316">Reemplazar `@Scripts.Render("~/bundles/jquery")` por un `<script>` elemento (vea más abajo).</span><span class="sxs-lookup"><span data-stu-id="c897d-316">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="c897d-317">Reemplazar `@Scripts.Render("~/bundles/bootstrap")` por un `<script>` elemento (vea más abajo).</span><span class="sxs-lookup"><span data-stu-id="c897d-317">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="c897d-318">El marcado de reemplazo para la inclusión de CSS de bootstrap:</span><span class="sxs-lookup"><span data-stu-id="c897d-318">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="c897d-319">El marcado de reemplazo para jQuery y bootstrap JavaScript incluirá:</span><span class="sxs-lookup"><span data-stu-id="c897d-319">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="c897d-320">El archivo *_Layout. cshtml* actualizado se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="c897d-320">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="c897d-321">Vea el sitio en el explorador.</span><span class="sxs-lookup"><span data-stu-id="c897d-321">View the site in the browser.</span></span> <span data-ttu-id="c897d-322">Ahora debería cargarse correctamente, con los estilos esperados en su lugar.</span><span class="sxs-lookup"><span data-stu-id="c897d-322">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="c897d-323">*Opcional:* Pruebe a usar el nuevo archivo de diseño.</span><span class="sxs-lookup"><span data-stu-id="c897d-323">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="c897d-324">Copie el archivo de diseño del proyecto *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="c897d-324">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="c897d-325">El nuevo archivo de diseño utiliza [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) y otras mejoras.</span><span class="sxs-lookup"><span data-stu-id="c897d-325">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="c897d-326">Configuración de la unión y la minimización</span><span class="sxs-lookup"><span data-stu-id="c897d-326">Configure bundling and minification</span></span>

<span data-ttu-id="c897d-327">Para obtener información acerca de cómo configurar la agrupación y la minificación, consulte [agrupación y minificación](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="c897d-327">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="c897d-328">Solucionar errores HTTP 500</span><span class="sxs-lookup"><span data-stu-id="c897d-328">Solve HTTP 500 errors</span></span>

<span data-ttu-id="c897d-329">Hay muchos problemas que pueden producir mensajes de error HTTP 500 que no contienen información sobre el origen del problema.</span><span class="sxs-lookup"><span data-stu-id="c897d-329">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="c897d-330">Por ejemplo, si el archivo *views/_ViewImports. cshtml* contiene un espacio de nombres que no existe en el proyecto, se genera un error http 500.</span><span class="sxs-lookup"><span data-stu-id="c897d-330">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="c897d-331">De forma predeterminada, en ASP.NET Core aplicaciones, la `UseDeveloperExceptionPage` extensión se agrega a `IApplicationBuilder` y se ejecuta cuando la configuración es *desarrollo*.</span><span class="sxs-lookup"><span data-stu-id="c897d-331">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="c897d-332">Vea un ejemplo en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="c897d-332">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="c897d-333">ASP.NET Core convierte las excepciones no controladas en respuestas de error HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="c897d-333">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="c897d-334">Normalmente, los detalles del error no se incluyen en estas respuestas para evitar la divulgación de información potencialmente confidencial sobre el servidor.</span><span class="sxs-lookup"><span data-stu-id="c897d-334">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="c897d-335">Para obtener más información, consulte la [Página de excepciones para desarrolladores](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="c897d-335">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c897d-336">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c897d-336">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="c897d-337">En este artículo se muestra cómo iniciar la migración de un proyecto de ASP.NET MVC a [ASP.net Core mvc](xref:mvc/overview) 2,1.</span><span class="sxs-lookup"><span data-stu-id="c897d-337">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.1.</span></span> <span data-ttu-id="c897d-338">En el proceso, se resaltan muchas de las cosas que han cambiado de ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="c897d-338">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="c897d-339">La migración de ASP.NET MVC es un proceso de varios pasos.</span><span class="sxs-lookup"><span data-stu-id="c897d-339">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="c897d-340">En este artículo se describe:</span><span class="sxs-lookup"><span data-stu-id="c897d-340">This article covers:</span></span>

* <span data-ttu-id="c897d-341">Instalación inicial</span><span class="sxs-lookup"><span data-stu-id="c897d-341">Initial setup</span></span>
* <span data-ttu-id="c897d-342">Controladores y vistas básicos</span><span class="sxs-lookup"><span data-stu-id="c897d-342">Basic controllers and views</span></span>
* <span data-ttu-id="c897d-343">Contenido estático</span><span class="sxs-lookup"><span data-stu-id="c897d-343">Static content</span></span>
* <span data-ttu-id="c897d-344">Dependencias del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="c897d-344">Client-side dependencies.</span></span>

<span data-ttu-id="c897d-345">Para migrar la configuración y el Identity código, vea [migrar la configuración a ASP.net Core](xref:migration/configuration) y [migrar la autenticación y Identity a ASP.net Core](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="c897d-345">For migrating configuration and Identity code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity).</span></span>

> [!NOTE]
> <span data-ttu-id="c897d-346">Es posible que los números de versión de los ejemplos no estén actualizados, actualice los proyectos en consecuencia.</span><span class="sxs-lookup"><span data-stu-id="c897d-346">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="c897d-347">Crear el proyecto de MVC ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="c897d-347">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="c897d-348">Para demostrar la actualización, comenzaremos por crear una aplicación de ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="c897d-348">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="c897d-349">Créelo con el nombre *WebApp1* para que el espacio de nombres coincida con el proyecto de ASP.net Core creado en el paso siguiente.</span><span class="sxs-lookup"><span data-stu-id="c897d-349">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Cuadro de diálogo Nuevo proyecto de Visual Studio](mvc/_static/new-project.png)

![Cuadro de diálogo Nueva aplicación web: plantilla de proyecto de MVC seleccionada en el panel de plantillas de ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="c897d-352">*Opcional:* Cambie el nombre de la solución de *WebApp1* a *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="c897d-352">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="c897d-353">Visual Studio muestra el nuevo nombre de la solución (*Mvc5*), lo que facilita la información de este proyecto desde el proyecto siguiente.</span><span class="sxs-lookup"><span data-stu-id="c897d-353">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="c897d-354">Crear el proyecto de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c897d-354">Create the ASP.NET Core project</span></span>

<span data-ttu-id="c897d-355">Cree una nueva aplicación Web *vacía* de ASP.net Core con el mismo nombre que el proyecto anterior (*WebApp1*) para que coincidan los espacios de nombres de los dos proyectos.</span><span class="sxs-lookup"><span data-stu-id="c897d-355">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="c897d-356">Tener el mismo espacio de nombres facilita la copia de código entre los dos proyectos.</span><span class="sxs-lookup"><span data-stu-id="c897d-356">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="c897d-357">Cree este proyecto en un directorio diferente al del proyecto anterior para usar el mismo nombre.</span><span class="sxs-lookup"><span data-stu-id="c897d-357">Create this project in a different directory than the previous project to use the same name.</span></span>

![Cuadro de diálogo Nuevo proyecto](mvc/_static/new_core.png)

![Cuadro de diálogo Nueva aplicación Web de ASP.NET: plantilla de proyecto vacía seleccionada en el panel de plantillas de ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="c897d-360">*Opcional:* Cree una nueva aplicación de ASP.NET Core mediante la plantilla de proyecto de *aplicación web* .</span><span class="sxs-lookup"><span data-stu-id="c897d-360">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="c897d-361">Asigne al proyecto el nombre *WebApp1*y seleccione una opción de autenticación de **cuentas de usuario individuales**.</span><span class="sxs-lookup"><span data-stu-id="c897d-361">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="c897d-362">Cambie el nombre de esta aplicación a *FullAspNetCore*.</span><span class="sxs-lookup"><span data-stu-id="c897d-362">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="c897d-363">Al crear este proyecto, se ahorra tiempo en la conversión.</span><span class="sxs-lookup"><span data-stu-id="c897d-363">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="c897d-364">El resultado final se puede ver en el código generado por la plantilla, el código puede copiarse en el proyecto de conversión o compararse con el proyecto generado por una plantilla.</span><span class="sxs-lookup"><span data-stu-id="c897d-364">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="c897d-365">Configurar el sitio para usar MVC</span><span class="sxs-lookup"><span data-stu-id="c897d-365">Configure the site to use MVC</span></span>

* <span data-ttu-id="c897d-366">Cuando el destino es .NET Core, se hace referencia al [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c897d-366">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="c897d-367">Este paquete contiene los paquetes que usan normalmente las aplicaciones MVC.</span><span class="sxs-lookup"><span data-stu-id="c897d-367">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="c897d-368">Si el destino es .NET Framework, las referencias del paquete deben aparecer de forma individual en el archivo del proyecto.</span><span class="sxs-lookup"><span data-stu-id="c897d-368">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="c897d-369">`Microsoft.AspNetCore.Mvc` es el marco de MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c897d-369">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="c897d-370">`Microsoft.AspNetCore.StaticFiles` es el controlador de archivos estático.</span><span class="sxs-lookup"><span data-stu-id="c897d-370">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="c897d-371">ASP.NET Core aplicaciones optan explícitamente a un middleware, como para servir archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="c897d-371">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="c897d-372">Para obtener más información, consulte [Archivos estáticos](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="c897d-372">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="c897d-373">Abra el archivo *Startup.CS* y cambie el código para que coincida con lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c897d-373">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="c897d-374">El <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> método de extensión agrega el controlador de archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="c897d-374">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="c897d-375">El `UseMvc` método de extensión agrega enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="c897d-375">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="c897d-376">Para obtener más información, consulte Inicio y [enrutamiento](xref:fundamentals/routing)de la [aplicación](xref:fundamentals/startup) .</span><span class="sxs-lookup"><span data-stu-id="c897d-376">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="c897d-377">Agregar un controlador y una vista</span><span class="sxs-lookup"><span data-stu-id="c897d-377">Add a controller and view</span></span>

<span data-ttu-id="c897d-378">En esta sección, se agrega un controlador y una vista mínimos para servir como marcadores de posición para el controlador ASP.NET MVC y las vistas migradas en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="c897d-378">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="c897d-379">Agregue un directorio de *Controladores* .</span><span class="sxs-lookup"><span data-stu-id="c897d-379">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="c897d-380">Agregue una **clase de controlador** denominada *HomeController.CS* al directorio *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="c897d-380">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![Cuadro de diálogo Agregar nuevo elemento](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="c897d-382">Agregue un directorio *views* .</span><span class="sxs-lookup"><span data-stu-id="c897d-382">Add a *Views* directory.</span></span>

* <span data-ttu-id="c897d-383">Agregue un directorio de *vistas o inicio* .</span><span class="sxs-lookup"><span data-stu-id="c897d-383">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="c897d-384">Agregue una \*\* Razor vista\*\* denominada *index. cshtml* al directorio *views/Home* .</span><span class="sxs-lookup"><span data-stu-id="c897d-384">Add a **Razor View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![Cuadro de diálogo Agregar nuevo elemento](mvc/_static/view.png)

<span data-ttu-id="c897d-386">La estructura del proyecto se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="c897d-386">The project structure is shown below:</span></span>

![Explorador de soluciones que muestra los archivos y directorios de WebApp1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="c897d-388">Reemplace el contenido del archivo *Views/Home/Index.cshtml* por el marcado siguiente:</span><span class="sxs-lookup"><span data-stu-id="c897d-388">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="c897d-389">Ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c897d-389">Run the app.</span></span>

![Aplicación web abierta en Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="c897d-391">Para obtener más información, vea [Controladores](xref:mvc/controllers/actions) y [vistas](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="c897d-391">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="c897d-392">La funcionalidad siguiente requiere la migración del proyecto de ejemplo ASP.NET MVC al proyecto ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="c897d-392">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="c897d-393">contenido del lado cliente (CSS, fuentes y scripts)</span><span class="sxs-lookup"><span data-stu-id="c897d-393">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="c897d-394">controllers</span><span class="sxs-lookup"><span data-stu-id="c897d-394">controllers</span></span>

* <span data-ttu-id="c897d-395">views</span><span class="sxs-lookup"><span data-stu-id="c897d-395">views</span></span>

* <span data-ttu-id="c897d-396">modelos</span><span class="sxs-lookup"><span data-stu-id="c897d-396">models</span></span>

* <span data-ttu-id="c897d-397">Unión</span><span class="sxs-lookup"><span data-stu-id="c897d-397">bundling</span></span>

* <span data-ttu-id="c897d-398">filters</span><span class="sxs-lookup"><span data-stu-id="c897d-398">filters</span></span>

* <span data-ttu-id="c897d-399">Iniciar y cerrar sesión Identity (esto se hace en el siguiente tutorial).</span><span class="sxs-lookup"><span data-stu-id="c897d-399">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="c897d-400">Controladores y vistas</span><span class="sxs-lookup"><span data-stu-id="c897d-400">Controllers and views</span></span>

* <span data-ttu-id="c897d-401">Copie cada uno de los métodos de MVC de ASP.NET `HomeController` al nuevo `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="c897d-401">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="c897d-402">En ASP.NET MVC, el tipo de valor devuelto del método de acción del controlador de la plantilla integrada es <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; en ASP.net Core MVC, los métodos de acción devuelven en `IActionResult` su lugar.</span><span class="sxs-lookup"><span data-stu-id="c897d-402">In ASP.NET MVC, the built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="c897d-403">`ActionResult` implementa `IActionResult` , por lo que no es necesario cambiar el tipo de valor devuelto de los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="c897d-403">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="c897d-404">Copie los archivos de vista *About. cshtml*, *Contact. cshtml*e *Index. cshtml* Razor del proyecto ASP.NET MVC en el proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="c897d-404">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="c897d-405">Prueba de cada método</span><span class="sxs-lookup"><span data-stu-id="c897d-405">Test each method</span></span>

<span data-ttu-id="c897d-406">Todavía no se han migrado el archivo de diseño y los estilos, por lo que las vistas representadas solo contienen el contenido de los archivos de vista.</span><span class="sxs-lookup"><span data-stu-id="c897d-406">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="c897d-407">Los vínculos generados por el archivo de diseño para las `About` `Contact` vistas y no estarán disponibles todavía.</span><span class="sxs-lookup"><span data-stu-id="c897d-407">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

* <span data-ttu-id="c897d-408">Invoque las vistas representadas desde el explorador en la aplicación ASP.NET Core en ejecución reemplazando el número de puerto actual por el número de Puerto usado en el proyecto de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c897d-408">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="c897d-409">Por ejemplo: `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="c897d-409">For example: `https://localhost:44375/home/about`.</span></span>

![Página de contacto](mvc/_static/contact-page.png)

<span data-ttu-id="c897d-411">Tenga en cuenta la falta de estilo y elementos de menú.</span><span class="sxs-lookup"><span data-stu-id="c897d-411">Note the lack of styling and menu items.</span></span> <span data-ttu-id="c897d-412">El estilo se corregirá en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="c897d-412">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="c897d-413">Contenido estático</span><span class="sxs-lookup"><span data-stu-id="c897d-413">Static content</span></span>

<span data-ttu-id="c897d-414">En ASP.NET MVC 5 y versiones anteriores, el contenido estático se hospedaba en la raíz del proyecto web y se combinaba con los archivos del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="c897d-414">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="c897d-415">En ASP.NET Core, el contenido estático se hospeda en el directorio *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="c897d-415">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="c897d-416">Copie el contenido estático de la aplicación ASP.NET MVC en el directorio *wwwroot* del proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="c897d-416">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="c897d-417">En esta conversión de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c897d-417">In this sample conversion:</span></span>

* <span data-ttu-id="c897d-418">Copie el archivo *favicon. ico* del proyecto ASP.NET MVC en el directorio *wwwroot* del proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="c897d-418">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="c897d-419">El proyecto ASP.NET MVC usa [bootstrap](https://getbootstrap.com/) para su estilo y almacena los archivos de arranque en los directorios de *contenido* y *scripts* .</span><span class="sxs-lookup"><span data-stu-id="c897d-419">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="c897d-420">La plantilla, que generó el proyecto ASP.NET MVC, hace referencia a bootstrap en el archivo de diseño (*views/Shared/_Layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="c897d-420">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="c897d-421">Los archivos *bootstrap.js* y *bootstrap. CSS* se pueden copiar del proyecto ASP.NET MVC al directorio *wwwroot* del nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="c897d-421">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="c897d-422">En su lugar, este documento agrega compatibilidad con bootstrap (y otras bibliotecas del lado cliente) mediante redes CDN, en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="c897d-422">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="c897d-423">Migrar el archivo de diseño</span><span class="sxs-lookup"><span data-stu-id="c897d-423">Migrate the layout file</span></span>

* <span data-ttu-id="c897d-424">Copie el archivo *_ViewStart. cshtml* del directorio *views* del proyecto ASP.NET MVC en el directorio *views* del proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="c897d-424">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="c897d-425">El archivo *_ViewStart. cshtml* no ha cambiado en ASP.net Core MVC.</span><span class="sxs-lookup"><span data-stu-id="c897d-425">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="c897d-426">Cree un directorio *views/Shared* .</span><span class="sxs-lookup"><span data-stu-id="c897d-426">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="c897d-427">*Opcional:* Copie *_ViewImports. cshtml* del directorio *views* del proyecto de *FullAspNetCore* MVC en el directorio *views* del proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="c897d-427">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="c897d-428">Quite cualquier declaración de espacio de nombres del archivo *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="c897d-428">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="c897d-429">El archivo *_ViewImports. cshtml* proporciona espacios de nombres para todos los archivos de vista y ofrece [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="c897d-429">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="c897d-430">Las aplicaciones auxiliares de etiquetas se usan en el nuevo archivo de diseño.</span><span class="sxs-lookup"><span data-stu-id="c897d-430">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="c897d-431">El archivo *_ViewImports. cshtml* es nuevo para ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="c897d-431">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="c897d-432">Copie el archivo *_Layout. cshtml* del directorio de *vistas/compartidos* del proyecto de ASP.NET MVC en el directorio de *vistas/compartidos* del proyecto de ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="c897d-432">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="c897d-433">Abra *_Layout archivo. cshtml* y realice los cambios siguientes (el código completo se muestra a continuación):</span><span class="sxs-lookup"><span data-stu-id="c897d-433">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="c897d-434">Reemplace `@Styles.Render("~/Content/css")` por un `<link>` elemento para cargar *bootstrap. CSS* (consulte a continuación).</span><span class="sxs-lookup"><span data-stu-id="c897d-434">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="c897d-435">Quite `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="c897d-435">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="c897d-436">Comente la `@Html.Partial("_LoginPartial")` línea (rodee la línea con `@*...*@` ).</span><span class="sxs-lookup"><span data-stu-id="c897d-436">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="c897d-437">Para obtener más información, vea [migrar la autenticación y Identity ASP.net Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="c897d-437">For more information, see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="c897d-438">Reemplazar `@Scripts.Render("~/bundles/jquery")` por un `<script>` elemento (vea más abajo).</span><span class="sxs-lookup"><span data-stu-id="c897d-438">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="c897d-439">Reemplazar `@Scripts.Render("~/bundles/bootstrap")` por un `<script>` elemento (vea más abajo).</span><span class="sxs-lookup"><span data-stu-id="c897d-439">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="c897d-440">El marcado de reemplazo para la inclusión de CSS de bootstrap:</span><span class="sxs-lookup"><span data-stu-id="c897d-440">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="c897d-441">El marcado de reemplazo para jQuery y bootstrap JavaScript incluirá:</span><span class="sxs-lookup"><span data-stu-id="c897d-441">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="c897d-442">El archivo *_Layout. cshtml* actualizado se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="c897d-442">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="c897d-443">Vea el sitio en el explorador.</span><span class="sxs-lookup"><span data-stu-id="c897d-443">View the site in the browser.</span></span> <span data-ttu-id="c897d-444">Ahora debería cargarse correctamente, con los estilos esperados en su lugar.</span><span class="sxs-lookup"><span data-stu-id="c897d-444">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="c897d-445">*Opcional:* Pruebe a usar el nuevo archivo de diseño.</span><span class="sxs-lookup"><span data-stu-id="c897d-445">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="c897d-446">Copie el archivo de diseño del proyecto *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="c897d-446">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="c897d-447">El nuevo archivo de diseño utiliza [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) y otras mejoras.</span><span class="sxs-lookup"><span data-stu-id="c897d-447">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="c897d-448">Configuración de la unión y la minimización</span><span class="sxs-lookup"><span data-stu-id="c897d-448">Configure bundling and minification</span></span>

<span data-ttu-id="c897d-449">Para obtener información acerca de cómo configurar la agrupación y la minificación, consulte [agrupación y minificación](xref:client-side/bundling-and-minification).</span><span class="sxs-lookup"><span data-stu-id="c897d-449">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="c897d-450">Solucionar errores HTTP 500</span><span class="sxs-lookup"><span data-stu-id="c897d-450">Solve HTTP 500 errors</span></span>

<span data-ttu-id="c897d-451">Hay muchos problemas que pueden producir mensajes de error HTTP 500 que no contienen información sobre el origen del problema.</span><span class="sxs-lookup"><span data-stu-id="c897d-451">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="c897d-452">Por ejemplo, si el archivo *views/_ViewImports. cshtml* contiene un espacio de nombres que no existe en el proyecto, se genera un error http 500.</span><span class="sxs-lookup"><span data-stu-id="c897d-452">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="c897d-453">De forma predeterminada, en ASP.NET Core aplicaciones, la `UseDeveloperExceptionPage` extensión se agrega a `IApplicationBuilder` y se ejecuta cuando la configuración es *desarrollo*.</span><span class="sxs-lookup"><span data-stu-id="c897d-453">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="c897d-454">Vea un ejemplo en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="c897d-454">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="c897d-455">ASP.NET Core convierte las excepciones no controladas en respuestas de error HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="c897d-455">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="c897d-456">Normalmente, los detalles del error no se incluyen en estas respuestas para evitar la divulgación de información potencialmente confidencial sobre el servidor.</span><span class="sxs-lookup"><span data-stu-id="c897d-456">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="c897d-457">Para obtener más información, consulte la [Página de excepciones para desarrolladores](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="c897d-457">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c897d-458">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c897d-458">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
