---
title: Bibliotecas de clases de componentes de Razor de ASP.NET Core
author: guardrex
description: Descubra cómo se pueden incluir componentes en aplicaciones de Blazor desde una biblioteca de componentes externa.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/class-libraries
ms.openlocfilehash: b54eb7142fc7e1665cc0aaaad068a67852ac2f74
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399080"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="a1dcc-103">Bibliotecas de clases de componentes de Razor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a1dcc-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="a1dcc-104">Por [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="a1dcc-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="a1dcc-105">Los componentes se pueden compartir entre proyectos en una [biblioteca de clases de Razor (RCL)](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="a1dcc-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="a1dcc-106">Se puede incluir una *biblioteca de clases de componentes de Razor* desde:</span><span class="sxs-lookup"><span data-stu-id="a1dcc-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="a1dcc-107">Otro proyecto de la solución.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-107">Another project in the solution.</span></span>
* <span data-ttu-id="a1dcc-108">Un paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-108">A NuGet package.</span></span>
* <span data-ttu-id="a1dcc-109">Una biblioteca de .NET a la que se hace referencia.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-109">A referenced .NET library.</span></span>

<span data-ttu-id="a1dcc-110">Del mismo modo que los componentes son tipos normales de .NET, los componentes proporcionados por una RCL son ensamblados .NET normales.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="a1dcc-111">Creación de una RCL</span><span class="sxs-lookup"><span data-stu-id="a1dcc-111">Create an RCL</span></span>

<span data-ttu-id="a1dcc-112">Siga las instrucciones del artículo <xref:blazor/get-started> para configurar el entorno para Blazor.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1dcc-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1dcc-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a1dcc-114">Cree un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-114">Create a new project.</span></span>
1. <span data-ttu-id="a1dcc-115">Seleccione **Biblioteca de clases de Razor** .</span><span class="sxs-lookup"><span data-stu-id="a1dcc-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="a1dcc-116">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-116">Select **Next**.</span></span>
1. <span data-ttu-id="a1dcc-117">En el cuadro de diálogo **Crear una biblioteca de clases de Razor** , seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="a1dcc-118">Proporcione un nombre para el proyecto en el campo **Nombre del proyecto** o acepte el predeterminado.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="a1dcc-119">En los ejemplos de este tema se usa el nombre de proyecto `MyComponentLib1`.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="a1dcc-120">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-120">Select **Create**.</span></span>
1. <span data-ttu-id="a1dcc-121">Agregue la RCL a una solución:</span><span class="sxs-lookup"><span data-stu-id="a1dcc-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="a1dcc-122">Haga clic con el botón derecho en la solución.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-122">Right-click the solution.</span></span> <span data-ttu-id="a1dcc-123">Seleccione **Agregar** > **Proyecto existente**.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="a1dcc-124">Navegue hasta el archivo del proyecto de la RCL.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="a1dcc-125">Seleccione el archivo de proyecto de la RCL (`.csproj`).</span><span class="sxs-lookup"><span data-stu-id="a1dcc-125">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="a1dcc-126">Agregue una referencia a la RCL desde la aplicación:</span><span class="sxs-lookup"><span data-stu-id="a1dcc-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="a1dcc-127">Haga clic con el botón derecho en el proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-127">Right-click the app project.</span></span> <span data-ttu-id="a1dcc-128">Seleccione **Agregar** > **Referencia**.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="a1dcc-129">Seleccione el proyecto de la RCL.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-129">Select the RCL project.</span></span> <span data-ttu-id="a1dcc-130">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-130">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="a1dcc-131">Si la casilla **Páginas y vistas de soporte técnico** está activada al generar la RCL desde la plantilla, agregue también un archivo `_Imports.razor` a la raíz del proyecto generado con el siguiente contenido para habilitar la creación de componentes de Razor:</span><span class="sxs-lookup"><span data-stu-id="a1dcc-131">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="a1dcc-132">Agregue manualmente el archivo a la raíz del proyecto generado.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-132">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a1dcc-133">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="a1dcc-133">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="a1dcc-134">Use la plantilla **Biblioteca de clases de Razor** (`razorclasslib`) con el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) en un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-134">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="a1dcc-135">En el ejemplo siguiente, se crea una RCL llamada `MyComponentLib1`.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="a1dcc-136">La carpeta que contiene `MyComponentLib1` se crea automáticamente cuando se ejecuta el comando:</span><span class="sxs-lookup"><span data-stu-id="a1dcc-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > <span data-ttu-id="a1dcc-137">Si se usa el modificador `-s|--support-pages-and-views` al generar la RCL desde la plantilla, agregue también un archivo `_Imports.razor` a la raíz del proyecto generado con el siguiente contenido para habilitar la creación de componentes de Razor:</span><span class="sxs-lookup"><span data-stu-id="a1dcc-137">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="a1dcc-138">Agregue manualmente el archivo a la raíz del proyecto generado.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-138">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="a1dcc-139">Para agregar la biblioteca a un proyecto existente, use el comando [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) en un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-139">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="a1dcc-140">En el siguiente ejemplo, se agrega la RCL a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-140">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="a1dcc-141">Ejecute el siguiente comando desde la carpeta de proyecto de la aplicación con la ruta de acceso a la biblioteca:</span><span class="sxs-lookup"><span data-stu-id="a1dcc-141">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="a1dcc-142">Consumo de un componente de biblioteca</span><span class="sxs-lookup"><span data-stu-id="a1dcc-142">Consume a library component</span></span>

