---
title: Impedir el scripting entre sitios (XSS) en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre las técnicas de scripting entre sitios (XSS) y para solucionar esta vulnerabilidad en una aplicación ASP.NET Core.
ms.author: riande
ms.date: 10/02/2018
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
uid: security/cross-site-scripting
ms.openlocfilehash: 38e9e102e9ac18ec14bceebf391c11a434492ac9
ms.sourcegitcommit: 6ecdc481d5b9a10d2c6e091217f017b36bdba957
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/15/2020
ms.locfileid: "90456067"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a><span data-ttu-id="dfc57-103">Impedir el scripting entre sitios (XSS) en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dfc57-103">Prevent Cross-Site Scripting (XSS) in ASP.NET Core</span></span>

<span data-ttu-id="dfc57-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="dfc57-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="dfc57-105">El scripting entre sitios (XSS) es una vulnerabilidad de seguridad que permite a un atacante colocar scripts del lado cliente (normalmente JavaScript) en páginas Web.</span><span class="sxs-lookup"><span data-stu-id="dfc57-105">Cross-Site Scripting (XSS) is a security vulnerability which enables an attacker to place client side scripts (usually JavaScript) into web pages.</span></span> <span data-ttu-id="dfc57-106">Cuando otros usuarios cargan las páginas afectadas, se ejecutarán los scripts del atacante, lo que permite que el atacante robe cookie s y tokens de sesión, cambie el contenido de la página web a través de la manipulación del Dom o redirija el explorador a otra página.</span><span class="sxs-lookup"><span data-stu-id="dfc57-106">When other users load affected pages the attacker's scripts will run, enabling the attacker to steal cookies and session tokens, change the contents of the web page through DOM manipulation or redirect the browser to another page.</span></span> <span data-ttu-id="dfc57-107">Las vulnerabilidades XSS suelen producirse cuando una aplicación toma datos proporcionados por el usuario y los envía a una página sin validarla, codificarla o escapar de ella.</span><span class="sxs-lookup"><span data-stu-id="dfc57-107">XSS vulnerabilities generally occur when an application takes user input and outputs it to a page without validating, encoding or escaping it.</span></span>

## <a name="protecting-your-application-against-xss"></a><span data-ttu-id="dfc57-108">Proteger la aplicación contra XSS</span><span class="sxs-lookup"><span data-stu-id="dfc57-108">Protecting your application against XSS</span></span>

<span data-ttu-id="dfc57-109">En un nivel básico, XSS funciona engañando a la aplicación para insertar una `<script>` etiqueta en la página representada o insertando un `On*` evento en un elemento.</span><span class="sxs-lookup"><span data-stu-id="dfc57-109">At a basic level XSS works by tricking your application into inserting a `<script>` tag into your rendered page, or by inserting an `On*` event into an element.</span></span> <span data-ttu-id="dfc57-110">Los desarrolladores deben usar los siguientes pasos de prevención para evitar la introducción de XSS en su aplicación.</span><span class="sxs-lookup"><span data-stu-id="dfc57-110">Developers should use the following prevention steps to avoid introducing XSS into their application.</span></span>

1. <span data-ttu-id="dfc57-111">No coloque nunca los datos que no son de confianza en la entrada HTML, a menos que siga el resto de los pasos que se indican a continuación.</span><span class="sxs-lookup"><span data-stu-id="dfc57-111">Never put untrusted data into your HTML input, unless you follow the rest of the steps below.</span></span> <span data-ttu-id="dfc57-112">Los datos que no son de confianza son aquellos que pueden ser controlados por un atacante, entradas de formulario HTML, cadenas de consulta, encabezados HTTP, incluso los datos procedentes de una base de datos como atacante pueden infringir la base de datos incluso si no pueden infringir la aplicación.</span><span class="sxs-lookup"><span data-stu-id="dfc57-112">Untrusted data is any data that may be controlled by an attacker, HTML form inputs, query strings, HTTP headers, even data sourced from a database as an attacker may be able to breach your database even if they cannot breach your application.</span></span>

