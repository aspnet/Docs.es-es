<span data-ttu-id="f4ba4-101">El componente `FetchData` muestra:</span><span class="sxs-lookup"><span data-stu-id="f4ba4-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="f4ba4-102">Cómo aprovisionar un token de acceso.</span><span class="sxs-lookup"><span data-stu-id="f4ba4-102">Provision an access token.</span></span>
* <span data-ttu-id="f4ba4-103">Cómo usar el token de acceso para llamar a una API de recursos protegidos en la aplicación *Server*.</span><span class="sxs-lookup"><span data-stu-id="f4ba4-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="f4ba4-104">La directiva [`@attribute [Authorize]`](xref:mvc/views/razor#attribute) indica al sistema de autorización de WebAssembly de Blazor que el usuario debe estar autorizado para visitar este componente.</span><span class="sxs-lookup"><span data-stu-id="f4ba4-104">The [`@attribute [Authorize]`](xref:mvc/views/razor#attribute) directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="f4ba4-105">La presencia del atributo en la aplicación `Client` no impide que se llame a la API en el servidor sin credenciales adecuadas.</span><span class="sxs-lookup"><span data-stu-id="f4ba4-105">The presence of the attribute in the `Client` app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="f4ba4-106">La aplicación `Server` debe usar también `[Authorize]` en los puntos de conexión adecuados para protegerlos correctamente.</span><span class="sxs-lookup"><span data-stu-id="f4ba4-106">The `Server` app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="f4ba4-107"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> se encarga de solicitar un token de acceso que se puede agregar a la solicitud para llamar a la API.</span><span class="sxs-lookup"><span data-stu-id="f4ba4-107"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="f4ba4-108">Si el token se almacena en caché o el servicio puede aprovisionar un nuevo token de acceso sin la interacción del usuario, la solicitud de token se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="f4ba4-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="f4ba4-109">De lo contrario, se produce un error en la solicitud de token con una <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>, que se detecta en una instrucción [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch).</span><span class="sxs-lookup"><span data-stu-id="f4ba4-109">Otherwise, the token request fails with an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>, which is caught in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>

<span data-ttu-id="f4ba4-110">Para obtener el token real que se va a incluir en la solicitud, la aplicación debe comprobar que la solicitud se ha realizado correctamente mediante una llamada a [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A).</span><span class="sxs-lookup"><span data-stu-id="f4ba4-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A).</span></span>

<span data-ttu-id="f4ba4-111">Si la solicitud se ha realizado correctamente, la variable de token se rellena con el token de acceso.</span><span class="sxs-lookup"><span data-stu-id="f4ba4-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="f4ba4-112">La propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> del token expone la cadena literal que se va a incluir en el encabezado de solicitud `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="f4ba4-112">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="f4ba4-113">Si se produjo un error en la solicitud porque no se pudo aprovisionar el token sin la interacción del usuario, el resultado del token contiene una dirección URL de redireccionamiento.</span><span class="sxs-lookup"><span data-stu-id="f4ba4-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="f4ba4-114">Al navegar a esta dirección URL, el usuario se dirige a la página de inicio de sesión y vuelve a la página actual si se autentica correctamente.</span><span class="sxs-lookup"><span data-stu-id="f4ba4-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using {APP NAMESPACE}.Shared
@attribute [Authorize]
@inject HttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```
