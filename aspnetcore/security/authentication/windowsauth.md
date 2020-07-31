---
title: Configurar la autenticación de Windows en ASP.NET Core
author: scottaddie
description: Obtenga información sobre cómo configurar la autenticación de Windows en ASP.NET Core para IIS y HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/windowsauth
ms.openlocfilehash: 8f6dc8620df04bcebe996119869ca2e498cffccc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "87330694"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="3f2bc-103">Configurar la autenticación de Windows en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3f2bc-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="3f2bc-104">Por [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="3f2bc-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3f2bc-105">La autenticación de Windows (también conocida como autenticación de negociación, Kerberos o NTLM) puede configurarse para ASP.NET Core aplicaciones hospedadas con [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel)o [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="3f2bc-105">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel), or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3f2bc-106">La autenticación de Windows (también conocida como autenticación de negociación, Kerberos o NTLM) puede configurarse para ASP.NET Core aplicaciones hospedadas con [IIS](xref:host-and-deploy/iis/index) o [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="3f2bc-106">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

<span data-ttu-id="3f2bc-107">La autenticación de Windows se basa en el sistema operativo para autenticar a los usuarios de ASP.NET Core aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-107">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="3f2bc-108">Puede usar la autenticación de Windows cuando el servidor se ejecuta en una red corporativa con Active Directory identidades de dominio o cuentas de Windows para identificar a los usuarios.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-108">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="3f2bc-109">La autenticación de Windows es más adecuada para entornos de intranet en los que los usuarios, las aplicaciones cliente y los servidores Web pertenecen al mismo dominio de Windows.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-109">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

> [!NOTE]
> <span data-ttu-id="3f2bc-110">La autenticación de Windows no es compatible con HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-110">Windows Authentication isn't supported with HTTP/2.</span></span> <span data-ttu-id="3f2bc-111">Los desafíos de autenticación se pueden enviar en respuestas HTTP/2, pero el cliente debe degradar a HTTP/1.1 antes de la autenticación.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-111">Authentication challenges can be sent on HTTP/2 responses, but the client must downgrade to HTTP/1.1 before authenticating.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="3f2bc-112">Escenarios de proxy y equilibrador de carga</span><span class="sxs-lookup"><span data-stu-id="3f2bc-112">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="3f2bc-113">La autenticación de Windows es un escenario con estado que se usa principalmente en una intranet, donde un proxy o un equilibrador de carga normalmente controla el tráfico entre clientes y servidores.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-113">Windows Authentication is a stateful scenario primarily used in an intranet, where a proxy or load balancer doesn't usually handle traffic between clients and servers.</span></span> <span data-ttu-id="3f2bc-114">Si se usa un proxy o un equilibrador de carga, la autenticación de Windows solo funciona si el proxy o el equilibrador de carga:</span><span class="sxs-lookup"><span data-stu-id="3f2bc-114">If a proxy or load balancer is used, Windows Authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="3f2bc-115">Controla la autenticación.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-115">Handles the authentication.</span></span>
* <span data-ttu-id="3f2bc-116">Pasa la información de autenticación del usuario a la aplicación (por ejemplo, en un encabezado de solicitud), que actúa en la información de autenticación.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-116">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="3f2bc-117">Una alternativa a la autenticación de Windows en entornos en los que se usan servidores proxy y equilibradores de carga es Active Directory Federated Services (ADFS) con OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="3f2bc-117">An alternative to Windows Authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="iisiis-express"></a><span data-ttu-id="3f2bc-118">IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="3f2bc-118">IIS/IIS Express</span></span>

<span data-ttu-id="3f2bc-119">Agregar servicios de autenticación mediante la invocación <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> de (espacio de nombres) en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3f2bc-119">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a><span data-ttu-id="3f2bc-120">Configuración de inicio (depurador)</span><span class="sxs-lookup"><span data-stu-id="3f2bc-120">Launch settings (debugger)</span></span>

<span data-ttu-id="3f2bc-121">La configuración de la configuración de inicio solo afecta a las *propiedades/launchSettings.jsdel* archivo para IIS Express y no configura IIS para la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-121">Configuration for launch settings only affects the *Properties/launchSettings.json* file for IIS Express and doesn't configure IIS for Windows Authentication.</span></span> <span data-ttu-id="3f2bc-122">La configuración del servidor se explica en la sección [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="3f2bc-122">Server configuration is explained in the [IIS](#iis) section.</span></span>

<span data-ttu-id="3f2bc-123">La plantilla de **aplicación web** disponible a través de Visual Studio o el CLI de .net Core puede configurarse para admitir la autenticación de Windows, que actualiza las *propiedades o el launchSettings.jsen* el archivo automáticamente.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-123">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication, which updates the *Properties/launchSettings.json* file automatically.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3f2bc-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f2bc-124">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3f2bc-125">**Nuevo proyecto**</span><span class="sxs-lookup"><span data-stu-id="3f2bc-125">**New project**</span></span>

1. <span data-ttu-id="3f2bc-126">Cree un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-126">Create a new project.</span></span>
1. <span data-ttu-id="3f2bc-127">Seleccione **Aplicación web de ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-127">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="3f2bc-128">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-128">Select **Next**.</span></span>
1. <span data-ttu-id="3f2bc-129">Proporcione un nombre en el campo **nombre del proyecto** .</span><span class="sxs-lookup"><span data-stu-id="3f2bc-129">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="3f2bc-130">Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-130">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="3f2bc-131">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-131">Select **Create**.</span></span>
1. <span data-ttu-id="3f2bc-132">Seleccione **cambiar** en **autenticación**.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-132">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="3f2bc-133">En la ventana **cambiar autenticación** , seleccione **autenticación de Windows**.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-133">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="3f2bc-134">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-134">Select **OK**.</span></span>
1. <span data-ttu-id="3f2bc-135">Seleccione **Aplicación web**.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-135">Select **Web Application**.</span></span>
1. <span data-ttu-id="3f2bc-136">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-136">Select **Create**.</span></span>

<span data-ttu-id="3f2bc-137">Ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-137">Run the app.</span></span> <span data-ttu-id="3f2bc-138">El nombre de usuario aparece en la interfaz de usuario de la aplicación representada.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-138">The username appears in the rendered app's user interface.</span></span>

<span data-ttu-id="3f2bc-139">**Proyecto existente**</span><span class="sxs-lookup"><span data-stu-id="3f2bc-139">**Existing project**</span></span>

<span data-ttu-id="3f2bc-140">Las propiedades del proyecto habilitan la autenticación de Windows y deshabilitan la autenticación anónima:</span><span class="sxs-lookup"><span data-stu-id="3f2bc-140">The project's properties enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="3f2bc-141">Haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y seleccione **Propiedades**.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-141">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="3f2bc-142">Seleccione la pestaña **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-142">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="3f2bc-143">Desactive la casilla **Habilitar autenticación anónima**.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-143">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="3f2bc-144">Active la casilla **Habilitar autenticación de Windows**.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-144">Select the check box for **Enable Windows Authentication**.</span></span>
1. <span data-ttu-id="3f2bc-145">Guarde y cierre la página de propiedades.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-145">Save and close the property page.</span></span>

<span data-ttu-id="3f2bc-146">Como alternativa, las propiedades se pueden configurar en el `iisSettings` nodo del *launchSettings.jsen* el archivo:</span><span class="sxs-lookup"><span data-stu-id="3f2bc-146">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-cli"></a>[<span data-ttu-id="3f2bc-147">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="3f2bc-147">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3f2bc-148">**Nuevo proyecto**</span><span class="sxs-lookup"><span data-stu-id="3f2bc-148">**New project**</span></span>

<span data-ttu-id="3f2bc-149">Ejecute el comando [dotnet New](/dotnet/core/tools/dotnet-new) con el `webapp` argumento (ASP.net Core aplicación web) y el `--auth Windows` modificador:</span><span class="sxs-lookup"><span data-stu-id="3f2bc-149">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```dotnetcli
dotnet new webapp --auth Windows
```

<span data-ttu-id="3f2bc-150">**Proyecto existente**</span><span class="sxs-lookup"><span data-stu-id="3f2bc-150">**Existing project**</span></span>

<span data-ttu-id="3f2bc-151">Actualice el `iisSettings` nodo del *launchSettings.jsen* el archivo:</span><span class="sxs-lookup"><span data-stu-id="3f2bc-151">Update the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

<span data-ttu-id="3f2bc-152">Al modificar un proyecto existente, confirme que el archivo de proyecto incluye una referencia de paquete para el [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) **o** el paquete NuGet [Microsoft. AspNetCore. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="3f2bc-152">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

### <a name="iis"></a><span data-ttu-id="3f2bc-153">IIS</span><span class="sxs-lookup"><span data-stu-id="3f2bc-153">IIS</span></span>

<span data-ttu-id="3f2bc-154">IIS usa el [módulo ASP.net Core](xref:host-and-deploy/aspnet-core-module) para hospedar ASP.net Core aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-154">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="3f2bc-155">La autenticación de Windows está configurada para IIS a través del archivo de *web.config* .</span><span class="sxs-lookup"><span data-stu-id="3f2bc-155">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="3f2bc-156">En las secciones siguientes se muestra cómo:</span><span class="sxs-lookup"><span data-stu-id="3f2bc-156">The following sections show how to:</span></span>

* <span data-ttu-id="3f2bc-157">Proporcione un archivo de *web.config* local que active la autenticación de Windows en el servidor cuando se implemente la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-157">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="3f2bc-158">Use el administrador de IIS para configurar el archivo de *web.config* de una aplicación ASP.net Core que ya se ha implementado en el servidor.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-158">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

<span data-ttu-id="3f2bc-159">Si todavía no lo ha hecho, habilite IIS para hospedar ASP.NET Core aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-159">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="3f2bc-160">Para obtener más información, vea <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-160">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="3f2bc-161">Habilite el servicio de función IIS para la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-161">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="3f2bc-162">Para obtener más información, vea [Habilitar la autenticación de Windows en los servicios de rol de IIS (consulte el paso 2)](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="3f2bc-162">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="3f2bc-163">El [middleware de integración con IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) está configurado para autenticar solicitudes automáticamente de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-163">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="3f2bc-164">Para obtener más información, vea [Host ASP.net Core en Windows con IIS: opciones de IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span><span class="sxs-lookup"><span data-stu-id="3f2bc-164">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="3f2bc-165">El módulo ASP.NET Core está configurado para reenviar el token de autenticación de Windows a la aplicación de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-165">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="3f2bc-166">Para obtener más información, vea [referencia de configuración del módulo ASP.net Core: atributos del elemento aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="3f2bc-166">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

<span data-ttu-id="3f2bc-167">Use **cualquiera** de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="3f2bc-167">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="3f2bc-168">**Antes de publicar e implementar el proyecto,** agregue el siguiente archivo de *web.config* a la raíz del proyecto:</span><span class="sxs-lookup"><span data-stu-id="3f2bc-168">**Before publishing and deploying the project,** add the following *web.config* file to the project root:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  <span data-ttu-id="3f2bc-169">Cuando el SDK de .NET Core publica el proyecto (sin la `<IsTransformWebConfigDisabled>` propiedad establecida en `true` en el archivo de proyecto), el archivo de *web.config* publicado incluye la `<location><system.webServer><security><authentication>` sección.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-169">When the project is published by the .NET Core SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="3f2bc-170">Para obtener más información sobre la `<IsTransformWebConfigDisabled>` propiedad, vea <xref:host-and-deploy/iis/index#webconfig-file> .</span><span class="sxs-lookup"><span data-stu-id="3f2bc-170">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

* <span data-ttu-id="3f2bc-171">**Después de publicar e implementar el proyecto,** realice la configuración del servidor con el administrador de IIS:</span><span class="sxs-lookup"><span data-stu-id="3f2bc-171">**After publishing and deploying the project,** perform server-side configuration with the IIS Manager:</span></span>

  1. <span data-ttu-id="3f2bc-172">En el administrador de IIS, seleccione el sitio de IIS en el nodo **sitios** de la barra lateral **conexiones** .</span><span class="sxs-lookup"><span data-stu-id="3f2bc-172">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
  1. <span data-ttu-id="3f2bc-173">Haga doble clic en **autenticación** en el área **IIS** .</span><span class="sxs-lookup"><span data-stu-id="3f2bc-173">Double-click **Authentication** in the **IIS** area.</span></span>
  1. <span data-ttu-id="3f2bc-174">Seleccione **autenticación anónima**.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-174">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="3f2bc-175">Seleccione **deshabilitar** en la barra lateral de **acciones** .</span><span class="sxs-lookup"><span data-stu-id="3f2bc-175">Select **Disable** in the **Actions** sidebar.</span></span>
  1. <span data-ttu-id="3f2bc-176">Seleccione **Autenticación de Windows**.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-176">Select **Windows Authentication**.</span></span> <span data-ttu-id="3f2bc-177">Seleccione **Habilitar** en la barra lateral de **acciones** .</span><span class="sxs-lookup"><span data-stu-id="3f2bc-177">Select **Enable** in the **Actions** sidebar.</span></span>

  <span data-ttu-id="3f2bc-178">Cuando se realizan estas acciones, el administrador de IIS modifica el archivo de *web.config* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-178">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="3f2bc-179">`<system.webServer><security><authentication>`Se agrega un nodo con la configuración actualizada para `anonymousAuthentication` y `windowsAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="3f2bc-179">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  <span data-ttu-id="3f2bc-180">La `<system.webServer>` sección agregada al archivo *web.config* por el administrador de IIS está fuera de la sección de la aplicación `<location>` agregada por el SDK de .net Core cuando se publica la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-180">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="3f2bc-181">Dado que la sección se agrega fuera del `<location>` nodo, todas las [aplicaciones secundarias](xref:host-and-deploy/iis/index#sub-applications) de la aplicación actual heredan la configuración.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-181">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="3f2bc-182">Para evitar la herencia, mueva la `<security>` sección agregada dentro de la `<location><system.webServer>` sección que proporciona el SDK de .net Core.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-182">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the .NET Core SDK provided.</span></span>

  <span data-ttu-id="3f2bc-183">Cuando se utiliza el administrador de IIS para agregar la configuración de IIS, solo afecta al archivo de *web.config* de la aplicación en el servidor.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-183">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="3f2bc-184">Una implementación posterior de la aplicación puede sobrescribir la configuración del servidor si la copia del servidor de *web.config* se reemplaza por el archivo *web.config* del proyecto.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-184">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="3f2bc-185">Use **cualquiera** de los métodos siguientes para administrar la configuración:</span><span class="sxs-lookup"><span data-stu-id="3f2bc-185">Use **either** of the following approaches to manage the settings:</span></span>

  * <span data-ttu-id="3f2bc-186">Use el administrador de IIS para restablecer la configuración del archivo de *web.config* después de sobrescribir el archivo en la implementación.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-186">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
  * <span data-ttu-id="3f2bc-187">Agregue un *archivo deweb.config* a la aplicación localmente con la configuración.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-187">Add a *web.config file* to the app locally with the settings.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a><span data-ttu-id="3f2bc-188">Kestrel</span><span class="sxs-lookup"><span data-stu-id="3f2bc-188">Kestrel</span></span>

<span data-ttu-id="3f2bc-189">El paquete NuGet [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) se puede usar con [Kestrel](xref:fundamentals/servers/kestrel) para admitir la autenticación de Windows mediante Negotiate y Kerberos en Windows, Linux y MacOS.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-189">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet package can be used with [Kestrel](xref:fundamentals/servers/kestrel) to support Windows Authentication using Negotiate and Kerberos on Windows, Linux, and macOS.</span></span>

> [!WARNING]
> <span data-ttu-id="3f2bc-190">Las credenciales se pueden conservar entre las solicitudes de una conexión.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-190">Credentials can be persisted across requests on a connection.</span></span> <span data-ttu-id="3f2bc-191">*La autenticación Negotiate no debe usarse con servidores proxy a menos que el proxy mantenga una afinidad de conexión 1:1 (una conexión persistente) con Kestrel.*</span><span class="sxs-lookup"><span data-stu-id="3f2bc-191">*Negotiate authentication must not be used with proxies unless the proxy maintains a 1:1 connection affinity (a persistent connection) with Kestrel.*</span></span>

> [!NOTE]
> <span data-ttu-id="3f2bc-192">El controlador Negotiate detecta si el servidor subyacente admite la autenticación de Windows de forma nativa y si está habilitado.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-192">The Negotiate handler detects if the underlying server supports Windows Authentication natively and if it's enabled.</span></span> <span data-ttu-id="3f2bc-193">Si el servidor admite la autenticación de Windows pero está deshabilitado, se produce un error que le pide que habilite la implementación del servidor.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-193">If the server supports Windows Authentication but it's disabled, an error is thrown asking you to enable the server implementation.</span></span> <span data-ttu-id="3f2bc-194">Cuando la autenticación de Windows está habilitada en el servidor, el controlador de negociación lo reenvía de forma transparente.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-194">When Windows Authentication is enabled in the server, the Negotiate handler transparently forwards to it.</span></span>

<span data-ttu-id="3f2bc-195">Agregue servicios de autenticación invocando <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> y <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3f2bc-195">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> in `Startup.ConfigureServices`:</span></span>

 ```csharp
// using Microsoft.AspNetCore.Authentication.Negotiate;
// using Microsoft.Extensions.DependencyInjection;

services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

<span data-ttu-id="3f2bc-196">Agregue middleware de autenticación mediante una llamada a <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> en `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="3f2bc-196">Add Authentication Middleware by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> in `Startup.Configure`:</span></span>

 ```csharp
app.UseAuthentication();
```

<span data-ttu-id="3f2bc-197">Para obtener más información sobre middleware, vea <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="3f2bc-197">For more information on middleware, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="3f2bc-198">Se permiten las solicitudes anónimas.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-198">Anonymous requests are allowed.</span></span> <span data-ttu-id="3f2bc-199">Utilice [ASP.net Core autorización](xref:security/authorization/introduction) para desafiar las solicitudes anónimas de autenticación.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-199">Use [ASP.NET Core Authorization](xref:security/authorization/introduction) to challenge anonymous requests for authentication.</span></span>

### <a name="windows-environment-configuration"></a><span data-ttu-id="3f2bc-200">Configuración del entorno de Windows</span><span class="sxs-lookup"><span data-stu-id="3f2bc-200">Windows environment configuration</span></span>

<span data-ttu-id="3f2bc-201">El componente [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) realiza la autenticación de modo de usuario.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-201">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) component performs User Mode authentication.</span></span> <span data-ttu-id="3f2bc-202">Los nombres de entidad de seguridad de servicio (SPN) se deben agregar a la cuenta de usuario que ejecuta el servicio, no a la cuenta de equipo.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-202">Service Principal Names (SPNs) must be added to the user account running the service, not the machine account.</span></span> <span data-ttu-id="3f2bc-203">Ejecute `setspn -S HTTP/myservername.mydomain.com myuser` en un shell de comandos administrativo.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-203">Execute `setspn -S HTTP/myservername.mydomain.com myuser` in an administrative command shell.</span></span>

### <a name="linux-and-macos-environment-configuration"></a><span data-ttu-id="3f2bc-204">Configuración del entorno de Linux y macOS</span><span class="sxs-lookup"><span data-stu-id="3f2bc-204">Linux and macOS environment configuration</span></span>

<span data-ttu-id="3f2bc-205">Las instrucciones para unir una máquina Linux o macOS a un dominio de Windows están disponibles en el artículo [conexión Azure Data Studio a la SQL Server mediante la autenticación de Windows: Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) .</span><span class="sxs-lookup"><span data-stu-id="3f2bc-205">Instructions for joining a Linux or macOS machine to a Windows domain are available in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article.</span></span> <span data-ttu-id="3f2bc-206">Las instrucciones crean una cuenta de equipo para la máquina Linux en el dominio.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-206">The instructions create a machine account for the Linux machine on the domain.</span></span> <span data-ttu-id="3f2bc-207">Los SPN se deben agregar a esa cuenta de equipo.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-207">SPNs must be added to that machine account.</span></span>

> [!NOTE]
> <span data-ttu-id="3f2bc-208">Cuando siga las instrucciones que se indican en el artículo [conexión de Azure Data Studio al SQL Server mediante la autenticación de Windows: Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) , reemplace `python-software-properties` por `python3-software-properties` si es necesario.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-208">When following the guidance in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article, replace `python-software-properties` with `python3-software-properties` if needed.</span></span>

<span data-ttu-id="3f2bc-209">Una vez que la máquina Linux o macOS está unida al dominio, es necesario realizar pasos adicionales para proporcionar un [archivo de fichas de claves](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) con los SPN:</span><span class="sxs-lookup"><span data-stu-id="3f2bc-209">Once the Linux or macOS machine is joined to the domain, additional steps are required to provide a [keytab file](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) with the SPNs:</span></span>

* <span data-ttu-id="3f2bc-210">En el controlador de dominio, agregue nuevos SPN de servicio Web a la cuenta del equipo:</span><span class="sxs-lookup"><span data-stu-id="3f2bc-210">On the domain controller, add new web service SPNs to the machine account:</span></span>
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* <span data-ttu-id="3f2bc-211">Use [ktpass](/windows-server/administration/windows-commands/ktpass) para generar un archivo de la fichas de claves:</span><span class="sxs-lookup"><span data-stu-id="3f2bc-211">Use [ktpass](/windows-server/administration/windows-commands/ktpass) to generate a keytab file:</span></span>
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * <span data-ttu-id="3f2bc-212">Algunos campos deben especificarse en mayúsculas como se indica.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-212">Some fields must be specified in uppercase as indicated.</span></span>
* <span data-ttu-id="3f2bc-213">Copie el archivo de la fichas de claves en el equipo Linux o macOS.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-213">Copy the keytab file to the Linux or macOS machine.</span></span>
* <span data-ttu-id="3f2bc-214">Seleccione el archivo de la información de claves a través de una variable de entorno:`export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span><span class="sxs-lookup"><span data-stu-id="3f2bc-214">Select the keytab file via an environment variable: `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span></span>
* <span data-ttu-id="3f2bc-215">Invoque `klist` para mostrar los SPN actualmente disponibles para su uso.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-215">Invoke `klist` to show the SPNs currently available for use.</span></span>

> [!NOTE]
> <span data-ttu-id="3f2bc-216">Un archivo de fichas de claves contiene credenciales de acceso de dominio y debe protegerse en consecuencia.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-216">A keytab file contains domain access credentials and must be protected accordingly.</span></span>

::: moniker-end

## <a name="httpsys"></a><span data-ttu-id="3f2bc-217">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="3f2bc-217">HTTP.sys</span></span>

<span data-ttu-id="3f2bc-218">[HTTP.sys](xref:fundamentals/servers/httpsys) admite la autenticación de Windows en modo kernel mediante la autenticación Negotiate, NTLM o básica.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-218">[HTTP.sys](xref:fundamentals/servers/httpsys) supports Kernel Mode Windows Authentication using Negotiate, NTLM, or Basic authentication.</span></span>

<span data-ttu-id="3f2bc-219">Agregar servicios de autenticación mediante la invocación <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> de (espacio de nombres) en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3f2bc-219">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

<span data-ttu-id="3f2bc-220">Configure el host Web de la aplicación para usar HTTP.sys con la autenticación de Windows (*Program.CS*).</span><span class="sxs-lookup"><span data-stu-id="3f2bc-220">Configure the app's web host to use HTTP.sys with Windows Authentication (*Program.cs*).</span></span> <span data-ttu-id="3f2bc-221"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>está en el <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-221"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> is in the <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="3f2bc-222">HTTP.sys delega en la autenticación de modo kernel con el protocolo de autenticación de Kerberos.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-222">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="3f2bc-223">La autenticación de modo usuario no se admite con Kerberos y HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-223">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="3f2bc-224">Se debe usar la cuenta de equipo para descifrar el token o el vale de Kerberos que se obtiene de Active Directory y que el cliente reenvía al servidor para autenticar al usuario.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-224">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="3f2bc-225">Registre el nombre de entidad de seguridad de servicio (SPN) para el host, no el usuario de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-225">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="3f2bc-226">HTTP.sys no se admite en la versión 1709 o posterior de nano Server.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-226">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="3f2bc-227">Para usar la autenticación de Windows y HTTP.sys con nano Server, use un [contenedor Server Core (Microsoft/windowsservercore)](https://hub.docker.com/r/microsoft/windowsservercore/).</span><span class="sxs-lookup"><span data-stu-id="3f2bc-227">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="3f2bc-228">Para obtener más información sobre Server Core, vea [¿Qué es la opción de instalación Server Core en Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span><span class="sxs-lookup"><span data-stu-id="3f2bc-228">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="authorize-users"></a><span data-ttu-id="3f2bc-229">Autorizar a usuarios</span><span class="sxs-lookup"><span data-stu-id="3f2bc-229">Authorize users</span></span>

<span data-ttu-id="3f2bc-230">El estado de configuración de acceso anónimo determina el modo en que `[Authorize]` `[AllowAnonymous]` se usan los atributos y en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-230">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="3f2bc-231">En las dos secciones siguientes se explica cómo administrar los Estados de configuración no permitidos y permitidos de acceso anónimo.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-231">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="3f2bc-232">No permitir el acceso anónimo</span><span class="sxs-lookup"><span data-stu-id="3f2bc-232">Disallow anonymous access</span></span>

<span data-ttu-id="3f2bc-233">Cuando se habilita la autenticación de Windows y se deshabilita el acceso anónimo, los `[Authorize]` `[AllowAnonymous]` atributos y no tienen ningún efecto.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-233">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="3f2bc-234">Si un sitio de IIS está configurado para no permitir el acceso anónimo, la solicitud nunca llega a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-234">If an IIS site is configured to disallow anonymous access, the request never reaches the app.</span></span> <span data-ttu-id="3f2bc-235">Por esta razón, el `[AllowAnonymous]` atributo no es aplicable.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-235">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="3f2bc-236">Permitir acceso anónimo</span><span class="sxs-lookup"><span data-stu-id="3f2bc-236">Allow anonymous access</span></span>

<span data-ttu-id="3f2bc-237">Cuando esté habilitada la autenticación de Windows y el acceso anónimo, utilice los `[Authorize]` `[AllowAnonymous]` atributos y.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-237">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="3f2bc-238">El `[Authorize]` atributo permite proteger los puntos de conexión de la aplicación que requieren autenticación.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-238">The `[Authorize]` attribute allows you to secure endpoints of the app which require authentication.</span></span> <span data-ttu-id="3f2bc-239">El `[AllowAnonymous]` atributo invalida el `[Authorize]` atributo en las aplicaciones que permiten el acceso anónimo.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-239">The `[AllowAnonymous]` attribute overrides the `[Authorize]` attribute in apps that allow anonymous access.</span></span> <span data-ttu-id="3f2bc-240">Para obtener información sobre el uso de atributos, vea <xref:security/authorization/simple> .</span><span class="sxs-lookup"><span data-stu-id="3f2bc-240">For attribute usage details, see <xref:security/authorization/simple>.</span></span>

> [!NOTE]
> <span data-ttu-id="3f2bc-241">De forma predeterminada, los usuarios que carecen de autorización para tener acceso a una página presentan una respuesta HTTP 403 vacía.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-241">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="3f2bc-242">El [middleware StatusCodePages](xref:fundamentals/error-handling#usestatuscodepages) puede configurarse para proporcionar a los usuarios una mejor experiencia de "acceso denegado".</span><span class="sxs-lookup"><span data-stu-id="3f2bc-242">The [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

## <a name="impersonation"></a><span data-ttu-id="3f2bc-243">Suplantación</span><span class="sxs-lookup"><span data-stu-id="3f2bc-243">Impersonation</span></span>

<span data-ttu-id="3f2bc-244">ASP.NET Core no implementa la suplantación.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-244">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="3f2bc-245">Las aplicaciones se ejecutan con la identidad de la aplicación para todas las solicitudes, mediante el grupo de aplicaciones o la identidad del proceso.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-245">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="3f2bc-246">Si la aplicación debe realizar una acción en nombre de un usuario, use [WindowsIdentity. RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) en un [middleware de terminal en línea](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="3f2bc-246">If the app should perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="3f2bc-247">Ejecute una sola acción en este contexto y, a continuación, cierre el contexto.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-247">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="3f2bc-248">`RunImpersonated`no es compatible con las operaciones asincrónicas y no debe usarse en escenarios complejos.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-248">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="3f2bc-249">Por ejemplo, no se admite el ajuste de solicitudes completas o cadenas de middleware.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-249">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3f2bc-250">Aunque el paquete [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) habilita la autenticación en Windows, Linux y MacOS, la suplantación solo se admite en Windows.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-250">While the [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package enables authentication on Windows, Linux, and macOS, impersonation is only supported on Windows.</span></span>

::: moniker-end

## <a name="claims-transformations"></a><span data-ttu-id="3f2bc-251">Transformaciones de notificaciones</span><span class="sxs-lookup"><span data-stu-id="3f2bc-251">Claims transformations</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3f2bc-252">Al hospedar con IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> no se llama a internamente para inicializar un usuario.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-252">When hosting with IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="3f2bc-253">Por tanto, se usa una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> para transformar las notificaciones después de que cada autenticación no se active de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-253">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="3f2bc-254">Para obtener más información y un ejemplo de código que activa las transformaciones de notificaciones, vea <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .</span><span class="sxs-lookup"><span data-stu-id="3f2bc-254">For more information and a code example that activates claims transformations, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3f2bc-255">Al hospedar con IIS en modo de proceso, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> no se llama a internamente para inicializar un usuario.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-255">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="3f2bc-256">Por tanto, se usa una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> para transformar las notificaciones después de que cada autenticación no se active de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="3f2bc-256">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="3f2bc-257">Para obtener más información y un ejemplo de código que active las transformaciones de notificaciones al hospedar en proceso, vea <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .</span><span class="sxs-lookup"><span data-stu-id="3f2bc-257">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3f2bc-258">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="3f2bc-258">Additional resources</span></span>

* [<span data-ttu-id="3f2bc-259">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="3f2bc-259">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