2. <span data-ttu-id="dfc57-113">Antes de colocar los datos que no son de confianza dentro de un elemento HTML, asegúrese de que está codificado en HTML.</span><span class="sxs-lookup"><span data-stu-id="dfc57-113">Before putting untrusted data inside an HTML element ensure it's HTML encoded.</span></span> <span data-ttu-id="dfc57-114">La codificación HTML toma caracteres como &lt; y los cambia de forma segura como &amp; lt;</span><span class="sxs-lookup"><span data-stu-id="dfc57-114">HTML encoding takes characters such as &lt; and changes them into a safe form like &amp;lt;</span></span>

3. <span data-ttu-id="dfc57-115">Antes de colocar los datos que no son de confianza en un atributo HTML, asegúrese de que está codificado en HTML.</span><span class="sxs-lookup"><span data-stu-id="dfc57-115">Before putting untrusted data into an HTML attribute ensure it's HTML encoded.</span></span> <span data-ttu-id="dfc57-116">La codificación de atributos HTML es un supraconjunto de codificación HTML y codifica caracteres adicionales como "y".</span><span class="sxs-lookup"><span data-stu-id="dfc57-116">HTML attribute encoding is a superset of HTML encoding and encodes additional characters such as " and '.</span></span>

4. <span data-ttu-id="dfc57-117">Antes de colocar los datos que no son de confianza en JavaScript, coloque los datos en un elemento HTML cuyo contenido recupera en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="dfc57-117">Before putting untrusted data into JavaScript place the data in an HTML element whose contents you retrieve at runtime.</span></span> <span data-ttu-id="dfc57-118">Si esto no es posible, asegúrese de que los datos están codificados con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="dfc57-118">If this isn't possible, then ensure the data is JavaScript encoded.</span></span> <span data-ttu-id="dfc57-119">La codificación de JavaScript toma caracteres peligrosos para JavaScript y los reemplaza por su hexadecimal, por ejemplo, &lt; se codificaría como `\u003C` .</span><span class="sxs-lookup"><span data-stu-id="dfc57-119">JavaScript encoding takes dangerous characters for JavaScript and replaces them with their hex, for example &lt; would be encoded as `\u003C`.</span></span>

5. <span data-ttu-id="dfc57-120">Antes de colocar los datos que no son de confianza en una cadena de consulta de dirección URL, asegúrese de que su dirección URL está codificada.</span><span class="sxs-lookup"><span data-stu-id="dfc57-120">Before putting untrusted data into a URL query string ensure it's URL encoded.</span></span>

## <a name="html-encoding-using-no-locrazor"></a><span data-ttu-id="dfc57-121">Codificación HTML mediante Razor</span><span class="sxs-lookup"><span data-stu-id="dfc57-121">HTML Encoding using Razor</span></span>

<span data-ttu-id="dfc57-122">El Razor motor usado en MVC codifica automáticamente todas las salidas que se han generado a partir de variables, a menos que trabaje realmente difícil para evitar que lo haga.</span><span class="sxs-lookup"><span data-stu-id="dfc57-122">The Razor engine used in MVC automatically encodes all output sourced from variables, unless you work really hard to prevent it doing so.</span></span> <span data-ttu-id="dfc57-123">Usa reglas de codificación de atributos HTML siempre que se usa la *@* Directiva.</span><span class="sxs-lookup"><span data-stu-id="dfc57-123">It uses HTML attribute encoding rules whenever you use the *@* directive.</span></span> <span data-ttu-id="dfc57-124">Como la codificación de atributos HTML es un supraconjunto de codificación HTML, esto significa que no tiene que preocuparse de si debe usar la codificación HTML o la codificación de atributos HTML.</span><span class="sxs-lookup"><span data-stu-id="dfc57-124">As HTML attribute encoding is a superset of HTML encoding this means you don't have to concern yourself with whether you should use HTML encoding or HTML attribute encoding.</span></span> <span data-ttu-id="dfc57-125">Debe asegurarse de que solo usa @ en un contexto HTML, no cuando intenta insertar una entrada que no es de confianza directamente en JavaScript.</span><span class="sxs-lookup"><span data-stu-id="dfc57-125">You must ensure that you only use @ in an HTML context, not when attempting to insert untrusted input directly into JavaScript.</span></span> <span data-ttu-id="dfc57-126">Las aplicaciones auxiliares de etiquetas también codificarán la entrada que se usa en los parámetros de etiqueta.</span><span class="sxs-lookup"><span data-stu-id="dfc57-126">Tag helpers will also encode input you use in tag parameters.</span></span>

