<span data-ttu-id="1241d-101">El marco tiene como valor predeterminado el modo de inicio de sesión emergente y vuelve al modo de inicio de sesión de redireccionamiento si no se puede abrir un elemento emergente.</span><span class="sxs-lookup"><span data-stu-id="1241d-101">The framework defaults to pop-up login mode and falls back to redirect login mode if a pop-up can't be opened.</span></span> <span data-ttu-id="1241d-102">Configure MSAL para usar el modo de inicio de sesión de redireccionamiento mediante el establecimiento de la propiedad `LoginMode` de <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> en `redirect`:</span><span class="sxs-lookup"><span data-stu-id="1241d-102">Configure MSAL to use redirect login mode by setting the `LoginMode` property of <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> to `redirect`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

<span data-ttu-id="1241d-103">La configuración predeterminada es `popup`y el valor de cadena no distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="1241d-103">The default setting is `popup`, and the string value isn't case sensitive.</span></span>
