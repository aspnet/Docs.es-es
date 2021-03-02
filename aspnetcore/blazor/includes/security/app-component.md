---
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
ms.openlocfilehash: d632ab0604f81f7b6067d4535b0f5da0afe2e0ad
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552945"
---
<span data-ttu-id="ebe9a-101">El componente `App` (`App.razor`) es similar al componente `App` que se encuentra en las aplicaciones Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="ebe9a-101">The `App` component (`App.razor`) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="ebe9a-102">El componente <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> administra la exposición de <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> al resto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ebe9a-102">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component manages exposing the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> to the rest of the app.</span></span>
* <span data-ttu-id="ebe9a-103">El componente <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> se asegura de que el usuario actual está autorizado para tener acceso a una página determinada o, de lo contrario, representa el componente `RedirectToLogin`.</span><span class="sxs-lookup"><span data-stu-id="ebe9a-103">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="ebe9a-104">El componente `RedirectToLogin` administra el redireccionamiento de usuarios no autorizados a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="ebe9a-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

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