<span data-ttu-id="dfc57-127">Tome la siguiente Razor vista:</span><span class="sxs-lookup"><span data-stu-id="dfc57-127">Take the following Razor view:</span></span>

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

<span data-ttu-id="dfc57-128">Esta vista genera el contenido de la variable *untrustedInput* .</span><span class="sxs-lookup"><span data-stu-id="dfc57-128">This view outputs the contents of the *untrustedInput* variable.</span></span> <span data-ttu-id="dfc57-129">Esta variable incluye algunos caracteres que se usan en ataques XSS, es decir &lt; , "y" &gt; .</span><span class="sxs-lookup"><span data-stu-id="dfc57-129">This variable includes some characters which are used in XSS attacks, namely &lt;, " and &gt;.</span></span> <span data-ttu-id="dfc57-130">Al examinar el origen se muestra la salida representada como:</span><span class="sxs-lookup"><span data-stu-id="dfc57-130">Examining the source shows the rendered output encoded as:</span></span>

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> <span data-ttu-id="dfc57-131">ASP.NET Core MVC proporciona una `HtmlString` clase que no se codifica automáticamente en la salida.</span><span class="sxs-lookup"><span data-stu-id="dfc57-131">ASP.NET Core MVC provides an `HtmlString` class which isn't automatically encoded upon output.</span></span> <span data-ttu-id="dfc57-132">Nunca debe usarse en combinación con una entrada que no sea de confianza, ya que esto expone una vulnerabilidad de XSS.</span><span class="sxs-lookup"><span data-stu-id="dfc57-132">This should never be used in combination with untrusted input as this will expose an XSS vulnerability.</span></span>

## <a name="javascript-encoding-using-no-locrazor"></a><span data-ttu-id="dfc57-133">Codificación de JavaScript mediante Razor</span><span class="sxs-lookup"><span data-stu-id="dfc57-133">JavaScript Encoding using Razor</span></span>

<span data-ttu-id="dfc57-134">Puede haber ocasiones en las que desee insertar un valor en JavaScript para procesarlo en la vista.</span><span class="sxs-lookup"><span data-stu-id="dfc57-134">There may be times you want to insert a value into JavaScript to process in your view.</span></span> <span data-ttu-id="dfc57-135">Existen dos formas de hacerlo.</span><span class="sxs-lookup"><span data-stu-id="dfc57-135">There are two ways to do this.</span></span> <span data-ttu-id="dfc57-136">La forma más segura de insertar valores es colocar el valor en un atributo de datos de una etiqueta y recuperarlo en el código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="dfc57-136">The safest way to insert values is to place the value in a data attribute of a tag and retrieve it in your JavaScript.</span></span> <span data-ttu-id="dfc57-137">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="dfc57-137">For example:</span></span>

```cshtml
@{
    var untrustedInput = "<script>alert(1)</script>";
}

<div id="injectedData"
     data-untrustedinput="@untrustedInput" />

<div id="scriptedWrite" />
<div id="scriptedWrite-html5" />

<script>
    var injectedData = document.getElementById("injectedData");

    // All clients
    var clientSideUntrustedInputOldStyle =
        injectedData.getAttribute("data-untrustedinput");

    // HTML 5 clients only
    var clientSideUntrustedInputHtml5 =
        injectedData.dataset.untrustedinput;

    // Put the injected, untrusted data into the scriptedWrite div tag.
    // Do NOT use document.write() on dynamically generated data as it
    // can lead to XSS.

    document.getElementById("scriptedWrite").innerText += clientSideUntrustedInputOldStyle;

    // Or you can use createElement() to dynamically create document elements
    // This time we're using textContent to ensure the data is properly encoded.
    var x = document.createElement("div");
    x.textContent = clientSideUntrustedInputHtml5;
    document.body.appendChild(x);

    // You can also use createTextNode on an element to ensure data is properly encoded.
    var y = document.createElement("div");
    y.appendChild(document.createTextNode(clientSideUntrustedInputHtml5));
    document.body.appendChild(y);

</script>
   ```

