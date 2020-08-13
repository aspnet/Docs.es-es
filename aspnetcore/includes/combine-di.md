<a name="csc"></a>

## <a name="combining-service-collection"></a>Combinación de colecciones de servicios

Supongamos que el siguiente elemento `ConfigureServices` contiene varias colecciones de servicios:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

Los grupos de registros relacionados pueden moverse a un método de extensión para registrar los servicios. Por ejemplo, los servicios de configuración se agregan a la siguiente clase:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfgServiceCollectionExtensions.cs)]

Los servicios restantes se registran en una clase similar. El siguiente elemento `ConfigureServices` usa los nuevos métodos de extensión para registrar los servicios:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

***Nota:*** Cada método de extensión `services.Add{SERVICE_NAME}` agrega servicios y potencialmente los configura. Por ejemplo, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> agrega los servicios que requieren los controladores MVC con vistas. <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> agrega los servicios que requiere Razor Pages. Se recomienda que las aplicaciones sigan esta convención de nomenclatura. Coloque los métodos de extensión en el espacio de nombres [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros del servicio.