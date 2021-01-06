# <a name="aspnet-core-health-check-sample"></a>Ejemplo de comprobación de estado de ASP.NET Core

En este ejemplo, se muestra el uso de Middleware de comprobación de estado y de los servicios. En este ejemplo, se muestra el escenario descrito en el tema [Comprobaciones de estado en ASP.NET Core](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks).

Para ejecutar la aplicación de ejemplo para un escenario descrito en el tema, use el comando [dotnet run](https://docs.microsoft.com/dotnet/core/tools/dotnet-run) desde la carpeta del proyecto en un shell de comandos. Pase un modificador para el escenario que esté explorando. De manera predeterminada, la aplicación adopta la configuración `basic` cuando no se proporciona ningún modificador para `dotnet run`.

| Escenario                                               | Comando de aplicación de ejemplo               | Descripción |
| ------------------------------------------------------ | -------------------------------- | ----------- |
| Sondeo de estado básico (predeterminado)                           | `dotnet run --scenario basic`    | Confirma que la aplicación puede procesar las solicitudes HTTP. |
| Sondeo de bases de datos                                         | `dotnet run --scenario db`       | Comprueba una conexión a la base de datos de SQL Server. Consulte la sección [Sondeo de bases de datos](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#database-probe) del tema para obtener instrucciones. |
| Sondeo de preparación y ejecución                              | `dotnet run --scenario liveness` | Realiza comprobaciones de estado de una aplicación activa (*ejecución*) frente a la aplicación que se prepara para ser activa (*preparación*). |
| Sondeo basado en métricas (memoria) o<br>escritor de respuesta personalizada | `dotnet run --scenario writer`   | Comprueba el uso de memoria y escribe código JSON personalizado al comprobar el punto de conexión de estado. |
| Filtrado por puerto                                         | `dotnet run --scenario port`     | Filtra las comprobaciones de estado para un puerto determinado. Consulte la sección [Filtrado por puerto](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#filter-by-port) del tema para obtener instrucciones. |

Los escenarios de sondeo de bases de datos y filtrado de puerto requieren configuración adicional. Consulte el tema [Comprobaciones de estado](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks) para obtener información.
