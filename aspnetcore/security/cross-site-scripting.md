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
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a>Impedir el scripting entre sitios (XSS) en ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

El scripting entre sitios (XSS) es una vulnerabilidad de seguridad que permite a un atacante colocar scripts del lado cliente (normalmente JavaScript) en páginas Web. Cuando otros usuarios cargan las páginas afectadas, se ejecutarán los scripts del atacante, lo que permite que el atacante robe cookie s y tokens de sesión, cambie el contenido de la página web a través de la manipulación del Dom o redirija el explorador a otra página. Las vulnerabilidades XSS suelen producirse cuando una aplicación toma datos proporcionados por el usuario y los envía a una página sin validarla, codificarla o escapar de ella.

## <a name="protecting-your-application-against-xss"></a>Proteger la aplicación contra XSS

En un nivel básico, XSS funciona engañando a la aplicación para insertar una `<script>` etiqueta en la página representada o insertando un `On*` evento en un elemento. Los desarrolladores deben usar los siguientes pasos de prevención para evitar la introducción de XSS en su aplicación.

1. No coloque nunca los datos que no son de confianza en la entrada HTML, a menos que siga el resto de los pasos que se indican a continuación. Los datos que no son de confianza son aquellos que pueden ser controlados por un atacante, entradas de formulario HTML, cadenas de consulta, encabezados HTTP, incluso los datos procedentes de una base de datos como atacante pueden infringir la base de datos incluso si no pueden infringir la aplicación.

2. Antes de colocar los datos que no son de confianza dentro de un elemento HTML, asegúrese de que está codificado en HTML. La codificación HTML toma caracteres como &lt; y los cambia de forma segura como &amp; lt;

3. Antes de colocar los datos que no son de confianza en un atributo HTML, asegúrese de que está codificado en HTML. La codificación de atributos HTML es un supraconjunto de codificación HTML y codifica caracteres adicionales como "y".

4. Antes de colocar los datos que no son de confianza en JavaScript, coloque los datos en un elemento HTML cuyo contenido recupera en tiempo de ejecución. Si esto no es posible, asegúrese de que los datos están codificados con JavaScript. La codificación de JavaScript toma caracteres peligrosos para JavaScript y los reemplaza por su hexadecimal, por ejemplo, &lt; se codificaría como `\u003C` .

5. Antes de colocar los datos que no son de confianza en una cadena de consulta de dirección URL, asegúrese de que su dirección URL está codificada.

## <a name="html-encoding-using-no-locrazor"></a>Codificación HTML mediante Razor

El Razor motor usado en MVC codifica automáticamente todas las salidas que se han generado a partir de variables, a menos que trabaje realmente difícil para evitar que lo haga. Usa reglas de codificación de atributos HTML siempre que se usa la *@* Directiva. Como la codificación de atributos HTML es un supraconjunto de codificación HTML, esto significa que no tiene que preocuparse de si debe usar la codificación HTML o la codificación de atributos HTML. Debe asegurarse de que solo usa @ en un contexto HTML, no cuando intenta insertar una entrada que no es de confianza directamente en JavaScript. Las aplicaciones auxiliares de etiquetas también codificarán la entrada que se usa en los parámetros de etiqueta.

Tome la siguiente Razor vista:

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

Esta vista genera el contenido de la variable *untrustedInput* . Esta variable incluye algunos caracteres que se usan en ataques XSS, es decir &lt; , "y" &gt; . Al examinar el origen se muestra la salida representada como:

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> ASP.NET Core MVC proporciona una `HtmlString` clase que no se codifica automáticamente en la salida. Nunca debe usarse en combinación con una entrada que no sea de confianza, ya que esto expone una vulnerabilidad de XSS.

## <a name="javascript-encoding-using-no-locrazor"></a>Codificación de JavaScript mediante Razor

Puede haber ocasiones en las que desee insertar un valor en JavaScript para procesarlo en la vista. Existen dos formas de hacerlo. La forma más segura de insertar valores es colocar el valor en un atributo de datos de una etiqueta y recuperarlo en el código JavaScript. Por ejemplo:

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

El marcado anterior genera el siguiente código HTML:

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

El código anterior genera el siguiente resultado:

```
<script>alert(1)</script>
<script>alert(1)</script>
<script>alert(1)</script>
```

>[!WARNING]
> No concatene la entrada que ***no*** es de confianza en JavaScript para crear elementos DOM o usar `document.write()` en contenido generado dinámicamente.
>
> Use uno de los métodos siguientes para evitar que el código se exponga a XSS basado en DOM:
> * `createElement()` y asignan valores de propiedad con métodos o propiedades adecuados, como `node.textContent=` o nodo. InnerText = '.
> * `document.CreateTextNode()` y agréguelo en la ubicación del DOM adecuada.
> * `element.SetAttribute()`
> * `element[attribute]=`

