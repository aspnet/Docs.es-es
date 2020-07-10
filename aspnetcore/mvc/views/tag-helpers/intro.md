---
title: Asistentes de etiquetas en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre qué son los asistentes de etiquetas y cómo se usan en ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 03/18/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/intro
ms.openlocfilehash: d2791422e50082eab51c0735e2209e5b829d22f2
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212502"
---
# <a name="tag-helpers-in-aspnet-core"></a>Asistentes de etiquetas en ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="what-are-tag-helpers"></a>Qué son los asistentes de etiquetas

Las aplicaciones auxiliares de etiquetas permiten que el código de servidor participe en la creación y la representación de elementos HTML en archivos de Razor. Por ejemplo, la aplicación auxiliar `ImageTagHelper` integrada puede anexar un número de versión al nombre de imagen. Cada vez que la imagen cambia, el servidor genera una nueva versión única para la imagen, lo que garantiza que los clientes puedan obtener la imagen actual (en lugar de una imagen obsoleta almacenada en caché). Hay muchos asistentes de etiquetas integradas para tareas comunes (como la creación de formularios, vínculos, carga de activos, etc.) y existen muchos más a disposición en repositorios públicos de GitHub y como paquetes NuGet. Los asistentes de etiquetas se crean en C# y tienen como destino elementos HTML en función del nombre de elemento, el nombre de atributo o la etiqueta principal. Por ejemplo, la aplicación auxiliar `LabelTagHelper` integrada puede tener como destino el elemento HTML `<label>` cuando se aplican atributos `LabelTagHelper`. Si está familiarizado con las [aplicaciones auxiliares HTML](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), las aplicaciones auxiliares de etiquetas reducen las transiciones explícitas entre HTML y C# en las Razor vistas. En muchos casos, los asistentes de HTML proporcionan un método alternativo para un asistente de etiquetas específico, pero es importante tener en cuenta que los asistentes de etiquetas no reemplazan a los asistentes de HTML y que no hay un asistente de etiquetas para cada asistente de HTML. En [Comparación entre los asistentes de etiquetas y los asistentes de HTML](#tag-helpers-compared-to-html-helpers) se explican las diferencias con más detalle.

## <a name="what-tag-helpers-provide"></a>¿Qué proporcionan los asistentes de etiquetas?

**Una experiencia de desarrollo compatible con HTML** En su mayor parte, el Razor marcado con aplicaciones auxiliares de etiquetas es similar a HTML estándar. Los diseñadores de front-end que no son conformes con HTML/CSS/JavaScript pueden editar Razor sin tener que aprender la sintaxis de C# Razor .

