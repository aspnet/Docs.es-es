<span data-ttu-id="47398-101">La página de índice (`wwwroot/index.html`) incluye un script que define `AuthenticationService` en JavaScript.</span><span class="sxs-lookup"><span data-stu-id="47398-101">The Index page (`wwwroot/index.html`) page includes a script that defines the `AuthenticationService` in JavaScript.</span></span> <span data-ttu-id="47398-102">`AuthenticationService` controla los detalles de bajo nivel del protocolo OIDC.</span><span class="sxs-lookup"><span data-stu-id="47398-102">`AuthenticationService` handles the low-level details of the OIDC protocol.</span></span> <span data-ttu-id="47398-103">La aplicación llama internamente a métodos definidos en el script para realizar las operaciones de autenticación.</span><span class="sxs-lookup"><span data-stu-id="47398-103">The app internally calls methods defined in the script to perform the authentication operations.</span></span>

```html
<script src="_content/Microsoft.Authentication.WebAssembly.Msal/
    AuthenticationService.js"></script>
```
