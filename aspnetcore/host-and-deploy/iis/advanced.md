---
title: Configuración avanzada
author: rick-anderson
description: Configuración avanzada con el módulo de ASP.NET Core e Internet Information Services (IIS).
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/advanced
ms.openlocfilehash: 9f14929a7d298d6f4d66abcc88665db34fc072bf
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058621"
---
# <a name="advanced-configuration-of-the-aspnet-core-module-and-iis"></a><span data-ttu-id="20095-103">Configuración avanzada del módulo de ASP.NET Core e IIS</span><span class="sxs-lookup"><span data-stu-id="20095-103">Advanced configuration of the ASP.NET Core Module and IIS</span></span>

<span data-ttu-id="20095-104">En este artículo se describen las opciones de configuración y los escenarios avanzados para el módulo de ASP.NET Core e IIS.</span><span class="sxs-lookup"><span data-stu-id="20095-104">This article covers advanced configuration options and scenarios for the ASP.NET Core Module and IIS.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="20095-105">Modificación del tamaño de la pila</span><span class="sxs-lookup"><span data-stu-id="20095-105">Modify the stack size</span></span>

<span data-ttu-id="20095-106">*Solo se aplica cuando se usa el modelo de hospedaje dentro de proceso.*</span><span class="sxs-lookup"><span data-stu-id="20095-106">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="20095-107">Configure el tamaño de la pila administrada mediante el valor `stackSize` en bytes en el archivo `web.config`.</span><span class="sxs-lookup"><span data-stu-id="20095-107">Configure the managed stack size using the `stackSize` setting in bytes in the `web.config` file.</span></span> <span data-ttu-id="20095-108">El tamaño predeterminado es de 1 048 576 bytes (1 MB).</span><span class="sxs-lookup"><span data-stu-id="20095-108">The default size is 1,048,576 bytes (1 MB).</span></span> <span data-ttu-id="20095-109">En el ejemplo siguiente, se cambia el tamaño de la pila a 2 MB (2 097 152 bytes):</span><span class="sxs-lookup"><span data-stu-id="20095-109">The following example changes the stack size to 2 MB (2,097,152 bytes):</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="20095-110">La configuración de proxy usa el protocolo HTTP y un token de emparejamiento</span><span class="sxs-lookup"><span data-stu-id="20095-110">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="20095-111">*Solo se aplica al hospedaje fuera de proceso.*</span><span class="sxs-lookup"><span data-stu-id="20095-111">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="20095-112">El proxy creado entre el módulo ASP.NET Core y Kestrel usa el protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="20095-112">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="20095-113">No hay ningún riesgo de interceptación del tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor.</span><span class="sxs-lookup"><span data-stu-id="20095-113">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="20095-114">Un token de emparejamiento sirve para garantizar que las solicitudes recibidas por Kestrel se redirigieron mediante proxy por IIS y no procedieron de otra fuente.</span><span class="sxs-lookup"><span data-stu-id="20095-114">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="20095-115">El módulo crea el token de emparejamiento y lo establece en una variable de entorno (`ASPNETCORE_TOKEN`).</span><span class="sxs-lookup"><span data-stu-id="20095-115">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="20095-116">El token de emparejamiento también se establece en un encabezado (`MS-ASPNETCORE-TOKEN`) en cada solicitud redirigida mediante proxy.</span><span class="sxs-lookup"><span data-stu-id="20095-116">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="20095-117">El middleware de IIS comprueba cada solicitud recibida para confirmar que el valor del encabezado del token de emparejamiento coincida con el valor de la variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="20095-117">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="20095-118">Si los valores del token no coinciden, la solicitud se registra y se rechaza.</span><span class="sxs-lookup"><span data-stu-id="20095-118">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="20095-119">No se puede acceder a la variable de entorno del token de emparejamiento y al tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor.</span><span class="sxs-lookup"><span data-stu-id="20095-119">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="20095-120">Sin conocer el valor del token de emparejamiento, un atacante no puede enviar solicitudes que omitan la comprobación en el middleware de IIS.</span><span class="sxs-lookup"><span data-stu-id="20095-120">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="20095-121">El módulo ASP.NET Core con una configuración compartida de IIS</span><span class="sxs-lookup"><span data-stu-id="20095-121">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="20095-122">El instalador del módulo ASP.NET Core se ejecuta con los privilegios de la cuenta `TrustedInstaller`.</span><span class="sxs-lookup"><span data-stu-id="20095-122">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="20095-123">Como la cuenta local del sistema no tiene permiso de modificación en la ruta de acceso de recurso compartido que se usa en la configuración compartida de IIS, el instalador inicia un error de acceso denegado al intentar configurar los valores del módulo en el archivo `applicationHost.config` del recurso compartido.</span><span class="sxs-lookup"><span data-stu-id="20095-123">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="20095-124">Cuando se usa una configuración compartida de IIS en el mismo equipo que la instalación de IIS, ejecute el instalador del lote de hospedaje de ASP.NET Core con el parámetro `OPT_NO_SHARED_CONFIG_CHECK` establecido en `1`:</span><span class="sxs-lookup"><span data-stu-id="20095-124">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="20095-125">Cuando la ruta de acceso a la configuración compartida no se encuentra en el mismo equipo que la instalación de IIS, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="20095-125">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="20095-126">Deshabilite la configuración compartida de IIS.</span><span class="sxs-lookup"><span data-stu-id="20095-126">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="20095-127">Ejecute el instalador.</span><span class="sxs-lookup"><span data-stu-id="20095-127">Run the installer.</span></span>
1. <span data-ttu-id="20095-128">Exporte el archivo `applicationHost.config` actualizado en el recurso compartido de archivos.</span><span class="sxs-lookup"><span data-stu-id="20095-128">Export the updated `applicationHost.config` file to the file share.</span></span>
1. <span data-ttu-id="20095-129">Vuelva a habilitar la configuración compartida de IIS.</span><span class="sxs-lookup"><span data-stu-id="20095-129">Re-enable the IIS Shared Configuration.</span></span>

