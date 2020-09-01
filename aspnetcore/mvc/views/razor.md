---
title: referencia de la sintaxis de Razor para ASP.NET Core
author: rick-anderson
description: Obtenga información sobre la Razor Sintaxis de marcado para insertar código basado en servidor en páginas Web.
ms.author: riande
ms.date: 02/12/2020
no-loc:
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
uid: mvc/views/razor
ms.openlocfilehash: 9c2bbd2d463af8a2ea7db716d01bf1436338ea77
ms.sourcegitcommit: cd861463faf44956855e3c4b3669483bbc4a7463
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2020
ms.locfileid: "89101366"
---
# <a name="no-locrazor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="9dffd-103">Razor referencia de sintaxis para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9dffd-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="9dffd-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)y [dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="9dffd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="9dffd-105">Razor es una sintaxis de marcado para insertar código basado en servidor en páginas Web.</span><span class="sxs-lookup"><span data-stu-id="9dffd-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="9dffd-106">La Razor Sintaxis consta de Razor marcado, C# y HTML.</span><span class="sxs-lookup"><span data-stu-id="9dffd-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="9dffd-107">Los archivos que contienen Razor generalmente tienen una extensión de archivo *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="9dffd-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="9dffd-108">Razortambién se encuentra en archivos de [ Razor componentes](xref:blazor/components/index) (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="9dffd-108">Razor is also found in [Razor components](xref:blazor/components/index) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="9dffd-109">Representación de HTML</span><span class="sxs-lookup"><span data-stu-id="9dffd-109">Rendering HTML</span></span>

<span data-ttu-id="9dffd-110">El Razor idioma predeterminado es HTML.</span><span class="sxs-lookup"><span data-stu-id="9dffd-110">The default Razor language is HTML.</span></span> <span data-ttu-id="9dffd-111">La representación de HTML a partir del Razor marcado no es diferente de representar HTML desde un archivo HTML.</span><span class="sxs-lookup"><span data-stu-id="9dffd-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="9dffd-112">*.cshtml* Razor El servidor no ha cambiado el formato HTML en los archivos. cshtml.</span><span class="sxs-lookup"><span data-stu-id="9dffd-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="no-locrazor-syntax"></a><span data-ttu-id="9dffd-113">Sintaxis de Razor</span><span class="sxs-lookup"><span data-stu-id="9dffd-113">Razor syntax</span></span>

<span data-ttu-id="9dffd-114">Razor admite C# y usa el `@` símbolo para pasar de HTML a C#.</span><span class="sxs-lookup"><span data-stu-id="9dffd-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="9dffd-115">Razor evalúa las expresiones de C# y las representa en la salida HTML.</span><span class="sxs-lookup"><span data-stu-id="9dffd-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="9dffd-116">Cuando un `@` símbolo va seguido de una [ Razor palabra clave reservada](#razor-reserved-keywords), realiza la transición a un Razor marcado específico.</span><span class="sxs-lookup"><span data-stu-id="9dffd-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="9dffd-117">en caso contrario, realiza la transición a C# simple.</span><span class="sxs-lookup"><span data-stu-id="9dffd-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="9dffd-118">Para escapar un `@` símbolo en Razor el marcado, use un segundo `@` símbolo:</span><span class="sxs-lookup"><span data-stu-id="9dffd-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="9dffd-119">El código aparecerá en HTML con un solo símbolo `@`:</span><span class="sxs-lookup"><span data-stu-id="9dffd-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="9dffd-120">El contenido y los atributos HTML que tienen direcciones de correo electrónico no tratan el símbolo `@` como un carácter de transición.</span><span class="sxs-lookup"><span data-stu-id="9dffd-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="9dffd-121">Las direcciones de correo electrónico en el ejemplo siguiente no se tocan mediante el Razor análisis:</span><span class="sxs-lookup"><span data-stu-id="9dffd-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-no-locrazor-expressions"></a><span data-ttu-id="9dffd-122">RazorExpresiones IMPLÍCITAS</span><span class="sxs-lookup"><span data-stu-id="9dffd-122">Implicit Razor expressions</span></span>

<span data-ttu-id="9dffd-123">Las Razor expresiones implícitas comienzan con `@` seguido de código C#:</span><span class="sxs-lookup"><span data-stu-id="9dffd-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="9dffd-124">Con la excepción de la palabra clave de C# `await`, las expresiones implícitas no deben contener espacios.</span><span class="sxs-lookup"><span data-stu-id="9dffd-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="9dffd-125">Si la instrucción de C# tiene un final claro, se pueden entremezclar espacios:</span><span class="sxs-lookup"><span data-stu-id="9dffd-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="9dffd-126">Las expresiones implícitas **no pueden** contener tipos genéricos de C#, ya que los caracteres dentro de los corchetes (`<>`) se interpretan como una etiqueta HTML.</span><span class="sxs-lookup"><span data-stu-id="9dffd-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="9dffd-127">El siguiente código **no** es válido:</span><span class="sxs-lookup"><span data-stu-id="9dffd-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="9dffd-128">El código anterior genera un error del compilador similar a uno de los siguientes:</span><span class="sxs-lookup"><span data-stu-id="9dffd-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="9dffd-129">El elemento "int" no estaba cerrado.</span><span class="sxs-lookup"><span data-stu-id="9dffd-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="9dffd-130">Todos los elementos deben ser de autocierre o tener una etiqueta de fin coincidente.</span><span class="sxs-lookup"><span data-stu-id="9dffd-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="9dffd-131">No se puede convertir el grupo de métodos "GenericMethod" en el tipo no delegado "object".</span><span class="sxs-lookup"><span data-stu-id="9dffd-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="9dffd-132">¿Intentó invocar el método?</span><span class="sxs-lookup"><span data-stu-id="9dffd-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="9dffd-133">Las llamadas a métodos genéricos se deben encapsular en una [ Razor expresión explícita](#explicit-razor-expressions) o en un [ Razor bloque de código](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="9dffd-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-no-locrazor-expressions"></a><span data-ttu-id="9dffd-134">Expresiones explícitas Razor</span><span class="sxs-lookup"><span data-stu-id="9dffd-134">Explicit Razor expressions</span></span>

