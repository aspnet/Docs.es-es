<span data-ttu-id="5e1ee-101">Ejecute los siguientes comandos CLI de .NET Core:</span><span class="sxs-lookup"><span data-stu-id="5e1ee-101">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.Extensions.Logging.Debug
```

<span data-ttu-id="5e1ee-102">Los comandos anteriores agregan:</span><span class="sxs-lookup"><span data-stu-id="5e1ee-102">The preceding commands add:</span></span>

* <span data-ttu-id="5e1ee-103">La [herramienta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="5e1ee-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="5e1ee-104">Las herramientas de Entity Framework Core para la CLI de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="5e1ee-104">The Entity Framework Core Tools for the .NET Core CLI.</span></span>
* <span data-ttu-id="5e1ee-105">El proveedor SQLite de EF Core, que instala el paquete de EF Core como una dependencia.</span><span class="sxs-lookup"><span data-stu-id="5e1ee-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="5e1ee-106">Los paquetes necesarios para scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` y `Microsoft.EntityFrameworkCore.SqlServer`.</span><span class="sxs-lookup"><span data-stu-id="5e1ee-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="5e1ee-107">Para obtener instrucciones sobre la configuración de varios entornos que permite que una aplicación configure sus contextos de base de datos por entorno, vea <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span><span class="sxs-lookup"><span data-stu-id="5e1ee-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM.md)]