## <a name="data-protection"></a><span data-ttu-id="20095-130">Protección de datos</span><span class="sxs-lookup"><span data-stu-id="20095-130">Data protection</span></span>

<span data-ttu-id="20095-131">La [pila de protección de datos de ASP.NET Core](xref:security/data-protection/introduction) la usan varios [middlewares](xref:fundamentals/middleware/index) de ASP.NET Core, incluidos los que se emplean en la autenticación.</span><span class="sxs-lookup"><span data-stu-id="20095-131">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="20095-132">Aunque el código de usuario no llame a las API de protección de datos, la protección de datos se debe configurar con un script de implementación o en el código de usuario para crear un [almacén de claves](xref:security/data-protection/implementation/key-management) criptográficas persistente.</span><span class="sxs-lookup"><span data-stu-id="20095-132">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="20095-133">Si no se configura la protección de datos, las claves se conservan en memoria y se descartan cuando se reinicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="20095-133">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="20095-134">Si el conjunto de claves de protección de datos se almacena en memoria cuando se reinicia la aplicación:</span><span class="sxs-lookup"><span data-stu-id="20095-134">If the Data Protection key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="20095-135">Todos los tokens de autenticación basados en cookie se invalidan.</span><span class="sxs-lookup"><span data-stu-id="20095-135">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="20095-136">Los usuarios tienen que iniciar sesión de nuevo en la siguiente solicitud.</span><span class="sxs-lookup"><span data-stu-id="20095-136">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="20095-137">Ya no se pueden descifrar los datos protegidos con el conjunto de claves.</span><span class="sxs-lookup"><span data-stu-id="20095-137">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="20095-138">Esto puede incluir [tokens CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) y [cookies](xref:fundamentals/app-state#tempdata) de TempData de ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="20095-138">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="20095-139">Para configurar la protección de datos en IIS para conservar el conjunto de claves, use **uno** de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="20095-139">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="20095-140">**Creación de claves del Registro de protección de datos**</span><span class="sxs-lookup"><span data-stu-id="20095-140">**Create Data Protection Registry keys**</span></span>

  <span data-ttu-id="20095-141">Las claves de protección de datos que las aplicaciones de ASP.NET Core usan se almacenan en el registro externo a las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="20095-141">Data Protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="20095-142">Para conservar las claves de una determinada aplicación, cree claves del Registro para el grupo de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="20095-142">To persist the keys for a given app, create Registry keys for the app pool.</span></span>

  <span data-ttu-id="20095-143">En las instalaciones independientes de IIS que no son de granja de servidores web, puede usar el [script de PowerShell Provision-AutoGenKeys.ps1 de protección de datos](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) para cada grupo de aplicaciones usado con una aplicación de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="20095-143">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="20095-144">Este script crea una clave del Registro en el registro HKLM que solo es accesible para la cuenta de proceso de trabajo del grupo de aplicaciones de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="20095-144">This script creates a Registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="20095-145">Las claves se cifran en reposo mediante DPAPI con una clave de equipo.</span><span class="sxs-lookup"><span data-stu-id="20095-145">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="20095-146">En escenarios de granja de servidores web, una aplicación puede configurarse para usar una ruta de acceso UNC con el fin de almacenar su conjunto de claves de protección de datos.</span><span class="sxs-lookup"><span data-stu-id="20095-146">In web farm scenarios, an app can be configured to use a UNC path to store its Data Protection key ring.</span></span> <span data-ttu-id="20095-147">De forma predeterminada, las claves no se cifran.</span><span class="sxs-lookup"><span data-stu-id="20095-147">By default, the keys aren't encrypted.</span></span> <span data-ttu-id="20095-148">Asegúrese de que los permisos de archivo de un recurso compartido de red se limitan a la cuenta de Windows en la que se ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="20095-148">Ensure that the file permissions for the network share are limited to the Windows account that the app runs under.</span></span> <span data-ttu-id="20095-149">Puede usar un certificado X509 para proteger las claves en reposo.</span><span class="sxs-lookup"><span data-stu-id="20095-149">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="20095-150">Considere un mecanismo que permita a los usuarios cargar certificados.</span><span class="sxs-lookup"><span data-stu-id="20095-150">Consider a mechanism to allow users to upload certificates.</span></span> <span data-ttu-id="20095-151">coloque los certificados en el almacén de certificados de confianza del usuario y asegúrese de que están disponibles en todos los equipos en los que se ejecuta la aplicación del usuario.</span><span class="sxs-lookup"><span data-stu-id="20095-151">Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="20095-152">Para obtener más información, vea <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="20095-152">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>

* <span data-ttu-id="20095-153">**Configurar el grupo de aplicaciones de IIS para cargar el perfil de usuario**</span><span class="sxs-lookup"><span data-stu-id="20095-153">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="20095-154">Esta opción está en la sección **Modelo de proceso**, en la **Configuración avanzada** del grupo de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="20095-154">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="20095-155">Establezca **Cargar perfil de usuario** en `True`.</span><span class="sxs-lookup"><span data-stu-id="20095-155">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="20095-156">Cuando se establece en `True`, las claves se almacenan en el directorio del perfil de usuario y se protegen mediante DPAPI con una clave específica de la cuenta de usuario.</span><span class="sxs-lookup"><span data-stu-id="20095-156">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="20095-157">Las claves se conservan en la carpeta `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys`.</span><span class="sxs-lookup"><span data-stu-id="20095-157">Keys are persisted to the `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` folder.</span></span>

  <span data-ttu-id="20095-158">También se debe habilitar el [atributo `setProfileEnvironment`](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) del grupo de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="20095-158">The app pool's [`setProfileEnvironment` attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="20095-159">El valor predeterminado de `setProfileEnvironment` es `true`.</span><span class="sxs-lookup"><span data-stu-id="20095-159">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="20095-160">En algunos escenarios (por ejemplo, SO Windows), `setProfileEnvironment` está establecido en `false`.</span><span class="sxs-lookup"><span data-stu-id="20095-160">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="20095-161">Si las claves no se almacenan en el directorio del perfil de usuario como se esperaba:</span><span class="sxs-lookup"><span data-stu-id="20095-161">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="20095-162">Vaya a la carpeta `%windir%/system32/inetsrv/config`.</span><span class="sxs-lookup"><span data-stu-id="20095-162">Navigate to the `%windir%/system32/inetsrv/config` folder.</span></span>
  1. <span data-ttu-id="20095-163">Abra el archivo `applicationHost.config` .</span><span class="sxs-lookup"><span data-stu-id="20095-163">Open the `applicationHost.config` file.</span></span>
  1. <span data-ttu-id="20095-164">Busque el elemento `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .</span><span class="sxs-lookup"><span data-stu-id="20095-164">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="20095-165">Confirme que el atributo `setProfileEnvironment` no está presente, que adopta de forma predeterminada el valor `true`, o establezca explícitamente el valor del atributo en `true`.</span><span class="sxs-lookup"><span data-stu-id="20095-165">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="20095-166">**Usar el sistema de archivos como un almacén de conjunto de claves**</span><span class="sxs-lookup"><span data-stu-id="20095-166">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="20095-167">Ajuste el código de la aplicación para [usar el sistema de archivos como un almacén de conjunto de claves](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="20095-167">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="20095-168">Use un certificado X509 para proteger el conjunto de claves y asegúrese de que sea un certificado de confianza.</span><span class="sxs-lookup"><span data-stu-id="20095-168">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="20095-169">Si es un certificado autofirmado, colóquelo en el almacén raíz de confianza.</span><span class="sxs-lookup"><span data-stu-id="20095-169">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="20095-170">Cuando se usa IIS en una granja de servidores web:</span><span class="sxs-lookup"><span data-stu-id="20095-170">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="20095-171">Use un recurso compartido de archivos al que puedan acceder todos los equipos.</span><span class="sxs-lookup"><span data-stu-id="20095-171">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="20095-172">Implemente un certificado X509 en cada equipo.</span><span class="sxs-lookup"><span data-stu-id="20095-172">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="20095-173">Configure la [protección de datos en el código](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="20095-173">Configure [Data Protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="20095-174">**Establecimiento de una directiva a nivel de equipo para la protección de datos**</span><span class="sxs-lookup"><span data-stu-id="20095-174">**Set a machine-wide policy for Data Protection**</span></span>

  <span data-ttu-id="20095-175">El sistema de protección de datos tiene compatibilidad limitada con el establecimiento de una [directiva a nivel de equipo](xref:security/data-protection/configuration/machine-wide-policy) predeterminada para todas las aplicaciones que usan las API de protección de datos.</span><span class="sxs-lookup"><span data-stu-id="20095-175">The Data Protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="20095-176">Para obtener más información, vea <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="20095-176">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="20095-177">Configuración de IIS</span><span class="sxs-lookup"><span data-stu-id="20095-177">IIS configuration</span></span>

<span data-ttu-id="20095-178">**Sistemas operativos de servidor Windows**</span><span class="sxs-lookup"><span data-stu-id="20095-178">**Windows Server operating systems**</span></span>

<span data-ttu-id="20095-179">Habilite el rol de servidor **Servidor web (IIS)** y establezca los servicios de rol.</span><span class="sxs-lookup"><span data-stu-id="20095-179">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="20095-180">Use el asistente **Agregar roles y características** del menú **Administrar** o el vínculo de **Administrador del servidor**.</span><span class="sxs-lookup"><span data-stu-id="20095-180">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="20095-181">En el paso **Roles de servidor**, active la casilla de **Servidor web (IIS)** .</span><span class="sxs-lookup"><span data-stu-id="20095-181">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![El rol Servidor web (IIS) se activa en el paso Seleccionar roles de servidor.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="20095-183">Después del paso **Características**, el paso **Servicios de rol** se carga para el servidor Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="20095-183">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="20095-184">Seleccione los servicios de rol IIS que quiera o acepte los servicios de rol predeterminados proporcionados.</span><span class="sxs-lookup"><span data-stu-id="20095-184">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Los servicios de rol predeterminados se activan en el paso Seleccionar servicios de rol.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="20095-186">**Autenticación de Windows (opcional)**</span><span class="sxs-lookup"><span data-stu-id="20095-186">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="20095-187">Para habilitar Autenticación de Windows, expanda los nodos siguientes: **Servidor web** > **Seguridad**.</span><span class="sxs-lookup"><span data-stu-id="20095-187">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="20095-188">Seleccione la característica **Autenticación de Windows**.</span><span class="sxs-lookup"><span data-stu-id="20095-188">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="20095-189">Para más información, consulte [Autenticación de Windows `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) y [Configuración de la autenticación de Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="20095-189">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="20095-190">**WebSockets (opcional)**</span><span class="sxs-lookup"><span data-stu-id="20095-190">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="20095-191">WebSockets es compatible con ASP.NET Core 1.1 o posterior.</span><span class="sxs-lookup"><span data-stu-id="20095-191">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="20095-192">Para habilitar WebSockets, expanda los nodos siguientes: **Servidor web** > **Desarrollo de aplicaciones**.</span><span class="sxs-lookup"><span data-stu-id="20095-192">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="20095-193">Seleccione la característica **Protocolo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="20095-193">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="20095-194">Para más información, vea [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="20095-194">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="20095-195">Continúe con el paso **Confirmación** para instalar el rol y los servicios de servidor web.</span><span class="sxs-lookup"><span data-stu-id="20095-195">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="20095-196">No es necesario reiniciar el servidor ni IIS después de instalar el rol **Servidor web (IIS)** .</span><span class="sxs-lookup"><span data-stu-id="20095-196">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="20095-197">**Sistemas operativos de escritorio Windows**</span><span class="sxs-lookup"><span data-stu-id="20095-197">**Windows desktop operating systems**</span></span>

<span data-ttu-id="20095-198">Habilite **Consola de administración de IIS** y **Servicios World Wide Web**.</span><span class="sxs-lookup"><span data-stu-id="20095-198">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="20095-199">Vaya a **Panel de control** > **Programas** > **Programas y características** > **Activar o desactivar las características de Windows** (lado izquierdo de la pantalla).</span><span class="sxs-lookup"><span data-stu-id="20095-199">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="20095-200">Abra el nodo **Internet Information Services**.</span><span class="sxs-lookup"><span data-stu-id="20095-200">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="20095-201">Abra el nodo **Herramientas de administración web**.</span><span class="sxs-lookup"><span data-stu-id="20095-201">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="20095-202">Active la casilla de **Consola de administración de IIS**.</span><span class="sxs-lookup"><span data-stu-id="20095-202">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="20095-203">Active la casilla de **Servicios World Wide Web**.</span><span class="sxs-lookup"><span data-stu-id="20095-203">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="20095-204">Acepte las características predeterminadas de **Servicios World Wide Web** o personalice las características de IIS.</span><span class="sxs-lookup"><span data-stu-id="20095-204">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="20095-205">**Autenticación de Windows (opcional)**</span><span class="sxs-lookup"><span data-stu-id="20095-205">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="20095-206">Para habilitar Autenticación de Windows, expanda los nodos siguientes: **Servicios World Wide Web** > **Seguridad**.</span><span class="sxs-lookup"><span data-stu-id="20095-206">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="20095-207">Seleccione la característica **Autenticación de Windows**.</span><span class="sxs-lookup"><span data-stu-id="20095-207">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="20095-208">Para más información, consulte [Autenticación de Windows `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) y [Configuración de la autenticación de Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="20095-208">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="20095-209">**WebSockets (opcional)**</span><span class="sxs-lookup"><span data-stu-id="20095-209">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="20095-210">WebSockets es compatible con ASP.NET Core 1.1 o posterior.</span><span class="sxs-lookup"><span data-stu-id="20095-210">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="20095-211">Para habilitar WebSockets, expanda los nodos siguientes: **Servicios World Wide Web** > **Características de desarrollo de aplicaciones**.</span><span class="sxs-lookup"><span data-stu-id="20095-211">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="20095-212">Seleccione la característica **Protocolo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="20095-212">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="20095-213">Para más información, vea [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="20095-213">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="20095-214">Si la instalación de IIS requiere un reinicio, reinicie el sistema.</span><span class="sxs-lookup"><span data-stu-id="20095-214">If the IIS installation requires a restart, restart the system.</span></span>

![Consola de administración de IIS y Servicios World Wide Web se activan en Características de Windows.](index/_static/windows-features-win10.png)

## <a name="virtual-directories"></a><span data-ttu-id="20095-216">Directorios virtuales</span><span class="sxs-lookup"><span data-stu-id="20095-216">Virtual Directories</span></span>

<span data-ttu-id="20095-217">Los [directorios virtuales de IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) no son compatibles con aplicaciones ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="20095-217">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="20095-218">Una aplicación puede hospedarse como [subaplicación](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="20095-218">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="20095-219">Subaplicaciones</span><span class="sxs-lookup"><span data-stu-id="20095-219">Sub-applications</span></span>

<span data-ttu-id="20095-220">Se puede hospedar una aplicación ASP.NET Core como una [subaplicación IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="20095-220">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="20095-221">La ruta de acceso de la subaplicación se convierte en parte de la dirección URL de la aplicación raíz.</span><span class="sxs-lookup"><span data-stu-id="20095-221">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="20095-222">Los vínculos a los recursos estáticos dentro de la aplicación secundaria deben utilizar una notación de una tilde con una barra diagonal (`~/`).</span><span class="sxs-lookup"><span data-stu-id="20095-222">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="20095-223">La notación de tilde barra diagonal desencadena un [asistente de etiquetas](xref:mvc/views/tag-helpers/intro) para anteponer la ruta de acceso de la aplicación secundaria al vínculo relativo representado.</span><span class="sxs-lookup"><span data-stu-id="20095-223">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="20095-224">Para una aplicación secundaria en `/subapp_path`, una imagen vinculada con `src="~/image.png"` se representa como `src="/subapp_path/image.png"`.</span><span class="sxs-lookup"><span data-stu-id="20095-224">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="20095-225">El middleware de archivos estáticos de la aplicación raíz no procesa la solicitud de archivo estático.</span><span class="sxs-lookup"><span data-stu-id="20095-225">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="20095-226">La solicitud se procesa mediante el middleware de archivos estáticos de la aplicación secundaria.</span><span class="sxs-lookup"><span data-stu-id="20095-226">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="20095-227">Si el atributo `src` de un recurso estático se establece en una ruta de acceso absoluta (por ejemplo, `src="/image.png"`), el vínculo se representa sin la base de la ruta de acceso de la aplicación secundaria.</span><span class="sxs-lookup"><span data-stu-id="20095-227">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="20095-228">El middleware de archivos estáticos de la aplicación raíz intenta atender al recurso desde el [web root](xref:fundamentals/index#web-root), lo que resulta en una respuesta *404 - No encontrado* a menos que el recurso estático esté disponible desde la aplicación raíz.</span><span class="sxs-lookup"><span data-stu-id="20095-228">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="20095-229">Para hospedar una aplicación ASP.NET Core como aplicación secundaria en otra aplicación ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="20095-229">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="20095-230">Establezca un grupo de aplicaciones para la aplicación secundaria.</span><span class="sxs-lookup"><span data-stu-id="20095-230">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="20095-231">Establezca **Versión de .NET CLR**  en **Sin código administrado**  porque Core Common Language Runtime (CoreCLR) para .NET Core se arranca para hospedar la aplicación en el proceso de trabajo, no el CLR de escritorio (.NET CLR).</span><span class="sxs-lookup"><span data-stu-id="20095-231">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="20095-232">Agregue el sitio raíz en el Administrador de IIS con la aplicación secundaria en una carpeta en el sitio raíz.</span><span class="sxs-lookup"><span data-stu-id="20095-232">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="20095-233">Haga clic con el botón derecho en la carpeta de la aplicación secundaria en el Administrador de IIS y seleccione **Convertir en aplicación**.</span><span class="sxs-lookup"><span data-stu-id="20095-233">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="20095-234">En el cuadro de diálogo **Agregar aplicación**, use el botón **Seleccionar** en **Grupo de aplicaciones** para asignar el grupo de aplicaciones que ha creado para la aplicación secundaria.</span><span class="sxs-lookup"><span data-stu-id="20095-234">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="20095-235">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="20095-235">Select **OK**.</span></span>

<span data-ttu-id="20095-236">La asignación de un grupo de aplicaciones independiente de la aplicación secundaria es un requisito cuando se utiliza el modelo de hospedaje en proceso.</span><span class="sxs-lookup"><span data-stu-id="20095-236">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="20095-237">Para más información sobre el modelo de hospedaje en proceso y cómo configurar el módulo de ASP.NET Core, consulte <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="20095-237">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="application-pools"></a><span data-ttu-id="20095-238">Grupos de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="20095-238">Application Pools</span></span>

<span data-ttu-id="20095-239">El aislamiento de los grupos de aplicaciones se determinan mediante el modelo de hospedaje:</span><span class="sxs-lookup"><span data-stu-id="20095-239">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="20095-240">Hospedaje dentro de proceso: es necesario que las aplicaciones se ejecuten en grupos de aplicaciones distintos.</span><span class="sxs-lookup"><span data-stu-id="20095-240">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="20095-241">Hospedaje fuera de proceso: nuestra recomendación es aislar las aplicaciones entre sí ejecutándolas en su propio grupo de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="20095-241">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="20095-242">El valor predeterminado del cuadro de diálogo **Agregar sitio web** de IIS es un único grupo de aplicaciones por aplicación.</span><span class="sxs-lookup"><span data-stu-id="20095-242">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="20095-243">Cuando se proporciona el **Nombre del sitio**, el texto se transfiere automáticamente al cuadro de texto **Grupo de aplicaciones**.</span><span class="sxs-lookup"><span data-stu-id="20095-243">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="20095-244">Al agregar el sitio se crea un grupo de aplicaciones con el nombre del sitio.</span><span class="sxs-lookup"><span data-stu-id="20095-244">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="20095-245">Identity del grupo de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="20095-245">Application Pool Identity</span></span>

<span data-ttu-id="20095-246">Una cuenta de identidad del grupo de aplicaciones permite ejecutar una aplicación en una cuenta única sin tener que crear ni administrar dominios o cuentas locales.</span><span class="sxs-lookup"><span data-stu-id="20095-246">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="20095-247">En IIS 8.0 o versiones posteriores, el proceso de trabajo de administración de IIS (WAS) crea una cuenta virtual con el nombre del nuevo grupo de aplicaciones y ejecuta los procesos de trabajo del grupo de aplicaciones en esta cuenta de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="20095-247">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="20095-248">En la Consola de administración de IIS, en la opción **Configuración avanzada** del grupo de aplicaciones, asegúrese de que **Identity** esté establecido para usar `ApplicationPoolIdentity`:</span><span class="sxs-lookup"><span data-stu-id="20095-248">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use `ApplicationPoolIdentity`:</span></span>

![Cuadro de diálogo Configuración avanzada del grupo de aplicaciones](index/_static/apppool-identity.png)

<span data-ttu-id="20095-250">El proceso de administración de IIS crea un identificador seguro con el nombre del grupo de aplicaciones en el sistema de seguridad de Windows.</span><span class="sxs-lookup"><span data-stu-id="20095-250">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="20095-251">Los recursos se pueden proteger mediante esta identidad.</span><span class="sxs-lookup"><span data-stu-id="20095-251">Resources can be secured using this identity.</span></span> <span data-ttu-id="20095-252">Sin embargo, no es una cuenta de usuario real ni se muestra en la consola de administración de usuario de Windows.</span><span class="sxs-lookup"><span data-stu-id="20095-252">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="20095-253">Si el proceso de trabajo de IIS requiere acceso con privilegios elevados a la aplicación, modifique la lista de control de acceso (ACL) del directorio que contiene la aplicación:</span><span class="sxs-lookup"><span data-stu-id="20095-253">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="20095-254">Abra el Explorador de Windows y vaya al directorio.</span><span class="sxs-lookup"><span data-stu-id="20095-254">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="20095-255">Haga clic con el botón derecho en el directorio y seleccione **Propiedades**.</span><span class="sxs-lookup"><span data-stu-id="20095-255">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="20095-256">En la pestaña **Seguridad**, haga clic en el botón **Editar** y en el botón **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="20095-256">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="20095-257">Haga clic en el botón **Ubicaciones** y asegúrese de seleccionar el sistema.</span><span class="sxs-lookup"><span data-stu-id="20095-257">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="20095-258">Escriba el formato `IIS AppPool\{APP POOL NAME}`, donde el marcador de posición `{APP POOL NAME}` es el nombre del grupo de aplicaciones, en la sección **Escribir los nombres de objeto para seleccionar**.</span><span class="sxs-lookup"><span data-stu-id="20095-258">Enter `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, in **Enter the object names to select** area.</span></span> <span data-ttu-id="20095-259">Haga clic en el botón **Comprobar nombres**.</span><span class="sxs-lookup"><span data-stu-id="20095-259">Select the **Check Names** button.</span></span> <span data-ttu-id="20095-260">Para *DefaultAppPool* compruebe los nombres con `IIS AppPool\DefaultAppPool`.</span><span class="sxs-lookup"><span data-stu-id="20095-260">For the *DefaultAppPool* check the names using `IIS AppPool\DefaultAppPool`.</span></span> <span data-ttu-id="20095-261">Cuando el botón **Comprobar nombres** está seleccionado, un valor de `DefaultAppPool` se indica en el área de los nombres de objeto.</span><span class="sxs-lookup"><span data-stu-id="20095-261">When the **Check Names** button is selected, a value of `DefaultAppPool` is indicated in the object names area.</span></span> <span data-ttu-id="20095-262">No es posible escribir el nombre del grupo de aplicaciones directamente en el área de los nombres de objeto.</span><span class="sxs-lookup"><span data-stu-id="20095-262">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="20095-263">Use el formato `IIS AppPool\{APP POOL NAME}`, donde el marcador de posición `{APP POOL NAME}` es el nombre del grupo de aplicaciones, al comprobar el nombre del objeto.</span><span class="sxs-lookup"><span data-stu-id="20095-263">Use the `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, when checking for the object name.</span></span>

   ![Cuadro de diálogo Seleccionar usuarios o grupos para la carpeta de la aplicación: el nombre del grupo de aplicaciones de "DefaultAppPool" se anexa al "IIS AppPool\" en el área de los nombres de objeto antes de seleccionar "Comprobar nombres".](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="20095-265">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="20095-265">Select **OK**.</span></span>

   ![Cuadro de diálogo Seleccionar usuarios o grupos para la carpeta de la aplicación: después de seleccionar "Comprobar nombres", el nombre del objeto "DefaultAppPool" se muestra en el área de los nombres de objeto.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="20095-267">Los permisos de lectura y ejecución se deben conceder de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="20095-267">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="20095-268">Proporcione permisos adicionales según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="20095-268">Provide additional permissions as needed.</span></span>

<span data-ttu-id="20095-269">El acceso también se puede conceder mediante un símbolo del sistema con la herramienta **ICACLS**.</span><span class="sxs-lookup"><span data-stu-id="20095-269">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="20095-270">En el siguiente comando se usa *DefaultAppPool* como ejemplo:</span><span class="sxs-lookup"><span data-stu-id="20095-270">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="20095-271">Para más información, consulte el tema [icacls](/windows-server/administration/windows-commands/icacls).</span><span class="sxs-lookup"><span data-stu-id="20095-271">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="20095-272">Compatibilidad con HTTP/2</span><span class="sxs-lookup"><span data-stu-id="20095-272">HTTP/2 support</span></span>

<span data-ttu-id="20095-273">[HTTP/2](https://httpwg.org/specs/rfc7540.html) es compatible con ASP.NET Core en los escenarios de implementación de IIS siguientes:</span><span class="sxs-lookup"><span data-stu-id="20095-273">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="20095-274">En proceso</span><span class="sxs-lookup"><span data-stu-id="20095-274">In-process</span></span>
  * <span data-ttu-id="20095-275">Windows Server 2016/Windows 10 o posterior; IIS 10 o posterior</span><span class="sxs-lookup"><span data-stu-id="20095-275">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="20095-276">Conexión con TLS 1.2 o una versión posterior</span><span class="sxs-lookup"><span data-stu-id="20095-276">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="20095-277">Fuera de proceso</span><span class="sxs-lookup"><span data-stu-id="20095-277">Out-of-process</span></span>
  * <span data-ttu-id="20095-278">Windows Server 2016/Windows 10 o posterior; IIS 10 o posterior</span><span class="sxs-lookup"><span data-stu-id="20095-278">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="20095-279">Las conexiones de servidor perimetral de acceso público usan HTTP/2, pero la conexión de proxy inverso al [servidor de Kestrel](xref:fundamentals/servers/kestrel) usa HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="20095-279">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="20095-280">Conexión con TLS 1.2 o una versión posterior</span><span class="sxs-lookup"><span data-stu-id="20095-280">TLS 1.2 or later connection</span></span>

<span data-ttu-id="20095-281">Para una implementación dentro del proceso cuando se establece una conexión HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) notifica `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="20095-281">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="20095-282">Para una implementación fuera del proceso cuando se establece una conexión HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) notifica `HTTP/1.1`.</span><span class="sxs-lookup"><span data-stu-id="20095-282">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="20095-283">Para obtener más información sobre los modelos de hospedaje en proceso y fuera de proceso, consulte <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="20095-283">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="20095-284">HTTP/2 está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="20095-284">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="20095-285">Las conexiones vuelven a HTTP/1.1 si no se establece una conexión HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="20095-285">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="20095-286">Para más información sobre la configuración HTTP/2 con implementaciones de IIS, vea [HTTP/2 en IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="20095-286">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="20095-287">Solicitudes CORS de preflight</span><span class="sxs-lookup"><span data-stu-id="20095-287">CORS preflight requests</span></span>

<span data-ttu-id="20095-288">*Esta sección solo se aplica a las aplicaciones de ASP.NET Core que tienen como destino .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="20095-288">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="20095-289">Para una aplicación de ASP.NET Core que tiene como destino .NET Framework, las solicitudes OPTIONS no se pasan a la aplicación de forma predeterminada en IIS.</span><span class="sxs-lookup"><span data-stu-id="20095-289">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="20095-290">Para obtener información sobre cómo configurar los controladores de IIS de la aplicación en `web.config` para pasar las solicitudes OPTIONS, consulte [Habilitar solicitudes entre orígenes en ASP.NET Web API 2: Cómo funciona la CORS](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span><span class="sxs-lookup"><span data-stu-id="20095-290">To learn how to configure the app's IIS handlers in `web.config` to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="20095-291">Módulo de inicialización de aplicaciones y tiempo de espera de inactividad</span><span class="sxs-lookup"><span data-stu-id="20095-291">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="20095-292">Cuando se hospeda en IIS mediante la versión 2 del módulo de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="20095-292">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="20095-293">[Módulo de inicialización de aplicaciones](#application-initialization-module): las aplicaciones hospedadas [dentro de proceso](xref:host-and-deploy/iis/in-process-hosting) o [fuera de proceso](xref:host-and-deploy/iis/out-of-process-hosting) se pueden configurar para iniciarse de forma automática en un reinicio de proceso de trabajo o un reinicio de servidor.</span><span class="sxs-lookup"><span data-stu-id="20095-293">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) or [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="20095-294">[Tiempo de espera de inactividad](#idle-timeout): Las aplicaciones hospedadas [dentro de proceso](xref:host-and-deploy/iis/in-process-hosting) se pueden configurar para que no tengan tiempo de espera durante períodos de inactividad.</span><span class="sxs-lookup"><span data-stu-id="20095-294">[Idle Timeout](#idle-timeout): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) can be configured not to time out during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="20095-295">Módulo de inicialización de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="20095-295">Application Initialization Module</span></span>

<span data-ttu-id="20095-296">*Se aplica a las aplicaciones hospedadas dentro de proceso y fuera de proceso.*</span><span class="sxs-lookup"><span data-stu-id="20095-296">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="20095-297">[Inicialización de aplicaciones de IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) es una característica de IIS que envía una solicitud HTTP a la aplicación al iniciarse o reciclarse el grupo de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="20095-297">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="20095-298">La solicitud desencadena el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="20095-298">The request triggers the app to start.</span></span> <span data-ttu-id="20095-299">De forma predeterminada, IIS emite una solicitud a la dirección URL raíz de la aplicación (`/`) para inicializar esta (consulte los [recursos adicionales](#application-initialization-module-and-idle-timeout-additional-resources) para más detalles sobre la configuración).</span><span class="sxs-lookup"><span data-stu-id="20095-299">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="20095-300">Confirme que la característica de rol Inicialización de aplicaciones de IIS está habilitada:</span><span class="sxs-lookup"><span data-stu-id="20095-300">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="20095-301">En Windows 7 o sistemas de escritorio posteriores cuando se usa IIS localmente:</span><span class="sxs-lookup"><span data-stu-id="20095-301">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="20095-302">Vaya a **Panel de control** > **Programas** > **Programas y características** > **Activar o desactivar las características de Windows** (lado izquierdo de la pantalla).</span><span class="sxs-lookup"><span data-stu-id="20095-302">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="20095-303">Abra **Internet Information Services** > **Servicios World Wide Web** > **Características de desarrollo de aplicaciones**.</span><span class="sxs-lookup"><span data-stu-id="20095-303">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="20095-304">Active la casilla de **Inicialización de aplicaciones**.</span><span class="sxs-lookup"><span data-stu-id="20095-304">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="20095-305">En Windows Server 2008 R2 o posterior:</span><span class="sxs-lookup"><span data-stu-id="20095-305">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="20095-306">Abra el **Asistente para agregar roles y características**.</span><span class="sxs-lookup"><span data-stu-id="20095-306">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="20095-307">En el panel **Seleccionar servicios de rol**, abra el nodo **Desarrollo de aplicaciones**.</span><span class="sxs-lookup"><span data-stu-id="20095-307">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="20095-308">Active la casilla de **Inicialización de aplicaciones**.</span><span class="sxs-lookup"><span data-stu-id="20095-308">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="20095-309">Use cualquiera de los enfoques siguientes para habilitar el módulo de inicialización de aplicaciones para el sitio:</span><span class="sxs-lookup"><span data-stu-id="20095-309">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="20095-310">Mediante el Administrador de IIS:</span><span class="sxs-lookup"><span data-stu-id="20095-310">Using IIS Manager:</span></span>

  1. <span data-ttu-id="20095-311">Seleccione **Grupos de aplicaciones** en el panel **Conexiones**.</span><span class="sxs-lookup"><span data-stu-id="20095-311">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="20095-312">Haga clic con el botón derecho en el grupo de aplicaciones de la aplicación en la lista y seleccione **Configuración avanzada**.</span><span class="sxs-lookup"><span data-stu-id="20095-312">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="20095-313">El valor predeterminado de **Modo de inicio** es `OnDemand`.</span><span class="sxs-lookup"><span data-stu-id="20095-313">The default **Start Mode** is `OnDemand`.</span></span> <span data-ttu-id="20095-314">Establezca **Modo de inicio** en `AlwaysRunning`.</span><span class="sxs-lookup"><span data-stu-id="20095-314">Set the **Start Mode** to `AlwaysRunning`.</span></span> <span data-ttu-id="20095-315">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="20095-315">Select **OK**.</span></span>
  1. <span data-ttu-id="20095-316">Abra el nodo **Sitios** en el panel **Conexiones**.</span><span class="sxs-lookup"><span data-stu-id="20095-316">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="20095-317">Haga clic con el botón derecho en la aplicación y seleccione **Administrar sitio web** > **Configuración avanzada**.</span><span class="sxs-lookup"><span data-stu-id="20095-317">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="20095-318">El valor predeterminado de **Carga previa activada** es `False`.</span><span class="sxs-lookup"><span data-stu-id="20095-318">The default **Preload Enabled** setting is `False`.</span></span> <span data-ttu-id="20095-319">Establezca **Carga previa activada** en `True`.</span><span class="sxs-lookup"><span data-stu-id="20095-319">Set **Preload Enabled** to `True`.</span></span> <span data-ttu-id="20095-320">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="20095-320">Select **OK**.</span></span>

* <span data-ttu-id="20095-321">Mediante `web.config`, agregue el elemento `<applicationInitialization>` con `doAppInitAfterRestart` establecido en `true` a los elementos `<system.webServer>` del archivo `web.config` de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="20095-321">Using `web.config`, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's `web.config` file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="20095-322">Tiempo de espera de inactividad</span><span class="sxs-lookup"><span data-stu-id="20095-322">Idle Timeout</span></span>

<span data-ttu-id="20095-323">*Solo se aplica a las aplicaciones hospedadas dentro de proceso.*</span><span class="sxs-lookup"><span data-stu-id="20095-323">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="20095-324">Para evitar la inactividad en la aplicación, establezca el tiempo de espera de inactividad del grupo de aplicaciones mediante el Administrador de IIS:</span><span class="sxs-lookup"><span data-stu-id="20095-324">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="20095-325">Seleccione **Grupos de aplicaciones** en el panel **Conexiones**.</span><span class="sxs-lookup"><span data-stu-id="20095-325">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="20095-326">Haga clic con el botón derecho en el grupo de aplicaciones de la aplicación en la lista y seleccione **Configuración avanzada**.</span><span class="sxs-lookup"><span data-stu-id="20095-326">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="20095-327">El valor predeterminado de **Tiempo de inactividad (minutos)**  es `20` minutos.</span><span class="sxs-lookup"><span data-stu-id="20095-327">The default **Idle Time-out (minutes)** is `20` minutes.</span></span> <span data-ttu-id="20095-328">Establezca **Tiempo de inactividad (minutos)** en `0` (cero).</span><span class="sxs-lookup"><span data-stu-id="20095-328">Set the **Idle Time-out (minutes)** to `0` (zero).</span></span> <span data-ttu-id="20095-329">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="20095-329">Select **OK**.</span></span>
1. <span data-ttu-id="20095-330">Desactive y vuelva a activar el proceso de trabajo.</span><span class="sxs-lookup"><span data-stu-id="20095-330">Recycle the worker process.</span></span>

<span data-ttu-id="20095-331">Para evitar que las aplicaciones hospedadas [fuera de proceso](xref:host-and-deploy/iis/out-of-process-hosting) agoten el tiempo de espera, use cualquiera de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="20095-331">To prevent apps hosted [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="20095-332">Haga ping a la aplicación desde un servicio externo con el fin de mantenerla funcionando.</span><span class="sxs-lookup"><span data-stu-id="20095-332">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="20095-333">Si la aplicación solo hospeda servicios en segundo plano, evite el hospedaje de IIS y use un [servicio de Windows para hospedar la aplicación de ASP.NET Core](xref:host-and-deploy/windows-service).</span><span class="sxs-lookup"><span data-stu-id="20095-333">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="20095-334">Recursos adicionales del módulo de inicialización de aplicaciones y del tiempo de espera de inactividad</span><span class="sxs-lookup"><span data-stu-id="20095-334">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="20095-335">Inicialización de aplicaciones IIS 8.0</span><span class="sxs-lookup"><span data-stu-id="20095-335">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="20095-336">[Inicialización de aplicaciones `<applicationInitialization>`](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="20095-336">[Application Initialization `<applicationInitialization>`](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="20095-337">[Configuración del modelo de proceso para un grupo de aplicaciones `<processModel>`](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="20095-337">[Process Model Settings for an Application Pool `<processModel>`](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="20095-338">Ubicaciones del módulo, el esquema y el archivo de configuración</span><span class="sxs-lookup"><span data-stu-id="20095-338">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="20095-339">Module</span><span class="sxs-lookup"><span data-stu-id="20095-339">Module</span></span>

<span data-ttu-id="20095-340">**IIS (x86/amd64)** :</span><span class="sxs-lookup"><span data-stu-id="20095-340">**IIS (x86/amd64)**:</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="20095-341">**IIS Express (x86/amd64)** :</span><span class="sxs-lookup"><span data-stu-id="20095-341">**IIS Express (x86/amd64)**:</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="20095-342">Schema</span><span class="sxs-lookup"><span data-stu-id="20095-342">Schema</span></span>

<span data-ttu-id="20095-343">**IIS**</span><span class="sxs-lookup"><span data-stu-id="20095-343">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="20095-344">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="20095-344">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="20095-345">Configuración</span><span class="sxs-lookup"><span data-stu-id="20095-345">Configuration</span></span>

<span data-ttu-id="20095-346">**IIS**</span><span class="sxs-lookup"><span data-stu-id="20095-346">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="20095-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="20095-347">**IIS Express**</span></span>

* <span data-ttu-id="20095-348">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="20095-348">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="20095-349">CLI de *iisexpress.exe*: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="20095-349">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="20095-350">Los archivos se pueden encontrar mediante la búsqueda de `aspnetcore` en el archivo `applicationHost.config`.</span><span class="sxs-lookup"><span data-stu-id="20095-350">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>
