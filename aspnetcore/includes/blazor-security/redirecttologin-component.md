El componente `RedirectToLogin` (`Shared/RedirectToLogin.razor`):

* Administra el redireccionamiento de usuarios no autorizados a la página de inicio de sesión.
* Conserva la dirección URL actual a la que el usuario intenta tener acceso para que pueda volver a esa página si la autenticación se realiza correctamente.

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(
            $"authentication/login?returnUrl={Uri.EscapeDataString(Navigation.Uri)}");
    }
}
```