<span data-ttu-id="9dffd-135">Las Razor expresiones explícitas están compuestas por un `@` símbolo con paréntesis equilibrados.</span><span class="sxs-lookup"><span data-stu-id="9dffd-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="9dffd-136">Para representar la hora de la semana pasada, Razor se usa el marcado siguiente:</span><span class="sxs-lookup"><span data-stu-id="9dffd-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="9dffd-137">El contenido que haya entre paréntesis `@()` se evalúa y se representa en la salida.</span><span class="sxs-lookup"><span data-stu-id="9dffd-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="9dffd-138">Por lo general, las expresiones implícitas (que explicamos en la sección anterior) no pueden contener espacios.</span><span class="sxs-lookup"><span data-stu-id="9dffd-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="9dffd-139">En el siguiente código, una semana no se resta de la hora actual:</span><span class="sxs-lookup"><span data-stu-id="9dffd-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="9dffd-140">El código representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="9dffd-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="9dffd-141">Se pueden usar expresiones explícitas para concatenar texto con un resultado de la expresión:</span><span class="sxs-lookup"><span data-stu-id="9dffd-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="9dffd-142">Sin la expresión explícita, `<p>Age@joe.Age</p>` se trataría como una dirección de correo electrónico y se mostraría como `<p>Age@joe.Age</p>`.</span><span class="sxs-lookup"><span data-stu-id="9dffd-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="9dffd-143">Pero, si se escribe como una expresión explícita, se representa `<p>Age33</p>`.</span><span class="sxs-lookup"><span data-stu-id="9dffd-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="9dffd-144">Se pueden usar expresiones explícitas para representar la salida de métodos genéricos en los archivos *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="9dffd-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="9dffd-145">En el siguiente marcado se muestra cómo corregir el error mostrado anteriormente provocado por el uso de corchetes en un código C# genérico.</span><span class="sxs-lookup"><span data-stu-id="9dffd-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="9dffd-146">El código se escribe como una expresión explícita:</span><span class="sxs-lookup"><span data-stu-id="9dffd-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="9dffd-147">Codificación de expresiones</span><span class="sxs-lookup"><span data-stu-id="9dffd-147">Expression encoding</span></span>

<span data-ttu-id="9dffd-148">Las expresiones de C# que se evalúan como una cadena están codificadas en HTML.</span><span class="sxs-lookup"><span data-stu-id="9dffd-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="9dffd-149">Las expresiones de C# que se evalúan como `IHtmlContent` se representan directamente a través de `IHtmlContent.WriteTo`.</span><span class="sxs-lookup"><span data-stu-id="9dffd-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="9dffd-150">Las expresiones de C# que no se evalúan como `IHtmlContent` se convierten en una cadena por medio de `ToString` y se codifican antes de que se representen.</span><span class="sxs-lookup"><span data-stu-id="9dffd-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="9dffd-151">El código anterior representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="9dffd-151">The preceding code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="9dffd-152">El código HTML se muestra en el explorador como texto sin formato:</span><span class="sxs-lookup"><span data-stu-id="9dffd-152">The HTML is shown in the browser as plain text:</span></span>

<span data-ttu-id="9dffd-153">&lt;intervalo &gt; Hola mundo &lt; /span&gt;</span><span class="sxs-lookup"><span data-stu-id="9dffd-153">&lt;span&gt;Hello World&lt;/span&gt;</span></span>

<span data-ttu-id="9dffd-154">La salida de `HtmlHelper.Raw` no está codificada, pero se representa como marcado HTML.</span><span class="sxs-lookup"><span data-stu-id="9dffd-154">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="9dffd-155">Usar `HtmlHelper.Raw` en una entrada de usuario no saneada constituye un riesgo de seguridad.</span><span class="sxs-lookup"><span data-stu-id="9dffd-155">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="9dffd-156">Dicha entrada podría contener código JavaScript malintencionado u otras vulnerabilidades de seguridad.</span><span class="sxs-lookup"><span data-stu-id="9dffd-156">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="9dffd-157">Sanear una entrada de usuario es complicado.</span><span class="sxs-lookup"><span data-stu-id="9dffd-157">Sanitizing user input is difficult.</span></span> <span data-ttu-id="9dffd-158">Evite usar `HtmlHelper.Raw` con entradas de usuario.</span><span class="sxs-lookup"><span data-stu-id="9dffd-158">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="9dffd-159">El código representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="9dffd-159">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="no-locrazor-code-blocks"></a><span data-ttu-id="9dffd-160">Razor bloques de código</span><span class="sxs-lookup"><span data-stu-id="9dffd-160">Razor code blocks</span></span>

<span data-ttu-id="9dffd-161">Razor los bloques de código comienzan con `@` y se incluyen en `{}` .</span><span class="sxs-lookup"><span data-stu-id="9dffd-161">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="9dffd-162">A diferencia de las expresiones, el código de C# dentro de los bloques de código no se representa.</span><span class="sxs-lookup"><span data-stu-id="9dffd-162">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="9dffd-163">Las expresiones y los bloques de código de una vista comparten el mismo ámbito y se definen en orden:</span><span class="sxs-lookup"><span data-stu-id="9dffd-163">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="9dffd-164">El código representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="9dffd-164">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9dffd-165">En los bloques de código, declare las [funciones locales](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) con marcado para utilizarlas como métodos en la creación de plantillas:</span><span class="sxs-lookup"><span data-stu-id="9dffd-165">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

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

<span data-ttu-id="9dffd-166">El código representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="9dffd-166">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="9dffd-167">Transiciones implícitas</span><span class="sxs-lookup"><span data-stu-id="9dffd-167">Implicit transitions</span></span>