<span data-ttu-id="dfc57-138">El marcado anterior genera el siguiente código HTML:</span><span class="sxs-lookup"><span data-stu-id="dfc57-138">The preceding markup generates the following HTML:</span></span>

```html
<div id="injectedData"
     data-untrustedinput="&lt;script&gt;alert(1)&lt;/script&gt;" />

<div id="scriptedWrite" />
<div id="scriptedWrite-html5" />

<script>
    var injectedData = document.getElementById("injectedData");

    // All clients
    var clientSideUntrustedInputOldStyle =
        injectedData.getAttribute("data-untrustedinput");

    // HTML 5 clients only
    var clientSideUntrustedInputHtml5 =
        injectedData.dataset.untrustedinput;

    // Put the injected, untrusted data into the scriptedWrite div tag.
    // Do NOT use document.write() on dynamically generated data as it can
    // lead to XSS.

    document.getElementById("scriptedWrite").innerText += clientSideUntrustedInputOldStyle;

    // Or you can use createElement() to dynamically create document elements
    // This time we're using textContent to ensure the data is properly encoded.
    var x = document.createElement("div");
    x.textContent = clientSideUntrustedInputHtml5;
    document.body.appendChild(x);

    // You can also use createTextNode on an element to ensure data is properly encoded.
    var y = document.createElement("div");
    y.appendChild(document.createTextNode(clientSideUntrustedInputHtml5));
    document.body.appendChild(y);

</script>
   ```

<span data-ttu-id="dfc57-139">El código anterior genera el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="dfc57-139">The preceding code generates the following output:</span></span>

```
<script>alert(1)</script>
<script>alert(1)</script>
<script>alert(1)</script>
```

>[!WARNING]
> <span data-ttu-id="dfc57-140">No concatene la entrada que ***no*** es de confianza en JavaScript para crear elementos DOM o usar `document.write()` en contenido generado dinámicamente.</span><span class="sxs-lookup"><span data-stu-id="dfc57-140">Do ***NOT*** concatenate untrusted input in JavaScript to create DOM elements or use `document.write()` on dynamically generated content.</span></span>
>
> <span data-ttu-id="dfc57-141">Use uno de los métodos siguientes para evitar que el código se exponga a XSS basado en DOM:</span><span class="sxs-lookup"><span data-stu-id="dfc57-141">Use one of the following approaches to prevent code from being exposed to DOM-based XSS:</span></span>
> * <span data-ttu-id="dfc57-142">`createElement()` y asignan valores de propiedad con métodos o propiedades adecuados, como `node.textContent=` o nodo. InnerText = '.</span><span class="sxs-lookup"><span data-stu-id="dfc57-142">`createElement()` and assign property values with appropriate methods or properties such as `node.textContent=` or node.InnerText=\`.</span></span>
> * <span data-ttu-id="dfc57-143">`document.CreateTextNode()` y agréguelo en la ubicación del DOM adecuada.</span><span class="sxs-lookup"><span data-stu-id="dfc57-143">`document.CreateTextNode()` and append it in the appropriate DOM location.</span></span>
> * `element.SetAttribute()`
> * `element[attribute]=`

## <a name="accessing-encoders-in-code"></a><span data-ttu-id="dfc57-144">Obtener acceso a codificadores en el código</span><span class="sxs-lookup"><span data-stu-id="dfc57-144">Accessing encoders in code</span></span>

<span data-ttu-id="dfc57-145">Los codificadores HTML, JavaScript y URL están disponibles para el código de dos maneras: puede inyectarlos a través de la [inserción de dependencias](xref:fundamentals/dependency-injection) o puede usar los codificadores predeterminados contenidos en el `System.Text.Encodings.Web` espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="dfc57-145">The HTML, JavaScript and URL encoders are available to your code in two ways, you can inject them via [dependency injection](xref:fundamentals/dependency-injection) or you can use the default encoders contained in the `System.Text.Encodings.Web` namespace.</span></span> <span data-ttu-id="dfc57-146">Si usa los codificadores predeterminados, los que aplique a los intervalos de caracteres que se tratarán como seguros no surtirán efecto: los codificadores predeterminados usan las reglas de codificación más seguras posibles.</span><span class="sxs-lookup"><span data-stu-id="dfc57-146">If you use the default encoders then any  you applied to character ranges to be treated as safe won't take effect - the default encoders use the safest encoding rules possible.</span></span>