<span data-ttu-id="a1dcc-143">Para consumir los componentes definidos en una biblioteca de otro proyecto, siga cualquiera de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="a1dcc-143">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="a1dcc-144">Use el nombre de tipo completo con el espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-144">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="a1dcc-145">Use la directiva [`@using`](xref:mvc/views/razor#using) de Razor.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-145">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="a1dcc-146">Los componentes individuales se pueden agregar por nombre.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-146">Individual components can be added by name.</span></span>

<span data-ttu-id="a1dcc-147">En los ejemplos siguientes, `MyComponentLib1` es una biblioteca de componentes que contiene un componente `SalesReport`.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-147">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="a1dcc-148">Se puede hacer referencia al componente `SalesReport` usando su nombre de tipo completo con el espacio de nombres:</span><span class="sxs-lookup"><span data-stu-id="a1dcc-148">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="a1dcc-149">También se puede hacer referencia al componente si la biblioteca se incluye en el ámbito con una directiva `@using`:</span><span class="sxs-lookup"><span data-stu-id="a1dcc-149">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="a1dcc-150">Incluya la directiva `@using MyComponentLib1` en el archivo de nivel superior `_Import.razor` a fin de que los componentes de la biblioteca estén disponibles para un proyecto completo.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-150">Include the `@using MyComponentLib1` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="a1dcc-151">Agregue la directiva a un archivo `_Import.razor` en cualquier nivel para aplicar el espacio de nombres a una sola página o a un conjunto de páginas dentro de una carpeta.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="a1dcc-152">Creación de una biblioteca de clases de componentes de Razor con recursos estáticos</span><span class="sxs-lookup"><span data-stu-id="a1dcc-152">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="a1dcc-153">Una RCL puede incluir recursos estáticos.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-153">An RCL can include static assets.</span></span> <span data-ttu-id="a1dcc-154">Los recursos estáticos están disponibles para cualquier aplicación que use la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-154">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="a1dcc-155">Para obtener más información, vea <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-155">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="a1dcc-156">Compilación, empaquetado y envío de bibliotecas a NuGet</span><span class="sxs-lookup"><span data-stu-id="a1dcc-156">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="a1dcc-157">Dado que las bibliotecas de componentes son bibliotecas estándar de .NET, se empaquetan y se envían a NuGet de la misma manera que cualquier otra biblioteca.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-157">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="a1dcc-158">El empaquetado se realiza mediante el comando [`dotnet pack`](/dotnet/core/tools/dotnet-pack) en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="a1dcc-158">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="a1dcc-159">Cargue el paquete en NuGet usando el comando [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) en un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="a1dcc-159">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a1dcc-160">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="a1dcc-160">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="a1dcc-161">Adición de un archivo de configuración del enlazador XML a una biblioteca</span><span class="sxs-lookup"><span data-stu-id="a1dcc-161">Add an XML linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
