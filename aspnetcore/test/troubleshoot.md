---
title: Solución de problemas y depuración de proyectos de ASP.NET Core
author: Rick-Anderson
description: Conozca y solucione advertencias y errores en proyectos de ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
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
uid: test/troubleshoot
ms.openlocfilehash: 8e6c640cd775e5d4cbe6e34c1cecc391baf57344
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059577"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="00293-103">Solución de problemas y depuración de proyectos de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="00293-103">Troubleshoot and debug ASP.NET Core projects</span></span>

<span data-ttu-id="00293-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="00293-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="00293-105">En los vínculos siguientes se proporciona una guía de solución de problemas:</span><span class="sxs-lookup"><span data-stu-id="00293-105">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="00293-106">Conferencia NDC (Londres, 2018): diagnóstico de problemas en aplicaciones ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="00293-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="00293-107">Blog de ASP.NET: solución de problemas de rendimiento de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="00293-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="00293-108">Advertencias del SDK de .NET Core</span><span class="sxs-lookup"><span data-stu-id="00293-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="00293-109">Se instalaron las versiones de 32 y 64 bits del SDK de .NET Core</span><span class="sxs-lookup"><span data-stu-id="00293-109">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="00293-110">En el cuadro de diálogo **Nuevo proyecto** de ASP.NET Core, puede ver la siguiente advertencia:</span><span class="sxs-lookup"><span data-stu-id="00293-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="00293-111">Se instalaron las versiones de 32 y 64 bits del SDK de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="00293-111">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="00293-112">Solo se mostrarán las plantillas de las versiones de 64 bits instaladas en "C:\\Archivos de programa\\dotnet\\sdk\\".</span><span class="sxs-lookup"><span data-stu-id="00293-112">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="00293-113">Esta advertencia aparece si se instalan las versiones de 32 bits (x86) y 64 bits (x64) del [SDK de .NET Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="00293-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) are installed.</span></span> <span data-ttu-id="00293-114">Entre los motivos habituales por los que pueden estar instaladas ambas versiones se encuentran los siguientes:</span><span class="sxs-lookup"><span data-stu-id="00293-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="00293-115">En un principio, descargó el instalador del SDK de .NET Core con un equipo de 32 bits, pero después lo copió e instaló en un equipo de 64 bits.</span><span class="sxs-lookup"><span data-stu-id="00293-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="00293-116">Otra aplicación ha instalado el SDK de .NET Core de 32 bits.</span><span class="sxs-lookup"><span data-stu-id="00293-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="00293-117">Se ha descargado e instalado la versión incorrecta.</span><span class="sxs-lookup"><span data-stu-id="00293-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="00293-118">Desinstale el SDK de .NET Core de 32 bits para evitar que aparezca esta advertencia.</span><span class="sxs-lookup"><span data-stu-id="00293-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="00293-119">Desinstálelo en **Panel de control** > **Programas y características** > **Desinstalar o cambiar un programa**.</span><span class="sxs-lookup"><span data-stu-id="00293-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="00293-120">Si comprende por qué se produce la advertencia y sus implicaciones, puede omitirla.</span><span class="sxs-lookup"><span data-stu-id="00293-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="00293-121">El SDK de .NET Core está instalado en varias ubicaciones</span><span class="sxs-lookup"><span data-stu-id="00293-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="00293-122">En el cuadro de diálogo **Nuevo proyecto** de ASP.NET Core, puede ver la siguiente advertencia:</span><span class="sxs-lookup"><span data-stu-id="00293-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="00293-123">El SDK de .NET Core está instalado en varias ubicaciones.</span><span class="sxs-lookup"><span data-stu-id="00293-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="00293-124">Solo se mostrarán las plantillas de los SDK instalados en "C:\\Archivos de programa\\dotnet\\sdk\\".</span><span class="sxs-lookup"><span data-stu-id="00293-124">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="00293-125">Verá este mensaje si tiene al menos una instalación del SDK de .NET Core en un directorio fuera de *C:\\Archivos de programa\\dotnet\\sdk\\* .</span><span class="sxs-lookup"><span data-stu-id="00293-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="00293-126">Esto suele suceder si el SDK de .NET Core se ha implementado en un equipo mediante el método de copiar y pegar en lugar de con el instalador de MSI.</span><span class="sxs-lookup"><span data-stu-id="00293-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="00293-127">Desinstale todos los SDK y los entornos de ejecución de .NET Core de 32 bits para evitar que aparezca esta advertencia.</span><span class="sxs-lookup"><span data-stu-id="00293-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="00293-128">Desinstálelo en **Panel de control** > **Programas y características** > **Desinstalar o cambiar un programa**.</span><span class="sxs-lookup"><span data-stu-id="00293-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="00293-129">Si comprende por qué se produce la advertencia y sus implicaciones, puede omitirla.</span><span class="sxs-lookup"><span data-stu-id="00293-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="00293-130">No se ha detectado ningún SDK de .NET Core</span><span class="sxs-lookup"><span data-stu-id="00293-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="00293-131">En el cuadro de diálogo **Nuevo proyecto** de Visual Studio de ASP.NET Core, puede ver la siguiente advertencia:</span><span class="sxs-lookup"><span data-stu-id="00293-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="00293-132">No se ha detectado ningún SDK de .NET Core, asegúrese de que se incluyen en la variable de entorno `PATH`.</span><span class="sxs-lookup"><span data-stu-id="00293-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="00293-133">Al ejecutar un comando `dotnet`, la advertencia aparece como:</span><span class="sxs-lookup"><span data-stu-id="00293-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="00293-134">No se pudo encontrar ningún SDK de dotnet instalado.</span><span class="sxs-lookup"><span data-stu-id="00293-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="00293-135">Estas advertencias aparecen si la variable de entorno `PATH` no apunta a ningún SDK de .NET Core en el equipo.</span><span class="sxs-lookup"><span data-stu-id="00293-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="00293-136">Para resolver este problema:</span><span class="sxs-lookup"><span data-stu-id="00293-136">To resolve this problem:</span></span>

* <span data-ttu-id="00293-137">Instale el SDK de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="00293-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="00293-138">Obtenga el instalador más reciente en [Descargas de .NET](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="00293-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="00293-139">Compruebe que la variable de entorno `PATH` apunta a la ubicación en la que está instalado el SDK (`C:\Program Files\dotnet\` para 64 bits/x64 o `C:\Program Files (x86)\dotnet\` para 32 bits/x86).</span><span class="sxs-lookup"><span data-stu-id="00293-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="00293-140">Normalmente, el instalador del SDK establece el valor de `PATH`.</span><span class="sxs-lookup"><span data-stu-id="00293-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="00293-141">Instale siempre los mismos valores de bits de los SDK y los entornos de ejecución en el mismo equipo.</span><span class="sxs-lookup"><span data-stu-id="00293-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="00293-142">Falta el SDK después de instalar el conjunto de hospedaje de .NET Core</span><span class="sxs-lookup"><span data-stu-id="00293-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="00293-143">Al instalar el [conjunto de hospedaje de .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) se modifica el valor de `PATH` cuando instala el entorno de ejecución de .NET Core para que apunte a la versión de 32 bits (x86) de .NET Core (`C:\Program Files (x86)\dotnet\`).</span><span class="sxs-lookup"><span data-stu-id="00293-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="00293-144">Esto puede dar lugar a que falten SDK cuando se usa el comando `dotnet` de 32 bits (x86) de .NET Core ([No se ha detectado ningún SDK de .NET Core](#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="00293-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="00293-145">Para solucionar este problema, mueva `C:\Program Files\dotnet\` a una posición antes de `C:\Program Files (x86)\dotnet\` en `PATH`.</span><span class="sxs-lookup"><span data-stu-id="00293-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="00293-146">Obtención de datos de una aplicación</span><span class="sxs-lookup"><span data-stu-id="00293-146">Obtain data from an app</span></span>

<span data-ttu-id="00293-147">Si una aplicación es capaz de responder a las solicitudes, puede obtener los siguientes datos de la aplicación mediante middleware:</span><span class="sxs-lookup"><span data-stu-id="00293-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="00293-148">Solicitud: método, esquema, host, ruta de acceso base, ruta de acceso, cadena de consulta, encabezados</span><span class="sxs-lookup"><span data-stu-id="00293-148">Request: Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="00293-149">Conexión: dirección IP remota, puerto remoto, dirección IP local, puerto local, certificado de cliente</span><span class="sxs-lookup"><span data-stu-id="00293-149">Connection: Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="00293-150">:::no-loc(Identity):::: nombre, nombre para mostrar</span><span class="sxs-lookup"><span data-stu-id="00293-150">:::no-loc(Identity):::: Name, display name</span></span>
* <span data-ttu-id="00293-151">Valores de configuración</span><span class="sxs-lookup"><span data-stu-id="00293-151">Configuration settings</span></span>
* <span data-ttu-id="00293-152">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="00293-152">Environment variables</span></span>

<span data-ttu-id="00293-153">Coloque el siguiente código de [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) al principio de la canalización de procesamiento de solicitudes del método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="00293-153">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="00293-154">El entorno se comprueba antes de que se ejecute el middleware para asegurarse de que el código solo se ejecuta en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="00293-154">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="00293-155">Para obtener el entorno, use cualquiera de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="00293-155">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="00293-156">Inserte `IHostingEnvironment` en el método `Startup.Configure` y compruebe el entorno con la variable local.</span><span class="sxs-lookup"><span data-stu-id="00293-156">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="00293-157">En el siguiente ejemplo de código se muestra este enfoque.</span><span class="sxs-lookup"><span data-stu-id="00293-157">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="00293-158">Asigne el entorno a una propiedad de la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="00293-158">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="00293-159">Compruebe el entorno con la propiedad (por ejemplo, `if (Environment.IsDevelopment())`).</span><span class="sxs-lookup"><span data-stu-id="00293-159">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($":::no-loc(Identity):::{rule}");
            sb.Append($"User: {context.User.:::no-loc(Identity):::.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="00293-160">Depuración de aplicaciones ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="00293-160">Debug ASP.NET Core apps</span></span>

<span data-ttu-id="00293-161">En los vínculos siguientes se proporciona información sobre la depuración de aplicaciones ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="00293-161">The following links provide information on debugging ASP.NET Core apps.</span></span>

* [<span data-ttu-id="00293-162">Depuración de ASP Core en Linux</span><span class="sxs-lookup"><span data-stu-id="00293-162">Debugging ASP Core on Linux</span></span>](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [<span data-ttu-id="00293-163">Depuración de .NET Core en Unix a través de SSH</span><span class="sxs-lookup"><span data-stu-id="00293-163">Debugging .NET Core on Unix over SSH</span></span>](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [<span data-ttu-id="00293-164">Inicio rápido: Depuración de ASP.NET con el depurador de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00293-164">Quickstart: Debug ASP.NET with the Visual Studio debugger</span></span>](/visualstudio/debugger/quickstart-debug-aspnet)
* <span data-ttu-id="00293-165">Consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/2960) para obtener más información sobre la depuración.</span><span class="sxs-lookup"><span data-stu-id="00293-165">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/2960) for more debugging information.</span></span>
