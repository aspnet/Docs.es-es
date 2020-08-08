---
title: referencia de la sintaxis de Razor para ASP.NET Core
author: rick-anderson
description: Obtenga información sobre la Razor Sintaxis de marcado para insertar código basado en servidor en páginas Web.
ms.author: riande
ms.date: 02/12/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/razor
ms.openlocfilehash: 4b418c53535965eae6b41f3297b0c6336fb993d5
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020592"
---
# <a name="no-locrazor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="a6440-103">Razorreferencia de sintaxis para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a6440-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="a6440-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)y [dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="a6440-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="a6440-105">Razores una sintaxis de marcado para insertar código basado en servidor en páginas Web.</span><span class="sxs-lookup"><span data-stu-id="a6440-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="a6440-106">La Razor Sintaxis consta de Razor marcado, C# y HTML.</span><span class="sxs-lookup"><span data-stu-id="a6440-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="a6440-107">Los archivos que contienen Razor generalmente tienen una extensión de archivo *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a6440-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="a6440-108">Razortambién se encuentra en archivos de [ Razor componentes](xref:blazor/components/index) (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="a6440-108">Razor is also found in [Razor components](xref:blazor/components/index) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="a6440-109">Representación de HTML</span><span class="sxs-lookup"><span data-stu-id="a6440-109">Rendering HTML</span></span>

<span data-ttu-id="a6440-110">El Razor idioma predeterminado es HTML.</span><span class="sxs-lookup"><span data-stu-id="a6440-110">The default Razor language is HTML.</span></span> <span data-ttu-id="a6440-111">La representación de HTML a partir del Razor marcado no es diferente de representar HTML desde un archivo HTML.</span><span class="sxs-lookup"><span data-stu-id="a6440-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="a6440-112">*.cshtml* Razor El servidor no ha cambiado el formato HTML en los archivos. cshtml.</span><span class="sxs-lookup"><span data-stu-id="a6440-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="no-locrazor-syntax"></a><span data-ttu-id="a6440-113">Sintaxis de Razor</span><span class="sxs-lookup"><span data-stu-id="a6440-113">Razor syntax</span></span>

<span data-ttu-id="a6440-114">Razoradmite C# y usa el `@` símbolo para pasar de HTML a C#.</span><span class="sxs-lookup"><span data-stu-id="a6440-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="a6440-115">Razorevalúa las expresiones de C# y las representa en la salida HTML.</span><span class="sxs-lookup"><span data-stu-id="a6440-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="a6440-116">Cuando un `@` símbolo va seguido de una [ Razor palabra clave reservada](#razor-reserved-keywords), realiza la transición a un Razor marcado específico.</span><span class="sxs-lookup"><span data-stu-id="a6440-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="a6440-117">en caso contrario, realiza la transición a C# simple.</span><span class="sxs-lookup"><span data-stu-id="a6440-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="a6440-118">Para escapar un `@` símbolo en Razor el marcado, use un segundo `@` símbolo:</span><span class="sxs-lookup"><span data-stu-id="a6440-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="a6440-119">El código aparecerá en HTML con un solo símbolo `@`:</span><span class="sxs-lookup"><span data-stu-id="a6440-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="a6440-120">El contenido y los atributos HTML que tienen direcciones de correo electrónico no tratan el símbolo `@` como un carácter de transición.</span><span class="sxs-lookup"><span data-stu-id="a6440-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="a6440-121">Las direcciones de correo electrónico en el ejemplo siguiente no se tocan mediante el Razor análisis:</span><span class="sxs-lookup"><span data-stu-id="a6440-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-no-locrazor-expressions"></a><span data-ttu-id="a6440-122">RazorExpresiones IMPLÍCITAS</span><span class="sxs-lookup"><span data-stu-id="a6440-122">Implicit Razor expressions</span></span>

<span data-ttu-id="a6440-123">Las Razor expresiones implícitas comienzan con `@` seguido de código C#:</span><span class="sxs-lookup"><span data-stu-id="a6440-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="a6440-124">Con la excepción de la palabra clave de C# `await`, las expresiones implícitas no deben contener espacios.</span><span class="sxs-lookup"><span data-stu-id="a6440-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="a6440-125">Si la instrucción de C# tiene un final claro, se pueden entremezclar espacios:</span><span class="sxs-lookup"><span data-stu-id="a6440-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="a6440-126">Las expresiones implícitas **no pueden** contener tipos genéricos de C#, ya que los caracteres dentro de los corchetes (`<>`) se interpretan como una etiqueta HTML.</span><span class="sxs-lookup"><span data-stu-id="a6440-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="a6440-127">El siguiente código **no** es válido:</span><span class="sxs-lookup"><span data-stu-id="a6440-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="a6440-128">El código anterior genera un error del compilador similar a uno de los siguientes:</span><span class="sxs-lookup"><span data-stu-id="a6440-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="a6440-129">El elemento "int" no estaba cerrado.</span><span class="sxs-lookup"><span data-stu-id="a6440-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="a6440-130">Todos los elementos deben ser de autocierre o tener una etiqueta de fin coincidente.</span><span class="sxs-lookup"><span data-stu-id="a6440-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="a6440-131">No se puede convertir el grupo de métodos "GenericMethod" en el tipo no delegado "object".</span><span class="sxs-lookup"><span data-stu-id="a6440-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="a6440-132">¿Intentó invocar el método?</span><span class="sxs-lookup"><span data-stu-id="a6440-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="a6440-133">Las llamadas a métodos genéricos se deben encapsular en una [ Razor expresión explícita](#explicit-razor-expressions) o en un [ Razor bloque de código](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="a6440-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-no-locrazor-expressions"></a><span data-ttu-id="a6440-134">Expresiones explícitas Razor</span><span class="sxs-lookup"><span data-stu-id="a6440-134">Explicit Razor expressions</span></span>

