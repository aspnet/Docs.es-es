---
title: Compatibilidad de IIS de tiempo de desarrollo en Visual Studio para ASP.NET Core
author: rick-anderson
description: Descubra la compatibilidad con la depuración de aplicaciones ASP.NET Core cuando se ejecutan con IIS en Windows Server.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: d922b2eb4d00a252b3e7da2f00de8b4571359b20
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406438"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="af970-103">Compatibilidad de IIS de tiempo de desarrollo en Visual Studio para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af970-103">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="af970-104">Por [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="af970-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="af970-105">En este artículo se describe la compatibilidad de [Visual Studio](https://visualstudio.microsoft.com) con la depuración de aplicaciones ASP.NET Core que se ejecutan con IIS en Windows Server.</span><span class="sxs-lookup"><span data-stu-id="af970-105">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="af970-106">En este tema se explica cómo habilitar este escenario y configurar un proyecto.</span><span class="sxs-lookup"><span data-stu-id="af970-106">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="af970-107">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="af970-107">Prerequisites</span></span>

* <span data-ttu-id="af970-108">[Visual Studio para Windows](https://visualstudio.microsoft.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="af970-108">[Visual Studio for Windows](https://visualstudio.microsoft.com/downloads/)</span></span>
* <span data-ttu-id="af970-109">Carga de trabajo de **ASP.NET y desarrollo web**</span><span class="sxs-lookup"><span data-stu-id="af970-109">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="af970-110">Carga de trabajo **Desarrollo multiplataforma de .NET Core**</span><span class="sxs-lookup"><span data-stu-id="af970-110">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="af970-111">Certificado de seguridad X.509 (para compatibilidad con HTTPS)</span><span class="sxs-lookup"><span data-stu-id="af970-111">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="af970-112">Habilitar IIS</span><span class="sxs-lookup"><span data-stu-id="af970-112">Enable IIS</span></span>

1. <span data-ttu-id="af970-113">En Windows, navegue a **Panel de control** > **Programas** > **Programas y características** > **Activar o desactivar las características de Windows** (lado izquierdo de la pantalla).</span><span class="sxs-lookup"><span data-stu-id="af970-113">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="af970-114">Active la casilla **Internet Information Services**.</span><span class="sxs-lookup"><span data-stu-id="af970-114">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="af970-115">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="af970-115">Select **OK**.</span></span>

<span data-ttu-id="af970-116">La instalación de IIS puede requerir un reinicio del sistema.</span><span class="sxs-lookup"><span data-stu-id="af970-116">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="af970-117">Configurar IIS</span><span class="sxs-lookup"><span data-stu-id="af970-117">Configure IIS</span></span>

<span data-ttu-id="af970-118">IIS debe tener un sitio web configurado con lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="af970-118">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="af970-119">**Nombre de host**: por lo general, el **sitio web predeterminado** se usa con un **nombre de host** de `localhost`.</span><span class="sxs-lookup"><span data-stu-id="af970-119">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="af970-120">Sin embargo, sirve cualquier sitio web de IIS válido con un nombre de host único.</span><span class="sxs-lookup"><span data-stu-id="af970-120">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="af970-121">**Enlace de sitio**</span><span class="sxs-lookup"><span data-stu-id="af970-121">**Site Binding**</span></span>
  * <span data-ttu-id="af970-122">Para las aplicaciones que requieran HTTPS, cree un enlace al puerto 443 con un certificado.</span><span class="sxs-lookup"><span data-stu-id="af970-122">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="af970-123">Por lo general, se usa el **certificado de desarrollo de IIS Express**, pero cualquier certificado válido sirve.</span><span class="sxs-lookup"><span data-stu-id="af970-123">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="af970-124">Para las aplicaciones que usan HTTP, confirme la existencia de un enlace al puerto 80 o cree un enlace a dicho puerto si se trata de un sitio nuevo.</span><span class="sxs-lookup"><span data-stu-id="af970-124">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="af970-125">Utilice un enlace único para HTTP o HTTPS.</span><span class="sxs-lookup"><span data-stu-id="af970-125">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="af970-126">**No se admite el enlace al mismo tiempo a los puertos HTTP y HTTPS.**</span><span class="sxs-lookup"><span data-stu-id="af970-126">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="af970-127">Habilitación de la compatibilidad con IIS en tiempo de desarrollo en Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af970-127">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="af970-128">Inicie el instalador de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="af970-128">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="af970-129">Seleccione **Modificar** para la instalación de Visual Studio que se pretende usar para la compatibilidad con IIS en tiempo de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="af970-129">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="af970-130">Para la carga de trabajo de **Desarrollo de ASP.NET y web** , busque e instale el componente **Compatibilidad con IIS en tiempo de desarrollo**.</span><span class="sxs-lookup"><span data-stu-id="af970-130">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="af970-131">El componente se enumera en la sección **Opcional**, en **Compatibilidad con IIS en tiempo de desarrollo**, dentro del panel **Detalles de instalación** a la derecha de las cargas de trabajo.</span><span class="sxs-lookup"><span data-stu-id="af970-131">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="af970-132">El componente instala el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), que es un módulo de IIS nativo necesario para ejecutar aplicaciones de ASP.NET Core con IIS.</span><span class="sxs-lookup"><span data-stu-id="af970-132">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="af970-133">Configuración del proyecto</span><span class="sxs-lookup"><span data-stu-id="af970-133">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="af970-134">Redireccionamiento de HTTPS</span><span class="sxs-lookup"><span data-stu-id="af970-134">HTTPS redirection</span></span>

<span data-ttu-id="af970-135">Para un nuevo proyecto que requiere HTTPS, seleccione la casilla **Configurar para HTTPS** en la ventana **Crear una aplicación web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="af970-135">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="af970-136">Al seleccionar la casilla, se agrega [redireccionamiento HTTPS y middleware HSTS](xref:security/enforcing-ssl) a la aplicación cuando esta se crea.</span><span class="sxs-lookup"><span data-stu-id="af970-136">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="af970-137">Para un proyecto existente que requiere HTTPS, use el redireccionamiento HTTPS y el middleware HSTS en `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="af970-137">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="af970-138">Para obtener más información, vea <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="af970-138">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="af970-139">Para un proyecto que usa HTTP, el [redireccionamiento HTTPS y el middleware HSTS](xref:security/enforcing-ssl) no se agregan a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="af970-139">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="af970-140">No se requiere ninguna configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="af970-140">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="af970-141">Perfil de inicio de IIS</span><span class="sxs-lookup"><span data-stu-id="af970-141">IIS launch profile</span></span>

<span data-ttu-id="af970-142">Cree un nuevo perfil de inicio para agregar la compatibilidad con IIS en tiempo de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="af970-142">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="af970-143">Haga clic con el botón derecho en el **Explorador de soluciones**.</span><span class="sxs-lookup"><span data-stu-id="af970-143">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="af970-144">Haga clic en **Propiedades**.</span><span class="sxs-lookup"><span data-stu-id="af970-144">Select **Properties**.</span></span> <span data-ttu-id="af970-145">Abra la pestaña **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="af970-145">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="af970-146">En **Perfil**, seleccione el botón **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="af970-146">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="af970-147">Asigne el perfil el nombre "IIS" en la ventana emergente.</span><span class="sxs-lookup"><span data-stu-id="af970-147">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="af970-148">Seleccione **Aceptar** para crear el perfil.</span><span class="sxs-lookup"><span data-stu-id="af970-148">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="af970-149">En **Iniciar**, seleccione **IIS** en la lista.</span><span class="sxs-lookup"><span data-stu-id="af970-149">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="af970-150">Active la casilla **Iniciar explorador** y proporcione la dirección URL del punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="af970-150">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="af970-151">Si la aplicación requiere HTTPS, use un punto de conexión HTTPS (`https://`).</span><span class="sxs-lookup"><span data-stu-id="af970-151">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="af970-152">Para HTTP, use un punto de conexión HTTP (`http://`).</span><span class="sxs-lookup"><span data-stu-id="af970-152">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="af970-153">Proporcione el mismo nombre de host y puerto especificados en la [configuración de IIS establecida en usos anteriores](#configure-iis), normalmente `localhost`.</span><span class="sxs-lookup"><span data-stu-id="af970-153">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="af970-154">Proporcione el nombre de la aplicación al final de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="af970-154">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="af970-155">Por ejemplo, `https://localhost/WebApplication1` (HTTPS) o `http://localhost/WebApplication1` (HTTP) son direcciones URL de punto de conexión válidas.</span><span class="sxs-lookup"><span data-stu-id="af970-155">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="af970-156">En la sección **Variables de entorno**, seleccione el botón **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="af970-156">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="af970-157">Proporcione una variable de entorno con un **Nombre** de `ASPNETCORE_ENVIRONMENT` y un **Valor** de `Development`.</span><span class="sxs-lookup"><span data-stu-id="af970-157">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="af970-158">En el área **Configuración del servidor web**, defina la **Dirección URL de la aplicación** con el mismo valor utilizado para la dirección URL de punto de conexión de **Iniciar el explorador**.</span><span class="sxs-lookup"><span data-stu-id="af970-158">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="af970-159">Para la configuración del **Modelo de hospedaje** de Visual Studio 2019 o posterior, seleccione **Predeterminado** para usar el modelo de hospedaje utilizado por el proyecto.</span><span class="sxs-lookup"><span data-stu-id="af970-159">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="af970-160">Si el proyecto establece la propiedad `<AspNetCoreHostingModel>` en su archivo del proyecto, se usa el valor de la propiedad (`InProcess` o `OutOfProcess`).</span><span class="sxs-lookup"><span data-stu-id="af970-160">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="af970-161">Si la propiedad no existe, se usa el modelo de hospedaje predeterminado de la aplicación, que está en proceso.</span><span class="sxs-lookup"><span data-stu-id="af970-161">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="af970-162">Si la aplicación requiere una configuración del modelo de hospedaje distinta a la del modelo de hospedaje habitual de la aplicación, defina el **Modelo de hospedaje** como `In Process` o `Out Of Process`, según proceda.</span><span class="sxs-lookup"><span data-stu-id="af970-162">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="af970-163">Guarde el perfil.</span><span class="sxs-lookup"><span data-stu-id="af970-163">Save the profile.</span></span>

<span data-ttu-id="af970-164">Si no se usa Visual Studio, agregue manualmente un perfil de inicio al archivo [launchSettings.json](https://json.schemastore.org/launchsettings) en la carpeta *Propiedades*.</span><span class="sxs-lookup"><span data-stu-id="af970-164">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="af970-165">El siguiente ejemplo configura el perfil para usar el protocolo HTTPS:</span><span class="sxs-lookup"><span data-stu-id="af970-165">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="af970-166">Confirme que los puntos de conexión `applicationUrl` y `launchUrl` coinciden y use el mismo protocolo que la configuración de enlace de IIS, es decir, HTTP o HTTPS.</span><span class="sxs-lookup"><span data-stu-id="af970-166">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="af970-167">Ejecución del proyecto</span><span class="sxs-lookup"><span data-stu-id="af970-167">Run the project</span></span>

<span data-ttu-id="af970-168">Ejecute Visual Studio como administrador:</span><span class="sxs-lookup"><span data-stu-id="af970-168">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="af970-169">Confirme que la lista desplegable de configuración de compilación está configurada como **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="af970-169">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="af970-170">Establezca el botón [Iniciar depuración](/visualstudio/debugger/debugger-feature-tour) en el perfil de **IIS** y seleccione el botón para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="af970-170">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="af970-171">Visual Studio puede solicitar un reinicio si no se ejecuta como administrador.</span><span class="sxs-lookup"><span data-stu-id="af970-171">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="af970-172">Si es así, reinicie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="af970-172">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="af970-173">Si se usa un certificado de desarrollo que no es de confianza, el explorador puede pedirle que cree una excepción para un certificado de esta clase.</span><span class="sxs-lookup"><span data-stu-id="af970-173">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="af970-174">La depuración de una configuración de compilación de versión con [Solo mi código](/visualstudio/debugger/just-my-code) y las optimizaciones del compilador degradan el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="af970-174">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="af970-175">Por ejemplo, no se alcanzan los puntos de interrupción.</span><span class="sxs-lookup"><span data-stu-id="af970-175">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="af970-176">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="af970-176">Additional resources</span></span>

* [<span data-ttu-id="af970-177">Introducción al Administrador de IIS en IIS</span><span class="sxs-lookup"><span data-stu-id="af970-177">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="af970-178">En este artículo se describe la compatibilidad de [Visual Studio](https://visualstudio.microsoft.com) con la depuración de aplicaciones ASP.NET Core que se ejecutan con IIS en Windows Server.</span><span class="sxs-lookup"><span data-stu-id="af970-178">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="af970-179">En este tema se explica cómo habilitar este escenario y configurar un proyecto.</span><span class="sxs-lookup"><span data-stu-id="af970-179">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="af970-180">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="af970-180">Prerequisites</span></span>

* <span data-ttu-id="af970-181">[Visual Studio para Windows](https://visualstudio.microsoft.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="af970-181">[Visual Studio for Windows](https://visualstudio.microsoft.com/downloads/)</span></span>
* <span data-ttu-id="af970-182">Carga de trabajo de **ASP.NET y desarrollo web**</span><span class="sxs-lookup"><span data-stu-id="af970-182">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="af970-183">Carga de trabajo **Desarrollo multiplataforma de .NET Core**</span><span class="sxs-lookup"><span data-stu-id="af970-183">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="af970-184">Certificado de seguridad X.509 (para compatibilidad con HTTPS)</span><span class="sxs-lookup"><span data-stu-id="af970-184">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="af970-185">Habilitar IIS</span><span class="sxs-lookup"><span data-stu-id="af970-185">Enable IIS</span></span>

1. <span data-ttu-id="af970-186">En Windows, navegue a **Panel de control** > **Programas** > **Programas y características** > **Activar o desactivar las características de Windows** (lado izquierdo de la pantalla).</span><span class="sxs-lookup"><span data-stu-id="af970-186">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="af970-187">Active la casilla **Internet Information Services**.</span><span class="sxs-lookup"><span data-stu-id="af970-187">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="af970-188">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="af970-188">Select **OK**.</span></span>

<span data-ttu-id="af970-189">La instalación de IIS puede requerir un reinicio del sistema.</span><span class="sxs-lookup"><span data-stu-id="af970-189">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="af970-190">Configurar IIS</span><span class="sxs-lookup"><span data-stu-id="af970-190">Configure IIS</span></span>

<span data-ttu-id="af970-191">IIS debe tener un sitio web configurado con lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="af970-191">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="af970-192">**Nombre de host**: por lo general, el **sitio web predeterminado** se usa con un **nombre de host** de `localhost`.</span><span class="sxs-lookup"><span data-stu-id="af970-192">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="af970-193">Sin embargo, sirve cualquier sitio web de IIS válido con un nombre de host único.</span><span class="sxs-lookup"><span data-stu-id="af970-193">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="af970-194">**Enlace de sitio**</span><span class="sxs-lookup"><span data-stu-id="af970-194">**Site Binding**</span></span>
  * <span data-ttu-id="af970-195">Para las aplicaciones que requieran HTTPS, cree un enlace al puerto 443 con un certificado.</span><span class="sxs-lookup"><span data-stu-id="af970-195">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="af970-196">Por lo general, se usa el **certificado de desarrollo de IIS Express**, pero cualquier certificado válido sirve.</span><span class="sxs-lookup"><span data-stu-id="af970-196">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="af970-197">Para las aplicaciones que usan HTTP, confirme la existencia de un enlace al puerto 80 o cree un enlace a dicho puerto si se trata de un sitio nuevo.</span><span class="sxs-lookup"><span data-stu-id="af970-197">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="af970-198">Utilice un enlace único para HTTP o HTTPS.</span><span class="sxs-lookup"><span data-stu-id="af970-198">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="af970-199">**No se admite el enlace al mismo tiempo a los puertos HTTP y HTTPS.**</span><span class="sxs-lookup"><span data-stu-id="af970-199">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="af970-200">Habilitación de la compatibilidad con IIS en tiempo de desarrollo en Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af970-200">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="af970-201">Inicie el instalador de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="af970-201">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="af970-202">Seleccione **Modificar** para la instalación de Visual Studio que se pretende usar para la compatibilidad con IIS en tiempo de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="af970-202">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="af970-203">Para la carga de trabajo de **Desarrollo de ASP.NET y web** , busque e instale el componente **Compatibilidad con IIS en tiempo de desarrollo**.</span><span class="sxs-lookup"><span data-stu-id="af970-203">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="af970-204">El componente se enumera en la sección **Opcional**, en **Compatibilidad con IIS en tiempo de desarrollo**, dentro del panel **Detalles de instalación** a la derecha de las cargas de trabajo.</span><span class="sxs-lookup"><span data-stu-id="af970-204">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="af970-205">El componente instala el [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), que es un módulo de IIS nativo necesario para ejecutar aplicaciones de ASP.NET Core con IIS.</span><span class="sxs-lookup"><span data-stu-id="af970-205">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="af970-206">Configuración del proyecto</span><span class="sxs-lookup"><span data-stu-id="af970-206">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="af970-207">Redireccionamiento de HTTPS</span><span class="sxs-lookup"><span data-stu-id="af970-207">HTTPS redirection</span></span>

<span data-ttu-id="af970-208">Para un nuevo proyecto que requiere HTTPS, seleccione la casilla **Configurar para HTTPS** en la ventana **Crear una aplicación web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="af970-208">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="af970-209">Al seleccionar la casilla, se agrega [redireccionamiento HTTPS y middleware HSTS](xref:security/enforcing-ssl) a la aplicación cuando esta se crea.</span><span class="sxs-lookup"><span data-stu-id="af970-209">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="af970-210">Para un proyecto existente que requiere HTTPS, use el redireccionamiento HTTPS y el middleware HSTS en `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="af970-210">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="af970-211">Para obtener más información, vea <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="af970-211">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="af970-212">Para un proyecto que usa HTTP, el [redireccionamiento HTTPS y el middleware HSTS](xref:security/enforcing-ssl) no se agregan a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="af970-212">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="af970-213">No se requiere ninguna configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="af970-213">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="af970-214">Perfil de inicio de IIS</span><span class="sxs-lookup"><span data-stu-id="af970-214">IIS launch profile</span></span>

<span data-ttu-id="af970-215">Cree un nuevo perfil de inicio para agregar la compatibilidad con IIS en tiempo de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="af970-215">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="af970-216">Haga clic con el botón derecho en el **Explorador de soluciones**.</span><span class="sxs-lookup"><span data-stu-id="af970-216">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="af970-217">Haga clic en **Propiedades**.</span><span class="sxs-lookup"><span data-stu-id="af970-217">Select **Properties**.</span></span> <span data-ttu-id="af970-218">Abra la pestaña **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="af970-218">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="af970-219">En **Perfil**, seleccione el botón **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="af970-219">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="af970-220">Asigne el perfil el nombre "IIS" en la ventana emergente.</span><span class="sxs-lookup"><span data-stu-id="af970-220">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="af970-221">Seleccione **Aceptar** para crear el perfil.</span><span class="sxs-lookup"><span data-stu-id="af970-221">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="af970-222">En **Iniciar**, seleccione **IIS** en la lista.</span><span class="sxs-lookup"><span data-stu-id="af970-222">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="af970-223">Active la casilla **Iniciar explorador** y proporcione la dirección URL del punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="af970-223">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="af970-224">Si la aplicación requiere HTTPS, use un punto de conexión HTTPS (`https://`).</span><span class="sxs-lookup"><span data-stu-id="af970-224">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="af970-225">Para HTTP, use un punto de conexión HTTP (`http://`).</span><span class="sxs-lookup"><span data-stu-id="af970-225">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="af970-226">Proporcione el mismo nombre de host y puerto especificados en la [configuración de IIS establecida en usos anteriores](#configure-iis), normalmente `localhost`.</span><span class="sxs-lookup"><span data-stu-id="af970-226">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="af970-227">Proporcione el nombre de la aplicación al final de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="af970-227">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="af970-228">Por ejemplo, `https://localhost/WebApplication1` (HTTPS) o `http://localhost/WebApplication1` (HTTP) son direcciones URL de punto de conexión válidas.</span><span class="sxs-lookup"><span data-stu-id="af970-228">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="af970-229">En la sección **Variables de entorno**, seleccione el botón **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="af970-229">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="af970-230">Proporcione una variable de entorno con un **Nombre** de `ASPNETCORE_ENVIRONMENT` y un **Valor** de `Development`.</span><span class="sxs-lookup"><span data-stu-id="af970-230">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="af970-231">En el área **Configuración del servidor web**, defina la **Dirección URL de la aplicación** con el mismo valor utilizado para la dirección URL de punto de conexión de **Iniciar el explorador**.</span><span class="sxs-lookup"><span data-stu-id="af970-231">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="af970-232">Para la configuración del **Modelo de hospedaje** de Visual Studio 2019 o posterior, seleccione **Predeterminado** para usar el modelo de hospedaje utilizado por el proyecto.</span><span class="sxs-lookup"><span data-stu-id="af970-232">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="af970-233">Si el proyecto establece la propiedad `<AspNetCoreHostingModel>` en su archivo del proyecto, se usa el valor de la propiedad (`InProcess` o `OutOfProcess`).</span><span class="sxs-lookup"><span data-stu-id="af970-233">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="af970-234">Si la propiedad no existe, se usa el modelo de hospedaje predeterminado de la aplicación, que está fuera de proceso.</span><span class="sxs-lookup"><span data-stu-id="af970-234">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="af970-235">Si la aplicación requiere una configuración del modelo de hospedaje distinta a la del modelo de hospedaje habitual de la aplicación, defina el **Modelo de hospedaje** como `In Process` o `Out Of Process`, según proceda.</span><span class="sxs-lookup"><span data-stu-id="af970-235">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="af970-236">Guarde el perfil.</span><span class="sxs-lookup"><span data-stu-id="af970-236">Save the profile.</span></span>

<span data-ttu-id="af970-237">Si no se usa Visual Studio, agregue manualmente un perfil de inicio al archivo [launchSettings.json](https://json.schemastore.org/launchsettings) en la carpeta *Propiedades*.</span><span class="sxs-lookup"><span data-stu-id="af970-237">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="af970-238">El siguiente ejemplo configura el perfil para usar el protocolo HTTPS:</span><span class="sxs-lookup"><span data-stu-id="af970-238">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="af970-239">Confirme que los puntos de conexión `applicationUrl` y `launchUrl` coinciden y use el mismo protocolo que la configuración de enlace de IIS, es decir, HTTP o HTTPS.</span><span class="sxs-lookup"><span data-stu-id="af970-239">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="af970-240">Ejecución del proyecto</span><span class="sxs-lookup"><span data-stu-id="af970-240">Run the project</span></span>

<span data-ttu-id="af970-241">Ejecute Visual Studio como administrador:</span><span class="sxs-lookup"><span data-stu-id="af970-241">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="af970-242">Confirme que la lista desplegable de configuración de compilación está configurada como **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="af970-242">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="af970-243">Establezca el botón [Iniciar depuración](/visualstudio/debugger/debugger-feature-tour) en el perfil de **IIS** y seleccione el botón para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="af970-243">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="af970-244">Visual Studio puede solicitar un reinicio si no se ejecuta como administrador.</span><span class="sxs-lookup"><span data-stu-id="af970-244">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="af970-245">Si es así, reinicie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="af970-245">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="af970-246">Si se usa un certificado de desarrollo que no es de confianza, el explorador puede pedirle que cree una excepción para un certificado de esta clase.</span><span class="sxs-lookup"><span data-stu-id="af970-246">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="af970-247">La depuración de una configuración de compilación de versión con [Solo mi código](/visualstudio/debugger/just-my-code) y las optimizaciones del compilador degradan el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="af970-247">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="af970-248">Por ejemplo, no se alcanzan los puntos de interrupción.</span><span class="sxs-lookup"><span data-stu-id="af970-248">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="af970-249">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="af970-249">Additional resources</span></span>

* [<span data-ttu-id="af970-250">Introducción al Administrador de IIS en IIS</span><span class="sxs-lookup"><span data-stu-id="af970-250">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