<span data-ttu-id="dfc57-147">Para usar los codificadores configurables a través de DI, los constructores deben tomar un parámetro *HtmlEncoder*, *JavaScriptEncoder* y *UrlEncoder* , según corresponda.</span><span class="sxs-lookup"><span data-stu-id="dfc57-147">To use the configurable encoders via DI your constructors should take an *HtmlEncoder*, *JavaScriptEncoder* and *UrlEncoder* parameter as appropriate.</span></span> <span data-ttu-id="dfc57-148">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="dfc57-148">For example;</span></span>

```csharp
public class HomeController : Controller
   {
       HtmlEncoder _htmlEncoder;
       JavaScriptEncoder _javaScriptEncoder;
       UrlEncoder _urlEncoder;

       public HomeController(HtmlEncoder htmlEncoder,
                             JavaScriptEncoder javascriptEncoder,
                             UrlEncoder urlEncoder)
       {
           _htmlEncoder = htmlEncoder;
           _javaScriptEncoder = javascriptEncoder;
           _urlEncoder = urlEncoder;
       }
   }
   ```

## <a name="encoding-url-parameters"></a><span data-ttu-id="dfc57-149">Parámetros de URL de codificación</span><span class="sxs-lookup"><span data-stu-id="dfc57-149">Encoding URL Parameters</span></span>

<span data-ttu-id="dfc57-150">Si desea crear una cadena de consulta de dirección URL con una entrada que no sea de confianza como valor, use `UrlEncoder` para codificar el valor.</span><span class="sxs-lookup"><span data-stu-id="dfc57-150">If you want to build a URL query string with untrusted input as a value use the `UrlEncoder` to encode the value.</span></span> <span data-ttu-id="dfc57-151">Por ejemplo,</span><span class="sxs-lookup"><span data-stu-id="dfc57-151">For example,</span></span>

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

