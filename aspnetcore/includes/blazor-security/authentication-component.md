<span data-ttu-id="ebb2d-101">La página generada por el componente `Authentication` (`Pages/Authentication.razor`) define las rutas necesarias para controlar diferentes fases de autenticación.</span><span class="sxs-lookup"><span data-stu-id="ebb2d-101">The page produced by the `Authentication` component (`Pages/Authentication.razor`) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="ebb2d-102">El componente <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>:</span><span class="sxs-lookup"><span data-stu-id="ebb2d-102">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> component:</span></span>

* <span data-ttu-id="ebb2d-103">Se proporciona mediante el paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/).</span><span class="sxs-lookup"><span data-stu-id="ebb2d-103">Is provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span>
* <span data-ttu-id="ebb2d-104">Administra la realización de las acciones adecuadas en cada fase de autenticación.</span><span class="sxs-lookup"><span data-stu-id="ebb2d-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