**Un completo entorno de IntelliSense para crear HTML y Razor marcado** esto está en contraste con las aplicaciones auxiliares HTML, el enfoque anterior para la creación del marcado en el servidor en las Razor vistas. En [Comparación entre los asistentes de etiquetas y los asistentes de HTML](#tag-helpers-compared-to-html-helpers) se explican las diferencias con más detalle. En [Compatibilidad de IntelliSense con asistentes de etiquetas](#intellisense-support-for-tag-helpers) se explica el entorno de IntelliSense. Incluso los desarrolladores con experiencia en Razor c# son más productivos mediante aplicaciones auxiliares de etiquetas que escribir marcado en c# Razor .

**Una forma de ser más productivo y generar código más sólido, confiable y fácil de mantener con información que solo está disponible en el servidor** Por ejemplo, lo habitual a la hora de actualizar las imágenes era cambiar el nombre de la imagen cuando se modificaba. Las imágenes debían almacenarse en caché de forma activa por motivos de rendimiento y, a menos que se cambiase el nombre de una imagen, se corría el riesgo de que los clientes obtuviesen una copia obsoleta. Antes, después de editar una imagen, era necesario cambiarle el nombre y actualizar todas las referencias a la imagen en la aplicación web. No solo se trata de una gran cantidad de trabajo, sino que también es propenso a errores (puede omitir una referencia, escribir accidentalmente la cadena equivocada, etc.). El integrado `ImageTagHelper` puede hacer esto automáticamente. `ImageTagHelper` puede anexar un número de versión al nombre de la imagen, por lo que cada vez que la imagen cambia, el servidor genera automáticamente una nueva versión única de la imagen. Esto garantiza que los clientes obtengan la imagen actual. Esta solidez y ahorro de trabajo se consiguen de forma gratuita mediante el uso de `ImageTagHelper`.

La mayoría de los asistentes de etiquetas integradas tienen como destino elementos HTML estándar y proporcionan atributos del lado servidor del elemento. Por ejemplo, el elemento `<input>` que muchas vistas usan en la carpeta *Views/Account* contiene el atributo `asp-for`. Este atributo extrae el nombre de la propiedad de modelo especificada en el HTML representado. Considere una Razor vista con el siguiente modelo:

```csharp
public class Movie
{
    public int ID { get; set; }
    public string Title { get; set; }
    public DateTime ReleaseDate { get; set; }
    public string Genre { get; set; }
    public decimal Price { get; set; }
}
```

El Razor marcado siguiente:

```cshtml
<label asp-for="Movie.Title"></label>
```

Se genera el siguiente código HTML:

```html
<label for="Movie_Title">Title</label>
```

El atributo `asp-for` está disponible por medio de la propiedad `For` en [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0). Vea [Creación de asistentes de etiquetas](xref:mvc/views/tag-helpers/authoring) para más información.

## <a name="managing-tag-helper-scope"></a>Administración del ámbito de los asistentes de etiquetas

El ámbito de los asistentes de etiquetas se controla mediante una combinación de `@addTagHelper`, `@removeTagHelper` y el carácter de exclusión "!".

<a name="add-helper-label"></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a>`@addTagHelper` hace que los asistentes de etiquetas estén disponibles

Si crea una aplicación web de ASP.NET Core denominada *AuthoringTagHelpers*, el siguiente archivo *Views/_ViewImports.cshtml* se agregará al proyecto:

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

La directiva `@addTagHelper` hace que los asistentes de etiquetas estén disponibles en la vista. En este caso, el archivo de vista es *Pages/_ViewImports.cshtml*, que heredan de forma predeterminada todos los archivos contenidos en la carpeta *Pages* y sus subcarpetas, lo que hace que los asistentes de etiquetas estén disponibles. El código anterior usa la sintaxis de comodines ("\*") para especificar que todas los asistentes de etiquetas del ensamblado especificado (*Microsoft.AspNetCore.Mvc.TagHelpers*) estarán disponibles para todos los archivos de vista del directorio o subdirectorio *Views*. El primer parámetro después de `@addTagHelper` especifica los asistentes de etiquetas que se van a cargar (usamos "\*" para todas los asistentes de etiquetas), y el segundo parámetro ("Microsoft.AspNetCore.Mvc.TagHelpers") especifica el ensamblado que contiene los asistentes de etiquetas. *Microsoft.AspNetCore.Mvc.TagHelpers* es el ensamblado para los asistentes de etiquetas integradas de ASP.NET Core.

Para exponer todas los asistentes de etiquetas de este proyecto (que crea un ensamblado denominado *AuthoringTagHelpers*), use lo siguiente:

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

Si el proyecto contiene un asistente `EmailTagHelper` con el espacio de nombres predeterminado (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), puede proporcionar el nombre completo (FQN) del asistente de etiquetas:

```cshtml
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

Para agregar un asistente de etiquetas a una vista con un FQN, agregue primero el FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) y, después, el nombre del ensamblado (*AuthoringTagHelpers*). La mayoría de los desarrolladores prefiere usar la sintaxis de comodines "\*". La sintaxis de comodines permite insertar el carácter comodín "\*" como sufijo en un FQN. Por ejemplo, cualquiera de las siguientes directivas incorporará `EmailTagHelper`:

```cshtml
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

Como se mencionó anteriormente, la adición de la `@addTagHelper` Directiva al archivo *views/_ViewImports. cshtml* hace que la aplicación auxiliar de etiquetas esté disponible para todos los archivos de vista del directorio *views* y los subdirectorios. Puede usar la directiva `@addTagHelper` en archivos de vista específicos si quiere exponer el asistente de etiquetas solo a esas vistas.

<a name="remove-razor-directives-label"></a>

### <a name="removetaghelper-removes-tag-helpers"></a>`@removeTagHelper` quita los asistentes de etiquetas

`@removeTagHelper` tiene los mismos parámetros que `@addTagHelper`, y quita un asistente de etiquetas que se ha agregado anteriormente. Por ejemplo, si se aplica `@removeTagHelper` a una vista específica, se quita de la vista el asistente de etiquetas especificada. Si se usa `@removeTagHelper` en un archivo *Views/Folder/_ViewImports.cshtml*, se quita el asistente de etiquetas especificada de todas las vistas de *Folder*.

### <a name="controlling-tag-helper-scope-with-the-_viewimportscshtml-file"></a>Controlar el ámbito del asistente de etiquetas con el archivo *_ViewImports.cshtml*

Si agrega un archivo *_ViewImports.cshtml* a cualquier carpeta de vistas, el motor de vistas aplica las directivas de ese archivo y del archivo *Views/_ViewImports.cshtml*. Si agregara un archivo *Views/Home/_ViewImports.cshtml* vacío para las vistas de *Home*, no se produciría ningún cambio porque el archivo *_ViewImports.cshtml* se suma. Todas las directivas `@addTagHelper` que agregue al archivo *Views/Home/_ViewImports.cshtml* (que no estén en el archivo predeterminado *Views/_ViewImports.cshtml*) expondrán esos asistentes de etiquetas únicamente a las vistas de la carpeta *Home*.

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a>Excluir elementos individuales

Puede deshabilitar un asistente de etiquetas en el nivel de elemento con el carácter de exclusión ("!") del asistente de etiquetas. Por ejemplo, la validación de `Email` se deshabilita en `<span>` con el carácter de exclusión del asistente de etiquetas:

```cshtml
<!span asp-validation-for="Email" class="text-danger"></!span>
```

Debe aplicar el carácter de exclusión del asistente de etiquetas en la etiqueta de apertura y de cierre. (El editor de Visual Studio agrega automáticamente el carácter de exclusión a la etiqueta de cierre cuando se agrega uno a la etiqueta de apertura). Después de agregar el carácter de exclusión, el elemento y los atributos del asistente de etiquetas ya no se muestran en una fuente distinta.

<a name="prefix-razor-directives-label"></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a>Usar `@tagHelperPrefix` para hacer explícito el uso del asistente de etiquetas

La directiva `@tagHelperPrefix` permite especificar una cadena de prefijo de etiqueta para habilitar la compatibilidad con el asistente de etiquetas y hacer explícito su uso. Por ejemplo, podría agregar el marcado siguiente al archivo *Views/_ViewImports.cshtml*:

```cshtml
@tagHelperPrefix th:
```

En la imagen de código siguiente, el prefijo del asistente de etiquetas se establece en `th:`, por lo que solo los elementos con el prefijo `th:` admiten asistentes de etiquetas (los elementos habilitados para asistentes de etiquetas tienen una fuente distinta). Los elementos `<label>` y `<input>` tienen el prefijo de los asistentes de etiquetas y están habilitados para estas, a diferencia del elemento `<span>`.

![image](intro/_static/thp.png)

Las mismas reglas de jerarquía que se aplican a `@addTagHelper` también se aplican a `@tagHelperPrefix`.

## <a name="self-closing-tag-helpers"></a>Asistentes de etiquetas de autocierre

Muchos asistentes de etiquetas no se pueden usar como etiquetas de autocierre. Algunos asistentes de etiquetas están diseñados para ser etiquetas de cierre. Si se usa un asistente de etiquetas que no se ha diseñado para ser de autocierre se suprime la salida representada. Si se usa el autocierre para un asistente de etiquetas, se genera una etiqueta de autocierre en la salida representada. Vea [esta nota](xref:mvc/views/tag-helpers/authoring#self-closing) en [Creación de asistentes de etiquetas](xref:mvc/views/tag-helpers/authoring) para más información.

## <a name="c-in-tag-helpers-attributedeclaration"></a>C# en el atributo/declaración de los asistentes de etiquetas 

Los asistentes de etiquetas no admiten C# en el área de declaraciones de etiquetas o atributos del elemento. Por ejemplo, el código siguiente no es válido:

```cshtml
<input asp-for="LastName"  
       @(Model?.LicenseId == null ? "disabled" : string.Empty) />
```

El código anterior se puede escribir como:

```cshtml
<input asp-for="LastName" 
       disabled="@(Model?.LicenseId == null)" />
```

## <a name="intellisense-support-for-tag-helpers"></a>Compatibilidad de IntelliSense con asistentes de etiquetas

Al crear una nueva ASP.NET Core aplicación web en Visual Studio, se agrega el paquete NuGet "Microsoft. AspNetCore. Razor . Herramientas ". Este es el paquete que agrega las herramientas de los asistentes de etiquetas.

Considere la posibilidad de escribir un elemento HTML `<label>`. En cuanto escriba `<l` en el editor de Visual Studio, IntelliSense mostrará elementos coincidentes:

![image](intro/_static/label.png)

No solo obtendrá ayuda HTML, sino el icono (el "@" symbol with "<>" situado debajo).

![image](intro/_static/tagSym.png)

Esto identifica el elemento como el destino de los asistentes de etiquetas. Los elementos HTML puros (como `fieldset`) muestran el icono "<>".

Una etiqueta HTML pura `<label>` muestra la etiqueta HTML (con el tema de color de Visual Studio predeterminado) en una fuente marrón, con los atributos en rojo y los valores de atributo en azul.

![image](intro/_static/LableHtmlTag.png)

Después de escribir `<label`, IntelliSense muestra los atributos HTML/CSS disponibles y los atributos destinados al asistente de etiquetas:

![image](intro/_static/labelattr.png)

La finalización de instrucciones de IntelliSense permite presionar la tecla TAB para completar la instrucción con el valor seleccionado:

![image](intro/_static/stmtcomplete.png)

En cuanto se especifica un atributo del asistente de etiquetas, las fuentes de las etiquetas y los atributos cambian. Si usa el tema de color predeterminado "Azul" o "Claro" de Visual Studio, la fuente es púrpura en negrita. Si usa el tema "Oscuro", la fuente es verde azulado en negrita. Las imágenes de este documento se han realizado con el tema predeterminado.

![image](intro/_static/labelaspfor2.png)

Puede usar el acceso directo *CompleteWord* de Visual Studio (el valor [predeterminado](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) es Ctrl+barra espaciadora) entre comillas dobles (""). Ahora se encuentra en C#, como si estuviera en una clase de C#. IntelliSense muestra todos los métodos y propiedades en el modelo de páginas. Los métodos y las propiedades están disponibles porque el tipo de propiedad es `ModelExpression`. En la imagen siguiente se edita la vista `Register`, por lo que `RegisterViewModel` está disponible.

![image](intro/_static/intellemail.png)

IntelliSense muestra las propiedades y los métodos disponibles para el modelo en la página. El entorno enriquecido de IntelliSense le ayuda a seleccionar la clase CSS:

![imagen](intro/_static/iclass.png)

![imagen](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a>Comparación entre los asistentes de etiquetas y los asistentes de HTML

Las aplicaciones auxiliares de etiquetas se asocian a elementos HTML en las Razor vistas, mientras que las [aplicaciones auxiliares html](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) se invocan como métodos intercalados con HTML en las Razor vistas. Considere el siguiente Razor marcado, que crea una etiqueta HTML con la clase CSS "Caption":

```cshtml
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

El símbolo de arroba ( `@` ) indica Razor que se trata del inicio del código. Los dos parámetros siguientes ("FirstName" y "First Name:") son cadenas, por lo que [IntelliSense](/visualstudio/ide/using-intellisense) no sirve de ayuda. El último argumento:

```cshtml
new {@class="caption"}
```

Es un objeto anónimo que se usa para representar atributos. Dado que `class` es una palabra clave reservada en C#, use el símbolo `@` para forzar a C# a interpretar `@class=` como un símbolo (nombre de propiedad). En el diseñador de front-end (alguien familiarizado con HTML/CSS/JavaScript y otras tecnologías de cliente, pero no está familiarizado con C# y Razor ), la mayor parte de la línea es externa. Es necesario crear toda la línea sin ayuda de IntelliSense.

Si usa `LabelTagHelper`, se puede escribir el mismo marcado de la manera siguiente:

```cshtml
<label class="caption" asp-for="FirstName"></label>
```

Con la versión del asistente de etiquetas, en cuanto escriba `<l` en el editor de Visual Studio, IntelliSense mostrará elementos coincidentes:

![image](intro/_static/label.png)

IntelliSense le ayuda a escribir toda la línea.

En la imagen de código siguiente se muestra la parte del formulario de la vista *views/Account/Register. cshtml* Razor generada a partir de la plantilla MVC de ASP.net 4.5. x incluida con Visual Studio.

![image](intro/_static/regCS.png)

En el editor de Visual Studio se muestra el código de C# con un fondo gris. Por ejemplo, el asistente de HTML `AntiForgeryToken`:

```cshtml
@Html.AntiForgeryToken()
```

Se muestra con un fondo gris. La mayor parte del marcado en la vista de registro es de C#. Compárelo con el método equivalente con asistentes de etiquetas:

![image](intro/_static/regTH.png)

El marcado es mucho más ordenado y más fácil de leer, modificar y mantener que en el método de asistentes de HTML. El código de C# se reduce a la cantidad mínima que el servidor necesita conocer. En el editor de Visual Studio se muestra el marcado de destino de un asistente de etiquetas en una fuente distinta.

Observe el grupo *Email*:

[!code-cshtml[](intro/sample/Register.cshtml?range=12-18)]

Cada uno de los atributos "asp-" tiene un valor de "Email", pero "Email" no es una cadena. En este contexto, "Email" es la propiedad de expresión del modelo de C# para `RegisterViewModel`.

El editor de Visual Studio le ayuda a escribir **todo** el marcado en el método del asistente de etiquetas del formulario de registro, mientras que Visual Studio no proporciona ninguna ayuda para la mayor parte del código en el método de asistentes de HTML. En [Compatibilidad de IntelliSense con asistentes de etiquetas](#intellisense-support-for-tag-helpers) se explica en detalle cómo trabajar con asistentes de etiquetas en el editor de Visual Studio.

## <a name="tag-helpers-compared-to-web-server-controls"></a>Comparación entre los asistentes de etiquetas y los controles de servidor web

* Los asistentes de etiquetas no poseen el elemento al que están asociadas; simplemente participan en la representación del elemento y el contenido. Los [controles de servidor web](https://msdn.microsoft.com/library/7698y1f0.aspx) de ASP.NET se declaran y se invocan en una página.

* Los [controles de servidor web](https://msdn.microsoft.com/library/zsyt68f1.aspx) tienen un ciclo de vida no trivial que puede dificultar el desarrollo y la depuración.

* Los controles de servidor web permiten agregar funcionalidad a los elementos Document Object Model (DOM) de cliente mediante el uso de un control cliente. Los asistentes de etiquetas no tienen ningún DOM.

* Los controles de servidor web incluyen la detección automática del explorador. Los asistentes de etiquetas no tienen conocimiento del explorador.

* Varios asistentes de etiquetas pueden actuar en el mismo elemento (vea [Evitar conflictos de asistentes de etiquetas](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts)), mientras que normalmente no se pueden crear controles de servidor web.

* Los asistentes de etiquetas pueden modificar la etiqueta y el contenido de los elementos HTML que tienen como ámbito, pero no modifican directamente ningún otro elemento de una página. Los controles de servidor web tienen un ámbito menos específico y pueden realizar acciones que afectan a otras partes de la página, lo que puede tener efectos secundarios imprevistos.

* Los controles de servidor web usan convertidores de tipos para convertir cadenas en objetos. Con los asistentes de etiquetas, trabajará de forma nativa en C#, por lo que no necesitará ninguna conversión de tipos.

* Los controles de servidor web usan [System.ComponentModel](/dotnet/api/system.componentmodel) para implementar el comportamiento de componentes y controles en tiempo de diseño y en tiempo de ejecución. `System.ComponentModel` incluye las clases base y las interfaces para implementar atributos y convertidores de tipos, enlazarlos con orígenes de datos y generar licencias para los componentes. Compare esto con los asistentes de etiquetas, que normalmente se derivan de `TagHelper`, y la clase base `TagHelper` solo expone dos métodos, `Process` y `ProcessAsync`.

## <a name="customizing-the-tag-helper-element-font"></a>Personalizar la fuente de elemento de asistentes de etiquetas

Puede personalizar la fuente y la coloración desde **herramientas**  >  **Opciones**  >  **entorno**  >  **fuentes y colores**:

![image](intro/_static/fontoptions2.png)

[!INCLUDE[](~/includes/built-in-TH.md)]

## <a name="additional-resources"></a>Recursos adicionales

* [Creación de asistentes de etiquetas](xref:mvc/views/tag-helpers/authoring)
* [Trabajar con formularios](xref:mvc/views/working-with-forms)
* [TagHelperSamples en GitHub](https://github.com/dpaquette/TagHelperSamples) contiene ejemplos de asistentes de etiquetas para trabajar con [Bootstrap](https://getbootstrap.com/).
