# <a name="response-compression-sample-application-aspnet-core-3x"></a>Aplicación de ejemplo de compresión de respuesta (ASP.NET Core 3. x)

En este ejemplo se muestra el uso de middleware de compresión de respuesta de ASP.NET Core 3. x para comprimir las respuestas HTTP. En el ejemplo se muestran los proveedores de compresión personalizados, Brotli y gzip para las respuestas de texto e imagen, y se muestra cómo agregar un tipo MIME para la compresión. Para obtener el ejemplo ASP.NET Core 2. x, vea [aplicación de ejemplo de compresión de respuesta (ASP.net Core 2. x)](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/response-compression/samples/2.x).

## <a name="examples-in-this-sample"></a>Ejemplos que se incluyen

* `BrotliCompressionProvider`
  * `text/plain`
    * **/** -Lorem ipsum respuesta del archivo de texto en 2.044 bytes que comprime hasta ~ 979 bytes.
    * **/testfile1kb.txt** -respuesta del archivo de texto en 1.033 bytes que comprime hasta ~ 36 bytes.
    * **/Trickle** : respuesta emitida como caracteres individuales a intervalos de 1 segundo.
* `GzipCompressionProvider`
  * `text/plain`
    * **/** -Lorem ipsum respuesta del archivo de texto en 2.044 bytes que comprime hasta ~ 927 bytes.
    * **/testfile1kb.txt** -respuesta del archivo de texto en 1.033 bytes que comprime hasta ~ 47 bytes.
    * **/Trickle** : respuesta emitida como caracteres individuales a intervalos de 1 segundo.
  * `image/svg+xml`
    * **/banner.svg** : respuesta de imagen de gráficos vectoriales escalables (SVG) en 9.707 bytes que comprime hasta ~ 4.459 bytes.
* `CustomCompressionProvider`<br>Muestra cómo implementar un proveedor de compresión personalizado para su uso con el middleware.

Cuando la solicitud incluye el `Accept-Encoding` encabezado y la compresión de respuesta se realiza correctamente, el middleware agrega automáticamente un `Vary: Accept-Encoding` encabezado a la respuesta. El `Vary` encabezado indica a las memorias caché que conserven varias copias de la respuesta en función de valores alternativos de `Accept-Encoding` , por lo que tanto una versión comprimida (gzip o Brotli) como una versión sin comprimir se almacenan en cachés para los sistemas que pueden aceptar la respuesta comprimida o sin comprimir.

## <a name="use-the-sample"></a>Usar el ejemplo

1. Realice una solicitud con [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)o [Postman](https://www.getpostman.com/) a la aplicación sin un `Accept-Encoding` encabezado y anote la carga de respuesta, el tamaño de respuesta y los encabezados de respuesta.
1. Agregue un `Accept-Encoding: br` `Accept-Encoding: gzip` encabezado o y anote el tamaño de la respuesta comprimida y los encabezados de respuesta. El tamaño de la respuesta desciende y el `Content-Encoding` encabezado de respuesta lo incluye el middleware que indica que se ha producido la compresión con gzip o Brotli. Cuando examine el cuerpo de respuesta de la respuesta Lorem ipsum o **testfile1kb.txt** , verá que el texto está comprimido e ilegible.
1. Agregue un `Accept-Encoding: mycustomcompression` encabezado y anote los encabezados de respuesta. `CustomCompressionProvider`Es una implementación vacía que no comprime realmente la respuesta, pero puede crear un contenedor de secuencias de compresión personalizado para el `CreateStream()` método.
