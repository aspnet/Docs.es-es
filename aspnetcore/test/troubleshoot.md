---
title: Solución de problemas y depuración de proyectos de ASP.NET Core
author: Rick-Anderson
description: Conozca y solucione advertencias y errores en proyectos de ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
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
uid: test/troubleshoot
ms.openlocfilehash: 8e6c640cd775e5d4cbe6e34c1cecc391baf57344
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059577"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a>Solución de problemas y depuración de proyectos de ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

En los vínculos siguientes se proporciona una guía de solución de problemas:

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Conferencia NDC (Londres, 2018): diagnóstico de problemas en aplicaciones ASP.NET Core](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [Blog de ASP.NET: solución de problemas de rendimiento de ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>Advertencias del SDK de .NET Core

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>Se instalaron las versiones de 32 y 64 bits del SDK de .NET Core

En el cuadro de diálogo **Nuevo proyecto** de ASP.NET Core, puede ver la siguiente advertencia:

> Se instalaron las versiones de 32 y 64 bits del SDK de .NET Core. Solo se mostrarán las plantillas de las versiones de 64 bits instaladas en "C:\\Archivos de programa\\dotnet\\sdk\\".

Esta advertencia aparece si se instalan las versiones de 32 bits (x86) y 64 bits (x64) del [SDK de .NET Core](https://dotnet.microsoft.com/download/dotnet-core). Entre los motivos habituales por los que pueden estar instaladas ambas versiones se encuentran los siguientes:

* En un principio, descargó el instalador del SDK de .NET Core con un equipo de 32 bits, pero después lo copió e instaló en un equipo de 64 bits.
* Otra aplicación ha instalado el SDK de .NET Core de 32 bits.
* Se ha descargado e instalado la versión incorrecta.

Desinstale el SDK de .NET Core de 32 bits para evitar que aparezca esta advertencia. Desinstálelo en **Panel de control** > **Programas y características** > **Desinstalar o cambiar un programa**. Si comprende por qué se produce la advertencia y sus implicaciones, puede omitirla.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>El SDK de .NET Core está instalado en varias ubicaciones

En el cuadro de diálogo **Nuevo proyecto** de ASP.NET Core, puede ver la siguiente advertencia:

> El SDK de .NET Core está instalado en varias ubicaciones. Solo se mostrarán las plantillas de los SDK instalados en "C:\\Archivos de programa\\dotnet\\sdk\\".

Verá este mensaje si tiene al menos una instalación del SDK de .NET Core en un directorio fuera de *C:\\Archivos de programa\\dotnet\\sdk\\* . Esto suele suceder si el SDK de .NET Core se ha implementado en un equipo mediante el método de copiar y pegar en lugar de con el instalador de MSI.

Desinstale todos los SDK y los entornos de ejecución de .NET Core de 32 bits para evitar que aparezca esta advertencia. Desinstálelo en **Panel de control** > **Programas y características** > **Desinstalar o cambiar un programa**. Si comprende por qué se produce la advertencia y sus implicaciones, puede omitirla.

### <a name="no-net-core-sdks-were-detected"></a>No se ha detectado ningún SDK de .NET Core

* En el cuadro de diálogo **Nuevo proyecto** de Visual Studio de ASP.NET Core, puede ver la siguiente advertencia:

  > No se ha detectado ningún SDK de .NET Core, asegúrese de que se incluyen en la variable de entorno `PATH`.

* Al ejecutar un comando `dotnet`, la advertencia aparece como:

  > No se pudo encontrar ningún SDK de dotnet instalado.

Estas advertencias aparecen si la variable de entorno `PATH` no apunta a ningún SDK de .NET Core en el equipo. Para resolver este problema:

* Instale el SDK de .NET Core. Obtenga el instalador más reciente en [Descargas de .NET](https://dotnet.microsoft.com/download).
* Compruebe que la variable de entorno `PATH` apunta a la ubicación en la que está instalado el SDK (`C:\Program Files\dotnet\` para 64 bits/x64 o `C:\Program Files (x86)\dotnet\` para 32 bits/x86). Normalmente, el instalador del SDK establece el valor de `PATH`. Instale siempre los mismos valores de bits de los SDK y los entornos de ejecución en el mismo equipo.

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>Falta el SDK después de instalar el conjunto de hospedaje de .NET Core

Al instalar el [conjunto de hospedaje de .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) se modifica el valor de `PATH` cuando instala el entorno de ejecución de .NET Core para que apunte a la versión de 32 bits (x86) de .NET Core (`C:\Program Files (x86)\dotnet\`). Esto puede dar lugar a que falten SDK cuando se usa el comando `dotnet` de 32 bits (x86) de .NET Core ([No se ha detectado ningún SDK de .NET Core](#no-net-core-sdks-were-detected)). Para solucionar este problema, mueva `C:\Program Files\dotnet\` a una posición antes de `C:\Program Files (x86)\dotnet\` en `PATH`.

## <a name="obtain-data-from-an-app"></a>Obtención de datos de una aplicación

Si una aplicación es capaz de responder a las solicitudes, puede obtener los siguientes datos de la aplicación mediante middleware:

* Solicitud: método, esquema, host, ruta de acceso base, ruta de acceso, cadena de consulta, encabezados
* Conexión: dirección IP remota, puerto remoto, dirección IP local, puerto local, certificado de cliente
* Identity: nombre, nombre para mostrar
* Valores de configuración
* Variables de entorno

Coloque el siguiente código de [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) al principio de la canalización de procesamiento de solicitudes del método `Startup.Configure`. El entorno se comprueba antes de que se ejecute el middleware para asegurarse de que el código solo se ejecuta en el entorno de desarrollo.

Para obtener el entorno, use cualquiera de los enfoques siguientes:

* Inserte `IHostingEnvironment` en el método `Startup.Configure` y compruebe el entorno con la variable local. En el siguiente ejemplo de código se muestra este enfoque.

* Asigne el entorno a una propiedad de la clase `Startup`. Compruebe el entorno con la propiedad (por ejemplo, `if (Environment.IsDevelopment())`).

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

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
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

## <a name="debug-aspnet-core-apps"></a>Depuración de aplicaciones ASP.NET Core

En los vínculos siguientes se proporciona información sobre la depuración de aplicaciones ASP.NET Core.

* [Depuración de ASP Core en Linux](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [Depuración de .NET Core en Unix a través de SSH](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [Inicio rápido: Depuración de ASP.NET con el depurador de Visual Studio](/visualstudio/debugger/quickstart-debug-aspnet)
* Consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/2960) para obtener más información sobre la depuración.
