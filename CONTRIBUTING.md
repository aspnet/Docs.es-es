# <a name="contribute-to-the-aspnet-core-documentation"></a>Colaboración en la documentación de ASP.NET Core

En este documento se describe el proceso para colaborar en los artículos y ejemplos de código que se hospedan en el [sitio de documentación de ASP.NET](https://docs.microsoft.com/aspnet/). La corrección ortográfica y el envío de artículos nuevos son algunas de las colaboraciones que se aceptan.

## <a name="how-to-make-a-simple-correction-or-suggestion"></a>Cómo hacer una corrección o sugerencia sencilla

Los artículos se almacenan en el repositorio como archivos Markdown. Para realizar cambios sencillos en el contenido de un archivo Markdown en el explorador, seleccione el vínculo **Editar** en la esquina superior derecha de la ventana del explorador. (En una ventana de explorador estrecha, expanda la barra **Opciones** para mostrar el vínculo **Editar**). Siga las instrucciones para crear una solicitud de incorporación de cambios (PR). Revisaremos la PR y la aceptaremos o sugeriremos cambios.

## <a name="how-to-make-a-more-complex-submission"></a>Cómo realizar un envío más complejo

Necesita tener conocimientos básicos de [Git y GitHub.com](https://guides.github.com/activities/hello-world/).

* Abra un [problema](https://github.com/dotnet/AspNetCore.Docs/issues/new) que describa qué quiere hacer, como cambiar un artículo existente o crear uno nuevo. A menudo solicita un esquema cuando se sugiere un nuevo tema. Espere la aprobación del equipo antes de dedicar mucho tiempo.
* Bifurque el repositorio [aspnet/Docs](https://github.com/dotnet/AspNetCore.Docs/) y cree una rama para los cambios.
* Envíe una PR al administrador con los cambios.
* Si la PR tiene asignada la etiqueta 'cla-required' [rellene el contrato de licencia de colaboración (CLA)](https://cla.dotnetfoundation.org/).
* Responder a los comentarios de la PR.

Para obtener un ejemplo en el que este proceso dio lugar a la publicación de un nuevo artículo, vea el [problema &num;67](https://github.com/dotnet/docs/issues/67) y la [solicitud de extracción &num;798](https://github.com/dotnet/docs/pull/798) en el repositorio .NET Docs. El nuevo artículo es [Documentar el código con comentarios XML](https://docs.microsoft.com/dotnet/articles/csharp/codedoc).

## <a name="docs-authoring-pack-extension-in-visual-studio-code"></a>Extensión Docs Authoring Pack en Visual Studio Code

Si usa Visual Studio Code para colaborar en la documentación de ASP.NET, instale la extensión [Docs Authoring Pack](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-authoring-pack) para mejorar la productividad. La extensión proporciona una variedad de herramientas con las que es más fácil realizar linting de Markdown, pasar el corrector ortográfico para el código y obtener plantillas de artículo.

## <a name="markdown-syntax"></a>Sintaxis de Markdown

Los artículos se escriben en [DocFx flavored Markdown](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), que es un superconjunto de [GitHub flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/). Para obtener ejemplos de sintaxis DFM para las características de interfaz de usuario usadas en la documentación de ASP.NET, vea [Metadata and Markdown Template](https://github.com/dotnet/docs/blob/master/styleguide/template.md) (Metadatos y plantillas de Markdown) en la guía de estilo del repositorio .NET Docs. 

## <a name="folder-structure-conventions"></a>Convenciones de estructura de carpetas

Para cada archivo con Markdown, puede haber una carpeta para las imágenes y una carpeta para el código de ejemplo. Si el artículo es [fundamentals/configuration/index.md](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/fundamentals/configuration/index.md), las imágenes se encuentran en [Fundamentos/configuration/índice/\_static](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/_static) y los archivos de proyecto de aplicación de ejemplo están en [fundamentals/configuration/index/sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/sample). Una imagen en el archivo *fundamentals/configuration/index.md* se representa mediante el siguiente código Markdown:

```md
![description of image for alt attribute](configuration/index/_static/imagename.png)
```

Todas las imágenes deben tener [texto alternativo (alt)](https://wikipedia.org/wiki/Alt_attribute). Para obtener consejos sobre cómo especificar el texto alternativo, eche un vistazo a recursos en línea, como [WebAIM: Alternative Text](https://webaim.org/techniques/alttext/) (WebAIM: Texto alternativo).

Use minúsculas para nombres de archivo Markdown y nombres de archivo de imagen.

## <a name="internal-links"></a>Vínculos internos

Los vínculos internos deben usar el `uid` del artículo de destino con un vínculo xref (el texto del vínculo se determina con título del contenido vinculado):

```md
<xref:uid_of_the_topic>
```

Si el título del artículo no es adecuado para el texto del vínculo (por ejemplo, el texto del vínculo es una palabra o frase en una oración), especifique el vínculo xref y el texto del vínculo con lo siguiente:

```md
[link text](xref:uid_of_the_topic)
```

Para más información, vea [DocFX Cross Reference](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#cross-reference) (Referencias cruzadas de DocFX).

## <a name="images-and-screenshots"></a>Imágenes y capturas de pantalla

No incluya imágenes con los artículos, excepto:

* En los tutoriales de incorporación básicos (de principiante).
* Cuando se necesite una imagen para mayor claridad.

Estas restricciones reducen el tamaño del repositorio.

Como paso opcional, asegúrese de que todas las imágenes y capturas de pantalla que se usan en la documentación están comprimidas, lo que contribuye al tamaño del archivo y mejora el rendimiento de la carga de página. Algunas herramientas populares incluyen TinyPNG (mediante el [sitio web de TinyPNG](https://tinypng.com/) o la [API de TinyPNG](https://tinypng.com/developers)) o la extensión [Optimizador de imagen](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ImageOptimizer) de Visual Studio. 

## <a name="code-snippets"></a>Fragmentos de código

Los artículos suelen contener fragmentos de código para ilustrar el contenido. DFM permite copiar código en el archivo Markdown o hacer referencia a un archivo de código independiente. Es preferible usar archivos de código independientes, siempre que sea posible, para minimizar la posibilidad de errores en el código. Los archivos de código se almacenan en el repositorio con la estructura de carpetas que se ha descrito antes para proyectos de ejemplo. 

En estos ejemplos se ilustra la [sintaxis de fragmento de código DFM](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet) para usarla en un archivo *configuration/index.md*.

Para representar un archivo de código completo como un fragmento de código:

```md
[!code-csharp[](configuration/index/sample/Program.cs)]
```

Para representar una parte de un archivo como un fragmento de código mediante el uso de números de línea:

```md
[!code-csharp[](configuration/index/sample/Program.cs?range=1-10,20,30,40-50]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=1-10,20,30,40-50]
```

Para fragmentos de código en C#, haga referencia a una [región de C#](https://docs.microsoft.com/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region). Siempre que sea posible, use regiones en lugar de números de línea porque los números de línea en un archivo de código tienden a cambiar y dejan de estar sincronizados con las referencias de números de línea en Markdown. Las regiones de C# se pueden anidar. Si se hace referencia a la región externa, las directivas internas `#region` y `#endregion` no se representan en un fragmento de código. 

Para representar una región de C# denominada "snippet_Example":

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example)]
```

Para resaltar las líneas seleccionadas en un fragmento representado (normalmente se representa con el color de fondo amarillo):

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
[!code-csharp[](configuration/index/sample/Program.cs?range=10-20&highlight=1-3]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=10-20&highlight=1-3]
[!code-javascript[](configuration/index/sample/UsingOptionsSample.csproj?range=10-20&highlight=1-3]
```

## <a name="test-changes-with-docfx"></a>Probar cambios con DocFX

Pruebe los cambios con la [herramienta de línea de comandos DocFX](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), que crea una versión del sitio hospedada localmente. DocFX no representa las extensiones de sitio y de estilo creadas para docs.microsoft.com.

DocFX necesita:

* .NET Framework en Windows.
* Mono para Linux o macOS. 

### <a name="windows-instructions"></a>Instrucciones para Windows

* Descargue y descomprima *docfx.zip* desde [versiones de DocFX](https://github.com/dotnet/docfx/releases).
* Agregue DocFX a la ruta de acceso.
* En un shell de comandos, vaya a la carpeta que contiene el archivo *docfx.json* (*aspnet* para el contenido de ASP.NET o *aspnetcore* para el contenido de ASP.NET Core) y ejecute este comando:

  ```console
  docfx --serve
  ```

* En un navegador, vaya a `http://localhost:8080/group1-dest/`.

### <a name="mono-instructions"></a>Instrucciones para Mono

* Instale Mono a través de Homebrew:

  ```console
  brew install mono
  ```

* Descargue la [versión más reciente de DocFX](https://github.com/dotnet/docfx/releases).
* Extraiga el archivo en *$HOME/bin/docfx*.
* Cree un par de alias para **docfx** en un shell de Bash. El primer alias se usa para generar la documentación. El segundo alias se usa para crear y publicar la documentación.

  ```console
  alias docfx='mono $HOME/bin/docfx/docfx.exe'
  alias docfx-serve='mono $HOME/bin/docfx/docfx.exe --serve'
  ```

* En un shell de comandos, vaya a la carpeta que contiene el archivo *docfx.json* (*aspnet* para el contenido de ASP.NET o *aspnetcore* para el contenido de ASP.NET Core) y ejecute este comando para generar y servir a los documentos mediante su alias:

  ```console
  docfx-serve
  ```

* En un navegador, vaya a `http://localhost:8080/group1-dest/`.

## <a name="voice-and-tone"></a>Voz y tono

Nuestro objetivo consiste en escribir documentación que sea fácil de comprender para el público más amplio posible. Con ese fin, hemos establecido directrices para escribir el estilo que queremos que sigan nuestros colaboradores. Para más información, vea [Voice and tone guidelines](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) (Directrices de voz y tono) en el repositorio de .NET.

## <a name="microsoft-writing-style-guide"></a>Guía de estilo de redacción de Microsoft

La [Guía de estilo de redacción de Microsoft](https://docs.microsoft.com/style-guide/welcome/) sirve de guía en cuanto a terminología y el estilo de redacción para las comunicaciones de cualquier tecnología, incluida la documentación de ASP.NET Core.

## <a name="redirects"></a>Redireccionamientos

Si elimina un artículo, cambie su nombre de archivo o muévalo a otra carpeta, cree un redireccionamiento para que las personas que hayan marcado el artículo no reciban un error *404 No encontrado*. Agregue redirecciones al [archivo de redireccionamiento principal](https://github.com/dotnet/AspNetCore.Docs/blob/master/.openpublishing.redirection.json).