## <a name="accessing-encoders-in-code"></a>Obtener acceso a codificadores en el código

Los codificadores HTML, JavaScript y URL están disponibles para el código de dos maneras: puede inyectarlos a través de la [inserción de dependencias](xref:fundamentals/dependency-injection) o puede usar los codificadores predeterminados contenidos en el `System.Text.Encodings.Web` espacio de nombres. Si usa los codificadores predeterminados, los que aplique a los intervalos de caracteres que se tratarán como seguros no surtirán efecto: los codificadores predeterminados usan las reglas de codificación más seguras posibles.

Para usar los codificadores configurables a través de DI, los constructores deben tomar un parámetro *HtmlEncoder*, *JavaScriptEncoder* y *UrlEncoder* , según corresponda. Por ejemplo:

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

## <a name="encoding-url-parameters"></a>Parámetros de URL de codificación

Si desea crear una cadena de consulta de dirección URL con una entrada que no sea de confianza como valor, use `UrlEncoder` para codificar el valor. Por ejemplo,

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

Después de la codificación, la variable encodedValue contendrá `%22Quoted%20Value%20with%20spaces%20and%20%26%22` . Los espacios, las comillas, los signos de puntuación y otros caracteres no seguros se codificarán por porcentaje en su valor hexadecimal; por ejemplo, un carácter de espacio se convertirá en %20.

>[!WARNING]
> No utilice la entrada que no sea de confianza como parte de una ruta de dirección URL. Pase siempre la entrada que no sea de confianza como un valor de cadena de consulta.

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a>Personalización de los codificadores

De forma predeterminada, los codificadores usan una lista segura limitada al intervalo de Unicode latín básico y codifican todos los caracteres que se encuentran fuera de ese intervalo como sus equivalentes en el código de carácter. Este comportamiento también afecta a Razor la representación de TagHelper y HtmlHelper, ya que usará los codificadores para generar las cadenas.

La razón de esto es proteger frente a errores desconocidos o futuros del explorador (los errores anteriores del explorador se han recorrido en el análisis en función del procesamiento de caracteres que no estén en inglés). Si el sitio web hace un uso intensivo de caracteres no latinos, como chino, cirílico u otros, es probable que no sea el comportamiento deseado.

Puede personalizar las listas seguras del codificador para incluir los intervalos Unicode adecuados para la aplicación durante el inicio, en `ConfigureServices()` .

Por ejemplo, si usa la configuración predeterminada, puede usar una Razor HtmlHelper como tal;

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

Al ver el origen de la página web, verá que se ha representado como sigue, con el texto en chino codificado.

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

Para ampliar los caracteres que el codificador trata como seguros, debe insertar la siguiente línea en el `ConfigureServices()` método de `startup.cs` ;

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

Este ejemplo amplía la lista segura para incluir el intervalo Unicode CjkUnifiedIdeographs. La salida representada se convertirá ahora en

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

Los intervalos de la lista segura se especifican como gráficos de códigos Unicode, no como lenguajes. El [estándar Unicode](https://unicode.org/) tiene una lista de [gráficos de código](https://www.unicode.org/charts/index.html) que puede usar para buscar el gráfico que contiene los caracteres. Cada codificador, HTML, JavaScript y URL, debe configurarse por separado.

> [!NOTE]
> La personalización de la lista segura solo afecta a los codificadores que tienen el origen a través de DI. Si obtiene acceso directamente a un codificador a través `System.Text.Encodings.Web.*Encoder.Default` de, se usará la configuración de la base de datos de solo latín básica básica.

## <a name="where-should-encoding-take-place"></a>¿Dónde se debe realizar la codificación?

La práctica aceptada general es que la codificación se realiza en el punto de salida y los valores codificados nunca deben almacenarse en una base de datos. La codificación en el punto de salida permite cambiar el uso de datos, por ejemplo, de HTML a un valor de cadena de consulta. También permite realizar búsquedas fácilmente en los datos sin tener que codificar valores antes de realizar búsquedas y permite aprovechar las ventajas de los cambios o las correcciones de errores que se realicen en los codificadores.

## <a name="validation-as-an-xss-prevention-technique"></a>Validación como técnica de prevención de XSS

La validación puede ser una herramienta útil para limitar los ataques XSS. Por ejemplo, una cadena numérica que solo contenga los caracteres 0-9 no desencadenará un ataque XSS. La validación es más complicada al aceptar HTML en los datos proporcionados por el usuario. El análisis de la entrada HTML es difícil, si no imposible. Markdown, junto con un analizador que elimina el código HTML incrustado, es una opción más segura para aceptar entradas enriquecidas. Nunca se basa solo en la validación. Codifique siempre la entrada que no sea de confianza antes de la salida, independientemente de la validación o la limpieza que se haya realizado.
