Los finalizadores HTTP son similares a los encabezados HTTP, salvo que se envían después de enviar el cuerpo de la respuesta. Para IIS y HTTP.sys, solo se admiten los finalizadores de respuesta HTTP/2.

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

En el código de ejemplo anterior:

* `SupportsTrailers` garantiza que los finalizadores se admitan para la respuesta.
* `DeclareTrailer` agrega el nombre del finalizador dado al encabezado de respuesta `Trailer`. Declarar los finalizadores de una respuesta es opcional, pero se recomienda. Si se llama a `DeclareTrailer`, debe ser antes de que se envíen los encabezados de respuesta.
* `AppendTrailer` anexa el finalizador.