<span data-ttu-id="a6440-135">Las Razor expresiones explícitas están compuestas por un `@` símbolo con paréntesis equilibrados.</span><span class="sxs-lookup"><span data-stu-id="a6440-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="a6440-136">Para representar la hora de la semana pasada, Razor se usa el marcado siguiente:</span><span class="sxs-lookup"><span data-stu-id="a6440-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="a6440-137">El contenido que haya entre paréntesis `@()` se evalúa y se representa en la salida.</span><span class="sxs-lookup"><span data-stu-id="a6440-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="a6440-138">Por lo general, las expresiones implícitas (que explicamos en la sección anterior) no pueden contener espacios.</span><span class="sxs-lookup"><span data-stu-id="a6440-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="a6440-139">En el siguiente código, una semana no se resta de la hora actual:</span><span class="sxs-lookup"><span data-stu-id="a6440-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="a6440-140">El código representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="a6440-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="a6440-141">Se pueden usar expresiones explícitas para concatenar texto con un resultado de la expresión:</span><span class="sxs-lookup"><span data-stu-id="a6440-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="a6440-142">Sin la expresión explícita, `<p>Age@joe.Age</p>` se trataría como una dirección de correo electrónico y se mostraría como `<p>Age@joe.Age</p>`.</span><span class="sxs-lookup"><span data-stu-id="a6440-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="a6440-143">Pero, si se escribe como una expresión explícita, se representa `<p>Age33</p>`.</span><span class="sxs-lookup"><span data-stu-id="a6440-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="a6440-144">Se pueden usar expresiones explícitas para representar la salida de métodos genéricos en los archivos *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a6440-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="a6440-145">En el siguiente marcado se muestra cómo corregir el error mostrado anteriormente provocado por el uso de corchetes en un código C# genérico.</span><span class="sxs-lookup"><span data-stu-id="a6440-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="a6440-146">El código se escribe como una expresión explícita:</span><span class="sxs-lookup"><span data-stu-id="a6440-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="a6440-147">Codificación de expresiones</span><span class="sxs-lookup"><span data-stu-id="a6440-147">Expression encoding</span></span>

<span data-ttu-id="a6440-148">Las expresiones de C# que se evalúan como una cadena están codificadas en HTML.</span><span class="sxs-lookup"><span data-stu-id="a6440-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="a6440-149">Las expresiones de C# que se evalúan como `IHtmlContent` se representan directamente a través de `IHtmlContent.WriteTo`.</span><span class="sxs-lookup"><span data-stu-id="a6440-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="a6440-150">Las expresiones de C# que no se evalúan como `IHtmlContent` se convierten en una cadena por medio de `ToString` y se codifican antes de que se representen.</span><span class="sxs-lookup"><span data-stu-id="a6440-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="a6440-151">El código representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="a6440-151">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="a6440-152">El HTML se muestra en el explorador como:</span><span class="sxs-lookup"><span data-stu-id="a6440-152">The HTML is shown in the browser as:</span></span>

```html
<span>Hello World</span>
```

<span data-ttu-id="a6440-153">La salida de `HtmlHelper.Raw` no está codificada, pero se representa como marcado HTML.</span><span class="sxs-lookup"><span data-stu-id="a6440-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="a6440-154">Usar `HtmlHelper.Raw` en una entrada de usuario no saneada constituye un riesgo de seguridad.</span><span class="sxs-lookup"><span data-stu-id="a6440-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="a6440-155">Dicha entrada podría contener código JavaScript malintencionado u otras vulnerabilidades de seguridad.</span><span class="sxs-lookup"><span data-stu-id="a6440-155">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="a6440-156">Sanear una entrada de usuario es complicado.</span><span class="sxs-lookup"><span data-stu-id="a6440-156">Sanitizing user input is difficult.</span></span> <span data-ttu-id="a6440-157">Evite usar `HtmlHelper.Raw` con entradas de usuario.</span><span class="sxs-lookup"><span data-stu-id="a6440-157">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="a6440-158">El código representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="a6440-158">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="no-locrazor-code-blocks"></a><span data-ttu-id="a6440-159">Razorbloques de código</span><span class="sxs-lookup"><span data-stu-id="a6440-159">Razor code blocks</span></span>

<span data-ttu-id="a6440-160">Razorlos bloques de código comienzan con `@` y se incluyen en `{}` .</span><span class="sxs-lookup"><span data-stu-id="a6440-160">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="a6440-161">A diferencia de las expresiones, el código de C# dentro de los bloques de código no se representa.</span><span class="sxs-lookup"><span data-stu-id="a6440-161">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="a6440-162">Las expresiones y los bloques de código de una vista comparten el mismo ámbito y se definen en orden:</span><span class="sxs-lookup"><span data-stu-id="a6440-162">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

<span data-ttu-id="a6440-163">El código representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="a6440-163">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a6440-164">En los bloques de código, declare las [funciones locales](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) con marcado para utilizarlas como métodos en la creación de plantillas:</span><span class="sxs-lookup"><span data-stu-id="a6440-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

```cshtml
@{
    void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }

    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}
```

<span data-ttu-id="a6440-165">El código representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="a6440-165">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="a6440-166">Transiciones implícitas</span><span class="sxs-lookup"><span data-stu-id="a6440-166">Implicit transitions</span></span>

<span data-ttu-id="a6440-167">El lenguaje predeterminado en un bloque de código es C#, pero la Razor página puede volver a pasar a HTML:</span><span class="sxs-lookup"><span data-stu-id="a6440-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="a6440-168">Transición delimitada explícita</span><span class="sxs-lookup"><span data-stu-id="a6440-168">Explicit delimited transition</span></span>

