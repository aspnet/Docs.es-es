---
title: 'Bibliotecas de clases de componentes de :::no-loc(Razor)::: de ASP.NET Core'
author: guardrex
description: 'Descubra cómo se pueden incluir componentes en aplicaciones de :::no-loc(Blazor)::: desde una biblioteca de componentes externa.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
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
uid: blazor/components/class-libraries
ms.openlocfilehash: f8e36cbe905b5ec2e674123c0f2ab6db99683c7c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056418"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a><span data-ttu-id="e9968-103">Bibliotecas de clases de componentes de :::no-loc(Razor)::: de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e9968-103">ASP.NET Core :::no-loc(Razor)::: components class libraries</span></span>

<span data-ttu-id="e9968-104">Por [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="e9968-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="e9968-105">Los componentes se pueden compartir entre proyectos en una [biblioteca de clases de :::no-loc(Razor)::: (RCL)](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="e9968-105">Components can be shared in a [:::no-loc(Razor)::: class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="e9968-106">Se puede incluir una *biblioteca de clases de componentes de :::no-loc(Razor):::* desde:</span><span class="sxs-lookup"><span data-stu-id="e9968-106">A *:::no-loc(Razor)::: components class library* can be included from:</span></span>

* <span data-ttu-id="e9968-107">Otro proyecto de la solución.</span><span class="sxs-lookup"><span data-stu-id="e9968-107">Another project in the solution.</span></span>
* <span data-ttu-id="e9968-108">Un paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="e9968-108">A NuGet package.</span></span>
* <span data-ttu-id="e9968-109">Una biblioteca de .NET a la que se hace referencia.</span><span class="sxs-lookup"><span data-stu-id="e9968-109">A referenced .NET library.</span></span>

<span data-ttu-id="e9968-110">Del mismo modo que los componentes son tipos normales de .NET, los componentes proporcionados por una RCL son ensamblados .NET normales.</span><span class="sxs-lookup"><span data-stu-id="e9968-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="e9968-111">Creación de una RCL</span><span class="sxs-lookup"><span data-stu-id="e9968-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9968-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9968-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e9968-113">Cree un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="e9968-113">Create a new project.</span></span>
1. <span data-ttu-id="e9968-114">Seleccione **Biblioteca de clases de :::no-loc(Razor):::** .</span><span class="sxs-lookup"><span data-stu-id="e9968-114">Select **:::no-loc(Razor)::: Class Library**.</span></span> <span data-ttu-id="e9968-115">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="e9968-115">Select **Next**.</span></span>
1. <span data-ttu-id="e9968-116">En el cuadro de diálogo **Crear una biblioteca de clases de :::no-loc(Razor):::** , seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="e9968-116">In the **Create a new :::no-loc(Razor)::: class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="e9968-117">Proporcione un nombre para el proyecto en el campo **Nombre del proyecto** o acepte el predeterminado.</span><span class="sxs-lookup"><span data-stu-id="e9968-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="e9968-118">En los ejemplos de este tema se usa el nombre de proyecto `ComponentLibrary`.</span><span class="sxs-lookup"><span data-stu-id="e9968-118">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="e9968-119">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="e9968-119">Select **Create**.</span></span>
1. <span data-ttu-id="e9968-120">Agregue la RCL a una solución:</span><span class="sxs-lookup"><span data-stu-id="e9968-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="e9968-121">Haga clic con el botón derecho en la solución.</span><span class="sxs-lookup"><span data-stu-id="e9968-121">Right-click the solution.</span></span> <span data-ttu-id="e9968-122">Seleccione **Agregar** > **Proyecto existente**.</span><span class="sxs-lookup"><span data-stu-id="e9968-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="e9968-123">Navegue hasta el archivo del proyecto de la RCL.</span><span class="sxs-lookup"><span data-stu-id="e9968-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="e9968-124">Seleccione el archivo de proyecto de la RCL (`.csproj`).</span><span class="sxs-lookup"><span data-stu-id="e9968-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="e9968-125">Agregue una referencia a la RCL desde la aplicación:</span><span class="sxs-lookup"><span data-stu-id="e9968-125">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="e9968-126">Haga clic con el botón derecho en el proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e9968-126">Right-click the app project.</span></span> <span data-ttu-id="e9968-127">Seleccione **Agregar** > **Referencia**.</span><span class="sxs-lookup"><span data-stu-id="e9968-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="e9968-128">Seleccione el proyecto de la RCL.</span><span class="sxs-lookup"><span data-stu-id="e9968-128">Select the RCL project.</span></span> <span data-ttu-id="e9968-129">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="e9968-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="e9968-130">Si la casilla **Páginas y vistas de soporte técnico** está activada al generar la RCL desde la plantilla, agregue también un archivo `_Imports.razor` a la raíz del proyecto generado con el siguiente contenido para habilitar la creación de componentes de :::no-loc(Razor)::::</span><span class="sxs-lookup"><span data-stu-id="e9968-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable :::no-loc(Razor)::: component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="e9968-131">Agregue manualmente el archivo a la raíz del proyecto generado.</span><span class="sxs-lookup"><span data-stu-id="e9968-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e9968-132">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="e9968-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="e9968-133">Use la plantilla **Biblioteca de clases de :::no-loc(Razor):::** (`razorclasslib`) con el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) en un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="e9968-133">Use the **:::no-loc(Razor)::: Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="e9968-134">En el ejemplo siguiente, se crea una RCL llamada `ComponentLibrary`.</span><span class="sxs-lookup"><span data-stu-id="e9968-134">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="e9968-135">La carpeta que contiene `ComponentLibrary` se crea automáticamente cuando se ejecuta el comando:</span><span class="sxs-lookup"><span data-stu-id="e9968-135">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="e9968-136">Si se usa el modificador `-s|--support-pages-and-views` al generar la RCL desde la plantilla, agregue también un archivo `_Imports.razor` a la raíz del proyecto generado con el siguiente contenido para habilitar la creación de componentes de :::no-loc(Razor)::::</span><span class="sxs-lookup"><span data-stu-id="e9968-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable :::no-loc(Razor)::: component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="e9968-137">Agregue manualmente el archivo a la raíz del proyecto generado.</span><span class="sxs-lookup"><span data-stu-id="e9968-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="e9968-138">Para agregar la biblioteca a un proyecto existente, use el comando [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) en un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="e9968-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="e9968-139">En el siguiente ejemplo, se agrega la RCL a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e9968-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="e9968-140">Ejecute el siguiente comando desde la carpeta de proyecto de la aplicación con la ruta de acceso a la biblioteca:</span><span class="sxs-lookup"><span data-stu-id="e9968-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="e9968-141">Consumo de un componente de biblioteca</span><span class="sxs-lookup"><span data-stu-id="e9968-141">Consume a library component</span></span>

<span data-ttu-id="e9968-142">Para consumir los componentes definidos en una biblioteca de otro proyecto, siga cualquiera de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="e9968-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="e9968-143">Use el nombre de tipo completo con el espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="e9968-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="e9968-144">Use la directiva [`@using`](xref:mvc/views/razor#using) de :::no-loc(Razor):::.</span><span class="sxs-lookup"><span data-stu-id="e9968-144">Use :::no-loc(Razor):::'s [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="e9968-145">Los componentes individuales se pueden agregar por nombre.</span><span class="sxs-lookup"><span data-stu-id="e9968-145">Individual components can be added by name.</span></span>

<span data-ttu-id="e9968-146">En los ejemplos siguientes, `ComponentLibrary` es una biblioteca de componentes que contiene el componente `Component1` (`Component1.razor`).</span><span class="sxs-lookup"><span data-stu-id="e9968-146">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="e9968-147">El componente `Component1` es un componente de ejemplo que la plantilla de proyecto de RCL agrega automáticamente cuando se crea la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="e9968-147">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="e9968-148">Haga referencia al componente `Component1` mediante su espacio de nombres:</span><span class="sxs-lookup"><span data-stu-id="e9968-148">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="e9968-149">Como alternativa, coloque la biblioteca en el ámbito con una directiva [`@using`](xref:mvc/views/razor#using) y use el componente sin su espacio de nombres:</span><span class="sxs-lookup"><span data-stu-id="e9968-149">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="e9968-150">De manera opcional, incluya la directiva `@using ComponentLibrary` en el archivo de nivel superior `_Import.razor` a fin de que los componentes de la biblioteca estén disponibles para un proyecto completo.</span><span class="sxs-lookup"><span data-stu-id="e9968-150">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="e9968-151">Agregue la directiva a un archivo `_Import.razor` en cualquier nivel para aplicar el espacio de nombres a un solo componente o a un conjunto de componentes dentro de una carpeta.</span><span class="sxs-lookup"><span data-stu-id="e9968-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

To provide `Component1`'s `my-component` CSS class to the component, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This :::no-loc(Blazor)::: component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

To provide the stylesheet across the app, you can alternatively link to the library's stylesheet in the app's `wwwroot/index.html` file (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` file (:::no-loc(Blazor Server):::):

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered. The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded `<link>` HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

<span data-ttu-id="e9968-152">Para proporcionar la clase CSS `my-component` de `Component1`, establezca un vínculo con la hoja de estilo de la biblioteca en el archivo `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) o el archivo `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::) de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="e9968-152">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` file (:::no-loc(Blazor Server):::):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a><span data-ttu-id="e9968-153">Creación de una biblioteca de clases de componentes de :::no-loc(Razor)::: con recursos estáticos</span><span class="sxs-lookup"><span data-stu-id="e9968-153">Create a :::no-loc(Razor)::: components class library with static assets</span></span>

<span data-ttu-id="e9968-154">Una RCL puede incluir recursos estáticos.</span><span class="sxs-lookup"><span data-stu-id="e9968-154">An RCL can include static assets.</span></span> <span data-ttu-id="e9968-155">Los recursos estáticos están disponibles para cualquier aplicación que use la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="e9968-155">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="e9968-156">Para obtener más información, vea <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="e9968-156">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a><span data-ttu-id="e9968-157">Proporcionar componentes y recursos estáticos a varias aplicaciones de :::no-loc(Blazor)::: hospedadas</span><span class="sxs-lookup"><span data-stu-id="e9968-157">Supply components and static assets to multiple hosted :::no-loc(Blazor)::: apps</span></span>

<span data-ttu-id="e9968-158">Para obtener más información, vea <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="e9968-158">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-no-locblazor-webassembly"></a><span data-ttu-id="e9968-159">Analizador de compatibilidad con el explorador de :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="e9968-159">Browser compatibility analyzer for :::no-loc(Blazor WebAssembly):::</span></span>

<span data-ttu-id="e9968-160">Las aplicaciones :::no-loc(Blazor WebAssembly)::: tienen como destino el área expuesta de la API de .NET completa, pero no todas las API de .NET son compatibles con WebAssembly, debido a las restricciones de espacio aislado del explorador.</span><span class="sxs-lookup"><span data-stu-id="e9968-160">:::no-loc(Blazor WebAssembly)::: apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id="e9968-161">Las API no compatibles inician la excepción <xref:System.PlatformNotSupportedException> cuando se ejecutan en WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="e9968-161">Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id="e9968-162">Un analizador de compatibilidad de plataforma advierte al desarrollador cuando la aplicación usa API que no son compatibles con las plataformas de destino de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e9968-162">A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id="e9968-163">En el caso de las aplicaciones :::no-loc(Blazor WebAssembly):::, esto significa comprobar que las API se admiten en los exploradores.</span><span class="sxs-lookup"><span data-stu-id="e9968-163">For :::no-loc(Blazor WebAssembly)::: apps, this means checking that APIs are supported in browsers.</span></span> <span data-ttu-id="e9968-164">La anotación de API de .NET Framework para el analizador de compatibilidad es un proceso constante, por lo que no todas las API de .NET Framework están anotadas actualmente.</span><span class="sxs-lookup"><span data-stu-id="e9968-164">Annotating .NET framework APIs for the compatibility analyzer is an on-going process, so not all .NET framework API is currently annotated.</span></span>

<span data-ttu-id="e9968-165">Los proyectos de biblioteca de clases de :::no-loc(Blazor WebAssembly)::: y :::no-loc(Razor)::: habilitan *automáticamente* las comprobaciones de compatibilidad del explorador agregando `browser` como plataforma compatible con el elemento `SupportedPlatform` de MSBuild.</span><span class="sxs-lookup"><span data-stu-id="e9968-165">:::no-loc(Blazor WebAssembly)::: and :::no-loc(Razor)::: class library projects *automatically* enable browser compatibilty checks by adding `browser` as a supported platform with the `SupportedPlatform` MSBuild item.</span></span> <span data-ttu-id="e9968-166">Los desarrolladores de bibliotecas pueden agregar manualmente el elemento `SupportedPlatform` al archivo de proyecto de una biblioteca para habilitar la característica:</span><span class="sxs-lookup"><span data-stu-id="e9968-166">Library developers can manually add the `SupportedPlatform` item to a library's project file to enable the feature:</span></span>

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

<span data-ttu-id="e9968-167">Al crear una biblioteca, especifique `browser` en <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute> para indicar que los exploradores no admiten una API determinada:</span><span class="sxs-lookup"><span data-stu-id="e9968-167">When authoring a library, indicate that a particular API isn't supported in browsers by specifying `browser` to <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute>:</span></span>

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

<span data-ttu-id="e9968-168">Para más información, vea [Anotación de API como no compatibles en plataformas específicas (repositorio de GitHub dotnet/designs)](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span><span class="sxs-lookup"><span data-stu-id="e9968-168">For more information, see [Annotating APIs as unsupported on specific platforms (dotnet/designs GitHub repository](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span></span>

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="e9968-169">Aislamiento de JavaScript y referencias a objetos en :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="e9968-169">:::no-loc(Blazor)::: JavaScript isolation and object references</span></span>

<span data-ttu-id="e9968-170">:::no-loc(Blazor)::: permite el aislamiento de JavaScript en [módulos de JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) estándar.</span><span class="sxs-lookup"><span data-stu-id="e9968-170">:::no-loc(Blazor)::: enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="e9968-171">El aislamiento de JavaScript reporta las siguientes ventajas:</span><span class="sxs-lookup"><span data-stu-id="e9968-171">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="e9968-172">El código JavaScript importado no contamina el espacio de nombres global.</span><span class="sxs-lookup"><span data-stu-id="e9968-172">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="e9968-173">No es necesario que los consumidores de la biblioteca y los componentes importen manualmente el código JavaScript relacionado.</span><span class="sxs-lookup"><span data-stu-id="e9968-173">Consumers of the library and components aren't required to manually import the related JavaScript.</span></span>

<span data-ttu-id="e9968-174">Para obtener más información, vea <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span><span class="sxs-lookup"><span data-stu-id="e9968-174">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="e9968-175">Compilación, empaquetado y envío de bibliotecas a NuGet</span><span class="sxs-lookup"><span data-stu-id="e9968-175">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="e9968-176">Dado que las bibliotecas de componentes son bibliotecas estándar de .NET, se empaquetan y se envían a NuGet de la misma manera que cualquier otra biblioteca.</span><span class="sxs-lookup"><span data-stu-id="e9968-176">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="e9968-177">El empaquetado se realiza mediante el comando [`dotnet pack`](/dotnet/core/tools/dotnet-pack) en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="e9968-177">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="e9968-178">Cargue el paquete en NuGet usando el comando [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) en un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="e9968-178">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e9968-179">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e9968-179">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="e9968-180">Adición de un archivo de configuración del recortador de lenguaje intermedio (IL) XML a una biblioteca</span><span class="sxs-lookup"><span data-stu-id="e9968-180">Add an XML Intermediate Language (IL) Trimmer configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-trimmer)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="e9968-181">Agregar un archivo de configuración del enlazador de lenguaje intermedio (IL) XML a una biblioteca</span><span class="sxs-lookup"><span data-stu-id="e9968-181">Add an XML Intermediate Language (IL) Linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
