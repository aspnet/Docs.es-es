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
ms.openlocfilehash: 0748f606314963788e6733ca2ae2ca2123d839b3
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2021
ms.locfileid: "99529987"
---
# <a name="aspnet-core-blazor-css-isolation"></a><span data-ttu-id="470a7-103">Aislamiento de CSS de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="470a7-103">ASP.NET Core Blazor CSS isolation</span></span>

<span data-ttu-id="470a7-104">De [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="470a7-104">By [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="470a7-105">El aislamiento de CSS simplifica la superficie de CSS de una aplicación al evitar dependencias en estilos globales y ayuda a evitar conflictos de estilo entre componentes y bibliotecas.</span><span class="sxs-lookup"><span data-stu-id="470a7-105">CSS isolation simplifies an app's CSS footprint by preventing dependencies on global styles and helps to avoid styling conflicts among components and libraries.</span></span>

## <a name="enable-css-isolation"></a><span data-ttu-id="470a7-106">Habilitación del aislamiento de CSS</span><span class="sxs-lookup"><span data-stu-id="470a7-106">Enable CSS isolation</span></span> 

<span data-ttu-id="470a7-107">Para definir estilos específicos de un componente, cree un archivo `.razor.css` cuyo nombre coincida con el del archivo `.razor` del componente en la misma carpeta.</span><span class="sxs-lookup"><span data-stu-id="470a7-107">To define component-specific styles, create a `.razor.css` file matching the name of the `.razor` file for the component in the same folder.</span></span> <span data-ttu-id="470a7-108">El archivo `.razor.css` es un *archivo CSS con ámbito*.</span><span class="sxs-lookup"><span data-stu-id="470a7-108">The `.razor.css` file is a *scoped CSS file*.</span></span> 

<span data-ttu-id="470a7-109">En un componente `Example` de un archivo `Example.razor`, cree un archivo junto al componente denominado `Example.razor.css`.</span><span class="sxs-lookup"><span data-stu-id="470a7-109">For an `Example` component in an `Example.razor` file, create a file alongside the component named `Example.razor.css`.</span></span> <span data-ttu-id="470a7-110">El archivo `Example.razor.css` debe residir en la misma carpeta que el componente `Example` (`Example.razor`).</span><span class="sxs-lookup"><span data-stu-id="470a7-110">The `Example.razor.css` file must reside in the same folder as the `Example` component (`Example.razor`).</span></span> <span data-ttu-id="470a7-111">El nombre base "`Example`" del archivo **no** distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="470a7-111">The "`Example`" base name of the file is **not** case-sensitive.</span></span>

<span data-ttu-id="470a7-112">`Pages/Example.razor`:</span><span class="sxs-lookup"><span data-stu-id="470a7-112">`Pages/Example.razor`:</span></span>

```razor
@page "/example"

<h1>Scoped CSS Example</h1>
```

<span data-ttu-id="470a7-113">`Pages/Example.razor.css`:</span><span class="sxs-lookup"><span data-stu-id="470a7-113">`Pages/Example.razor.css`:</span></span>

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

<span data-ttu-id="470a7-114">**Los estilos definidos en `Example.razor.css` solo se aplican a la salida representada del componente `Example`** .</span><span class="sxs-lookup"><span data-stu-id="470a7-114">**The styles defined in `Example.razor.css` are only applied to the rendered output of the `Example` component.**</span></span> <span data-ttu-id="470a7-115">El aislamiento de CSS se aplica a los elementos HTML en el archivo Razor coincidente.</span><span class="sxs-lookup"><span data-stu-id="470a7-115">CSS isolation is applied to HTML elements in the matching Razor file.</span></span> <span data-ttu-id="470a7-116">Cualquier declaración CSS `h1` definida en otra ubicación de la aplicación no entra en conflicto con los estilos del componente `Example`.</span><span class="sxs-lookup"><span data-stu-id="470a7-116">Any `h1` CSS declarations defined elsewhere in the app don't conflict with the `Example` component's styles.</span></span>

> [!NOTE]
> <span data-ttu-id="470a7-117">Para garantizar el aislamiento de estilo en el momento de la unión, no se admite la importación de CSS en bloques de código Razor.</span><span class="sxs-lookup"><span data-stu-id="470a7-117">In order to guarantee style isolation when bundling occurs, importing CSS in Razor code blocks isn't supported.</span></span>

## <a name="css-isolation-bundling"></a><span data-ttu-id="470a7-118">Unión del aislamiento de CSS</span><span class="sxs-lookup"><span data-stu-id="470a7-118">CSS isolation bundling</span></span>

<span data-ttu-id="470a7-119">El aislamiento de CSS se produce en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="470a7-119">CSS isolation occurs at build time.</span></span> <span data-ttu-id="470a7-120">Durante este proceso, Blazor reescribe los selectores de CSS para que coincidan con el marcado representado por el componente.</span><span class="sxs-lookup"><span data-stu-id="470a7-120">During this process, Blazor rewrites CSS selectors to match markup rendered by the component.</span></span> <span data-ttu-id="470a7-121">Estos estilos de CSS reescritos se unen y se generan como un recurso estático en `{PROJECT NAME}.styles.css`, donde el marcador de posición `{PROJECT NAME}` es el nombre del paquete o del producto al que se hace referencia.</span><span class="sxs-lookup"><span data-stu-id="470a7-121">These rewritten CSS styles are bundled and produced as a static asset at `{PROJECT NAME}.styles.css`, where the placeholder `{PROJECT NAME}` is the referenced package or product name.</span></span>

<span data-ttu-id="470a7-122">De forma predeterminada, se hace referencia a estos archivos estáticos desde la ruta de acceso raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="470a7-122">These static files are referenced from the root path of the app by default.</span></span> <span data-ttu-id="470a7-123">En la aplicación, haga referencia al archivo unido mediante la referencia dentro de la etiqueta `<head>` del HTML generado:</span><span class="sxs-lookup"><span data-stu-id="470a7-123">In the app, reference the bundled file by inspecting the reference inside the `<head>` tag of the generated HTML:</span></span>

```html
<link href="ProjectName.styles.css" rel="stylesheet">
```

<span data-ttu-id="470a7-124">En el archivo unido, cada componente está asociado a un identificador de ámbito.</span><span class="sxs-lookup"><span data-stu-id="470a7-124">Within the bundled file, each component is associated with a scope identifier.</span></span> <span data-ttu-id="470a7-125">En cada componente con estilo, se anexa un atributo HTML con el formato `b-<10-character-string>`.</span><span class="sxs-lookup"><span data-stu-id="470a7-125">For each styled component, an HTML attribute is appended with the format `b-<10-character-string>`.</span></span> <span data-ttu-id="470a7-126">El identificador es único y diferente para cada aplicación.</span><span class="sxs-lookup"><span data-stu-id="470a7-126">The identifier is unique and different for each app.</span></span> <span data-ttu-id="470a7-127">En el componente `Counter` representado, Blazor anexa un identificador de ámbito al elemento `h1`:</span><span class="sxs-lookup"><span data-stu-id="470a7-127">In the rendered `Counter` component, Blazor appends a scope identifier to the `h1` element:</span></span>

```html
<h1 b-3xxtam6d07>
```

<span data-ttu-id="470a7-128">El archivo `ProjectName.styles.css` usa el identificador de ámbito para agrupar una declaración de estilo con su componente.</span><span class="sxs-lookup"><span data-stu-id="470a7-128">The `ProjectName.styles.css` file uses the scope identifier to group a style declaration with its component.</span></span> <span data-ttu-id="470a7-129">En el ejemplo siguiente se proporciona el estilo del elemento `<h1>` anterior:</span><span class="sxs-lookup"><span data-stu-id="470a7-129">The following example provides the style for the preceding `<h1>` element:</span></span>

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

<span data-ttu-id="470a7-130">En tiempo de compilación, se crea un conjunto del proyecto con la convención `{STATIC WEB ASSETS BASE PATH}/Project.lib.scp.css`, donde el marcador de posición `{STATIC WEB ASSETS BASE PATH}` es la ruta de acceso base a los recursos web estáticos.</span><span class="sxs-lookup"><span data-stu-id="470a7-130">At build time, a project bundle is created with the convention `{STATIC WEB ASSETS BASE PATH}/Project.lib.scp.css`, where the placeholder `{STATIC WEB ASSETS BASE PATH}` is the static web assets base path.</span></span>

<span data-ttu-id="470a7-131">Si se usan otros proyectos, como paquetes NuGet o [bibliotecas de clases de Razor](xref:blazor/components/class-libraries), el archivo unido:</span><span class="sxs-lookup"><span data-stu-id="470a7-131">If other projects are utilized, such as NuGet packages or [Razor class libraries](xref:blazor/components/class-libraries), the bundled file:</span></span>

* <span data-ttu-id="470a7-132">Hace referencia a los estilos mediante importaciones de CSS.</span><span class="sxs-lookup"><span data-stu-id="470a7-132">References the styles using CSS imports.</span></span>
* <span data-ttu-id="470a7-133">No se publica como recurso web estático de la aplicación que consume los estilos.</span><span class="sxs-lookup"><span data-stu-id="470a7-133">Isn't published as a static web asset of the app that consumes the styles.</span></span>

## <a name="child-component-support"></a><span data-ttu-id="470a7-134">Compatibilidad de componente secundario</span><span class="sxs-lookup"><span data-stu-id="470a7-134">Child component support</span></span>

<span data-ttu-id="470a7-135">De forma predeterminada, el aislamiento de CSS solo se aplica al componente que se asocia con el formato `{COMPONENT NAME}.razor.css`, donde el marcador de posición `{COMPONENT NAME}` suele ser el nombre del componente.</span><span class="sxs-lookup"><span data-stu-id="470a7-135">By default, CSS isolation only applies to the component you associate with the format `{COMPONENT NAME}.razor.css`, where the placeholder `{COMPONENT NAME}` is usually the component name.</span></span> <span data-ttu-id="470a7-136">Para aplicar cambios a un componente secundario, use el combinador `::deep` con los elementos descendientes del archivo `.razor.css` del componente primario.</span><span class="sxs-lookup"><span data-stu-id="470a7-136">To apply changes to a child component, use the `::deep` combinator to any descendant elements in the parent component's `.razor.css` file.</span></span> <span data-ttu-id="470a7-137">El combinador `::deep` selecciona los elementos que son *descendientes* del identificador de ámbito generado de un elemento.</span><span class="sxs-lookup"><span data-stu-id="470a7-137">The `::deep` combinator selects elements that are *descendants* of an element's generated scope identifier.</span></span> 

<span data-ttu-id="470a7-138">En el ejemplo siguiente se muestra un componente primario denominado `Parent` con un componente secundario denominado `Child`.</span><span class="sxs-lookup"><span data-stu-id="470a7-138">The following example shows a parent component called `Parent` with a child component called `Child`.</span></span>

<span data-ttu-id="470a7-139">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="470a7-139">`Pages/Parent.razor`:</span></span>

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

<span data-ttu-id="470a7-140">`Shared/Child.razor`:</span><span class="sxs-lookup"><span data-stu-id="470a7-140">`Shared/Child.razor`:</span></span>

```razor
<h1>Child Component</h1>
```

<span data-ttu-id="470a7-141">Actualice la declaración `h1` de `Parent.razor.css` con el combinador `::deep` para indicar que la declaración de estilo de `h1` debe aplicarse al componente primario y a sus elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="470a7-141">Update the `h1` declaration in `Parent.razor.css` with the `::deep` combinator to signify the `h1` style declaration must apply to the parent component and its children.</span></span>

<span data-ttu-id="470a7-142">`Pages/Parent.razor.css`:</span><span class="sxs-lookup"><span data-stu-id="470a7-142">`Pages/Parent.razor.css`:</span></span>

```css
::deep h1 { 
    color: red;
}
```

<span data-ttu-id="470a7-143">El estilo de `h1` ahora se aplica a los componentes `Parent` y `Child` sin necesidad de crear un archivo CSS con ámbito independiente para el componente secundario.</span><span class="sxs-lookup"><span data-stu-id="470a7-143">The `h1` style now applies to the `Parent` and `Child` components without the need to create a separate scoped CSS file for the child component.</span></span>

<span data-ttu-id="470a7-144">El combinador `::deep` solo funciona con elementos descendientes.</span><span class="sxs-lookup"><span data-stu-id="470a7-144">The `::deep` combinator only works with descendant elements.</span></span> <span data-ttu-id="470a7-145">El marcado siguiente aplica los estilos de `h1` a los componentes según lo esperado.</span><span class="sxs-lookup"><span data-stu-id="470a7-145">The following markup applies the `h1` styles to components as expected.</span></span> <span data-ttu-id="470a7-146">El identificador de ámbito del componente primario se aplica al elemento `div`, por lo que se sabe que el explorador hereda los estilos del componente primario.</span><span class="sxs-lookup"><span data-stu-id="470a7-146">The parent component's scope identifier is applied to the `div` element, so the browser knows to inherit styles from the parent component.</span></span>

<span data-ttu-id="470a7-147">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="470a7-147">`Pages/Parent.razor`:</span></span>

```razor
<div>
    <h1>Parent</h1>

    <Child />
</div>
```

<span data-ttu-id="470a7-148">Sin embargo, si se excluye el elemento `div`, se quita la relación descendiente.</span><span class="sxs-lookup"><span data-stu-id="470a7-148">However, excluding the `div` element removes the descendant relationship.</span></span> <span data-ttu-id="470a7-149">En el ejemplo siguiente, el estilo **no** se aplica al componente secundario.</span><span class="sxs-lookup"><span data-stu-id="470a7-149">In the following example, the style is **not** applied to the child component.</span></span>

<span data-ttu-id="470a7-150">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="470a7-150">`Pages/Parent.razor`:</span></span>

```razor
<h1>Parent</h1>

<Child />
```

## <a name="css-preprocessor-support"></a><span data-ttu-id="470a7-151">Compatibilidad del preprocesador de CSS</span><span class="sxs-lookup"><span data-stu-id="470a7-151">CSS preprocessor support</span></span>

<span data-ttu-id="470a7-152">Los preprocesadores de CSS son útiles para mejorar el desarrollo de CSS mediante el uso de características como variables, anidamiento, módulos, mixins y herencia.</span><span class="sxs-lookup"><span data-stu-id="470a7-152">CSS preprocessors are useful for improving CSS development by utilizing features such as variables, nesting, modules, mixins, and inheritance.</span></span> <span data-ttu-id="470a7-153">Aunque el aislamiento de CSS no admite de forma nativa preprocesadores de CSS como Sass o Less, la integración de preprocesadores de CSS se realiza sin problemas siempre que se produzca la compilación del preprocesador antes de que Blazor reescriba los selectores de CSS durante el proceso de compilación.</span><span class="sxs-lookup"><span data-stu-id="470a7-153">While CSS isolation doesn't natively support CSS preprocessors such as Sass or Less, integrating CSS preprocessors is seamless as long as preprocessor compilation occurs before Blazor rewrites the CSS selectors during the build process.</span></span> <span data-ttu-id="470a7-154">Con Visual Studio, por ejemplo, configure la compilación del preprocesador existente como una tarea **Antes de la compilación** en el Explorador del Ejecutor de tareas de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="470a7-154">Using Visual Studio for example, configure existing preprocessor compilation as a **Before Build** task in the Visual Studio Task Runner Explorer.</span></span>

<span data-ttu-id="470a7-155">Muchos paquetes NuGet de terceros, como [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), pueden compilar archivos SASS/SCSS al principio del proceso de compilación antes de que se produzca el aislamiento de CSS y no requieren ninguna configuración adicional.</span><span class="sxs-lookup"><span data-stu-id="470a7-155">Many third-party NuGet packages, such as [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), can compile SASS/SCSS files at the beginning of the build process before CSS isolation occurs, and no additional configuration is required.</span></span>

## <a name="css-isolation-configuration"></a><span data-ttu-id="470a7-156">Configuración del aislamiento de CSS</span><span class="sxs-lookup"><span data-stu-id="470a7-156">CSS isolation configuration</span></span>

<span data-ttu-id="470a7-157">El aislamiento de CSS está diseñado para poder trabajar de inmediato, pero proporciona configuración para algunos escenarios avanzados, por ejemplo cuando hay dependencias en herramientas o flujos de trabajo existentes.</span><span class="sxs-lookup"><span data-stu-id="470a7-157">CSS isolation is designed to work out-of-the-box but provides configuration for some advanced scenarios, such as when there are dependencies on existing tools or workflows.</span></span>

### <a name="customize-scope-identifier-format"></a><span data-ttu-id="470a7-158">Personalización del formato del identificador de ámbito</span><span class="sxs-lookup"><span data-stu-id="470a7-158">Customize scope identifier format</span></span>

<span data-ttu-id="470a7-159">De forma predeterminada, los identificadores de ámbito usan el formato `b-<10-character-string>`.</span><span class="sxs-lookup"><span data-stu-id="470a7-159">By default, scope identifiers use the format `b-<10-character-string>`.</span></span> <span data-ttu-id="470a7-160">Para personalizar el formato de un identificador de ámbito, actualice el archivo del proyecto a un patrón deseado:</span><span class="sxs-lookup"><span data-stu-id="470a7-160">To customize the scope identifier format, update the project file to a desired pattern:</span></span>

```xml
<ItemGroup>
  <None Update="Pages/Example.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="470a7-161">En el ejemplo anterior, el CSS generado para `Example.Razor.css` cambia su identificador de ámbito de `b-<10-character-string>` a `my-custom-scope-identifier`.</span><span class="sxs-lookup"><span data-stu-id="470a7-161">In the preceding example, the CSS generated for `Example.Razor.css` changes its scope identifier from `b-<10-character-string>` to `my-custom-scope-identifier`.</span></span>

<span data-ttu-id="470a7-162">Use identificadores de ámbito para lograr la herencia con archivos CSS de ámbito.</span><span class="sxs-lookup"><span data-stu-id="470a7-162">Use scope identifiers to achieve inheritance with scoped CSS files.</span></span> <span data-ttu-id="470a7-163">En el siguiente ejemplo del archivo del proyecto, un archivo `BaseComponent.razor.css` contiene estilos comunes en todos los componentes.</span><span class="sxs-lookup"><span data-stu-id="470a7-163">In the following project file example, a `BaseComponent.razor.css` file contains common styles across components.</span></span> <span data-ttu-id="470a7-164">Un archivo `DerivedComponent.razor.css` hereda estos estilos.</span><span class="sxs-lookup"><span data-stu-id="470a7-164">A `DerivedComponent.razor.css` file inherits these styles.</span></span>

```xml
<ItemGroup>
  <None Update="Pages/BaseComponent.razor.css" CssScope="my-custom-scope-identifier" />
  <None Update="Pages/DerivedComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="470a7-165">Use el operador comodín (`*`) para compartir los identificadores de ámbito en varios archivos:</span><span class="sxs-lookup"><span data-stu-id="470a7-165">Use the wildcard (`*`) operator to share scope identifiers across multiple files:</span></span>

```xml
<ItemGroup>
  <None Update="Pages/*.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

### <a name="change-base-path-for-static-web-assets"></a><span data-ttu-id="470a7-166">Cambio de la ruta de acceso base de recursos web estáticos</span><span class="sxs-lookup"><span data-stu-id="470a7-166">Change base path for static web assets</span></span>

<span data-ttu-id="470a7-167">El archivo `scoped.styles.css` se genera en la raíz de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="470a7-167">The `scoped.styles.css` file is generated at the root of the app.</span></span> <span data-ttu-id="470a7-168">En el archivo del proyecto, use la propiedad `StaticWebAssetBasePath` para cambiar la ruta de acceso predeterminada.</span><span class="sxs-lookup"><span data-stu-id="470a7-168">In the project file, use the `StaticWebAssetBasePath` property to change the default path.</span></span> <span data-ttu-id="470a7-169">En el ejemplo siguiente se coloca el archivo `scoped.styles.css`, y el resto de los recursos de la aplicación, en la ruta de acceso `_content`:</span><span class="sxs-lookup"><span data-stu-id="470a7-169">The following example places the `scoped.styles.css` file, and the rest of the app's assets, at the `_content` path:</span></span>

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a><span data-ttu-id="470a7-170">Deshabilitación de la unión automática</span><span class="sxs-lookup"><span data-stu-id="470a7-170">Disable automatic bundling</span></span>

<span data-ttu-id="470a7-171">Para no usar el modo en que Blazor publica y carga archivos con ámbito en tiempo de ejecución, use la propiedad `DisableScopedCssBundling`.</span><span class="sxs-lookup"><span data-stu-id="470a7-171">To opt out of how Blazor publishes and loads scoped files at runtime, use the `DisableScopedCssBundling` property.</span></span> <span data-ttu-id="470a7-172">Al usar esta propiedad, otras herramientas o procesos son responsables de tomar los archivos CSS aislados del directorio `obj` y de publicarlos y cargarlos en tiempo de ejecución:</span><span class="sxs-lookup"><span data-stu-id="470a7-172">When using this property, it means other tools or processes are responsible for taking the isolated CSS files from the `obj` directory and publishing and loading them at runtime:</span></span>

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```

## <a name="razor-class-library-rcl-support"></a><span data-ttu-id="470a7-173">Razor Compatibilidad con la biblioteca de clases (RCL)</span><span class="sxs-lookup"><span data-stu-id="470a7-173">Razor class library (RCL) support</span></span>

<span data-ttu-id="470a7-174">Cuando una [Razor biblioteca de clases (RCL)](xref:razor-pages/ui-class) proporciona estilos aislados, el atributo `href` de la etiqueta `<link>` apunta a `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`, donde los marcadores de posición son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="470a7-174">When a [Razor class library (RCL)](xref:razor-pages/ui-class) provides isolated styles, the `<link>` tag's `href` attribute points to `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`, where the placeholders are:</span></span>

* <span data-ttu-id="470a7-175">`{STATIC WEB ASSET BASE PATH}`: ruta de acceso base del recurso web estático.</span><span class="sxs-lookup"><span data-stu-id="470a7-175">`{STATIC WEB ASSET BASE PATH}`: The static web asset base path.</span></span>
* <span data-ttu-id="470a7-176">`{ASSEMBLY NAME}`: nombre del ensamblado de la biblioteca de clases.</span><span class="sxs-lookup"><span data-stu-id="470a7-176">`{ASSEMBLY NAME}`: The class library's assembly name.</span></span>

<span data-ttu-id="470a7-177">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="470a7-177">In the following example:</span></span>

* <span data-ttu-id="470a7-178">La ruta de acceso base del recurso web estático es `_content/ClassLib`.</span><span class="sxs-lookup"><span data-stu-id="470a7-178">The static web asset base path is `_content/ClassLib`.</span></span>
* <span data-ttu-id="470a7-179">El nombre del ensamblado de la biblioteca de clases es `ClassLib`.</span><span class="sxs-lookup"><span data-stu-id="470a7-179">The class library's assembly name is `ClassLib`.</span></span>

<span data-ttu-id="470a7-180">`wwwroot/index.html` (Blazor WebAssembly) o `Pages_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="470a7-180">`wwwroot/index.html` (Blazor WebAssembly) or `Pages_Host.cshtml` (Blazor Server):</span></span>

```html
<link href="_content/ClassLib/ClassLib.bundle.scp.css" rel="stylesheet">
```

<span data-ttu-id="470a7-181">Para obtener más información sobre RCL y bibliotecas de componentes, vea lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="470a7-181">For more information on RCLs and component libraries, see:</span></span>

* <xref:razor-pages/ui-class>
* <span data-ttu-id="470a7-182"><xref:blazor/components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="470a7-182"><xref:blazor/components/class-libraries>.</span></span>