<span data-ttu-id="a6440-169">Para definir una subsección de un bloque de código que debe representar HTML, rodee los caracteres para su representación con la Razor `<text>` etiqueta:</span><span class="sxs-lookup"><span data-stu-id="a6440-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="a6440-170">Emplee este método para representar HTML que no esté insertado entre etiquetas HTML.</span><span class="sxs-lookup"><span data-stu-id="a6440-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="a6440-171">Sin un código HTML o una Razor etiqueta, Razor se produce un error de tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="a6440-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="a6440-172">La etiqueta `<text>` es útil para controlar el espacio en blanco al representar el contenido:</span><span class="sxs-lookup"><span data-stu-id="a6440-172">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="a6440-173">Solo se representa el contenido entre etiquetas `<text>`.</span><span class="sxs-lookup"><span data-stu-id="a6440-173">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="a6440-174">En la salida HTML no hay espacios en blanco antes o después de la etiqueta `<text>`.</span><span class="sxs-lookup"><span data-stu-id="a6440-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="a6440-175">Transición de línea explícita</span><span class="sxs-lookup"><span data-stu-id="a6440-175">Explicit line transition</span></span>

<span data-ttu-id="a6440-176">Para representar el resto de una línea completa como HTML dentro de un bloque de código, use la sintaxis `@:`:</span><span class="sxs-lookup"><span data-stu-id="a6440-176">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="a6440-177">Sin `@:` en el código, Razor se genera un error de tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="a6440-177">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="a6440-178">`@`Los caracteres adicionales de un Razor archivo pueden producir errores del compilador en las instrucciones más adelante en el bloque.</span><span class="sxs-lookup"><span data-stu-id="a6440-178">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="a6440-179">Estos errores de compilador pueden ser difíciles de entender porque el error real se produce antes del error notificado.</span><span class="sxs-lookup"><span data-stu-id="a6440-179">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="a6440-180">Este error es habitual después de combinar varias expresiones implícitas/explícitas en un mismo bloque de código.</span><span class="sxs-lookup"><span data-stu-id="a6440-180">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="a6440-181">Estructuras de control</span><span class="sxs-lookup"><span data-stu-id="a6440-181">Control structures</span></span>

