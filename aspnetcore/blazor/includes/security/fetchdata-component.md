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
ms.openlocfilehash: 8772f383a830936881564ca95a7f034ba08a5798
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552941"
---
El componente `FetchData` muestra:

* Cómo aprovisionar un token de acceso.
* Cómo usar el token de acceso para llamar a una API de recursos protegidos en la aplicación *Server*.

La directiva [`@attribute [Authorize]`](xref:mvc/views/razor#attribute) indica al sistema de autorización de Blazor WebAssembly que el usuario debe estar autorizado para poder visitar este componente. La presencia del atributo en la aplicación `Client` no impide que se llame a la API en el servidor sin credenciales adecuadas. La aplicación `Server` debe usar también `[Authorize]` en los puntos de conexión adecuados para protegerlos correctamente.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> se encarga de solicitar un token de acceso que se puede agregar a la solicitud para llamar a la API. Si el token se almacena en caché o el servicio puede aprovisionar un nuevo token de acceso sin la interacción del usuario, la solicitud de token se realiza correctamente. De lo contrario, se produce un error en la solicitud de token con una <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>, que se detecta en una instrucción [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch).

Para obtener el token real que se va a incluir en la solicitud, la aplicación debe comprobar que la solicitud se ha realizado correctamente mediante una llamada a [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A).

Si la solicitud se ha realizado correctamente, la variable de token se rellena con el token de acceso. La propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> del token expone la cadena literal que se va a incluir en el encabezado de solicitud `Authorization`.

Si se produjo un error en la solicitud porque no se pudo aprovisionar el token sin la interacción del usuario, el resultado del token contiene una dirección URL de redireccionamiento. Al navegar a esta dirección URL, el usuario se dirige a la página de inicio de sesión y vuelve a la página actual si se autentica correctamente.

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