<span data-ttu-id="9dffd-168">El lenguaje predeterminado en un bloque de código es C#, pero la Razor página puede volver a pasar a HTML:</span><span class="sxs-lookup"><span data-stu-id="9dffd-168">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="9dffd-169">Transición delimitada explícita</span><span class="sxs-lookup"><span data-stu-id="9dffd-169">Explicit delimited transition</span></span>

<span data-ttu-id="9dffd-170">Para definir una subsección de un bloque de código que debe representar HTML, rodee los caracteres para su representación con la Razor `<text>` etiqueta:</span><span class="sxs-lookup"><span data-stu-id="9dffd-170">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="9dffd-171">Emplee este método para representar HTML que no esté insertado entre etiquetas HTML.</span><span class="sxs-lookup"><span data-stu-id="9dffd-171">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="9dffd-172">Sin un código HTML o una Razor etiqueta, Razor se produce un error de tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="9dffd-172">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="9dffd-173">La etiqueta `<text>` es útil para controlar el espacio en blanco al representar el contenido:</span><span class="sxs-lookup"><span data-stu-id="9dffd-173">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="9dffd-174">Solo se representa el contenido entre etiquetas `<text>`.</span><span class="sxs-lookup"><span data-stu-id="9dffd-174">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="9dffd-175">En la salida HTML no hay espacios en blanco antes o después de la etiqueta `<text>`.</span><span class="sxs-lookup"><span data-stu-id="9dffd-175">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="9dffd-176">Transición de línea explícita</span><span class="sxs-lookup"><span data-stu-id="9dffd-176">Explicit line transition</span></span>

<span data-ttu-id="9dffd-177">Para representar el resto de una línea completa como HTML dentro de un bloque de código, use la sintaxis `@:`:</span><span class="sxs-lookup"><span data-stu-id="9dffd-177">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="9dffd-178">Sin `@:` en el código, Razor se genera un error de tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="9dffd-178">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="9dffd-179">`@`Los caracteres adicionales de un Razor archivo pueden producir errores del compilador en las instrucciones más adelante en el bloque.</span><span class="sxs-lookup"><span data-stu-id="9dffd-179">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="9dffd-180">Estos errores de compilador pueden ser difíciles de entender porque el error real se produce antes del error notificado.</span><span class="sxs-lookup"><span data-stu-id="9dffd-180">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="9dffd-181">Este error es habitual después de combinar varias expresiones implícitas/explícitas en un mismo bloque de código.</span><span class="sxs-lookup"><span data-stu-id="9dffd-181">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="9dffd-182">Estructuras de control</span><span class="sxs-lookup"><span data-stu-id="9dffd-182">Control structures</span></span>

