<span data-ttu-id="de97f-101">Azure Portal proporciona uno de los siguientes formatos de URI de identificador de aplicación en función de si el inquilino de AAD tiene un [dominio de publicador comprobado o no comprobado](/azure/active-directory/develop/howto-configure-publisher-domain):</span><span class="sxs-lookup"><span data-stu-id="de97f-101">The Azure portal provides one of the following App ID URI formats based on whether or not the AAD tenant has a [verified or unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain):</span></span>

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

<span data-ttu-id="de97f-102">Cuando se usa el último de los dos URI de identificador de aplicación anteriores en la aplicación cliente para formar el URI de ámbito y la autorización no se realiza correctamente, intente proporcionar el URI de ámbito sin el esquema y el host:</span><span class="sxs-lookup"><span data-stu-id="de97f-102">When the latter of the two preceding App ID URIs is used in the client app to form the scope URI and authorization isn't successful, try supplying the scope URI without the scheme and host:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

<span data-ttu-id="de97f-103">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="de97f-103">Example:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
