<span data-ttu-id="9279e-101">Cuando se trabaja con una API de servidor registrada en AAD y el registro de AAD de la aplicación se encuentra en un inquilino que se basa en un [dominio de publicador no comprobado](/azure/active-directory/develop/howto-configure-publisher-domain), el URI del identificador de la aplicación de la API del servidor no es `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` sino que, en su lugar, tiene el formato `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}`.</span><span class="sxs-lookup"><span data-stu-id="9279e-101">When working with a server API registered with AAD and the app's AAD registration is in an tenant that relies on an [unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain), the App ID URI of your server API app isn't `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` but instead is in the format `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}`.</span></span> <span data-ttu-id="9279e-102">Si ese es el caso, el ámbito del token de acceso predeterminado en `Program.Main` (`Program.cs`) de la aplicación *`Client`* es similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="9279e-102">If that's the case, the default access token scope in `Program.Main` (`Program.cs`) of the *`Client`* app appears similar to the following:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

<span data-ttu-id="9279e-103">Para configurar la aplicación de API de servidor para una audiencia coincidente, establezca el valor `Audience` en el archivo de configuración de la aplicación de API *`Server`* (`appsettings.json`) para que coincida con la audiencia de la aplicación proporcionada por Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="9279e-103">To configure the server API app for a matching audience, set the `Audience` in the *`Server`* API app settings file (`appsettings.json`) to match the app's audience provided by the Azure portal:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "ValidateAuthority": true,
    "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
  }
}
```

<span data-ttu-id="9279e-104">En la configuración anterior, el final del valor `Audience` **no** incluye el ámbito predeterminado `/{DEFAULT SCOPE}`.</span><span class="sxs-lookup"><span data-stu-id="9279e-104">In the preceding configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`.</span></span>

<span data-ttu-id="9279e-105">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9279e-105">Example:</span></span>

<span data-ttu-id="9279e-106">`Program.Main` (`Program.cs`) de la aplicación *`Client`* :</span><span class="sxs-lookup"><span data-stu-id="9279e-106">`Program.Main` (`Program.cs`) of the *`Client`* app:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

<span data-ttu-id="9279e-107">Configure el archivo de configuración de la aplicación de API *`Server`* (`appsettings.json`) con una audiencia correspondiente (`Audience`):</span><span class="sxs-lookup"><span data-stu-id="9279e-107">Configure the *`Server`* API app settings file (`appsettings.json`) with a matching audience (`Audience`):</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true,
    "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

<span data-ttu-id="9279e-108">En el ejemplo anterior, el final del valor `Audience` **no** incluye el ámbito predeterminado `/API.Access`.</span><span class="sxs-lookup"><span data-stu-id="9279e-108">In the preceding example, the end of the `Audience` value does **not** include the default scope `/API.Access`.</span></span>
