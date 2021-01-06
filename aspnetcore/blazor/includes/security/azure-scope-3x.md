Azure Portal proporciona uno de los siguientes formatos de URI de identificador de aplicación en función de si el inquilino de AAD tiene un [dominio de publicador comprobado o no comprobado](/azure/active-directory/develop/howto-configure-publisher-domain):

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

Cuando se usa el último de los dos URI de identificador de aplicación anteriores en la aplicación cliente para formar el URI de ámbito y la autorización no se realiza correctamente, intente proporcionar el URI de ámbito sin el esquema y el host:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

Ejemplo:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
