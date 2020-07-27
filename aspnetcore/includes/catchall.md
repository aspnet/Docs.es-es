> [!WARNING]
> Un parámetro **catch-all** puede relacionar rutas de forma incorrecta debido a un [error](https://github.com/dotnet/aspnetcore/issues/18677) en el enrutamiento. Las aplicaciones afectadas por este error tienen las características siguientes:
>
> * Una ruta catch-all (por ejemplo, `{**slug}"`)
> * La ruta catch-all causa un error al relacionar solicitudes que sí que debería relacionar.
> * Al quitar otras rutas, la ruta catch-all empieza a funcionar.
>
> Para ver casos de ejemplo relacionados con este error, consulte los errores [18677](https://github.com/dotnet/aspnetcore/issues/18677) y [16579](https://github.com/dotnet/aspnetcore/issues/16579) en GitHub.
>
> Se incluye una corrección de participación para este error en el [SDK de .NET Core 3.1.301 y versiones posteriores](https://dotnet.microsoft.com/download/dotnet-core/3.1). En el código que hay a continuación se establece un cambio interno que corrige este error:
>
>```csharp
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```