## <a name="debug-diagnostics"></a>Diagnóstico de depuración

Para ver la salida detallada del diagnóstico de cálculo de ruta, establezca `Logging:LogLevel:Microsoft` en `Debug`. En el entorno de desarrollo, establezca el nivel de registro en *appsettings.Development.jsen*:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```
