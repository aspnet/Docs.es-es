Las aplicaciones Blazor Server residen en la memoria del servidor. Eso significa que hay varias aplicaciones hospedadas dentro del mismo proceso. En cada sesión de aplicación, Blazor inicia un circuito con su propio ámbito de contenedor de DI. Eso significa que los servicios con ámbito son únicos por sesión de Blazor.

> [!WARNING]
> No se recomienda que las aplicaciones del mismo servidor compartan estado mediante servicios singleton, a menos que se tomen precauciones, ya que esto puede incorporar vulnerabilidades de seguridad, como la pérdida de estado de usuario entre circuitos.

Puede usar servicios singleton con estado en aplicaciones Blazor si están específicamente diseñadas para ello. Por ejemplo, es correcto usar una caché de memoria como singleton porque requiere una clave para acceder a una entrada determinada, dando por hecho que los usuarios no tienen control sobre las claves de caché que se usan.

**Además, de nuevo por motivos de seguridad, no debe usar <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> en aplicaciones Blazor.** Las aplicaciones de Blazor se ejecutan fuera del contexto de la canalización de ASP.NET Core. No se garantiza que <xref:Microsoft.AspNetCore.Http.HttpContext> esté disponible en <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>, ni tampoco se garantiza que contenga el contexto que inició la aplicación de Blazor.

La manera recomendada de pasar el estado de la solicitud a la aplicación Blazor es por medio de parámetros al componente raíz en la representación inicial de la aplicación:

* Defina una clase con todos los datos que quiere pasar a la aplicación Blazor.
* Rellene los datos de la página de Razor con el elemento <xref:Microsoft.AspNetCore.Http.HttpContext> disponible en ese momento.
* Pase los datos a la aplicación Blazor como parámetro al componente raíz (App).
* Defina un parámetro en el componente raíz para que contenga los datos que se pasan a la aplicación.
* Use los datos específicos del usuario dentro de la aplicación; o bien, copie esos datos en un servicio con ámbito dentro de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> para que se puedan usar en la aplicación.

Para obtener más información y un código de ejemplo, vea <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.
