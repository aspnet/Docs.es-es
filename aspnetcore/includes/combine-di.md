<a name="csc"></a>

Tenga en cuenta el siguiente método `ConfigureServices`, que registra los servicios y configura las opciones:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

Los grupos de registros relacionados pueden moverse a un método de extensión para registrar los servicios. Por ejemplo, los servicios de configuración se agregan a la siguiente clase:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfigServiceCollectionExtensions.cs)]

Los servicios restantes se registran en una clase similar. El siguiente método `ConfigureServices` usa los nuevos métodos de extensión para registrar los servicios:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

**_Nota:_** Cada método de extensión `services.Add{GROUP_NAME}` agrega servicios y potencialmente los configura. Por ejemplo, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> agrega los controladores MVC de servicios con vistas que se requieren y <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> agrega los servicios que Razor Pages requiere. Se recomienda que las aplicaciones sigan esta convención de nomenclatura. Coloque los métodos de extensión en el espacio de nombres <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> para encapsular grupos de registros del servicio.
