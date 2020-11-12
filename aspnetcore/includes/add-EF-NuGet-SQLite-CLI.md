Ejecute los siguientes comandos CLI de .NET Core:

```dotnetcli
dotnet tool install --global dotnet-ef --version 3.1.9
dotnet tool install --global dotnet-aspnet-codegenerator --version 3.1.4
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 3.1.9
dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore --version 3.1.9
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.1.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.1.9
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.1.9
dotnet add package Microsoft.Extensions.Logging.Debug --version 3.1.9
```

Los comandos anteriores agregan:

* La [herramienta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
* Las herramientas de Entity Framework Core para la CLI de .NET Core.
* El proveedor SQLite de EF Core, que instala el paquete de EF Core como una dependencia.
* Los paquetes necesarios para scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` y `Microsoft.EntityFrameworkCore.SqlServer`.

Para obtener instrucciones sobre la configuración de varios entornos que permite que una aplicación configure sus contextos de base de datos por entorno, vea <xref:fundamentals/environments#environment-based-startup-class-and-methods>.

[!INCLUDE[](~/includes/scaffoldTFM.md)]