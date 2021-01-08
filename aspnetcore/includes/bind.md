La mejor manera de leer valores de configuración relacionados es usar el [patrón de opciones](xref:fundamentals/configuration/options). Por ejemplo, para leer los siguientes valores de configuración:

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

Cree la siguiente clase `PositionOptions`:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

Una clase de opciones:

* Debe ser no abstracta con un constructor público sin parámetros.
* Todas las propiedades de lectura y escritura públicas del tipo están enlazadas.
* Los campos ***no** _ se enlazan. En el código anterior, `Position` no está enlazado. La propiedad `Position` se usa para que la cadena `"Position"` no tenga que estar codificada de forma rígida en la aplicación al enlazar la clase a un proveedor de configuración.

El código siguiente:

_ Llama a [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) para enlazar la clase `PositionOptions` a la sección `Position`.
* Muestra los datos de configuración de `Position`.

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

En el código anterior, de forma predeterminada, los cambios en el archivo de configuración de JSON producidos una vez iniciada la aplicación se leen.

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) enlaza y devuelve el tipo especificado. Puede ser más conveniente usar `ConfigurationBinder.Get<T>` que `ConfigurationBinder.Bind`. En el código siguiente se muestra cómo puede usar `ConfigurationBinder.Get<T>` con la clase `PositionOptions`:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

En el código anterior, de forma predeterminada, los cambios en el archivo de configuración de JSON producidos una vez iniciada la aplicación se leen.

Un enfoque alternativo a la hora de usar el ***patrón de opciones** _ consiste en enlazar la sección `Position` y agregarla al [contenedor del servicio de inserción de dependencias](xref:fundamentals/dependency-injection). En el siguiente código se agrega `PositionOptions` al contenedor de servicios con <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_> y se enlaza a la configuración:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

A partir del código anterior, el siguiente código lee las opciones de posición:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

En el código anterior, los cambios en el archivo de configuración de JSON producidos una vez iniciada la aplicación ***no*** se leen. Para leer los cambios una vez iniciada la aplicación, use [IOptionsSnapshot](xref:fundamentals/configuration/options#ios).
