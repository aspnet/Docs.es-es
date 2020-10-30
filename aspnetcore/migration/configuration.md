---
title: Migrar la configuración a ASP.NET Core
author: ardalis
description: Obtenga información sobre cómo migrar la configuración de un proyecto de ASP.NET MVC a un proyecto de MVC de ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
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
uid: migration/configuration
ms.openlocfilehash: d84204c8c791bfaf36432462cde3a42c294c7966
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059798"
---
# <a name="migrate-configuration-to-aspnet-core"></a><span data-ttu-id="b5b98-103">Migrar la configuración a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b5b98-103">Migrate configuration to ASP.NET Core</span></span>

<span data-ttu-id="b5b98-104">Por [Steve Smith](https://ardalis.com/) y [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="b5b98-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="b5b98-105">En el artículo anterior, comenzamos a [migrar un proyecto de ASP.NET MVC a ASP.net Core MVC](xref:migration/mvc).</span><span class="sxs-lookup"><span data-stu-id="b5b98-105">In the previous article, we began to [migrate an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/mvc).</span></span> <span data-ttu-id="b5b98-106">En este artículo se migra la configuración.</span><span class="sxs-lookup"><span data-stu-id="b5b98-106">In this article, we migrate configuration.</span></span>

<span data-ttu-id="b5b98-107">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b5b98-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="setup-configuration"></a><span data-ttu-id="b5b98-108">Configuración</span><span class="sxs-lookup"><span data-stu-id="b5b98-108">Setup configuration</span></span>

<span data-ttu-id="b5b98-109">ASP.NET Core ya no usa los archivos *global. asax* y *web.config* que usaban las versiones anteriores de ASP.net.</span><span class="sxs-lookup"><span data-stu-id="b5b98-109">ASP.NET Core no longer uses the *Global.asax* and *web.config* files that previous versions of ASP.NET utilized.</span></span> <span data-ttu-id="b5b98-110">En las versiones anteriores de ASP.NET, la lógica de inicio de la aplicación se colocó en un `Application_StartUp` método dentro de *global. asax* .</span><span class="sxs-lookup"><span data-stu-id="b5b98-110">In the earlier versions of ASP.NET, application startup logic was placed in an `Application_StartUp` method within *Global.asax* .</span></span> <span data-ttu-id="b5b98-111">Más adelante, en ASP.NET MVC, se incluyó un archivo *Startup.CS* en la raíz del proyecto; y se llamó al iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b5b98-111">Later, in ASP.NET MVC, a *Startup.cs* file was included in the root of the project; and, it was called when the application started.</span></span> <span data-ttu-id="b5b98-112">ASP.NET Core ha adoptado este enfoque completamente colocando toda la lógica de inicio en el archivo *Startup.CS* .</span><span class="sxs-lookup"><span data-stu-id="b5b98-112">ASP.NET Core has adopted this approach completely by placing all startup logic in the *Startup.cs* file.</span></span>

<span data-ttu-id="b5b98-113">El archivo *web.config* también se ha reemplazado en ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="b5b98-113">The *web.config* file has also been replaced in ASP.NET Core.</span></span> <span data-ttu-id="b5b98-114">La configuración se puede configurar ahora, como parte del procedimiento de inicio de la aplicación que se describe en *Startup.CS* .</span><span class="sxs-lookup"><span data-stu-id="b5b98-114">Configuration itself can now be configured, as part of the application startup procedure described in *Startup.cs* .</span></span> <span data-ttu-id="b5b98-115">La configuración puede seguir usando archivos XML, pero normalmente ASP.NET Core proyectos colocarán los valores de configuración en un archivo con formato JSON, como *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="b5b98-115">Configuration can still utilize XML files, but typically ASP.NET Core projects will place configuration values in a JSON-formatted file, such as *:::no-loc(appsettings.json):::* .</span></span> <span data-ttu-id="b5b98-116">El sistema de configuración de ASP.NET Core también puede acceder fácilmente a las variables de entorno, lo que puede proporcionar una [ubicación más segura y robusta](xref:security/app-secrets) para los valores específicos del entorno.</span><span class="sxs-lookup"><span data-stu-id="b5b98-116">ASP.NET Core's configuration system can also easily access environment variables, which can provide a [more secure and robust location](xref:security/app-secrets) for environment-specific values.</span></span> <span data-ttu-id="b5b98-117">Esto es especialmente cierto para secretos como cadenas de conexión y claves de API que no se deben proteger en el control de código fuente.</span><span class="sxs-lookup"><span data-stu-id="b5b98-117">This is especially true for secrets like connection strings and API keys that shouldn't be checked into source control.</span></span> <span data-ttu-id="b5b98-118">Consulte [configuración](xref:fundamentals/configuration/index) para obtener más información sobre la configuración en ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="b5b98-118">See [Configuration](xref:fundamentals/configuration/index) to learn more about configuration in ASP.NET Core.</span></span>

<span data-ttu-id="b5b98-119">En este artículo, vamos a empezar con el proyecto ASP.NET Core parcialmente migrado del [artículo anterior](xref:migration/mvc).</span><span class="sxs-lookup"><span data-stu-id="b5b98-119">For this article, we are starting with the partially migrated ASP.NET Core project from [the previous article](xref:migration/mvc).</span></span> <span data-ttu-id="b5b98-120">Para configurar la configuración, agregue el siguiente constructor y la propiedad al archivo *Startup.CS* que se encuentra en la raíz del proyecto:</span><span class="sxs-lookup"><span data-stu-id="b5b98-120">To setup configuration, add the following constructor and property to the *Startup.cs* file located in the root of the project:</span></span>

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

<span data-ttu-id="b5b98-121">Tenga en cuenta que, en este momento, el archivo *Startup.CS* no se compilará, ya que todavía es necesario agregar la siguiente `using` instrucción:</span><span class="sxs-lookup"><span data-stu-id="b5b98-121">Note that at this point, the *Startup.cs* file won't compile, as we still need to add the following `using` statement:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="b5b98-122">Agregue un *:::no-loc(appsettings.json):::* archivo a la raíz del proyecto con la plantilla de elemento adecuada:</span><span class="sxs-lookup"><span data-stu-id="b5b98-122">Add an *:::no-loc(appsettings.json):::* file to the root of the project using the appropriate item template:</span></span>

![Agregar JSON AppSettings](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a><span data-ttu-id="b5b98-124">Migrar las opciones de configuración desde web.config</span><span class="sxs-lookup"><span data-stu-id="b5b98-124">Migrate configuration settings from web.config</span></span>

<span data-ttu-id="b5b98-125">Nuestro proyecto ASP.NET MVC incluía la cadena de conexión de base de datos necesaria en *web.config* , en el `<connectionStrings>` elemento.</span><span class="sxs-lookup"><span data-stu-id="b5b98-125">Our ASP.NET MVC project included the required database connection string in *web.config* , in the `<connectionStrings>` element.</span></span> <span data-ttu-id="b5b98-126">En nuestro proyecto de ASP.NET Core, vamos a almacenar esta información en el *:::no-loc(appsettings.json):::* archivo.</span><span class="sxs-lookup"><span data-stu-id="b5b98-126">In our ASP.NET Core project, we are going to store this information in the *:::no-loc(appsettings.json):::* file.</span></span> <span data-ttu-id="b5b98-127">Abra *:::no-loc(appsettings.json):::* y observe que ya incluye lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b5b98-127">Open *:::no-loc(appsettings.json):::* , and note that it already includes the following:</span></span>

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/:::no-loc(appsettings.json):::?highlight=4)]

<span data-ttu-id="b5b98-128">En la línea resaltada descrita anteriormente, cambie el nombre de la base de datos de **_CHANGE_ME** por el nombre de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b5b98-128">In the highlighted line depicted above, change the name of the database from **_CHANGE_ME** to the name of your database.</span></span>

## <a name="summary"></a><span data-ttu-id="b5b98-129">Resumen</span><span class="sxs-lookup"><span data-stu-id="b5b98-129">Summary</span></span>

<span data-ttu-id="b5b98-130">ASP.NET Core coloca toda la lógica de inicio de la aplicación en un único archivo, en el que se pueden definir y configurar los servicios y dependencias necesarios.</span><span class="sxs-lookup"><span data-stu-id="b5b98-130">ASP.NET Core places all startup logic for the application in a single file, in which the necessary services and dependencies can be defined and configured.</span></span> <span data-ttu-id="b5b98-131">Reemplaza el archivo de *web.config* con una característica de configuración flexible que puede aprovechar una variedad de formatos de archivo, como JSON, y las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="b5b98-131">It replaces the *web.config* file with a flexible configuration feature that can leverage a variety of file formats, such as JSON, as well as environment variables.</span></span>