<span data-ttu-id="a6440-182">Las estructuras de control son una extensión de los bloques de código.</span><span class="sxs-lookup"><span data-stu-id="a6440-182">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="a6440-183">Todos los aspectos de los bloques de código (transición a marcado, C# en línea) son válidos también en las siguientes estructuras:</span><span class="sxs-lookup"><span data-stu-id="a6440-183">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="a6440-184">Condicionales`@if, else if, else, and @switch`</span><span class="sxs-lookup"><span data-stu-id="a6440-184">Conditionals `@if, else if, else, and @switch`</span></span>

<span data-ttu-id="a6440-185">`@if` controla cuándo se ejecuta el código:</span><span class="sxs-lookup"><span data-stu-id="a6440-185">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="a6440-186">`else` y `else if` no necesitan el símbolo `@`:</span><span class="sxs-lookup"><span data-stu-id="a6440-186">`else` and `else if` don't require the `@` symbol:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

<span data-ttu-id="a6440-187">En el siguiente marcado se muestra cómo usar una instrucción switch:</span><span class="sxs-lookup"><span data-stu-id="a6440-187">The following markup shows how to use a switch statement:</span></span>

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="a6440-188">Bucle`@for, @foreach, @while, and @do while`</span><span class="sxs-lookup"><span data-stu-id="a6440-188">Looping `@for, @foreach, @while, and @do while`</span></span>

<span data-ttu-id="a6440-189">El HTML con plantilla se puede representar con instrucciones de control en bucle.</span><span class="sxs-lookup"><span data-stu-id="a6440-189">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="a6440-190">Para representar una lista de personas:</span><span class="sxs-lookup"><span data-stu-id="a6440-190">To render a list of people:</span></span>

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

<span data-ttu-id="a6440-191">Se permiten las siguientes instrucciones en bucle:</span><span class="sxs-lookup"><span data-stu-id="a6440-191">The following looping statements are supported:</span></span>

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a><span data-ttu-id="a6440-192">Instrucción `@using` compuesta</span><span class="sxs-lookup"><span data-stu-id="a6440-192">Compound `@using`</span></span>

<span data-ttu-id="a6440-193">En C#, las instrucciones `using` se usan para garantizar que un objeto se elimina.</span><span class="sxs-lookup"><span data-stu-id="a6440-193">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="a6440-194">En Razor , se usa el mismo mecanismo para crear aplicaciones auxiliares HTML que contienen contenido adicional.</span><span class="sxs-lookup"><span data-stu-id="a6440-194">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="a6440-195">En el siguiente código, los asistentes de HTML representan una etiqueta `<form>` con la instrucción `@using`:</span><span class="sxs-lookup"><span data-stu-id="a6440-195">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### `@try, catch, finally`

<span data-ttu-id="a6440-196">El control de excepciones es similar a C#:</span><span class="sxs-lookup"><span data-stu-id="a6440-196">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

<span data-ttu-id="a6440-197">Razortiene la capacidad de proteger las secciones críticas con instrucciones Lock:</span><span class="sxs-lookup"><span data-stu-id="a6440-197">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="a6440-198">Comentarios</span><span class="sxs-lookup"><span data-stu-id="a6440-198">Comments</span></span>

<span data-ttu-id="a6440-199">Razoradmite comentarios de C# y HTML:</span><span class="sxs-lookup"><span data-stu-id="a6440-199">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="a6440-200">El código representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="a6440-200">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="a6440-201">Razorel servidor quita los comentarios antes de que se represente la Página Web.</span><span class="sxs-lookup"><span data-stu-id="a6440-201">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="a6440-202">Razorutiliza `@*  *@` para delimitar los comentarios.</span><span class="sxs-lookup"><span data-stu-id="a6440-202">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="a6440-203">El siguiente código está comentado, de modo que el servidor no representa ningún marcado:</span><span class="sxs-lookup"><span data-stu-id="a6440-203">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="a6440-204">Directivas</span><span class="sxs-lookup"><span data-stu-id="a6440-204">Directives</span></span>

<span data-ttu-id="a6440-205">Razorlas directivas se representan mediante expresiones implícitas con palabras clave reservadas después del `@` símbolo.</span><span class="sxs-lookup"><span data-stu-id="a6440-205">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="a6440-206">Normalmente, una directiva cambia la forma en que una vista se analiza, o bien habilita una funcionalidad diferente.</span><span class="sxs-lookup"><span data-stu-id="a6440-206">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="a6440-207">Entender cómo Razor genera código para una vista facilita la comprensión del funcionamiento de las directivas.</span><span class="sxs-lookup"><span data-stu-id="a6440-207">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="a6440-208">El código genera una clase similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="a6440-208">The code generates a class similar to the following:</span></span>

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

<span data-ttu-id="a6440-209">Más adelante en este artículo, la sección [inspeccionar la Razor clase de C# generada para una vista](#inspect-the-razor-c-class-generated-for-a-view) explica cómo ver esta clase generada.</span><span class="sxs-lookup"><span data-stu-id="a6440-209">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### `@attribute`

<span data-ttu-id="a6440-210">La directiva `@attribute` agrega el atributo especificado a la clase de la página o vista generada.</span><span class="sxs-lookup"><span data-stu-id="a6440-210">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="a6440-211">En el ejemplo siguiente se agrega el atributo `[Authorize]`:</span><span class="sxs-lookup"><span data-stu-id="a6440-211">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

<span data-ttu-id="a6440-212">*Este escenario solo se aplica a Razor los componentes de (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="a6440-212">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="a6440-213">El `@code` bloque permite a un [ Razor componente](xref:blazor/components/index) agregar miembros de C# (campos, propiedades y métodos) a un componente:</span><span class="sxs-lookup"><span data-stu-id="a6440-213">The `@code` block enables a [Razor component](xref:blazor/components/index) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="a6440-214">En el caso de Razor los componentes, `@code` es un alias de [`@functions`](#functions) y se recomienda en exceso `@functions` .</span><span class="sxs-lookup"><span data-stu-id="a6440-214">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="a6440-215">Se permite emplear más de un bloque `@code`.</span><span class="sxs-lookup"><span data-stu-id="a6440-215">More than one `@code` block is permissible.</span></span>

::: moniker-end

### `@functions`

<span data-ttu-id="a6440-216">La directiva `@functions` permite agregar miembros de C# (campos, propiedades y métodos) a la clase generada:</span><span class="sxs-lookup"><span data-stu-id="a6440-216">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a6440-217">En [ Razor componentes](xref:blazor/components/index), use `@code` `@functions` para agregar miembros de C#.</span><span class="sxs-lookup"><span data-stu-id="a6440-217">In [Razor components](xref:blazor/components/index), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="a6440-218">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a6440-218">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="a6440-219">El código genera el siguiente marcado HTML:</span><span class="sxs-lookup"><span data-stu-id="a6440-219">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="a6440-220">El código siguiente es la clase de C# generada Razor :</span><span class="sxs-lookup"><span data-stu-id="a6440-220">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a6440-221">Los métodos `@functions` se pueden usar para la creación de plantillas si están marcados:</span><span class="sxs-lookup"><span data-stu-id="a6440-221">`@functions` methods serve as templating methods when they have markup:</span></span>

```cshtml
@{
    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}

@functions {
    private void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }
}
```

<span data-ttu-id="a6440-222">El código representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="a6440-222">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

<span data-ttu-id="a6440-223">La directiva `@implements` implementa una interfaz para la clase generada.</span><span class="sxs-lookup"><span data-stu-id="a6440-223">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="a6440-224">En el ejemplo siguiente se implementa <xref:System.IDisposable?displayProperty=fullName> para que se pueda llamar al método <xref:System.IDisposable.Dispose*>:</span><span class="sxs-lookup"><span data-stu-id="a6440-224">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

```cshtml
@implements IDisposable

<h1>Example</h1>

@functions {
    private bool _isDisposed;

    ...

    public void Dispose() => _isDisposed = true;
}
```

::: moniker-end

### `@inherits`

<span data-ttu-id="a6440-225">La directiva `@inherits` proporciona control total sobre la clase que la vista hereda:</span><span class="sxs-lookup"><span data-stu-id="a6440-225">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="a6440-226">El código siguiente es un Razor tipo de página personalizado:</span><span class="sxs-lookup"><span data-stu-id="a6440-226">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="a6440-227">`CustomText` se muestra en una vista:</span><span class="sxs-lookup"><span data-stu-id="a6440-227">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="a6440-228">El código representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="a6440-228">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="a6440-229">`@model` y `@inherits` se pueden usar en la misma vista.</span><span class="sxs-lookup"><span data-stu-id="a6440-229">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="a6440-230">`@inherits` puede estar en un archivo *_ViewImports.cshtml* que la vista importa:</span><span class="sxs-lookup"><span data-stu-id="a6440-230">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="a6440-231">El siguiente código es un ejemplo de una vista fuertemente tipada:</span><span class="sxs-lookup"><span data-stu-id="a6440-231">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="a6440-232">Si "rick@contoso.com" se pasa en el modelo, la vista genera el siguiente marcado HTML:</span><span class="sxs-lookup"><span data-stu-id="a6440-232">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

<span data-ttu-id="a6440-233">La `@inject` directiva permite Razor que la página Inserte un servicio del [contenedor de servicios](xref:fundamentals/dependency-injection) en una vista.</span><span class="sxs-lookup"><span data-stu-id="a6440-233">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="a6440-234">Para más información, vea [Dependency injection into views](xref:mvc/views/dependency-injection) (Inserción de dependencias en vistas).</span><span class="sxs-lookup"><span data-stu-id="a6440-234">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### `@layout`

<span data-ttu-id="a6440-235">*Este escenario solo se aplica a Razor los componentes de (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="a6440-235">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="a6440-236">La `@layout` Directiva especifica un diseño para un Razor componente.</span><span class="sxs-lookup"><span data-stu-id="a6440-236">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="a6440-237">Los componentes de diseño se usan para evitar incoherencias y contenido duplicado en el código.</span><span class="sxs-lookup"><span data-stu-id="a6440-237">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="a6440-238">Para obtener más información, vea <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="a6440-238">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### `@model`

<span data-ttu-id="a6440-239">*Este escenario solo se aplica a las vistas y páginas de MVC Razor (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="a6440-239">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="a6440-240">La directiva `@model` especifica el tipo del modelo que se pasa a una vista o página:</span><span class="sxs-lookup"><span data-stu-id="a6440-240">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="a6440-241">En un ASP.NET Core aplicación MVC o Razor pages creada con cuentas de usuario individuales, *views/Account/login. cshtml* contiene la siguiente declaración de modelo:</span><span class="sxs-lookup"><span data-stu-id="a6440-241">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="a6440-242">La clase generada se hereda de `RazorPage<dynamic>`:</span><span class="sxs-lookup"><span data-stu-id="a6440-242">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="a6440-243">Razorexpone una `Model` propiedad para obtener acceso al modelo que se pasa a la vista:</span><span class="sxs-lookup"><span data-stu-id="a6440-243">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="a6440-244">La directiva `@model` especifica el tipo de la propiedad `Model`.</span><span class="sxs-lookup"><span data-stu-id="a6440-244">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="a6440-245">La directiva especifica el elemento `T` en `RazorPage<T>` de la clase generada de la que se deriva la vista.</span><span class="sxs-lookup"><span data-stu-id="a6440-245">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="a6440-246">Si la directiva `@model` no se especifica, la propiedad `Model` es de tipo `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="a6440-246">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="a6440-247">Para obtener más información, vea [modelos fuertemente tipados y la @model palabra clave](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="a6440-247">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### `@namespace`

<span data-ttu-id="a6440-248">Directiva `@namespace`:</span><span class="sxs-lookup"><span data-stu-id="a6440-248">The `@namespace` directive:</span></span>

* <span data-ttu-id="a6440-249">Establece el espacio de nombres de la clase de la Razor página, vista de MVC o componente generados Razor .</span><span class="sxs-lookup"><span data-stu-id="a6440-249">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="a6440-250">Establece los espacios de nombres derivados de la raíz de las clases de páginas, vistas o componentes del archivo de importaciones más cercano en el árbol de directorios, *_ViewImports. cshtml* (vistas o páginas) o *_Imports. Razor* ( Razor componentes).</span><span class="sxs-lookup"><span data-stu-id="a6440-250">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="a6440-251">En el Razor ejemplo de páginas que se muestra en la tabla siguiente:</span><span class="sxs-lookup"><span data-stu-id="a6440-251">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="a6440-252">Cada página importa *Pages/_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a6440-252">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="a6440-253">*Pages/_ViewImports.cshtml* contiene `@namespace Hello.World`.</span><span class="sxs-lookup"><span data-stu-id="a6440-253">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="a6440-254">Cada página tiene `Hello.World` como raíz de su espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="a6440-254">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="a6440-255">Página</span><span class="sxs-lookup"><span data-stu-id="a6440-255">Page</span></span>                                        | <span data-ttu-id="a6440-256">Espacio de nombres</span><span class="sxs-lookup"><span data-stu-id="a6440-256">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="a6440-257">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="a6440-257">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="a6440-258">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="a6440-258">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="a6440-259">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="a6440-259">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="a6440-260">Las relaciones anteriores se aplican a los archivos de importación usados con las vistas y Razor los componentes de MVC.</span><span class="sxs-lookup"><span data-stu-id="a6440-260">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="a6440-261">Cuando varios archivos de importación tienen una directiva `@namespace`, se usa el archivo más cercano a la página, vista o componente en el árbol de directorios para establecer el espacio de nombres raíz.</span><span class="sxs-lookup"><span data-stu-id="a6440-261">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="a6440-262">Si la carpeta *EvenMorePages* del ejemplo anterior tiene un archivo de importaciones con `@namespace Another.Planet` (o el archivo *Pages/MorePages/EvenMorePages/Page.cshtml* contiene `@namespace Another.Planet`), el resultado es el que se muestra en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="a6440-262">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="a6440-263">Página</span><span class="sxs-lookup"><span data-stu-id="a6440-263">Page</span></span>                                        | <span data-ttu-id="a6440-264">Espacio de nombres</span><span class="sxs-lookup"><span data-stu-id="a6440-264">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="a6440-265">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="a6440-265">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="a6440-266">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="a6440-266">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="a6440-267">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="a6440-267">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a6440-268">La directiva `@page` tiene efectos diferentes en función del tipo de archivo en el que aparece.</span><span class="sxs-lookup"><span data-stu-id="a6440-268">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="a6440-269">Directiva:</span><span class="sxs-lookup"><span data-stu-id="a6440-269">The directive:</span></span>

* <span data-ttu-id="a6440-270">En en un archivo *. cshtml* indica que el archivo es una Razor página.</span><span class="sxs-lookup"><span data-stu-id="a6440-270">In in a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="a6440-271">Para más información, consulte [Rutas personalizadas](xref:razor-pages/index#custom-routes) y <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="a6440-271">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="a6440-272">Especifica que un Razor componente debe controlar las solicitudes directamente.</span><span class="sxs-lookup"><span data-stu-id="a6440-272">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="a6440-273">Para obtener más información, vea <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="a6440-273">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a6440-274">La `@page` Directiva en la primera línea de un archivo *. cshtml* indica que el archivo es una Razor página.</span><span class="sxs-lookup"><span data-stu-id="a6440-274">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="a6440-275">Para obtener más información, vea <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="a6440-275">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### `@section`

<span data-ttu-id="a6440-276">*Este escenario solo se aplica a las vistas y páginas de MVC Razor (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="a6440-276">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="a6440-277">La `@section` Directiva se usa junto con los [diseños MVC y Razor pages](xref:mvc/views/layout) para permitir que las vistas o páginas representen el contenido en diferentes partes de la página HTML.</span><span class="sxs-lookup"><span data-stu-id="a6440-277">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="a6440-278">Para obtener más información, vea <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="a6440-278">For more information, see <xref:mvc/views/layout>.</span></span>

### `@using`

<span data-ttu-id="a6440-279">La directiva `@using` agrega la directiva `using` de C# a la vista generada:</span><span class="sxs-lookup"><span data-stu-id="a6440-279">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a6440-280">En [ Razor Components](xref:blazor/components/index), `@using` también controla qué componentes están en el ámbito.</span><span class="sxs-lookup"><span data-stu-id="a6440-280">In [Razor components](xref:blazor/components/index), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="a6440-281">Atributos de la directiva</span><span class="sxs-lookup"><span data-stu-id="a6440-281">Directive attributes</span></span>

<span data-ttu-id="a6440-282">Razorlos atributos de Directiva se representan mediante expresiones implícitas con palabras clave reservadas después del `@` símbolo.</span><span class="sxs-lookup"><span data-stu-id="a6440-282">Razor directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="a6440-283">Un atributo de directiva suele cambiar la forma en que se analiza un elemento o habilita una funcionalidad diferente.</span><span class="sxs-lookup"><span data-stu-id="a6440-283">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### `@attributes`

<span data-ttu-id="a6440-284">*Este escenario solo se aplica a Razor los componentes de (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="a6440-284">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="a6440-285">`@attributes` permite que un componente represente atributos no declarados.</span><span class="sxs-lookup"><span data-stu-id="a6440-285">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="a6440-286">Para obtener más información, vea <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="a6440-286">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

### `@bind`

<span data-ttu-id="a6440-287">*Este escenario solo se aplica a Razor los componentes de (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="a6440-287">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="a6440-288">El enlace de datos en los componentes se logra mediante el atributo `@bind`.</span><span class="sxs-lookup"><span data-stu-id="a6440-288">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="a6440-289">Para obtener más información, vea <xref:blazor/components/data-binding>.</span><span class="sxs-lookup"><span data-stu-id="a6440-289">For more information, see <xref:blazor/components/data-binding>.</span></span>

### `@on{EVENT}`

<span data-ttu-id="a6440-290">*Este escenario solo se aplica a Razor los componentes de (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="a6440-290">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="a6440-291">Razorproporciona características de control de eventos para los componentes de.</span><span class="sxs-lookup"><span data-stu-id="a6440-291">Razor provides event handling features for components.</span></span> <span data-ttu-id="a6440-292">Para obtener más información, vea <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="a6440-292">For more information, see <xref:blazor/components/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

<span data-ttu-id="a6440-293">*Este escenario solo se aplica a Razor los componentes de (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="a6440-293">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="a6440-294">Impide la acción predeterminada para el evento.</span><span class="sxs-lookup"><span data-stu-id="a6440-294">Prevents the default action for the event.</span></span>

### `@on{EVENT}:stopPropagation`

<span data-ttu-id="a6440-295">*Este escenario solo se aplica a Razor los componentes de (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="a6440-295">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="a6440-296">Detiene la propagación de eventos para el evento.</span><span class="sxs-lookup"><span data-stu-id="a6440-296">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

<span data-ttu-id="a6440-297">*Este escenario solo se aplica a Razor los componentes de (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="a6440-297">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="a6440-298">El atributo de directiva `@key` hace que el algoritmo de comparación de componentes garantice la preservación de elementos o componentes en función del valor de la clave.</span><span class="sxs-lookup"><span data-stu-id="a6440-298">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="a6440-299">Para obtener más información, vea <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="a6440-299">For more information, see <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### `@ref`

<span data-ttu-id="a6440-300">*Este escenario solo se aplica a Razor los componentes de (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="a6440-300">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="a6440-301">Las referencias de componentes (`@ref`) proporcionan una forma de hacer referencia a la instancia de un componente para poder emitir comandos a dicha instancia.</span><span class="sxs-lookup"><span data-stu-id="a6440-301">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="a6440-302">Para obtener más información, vea <xref:blazor/components/index#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="a6440-302">For more information, see <xref:blazor/components/index#capture-references-to-components>.</span></span>

### `@typeparam`

<span data-ttu-id="a6440-303">*Este escenario solo se aplica a Razor los componentes de (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="a6440-303">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="a6440-304">La directiva `@typeparam` declara un parámetro de tipo genérico para la clase de componente generada.</span><span class="sxs-lookup"><span data-stu-id="a6440-304">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="a6440-305">Para obtener más información, vea <xref:blazor/components/templated-components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="a6440-305">For more information, see <xref:blazor/components/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-no-locrazor-delegates"></a><span data-ttu-id="a6440-306">Delegados con plantilla Razor</span><span class="sxs-lookup"><span data-stu-id="a6440-306">Templated Razor delegates</span></span>

<span data-ttu-id="a6440-307">Razorlas plantillas permiten definir un fragmento de la interfaz de usuario con el siguiente formato:</span><span class="sxs-lookup"><span data-stu-id="a6440-307">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="a6440-308">En el ejemplo siguiente se muestra cómo especificar un Razor delegado con plantilla como <xref:System.Func%602> .</span><span class="sxs-lookup"><span data-stu-id="a6440-308">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="a6440-309">El [tipo dinámico](/dotnet/csharp/programming-guide/types/using-type-dynamic) se especifica para el parámetro del método encapsulado por el delegado.</span><span class="sxs-lookup"><span data-stu-id="a6440-309">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="a6440-310">Se especifica un [tipo de objeto](/dotnet/csharp/language-reference/keywords/object) como el valor devuelto del delegado.</span><span class="sxs-lookup"><span data-stu-id="a6440-310">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="a6440-311">La plantilla se usa con un elemento <xref:System.Collections.Generic.List%601> de `Pet` que tiene una propiedad `Name`.</span><span class="sxs-lookup"><span data-stu-id="a6440-311">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }
}
```

```cshtml
@{
    Func<dynamic, object> petTemplate = @<p>You have a pet named <strong>@item.Name</strong>.</p>;

    var pets = new List<Pet>
    {
        new Pet { Name = "Rin Tin Tin" },
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "K-9" }
    };
}
```

<span data-ttu-id="a6440-312">La plantilla se representa con el elemento `pets` proporcionado por una instrucción `foreach`:</span><span class="sxs-lookup"><span data-stu-id="a6440-312">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="a6440-313">Salida representada:</span><span class="sxs-lookup"><span data-stu-id="a6440-313">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="a6440-314">También puede proporcionar una plantilla insertada Razor como argumento a un método.</span><span class="sxs-lookup"><span data-stu-id="a6440-314">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="a6440-315">En el ejemplo siguiente, el `Repeat` método recibe una Razor plantilla.</span><span class="sxs-lookup"><span data-stu-id="a6440-315">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="a6440-316">El método usa la plantilla para generar contenido HTML con repeticiones de elementos proporcionados a partir de una lista:</span><span class="sxs-lookup"><span data-stu-id="a6440-316">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

```cshtml
@using Microsoft.AspNetCore.Html

@functions {
    public static IHtmlContent Repeat(IEnumerable<dynamic> items, int times,
        Func<dynamic, IHtmlContent> template)
    {
        var html = new HtmlContentBuilder();

        foreach (var item in items)
        {
            for (var i = 0; i < times; i++)
            {
                html.AppendHtml(template(item));
            }
        }

        return html;
    }
}
```

<span data-ttu-id="a6440-317">Con la lista de mascotas del ejemplo anterior, se llama al método `Repeat` con:</span><span class="sxs-lookup"><span data-stu-id="a6440-317">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="a6440-318"><xref:System.Collections.Generic.List%601> de `Pet`.</span><span class="sxs-lookup"><span data-stu-id="a6440-318"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="a6440-319">Número de veces que se repite cada mascota.</span><span class="sxs-lookup"><span data-stu-id="a6440-319">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="a6440-320">Plantilla insertada que se va a usar para los elementos de una lista sin ordenar.</span><span class="sxs-lookup"><span data-stu-id="a6440-320">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="a6440-321">Salida representada:</span><span class="sxs-lookup"><span data-stu-id="a6440-321">Rendered output:</span></span>

```html
<ul>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>K-9</li>
    <li>K-9</li>
    <li>K-9</li>
</ul>
```

## <a name="tag-helpers"></a><span data-ttu-id="a6440-322">Asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="a6440-322">Tag Helpers</span></span>

<span data-ttu-id="a6440-323">*Este escenario solo se aplica a las vistas y páginas de MVC Razor (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="a6440-323">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="a6440-324">Hay tres directivas que pertenecen a los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="a6440-324">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="a6440-325">Directiva</span><span class="sxs-lookup"><span data-stu-id="a6440-325">Directive</span></span> | <span data-ttu-id="a6440-326">Función</span><span class="sxs-lookup"><span data-stu-id="a6440-326">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="a6440-327">Pone los asistentes de etiquetas a disposición de una vista.</span><span class="sxs-lookup"><span data-stu-id="a6440-327">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="a6440-328">Quita los asistentes de etiquetas agregadas anteriormente desde una vista.</span><span class="sxs-lookup"><span data-stu-id="a6440-328">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="a6440-329">Especifica una cadena de prefijo de etiqueta para permitir la compatibilidad con el asistente de etiquetas y hacer explícito su uso.</span><span class="sxs-lookup"><span data-stu-id="a6440-329">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="no-locrazor-reserved-keywords"></a><span data-ttu-id="a6440-330">RazorPalabras clave reservadas</span><span class="sxs-lookup"><span data-stu-id="a6440-330">Razor reserved keywords</span></span>

### <a name="no-locrazor-keywords"></a><span data-ttu-id="a6440-331">Razorpalabra</span><span class="sxs-lookup"><span data-stu-id="a6440-331">Razor keywords</span></span>

* <span data-ttu-id="a6440-332">`page`(Requiere ASP.NET Core 2,1 o posterior)</span><span class="sxs-lookup"><span data-stu-id="a6440-332">`page` (Requires ASP.NET Core 2.1 or later)</span></span>
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* <span data-ttu-id="a6440-333">`helper`(Actualmente no es compatible con ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="a6440-333">`helper` (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="a6440-334">Razorlas palabras clave se incluyen en secuencias `@(Razor Keyword)` de escape (por ejemplo, `@(functions)` ).</span><span class="sxs-lookup"><span data-stu-id="a6440-334">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-no-locrazor-keywords"></a><span data-ttu-id="a6440-335">RazorPalabras clave de C#</span><span class="sxs-lookup"><span data-stu-id="a6440-335">C# Razor keywords</span></span>

* `case`
* `do`
* `default`
* `for`
* `foreach`
* `if`
* `else`
* `lock`
* `switch`
* `try`
* `catch`
* `finally`
* `using`
* `while`

<span data-ttu-id="a6440-336">Las Razor palabras clave de C# deben tener un doble escape con `@(@C# Razor Keyword)` (por ejemplo, `@(@case)` ).</span><span class="sxs-lookup"><span data-stu-id="a6440-336">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="a6440-337">El primero `@` convierte el analizador en caracteres de escape Razor .</span><span class="sxs-lookup"><span data-stu-id="a6440-337">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="a6440-338">y el segundo `@`, en el analizador de C#.</span><span class="sxs-lookup"><span data-stu-id="a6440-338">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-no-locrazor"></a><span data-ttu-id="a6440-339">Palabras clave reservadas no utilizadas porRazor</span><span class="sxs-lookup"><span data-stu-id="a6440-339">Reserved keywords not used by Razor</span></span>

* `class`

## <a name="inspect-the-no-locrazor-c-class-generated-for-a-view"></a><span data-ttu-id="a6440-340">Inspeccionar la Razor clase de C# generada para una vista</span><span class="sxs-lookup"><span data-stu-id="a6440-340">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="a6440-341">Con SDK de .NET Core 2,1 o posterior, el [ Razor SDK](xref:razor-pages/sdk) controla la compilación de Razor archivos.</span><span class="sxs-lookup"><span data-stu-id="a6440-341">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="a6440-342">Al compilar un proyecto, el Razor SDK genera un *build_configuration de <y de>/<Razor target_framework_moniker>/* directorio en la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="a6440-342">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="a6440-343">La estructura de directorios dentro del *Razor* directorio refleja la estructura de directorios del proyecto.</span><span class="sxs-lookup"><span data-stu-id="a6440-343">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="a6440-344">Considere la siguiente estructura de directorios en un proyecto de páginas de ASP.NET Core 2,1 que tiene Razor como destino .net Core 2,1:</span><span class="sxs-lookup"><span data-stu-id="a6440-344">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

```
 Areas/
   Admin/
     Pages/
       Index.cshtml
       Index.cshtml.cs
 Pages/
   Shared/
     _Layout.cshtml
   _ViewImports.cshtml
   _ViewStart.cshtml
   Index.cshtml
   Index.cshtml.cs
  ```

<span data-ttu-id="a6440-345">Al compilar el proyecto en la configuración *Depurar* se crea el directorio *obj* siguiente:</span><span class="sxs-lookup"><span data-stu-id="a6440-345">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

```
 obj/
   Debug/
     netcoreapp2.1/
       Razor/
         Areas/
           Admin/
             Pages/
               Index.g.cshtml.cs
         Pages/
           Shared/
             _Layout.g.cshtml.cs
           _ViewImports.g.cshtml.cs
           _ViewStart.g.cshtml.cs
           Index.g.cshtml.cs
```

<span data-ttu-id="a6440-346">Para ver la clase generada para *pages/index. cshtml*, Abra *obj/Debug/netcoreapp 2.1/ Razor /pages/index.g.cshtml.CS*.</span><span class="sxs-lookup"><span data-stu-id="a6440-346">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="a6440-347">Agregue la siguiente clase al proyecto de ASP.NET Core MVC:</span><span class="sxs-lookup"><span data-stu-id="a6440-347">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="a6440-348">En `Startup.ConfigureServices`, invalide el elemento `RazorTemplateEngine` agregado por MVC con la clase `CustomTemplateEngine`:</span><span class="sxs-lookup"><span data-stu-id="a6440-348">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="a6440-349">Establezca un punto de interrupción en la instrucción `return csharpDocument;` de `CustomTemplateEngine`.</span><span class="sxs-lookup"><span data-stu-id="a6440-349">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="a6440-350">Cuando la ejecución del programa se detenga en el punto de interrupción, vea el valor de `generatedCode`.</span><span class="sxs-lookup"><span data-stu-id="a6440-350">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Vista del visualizador de texto de generatedCode](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="a6440-352">Búsquedas de vistas y distinción entre mayúsculas y minúsculas</span><span class="sxs-lookup"><span data-stu-id="a6440-352">View lookups and case sensitivity</span></span>

<span data-ttu-id="a6440-353">El Razor motor de vista realiza búsquedas con distinción de mayúsculas y minúsculas para las vistas.</span><span class="sxs-lookup"><span data-stu-id="a6440-353">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="a6440-354">Pero la búsqueda real viene determinada por el sistema de archivos subyacente:</span><span class="sxs-lookup"><span data-stu-id="a6440-354">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="a6440-355">Origen basado en archivos:</span><span class="sxs-lookup"><span data-stu-id="a6440-355">File based source:</span></span>
  * <span data-ttu-id="a6440-356">En los sistemas operativos con sistemas de archivos que no distinguen entre mayúsculas y minúsculas (por ejemplo, Windows), las búsquedas de proveedor de archivos físicos no distinguirán mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="a6440-356">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="a6440-357">Por ejemplo, `return View("Test")` arrojará como resultados */Views/Home/Test.cshtml*, */Views/home/test.cshtml* y cualquier otra variante de mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="a6440-357">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="a6440-358">En los sistemas de archivos en los que sí se distingue entre mayúsculas y minúsculas (por ejemplo, Linux, OSX y al usar `EmbeddedFileProvider`), las búsquedas distinguirán mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="a6440-358">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="a6440-359">Por ejemplo, `return View("Test")` mostrará el resultado */Views/Home/Test.cshtml* única y exclusivamente.</span><span class="sxs-lookup"><span data-stu-id="a6440-359">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="a6440-360">Vistas precompiladas: En ASP.NET Core 2.0 y versiones posteriores, las búsquedas de vistas precompiladas no distinguen mayúsculas de minúsculas en todos los sistemas operativos.</span><span class="sxs-lookup"><span data-stu-id="a6440-360">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="a6440-361">Este comportamiento es idéntico al comportamiento del proveedor de archivos físicos en Windows.</span><span class="sxs-lookup"><span data-stu-id="a6440-361">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="a6440-362">Si dos vistas precompiladas difieren solo por sus mayúsculas o minúsculas, el resultado de la búsqueda será no determinante.</span><span class="sxs-lookup"><span data-stu-id="a6440-362">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="a6440-363">Por tanto, se anima a todos los desarrolladores a intentar que las mayúsculas y minúsculas de los nombres de archivo y de directorio sean las mismas que las mayúsculas y minúsculas de:</span><span class="sxs-lookup"><span data-stu-id="a6440-363">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="a6440-364">Nombres de acciones, controladores y áreas.</span><span class="sxs-lookup"><span data-stu-id="a6440-364">Area, controller, and action names.</span></span>
* <span data-ttu-id="a6440-365">RazorPáginas.</span><span class="sxs-lookup"><span data-stu-id="a6440-365">Razor Pages.</span></span>

<span data-ttu-id="a6440-366">La coincidencia de mayúsculas y minúsculas garantiza que las implementaciones van a encontrar sus vistas, independientemente de cuál sea el sistema de archivos subyacente.</span><span class="sxs-lookup"><span data-stu-id="a6440-366">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a6440-367">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="a6440-367">Additional resources</span></span>

<span data-ttu-id="a6440-368">[Introducción a la programación web de ASP.net mediante el Razor La sintaxis](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) proporciona muchos ejemplos de programación con Razor Sintaxis.</span><span class="sxs-lookup"><span data-stu-id="a6440-368">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
