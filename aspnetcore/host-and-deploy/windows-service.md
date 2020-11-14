---
title: Hospedaje de ASP.NET Core en un servicio de Windows
author: rick-anderson
description: Aprenda a hospedar una aplicación ASP.NET Core en un servicio de Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/windows-service
ms.openlocfilehash: 31a738e7aa8779171dfa09a5678d7240b8f62343
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057237"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="01674-103">Hospedaje de ASP.NET Core en un servicio de Windows</span><span class="sxs-lookup"><span data-stu-id="01674-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="01674-104">Una aplicación de ASP.NET Core se puede hospedar en Windows sin usar IIS como [servicio de Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications).</span><span class="sxs-lookup"><span data-stu-id="01674-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="01674-105">Cuando se hospeda como un servicio de Windows, la aplicación se inicia automáticamente después de reiniciar el servidor.</span><span class="sxs-lookup"><span data-stu-id="01674-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="01674-106">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="01674-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="01674-107">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="01674-107">Prerequisites</span></span>

* [<span data-ttu-id="01674-108">ASP.NET Core SDK 2.1 o posterior</span><span class="sxs-lookup"><span data-stu-id="01674-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="01674-109">PowerShell 6.2 o posterior</span><span class="sxs-lookup"><span data-stu-id="01674-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="01674-110">Plantilla Worker Service</span><span class="sxs-lookup"><span data-stu-id="01674-110">Worker Service template</span></span>

<span data-ttu-id="01674-111">La plantilla Worker Service de ASP.NET Core sirve de punto de partida para escribir aplicaciones de servicio de larga duración.</span><span class="sxs-lookup"><span data-stu-id="01674-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="01674-112">Para usar la plantilla como base de una aplicación de servicio de Windows:</span><span class="sxs-lookup"><span data-stu-id="01674-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="01674-113">Cree una aplicación Worker Service con la plantilla de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="01674-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="01674-114">Siga las instrucciones de la sección [Configuración de aplicaciones](#app-configuration) para actualizar la aplicación Worker Service a fin de que se ejecute como un servicio de Windows.</span><span class="sxs-lookup"><span data-stu-id="01674-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="01674-115">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="01674-115">App configuration</span></span>

<span data-ttu-id="01674-116">La aplicación requiere una referencia de paquete para [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="01674-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="01674-117">Se llama a `IHostBuilder.UseWindowsService` al compilar el host.</span><span class="sxs-lookup"><span data-stu-id="01674-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="01674-118">Si la aplicación se ejecuta como un servicio de Windows, el método:</span><span class="sxs-lookup"><span data-stu-id="01674-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="01674-119">Establece la vigencia del host en `WindowsServiceLifetime`.</span><span class="sxs-lookup"><span data-stu-id="01674-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="01674-120">Establece la [raíz del contenido](xref:fundamentals/index#content-root) en [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="01674-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="01674-121">Para obtener más información, consulte la sección [Directorio actual y raíz del contenido](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="01674-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="01674-122">Habilita el registro en el registro de eventos:</span><span class="sxs-lookup"><span data-stu-id="01674-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="01674-123">El nombre de la aplicación se usa como nombre de origen predeterminado.</span><span class="sxs-lookup"><span data-stu-id="01674-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="01674-124">El nivel de registro predeterminado es *Advertencia* o superior para una aplicación basada en una plantilla de ASP.NET Core que llama a `CreateDefaultBuilder` con el fin de compilar el host.</span><span class="sxs-lookup"><span data-stu-id="01674-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="01674-125">Invalide el nivel de registro predeterminado con la clave `Logging:EventLog:LogLevel:Default` en *appsettings.json* /*appsettings.{Environment}.json* u otro proveedor de configuración.</span><span class="sxs-lookup"><span data-stu-id="01674-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="01674-126">Los administradores son los únicos que pueden crear nuevos orígenes de eventos.</span><span class="sxs-lookup"><span data-stu-id="01674-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="01674-127">Cuando no se puede crear un origen de eventos con el nombre de la aplicación, se registra una advertencia para el origen *Aplicación* y los registros de eventos se deshabilitan.</span><span class="sxs-lookup"><span data-stu-id="01674-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="01674-128">En `CreateHostBuilder` de *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="01674-128">In `CreateHostBuilder` of *Program.cs* :</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="01674-129">Las aplicaciones de ejemplo siguientes acompañan a este tema:</span><span class="sxs-lookup"><span data-stu-id="01674-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="01674-130">Ejemplo de Background Worker Service: un ejemplo de una aplicación que no es para la Web basado en la [plantilla Worker Service](#worker-service-template) que usa [servicios hospedados](xref:fundamentals/host/hosted-services) para las tareas en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="01674-130">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="01674-131">Ejemplo de App Service web: un ejemplo de aplicación web de Razor Pages que se ejecuta como un servicio de Windows con [servicios hospedados](xref:fundamentals/host/hosted-services) para las tareas en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="01674-131">Web App Service Sample: A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="01674-132">Para obtener instrucciones sobre MVC, vea los artículos en <xref:mvc/overview> y <xref:migration/22-to-30>.</span><span class="sxs-lookup"><span data-stu-id="01674-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="01674-133">Tipo de implementación</span><span class="sxs-lookup"><span data-stu-id="01674-133">Deployment type</span></span>

<span data-ttu-id="01674-134">Para obtener información y consejos sobre los escenarios de implementación, consulte [Implementación de aplicaciones .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="01674-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="01674-135">SDK</span><span class="sxs-lookup"><span data-stu-id="01674-135">SDK</span></span>

<span data-ttu-id="01674-136">En el caso de un servicio basado en una aplicación web que use los marcos Razor Pages o MVC, especifique el SDK web en el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="01674-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="01674-137">Si el servicio solo ejecuta tareas en segundo plano (por ejemplo, [servicios hospedados](xref:fundamentals/host/hosted-services)), especifique el SDK de Worker en el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="01674-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="01674-138">Implementación dependiente de marco (FDD)</span><span class="sxs-lookup"><span data-stu-id="01674-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="01674-139">La implementación dependiente de marco de trabajo (FDD) se basa en la presencia de una versión compartida de .NET Core en todo el sistema en el sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="01674-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="01674-140">Cuando se adopta el escenario FDD siguiendo las instrucciones de este artículo, el SDK genera un archivo ejecutable ( *.exe* ), denominado *ejecutable dependiente del marco*.</span><span class="sxs-lookup"><span data-stu-id="01674-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable ( *.exe* ), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="01674-141">Si se usa el [SDK web](#sdk), para una aplicación de Windows Services no se requiere un archivo *web.config* , que normalmente se crea cuando se publica una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="01674-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="01674-142">Para deshabilitar la creación de un archivo *web.config* agregue la propiedad `<IsTransformWebConfigDisabled>` establecida en `true`.</span><span class="sxs-lookup"><span data-stu-id="01674-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="01674-143">Implementación autocontenida (SCD)</span><span class="sxs-lookup"><span data-stu-id="01674-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="01674-144">Una implementación autocontenida (SCD) no depende de la presencia de un marco compartido en el sistema host.</span><span class="sxs-lookup"><span data-stu-id="01674-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="01674-145">El tiempo de ejecución y las dependencias de la aplicación se implementan con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="01674-146">Un [identificador en tiempo de ejecución (RID)](/dotnet/core/rid-catalog) se incluye en el `<PropertyGroup>` que contiene la plataforma de destino:</span><span class="sxs-lookup"><span data-stu-id="01674-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="01674-147">Para publicar para varios RID:</span><span class="sxs-lookup"><span data-stu-id="01674-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="01674-148">Proporcione los RID en una lista delimitada por punto y coma.</span><span class="sxs-lookup"><span data-stu-id="01674-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="01674-149">Use el nombre de la propiedad [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="01674-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="01674-150">Para más información, vea el [Catálogo de identificadores de entorno de ejecución (RID) de .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="01674-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="01674-151">Cuenta de usuario de servicio</span><span class="sxs-lookup"><span data-stu-id="01674-151">Service user account</span></span>

<span data-ttu-id="01674-152">Para crear una cuenta de usuario para un servicio, use el cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de un shell de comandos administrativos de PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="01674-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="01674-153">En la actualización de octubre de 2018 de Windows 10 (versión 1809/compilación 10.0.17763) o posterior:</span><span class="sxs-lookup"><span data-stu-id="01674-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="01674-154">En el sistema operativo Windows anterior a la actualización de octubre de 2018 de Windows 10 (versión 1809/compilación 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="01674-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="01674-155">Proporcione una [contraseña segura](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) cuando se le solicite.</span><span class="sxs-lookup"><span data-stu-id="01674-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="01674-156">A menos que el parámetro `-AccountExpires` se proporcione para el cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) con una fecha de expiración <xref:System.DateTime>, la cuenta no expirará.</span><span class="sxs-lookup"><span data-stu-id="01674-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="01674-157">Para más información, vea [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) y [Service User Accounts](/windows/desktop/services/service-user-accounts) (Cuentas de usuario del servicio).</span><span class="sxs-lookup"><span data-stu-id="01674-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="01674-158">Un enfoque alternativo de administración de usuarios al usar Active Directory consiste en usar cuentas de servicio administradas.</span><span class="sxs-lookup"><span data-stu-id="01674-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="01674-159">Para obtener más información, consulte [grupo Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) (Información general sobre cuentas de servicio administradas de grupo).</span><span class="sxs-lookup"><span data-stu-id="01674-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="01674-160">Derechos para iniciar sesión como servicio</span><span class="sxs-lookup"><span data-stu-id="01674-160">Log on as a service rights</span></span>

<span data-ttu-id="01674-161">Para establecer los derechos de *Iniciar sesión como servicio* para una cuenta de usuario del servicio:</span><span class="sxs-lookup"><span data-stu-id="01674-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="01674-162">Abra el editor de la Directiva de seguridad local mediante la ejecución de *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="01674-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="01674-163">Expanda el nodo **Directivas locales** y, después, seleccione **Asignación de derechos de usuario**.</span><span class="sxs-lookup"><span data-stu-id="01674-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="01674-164">Abra la directiva **Iniciar sesión como servicio**.</span><span class="sxs-lookup"><span data-stu-id="01674-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="01674-165">Seleccione **Agregar usuario o grupo**.</span><span class="sxs-lookup"><span data-stu-id="01674-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="01674-166">Proporcione el nombre de objeto (cuenta de usuario) mediante cualquiera de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="01674-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="01674-167">Escriba la cuenta de usuario (`{DOMAIN OR COMPUTER NAME\USER}`) en el campo del nombre de objeto y seleccione **Aceptar** para agregar el usuario a la directiva.</span><span class="sxs-lookup"><span data-stu-id="01674-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="01674-168">Seleccione **Opciones avanzadas**.</span><span class="sxs-lookup"><span data-stu-id="01674-168">Select **Advanced**.</span></span> <span data-ttu-id="01674-169">Seleccione **Buscar ahora**.</span><span class="sxs-lookup"><span data-stu-id="01674-169">Select **Find Now**.</span></span> <span data-ttu-id="01674-170">Seleccione la cuenta de usuario de la lista.</span><span class="sxs-lookup"><span data-stu-id="01674-170">Select the user account from the list.</span></span> <span data-ttu-id="01674-171">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="01674-171">Select **OK**.</span></span> <span data-ttu-id="01674-172">Seleccione **Aceptar** nuevo para agregar el usuario a la directiva.</span><span class="sxs-lookup"><span data-stu-id="01674-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="01674-173">Seleccione **Aceptar** o **Aplicar** para aceptar los cambios.</span><span class="sxs-lookup"><span data-stu-id="01674-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="01674-174">Creación y administración del servicio de Windows</span><span class="sxs-lookup"><span data-stu-id="01674-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="01674-175">Creación de un servicio</span><span class="sxs-lookup"><span data-stu-id="01674-175">Create a service</span></span>

<span data-ttu-id="01674-176">Use los comandos de PowerShell para registrar un servicio.</span><span class="sxs-lookup"><span data-stu-id="01674-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="01674-177">Desde un shell de comandos administrativos de PowerShell 6, ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="01674-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = "{DOMAIN OR COMPUTER NAME\USER}", "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName "{EXE FILE PATH}" -Credential "{DOMAIN OR COMPUTER NAME\USER}" -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="01674-178">`{EXE PATH}`: ruta de acceso a la carpeta de la aplicación en el host (por ejemplo, `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="01674-178">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="01674-179">No incluya el archivo ejecutable de la aplicación en la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="01674-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="01674-180">No se requiere una barra diagonal al final.</span><span class="sxs-lookup"><span data-stu-id="01674-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="01674-181">`{DOMAIN OR COMPUTER NAME\USER}`: cuenta de usuario de servicio (por ejemplo, `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="01674-181">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="01674-182">`{SERVICE NAME}`: nombre de servicio (por ejemplo, `MyService`).</span><span class="sxs-lookup"><span data-stu-id="01674-182">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="01674-183">`{EXE FILE PATH}`: ruta de acceso ejecutable de la aplicación (por ejemplo, `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="01674-183">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="01674-184">Incluya el nombre de archivo del ejecutable con la extensión.</span><span class="sxs-lookup"><span data-stu-id="01674-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="01674-185">`{DESCRIPTION}`: descripción del servicio (por ejemplo, `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="01674-185">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="01674-186">`{DISPLAY NAME}`: nombre para mostrar del servicio (por ejemplo, `My Service`).</span><span class="sxs-lookup"><span data-stu-id="01674-186">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="01674-187">iniciar un servicio.</span><span class="sxs-lookup"><span data-stu-id="01674-187">Start a service</span></span>

<span data-ttu-id="01674-188">Inicie el servicio con el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="01674-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="01674-189">Este comando tarda unos segundos en iniciar el servicio.</span><span class="sxs-lookup"><span data-stu-id="01674-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="01674-190">Determinación del estado de un servicio</span><span class="sxs-lookup"><span data-stu-id="01674-190">Determine a service's status</span></span>

<span data-ttu-id="01674-191">Para comprobar el estado de un servicio, use el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="01674-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="01674-192">El estado se notifica como uno de los siguientes valores:</span><span class="sxs-lookup"><span data-stu-id="01674-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="01674-193">Detención de un servicio</span><span class="sxs-lookup"><span data-stu-id="01674-193">Stop a service</span></span>

<span data-ttu-id="01674-194">Detenga un servicio con el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="01674-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="01674-195">Eliminación de un servicio</span><span class="sxs-lookup"><span data-stu-id="01674-195">Remove a service</span></span>

<span data-ttu-id="01674-196">Tras un breve intervalo para detener un servicio, elimine el servicio con el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="01674-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="01674-197">Escenarios de servidor proxy y equilibrador de carga</span><span class="sxs-lookup"><span data-stu-id="01674-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="01674-198">Los servicios que interactúan con las solicitudes de Internet o de una red corporativa y están detrás de un proxy o de un equilibrador de carga podrían requerir configuración adicional.</span><span class="sxs-lookup"><span data-stu-id="01674-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="01674-199">Para obtener más información, vea <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="01674-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="01674-200">Configuración de puntos de conexión</span><span class="sxs-lookup"><span data-stu-id="01674-200">Configure endpoints</span></span>

<span data-ttu-id="01674-201">ASP.NET Core se enlaza a `http://localhost:5000` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="01674-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="01674-202">Configure la dirección URL y el puerto estableciendo la variable de entorno `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="01674-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="01674-203">Para información sobre otros enfoques de configuración de direcciones URL y puertos, consulte el artículo en cuestión:</span><span class="sxs-lookup"><span data-stu-id="01674-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="01674-204">En la guía anterior se trata la compatibilidad con los puntos de conexión HTTPS.</span><span class="sxs-lookup"><span data-stu-id="01674-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="01674-205">Por ejemplo, configure la aplicación para HTTPS cuando se use la autenticación con un servicio de Windows.</span><span class="sxs-lookup"><span data-stu-id="01674-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="01674-206">No se admite el uso del certificado de desarrollo HTTPS de ASP.NET Core para proteger un punto de conexión de servicio.</span><span class="sxs-lookup"><span data-stu-id="01674-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="01674-207">Directorio actual y raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="01674-207">Current directory and content root</span></span>

<span data-ttu-id="01674-208">El directorio de trabajo actual devuelto por una llamada a <xref:System.IO.Directory.GetCurrentDirectory*> para un servicio de Windows es la carpeta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="01674-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="01674-209">La carpeta *system32* no es una ubicación adecuada para almacenar los archivos de un servicio (por ejemplo los archivos de configuración).</span><span class="sxs-lookup"><span data-stu-id="01674-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="01674-210">Utilice uno de los siguientes enfoques para mantener los archivos de configuración y los activos de un servicio, así como para acceder a ellos.</span><span class="sxs-lookup"><span data-stu-id="01674-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="01674-211">Uso de ContentRootPath o ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="01674-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="01674-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) o <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> para buscar los recursos de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="01674-213">Cuando la aplicación se ejecuta como un servicio, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> establece la ruta de acceso <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> en [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="01674-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="01674-214">Los archivos de configuración predeterminados de la aplicación, *appsettings.json* y *appsettings.{Environment}.json* , se cargan desde la raíz del contenido de la aplicación mediante una llamada a [CreateDefaultBuilder durante la construcción del host](xref:fundamentals/host/generic-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="01674-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json* , are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="01674-215">En el caso de otros archivos de configuración cargados por el código para desarrolladores en <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, no es necesario llamar a <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="01674-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="01674-216">En el ejemplo siguiente, el archivo *custom_settings.json* ya se encuentra en la raíz del contenido de la aplicación y se carga sin establecer explícitamente una ruta de acceso base:</span><span class="sxs-lookup"><span data-stu-id="01674-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="01674-217">No intente usar <xref:System.IO.Directory.GetCurrentDirectory*> para obtener una ruta de acceso a recursos porque una aplicación de servicio de Windows devuelve la carpeta *C:\\WINDOWS\\system32* como su directorio actual.</span><span class="sxs-lookup"><span data-stu-id="01674-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="01674-218">Almacenamiento de los archivos de un servicio en una ubicación adecuada en el disco</span><span class="sxs-lookup"><span data-stu-id="01674-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="01674-219">Especifique una ruta de acceso absoluta con <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> al utilizar un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> a la carpeta que contiene los archivos.</span><span class="sxs-lookup"><span data-stu-id="01674-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="01674-220">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="01674-220">Troubleshoot</span></span>

<span data-ttu-id="01674-221">Para solucionar problemas de una aplicación de servicio de Windows, consulte <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="01674-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="01674-222">Errores comunes</span><span class="sxs-lookup"><span data-stu-id="01674-222">Common errors</span></span>

* <span data-ttu-id="01674-223">Se está usando una versión preliminar o antigua de PowerShell.</span><span class="sxs-lookup"><span data-stu-id="01674-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="01674-224">El servicio registrado no usa la salida **publicada** de la aplicación del comando [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="01674-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="01674-225">La salida del comando [dotnet build](/dotnet/core/tools/dotnet-build) no es compatible con la implementación de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="01674-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="01674-226">Los recursos publicados se encuentran en cualquiera de las siguientes carpetas, según el tipo de implementación:</span><span class="sxs-lookup"><span data-stu-id="01674-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="01674-227">*bin/Release/{PLATAFORMA DE DESTINO}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="01674-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="01674-228">*bin/Release/{PLATAFORMA DE DESTINO}/{IDENTIFICADOR DE RUNTIME}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="01674-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="01674-229">El servicio no está en el estado EN EJECUCIÓN.</span><span class="sxs-lookup"><span data-stu-id="01674-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="01674-230">Las rutas de acceso a los recursos que usa la aplicación (por ejemplo, certificados) son incorrectas.</span><span class="sxs-lookup"><span data-stu-id="01674-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="01674-231">La ruta de acceso base de un servicio de Windows es *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="01674-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="01674-232">El usuario no tiene derechos de *inicio de sesión como servicio*.</span><span class="sxs-lookup"><span data-stu-id="01674-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="01674-233">La contraseña del usuario ha expirado o se ha pasado incorrectamente al ejecutar el comando de PowerShell `New-Service`.</span><span class="sxs-lookup"><span data-stu-id="01674-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="01674-234">La aplicación requiere autenticación ASP.NET Core, pero no está configurada para conexiones seguras (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="01674-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="01674-235">El puerto de la dirección URL de solicitud es incorrecto o no está configurado correctamente en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="01674-236">Registros de eventos del sistema y de aplicación</span><span class="sxs-lookup"><span data-stu-id="01674-236">System and Application Event Logs</span></span>

<span data-ttu-id="01674-237">Acceda a los registros de eventos del sistema y de aplicación:</span><span class="sxs-lookup"><span data-stu-id="01674-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="01674-238">Abra el menú Inicio, busque *Visor de eventos* y seleccione la aplicación **Visor de eventos**.</span><span class="sxs-lookup"><span data-stu-id="01674-238">Open the Start menu, search for *Event Viewer* , and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="01674-239">En **Visor de eventos** , abra el nodo **Registros de Windows**.</span><span class="sxs-lookup"><span data-stu-id="01674-239">In **Event Viewer** , open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="01674-240">Seleccione **Sistema** para abrir el registro de eventos del sistema.</span><span class="sxs-lookup"><span data-stu-id="01674-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="01674-241">Seleccione **Aplicación** para abrir el registro de eventos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="01674-242">Busque los errores asociados a la aplicación objeto del error.</span><span class="sxs-lookup"><span data-stu-id="01674-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="01674-243">Ejecución de la aplicación en un símbolo del sistema</span><span class="sxs-lookup"><span data-stu-id="01674-243">Run the app at a command prompt</span></span>

<span data-ttu-id="01674-244">Muchos errores de inicio no generan información útil en el registro de eventos.</span><span class="sxs-lookup"><span data-stu-id="01674-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="01674-245">La causa de algunos errores se puede encontrar mediante la ejecución de la aplicación en un símbolo del sistema en el sistema de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="01674-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="01674-246">Para registrar detalles adicionales de la aplicación, reduzca el [nivel de registro](xref:fundamentals/logging/index#log-level) o ejecute la aplicación en el [entorno de desarrollo](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="01674-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="01674-247">Borrado de memorias caché de paquetes</span><span class="sxs-lookup"><span data-stu-id="01674-247">Clear package caches</span></span>

<span data-ttu-id="01674-248">Una aplicación en funcionamiento deja de ejecutarse inmediatamente después de actualizar el SDK de .NET Core en la máquina de desarrollo o de cambiar las versiones del paquete en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="01674-249">En algunos casos, los paquetes incoherentes pueden interrumpir una aplicación al realizar actualizaciones importantes.</span><span class="sxs-lookup"><span data-stu-id="01674-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="01674-250">La mayoría de estos problemas puede corregirse siguiendo estas instrucciones:</span><span class="sxs-lookup"><span data-stu-id="01674-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="01674-251">Elimine las carpetas *bin* y *obj*.</span><span class="sxs-lookup"><span data-stu-id="01674-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="01674-252">Borre las memorias caché del paquete ejecutando [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) desde un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="01674-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="01674-253">Otra manera de borrar las memorias caché de paquetes es usando la herramienta [nuget.exe](https://www.nuget.org/downloads) y ejecutando el comando `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="01674-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="01674-254">*nuget.exe* no es una instalación agrupada con el sistema operativo de escritorio de Windows y se debe obtener de forma independiente en el [sitio web de NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="01674-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="01674-255">Restaure el proyecto y vuelva a compilarlo.</span><span class="sxs-lookup"><span data-stu-id="01674-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="01674-256">Elimine todos los archivos de la carpeta de implementación del servidor antes de volver a implementar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="01674-257">Aplicación lenta o bloqueada</span><span class="sxs-lookup"><span data-stu-id="01674-257">Slow or hanging app</span></span>

<span data-ttu-id="01674-258">Un *volcado de memoria* es una instantánea de la memoria del sistema que puede ayudar a determinar la causa de un bloqueo de la aplicación, un error de inicio o una aplicación lenta.</span><span class="sxs-lookup"><span data-stu-id="01674-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="01674-259">Bloqueo o excepción de la aplicación</span><span class="sxs-lookup"><span data-stu-id="01674-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="01674-260">Obtenga y analice un volcado de memoria en [Informe de errores de Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="01674-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="01674-261">Cree una carpeta para almacenar los archivos de volcado de memoria en `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="01674-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="01674-262">Ejecute el script [EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) con el nombre del archivo ejecutable de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="01674-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="01674-263">Ejecute la aplicación en las condiciones que hacen que se produzca el bloqueo.</span><span class="sxs-lookup"><span data-stu-id="01674-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="01674-264">Una vez que se haya producido el bloqueo, ejecute el [script DisableDumps de PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="01674-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="01674-265">Después de que se bloquee la aplicación y se complete la recopilación del volcado de memoria, la aplicación puede finalizar con normalidad.</span><span class="sxs-lookup"><span data-stu-id="01674-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="01674-266">El script de PowerShell configura WER de modo que recopile un máximo de cinco volcados de memoria por aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="01674-267">Los volcados de memoria pueden ocupar una gran cantidad de espacio en disco (hasta varios gigabytes cada uno).</span><span class="sxs-lookup"><span data-stu-id="01674-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="01674-268">La aplicación deja de responder, produce un error durante el inicio o se ejecuta con normalidad</span><span class="sxs-lookup"><span data-stu-id="01674-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="01674-269">Si una aplicación *deja de responder* (se detiene, pero no se bloquea), produce un error durante el inicio o se ejecuta con normalidad, vea [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) (Archivos de volcado de memoria en modo de usuario: selección de la mejor herramienta) para seleccionar una herramienta apropiada para generar el volcado de memoria.</span><span class="sxs-lookup"><span data-stu-id="01674-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="01674-270">Análisis del volcado de memoria</span><span class="sxs-lookup"><span data-stu-id="01674-270">Analyze the dump</span></span>

<span data-ttu-id="01674-271">El volcado de memoria se puede analizar de varias maneras.</span><span class="sxs-lookup"><span data-stu-id="01674-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="01674-272">Para obtener más información, vea [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Análisis de un archivo de volcado de memoria en modo de usuario).</span><span class="sxs-lookup"><span data-stu-id="01674-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="01674-273">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="01674-273">Additional resources</span></span>

* <span data-ttu-id="01674-274">[Kestrel: configuración de punto de conexión](xref:fundamentals/servers/kestrel#endpoint-configuration) (configuración de HTTPS y compatibilidad de SNI incluidas)</span><span class="sxs-lookup"><span data-stu-id="01674-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="01674-275">Una aplicación de ASP.NET Core se puede hospedar en Windows sin usar IIS como [servicio de Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications).</span><span class="sxs-lookup"><span data-stu-id="01674-275">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="01674-276">Cuando se hospeda como un servicio de Windows, la aplicación se inicia automáticamente después de reiniciar el servidor.</span><span class="sxs-lookup"><span data-stu-id="01674-276">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="01674-277">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="01674-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="01674-278">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="01674-278">Prerequisites</span></span>

* [<span data-ttu-id="01674-279">ASP.NET Core SDK 2.1 o posterior</span><span class="sxs-lookup"><span data-stu-id="01674-279">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="01674-280">PowerShell 6.2 o posterior</span><span class="sxs-lookup"><span data-stu-id="01674-280">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="01674-281">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="01674-281">App configuration</span></span>

<span data-ttu-id="01674-282">La aplicación requiere referencias de paquetes para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) y [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="01674-282">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="01674-283">Para probar y depurar cuando se ejecuta fuera de un servicio, agregue código para determinar si la aplicación se ejecuta como un servicio o una aplicación de consola.</span><span class="sxs-lookup"><span data-stu-id="01674-283">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="01674-284">Compruebe si el depurador está asociado o hay presente un conmutador `--console`.</span><span class="sxs-lookup"><span data-stu-id="01674-284">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="01674-285">Si alguna de estas condiciones se cumple (la aplicación no se ejecuta como un servicio), llame a <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="01674-285">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="01674-286">Si las condiciones no se cumplen (la aplicación se ejecuta como servicio):</span><span class="sxs-lookup"><span data-stu-id="01674-286">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="01674-287">Llame a <xref:System.IO.Directory.SetCurrentDirectory*> y use una ruta de acceso a la ubicación de publicación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-287">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="01674-288">No llame a <xref:System.IO.Directory.GetCurrentDirectory*> para obtener la ruta de acceso porque una aplicación de servicio de Windows devuelve una carpeta *C:\\WINDOWS\\system32* cuando se llama a <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="01674-288">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="01674-289">Para obtener más información, consulte la sección [Directorio actual y raíz del contenido](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="01674-289">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="01674-290">Este paso se realiza antes de que la aplicación se configure en `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="01674-290">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="01674-291">Llame a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para ejecutar la aplicación como un servicio.</span><span class="sxs-lookup"><span data-stu-id="01674-291">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="01674-292">Dado que el [Proveedor de configuración de línea de comandos](xref:fundamentals/configuration/index#command-line-configuration-provider) requiere pares nombre-valor en los argumentos de línea de comandos, el conmutador `--console` se quita de los argumentos antes de que <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> los reciba.</span><span class="sxs-lookup"><span data-stu-id="01674-292">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="01674-293">Para escribir en el registro de eventos de Windows, agregue el proveedor EventLog a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="01674-293">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="01674-294">Establezca el nivel de registro con la clave `Logging:LogLevel:Default` en el archivo *appsettings.Production.json*.</span><span class="sxs-lookup"><span data-stu-id="01674-294">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="01674-295">En el ejemplo siguiente de la aplicación de ejemplo, se llama a `RunAsCustomService` en lugar de a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> con el fin de controlar los eventos de duración dentro de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-295">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="01674-296">Para obtener más información, consulte la sección [Control de los eventos de inicio y detención](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="01674-296">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="01674-297">Tipo de implementación</span><span class="sxs-lookup"><span data-stu-id="01674-297">Deployment type</span></span>

<span data-ttu-id="01674-298">Para obtener información y consejos sobre los escenarios de implementación, consulte [Implementación de aplicaciones .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="01674-298">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="01674-299">SDK</span><span class="sxs-lookup"><span data-stu-id="01674-299">SDK</span></span>

<span data-ttu-id="01674-300">En el caso de un servicio basado en una aplicación web que use los marcos Razor Pages o MVC, especifique el SDK web en el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="01674-300">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="01674-301">Si el servicio solo ejecuta tareas en segundo plano (por ejemplo, [servicios hospedados](xref:fundamentals/host/hosted-services)), especifique el SDK de Worker en el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="01674-301">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="01674-302">Implementación dependiente de marco (FDD)</span><span class="sxs-lookup"><span data-stu-id="01674-302">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="01674-303">La implementación dependiente de marco de trabajo (FDD) se basa en la presencia de una versión compartida de .NET Core en todo el sistema en el sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="01674-303">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="01674-304">Cuando se adopta el escenario FDD siguiendo las instrucciones de este artículo, el SDK genera un archivo ejecutable ( *.exe* ), denominado *ejecutable dependiente del marco*.</span><span class="sxs-lookup"><span data-stu-id="01674-304">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable ( *.exe* ), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="01674-305">El [identificador en tiempo de ejecución (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contiene la plataforma de destino.</span><span class="sxs-lookup"><span data-stu-id="01674-305">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="01674-306">En el ejemplo siguiente, el RID se establece en `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="01674-306">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="01674-307">La propiedad `<SelfContained>` se establece en `false`.</span><span class="sxs-lookup"><span data-stu-id="01674-307">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="01674-308">Estas propiedades indican al SDK que genere un archivo ejecutable ( *.exe* ) para Windows y una aplicación que depende del marco .NET Core compartido.</span><span class="sxs-lookup"><span data-stu-id="01674-308">These properties instruct the SDK to generate an executable ( *.exe* ) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="01674-309">No se requiere un archivo *web.config* , que normalmente se produce cuando se publica una aplicación ASP.NET Core, para una aplicación de Windows Services.</span><span class="sxs-lookup"><span data-stu-id="01674-309">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="01674-310">Para deshabilitar la creación de un archivo *web.config* agregue la propiedad `<IsTransformWebConfigDisabled>` establecida en `true`.</span><span class="sxs-lookup"><span data-stu-id="01674-310">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="01674-311">Implementación autocontenida (SCD)</span><span class="sxs-lookup"><span data-stu-id="01674-311">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="01674-312">Una implementación autocontenida (SCD) no depende de la presencia de un marco compartido en el sistema host.</span><span class="sxs-lookup"><span data-stu-id="01674-312">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="01674-313">El tiempo de ejecución y las dependencias de la aplicación se implementan con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-313">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="01674-314">Un [identificador en tiempo de ejecución (RID)](/dotnet/core/rid-catalog) se incluye en el `<PropertyGroup>` que contiene la plataforma de destino:</span><span class="sxs-lookup"><span data-stu-id="01674-314">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="01674-315">Para publicar para varios RID:</span><span class="sxs-lookup"><span data-stu-id="01674-315">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="01674-316">Proporcione los RID en una lista delimitada por punto y coma.</span><span class="sxs-lookup"><span data-stu-id="01674-316">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="01674-317">Use el nombre de la propiedad [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="01674-317">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="01674-318">Para más información, vea el [Catálogo de identificadores de entorno de ejecución (RID) de .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="01674-318">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="01674-319">Una propiedad `<SelfContained>` se establece en `true`:</span><span class="sxs-lookup"><span data-stu-id="01674-319">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="01674-320">Cuenta de usuario de servicio</span><span class="sxs-lookup"><span data-stu-id="01674-320">Service user account</span></span>

<span data-ttu-id="01674-321">Para crear una cuenta de usuario para un servicio, use el cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de un shell de comandos administrativos de PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="01674-321">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="01674-322">En la actualización de octubre de 2018 de Windows 10 (versión 1809/compilación 10.0.17763) o posterior:</span><span class="sxs-lookup"><span data-stu-id="01674-322">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="01674-323">En el sistema operativo Windows anterior a la actualización de octubre de 2018 de Windows 10 (versión 1809/compilación 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="01674-323">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="01674-324">Proporcione una [contraseña segura](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) cuando se le solicite.</span><span class="sxs-lookup"><span data-stu-id="01674-324">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="01674-325">A menos que el parámetro `-AccountExpires` se proporcione para el cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) con una fecha de expiración <xref:System.DateTime>, la cuenta no expirará.</span><span class="sxs-lookup"><span data-stu-id="01674-325">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="01674-326">Para más información, vea [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) y [Service User Accounts](/windows/desktop/services/service-user-accounts) (Cuentas de usuario del servicio).</span><span class="sxs-lookup"><span data-stu-id="01674-326">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="01674-327">Un enfoque alternativo de administración de usuarios al usar Active Directory consiste en usar cuentas de servicio administradas.</span><span class="sxs-lookup"><span data-stu-id="01674-327">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="01674-328">Para obtener más información, consulte [grupo Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) (Información general sobre cuentas de servicio administradas de grupo).</span><span class="sxs-lookup"><span data-stu-id="01674-328">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="01674-329">Derechos para iniciar sesión como servicio</span><span class="sxs-lookup"><span data-stu-id="01674-329">Log on as a service rights</span></span>

<span data-ttu-id="01674-330">Para establecer los derechos de *Iniciar sesión como servicio* para una cuenta de usuario del servicio:</span><span class="sxs-lookup"><span data-stu-id="01674-330">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="01674-331">Abra el editor de la Directiva de seguridad local mediante la ejecución de *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="01674-331">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="01674-332">Expanda el nodo **Directivas locales** y, después, seleccione **Asignación de derechos de usuario**.</span><span class="sxs-lookup"><span data-stu-id="01674-332">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="01674-333">Abra la directiva **Iniciar sesión como servicio**.</span><span class="sxs-lookup"><span data-stu-id="01674-333">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="01674-334">Seleccione **Agregar usuario o grupo**.</span><span class="sxs-lookup"><span data-stu-id="01674-334">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="01674-335">Proporcione el nombre de objeto (cuenta de usuario) mediante cualquiera de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="01674-335">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="01674-336">Escriba la cuenta de usuario (`{DOMAIN OR COMPUTER NAME\USER}`) en el campo del nombre de objeto y seleccione **Aceptar** para agregar el usuario a la directiva.</span><span class="sxs-lookup"><span data-stu-id="01674-336">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="01674-337">Seleccione **Opciones avanzadas**.</span><span class="sxs-lookup"><span data-stu-id="01674-337">Select **Advanced**.</span></span> <span data-ttu-id="01674-338">Seleccione **Buscar ahora**.</span><span class="sxs-lookup"><span data-stu-id="01674-338">Select **Find Now**.</span></span> <span data-ttu-id="01674-339">Seleccione la cuenta de usuario de la lista.</span><span class="sxs-lookup"><span data-stu-id="01674-339">Select the user account from the list.</span></span> <span data-ttu-id="01674-340">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="01674-340">Select **OK**.</span></span> <span data-ttu-id="01674-341">Seleccione **Aceptar** nuevo para agregar el usuario a la directiva.</span><span class="sxs-lookup"><span data-stu-id="01674-341">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="01674-342">Seleccione **Aceptar** o **Aplicar** para aceptar los cambios.</span><span class="sxs-lookup"><span data-stu-id="01674-342">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="01674-343">Creación y administración del servicio de Windows</span><span class="sxs-lookup"><span data-stu-id="01674-343">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="01674-344">Creación de un servicio</span><span class="sxs-lookup"><span data-stu-id="01674-344">Create a service</span></span>

<span data-ttu-id="01674-345">Use los comandos de PowerShell para registrar un servicio.</span><span class="sxs-lookup"><span data-stu-id="01674-345">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="01674-346">Desde un shell de comandos administrativos de PowerShell 6, ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="01674-346">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="01674-347">`{EXE PATH}`: ruta de acceso a la carpeta de la aplicación en el host (por ejemplo, `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="01674-347">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="01674-348">No incluya el archivo ejecutable de la aplicación en la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="01674-348">Don't include the app's executable in the path.</span></span> <span data-ttu-id="01674-349">No se requiere una barra diagonal al final.</span><span class="sxs-lookup"><span data-stu-id="01674-349">A trailing slash isn't required.</span></span>
* <span data-ttu-id="01674-350">`{DOMAIN OR COMPUTER NAME\USER}`: cuenta de usuario de servicio (por ejemplo, `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="01674-350">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="01674-351">`{SERVICE NAME}`: nombre de servicio (por ejemplo, `MyService`).</span><span class="sxs-lookup"><span data-stu-id="01674-351">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="01674-352">`{EXE FILE PATH}`: ruta de acceso ejecutable de la aplicación (por ejemplo, `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="01674-352">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="01674-353">Incluya el nombre de archivo del ejecutable con la extensión.</span><span class="sxs-lookup"><span data-stu-id="01674-353">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="01674-354">`{DESCRIPTION}`: descripción del servicio (por ejemplo, `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="01674-354">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="01674-355">`{DISPLAY NAME}`: nombre para mostrar del servicio (por ejemplo, `My Service`).</span><span class="sxs-lookup"><span data-stu-id="01674-355">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="01674-356">iniciar un servicio.</span><span class="sxs-lookup"><span data-stu-id="01674-356">Start a service</span></span>

<span data-ttu-id="01674-357">Inicie el servicio con el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="01674-357">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="01674-358">Este comando tarda unos segundos en iniciar el servicio.</span><span class="sxs-lookup"><span data-stu-id="01674-358">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="01674-359">Determinación del estado de un servicio</span><span class="sxs-lookup"><span data-stu-id="01674-359">Determine a service's status</span></span>

<span data-ttu-id="01674-360">Para comprobar el estado de un servicio, use el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="01674-360">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="01674-361">El estado se notifica como uno de los siguientes valores:</span><span class="sxs-lookup"><span data-stu-id="01674-361">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="01674-362">Detención de un servicio</span><span class="sxs-lookup"><span data-stu-id="01674-362">Stop a service</span></span>

<span data-ttu-id="01674-363">Detenga un servicio con el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="01674-363">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="01674-364">Eliminación de un servicio</span><span class="sxs-lookup"><span data-stu-id="01674-364">Remove a service</span></span>

<span data-ttu-id="01674-365">Tras un breve intervalo para detener un servicio, elimine el servicio con el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="01674-365">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="01674-366">Control de los eventos de inicio y detención</span><span class="sxs-lookup"><span data-stu-id="01674-366">Handle starting and stopping events</span></span>

<span data-ttu-id="01674-367">Para controlar los eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> y <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:</span><span class="sxs-lookup"><span data-stu-id="01674-367">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="01674-368">Cree una clase que se derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> con los métodos `OnStarting`, `OnStarted` y `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="01674-368">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="01674-369">Cree un método de extensión para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que pase `CustomWebHostService` a <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="01674-369">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="01674-370">En `Program.Main`, llame al método de extensión `RunAsCustomService` en lugar de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="01674-370">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="01674-371">Para ver la ubicación de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> en `Program.Main`, consulte el ejemplo de código que se muestra en la sección [Tipo de implementación](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="01674-371">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="01674-372">Escenarios de servidor proxy y equilibrador de carga</span><span class="sxs-lookup"><span data-stu-id="01674-372">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="01674-373">Los servicios que interactúan con las solicitudes de Internet o de una red corporativa y están detrás de un proxy o de un equilibrador de carga podrían requerir configuración adicional.</span><span class="sxs-lookup"><span data-stu-id="01674-373">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="01674-374">Para obtener más información, vea <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="01674-374">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="01674-375">Configuración de puntos de conexión</span><span class="sxs-lookup"><span data-stu-id="01674-375">Configure endpoints</span></span>

<span data-ttu-id="01674-376">ASP.NET Core se enlaza a `http://localhost:5000` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="01674-376">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="01674-377">Configure la dirección URL y el puerto estableciendo la variable de entorno `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="01674-377">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="01674-378">Para información sobre otros enfoques de configuración de direcciones URL y puertos, consulte el artículo en cuestión:</span><span class="sxs-lookup"><span data-stu-id="01674-378">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="01674-379">En la guía anterior se trata la compatibilidad con los puntos de conexión HTTPS.</span><span class="sxs-lookup"><span data-stu-id="01674-379">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="01674-380">Por ejemplo, configure la aplicación para HTTPS cuando se use la autenticación con un servicio de Windows.</span><span class="sxs-lookup"><span data-stu-id="01674-380">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="01674-381">No se admite el uso del certificado de desarrollo HTTPS de ASP.NET Core para proteger un punto de conexión de servicio.</span><span class="sxs-lookup"><span data-stu-id="01674-381">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="01674-382">Directorio actual y raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="01674-382">Current directory and content root</span></span>

<span data-ttu-id="01674-383">El directorio de trabajo actual devuelto por una llamada a <xref:System.IO.Directory.GetCurrentDirectory*> para un servicio de Windows es la carpeta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="01674-383">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="01674-384">La carpeta *system32* no es una ubicación adecuada para almacenar los archivos de un servicio (por ejemplo los archivos de configuración).</span><span class="sxs-lookup"><span data-stu-id="01674-384">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="01674-385">Utilice uno de los siguientes enfoques para mantener los archivos de configuración y los activos de un servicio, así como para acceder a ellos.</span><span class="sxs-lookup"><span data-stu-id="01674-385">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="01674-386">Configuración de la ruta de acceso raíz del contenido en la carpeta de la aplicación</span><span class="sxs-lookup"><span data-stu-id="01674-386">Set the content root path to the app's folder</span></span>

<span data-ttu-id="01674-387"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> es la misma ruta de acceso proporcionada al argumento `binPath` cuando se crea un servicio.</span><span class="sxs-lookup"><span data-stu-id="01674-387">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="01674-388">En lugar de llamar a `GetCurrentDirectory` para crear rutas de acceso a los archivos de configuración, llame a <xref:System.IO.Directory.SetCurrentDirectory*> con la ruta de acceso a la [raíz del contenido](xref:fundamentals/index#content-root) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-388">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="01674-389">En `Program.Main`, determine la ruta de acceso a la carpeta del archivo ejecutable del servicio y use la ruta de acceso para establecer la raíz del contenido de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="01674-389">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="01674-390">Almacenamiento de los archivos de un servicio en una ubicación adecuada en el disco</span><span class="sxs-lookup"><span data-stu-id="01674-390">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="01674-391">Especifique una ruta de acceso absoluta con <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> al utilizar un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> a la carpeta que contiene los archivos.</span><span class="sxs-lookup"><span data-stu-id="01674-391">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="01674-392">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="01674-392">Troubleshoot</span></span>

<span data-ttu-id="01674-393">Para solucionar problemas de una aplicación de servicio de Windows, consulte <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="01674-393">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="01674-394">Errores comunes</span><span class="sxs-lookup"><span data-stu-id="01674-394">Common errors</span></span>

* <span data-ttu-id="01674-395">Se está usando una versión preliminar o antigua de PowerShell.</span><span class="sxs-lookup"><span data-stu-id="01674-395">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="01674-396">El servicio registrado no usa la salida **publicada** de la aplicación del comando [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="01674-396">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="01674-397">La salida del comando [dotnet build](/dotnet/core/tools/dotnet-build) no es compatible con la implementación de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="01674-397">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="01674-398">Los recursos publicados se encuentran en cualquiera de las siguientes carpetas, según el tipo de implementación:</span><span class="sxs-lookup"><span data-stu-id="01674-398">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="01674-399">*bin/Release/{PLATAFORMA DE DESTINO}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="01674-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="01674-400">*bin/Release/{PLATAFORMA DE DESTINO}/{IDENTIFICADOR DE RUNTIME}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="01674-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="01674-401">El servicio no está en el estado EN EJECUCIÓN.</span><span class="sxs-lookup"><span data-stu-id="01674-401">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="01674-402">Las rutas de acceso a los recursos que usa la aplicación (por ejemplo, certificados) son incorrectas.</span><span class="sxs-lookup"><span data-stu-id="01674-402">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="01674-403">La ruta de acceso base de un servicio de Windows es *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="01674-403">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="01674-404">El usuario no tiene derechos de *inicio de sesión como servicio*.</span><span class="sxs-lookup"><span data-stu-id="01674-404">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="01674-405">La contraseña del usuario ha expirado o se ha pasado incorrectamente al ejecutar el comando de PowerShell `New-Service`.</span><span class="sxs-lookup"><span data-stu-id="01674-405">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="01674-406">La aplicación requiere autenticación ASP.NET Core, pero no está configurada para conexiones seguras (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="01674-406">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="01674-407">El puerto de la dirección URL de solicitud es incorrecto o no está configurado correctamente en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-407">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="01674-408">Registros de eventos del sistema y de aplicación</span><span class="sxs-lookup"><span data-stu-id="01674-408">System and Application Event Logs</span></span>

<span data-ttu-id="01674-409">Acceda a los registros de eventos del sistema y de aplicación:</span><span class="sxs-lookup"><span data-stu-id="01674-409">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="01674-410">Abra el menú Inicio, busque *Visor de eventos* y seleccione la aplicación **Visor de eventos**.</span><span class="sxs-lookup"><span data-stu-id="01674-410">Open the Start menu, search for *Event Viewer* , and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="01674-411">En **Visor de eventos** , abra el nodo **Registros de Windows**.</span><span class="sxs-lookup"><span data-stu-id="01674-411">In **Event Viewer** , open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="01674-412">Seleccione **Sistema** para abrir el registro de eventos del sistema.</span><span class="sxs-lookup"><span data-stu-id="01674-412">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="01674-413">Seleccione **Aplicación** para abrir el registro de eventos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-413">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="01674-414">Busque los errores asociados a la aplicación objeto del error.</span><span class="sxs-lookup"><span data-stu-id="01674-414">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="01674-415">Ejecución de la aplicación en un símbolo del sistema</span><span class="sxs-lookup"><span data-stu-id="01674-415">Run the app at a command prompt</span></span>

<span data-ttu-id="01674-416">Muchos errores de inicio no generan información útil en el registro de eventos.</span><span class="sxs-lookup"><span data-stu-id="01674-416">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="01674-417">La causa de algunos errores se puede encontrar mediante la ejecución de la aplicación en un símbolo del sistema en el sistema de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="01674-417">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="01674-418">Para registrar detalles adicionales de la aplicación, reduzca el [nivel de registro](xref:fundamentals/logging/index#log-level) o ejecute la aplicación en el [entorno de desarrollo](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="01674-418">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="01674-419">Borrado de memorias caché de paquetes</span><span class="sxs-lookup"><span data-stu-id="01674-419">Clear package caches</span></span>

<span data-ttu-id="01674-420">Una aplicación en funcionamiento deja de ejecutarse inmediatamente después de actualizar el SDK de .NET Core en la máquina de desarrollo o de cambiar las versiones del paquete en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-420">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="01674-421">En algunos casos, los paquetes incoherentes pueden interrumpir una aplicación al realizar actualizaciones importantes.</span><span class="sxs-lookup"><span data-stu-id="01674-421">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="01674-422">La mayoría de estos problemas puede corregirse siguiendo estas instrucciones:</span><span class="sxs-lookup"><span data-stu-id="01674-422">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="01674-423">Elimine las carpetas *bin* y *obj*.</span><span class="sxs-lookup"><span data-stu-id="01674-423">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="01674-424">Borre las memorias caché del paquete ejecutando [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) desde un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="01674-424">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="01674-425">Otra manera de borrar las memorias caché de paquetes es usando la herramienta [nuget.exe](https://www.nuget.org/downloads) y ejecutando el comando `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="01674-425">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="01674-426">*nuget.exe* no es una instalación agrupada con el sistema operativo de escritorio de Windows y se debe obtener de forma independiente en el [sitio web de NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="01674-426">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="01674-427">Restaure el proyecto y vuelva a compilarlo.</span><span class="sxs-lookup"><span data-stu-id="01674-427">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="01674-428">Elimine todos los archivos de la carpeta de implementación del servidor antes de volver a implementar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-428">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="01674-429">Aplicación lenta o bloqueada</span><span class="sxs-lookup"><span data-stu-id="01674-429">Slow or hanging app</span></span>

<span data-ttu-id="01674-430">Un *volcado de memoria* es una instantánea de la memoria del sistema que puede ayudar a determinar la causa de un bloqueo de la aplicación, un error de inicio o una aplicación lenta.</span><span class="sxs-lookup"><span data-stu-id="01674-430">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="01674-431">Bloqueo o excepción de la aplicación</span><span class="sxs-lookup"><span data-stu-id="01674-431">App crashes or encounters an exception</span></span>

<span data-ttu-id="01674-432">Obtenga y analice un volcado de memoria en [Informe de errores de Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="01674-432">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="01674-433">Cree una carpeta para almacenar los archivos de volcado de memoria en `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="01674-433">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="01674-434">Ejecute el script [EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) con el nombre del archivo ejecutable de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="01674-434">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="01674-435">Ejecute la aplicación en las condiciones que hacen que se produzca el bloqueo.</span><span class="sxs-lookup"><span data-stu-id="01674-435">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="01674-436">Una vez que se haya producido el bloqueo, ejecute el [script DisableDumps de PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="01674-436">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="01674-437">Después de que se bloquee la aplicación y se complete la recopilación del volcado de memoria, la aplicación puede finalizar con normalidad.</span><span class="sxs-lookup"><span data-stu-id="01674-437">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="01674-438">El script de PowerShell configura WER de modo que recopile un máximo de cinco volcados de memoria por aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-438">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="01674-439">Los volcados de memoria pueden ocupar una gran cantidad de espacio en disco (hasta varios gigabytes cada uno).</span><span class="sxs-lookup"><span data-stu-id="01674-439">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="01674-440">La aplicación deja de responder, produce un error durante el inicio o se ejecuta con normalidad</span><span class="sxs-lookup"><span data-stu-id="01674-440">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="01674-441">Si una aplicación *deja de responder* (se detiene, pero no se bloquea), produce un error durante el inicio o se ejecuta con normalidad, vea [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) (Archivos de volcado de memoria en modo de usuario: selección de la mejor herramienta) para seleccionar una herramienta apropiada para generar el volcado de memoria.</span><span class="sxs-lookup"><span data-stu-id="01674-441">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="01674-442">Análisis del volcado de memoria</span><span class="sxs-lookup"><span data-stu-id="01674-442">Analyze the dump</span></span>

<span data-ttu-id="01674-443">El volcado de memoria se puede analizar de varias maneras.</span><span class="sxs-lookup"><span data-stu-id="01674-443">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="01674-444">Para obtener más información, vea [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Análisis de un archivo de volcado de memoria en modo de usuario).</span><span class="sxs-lookup"><span data-stu-id="01674-444">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="01674-445">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="01674-445">Additional resources</span></span>

* <span data-ttu-id="01674-446">[Kestrel: configuración de punto de conexión](xref:fundamentals/servers/kestrel#endpoint-configuration) (configuración de HTTPS y compatibilidad de SNI incluidas)</span><span class="sxs-lookup"><span data-stu-id="01674-446">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="01674-447">Una aplicación de ASP.NET Core se puede hospedar en Windows sin usar IIS como [servicio de Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications).</span><span class="sxs-lookup"><span data-stu-id="01674-447">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="01674-448">Cuando se hospeda como un servicio de Windows, la aplicación se inicia automáticamente después de reiniciar el servidor.</span><span class="sxs-lookup"><span data-stu-id="01674-448">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="01674-449">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="01674-449">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="01674-450">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="01674-450">Prerequisites</span></span>

* [<span data-ttu-id="01674-451">ASP.NET Core SDK 2.1 o posterior</span><span class="sxs-lookup"><span data-stu-id="01674-451">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="01674-452">PowerShell 6.2 o posterior</span><span class="sxs-lookup"><span data-stu-id="01674-452">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="01674-453">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="01674-453">App configuration</span></span>

<span data-ttu-id="01674-454">La aplicación requiere referencias de paquetes para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) y [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="01674-454">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="01674-455">Para probar y depurar cuando se ejecuta fuera de un servicio, agregue código para determinar si la aplicación se ejecuta como un servicio o una aplicación de consola.</span><span class="sxs-lookup"><span data-stu-id="01674-455">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="01674-456">Compruebe si el depurador está asociado o hay presente un conmutador `--console`.</span><span class="sxs-lookup"><span data-stu-id="01674-456">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="01674-457">Si alguna de estas condiciones se cumple (la aplicación no se ejecuta como un servicio), llame a <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="01674-457">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="01674-458">Si las condiciones no se cumplen (la aplicación se ejecuta como servicio):</span><span class="sxs-lookup"><span data-stu-id="01674-458">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="01674-459">Llame a <xref:System.IO.Directory.SetCurrentDirectory*> y use una ruta de acceso a la ubicación de publicación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-459">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="01674-460">No llame a <xref:System.IO.Directory.GetCurrentDirectory*> para obtener la ruta de acceso porque una aplicación de servicio de Windows devuelve una carpeta *C:\\WINDOWS\\system32* cuando se llama a <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="01674-460">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="01674-461">Para obtener más información, consulte la sección [Directorio actual y raíz del contenido](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="01674-461">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="01674-462">Este paso se realiza antes de que la aplicación se configure en `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="01674-462">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="01674-463">Llame a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para ejecutar la aplicación como un servicio.</span><span class="sxs-lookup"><span data-stu-id="01674-463">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="01674-464">Dado que el [Proveedor de configuración de línea de comandos](xref:fundamentals/configuration/index#command-line-configuration-provider) requiere pares nombre-valor en los argumentos de línea de comandos, el conmutador `--console` se quita de los argumentos antes de que <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> los reciba.</span><span class="sxs-lookup"><span data-stu-id="01674-464">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="01674-465">Para escribir en el registro de eventos de Windows, agregue el proveedor EventLog a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="01674-465">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="01674-466">Establezca el nivel de registro con la clave `Logging:LogLevel:Default` en el archivo *appsettings.Production.json*.</span><span class="sxs-lookup"><span data-stu-id="01674-466">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="01674-467">En el ejemplo siguiente de la aplicación de ejemplo, se llama a `RunAsCustomService` en lugar de a <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> con el fin de controlar los eventos de duración dentro de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-467">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="01674-468">Para obtener más información, consulte la sección [Control de los eventos de inicio y detención](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="01674-468">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="01674-469">Tipo de implementación</span><span class="sxs-lookup"><span data-stu-id="01674-469">Deployment type</span></span>

<span data-ttu-id="01674-470">Para obtener información y consejos sobre los escenarios de implementación, consulte [Implementación de aplicaciones .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="01674-470">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="01674-471">SDK</span><span class="sxs-lookup"><span data-stu-id="01674-471">SDK</span></span>

<span data-ttu-id="01674-472">En el caso de un servicio basado en una aplicación web que use los marcos Razor Pages o MVC, especifique el SDK web en el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="01674-472">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="01674-473">Si el servicio solo ejecuta tareas en segundo plano (por ejemplo, [servicios hospedados](xref:fundamentals/host/hosted-services)), especifique el SDK de Worker en el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="01674-473">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="01674-474">Implementación dependiente de marco (FDD)</span><span class="sxs-lookup"><span data-stu-id="01674-474">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="01674-475">La implementación dependiente de marco de trabajo (FDD) se basa en la presencia de una versión compartida de .NET Core en todo el sistema en el sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="01674-475">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="01674-476">Cuando se adopta el escenario FDD siguiendo las instrucciones de este artículo, el SDK genera un archivo ejecutable ( *.exe* ), denominado *ejecutable dependiente del marco*.</span><span class="sxs-lookup"><span data-stu-id="01674-476">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable ( *.exe* ), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="01674-477">El [identificador en tiempo de ejecución (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contiene la plataforma de destino.</span><span class="sxs-lookup"><span data-stu-id="01674-477">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="01674-478">En el ejemplo siguiente, el RID se establece en `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="01674-478">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="01674-479">La propiedad `<SelfContained>` se establece en `false`.</span><span class="sxs-lookup"><span data-stu-id="01674-479">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="01674-480">Estas propiedades indican al SDK que genere un archivo ejecutable ( *.exe* ) para Windows y una aplicación que depende del marco .NET Core compartido.</span><span class="sxs-lookup"><span data-stu-id="01674-480">These properties instruct the SDK to generate an executable ( *.exe* ) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="01674-481">La propiedad `<UseAppHost>` se establece en `true`.</span><span class="sxs-lookup"><span data-stu-id="01674-481">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="01674-482">Esta propiedad proporciona el servicio con una ruta de acceso de activación (un archivo ejecutable, *.exe* ) para una FDD.</span><span class="sxs-lookup"><span data-stu-id="01674-482">This property provides the service with an activation path (an executable, *.exe* ) for an FDD.</span></span>

<span data-ttu-id="01674-483">No se requiere un archivo *web.config* , que normalmente se produce cuando se publica una aplicación ASP.NET Core, para una aplicación de Windows Services.</span><span class="sxs-lookup"><span data-stu-id="01674-483">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="01674-484">Para deshabilitar la creación de un archivo *web.config* agregue la propiedad `<IsTransformWebConfigDisabled>` establecida en `true`.</span><span class="sxs-lookup"><span data-stu-id="01674-484">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="01674-485">Implementación autocontenida (SCD)</span><span class="sxs-lookup"><span data-stu-id="01674-485">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="01674-486">Una implementación autocontenida (SCD) no depende de la presencia de un marco compartido en el sistema host.</span><span class="sxs-lookup"><span data-stu-id="01674-486">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="01674-487">El tiempo de ejecución y las dependencias de la aplicación se implementan con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-487">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="01674-488">Un [identificador en tiempo de ejecución (RID)](/dotnet/core/rid-catalog) se incluye en el `<PropertyGroup>` que contiene la plataforma de destino:</span><span class="sxs-lookup"><span data-stu-id="01674-488">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="01674-489">Para publicar para varios RID:</span><span class="sxs-lookup"><span data-stu-id="01674-489">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="01674-490">Proporcione los RID en una lista delimitada por punto y coma.</span><span class="sxs-lookup"><span data-stu-id="01674-490">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="01674-491">Use el nombre de la propiedad [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="01674-491">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="01674-492">Para más información, vea el [Catálogo de identificadores de entorno de ejecución (RID) de .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="01674-492">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="01674-493">Una propiedad `<SelfContained>` se establece en `true`:</span><span class="sxs-lookup"><span data-stu-id="01674-493">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="01674-494">Cuenta de usuario de servicio</span><span class="sxs-lookup"><span data-stu-id="01674-494">Service user account</span></span>

<span data-ttu-id="01674-495">Para crear una cuenta de usuario para un servicio, use el cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de un shell de comandos administrativos de PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="01674-495">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="01674-496">En la actualización de octubre de 2018 de Windows 10 (versión 1809/compilación 10.0.17763) o posterior:</span><span class="sxs-lookup"><span data-stu-id="01674-496">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="01674-497">En el sistema operativo Windows anterior a la actualización de octubre de 2018 de Windows 10 (versión 1809/compilación 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="01674-497">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="01674-498">Proporcione una [contraseña segura](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) cuando se le solicite.</span><span class="sxs-lookup"><span data-stu-id="01674-498">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="01674-499">A menos que el parámetro `-AccountExpires` se proporcione para el cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) con una fecha de expiración <xref:System.DateTime>, la cuenta no expirará.</span><span class="sxs-lookup"><span data-stu-id="01674-499">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="01674-500">Para más información, vea [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) y [Service User Accounts](/windows/desktop/services/service-user-accounts) (Cuentas de usuario del servicio).</span><span class="sxs-lookup"><span data-stu-id="01674-500">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="01674-501">Un enfoque alternativo de administración de usuarios al usar Active Directory consiste en usar cuentas de servicio administradas.</span><span class="sxs-lookup"><span data-stu-id="01674-501">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="01674-502">Para obtener más información, consulte [grupo Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) (Información general sobre cuentas de servicio administradas de grupo).</span><span class="sxs-lookup"><span data-stu-id="01674-502">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="01674-503">Derechos para iniciar sesión como servicio</span><span class="sxs-lookup"><span data-stu-id="01674-503">Log on as a service rights</span></span>

<span data-ttu-id="01674-504">Para establecer los derechos de *Iniciar sesión como servicio* para una cuenta de usuario del servicio:</span><span class="sxs-lookup"><span data-stu-id="01674-504">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="01674-505">Abra el editor de la Directiva de seguridad local mediante la ejecución de *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="01674-505">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="01674-506">Expanda el nodo **Directivas locales** y, después, seleccione **Asignación de derechos de usuario**.</span><span class="sxs-lookup"><span data-stu-id="01674-506">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="01674-507">Abra la directiva **Iniciar sesión como servicio**.</span><span class="sxs-lookup"><span data-stu-id="01674-507">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="01674-508">Seleccione **Agregar usuario o grupo**.</span><span class="sxs-lookup"><span data-stu-id="01674-508">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="01674-509">Proporcione el nombre de objeto (cuenta de usuario) mediante cualquiera de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="01674-509">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="01674-510">Escriba la cuenta de usuario (`{DOMAIN OR COMPUTER NAME\USER}`) en el campo del nombre de objeto y seleccione **Aceptar** para agregar el usuario a la directiva.</span><span class="sxs-lookup"><span data-stu-id="01674-510">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="01674-511">Seleccione **Opciones avanzadas**.</span><span class="sxs-lookup"><span data-stu-id="01674-511">Select **Advanced**.</span></span> <span data-ttu-id="01674-512">Seleccione **Buscar ahora**.</span><span class="sxs-lookup"><span data-stu-id="01674-512">Select **Find Now**.</span></span> <span data-ttu-id="01674-513">Seleccione la cuenta de usuario de la lista.</span><span class="sxs-lookup"><span data-stu-id="01674-513">Select the user account from the list.</span></span> <span data-ttu-id="01674-514">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="01674-514">Select **OK**.</span></span> <span data-ttu-id="01674-515">Seleccione **Aceptar** nuevo para agregar el usuario a la directiva.</span><span class="sxs-lookup"><span data-stu-id="01674-515">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="01674-516">Seleccione **Aceptar** o **Aplicar** para aceptar los cambios.</span><span class="sxs-lookup"><span data-stu-id="01674-516">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="01674-517">Creación y administración del servicio de Windows</span><span class="sxs-lookup"><span data-stu-id="01674-517">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="01674-518">Creación de un servicio</span><span class="sxs-lookup"><span data-stu-id="01674-518">Create a service</span></span>

<span data-ttu-id="01674-519">Use los comandos de PowerShell para registrar un servicio.</span><span class="sxs-lookup"><span data-stu-id="01674-519">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="01674-520">Desde un shell de comandos administrativos de PowerShell 6, ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="01674-520">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="01674-521">`{EXE PATH}`: ruta de acceso a la carpeta de la aplicación en el host (por ejemplo, `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="01674-521">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="01674-522">No incluya el archivo ejecutable de la aplicación en la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="01674-522">Don't include the app's executable in the path.</span></span> <span data-ttu-id="01674-523">No se requiere una barra diagonal al final.</span><span class="sxs-lookup"><span data-stu-id="01674-523">A trailing slash isn't required.</span></span>
* <span data-ttu-id="01674-524">`{DOMAIN OR COMPUTER NAME\USER}`: cuenta de usuario de servicio (por ejemplo, `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="01674-524">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="01674-525">`{SERVICE NAME}`: nombre de servicio (por ejemplo, `MyService`).</span><span class="sxs-lookup"><span data-stu-id="01674-525">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="01674-526">`{EXE FILE PATH}`: ruta de acceso ejecutable de la aplicación (por ejemplo, `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="01674-526">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="01674-527">Incluya el nombre de archivo del ejecutable con la extensión.</span><span class="sxs-lookup"><span data-stu-id="01674-527">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="01674-528">`{DESCRIPTION}`: descripción del servicio (por ejemplo, `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="01674-528">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="01674-529">`{DISPLAY NAME}`: nombre para mostrar del servicio (por ejemplo, `My Service`).</span><span class="sxs-lookup"><span data-stu-id="01674-529">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="01674-530">iniciar un servicio.</span><span class="sxs-lookup"><span data-stu-id="01674-530">Start a service</span></span>

<span data-ttu-id="01674-531">Inicie el servicio con el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="01674-531">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="01674-532">Este comando tarda unos segundos en iniciar el servicio.</span><span class="sxs-lookup"><span data-stu-id="01674-532">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="01674-533">Determinación del estado de un servicio</span><span class="sxs-lookup"><span data-stu-id="01674-533">Determine a service's status</span></span>

<span data-ttu-id="01674-534">Para comprobar el estado de un servicio, use el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="01674-534">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="01674-535">El estado se notifica como uno de los siguientes valores:</span><span class="sxs-lookup"><span data-stu-id="01674-535">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="01674-536">Detención de un servicio</span><span class="sxs-lookup"><span data-stu-id="01674-536">Stop a service</span></span>

<span data-ttu-id="01674-537">Detenga un servicio con el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="01674-537">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="01674-538">Eliminación de un servicio</span><span class="sxs-lookup"><span data-stu-id="01674-538">Remove a service</span></span>

<span data-ttu-id="01674-539">Tras un breve intervalo para detener un servicio, elimine el servicio con el siguiente comando de PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="01674-539">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="01674-540">Control de los eventos de inicio y detención</span><span class="sxs-lookup"><span data-stu-id="01674-540">Handle starting and stopping events</span></span>

<span data-ttu-id="01674-541">Para controlar los eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> y <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:</span><span class="sxs-lookup"><span data-stu-id="01674-541">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="01674-542">Cree una clase que se derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> con los métodos `OnStarting`, `OnStarted` y `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="01674-542">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="01674-543">Cree un método de extensión para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que pase `CustomWebHostService` a <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="01674-543">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="01674-544">En `Program.Main`, llame al método de extensión `RunAsCustomService` en lugar de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="01674-544">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="01674-545">Para ver la ubicación de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> en `Program.Main`, consulte el ejemplo de código que se muestra en la sección [Tipo de implementación](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="01674-545">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="01674-546">Escenarios de servidor proxy y equilibrador de carga</span><span class="sxs-lookup"><span data-stu-id="01674-546">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="01674-547">Los servicios que interactúan con las solicitudes de Internet o de una red corporativa y están detrás de un proxy o de un equilibrador de carga podrían requerir configuración adicional.</span><span class="sxs-lookup"><span data-stu-id="01674-547">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="01674-548">Para obtener más información, vea <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="01674-548">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="01674-549">Configuración de puntos de conexión</span><span class="sxs-lookup"><span data-stu-id="01674-549">Configure endpoints</span></span>

<span data-ttu-id="01674-550">ASP.NET Core se enlaza a `http://localhost:5000` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="01674-550">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="01674-551">Configure la dirección URL y el puerto estableciendo la variable de entorno `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="01674-551">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="01674-552">Para información sobre otros enfoques de configuración de direcciones URL y puertos, consulte el artículo en cuestión:</span><span class="sxs-lookup"><span data-stu-id="01674-552">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="01674-553">En la guía anterior se trata la compatibilidad con los puntos de conexión HTTPS.</span><span class="sxs-lookup"><span data-stu-id="01674-553">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="01674-554">Por ejemplo, configure la aplicación para HTTPS cuando se use la autenticación con un servicio de Windows.</span><span class="sxs-lookup"><span data-stu-id="01674-554">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="01674-555">No se admite el uso del certificado de desarrollo HTTPS de ASP.NET Core para proteger un punto de conexión de servicio.</span><span class="sxs-lookup"><span data-stu-id="01674-555">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="01674-556">Directorio actual y raíz del contenido</span><span class="sxs-lookup"><span data-stu-id="01674-556">Current directory and content root</span></span>

<span data-ttu-id="01674-557">El directorio de trabajo actual devuelto por una llamada a <xref:System.IO.Directory.GetCurrentDirectory*> para un servicio de Windows es la carpeta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="01674-557">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="01674-558">La carpeta *system32* no es una ubicación adecuada para almacenar los archivos de un servicio (por ejemplo los archivos de configuración).</span><span class="sxs-lookup"><span data-stu-id="01674-558">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="01674-559">Utilice uno de los siguientes enfoques para mantener los archivos de configuración y los activos de un servicio, así como para acceder a ellos.</span><span class="sxs-lookup"><span data-stu-id="01674-559">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="01674-560">Configuración de la ruta de acceso raíz del contenido en la carpeta de la aplicación</span><span class="sxs-lookup"><span data-stu-id="01674-560">Set the content root path to the app's folder</span></span>

<span data-ttu-id="01674-561"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> es la misma ruta de acceso proporcionada al argumento `binPath` cuando se crea un servicio.</span><span class="sxs-lookup"><span data-stu-id="01674-561">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="01674-562">En lugar de llamar a `GetCurrentDirectory` para crear rutas de acceso a los archivos de configuración, llame a <xref:System.IO.Directory.SetCurrentDirectory*> con la ruta de acceso a la [raíz del contenido](xref:fundamentals/index#content-root) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-562">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="01674-563">En `Program.Main`, determine la ruta de acceso a la carpeta del archivo ejecutable del servicio y use la ruta de acceso para establecer la raíz del contenido de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="01674-563">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="01674-564">Almacenamiento de los archivos de un servicio en una ubicación adecuada en el disco</span><span class="sxs-lookup"><span data-stu-id="01674-564">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="01674-565">Especifique una ruta de acceso absoluta con <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> al utilizar un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> a la carpeta que contiene los archivos.</span><span class="sxs-lookup"><span data-stu-id="01674-565">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="01674-566">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="01674-566">Troubleshoot</span></span>

<span data-ttu-id="01674-567">Para solucionar problemas de una aplicación de servicio de Windows, consulte <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="01674-567">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="01674-568">Errores comunes</span><span class="sxs-lookup"><span data-stu-id="01674-568">Common errors</span></span>

* <span data-ttu-id="01674-569">Se está usando una versión preliminar o antigua de PowerShell.</span><span class="sxs-lookup"><span data-stu-id="01674-569">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="01674-570">El servicio registrado no usa la salida **publicada** de la aplicación del comando [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="01674-570">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="01674-571">La salida del comando [dotnet build](/dotnet/core/tools/dotnet-build) no es compatible con la implementación de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="01674-571">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="01674-572">Los recursos publicados se encuentran en cualquiera de las siguientes carpetas, según el tipo de implementación:</span><span class="sxs-lookup"><span data-stu-id="01674-572">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="01674-573">*bin/Release/{PLATAFORMA DE DESTINO}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="01674-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="01674-574">*bin/Release/{PLATAFORMA DE DESTINO}/{IDENTIFICADOR DE RUNTIME}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="01674-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="01674-575">El servicio no está en el estado EN EJECUCIÓN.</span><span class="sxs-lookup"><span data-stu-id="01674-575">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="01674-576">Las rutas de acceso a los recursos que usa la aplicación (por ejemplo, certificados) son incorrectas.</span><span class="sxs-lookup"><span data-stu-id="01674-576">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="01674-577">La ruta de acceso base de un servicio de Windows es *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="01674-577">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="01674-578">El usuario no tiene derechos de *inicio de sesión como servicio*.</span><span class="sxs-lookup"><span data-stu-id="01674-578">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="01674-579">La contraseña del usuario ha expirado o se ha pasado incorrectamente al ejecutar el comando de PowerShell `New-Service`.</span><span class="sxs-lookup"><span data-stu-id="01674-579">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="01674-580">La aplicación requiere autenticación ASP.NET Core, pero no está configurada para conexiones seguras (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="01674-580">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="01674-581">El puerto de la dirección URL de solicitud es incorrecto o no está configurado correctamente en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-581">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="01674-582">Registros de eventos del sistema y de aplicación</span><span class="sxs-lookup"><span data-stu-id="01674-582">System and Application Event Logs</span></span>

<span data-ttu-id="01674-583">Acceda a los registros de eventos del sistema y de aplicación:</span><span class="sxs-lookup"><span data-stu-id="01674-583">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="01674-584">Abra el menú Inicio, busque *Visor de eventos* y seleccione la aplicación **Visor de eventos**.</span><span class="sxs-lookup"><span data-stu-id="01674-584">Open the Start menu, search for *Event Viewer* , and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="01674-585">En **Visor de eventos** , abra el nodo **Registros de Windows**.</span><span class="sxs-lookup"><span data-stu-id="01674-585">In **Event Viewer** , open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="01674-586">Seleccione **Sistema** para abrir el registro de eventos del sistema.</span><span class="sxs-lookup"><span data-stu-id="01674-586">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="01674-587">Seleccione **Aplicación** para abrir el registro de eventos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-587">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="01674-588">Busque los errores asociados a la aplicación objeto del error.</span><span class="sxs-lookup"><span data-stu-id="01674-588">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="01674-589">Ejecución de la aplicación en un símbolo del sistema</span><span class="sxs-lookup"><span data-stu-id="01674-589">Run the app at a command prompt</span></span>

<span data-ttu-id="01674-590">Muchos errores de inicio no generan información útil en el registro de eventos.</span><span class="sxs-lookup"><span data-stu-id="01674-590">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="01674-591">La causa de algunos errores se puede encontrar mediante la ejecución de la aplicación en un símbolo del sistema en el sistema de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="01674-591">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="01674-592">Para registrar detalles adicionales de la aplicación, reduzca el [nivel de registro](xref:fundamentals/logging/index#log-level) o ejecute la aplicación en el [entorno de desarrollo](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="01674-592">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="01674-593">Borrado de memorias caché de paquetes</span><span class="sxs-lookup"><span data-stu-id="01674-593">Clear package caches</span></span>

<span data-ttu-id="01674-594">Una aplicación en funcionamiento deja de ejecutarse inmediatamente después de actualizar el SDK de .NET Core en la máquina de desarrollo o de cambiar las versiones del paquete en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-594">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="01674-595">En algunos casos, los paquetes incoherentes pueden interrumpir una aplicación al realizar actualizaciones importantes.</span><span class="sxs-lookup"><span data-stu-id="01674-595">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="01674-596">La mayoría de estos problemas puede corregirse siguiendo estas instrucciones:</span><span class="sxs-lookup"><span data-stu-id="01674-596">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="01674-597">Elimine las carpetas *bin* y *obj*.</span><span class="sxs-lookup"><span data-stu-id="01674-597">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="01674-598">Borre las memorias caché del paquete ejecutando [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) desde un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="01674-598">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="01674-599">Otra manera de borrar las memorias caché de paquetes es usando la herramienta [nuget.exe](https://www.nuget.org/downloads) y ejecutando el comando `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="01674-599">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="01674-600">*nuget.exe* no es una instalación agrupada con el sistema operativo de escritorio de Windows y se debe obtener de forma independiente en el [sitio web de NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="01674-600">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="01674-601">Restaure el proyecto y vuelva a compilarlo.</span><span class="sxs-lookup"><span data-stu-id="01674-601">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="01674-602">Elimine todos los archivos de la carpeta de implementación del servidor antes de volver a implementar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-602">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="01674-603">Aplicación lenta o bloqueada</span><span class="sxs-lookup"><span data-stu-id="01674-603">Slow or hanging app</span></span>

<span data-ttu-id="01674-604">Un *volcado de memoria* es una instantánea de la memoria del sistema que puede ayudar a determinar la causa de un bloqueo de la aplicación, un error de inicio o una aplicación lenta.</span><span class="sxs-lookup"><span data-stu-id="01674-604">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="01674-605">Bloqueo o excepción de la aplicación</span><span class="sxs-lookup"><span data-stu-id="01674-605">App crashes or encounters an exception</span></span>

<span data-ttu-id="01674-606">Obtenga y analice un volcado de memoria en [Informe de errores de Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="01674-606">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="01674-607">Cree una carpeta para almacenar los archivos de volcado de memoria en `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="01674-607">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="01674-608">Ejecute el script [EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) con el nombre del archivo ejecutable de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="01674-608">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="01674-609">Ejecute la aplicación en las condiciones que hacen que se produzca el bloqueo.</span><span class="sxs-lookup"><span data-stu-id="01674-609">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="01674-610">Una vez que se haya producido el bloqueo, ejecute el [script DisableDumps de PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="01674-610">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="01674-611">Después de que se bloquee la aplicación y se complete la recopilación del volcado de memoria, la aplicación puede finalizar con normalidad.</span><span class="sxs-lookup"><span data-stu-id="01674-611">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="01674-612">El script de PowerShell configura WER de modo que recopile un máximo de cinco volcados de memoria por aplicación.</span><span class="sxs-lookup"><span data-stu-id="01674-612">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="01674-613">Los volcados de memoria pueden ocupar una gran cantidad de espacio en disco (hasta varios gigabytes cada uno).</span><span class="sxs-lookup"><span data-stu-id="01674-613">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="01674-614">La aplicación deja de responder, produce un error durante el inicio o se ejecuta con normalidad</span><span class="sxs-lookup"><span data-stu-id="01674-614">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="01674-615">Si una aplicación *deja de responder* (se detiene, pero no se bloquea), produce un error durante el inicio o se ejecuta con normalidad, vea [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) (Archivos de volcado de memoria en modo de usuario: selección de la mejor herramienta) para seleccionar una herramienta apropiada para generar el volcado de memoria.</span><span class="sxs-lookup"><span data-stu-id="01674-615">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="01674-616">Análisis del volcado de memoria</span><span class="sxs-lookup"><span data-stu-id="01674-616">Analyze the dump</span></span>

<span data-ttu-id="01674-617">El volcado de memoria se puede analizar de varias maneras.</span><span class="sxs-lookup"><span data-stu-id="01674-617">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="01674-618">Para obtener más información, vea [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Análisis de un archivo de volcado de memoria en modo de usuario).</span><span class="sxs-lookup"><span data-stu-id="01674-618">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="01674-619">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="01674-619">Additional resources</span></span>

* <span data-ttu-id="01674-620">[Kestrel: configuración de punto de conexión](xref:fundamentals/servers/kestrel#endpoint-configuration) (configuración de HTTPS y compatibilidad de SNI incluidas)</span><span class="sxs-lookup"><span data-stu-id="01674-620">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
