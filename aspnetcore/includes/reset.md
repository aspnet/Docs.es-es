Un restablecimiento permite que el servidor restablezca una solicitud HTTP/2 con un código de error especificado. Una solicitud de restablecimiento se considera una anulación.

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

En el ejemplo de código anterior, `Reset` especifica el código de error de `INTERNAL_ERROR`. Para obtener más información sobre los códigos de error HTTP/2, visite la [sección de código de error de especificación de HTTP/2](https://tools.ietf.org/html/rfc7540#page-50).