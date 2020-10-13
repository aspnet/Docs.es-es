<span data-ttu-id="e4277-101">Un restablecimiento permite que el servidor restablezca una solicitud HTTP/2 con un código de error especificado.</span><span class="sxs-lookup"><span data-stu-id="e4277-101">Reset allows for the server to reset a HTTP/2 request with a specified error code.</span></span> <span data-ttu-id="e4277-102">Una solicitud de restablecimiento se considera una anulación.</span><span class="sxs-lookup"><span data-stu-id="e4277-102">A reset request is considered aborted.</span></span>

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

<span data-ttu-id="e4277-103">En el ejemplo de código anterior, `Reset` especifica el código de error de `INTERNAL_ERROR`.</span><span class="sxs-lookup"><span data-stu-id="e4277-103">`Reset` in the preceding code example specifies the `INTERNAL_ERROR` error code.</span></span> <span data-ttu-id="e4277-104">Para obtener más información sobre los códigos de error HTTP/2, visite la [sección de código de error de especificación de HTTP/2](https://tools.ietf.org/html/rfc7540#page-50).</span><span class="sxs-lookup"><span data-stu-id="e4277-104">For more information about HTTP/2 error codes, visit the [HTTP/2 specification error code section](https://tools.ietf.org/html/rfc7540#page-50).</span></span>