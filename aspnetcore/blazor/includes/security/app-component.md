El componente `App` (`App.razor`) es similar al componente `App` que se encuentra en las aplicaciones Blazor Server:

* El componente <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> administra la exposición de <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> al resto de la aplicación.
* El componente <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> se asegura de que el usuario actual está autorizado para tener acceso a una página determinada o, de lo contrario, representa el componente `RedirectToLogin`.
* El componente `RedirectToLogin` administra el redireccionamiento de usuarios no autorizados a la página de inicio de sesión.

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    @if (!context.User.Identity.IsAuthenticated)
                    {
                        <RedirectToLogin />
                    }
                    else
                    {
                        <p>
                            You are not authorized to access 
                            this resource.
                        </p>
                    }
                </NotAuthorized>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

[!include[](../prefer-exact-matches.md)]