<span data-ttu-id="dfc57-152">Después de la codificación, la variable encodedValue contendrá `%22Quoted%20Value%20with%20spaces%20and%20%26%22` .</span><span class="sxs-lookup"><span data-stu-id="dfc57-152">After encoding the encodedValue variable will contain `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span></span> <span data-ttu-id="dfc57-153">Los espacios, las comillas, los signos de puntuación y otros caracteres no seguros se codificarán por porcentaje en su valor hexadecimal; por ejemplo, un carácter de espacio se convertirá en %20.</span><span class="sxs-lookup"><span data-stu-id="dfc57-153">Spaces, quotes, punctuation and other unsafe characters will be percent encoded to their hexadecimal value, for example a space character will become %20.</span></span>

>[!WARNING]
> <span data-ttu-id="dfc57-154">No utilice la entrada que no sea de confianza como parte de una ruta de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="dfc57-154">Don't use untrusted input as part of a URL path.</span></span> <span data-ttu-id="dfc57-155">Pase siempre la entrada que no sea de confianza como un valor de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="dfc57-155">Always pass untrusted input as a query string value.</span></span>

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a><span data-ttu-id="dfc57-156">Personalización de los codificadores</span><span class="sxs-lookup"><span data-stu-id="dfc57-156">Customizing the Encoders</span></span>

<span data-ttu-id="dfc57-157">De forma predeterminada, los codificadores usan una lista segura limitada al intervalo de Unicode latín básico y codifican todos los caracteres que se encuentran fuera de ese intervalo como sus equivalentes en el código de carácter.</span><span class="sxs-lookup"><span data-stu-id="dfc57-157">By default encoders use a safe list limited to the Basic Latin Unicode range and encode all characters outside of that range as their character code equivalents.</span></span> <span data-ttu-id="dfc57-158">Este comportamiento también afecta a Razor la representación de TagHelper y HtmlHelper, ya que usará los codificadores para generar las cadenas.</span><span class="sxs-lookup"><span data-stu-id="dfc57-158">This behavior also affects Razor TagHelper and HtmlHelper rendering as it will use the encoders to output your strings.</span></span>

<span data-ttu-id="dfc57-159">La razón de esto es proteger frente a errores desconocidos o futuros del explorador (los errores anteriores del explorador se han recorrido en el análisis en función del procesamiento de caracteres que no estén en inglés).</span><span class="sxs-lookup"><span data-stu-id="dfc57-159">The reasoning behind this is to protect against unknown or future browser bugs (previous browser bugs have tripped up parsing based on the processing of non-English characters).</span></span> <span data-ttu-id="dfc57-160">Si el sitio web hace un uso intensivo de caracteres no latinos, como chino, cirílico u otros, es probable que no sea el comportamiento deseado.</span><span class="sxs-lookup"><span data-stu-id="dfc57-160">If your web site makes heavy use of non-Latin characters, such as Chinese, Cyrillic or others this is probably not the behavior you want.</span></span>

<span data-ttu-id="dfc57-161">Puede personalizar las listas seguras del codificador para incluir los intervalos Unicode adecuados para la aplicación durante el inicio, en `ConfigureServices()` .</span><span class="sxs-lookup"><span data-stu-id="dfc57-161">You can customize the encoder safe lists to include Unicode ranges appropriate to your application during startup, in `ConfigureServices()`.</span></span>

<span data-ttu-id="dfc57-162">Por ejemplo, si usa la configuración predeterminada, puede usar una Razor HtmlHelper como tal;</span><span class="sxs-lookup"><span data-stu-id="dfc57-162">For example, using the default configuration you might use a Razor HtmlHelper like so;</span></span>

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

<span data-ttu-id="dfc57-163">Al ver el origen de la página web, verá que se ha representado como sigue, con el texto en chino codificado.</span><span class="sxs-lookup"><span data-stu-id="dfc57-163">When you view the source of the web page you will see it has been rendered as follows, with the Chinese text encoded;</span></span>

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

<span data-ttu-id="dfc57-164">Para ampliar los caracteres que el codificador trata como seguros, debe insertar la siguiente línea en el `ConfigureServices()` método de `startup.cs` ;</span><span class="sxs-lookup"><span data-stu-id="dfc57-164">To widen the characters treated as safe by the encoder you would insert the following line into the `ConfigureServices()` method in `startup.cs`;</span></span>

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

<span data-ttu-id="dfc57-165">Este ejemplo amplía la lista segura para incluir el intervalo Unicode CjkUnifiedIdeographs.</span><span class="sxs-lookup"><span data-stu-id="dfc57-165">This example widens the safe list to include the Unicode Range CjkUnifiedIdeographs.</span></span> <span data-ttu-id="dfc57-166">La salida representada se convertirá ahora en</span><span class="sxs-lookup"><span data-stu-id="dfc57-166">The rendered output would now become</span></span>

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

<span data-ttu-id="dfc57-167">Los intervalos de la lista segura se especifican como gráficos de códigos Unicode, no como lenguajes.</span><span class="sxs-lookup"><span data-stu-id="dfc57-167">Safe list ranges are specified as Unicode code charts, not languages.</span></span> <span data-ttu-id="dfc57-168">El [estándar Unicode](https://unicode.org/) tiene una lista de [gráficos de código](https://www.unicode.org/charts/index.html) que puede usar para buscar el gráfico que contiene los caracteres.</span><span class="sxs-lookup"><span data-stu-id="dfc57-168">The [Unicode standard](https://unicode.org/) has a list of [code charts](https://www.unicode.org/charts/index.html) you can use to find the chart containing your characters.</span></span> <span data-ttu-id="dfc57-169">Cada codificador, HTML, JavaScript y URL, debe configurarse por separado.</span><span class="sxs-lookup"><span data-stu-id="dfc57-169">Each encoder, Html, JavaScript and Url, must be configured separately.</span></span>

> [!NOTE]
> <span data-ttu-id="dfc57-170">La personalización de la lista segura solo afecta a los codificadores que tienen el origen a través de DI.</span><span class="sxs-lookup"><span data-stu-id="dfc57-170">Customization of the safe list only affects encoders sourced via DI.</span></span> <span data-ttu-id="dfc57-171">Si obtiene acceso directamente a un codificador a través `System.Text.Encodings.Web.*Encoder.Default` de, se usará la configuración de la base de datos de solo latín básica básica.</span><span class="sxs-lookup"><span data-stu-id="dfc57-171">If you directly access an encoder via `System.Text.Encodings.Web.*Encoder.Default` then the default, Basic Latin only safelist will be used.</span></span>

## <a name="where-should-encoding-take-place"></a><span data-ttu-id="dfc57-172">¿Dónde se debe realizar la codificación?</span><span class="sxs-lookup"><span data-stu-id="dfc57-172">Where should encoding take place?</span></span>

<span data-ttu-id="dfc57-173">La práctica aceptada general es que la codificación se realiza en el punto de salida y los valores codificados nunca deben almacenarse en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="dfc57-173">The general accepted practice is that encoding takes place at the point of output and encoded values should never be stored in a database.</span></span> <span data-ttu-id="dfc57-174">La codificación en el punto de salida permite cambiar el uso de datos, por ejemplo, de HTML a un valor de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="dfc57-174">Encoding at the point of output allows you to change the use of data, for example, from HTML to a query string value.</span></span> <span data-ttu-id="dfc57-175">También permite realizar búsquedas fácilmente en los datos sin tener que codificar valores antes de realizar búsquedas y permite aprovechar las ventajas de los cambios o las correcciones de errores que se realicen en los codificadores.</span><span class="sxs-lookup"><span data-stu-id="dfc57-175">It also enables you to easily search your data without having to encode values before searching and allows you to take advantage of any changes or bug fixes made to encoders.</span></span>

## <a name="validation-as-an-xss-prevention-technique"></a><span data-ttu-id="dfc57-176">Validación como técnica de prevención de XSS</span><span class="sxs-lookup"><span data-stu-id="dfc57-176">Validation as an XSS prevention technique</span></span>

<span data-ttu-id="dfc57-177">La validación puede ser una herramienta útil para limitar los ataques XSS.</span><span class="sxs-lookup"><span data-stu-id="dfc57-177">Validation can be a useful tool in limiting XSS attacks.</span></span> <span data-ttu-id="dfc57-178">Por ejemplo, una cadena numérica que solo contenga los caracteres 0-9 no desencadenará un ataque XSS.</span><span class="sxs-lookup"><span data-stu-id="dfc57-178">For example, a numeric string containing only the characters 0-9 won't trigger an XSS attack.</span></span> <span data-ttu-id="dfc57-179">La validación es más complicada al aceptar HTML en los datos proporcionados por el usuario.</span><span class="sxs-lookup"><span data-stu-id="dfc57-179">Validation becomes more complicated when accepting HTML in user input.</span></span> <span data-ttu-id="dfc57-180">El análisis de la entrada HTML es difícil, si no imposible.</span><span class="sxs-lookup"><span data-stu-id="dfc57-180">Parsing HTML input is difficult, if not impossible.</span></span> <span data-ttu-id="dfc57-181">Markdown, junto con un analizador que elimina el código HTML incrustado, es una opción más segura para aceptar entradas enriquecidas.</span><span class="sxs-lookup"><span data-stu-id="dfc57-181">Markdown, coupled with a parser that strips embedded HTML, is a safer option for accepting rich input.</span></span> <span data-ttu-id="dfc57-182">Nunca se basa solo en la validación.</span><span class="sxs-lookup"><span data-stu-id="dfc57-182">Never rely on validation alone.</span></span> <span data-ttu-id="dfc57-183">Codifique siempre la entrada que no sea de confianza antes de la salida, independientemente de la validación o la limpieza que se haya realizado.</span><span class="sxs-lookup"><span data-stu-id="dfc57-183">Always encode untrusted input before output, no matter what validation or sanitization has been performed.</span></span>
