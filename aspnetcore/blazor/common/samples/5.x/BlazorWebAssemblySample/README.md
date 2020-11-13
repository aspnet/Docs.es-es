# <a name="blazor-webassembly-sample-app"></a>Aplicación WebAssembly de Blazor de ejemplo

En este ejemplo se muestra el uso de los escenarios de Blazor descritos en la documentación.

## <a name="call-web-api-example"></a>Ejemplo de llamada a la API web

Para este ejemplo se necesita una API web en ejecución basada en la aplicación de ejemplo del tema <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">Creación de una API web con ASP.NET Core</a>, que usa de forma predeterminada el mismo puerto HTTPS (5001) que la aplicación de ejemplo de Blazor. Para usar ambas aplicaciones en la misma máquina a la vez, cambie el puerto de la API web (por ejemplo, use el puerto 10 000). La aplicación de ejemplo realiza solicitudes a la API web en `https://localhost:10000/api/TodoItems`. Si usa una dirección de API web diferente, actualice el valor constante `ServiceEndpoint` en el bloque `@code` del componente de Razor.</p>

La aplicación de ejemplo realiza una solicitud de <a href="https://docs.microsoft.com/aspnet/core/security/cors">uso compartido de recursos entre orígenes (CORS)</a> desde `http://localhost:5000` o `https://localhost:5001` a la API web. Se permite el uso de credenciales (encabezados o cookies de autorización). Agregue la siguiente configuración de middleware de CORS al método `Startup.Configure` de la API web:</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Ajuste los dominios y los puertos de `WithOrigins` según sea necesario para la aplicación de Blazor.

La API web está configurada para que CORS permita encabezados, cookies de autorización y solicitudes del código de cliente, pero tal como la crea el tutorial no autoriza realmente las solicitudes. Consulte el artículo sobre la <a href="https://docs.microsoft.com/aspnet/core/security/">seguridad y la identidad de ASP.NET Core</a> para obtener instrucciones de implementación.
