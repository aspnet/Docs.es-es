---
title: Bibliotecas de clases de componentes de Razor de ASP.NET Core
author: guardrex
description: Descubra cómo se pueden incluir componentes en aplicaciones de Blazor desde una biblioteca de componentes externa.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/12/2021
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
uid: blazor/components/class-libraries
ms.openlocfilehash: 14370f9bbf45079fd3654d3e55af4178691cf4f5
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252557"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a><span data-ttu-id="7d869-103">Bibliotecas de clases de componentes de Razor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7d869-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="7d869-104">Por [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="7d869-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="7d869-105">Los componentes se pueden compartir entre proyectos en una [biblioteca de clases de Razor (RCL)](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="7d869-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="7d869-106">Se puede incluir una *biblioteca de clases de componentes de Razor* desde:</span><span class="sxs-lookup"><span data-stu-id="7d869-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="7d869-107">Otro proyecto de la solución.</span><span class="sxs-lookup"><span data-stu-id="7d869-107">Another project in the solution.</span></span>
* <span data-ttu-id="7d869-108">Un paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="7d869-108">A NuGet package.</span></span>
* <span data-ttu-id="7d869-109">Una biblioteca de .NET a la que se hace referencia.</span><span class="sxs-lookup"><span data-stu-id="7d869-109">A referenced .NET library.</span></span>

<span data-ttu-id="7d869-110">Del mismo modo que los componentes son tipos normales de .NET, los componentes proporcionados por una RCL son ensamblados .NET normales.</span><span class="sxs-lookup"><span data-stu-id="7d869-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="7d869-111">Creación de una RCL</span><span class="sxs-lookup"><span data-stu-id="7d869-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d869-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d869-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="7d869-113">Cree un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="7d869-113">Create a new project.</span></span>
1. <span data-ttu-id="7d869-114">Seleccione **Biblioteca de clases de Razor** .</span><span class="sxs-lookup"><span data-stu-id="7d869-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="7d869-115">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="7d869-115">Select **Next**.</span></span>
1. <span data-ttu-id="7d869-116">En el cuadro de diálogo **Crear una biblioteca de clases de Razor** , seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="7d869-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="7d869-117">Proporcione un nombre para el proyecto en el campo **Nombre del proyecto** o acepte el predeterminado.</span><span class="sxs-lookup"><span data-stu-id="7d869-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="7d869-118">En los ejemplos de este tema se usa el nombre de proyecto `ComponentLibrary`.</span><span class="sxs-lookup"><span data-stu-id="7d869-118">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="7d869-119">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="7d869-119">Select **Create**.</span></span>
1. <span data-ttu-id="7d869-120">Agregue la RCL a una solución:</span><span class="sxs-lookup"><span data-stu-id="7d869-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="7d869-121">Haga clic con el botón derecho en la solución.</span><span class="sxs-lookup"><span data-stu-id="7d869-121">Right-click the solution.</span></span> <span data-ttu-id="7d869-122">Seleccione **Agregar** > **Proyecto existente**.</span><span class="sxs-lookup"><span data-stu-id="7d869-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="7d869-123">Navegue hasta el archivo del proyecto de la RCL.</span><span class="sxs-lookup"><span data-stu-id="7d869-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="7d869-124">Seleccione el archivo de proyecto de la RCL (`.csproj`).</span><span class="sxs-lookup"><span data-stu-id="7d869-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="7d869-125">Agregue una referencia a la RCL desde la aplicación:</span><span class="sxs-lookup"><span data-stu-id="7d869-125">Add a reference to the RCL from the app:</span></span>
   1. <span data-ttu-id="7d869-126">Haga clic con el botón derecho en el proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7d869-126">Right-click the app project.</span></span> <span data-ttu-id="7d869-127">Seleccione **Agregar** > **Referencia**.</span><span class="sxs-lookup"><span data-stu-id="7d869-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="7d869-128">Seleccione el proyecto de la RCL.</span><span class="sxs-lookup"><span data-stu-id="7d869-128">Select the RCL project.</span></span> <span data-ttu-id="7d869-129">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="7d869-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="7d869-130">Si la casilla **Páginas y vistas de soporte técnico** está activada al generar la RCL desde la plantilla, agregue también un archivo `_Imports.razor` a la raíz del proyecto generado con el siguiente contenido para habilitar la creación de componentes de Razor:</span><span class="sxs-lookup"><span data-stu-id="7d869-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="7d869-131">Agregue manualmente el archivo a la raíz del proyecto generado.</span><span class="sxs-lookup"><span data-stu-id="7d869-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="7d869-132">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="7d869-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="7d869-133">Use la plantilla **Biblioteca de clases de Razor** (`razorclasslib`) con el comando [`dotnet new`](/dotnet/core/tools/dotnet-new) en un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="7d869-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="7d869-134">En el ejemplo siguiente, se crea una RCL llamada `ComponentLibrary`.</span><span class="sxs-lookup"><span data-stu-id="7d869-134">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="7d869-135">La carpeta que contiene `ComponentLibrary` se crea automáticamente cuando se ejecuta el comando:</span><span class="sxs-lookup"><span data-stu-id="7d869-135">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="7d869-136">Si se usa el modificador `-s|--support-pages-and-views` al generar la RCL desde la plantilla, agregue también un archivo `_Imports.razor` a la raíz del proyecto generado con el siguiente contenido para habilitar la creación de componentes de Razor:</span><span class="sxs-lookup"><span data-stu-id="7d869-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="7d869-137">Agregue manualmente el archivo a la raíz del proyecto generado.</span><span class="sxs-lookup"><span data-stu-id="7d869-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="7d869-138">Para agregar la biblioteca a un proyecto existente, use el comando [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) en un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="7d869-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="7d869-139">En el siguiente ejemplo, se agrega la RCL a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7d869-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="7d869-140">Ejecute el siguiente comando desde la carpeta de proyecto de la aplicación con la ruta de acceso a la biblioteca:</span><span class="sxs-lookup"><span data-stu-id="7d869-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="7d869-141">Consumo de un componente de biblioteca</span><span class="sxs-lookup"><span data-stu-id="7d869-141">Consume a library component</span></span>

<span data-ttu-id="7d869-142">Para consumir los componentes definidos en una biblioteca de otro proyecto, siga cualquiera de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="7d869-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="7d869-143">Use el nombre de tipo completo con el espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="7d869-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="7d869-144">Use la directiva [`@using`](xref:mvc/views/razor#using) de Razor.</span><span class="sxs-lookup"><span data-stu-id="7d869-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="7d869-145">Los componentes individuales se pueden agregar por nombre.</span><span class="sxs-lookup"><span data-stu-id="7d869-145">Individual components can be added by name.</span></span>

<span data-ttu-id="7d869-146">En los ejemplos siguientes, `ComponentLibrary` es una biblioteca de componentes que contiene el componente `Component1` (`Component1.razor`).</span><span class="sxs-lookup"><span data-stu-id="7d869-146">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="7d869-147">El componente `Component1` es un componente de ejemplo que la plantilla de proyecto de RCL agrega automáticamente cuando se crea la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="7d869-147">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="7d869-148">Haga referencia al componente `Component1` mediante su espacio de nombres:</span><span class="sxs-lookup"><span data-stu-id="7d869-148">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="7d869-149">Como alternativa, coloque la biblioteca en el ámbito con una directiva [`@using`](xref:mvc/views/razor#using) y use el componente sin su espacio de nombres:</span><span class="sxs-lookup"><span data-stu-id="7d869-149">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="7d869-150">De manera opcional, incluya la directiva `@using ComponentLibrary` en el archivo de nivel superior `_Import.razor` a fin de que los componentes de la biblioteca estén disponibles para un proyecto completo.</span><span class="sxs-lookup"><span data-stu-id="7d869-150">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="7d869-151">Agregue la directiva a un archivo `_Import.razor` en cualquier nivel para aplicar el espacio de nombres a un solo componente o a un conjunto de componentes dentro de una carpeta.</span><span class="sxs-lookup"><span data-stu-id="7d869-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7d869-152">En el caso de los componentes de biblioteca que usan [aislamiento de CSS](xref:blazor/components/css-isolation), no es necesario vincular de forma explícita las hojas de estilo de cada componente de la biblioteca en la aplicación que la usa.</span><span class="sxs-lookup"><span data-stu-id="7d869-152">For library components that use [CSS isolation](xref:blazor/components/css-isolation), there's no need to explicitly link the library's individual component stylesheets in the app that consumes the library.</span></span> <span data-ttu-id="7d869-153">Los estilos de los componentes se ponen automáticamente a disposición de la aplicación de que los consume.</span><span class="sxs-lookup"><span data-stu-id="7d869-153">The component styles are automatically made available to the consuming app.</span></span>

<!-- REACTIVATE WHEN HEAD COMPONENTS COME BACK AT 6.0

To provide additional library component styles from stylesheets in the library's `wwwroot` folder, link the stylesheets using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

NEXT PARAGRAPH: RECAST TO 'CAN ALSO ADOPT ...'

-->

<span data-ttu-id="7d869-154">Para proporcionar estilos de componentes de biblioteca adicionales desde las hojas de estilo de la carpeta `wwwroot` de la biblioteca, vincule las hojas de estilo en el archivo `wwwroot/index.html` (Blazor WebAssembly) o el archivo `Pages/_Host.cshtml` (Blazor Server) de la aplicación que los consume:</span><span class="sxs-lookup"><span data-stu-id="7d869-154">To provide additional library component styles from stylesheets in the library's `wwwroot` folder, link the stylesheets in the consuming app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/additionalStyles.css" rel="stylesheet" />
</head>
```

<!-- REACTIVATE WHEN HEAD COMPONENTS COME BACK AT 6.0

When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered. The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded `<link>` HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="7d869-155">Para proporcionar la clase CSS `my-component` de `Component1`, establezca un vínculo con la hoja de estilo de la biblioteca en el archivo `wwwroot/index.html` (Blazor WebAssembly) o el archivo `Pages/_Host.cshtml` (Blazor Server) de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="7d869-155">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a><span data-ttu-id="7d869-156">Creación de una biblioteca de clases de componentes de Razor con recursos estáticos</span><span class="sxs-lookup"><span data-stu-id="7d869-156">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="7d869-157">Una RCL puede incluir recursos estáticos.</span><span class="sxs-lookup"><span data-stu-id="7d869-157">An RCL can include static assets.</span></span> <span data-ttu-id="7d869-158">Los recursos estáticos están disponibles para cualquier aplicación que use la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="7d869-158">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="7d869-159">Para obtener más información, vea <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="7d869-159">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a><span data-ttu-id="7d869-160">Proporcionar componentes y recursos estáticos a varias aplicaciones de Blazor hospedadas</span><span class="sxs-lookup"><span data-stu-id="7d869-160">Supply components and static assets to multiple hosted Blazor apps</span></span>

<span data-ttu-id="7d869-161">Para obtener más información, vea <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="7d869-161">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-no-locblazor-webassembly"></a><span data-ttu-id="7d869-162">Analizador de compatibilidad con el explorador de Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7d869-162">Browser compatibility analyzer for Blazor WebAssembly</span></span>

<span data-ttu-id="7d869-163">Las aplicaciones Blazor WebAssembly tienen como destino el área expuesta de la API de .NET completa, pero no todas las API de .NET son compatibles con WebAssembly, debido a las restricciones de espacio aislado del explorador.</span><span class="sxs-lookup"><span data-stu-id="7d869-163">Blazor WebAssembly apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id="7d869-164">Las API no compatibles inician la excepción <xref:System.PlatformNotSupportedException> cuando se ejecutan en WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="7d869-164">Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id="7d869-165">Un analizador de compatibilidad de plataforma advierte al desarrollador cuando la aplicación usa API que no son compatibles con las plataformas de destino de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7d869-165">A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id="7d869-166">En el caso de las aplicaciones Blazor WebAssembly, esto significa comprobar que las API se admiten en los exploradores.</span><span class="sxs-lookup"><span data-stu-id="7d869-166">For Blazor WebAssembly apps, this means checking that APIs are supported in browsers.</span></span> <span data-ttu-id="7d869-167">La anotación de API de .NET Framework para el analizador de compatibilidad es un proceso constante, por lo que no todas las API de .NET Framework están anotadas actualmente.</span><span class="sxs-lookup"><span data-stu-id="7d869-167">Annotating .NET framework APIs for the compatibility analyzer is an on-going process, so not all .NET framework API is currently annotated.</span></span>

<span data-ttu-id="7d869-168">Los proyectos de biblioteca de clases de Blazor WebAssembly y Razor habilitan *automáticamente* las comprobaciones de compatibilidad del explorador agregando `browser` como plataforma compatible con el elemento `SupportedPlatform` de MSBuild.</span><span class="sxs-lookup"><span data-stu-id="7d869-168">Blazor WebAssembly and Razor class library projects *automatically* enable browser compatibilty checks by adding `browser` as a supported platform with the `SupportedPlatform` MSBuild item.</span></span> <span data-ttu-id="7d869-169">Los desarrolladores de bibliotecas pueden agregar manualmente el elemento `SupportedPlatform` al archivo de proyecto de una biblioteca para habilitar la característica:</span><span class="sxs-lookup"><span data-stu-id="7d869-169">Library developers can manually add the `SupportedPlatform` item to a library's project file to enable the feature:</span></span>

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

<span data-ttu-id="7d869-170">Al crear una biblioteca, especifique `browser` en <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute> para indicar que los exploradores no admiten una API determinada:</span><span class="sxs-lookup"><span data-stu-id="7d869-170">When authoring a library, indicate that a particular API isn't supported in browsers by specifying `browser` to <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute>:</span></span>

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

<span data-ttu-id="7d869-171">Para más información, vea [Anotación de API como no compatibles en plataformas específicas (repositorio de GitHub dotnet/designs)](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span><span class="sxs-lookup"><span data-stu-id="7d869-171">For more information, see [Annotating APIs as unsupported on specific platforms (dotnet/designs GitHub repository](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span></span>

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="7d869-172">Aislamiento de JavaScript y referencias a objetos en Blazor</span><span class="sxs-lookup"><span data-stu-id="7d869-172">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="7d869-173">Blazor permite el aislamiento de JavaScript en [módulos de JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) estándar.</span><span class="sxs-lookup"><span data-stu-id="7d869-173">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="7d869-174">El aislamiento de JavaScript reporta las siguientes ventajas:</span><span class="sxs-lookup"><span data-stu-id="7d869-174">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="7d869-175">El código JavaScript importado no contamina el espacio de nombres global.</span><span class="sxs-lookup"><span data-stu-id="7d869-175">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="7d869-176">No es necesario que los consumidores de la biblioteca y los componentes importen manualmente el código JavaScript relacionado.</span><span class="sxs-lookup"><span data-stu-id="7d869-176">Consumers of the library and components aren't required to manually import the related JavaScript.</span></span>

<span data-ttu-id="7d869-177">Para obtener más información, vea <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span><span class="sxs-lookup"><span data-stu-id="7d869-177">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="7d869-178">Compilación, empaquetado y envío de bibliotecas a NuGet</span><span class="sxs-lookup"><span data-stu-id="7d869-178">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="7d869-179">Dado que las bibliotecas de componentes son bibliotecas estándar de .NET, se empaquetan y se envían a NuGet de la misma manera que cualquier otra biblioteca.</span><span class="sxs-lookup"><span data-stu-id="7d869-179">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="7d869-180">El empaquetado se realiza mediante el comando [`dotnet pack`](/dotnet/core/tools/dotnet-pack) en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="7d869-180">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="7d869-181">Cargue el paquete en NuGet usando el comando [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) en un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="7d869-181">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7d869-182">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="7d869-182">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="7d869-183">Adición de un archivo de configuración del recortador de lenguaje intermedio (IL) XML a una biblioteca</span><span class="sxs-lookup"><span data-stu-id="7d869-183">Add an XML Intermediate Language (IL) Trimmer configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-trimmer)
* [<span data-ttu-id="7d869-184">Compatibilidad de aislamiento de CSS con bibliotecas de clases Razor</span><span class="sxs-lookup"><span data-stu-id="7d869-184">CSS isolation support with Razor class libraries</span></span>](xref:blazor/components/css-isolation#razor-class-library-rcl-support)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="7d869-185">Agregar un archivo de configuración del enlazador de lenguaje intermedio (IL) XML a una biblioteca</span><span class="sxs-lookup"><span data-stu-id="7d869-185">Add an XML Intermediate Language (IL) Linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
