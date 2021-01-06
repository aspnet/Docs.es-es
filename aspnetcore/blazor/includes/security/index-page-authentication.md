La página de índice (`wwwroot/index.html`) incluye un script que define `AuthenticationService` en JavaScript. `AuthenticationService` controla los detalles de bajo nivel del protocolo OIDC. La aplicación llama internamente a métodos definidos en el script para realizar las operaciones de autenticación.

```html
<script src="_content/Microsoft.AspNetCore.Components.WebAssembly.Authentication/
    AuthenticationService.js"></script>
```