<span data-ttu-id="9dffd-183">Las estructuras de control son una extensión de los bloques de código.</span><span class="sxs-lookup"><span data-stu-id="9dffd-183">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="9dffd-184">Todos los aspectos de los bloques de código (transición a marcado, C# en línea) son válidos también en las siguientes estructuras:</span><span class="sxs-lookup"><span data-stu-id="9dffd-184">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="9dffd-185">Condicionales `@if, else if, else, and @switch`</span><span class="sxs-lookup"><span data-stu-id="9dffd-185">Conditionals `@if, else if, else, and @switch`</span></span>

<span data-ttu-id="9dffd-186">`@if` controla cuándo se ejecuta el código:</span><span class="sxs-lookup"><span data-stu-id="9dffd-186">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="9dffd-187">`else` y `else if` no necesitan el símbolo `@`:</span><span class="sxs-lookup"><span data-stu-id="9dffd-187">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="9dffd-188">En el siguiente marcado se muestra cómo usar una instrucción switch:</span><span class="sxs-lookup"><span data-stu-id="9dffd-188">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="9dffd-189">Bucle `@for, @foreach, @while, and @do while`</span><span class="sxs-lookup"><span data-stu-id="9dffd-189">Looping `@for, @foreach, @while, and @do while`</span></span>

<span data-ttu-id="9dffd-190">El HTML con plantilla se puede representar con instrucciones de control en bucle.</span><span class="sxs-lookup"><span data-stu-id="9dffd-190">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="9dffd-191">Para representar una lista de personas:</span><span class="sxs-lookup"><span data-stu-id="9dffd-191">To render a list of people:</span></span>

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

<span data-ttu-id="9dffd-192">Se permiten las siguientes instrucciones en bucle:</span><span class="sxs-lookup"><span data-stu-id="9dffd-192">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="9dffd-193">Instrucción `@using` compuesta</span><span class="sxs-lookup"><span data-stu-id="9dffd-193">Compound `@using`</span></span>

<span data-ttu-id="9dffd-194">En C#, las instrucciones `using` se usan para garantizar que un objeto se elimina.</span><span class="sxs-lookup"><span data-stu-id="9dffd-194">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="9dffd-195">En Razor , se usa el mismo mecanismo para crear aplicaciones auxiliares HTML que contienen contenido adicional.</span><span class="sxs-lookup"><span data-stu-id="9dffd-195">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="9dffd-196">En el siguiente código, los asistentes de HTML representan una etiqueta `<form>` con la instrucción `@using`:</span><span class="sxs-lookup"><span data-stu-id="9dffd-196">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

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

<span data-ttu-id="9dffd-197">El control de excepciones es similar a C#:</span><span class="sxs-lookup"><span data-stu-id="9dffd-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

<span data-ttu-id="9dffd-198">Razor tiene la capacidad de proteger las secciones críticas con instrucciones Lock:</span><span class="sxs-lookup"><span data-stu-id="9dffd-198">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="9dffd-199">Comentarios</span><span class="sxs-lookup"><span data-stu-id="9dffd-199">Comments</span></span>

<span data-ttu-id="9dffd-200">Razor admite comentarios de C# y HTML:</span><span class="sxs-lookup"><span data-stu-id="9dffd-200">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="9dffd-201">El código representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="9dffd-201">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="9dffd-202">Razor el servidor quita los comentarios antes de que se represente la Página Web.</span><span class="sxs-lookup"><span data-stu-id="9dffd-202">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="9dffd-203">Razor utiliza `@*  *@` para delimitar los comentarios.</span><span class="sxs-lookup"><span data-stu-id="9dffd-203">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="9dffd-204">El siguiente código está comentado, de modo que el servidor no representa ningún marcado:</span><span class="sxs-lookup"><span data-stu-id="9dffd-204">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="9dffd-205">Directivas</span><span class="sxs-lookup"><span data-stu-id="9dffd-205">Directives</span></span>

<span data-ttu-id="9dffd-206">Razor las directivas se representan mediante expresiones implícitas con palabras clave reservadas después del `@` símbolo.</span><span class="sxs-lookup"><span data-stu-id="9dffd-206">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="9dffd-207">Normalmente, una directiva cambia la forma en que una vista se analiza, o bien habilita una funcionalidad diferente.</span><span class="sxs-lookup"><span data-stu-id="9dffd-207">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="9dffd-208">Entender cómo Razor genera código para una vista facilita la comprensión del funcionamiento de las directivas.</span><span class="sxs-lookup"><span data-stu-id="9dffd-208">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="9dffd-209">El código genera una clase similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="9dffd-209">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="9dffd-210">Más adelante en este artículo, la sección [inspeccionar la Razor clase de C# generada para una vista](#inspect-the-razor-c-class-generated-for-a-view) explica cómo ver esta clase generada.</span><span class="sxs-lookup"><span data-stu-id="9dffd-210">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### `@attribute`

<span data-ttu-id="9dffd-211">La directiva `@attribute` agrega el atributo especificado a la clase de la página o vista generada.</span><span class="sxs-lookup"><span data-stu-id="9dffd-211">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="9dffd-212">En el ejemplo siguiente se agrega el atributo `[Authorize]`:</span><span class="sxs-lookup"><span data-stu-id="9dffd-212">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

<span data-ttu-id="9dffd-213">*Este escenario solo se aplica a Razor los componentes de (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="9dffd-213">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="9dffd-214">El `@code` bloque permite a un [ Razor componente](xref:blazor/components/index) agregar miembros de C# (campos, propiedades y métodos) a un componente:</span><span class="sxs-lookup"><span data-stu-id="9dffd-214">The `@code` block enables a [Razor component](xref:blazor/components/index) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="9dffd-215">En el caso de Razor los componentes, `@code` es un alias de [`@functions`](#functions) y se recomienda en exceso `@functions` .</span><span class="sxs-lookup"><span data-stu-id="9dffd-215">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="9dffd-216">Se permite emplear más de un bloque `@code`.</span><span class="sxs-lookup"><span data-stu-id="9dffd-216">More than one `@code` block is permissible.</span></span>

::: moniker-end

### `@functions`

<span data-ttu-id="9dffd-217">La directiva `@functions` permite agregar miembros de C# (campos, propiedades y métodos) a la clase generada:</span><span class="sxs-lookup"><span data-stu-id="9dffd-217">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9dffd-218">En [ Razor componentes](xref:blazor/components/index), use `@code` `@functions` para agregar miembros de C#.</span><span class="sxs-lookup"><span data-stu-id="9dffd-218">In [Razor components](xref:blazor/components/index), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="9dffd-219">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9dffd-219">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="9dffd-220">El código genera el siguiente marcado HTML:</span><span class="sxs-lookup"><span data-stu-id="9dffd-220">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="9dffd-221">El código siguiente es la clase de C# generada Razor :</span><span class="sxs-lookup"><span data-stu-id="9dffd-221">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9dffd-222">Los métodos `@functions` se pueden usar para la creación de plantillas si están marcados:</span><span class="sxs-lookup"><span data-stu-id="9dffd-222">`@functions` methods serve as templating methods when they have markup:</span></span>

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

<span data-ttu-id="9dffd-223">El código representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="9dffd-223">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

<span data-ttu-id="9dffd-224">La directiva `@implements` implementa una interfaz para la clase generada.</span><span class="sxs-lookup"><span data-stu-id="9dffd-224">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="9dffd-225">En el ejemplo siguiente se implementa <xref:System.IDisposable?displayProperty=fullName> para que se pueda llamar al método <xref:System.IDisposable.Dispose*>:</span><span class="sxs-lookup"><span data-stu-id="9dffd-225">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

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

<span data-ttu-id="9dffd-226">La directiva `@inherits` proporciona control total sobre la clase que la vista hereda:</span><span class="sxs-lookup"><span data-stu-id="9dffd-226">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="9dffd-227">El código siguiente es un Razor tipo de página personalizado:</span><span class="sxs-lookup"><span data-stu-id="9dffd-227">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="9dffd-228">`CustomText` se muestra en una vista:</span><span class="sxs-lookup"><span data-stu-id="9dffd-228">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="9dffd-229">El código representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="9dffd-229">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="9dffd-230">`@model` y `@inherits` se pueden usar en la misma vista.</span><span class="sxs-lookup"><span data-stu-id="9dffd-230">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="9dffd-231">`@inherits` puede estar en un archivo *_ViewImports.cshtml* que la vista importa:</span><span class="sxs-lookup"><span data-stu-id="9dffd-231">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="9dffd-232">El siguiente código es un ejemplo de una vista fuertemente tipada:</span><span class="sxs-lookup"><span data-stu-id="9dffd-232">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="9dffd-233">Si "rick@contoso.com" se pasa en el modelo, la vista genera el siguiente marcado HTML:</span><span class="sxs-lookup"><span data-stu-id="9dffd-233">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

<span data-ttu-id="9dffd-234">La `@inject` directiva permite Razor que la página Inserte un servicio del [contenedor de servicios](xref:fundamentals/dependency-injection) en una vista.</span><span class="sxs-lookup"><span data-stu-id="9dffd-234">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="9dffd-235">Para más información, vea [Dependency injection into views](xref:mvc/views/dependency-injection) (Inserción de dependencias en vistas).</span><span class="sxs-lookup"><span data-stu-id="9dffd-235">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### `@layout`

<span data-ttu-id="9dffd-236">*Este escenario solo se aplica a Razor los componentes de (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="9dffd-236">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="9dffd-237">La `@layout` Directiva especifica un diseño para un Razor componente.</span><span class="sxs-lookup"><span data-stu-id="9dffd-237">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="9dffd-238">Los componentes de diseño se usan para evitar incoherencias y contenido duplicado en el código.</span><span class="sxs-lookup"><span data-stu-id="9dffd-238">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="9dffd-239">Para obtener más información, vea <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="9dffd-239">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### `@model`

<span data-ttu-id="9dffd-240">*Este escenario solo se aplica a las vistas y páginas de MVC Razor (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="9dffd-240">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="9dffd-241">La directiva `@model` especifica el tipo del modelo que se pasa a una vista o página:</span><span class="sxs-lookup"><span data-stu-id="9dffd-241">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="9dffd-242">En un ASP.NET Core aplicación MVC o Razor pages creada con cuentas de usuario individuales, *views/Account/login. cshtml* contiene la siguiente declaración de modelo:</span><span class="sxs-lookup"><span data-stu-id="9dffd-242">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="9dffd-243">La clase generada se hereda de `RazorPage<dynamic>`:</span><span class="sxs-lookup"><span data-stu-id="9dffd-243">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="9dffd-244">Razor expone una `Model` propiedad para obtener acceso al modelo que se pasa a la vista:</span><span class="sxs-lookup"><span data-stu-id="9dffd-244">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="9dffd-245">La directiva `@model` especifica el tipo de la propiedad `Model`.</span><span class="sxs-lookup"><span data-stu-id="9dffd-245">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="9dffd-246">La directiva especifica el elemento `T` en `RazorPage<T>` de la clase generada de la que se deriva la vista.</span><span class="sxs-lookup"><span data-stu-id="9dffd-246">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="9dffd-247">Si la directiva `@model` no se especifica, la propiedad `Model` es de tipo `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="9dffd-247">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="9dffd-248">Para obtener más información, vea [modelos fuertemente tipados y la @model palabra clave](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="9dffd-248">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### `@namespace`

<span data-ttu-id="9dffd-249">Directiva `@namespace`:</span><span class="sxs-lookup"><span data-stu-id="9dffd-249">The `@namespace` directive:</span></span>

* <span data-ttu-id="9dffd-250">Establece el espacio de nombres de la clase de la Razor página, vista de MVC o componente generados Razor .</span><span class="sxs-lookup"><span data-stu-id="9dffd-250">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="9dffd-251">Establece los espacios de nombres derivados de la raíz de las clases de páginas, vistas o componentes del archivo de importaciones más cercano en el árbol de directorios, *_ViewImports. cshtml* (vistas o páginas) o *_Imports. Razor* ( Razor componentes).</span><span class="sxs-lookup"><span data-stu-id="9dffd-251">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="9dffd-252">En el Razor ejemplo de páginas que se muestra en la tabla siguiente:</span><span class="sxs-lookup"><span data-stu-id="9dffd-252">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="9dffd-253">Cada página importa *Pages/_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="9dffd-253">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="9dffd-254">*Pages/_ViewImports.cshtml* contiene `@namespace Hello.World`.</span><span class="sxs-lookup"><span data-stu-id="9dffd-254">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="9dffd-255">Cada página tiene `Hello.World` como raíz de su espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="9dffd-255">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="9dffd-256">Página</span><span class="sxs-lookup"><span data-stu-id="9dffd-256">Page</span></span>                                        | <span data-ttu-id="9dffd-257">Espacio de nombres</span><span class="sxs-lookup"><span data-stu-id="9dffd-257">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="9dffd-258">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="9dffd-258">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="9dffd-259">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="9dffd-259">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="9dffd-260">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="9dffd-260">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="9dffd-261">Las relaciones anteriores se aplican a los archivos de importación usados con las vistas y Razor los componentes de MVC.</span><span class="sxs-lookup"><span data-stu-id="9dffd-261">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="9dffd-262">Cuando varios archivos de importación tienen una directiva `@namespace`, se usa el archivo más cercano a la página, vista o componente en el árbol de directorios para establecer el espacio de nombres raíz.</span><span class="sxs-lookup"><span data-stu-id="9dffd-262">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="9dffd-263">Si la carpeta *EvenMorePages* del ejemplo anterior tiene un archivo de importaciones con `@namespace Another.Planet` (o el archivo *Pages/MorePages/EvenMorePages/Page.cshtml* contiene `@namespace Another.Planet`), el resultado es el que se muestra en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="9dffd-263">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="9dffd-264">Página</span><span class="sxs-lookup"><span data-stu-id="9dffd-264">Page</span></span>                                        | <span data-ttu-id="9dffd-265">Espacio de nombres</span><span class="sxs-lookup"><span data-stu-id="9dffd-265">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="9dffd-266">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="9dffd-266">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="9dffd-267">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="9dffd-267">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="9dffd-268">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="9dffd-268">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9dffd-269">La directiva `@page` tiene efectos diferentes en función del tipo de archivo en el que aparece.</span><span class="sxs-lookup"><span data-stu-id="9dffd-269">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="9dffd-270">Directiva:</span><span class="sxs-lookup"><span data-stu-id="9dffd-270">The directive:</span></span>

* <span data-ttu-id="9dffd-271">En un archivo *. cshtml* indica que el archivo es una Razor página.</span><span class="sxs-lookup"><span data-stu-id="9dffd-271">In a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="9dffd-272">Para más información, consulte [Rutas personalizadas](xref:razor-pages/index#custom-routes) y <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="9dffd-272">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="9dffd-273">Especifica que un Razor componente debe controlar las solicitudes directamente.</span><span class="sxs-lookup"><span data-stu-id="9dffd-273">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="9dffd-274">Para obtener más información, vea <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="9dffd-274">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9dffd-275">La `@page` Directiva en la primera línea de un archivo *. cshtml* indica que el archivo es una Razor página.</span><span class="sxs-lookup"><span data-stu-id="9dffd-275">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="9dffd-276">Para obtener más información, vea <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="9dffd-276">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### `@section`

<span data-ttu-id="9dffd-277">*Este escenario solo se aplica a las vistas y páginas de MVC Razor (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="9dffd-277">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="9dffd-278">La `@section` Directiva se usa junto con los [diseños MVC y Razor pages](xref:mvc/views/layout) para permitir que las vistas o páginas representen el contenido en diferentes partes de la página HTML.</span><span class="sxs-lookup"><span data-stu-id="9dffd-278">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="9dffd-279">Para obtener más información, vea <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="9dffd-279">For more information, see <xref:mvc/views/layout>.</span></span>

### `@using`

<span data-ttu-id="9dffd-280">La directiva `@using` agrega la directiva `using` de C# a la vista generada:</span><span class="sxs-lookup"><span data-stu-id="9dffd-280">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9dffd-281">En [ Razor Components](xref:blazor/components/index), `@using` también controla qué componentes están en el ámbito.</span><span class="sxs-lookup"><span data-stu-id="9dffd-281">In [Razor components](xref:blazor/components/index), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="9dffd-282">Atributos de la directiva</span><span class="sxs-lookup"><span data-stu-id="9dffd-282">Directive attributes</span></span>

<span data-ttu-id="9dffd-283">Razor los atributos de Directiva se representan mediante expresiones implícitas con palabras clave reservadas después del `@` símbolo.</span><span class="sxs-lookup"><span data-stu-id="9dffd-283">Razor directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="9dffd-284">Un atributo de directiva suele cambiar la forma en que se analiza un elemento o habilita una funcionalidad diferente.</span><span class="sxs-lookup"><span data-stu-id="9dffd-284">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### `@attributes`

<span data-ttu-id="9dffd-285">*Este escenario solo se aplica a Razor los componentes de (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="9dffd-285">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="9dffd-286">`@attributes` permite que un componente represente atributos no declarados.</span><span class="sxs-lookup"><span data-stu-id="9dffd-286">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="9dffd-287">Para obtener más información, vea <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="9dffd-287">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

### `@bind`

<span data-ttu-id="9dffd-288">*Este escenario solo se aplica a Razor los componentes de (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="9dffd-288">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="9dffd-289">El enlace de datos en los componentes se logra mediante el atributo `@bind`.</span><span class="sxs-lookup"><span data-stu-id="9dffd-289">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="9dffd-290">Para obtener más información, vea <xref:blazor/components/data-binding>.</span><span class="sxs-lookup"><span data-stu-id="9dffd-290">For more information, see <xref:blazor/components/data-binding>.</span></span>

### `@on{EVENT}`

<span data-ttu-id="9dffd-291">*Este escenario solo se aplica a Razor los componentes de (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="9dffd-291">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="9dffd-292">Razor proporciona características de control de eventos para los componentes de.</span><span class="sxs-lookup"><span data-stu-id="9dffd-292">Razor provides event handling features for components.</span></span> <span data-ttu-id="9dffd-293">Para obtener más información, vea <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="9dffd-293">For more information, see <xref:blazor/components/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

<span data-ttu-id="9dffd-294">*Este escenario solo se aplica a Razor los componentes de (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="9dffd-294">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="9dffd-295">Impide la acción predeterminada para el evento.</span><span class="sxs-lookup"><span data-stu-id="9dffd-295">Prevents the default action for the event.</span></span>

### `@on{EVENT}:stopPropagation`

<span data-ttu-id="9dffd-296">*Este escenario solo se aplica a Razor los componentes de (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="9dffd-296">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="9dffd-297">Detiene la propagación de eventos para el evento.</span><span class="sxs-lookup"><span data-stu-id="9dffd-297">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

<span data-ttu-id="9dffd-298">*Este escenario solo se aplica a Razor los componentes de (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="9dffd-298">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="9dffd-299">El atributo de directiva `@key` hace que el algoritmo de comparación de componentes garantice la preservación de elementos o componentes en función del valor de la clave.</span><span class="sxs-lookup"><span data-stu-id="9dffd-299">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="9dffd-300">Para obtener más información, vea <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="9dffd-300">For more information, see <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### `@ref`

<span data-ttu-id="9dffd-301">*Este escenario solo se aplica a Razor los componentes de (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="9dffd-301">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="9dffd-302">Las referencias de componentes (`@ref`) proporcionan una forma de hacer referencia a la instancia de un componente para poder emitir comandos a dicha instancia.</span><span class="sxs-lookup"><span data-stu-id="9dffd-302">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="9dffd-303">Para obtener más información, vea <xref:blazor/components/index#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="9dffd-303">For more information, see <xref:blazor/components/index#capture-references-to-components>.</span></span>

### `@typeparam`

<span data-ttu-id="9dffd-304">*Este escenario solo se aplica a Razor los componentes de (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="9dffd-304">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="9dffd-305">La directiva `@typeparam` declara un parámetro de tipo genérico para la clase de componente generada.</span><span class="sxs-lookup"><span data-stu-id="9dffd-305">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="9dffd-306">Para obtener más información, vea <xref:blazor/components/templated-components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="9dffd-306">For more information, see <xref:blazor/components/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-no-locrazor-delegates"></a><span data-ttu-id="9dffd-307">Delegados con plantilla Razor</span><span class="sxs-lookup"><span data-stu-id="9dffd-307">Templated Razor delegates</span></span>

<span data-ttu-id="9dffd-308">Razor las plantillas permiten definir un fragmento de la interfaz de usuario con el siguiente formato:</span><span class="sxs-lookup"><span data-stu-id="9dffd-308">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="9dffd-309">En el ejemplo siguiente se muestra cómo especificar un Razor delegado con plantilla como <xref:System.Func%602> .</span><span class="sxs-lookup"><span data-stu-id="9dffd-309">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="9dffd-310">El [tipo dinámico](/dotnet/csharp/programming-guide/types/using-type-dynamic) se especifica para el parámetro del método encapsulado por el delegado.</span><span class="sxs-lookup"><span data-stu-id="9dffd-310">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="9dffd-311">Se especifica un [tipo de objeto](/dotnet/csharp/language-reference/keywords/object) como el valor devuelto del delegado.</span><span class="sxs-lookup"><span data-stu-id="9dffd-311">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="9dffd-312">La plantilla se usa con un elemento <xref:System.Collections.Generic.List%601> de `Pet` que tiene una propiedad `Name`.</span><span class="sxs-lookup"><span data-stu-id="9dffd-312">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="9dffd-313">La plantilla se representa con el elemento `pets` proporcionado por una instrucción `foreach`:</span><span class="sxs-lookup"><span data-stu-id="9dffd-313">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="9dffd-314">Salida representada:</span><span class="sxs-lookup"><span data-stu-id="9dffd-314">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="9dffd-315">También puede proporcionar una plantilla insertada Razor como argumento a un método.</span><span class="sxs-lookup"><span data-stu-id="9dffd-315">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="9dffd-316">En el ejemplo siguiente, el `Repeat` método recibe una Razor plantilla.</span><span class="sxs-lookup"><span data-stu-id="9dffd-316">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="9dffd-317">El método usa la plantilla para generar contenido HTML con repeticiones de elementos proporcionados a partir de una lista:</span><span class="sxs-lookup"><span data-stu-id="9dffd-317">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="9dffd-318">Con la lista de mascotas del ejemplo anterior, se llama al método `Repeat` con:</span><span class="sxs-lookup"><span data-stu-id="9dffd-318">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="9dffd-319"><xref:System.Collections.Generic.List%601> de `Pet`.</span><span class="sxs-lookup"><span data-stu-id="9dffd-319"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="9dffd-320">Número de veces que se repite cada mascota.</span><span class="sxs-lookup"><span data-stu-id="9dffd-320">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="9dffd-321">Plantilla insertada que se va a usar para los elementos de una lista sin ordenar.</span><span class="sxs-lookup"><span data-stu-id="9dffd-321">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="9dffd-322">Salida representada:</span><span class="sxs-lookup"><span data-stu-id="9dffd-322">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="9dffd-323">Asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="9dffd-323">Tag Helpers</span></span>

<span data-ttu-id="9dffd-324">*Este escenario solo se aplica a las vistas y páginas de MVC Razor (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="9dffd-324">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="9dffd-325">Hay tres directivas que pertenecen a los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="9dffd-325">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="9dffd-326">Directiva</span><span class="sxs-lookup"><span data-stu-id="9dffd-326">Directive</span></span> | <span data-ttu-id="9dffd-327">Función</span><span class="sxs-lookup"><span data-stu-id="9dffd-327">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="9dffd-328">Pone los asistentes de etiquetas a disposición de una vista.</span><span class="sxs-lookup"><span data-stu-id="9dffd-328">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="9dffd-329">Quita los asistentes de etiquetas agregadas anteriormente desde una vista.</span><span class="sxs-lookup"><span data-stu-id="9dffd-329">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="9dffd-330">Especifica una cadena de prefijo de etiqueta para permitir la compatibilidad con el asistente de etiquetas y hacer explícito su uso.</span><span class="sxs-lookup"><span data-stu-id="9dffd-330">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="no-locrazor-reserved-keywords"></a><span data-ttu-id="9dffd-331">Razor Palabras clave reservadas</span><span class="sxs-lookup"><span data-stu-id="9dffd-331">Razor reserved keywords</span></span>

### <a name="no-locrazor-keywords"></a><span data-ttu-id="9dffd-332">Razor palabra</span><span class="sxs-lookup"><span data-stu-id="9dffd-332">Razor keywords</span></span>

* <span data-ttu-id="9dffd-333">`page` (Requiere ASP.NET Core 2,1 o posterior)</span><span class="sxs-lookup"><span data-stu-id="9dffd-333">`page` (Requires ASP.NET Core 2.1 or later)</span></span>
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* <span data-ttu-id="9dffd-334">`helper` (Actualmente no es compatible con ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="9dffd-334">`helper` (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="9dffd-335">Razor las palabras clave se incluyen en secuencias `@(Razor Keyword)` de escape (por ejemplo, `@(functions)` ).</span><span class="sxs-lookup"><span data-stu-id="9dffd-335">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-no-locrazor-keywords"></a><span data-ttu-id="9dffd-336">RazorPalabras clave de C#</span><span class="sxs-lookup"><span data-stu-id="9dffd-336">C# Razor keywords</span></span>

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

<span data-ttu-id="9dffd-337">Las Razor palabras clave de C# deben tener un doble escape con `@(@C# Razor Keyword)` (por ejemplo, `@(@case)` ).</span><span class="sxs-lookup"><span data-stu-id="9dffd-337">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="9dffd-338">El primero `@` convierte el analizador en caracteres de escape Razor .</span><span class="sxs-lookup"><span data-stu-id="9dffd-338">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="9dffd-339">y el segundo `@`, en el analizador de C#.</span><span class="sxs-lookup"><span data-stu-id="9dffd-339">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-no-locrazor"></a><span data-ttu-id="9dffd-340">Palabras clave reservadas no utilizadas por Razor</span><span class="sxs-lookup"><span data-stu-id="9dffd-340">Reserved keywords not used by Razor</span></span>

* `class`

## <a name="inspect-the-no-locrazor-c-class-generated-for-a-view"></a><span data-ttu-id="9dffd-341">Inspeccionar la Razor clase de C# generada para una vista</span><span class="sxs-lookup"><span data-stu-id="9dffd-341">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="9dffd-342">Con SDK de .NET Core 2,1 o posterior, el [ Razor SDK](xref:razor-pages/sdk) controla la compilación de Razor archivos.</span><span class="sxs-lookup"><span data-stu-id="9dffd-342">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="9dffd-343">Al compilar un proyecto, el Razor SDK genera un *build_configuration de <y de>/<Razor target_framework_moniker>/* directorio en la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="9dffd-343">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="9dffd-344">La estructura de directorios dentro del *Razor* directorio refleja la estructura de directorios del proyecto.</span><span class="sxs-lookup"><span data-stu-id="9dffd-344">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="9dffd-345">Considere la siguiente estructura de directorios en un proyecto de páginas de ASP.NET Core 2,1 que tiene Razor como destino .net Core 2,1:</span><span class="sxs-lookup"><span data-stu-id="9dffd-345">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

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

<span data-ttu-id="9dffd-346">Al compilar el proyecto en la configuración *Depurar* se crea el directorio *obj* siguiente:</span><span class="sxs-lookup"><span data-stu-id="9dffd-346">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

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

<span data-ttu-id="9dffd-347">Para ver la clase generada para *pages/index. cshtml*, Abra *obj/Debug/netcoreapp 2.1/ Razor /pages/index.g.cshtml.CS*.</span><span class="sxs-lookup"><span data-stu-id="9dffd-347">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="9dffd-348">Agregue la siguiente clase al proyecto de ASP.NET Core MVC:</span><span class="sxs-lookup"><span data-stu-id="9dffd-348">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="9dffd-349">En `Startup.ConfigureServices`, invalide el elemento `RazorTemplateEngine` agregado por MVC con la clase `CustomTemplateEngine`:</span><span class="sxs-lookup"><span data-stu-id="9dffd-349">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="9dffd-350">Establezca un punto de interrupción en la instrucción `return csharpDocument;` de `CustomTemplateEngine`.</span><span class="sxs-lookup"><span data-stu-id="9dffd-350">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="9dffd-351">Cuando la ejecución del programa se detenga en el punto de interrupción, vea el valor de `generatedCode`.</span><span class="sxs-lookup"><span data-stu-id="9dffd-351">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Vista del visualizador de texto de generatedCode](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="9dffd-353">Búsquedas de vistas y distinción entre mayúsculas y minúsculas</span><span class="sxs-lookup"><span data-stu-id="9dffd-353">View lookups and case sensitivity</span></span>

<span data-ttu-id="9dffd-354">El Razor motor de vista realiza búsquedas con distinción de mayúsculas y minúsculas para las vistas.</span><span class="sxs-lookup"><span data-stu-id="9dffd-354">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="9dffd-355">Pero la búsqueda real viene determinada por el sistema de archivos subyacente:</span><span class="sxs-lookup"><span data-stu-id="9dffd-355">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="9dffd-356">Origen basado en archivos:</span><span class="sxs-lookup"><span data-stu-id="9dffd-356">File based source:</span></span>
  * <span data-ttu-id="9dffd-357">En los sistemas operativos con sistemas de archivos que no distinguen entre mayúsculas y minúsculas (por ejemplo, Windows), las búsquedas de proveedor de archivos físicos no distinguirán mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="9dffd-357">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="9dffd-358">Por ejemplo, `return View("Test")` arrojará como resultados */Views/Home/Test.cshtml*, */Views/home/test.cshtml* y cualquier otra variante de mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="9dffd-358">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="9dffd-359">En los sistemas de archivos en los que sí se distingue entre mayúsculas y minúsculas (por ejemplo, Linux, OSX y al usar `EmbeddedFileProvider`), las búsquedas distinguirán mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="9dffd-359">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="9dffd-360">Por ejemplo, `return View("Test")` mostrará el resultado */Views/Home/Test.cshtml* única y exclusivamente.</span><span class="sxs-lookup"><span data-stu-id="9dffd-360">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="9dffd-361">Vistas precompiladas: En ASP.NET Core 2.0 y versiones posteriores, las búsquedas de vistas precompiladas no distinguen mayúsculas de minúsculas en todos los sistemas operativos.</span><span class="sxs-lookup"><span data-stu-id="9dffd-361">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="9dffd-362">Este comportamiento es idéntico al comportamiento del proveedor de archivos físicos en Windows.</span><span class="sxs-lookup"><span data-stu-id="9dffd-362">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="9dffd-363">Si dos vistas precompiladas difieren solo por sus mayúsculas o minúsculas, el resultado de la búsqueda será no determinante.</span><span class="sxs-lookup"><span data-stu-id="9dffd-363">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="9dffd-364">Por tanto, se anima a todos los desarrolladores a intentar que las mayúsculas y minúsculas de los nombres de archivo y de directorio sean las mismas que las mayúsculas y minúsculas de:</span><span class="sxs-lookup"><span data-stu-id="9dffd-364">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="9dffd-365">Nombres de acciones, controladores y áreas.</span><span class="sxs-lookup"><span data-stu-id="9dffd-365">Area, controller, and action names.</span></span>
* <span data-ttu-id="9dffd-366">Razor Páginas.</span><span class="sxs-lookup"><span data-stu-id="9dffd-366">Razor Pages.</span></span>

<span data-ttu-id="9dffd-367">La coincidencia de mayúsculas y minúsculas garantiza que las implementaciones van a encontrar sus vistas, independientemente de cuál sea el sistema de archivos subyacente.</span><span class="sxs-lookup"><span data-stu-id="9dffd-367">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9dffd-368">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="9dffd-368">Additional resources</span></span>

<span data-ttu-id="9dffd-369">[Introducción a la programación web de ASP.net mediante el Razor La sintaxis](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) proporciona muchos ejemplos de programación con Razor Sintaxis.</span><span class="sxs-lookup"><span data-stu-id="9dffd-369">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
