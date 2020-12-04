---
title: Aislamiento de CSS de Blazor de ASP.NET Core
author: daveabrock
description: Conozca que el aislamiento de CSS permite aplicar ámbito de CSS a los componentes, lo que puede simplificar CSS y evitar conflictos con otros componentes o bibliotecas.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/css-isolation
ms.openlocfilehash: 92545eab4004f6b67080f79d64b94bb424d5a102
ms.sourcegitcommit: 43a540e703b9096921de27abc6b66bc0783fe905
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320088"
---
# <a name="aspnet-core-no-locblazor-css-isolation"></a><span data-ttu-id="e01bc-103">Aislamiento de CSS de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e01bc-103">ASP.NET Core Blazor CSS isolation</span></span>

<span data-ttu-id="e01bc-104">De [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="e01bc-104">By [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="e01bc-105">El aislamiento de CSS simplifica la superficie de CSS de una aplicación al evitar dependencias en estilos globales y ayuda a evitar conflictos de estilo entre componentes y bibliotecas.</span><span class="sxs-lookup"><span data-stu-id="e01bc-105">CSS isolation simplifies an app's CSS footprint by preventing dependencies on global styles and helps to avoid styling conflicts among components and libraries.</span></span>

## <a name="enable-css-isolation"></a><span data-ttu-id="e01bc-106">Habilitación del aislamiento de CSS</span><span class="sxs-lookup"><span data-stu-id="e01bc-106">Enable CSS isolation</span></span> 

<span data-ttu-id="e01bc-107">Para definir estilos específicos de un componente, cree un archivo `.razor.css` cuyo nombre coincida con el del archivo `.razor` del componente.</span><span class="sxs-lookup"><span data-stu-id="e01bc-107">To define component-specific styles, create a `.razor.css` file matching the name of the `.razor` file for the component.</span></span> <span data-ttu-id="e01bc-108">Este archivo `.razor.css` es un *archivo CSS con ámbito*.</span><span class="sxs-lookup"><span data-stu-id="e01bc-108">This `.razor.css` file is a *scoped CSS file*.</span></span> 

<span data-ttu-id="e01bc-109">En un componente `MyComponent` que tenga un archivo `MyComponent.razor`, cree un archivo junto al componente denominado `MyComponent.razor.css`.</span><span class="sxs-lookup"><span data-stu-id="e01bc-109">For a `MyComponent` component that has a `MyComponent.razor` file, create a file alongside the component called `MyComponent.razor.css`.</span></span> <span data-ttu-id="e01bc-110">El valor `MyComponent` del nombre de archivo `.razor.css` **no** distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="e01bc-110">The `MyComponent` value in the `.razor.css` filename is **not** case-sensitive.</span></span>

<span data-ttu-id="e01bc-111">Por ejemplo, para agregar aislamiento de CSS al componente `Counter` de la plantilla de proyecto de Blazor predeterminada, agregue un nuevo archivo denominado `Counter.razor.css` junto con el archivo `Counter.razor` y luego agregue el siguiente CSS:</span><span class="sxs-lookup"><span data-stu-id="e01bc-111">For example to add CSS isolation to the `Counter` component in the default Blazor project template, add a new file named `Counter.razor.css` alongside the `Counter.razor` file, then add the following CSS:</span></span>

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

<span data-ttu-id="e01bc-112">Los estilos definidos en `Counter.razor.css` solo se aplican a la salida representada del componente `Counter`.</span><span class="sxs-lookup"><span data-stu-id="e01bc-112">The styles defined in `Counter.razor.css` are only applied to the rendered output of the `Counter` component.</span></span> <span data-ttu-id="e01bc-113">Cualquier declaración CSS `h1` definida en otra ubicación de la aplicación no entra en conflicto con los estilos de `Counter`.</span><span class="sxs-lookup"><span data-stu-id="e01bc-113">Any `h1` CSS declarations defined elsewhere in the app don't conflict with `Counter` styles.</span></span>

> [!NOTE]
> <span data-ttu-id="e01bc-114">Con el fin de garantizar el aislamiento de estilo en el momento de la unión, no se admiten bloques de Razor `@import` con archivos CSS con ámbito.</span><span class="sxs-lookup"><span data-stu-id="e01bc-114">In order to guarantee style isolation when bundling occurs, `@import` Razor blocks aren't supported with scoped CSS files.</span></span>

## <a name="css-isolation-bundling"></a><span data-ttu-id="e01bc-115">Unión del aislamiento de CSS</span><span class="sxs-lookup"><span data-stu-id="e01bc-115">CSS isolation bundling</span></span>

<span data-ttu-id="e01bc-116">El aislamiento de CSS se produce en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="e01bc-116">CSS isolation occurs at build time.</span></span> <span data-ttu-id="e01bc-117">Durante este proceso, Blazor reescribe los selectores de CSS para que coincidan con el marcado representado por el componente.</span><span class="sxs-lookup"><span data-stu-id="e01bc-117">During this process, Blazor rewrites CSS selectors to match markup rendered by the component.</span></span> <span data-ttu-id="e01bc-118">Estos estilos de CSS reescritos se unen y se generan como un recurso estático en `{PROJECT NAME}.styles.css`, donde el marcador de posición `{PROJECT NAME}` es el nombre del paquete o del producto al que se hace referencia.</span><span class="sxs-lookup"><span data-stu-id="e01bc-118">These rewritten CSS styles are bundled and produced as a static asset at `{PROJECT NAME}.styles.css`, where the placeholder `{PROJECT NAME}` is the referenced package or product name.</span></span>

<span data-ttu-id="e01bc-119">De forma predeterminada, se hace referencia a estos archivos estáticos desde la ruta de acceso raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e01bc-119">These static files are referenced from the root path of the app by default.</span></span> <span data-ttu-id="e01bc-120">En la aplicación, haga referencia al archivo unido mediante la referencia dentro de la etiqueta `<head>` del HTML generado:</span><span class="sxs-lookup"><span data-stu-id="e01bc-120">In the app, reference the bundled file by inspecting the reference inside the `<head>` tag of the generated HTML:</span></span>

```html
<link href="MyProjectName.styles.css" rel="stylesheet">
```

<span data-ttu-id="e01bc-121">En el archivo unido, cada componente está asociado a un identificador de ámbito.</span><span class="sxs-lookup"><span data-stu-id="e01bc-121">Within the bundled file, each component is associated with a scope identifier.</span></span> <span data-ttu-id="e01bc-122">En cada componente con estilo, se anexa un atributo HTML con el formato `b-<10-character-string>`.</span><span class="sxs-lookup"><span data-stu-id="e01bc-122">For each styled component, an HTML attribute is appended with the format `b-<10-character-string>`.</span></span> <span data-ttu-id="e01bc-123">El identificador es único y diferente para cada aplicación.</span><span class="sxs-lookup"><span data-stu-id="e01bc-123">The identifier is unique and different for each app.</span></span> <span data-ttu-id="e01bc-124">En el componente `Counter` representado, Blazor anexa un identificador de ámbito al elemento `h1`:</span><span class="sxs-lookup"><span data-stu-id="e01bc-124">In the rendered `Counter` component, Blazor appends a scope identifier to the `h1` element:</span></span>

```html
<h1 b-3xxtam6d07>
```

<span data-ttu-id="e01bc-125">El archivo `MyProjectName.styles.css` usa el identificador de ámbito para agrupar una declaración de estilo con su componente.</span><span class="sxs-lookup"><span data-stu-id="e01bc-125">The `MyProjectName.styles.css` file uses the scope identifier to group a style declaration with its component.</span></span> <span data-ttu-id="e01bc-126">En el ejemplo siguiente se proporciona el estilo del elemento `<h1>` anterior:</span><span class="sxs-lookup"><span data-stu-id="e01bc-126">The following example provides the style for the preceding `<h1>` element:</span></span>

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

<span data-ttu-id="e01bc-127">En tiempo de compilación, se crea un conjunto del proyecto con la convención `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css`, donde el marcador de posición `{STATIC WEB ASSETS BASE PATH}` es la ruta de acceso base a los recursos web estáticos.</span><span class="sxs-lookup"><span data-stu-id="e01bc-127">At build time, a project bundle is created with the convention `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css`, where the placeholder `{STATIC WEB ASSETS BASE PATH}` is the static web assets base path.</span></span>

<span data-ttu-id="e01bc-128">Si se usan otros proyectos, como paquetes NuGet o [bibliotecas de clases de Razor](xref:blazor/components/class-libraries), el archivo unido:</span><span class="sxs-lookup"><span data-stu-id="e01bc-128">If other projects are utilized, such as NuGet packages or [Razor class libraries](xref:blazor/components/class-libraries), the bundled file:</span></span>

* <span data-ttu-id="e01bc-129">Hace referencia a los estilos mediante importaciones de CSS.</span><span class="sxs-lookup"><span data-stu-id="e01bc-129">References the styles using CSS imports.</span></span>
* <span data-ttu-id="e01bc-130">No se publica como recurso web estático de la aplicación que consume los estilos.</span><span class="sxs-lookup"><span data-stu-id="e01bc-130">Isn't published as a static web asset of the app that consumes the styles.</span></span>

## <a name="child-component-support"></a><span data-ttu-id="e01bc-131">Compatibilidad de componente secundario</span><span class="sxs-lookup"><span data-stu-id="e01bc-131">Child component support</span></span>

<span data-ttu-id="e01bc-132">De forma predeterminada, el aislamiento de CSS solo se aplica al componente que se asocia con el formato `{COMPONENT NAME}.razor.css`, donde el marcador de posición `{COMPONENT NAME}` suele ser el nombre del componente.</span><span class="sxs-lookup"><span data-stu-id="e01bc-132">By default, CSS isolation only applies to the component you associate with the format `{COMPONENT NAME}.razor.css`, where the placeholder `{COMPONENT NAME}` is usually the component name.</span></span> <span data-ttu-id="e01bc-133">Para aplicar cambios a un componente secundario, use el combinador `::deep` con los elementos descendientes del archivo `.razor.css` del componente primario.</span><span class="sxs-lookup"><span data-stu-id="e01bc-133">To apply changes to a child component, use the `::deep` combinator to any descendant elements in the parent component's `.razor.css` file.</span></span> <span data-ttu-id="e01bc-134">El combinador `::deep` selecciona los elementos que son *descendientes* del identificador de ámbito generado de un elemento.</span><span class="sxs-lookup"><span data-stu-id="e01bc-134">The `::deep` combinator selects elements that are *descendants* of an element's generated scope identifier.</span></span> 

<span data-ttu-id="e01bc-135">En el ejemplo siguiente se muestra un componente primario denominado `Parent` con un componente secundario denominado `Child`.</span><span class="sxs-lookup"><span data-stu-id="e01bc-135">The following example shows a parent component called `Parent` with a child component called `Child`.</span></span>

<span data-ttu-id="e01bc-136">`Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="e01bc-136">`Parent.razor`:</span></span>

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

<span data-ttu-id="e01bc-137">`Child.razor`:</span><span class="sxs-lookup"><span data-stu-id="e01bc-137">`Child.razor`:</span></span>

```razor
<h1>Child Component</h1>
```

<span data-ttu-id="e01bc-138">Actualice la declaración `h1` de `Parent.razor.css` con el combinador `::deep` para indicar que la declaración de estilo de `h1` debe aplicarse al componente primario y a sus elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="e01bc-138">Update the `h1` declaration in `Parent.razor.css` with the `::deep` combinator to signify the `h1` style declaration must apply to the parent component and its children:</span></span>

```css
::deep h1 { 
    color: red;
}
```

<span data-ttu-id="e01bc-139">El estilo de `h1` ahora se aplica a los componentes `Parent` y `Child` sin necesidad de crear un archivo CSS con ámbito independiente para el componente secundario.</span><span class="sxs-lookup"><span data-stu-id="e01bc-139">The `h1` style now applies to the `Parent` and `Child` components without the need to create a separate scoped CSS file for the child component.</span></span>

> [!NOTE]
> <span data-ttu-id="e01bc-140">El combinador `::deep` solo funciona con elementos descendientes.</span><span class="sxs-lookup"><span data-stu-id="e01bc-140">The `::deep` combinator only works with descendant elements.</span></span> <span data-ttu-id="e01bc-141">La siguiente estructura HTML aplica los estilos de `h1` a los componentes según lo esperado:</span><span class="sxs-lookup"><span data-stu-id="e01bc-141">The following HTML structure applies the `h1` styles to components as expected:</span></span>
> 
> ```razor
> <div>
>     <h1>Parent</h1>
>
>     <Child />
> </div>
> ```
>
> <span data-ttu-id="e01bc-142">En este escenario, ASP.NET Core aplica el identificador de ámbito del componente primario al elemento `div`, por lo que se sabe que el explorador hereda los estilos del componente primario.</span><span class="sxs-lookup"><span data-stu-id="e01bc-142">In this scenario, ASP.NET Core applies the parent component's scope identifier to the `div` element, so the browser knows to inherit styles from the parent component.</span></span>
>
> <span data-ttu-id="e01bc-143">Pero, si se excluye el elemento `div`, se quita la relación de descendiente y el estilo **no** se aplica al componente secundario:</span><span class="sxs-lookup"><span data-stu-id="e01bc-143">However, excluding the `div` element removes the descendant relationship, and the style is **not** applied to the child component:</span></span>
>
> ```razor
> <h1>Parent</h1>
>
> <Child />
> ```

## <a name="css-preprocessor-support"></a><span data-ttu-id="e01bc-144">Compatibilidad del preprocesador de CSS</span><span class="sxs-lookup"><span data-stu-id="e01bc-144">CSS preprocessor support</span></span>

<span data-ttu-id="e01bc-145">Los preprocesadores de CSS son útiles para mejorar el desarrollo de CSS mediante el uso de características como variables, anidamiento, módulos, mixins y herencia.</span><span class="sxs-lookup"><span data-stu-id="e01bc-145">CSS preprocessors are useful for improving CSS development by utilizing features such as variables, nesting, modules, mixins, and inheritance.</span></span> <span data-ttu-id="e01bc-146">Aunque el aislamiento de CSS no admite de forma nativa preprocesadores de CSS como Sass o Less, la integración de preprocesadores de CSS se realiza sin problemas siempre que se produzca la compilación del preprocesador antes de que Blazor reescriba los selectores de CSS durante el proceso de compilación.</span><span class="sxs-lookup"><span data-stu-id="e01bc-146">While CSS isolation doesn't natively support CSS preprocessors such as Sass or Less, integrating CSS preprocessors is seamless as long as preprocessor compilation occurs before Blazor rewrites the CSS selectors during the build process.</span></span> <span data-ttu-id="e01bc-147">Con Visual Studio, por ejemplo, configure la compilación del preprocesador existente como una tarea **Antes de la compilación** en el Explorador del Ejecutor de tareas de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e01bc-147">Using Visual Studio for example, configure existing preprocessor compilation as a **Before Build** task in the Visual Studio Task Runner Explorer.</span></span>

<span data-ttu-id="e01bc-148">Muchos paquetes NuGet de terceros, como [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), pueden compilar archivos SASS/SCSS al principio del proceso de compilación antes de que se produzca el aislamiento de CSS y no requieren ninguna configuración adicional.</span><span class="sxs-lookup"><span data-stu-id="e01bc-148">Many third-party NuGet packages, such as [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), can compile SASS/SCSS files at the beginning of the build process before CSS isolation occurs, and no additional configuration is required.</span></span>

## <a name="css-isolation-configuration"></a><span data-ttu-id="e01bc-149">Configuración del aislamiento de CSS</span><span class="sxs-lookup"><span data-stu-id="e01bc-149">CSS isolation configuration</span></span>

<span data-ttu-id="e01bc-150">El aislamiento de CSS está diseñado para poder trabajar de inmediato, pero proporciona configuración para algunos escenarios avanzados, por ejemplo cuando hay dependencias en herramientas o flujos de trabajo existentes.</span><span class="sxs-lookup"><span data-stu-id="e01bc-150">CSS isolation is designed to work out-of-the-box but provides configuration for some advanced scenarios, such as when there are dependencies on existing tools or workflows.</span></span>

### <a name="customize-scope-identifier-format"></a><span data-ttu-id="e01bc-151">Personalización del formato del identificador de ámbito</span><span class="sxs-lookup"><span data-stu-id="e01bc-151">Customize scope identifier format</span></span>

<span data-ttu-id="e01bc-152">De forma predeterminada, los identificadores de ámbito usan el formato `b-<10-character-string>`.</span><span class="sxs-lookup"><span data-stu-id="e01bc-152">By default, scope identifiers use the format `b-<10-character-string>`.</span></span> <span data-ttu-id="e01bc-153">Para personalizar el formato de un identificador de ámbito, actualice el archivo del proyecto a un patrón deseado:</span><span class="sxs-lookup"><span data-stu-id="e01bc-153">To customize the scope identifier format, update the project file to a desired pattern:</span></span>

```xml
<ItemGroup>
    <None Update="MyComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="e01bc-154">En el ejemplo anterior, el CSS generado para `MyComponent.Razor.css` cambia su identificador de ámbito de `b-<10-character-string>` a `my-custom-scope-identifier`.</span><span class="sxs-lookup"><span data-stu-id="e01bc-154">In the preceding example, the CSS generated for `MyComponent.Razor.css` changes its scope identifier from `b-<10-character-string>` to `my-custom-scope-identifier`.</span></span>

### <a name="change-base-path-for-static-web-assets"></a><span data-ttu-id="e01bc-155">Cambio de la ruta de acceso base de recursos web estáticos</span><span class="sxs-lookup"><span data-stu-id="e01bc-155">Change base path for static web assets</span></span>

<span data-ttu-id="e01bc-156">El archivo `scoped.styles.css` se genera en la raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e01bc-156">The `scoped.styles.css` file is generated at the root of the app.</span></span> <span data-ttu-id="e01bc-157">En el archivo del proyecto, use la propiedad `StaticWebAssetBasePath` para cambiar la ruta de acceso predeterminada.</span><span class="sxs-lookup"><span data-stu-id="e01bc-157">In the project file, use the `StaticWebAssetBasePath` property to change the default path.</span></span> <span data-ttu-id="e01bc-158">En el ejemplo siguiente se coloca el archivo `scoped.styles.css`, y el resto de los recursos de la aplicación, en la ruta de acceso `_content`:</span><span class="sxs-lookup"><span data-stu-id="e01bc-158">The following example places the `scoped.styles.css` file, and the rest of the app's assets, at the `_content` path:</span></span>

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a><span data-ttu-id="e01bc-159">Deshabilitación de la unión automática</span><span class="sxs-lookup"><span data-stu-id="e01bc-159">Disable automatic bundling</span></span>

<span data-ttu-id="e01bc-160">Para no usar el modo en que Blazor publica y carga archivos con ámbito en tiempo de ejecución, use la propiedad `DisableScopedCssBundling`.</span><span class="sxs-lookup"><span data-stu-id="e01bc-160">To opt out of how Blazor publishes and loads scoped files at runtime, use the `DisableScopedCssBundling` property.</span></span> <span data-ttu-id="e01bc-161">Al usar esta propiedad, otras herramientas o procesos son responsables de tomar los archivos CSS aislados del directorio `obj` y de publicarlos y cargarlos en tiempo de ejecución:</span><span class="sxs-lookup"><span data-stu-id="e01bc-161">When using this property, it means other tools or processes are responsible for taking the isolated CSS files from the `obj` directory and publishing and loading them at runtime:</span></span>

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```

## <a name="no-locrazor-class-library-rcl-support"></a><span data-ttu-id="e01bc-162">Razor Compatibilidad con la biblioteca de clases (RCL)</span><span class="sxs-lookup"><span data-stu-id="e01bc-162">Razor class library (RCL) support</span></span>

<span data-ttu-id="e01bc-163">Cuando una [Razor biblioteca de clases (RCL)](xref:razor-pages/ui-class) proporciona estilos aislados, el atributo `href` de la etiqueta `<link>` apunta a `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`, donde los marcadores de posición son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="e01bc-163">When a [Razor class library (RCL)](xref:razor-pages/ui-class) provides isolated styles, the `<link>` tag's `href` attribute points to `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`, where the placeholders are:</span></span>

* <span data-ttu-id="e01bc-164">`{STATIC WEB ASSET BASE PATH}`: ruta de acceso base del recurso web estático.</span><span class="sxs-lookup"><span data-stu-id="e01bc-164">`{STATIC WEB ASSET BASE PATH}`: The static web asset base path.</span></span>
* <span data-ttu-id="e01bc-165">`{ASSEMBLY NAME}`: nombre del ensamblado de la biblioteca de clases.</span><span class="sxs-lookup"><span data-stu-id="e01bc-165">`{ASSEMBLY NAME}`: The class library's assembly name.</span></span>

<span data-ttu-id="e01bc-166">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e01bc-166">In the following example:</span></span>

* <span data-ttu-id="e01bc-167">La ruta de acceso base del recurso web estático es `_content/ClassLib`.</span><span class="sxs-lookup"><span data-stu-id="e01bc-167">The static web asset base path is `_content/ClassLib`.</span></span>
* <span data-ttu-id="e01bc-168">El nombre del ensamblado de la biblioteca de clases es `ClassLib`.</span><span class="sxs-lookup"><span data-stu-id="e01bc-168">The class library's assembly name is `ClassLib`.</span></span>

```html
<link href="_content/ClassLib/ClassLib.bundle.scp.css" rel="stylesheet">
```

<span data-ttu-id="e01bc-169">Para obtener más información sobre RCL y bibliotecas de componentes, vea lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e01bc-169">For more information on RCLs and component libraries, see:</span></span>

* <xref:razor-pages/ui-class>
* <span data-ttu-id="e01bc-170"><xref:blazor/components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="e01bc-170"><xref:blazor/components/class-libraries>.</span></span>
