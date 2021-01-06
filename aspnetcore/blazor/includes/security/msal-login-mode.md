El marco tiene como valor predeterminado el modo de inicio de sesión emergente y vuelve al modo de inicio de sesión de redireccionamiento si no se puede abrir un elemento emergente. Configure MSAL para usar el modo de inicio de sesión de redireccionamiento mediante el establecimiento de la propiedad `LoginMode` de <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> en `redirect`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

La configuración predeterminada es `popup`y el valor de cadena no distingue entre mayúsculas y minúsculas.
