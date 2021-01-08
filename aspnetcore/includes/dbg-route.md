## <a name="debug-diagnostics"></a><span data-ttu-id="52ea9-101">Diagnóstico de depuración</span><span class="sxs-lookup"><span data-stu-id="52ea9-101">Debug diagnostics</span></span>

<span data-ttu-id="52ea9-102">Para ver la salida detallada del diagnóstico de cálculo de ruta, establezca `Logging:LogLevel:Microsoft` en `Debug`.</span><span class="sxs-lookup"><span data-stu-id="52ea9-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="52ea9-103">En el entorno de desarrollo, establezca el nivel de registro en *appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="52ea9-103">In the development environment, set the log level in *appsettings.Development.json*:</span></span>

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
