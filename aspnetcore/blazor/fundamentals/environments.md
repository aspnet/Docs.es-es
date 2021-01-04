---
title: Entornos de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre los entornos en Blazor, incluido cómo configurar el entorno de una aplicación Blazor WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2020
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: 9ba23753df1726ee4c8a9802e1a1260ef7cf6fa5
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506960"
---
# <a name="aspnet-core-no-locblazor-environments"></a><span data-ttu-id="194f5-103">Entornos de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="194f5-103">ASP.NET Core Blazor environments</span></span>

> [!NOTE]
> <span data-ttu-id="194f5-104">Este tema se aplica a Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="194f5-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="194f5-105">Para obtener instrucciones generales sobre la configuración de la aplicación de ASP.NET Core, que describe los enfoques que se deben usar para las aplicaciones de Blazor Server, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="194f5-105">For general guidance on ASP.NET Core app configuration, which describes the approaches to use for Blazor Server apps, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="194f5-106">Cuando una aplicación se ejecuta de manera local, el entorno se establece de manera predeterminada en Desarrollo.</span><span class="sxs-lookup"><span data-stu-id="194f5-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="194f5-107">Cuando la aplicación se publica, el entorno se establece de manera predeterminada en Producción.</span><span class="sxs-lookup"><span data-stu-id="194f5-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="194f5-108">La aplicación de Blazor del lado cliente ( *`Client`* ) de una solución de Blazor WebAssembly hospedada determina el entorno a partir de la aplicación *`Server`* de la solución a través de un middleware que comunica el entorno con el explorador.</span><span class="sxs-lookup"><span data-stu-id="194f5-108">The client-side Blazor app (*`Client`*) of a hosted Blazor WebAssembly solution determines the environment from the *`Server`* app of the solution via a middleware that communicates the environment to the browser.</span></span> <span data-ttu-id="194f5-109">La aplicación *`Server`* agrega un encabezado denominado `blazor-environment` con el entorno como valor del encabezado.</span><span class="sxs-lookup"><span data-stu-id="194f5-109">The *`Server`* app adds a header named `blazor-environment` with the environment as the value of the header.</span></span> <span data-ttu-id="194f5-110">La aplicación *`Client`* lee el encabezado.</span><span class="sxs-lookup"><span data-stu-id="194f5-110">The *`Client`* app reads the header.</span></span> <span data-ttu-id="194f5-111">La aplicación *`Server`* de la solución es una aplicación ASP.NET Core, por lo que se encuentra más información sobre cómo configurar el entorno en <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="194f5-111">The the *`Server`* app of the solution is an ASP.NET Core app, so more information on how to configure the environment is found in <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="194f5-112">En el caso de una aplicación Blazor WebAssembly independiente que se ejecuta localmente, el servidor de desarrollo agrega el encabezado `blazor-environment` para especificar el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="194f5-112">For a standalone Blazor WebAssembly app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="194f5-113">Para especificar el entorno para otros entornos de hospedaje, agregue el encabezado `blazor-environment`.</span><span class="sxs-lookup"><span data-stu-id="194f5-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="194f5-114">En el ejemplo siguiente de IIS, se agrega el encabezado personalizado (`blazor-environment`) al archivo `web.config` publicado.</span><span class="sxs-lookup"><span data-stu-id="194f5-114">In the following example for IIS, the custom header (`blazor-environment`) is added to the published `web.config` file.</span></span> <span data-ttu-id="194f5-115">El archivo `web.config` se encuentra en la carpeta `bin/Release/{TARGET FRAMEWORK}/publish`, donde el marcador de posición `{TARGET FRAMEWORK}` corresponde a la plataforma de destino:</span><span class="sxs-lookup"><span data-stu-id="194f5-115">The `web.config` file is located in the `bin/Release/{TARGET FRAMEWORK}/publish` folder, where the placeholder `{TARGET FRAMEWORK}` is the target framework:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> <span data-ttu-id="194f5-116">Si quiere usar un archivo `web.config` personalizado para IIS que no se sobrescriba cuando la aplicación se publique en la carpeta `publish`, consulte <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="194f5-116">To use a custom `web.config` file for IIS that isn't overwritten when the app is published to the `publish` folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="194f5-117">Para obtener el entorno de la aplicación en un componente, inserte <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> y lea la propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment>.</span><span class="sxs-lookup"><span data-stu-id="194f5-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property.</span></span>

<span data-ttu-id="194f5-118">`Pages/ReadEnvironment.razor`:</span><span class="sxs-lookup"><span data-stu-id="194f5-118">`Pages/ReadEnvironment.razor`:</span></span>

[!code-razor[](environments/samples_snapshot/ReadEnvironment.razor?highlight=3,7)]

<span data-ttu-id="194f5-119">Durante el inicio, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> expone la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> a través de la propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment>, lo que habilita una lógica específica del entorno en el código del generador de host.</span><span class="sxs-lookup"><span data-stu-id="194f5-119">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables environment-specific logic in host builder code.</span></span>

<span data-ttu-id="194f5-120">En `Program.Main` de `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="194f5-120">In `Program.Main` of `Program.cs`:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="194f5-121">Los métodos de extensión útiles siguientes proporcionados a través de <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> permiten comprobar el entorno actual para Desarrollo, Producción, Ensayo y nombres de entornos personalizados:</span><span class="sxs-lookup"><span data-stu-id="194f5-121">The following convenience extension methods provided through <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsDevelopment%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsProduction%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsStaging%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsEnvironment%2A>

<span data-ttu-id="194f5-122">En `Program.Main` de `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="194f5-122">In `Program.Main` of `Program.cs`:</span></span>

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

<span data-ttu-id="194f5-123">La propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> se puede usar durante el inicio cuando el servicio <xref:Microsoft.AspNetCore.Components.NavigationManager> no está disponible.</span><span class="sxs-lookup"><span data-stu-id="194f5-123">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="194f5-124">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="194f5-124">Additional resources</span></span>

* <xref:fundamentals/environments>
