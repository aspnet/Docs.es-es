---
title: 'Parte 4. Razor Pages con EF Core en ASP.NET Core: Migraciones'
author: rick-anderson
description: Parte 4 de la serie de tutoriales sobre Razor Pages y Entity Framework.
ms.author: riande
ms.date: 07/22/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/migrations
ms.openlocfilehash: ebdcf5605e8b4067c52841b138184e51668f9c25
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019656"
---
# <a name="part-4-no-locrazor-pages-with-ef-core-migrations-in-aspnet-core"></a>Parte 4. Razor Pages con migraciones de EF Core en ASP.NET Core

Por [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) y [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

En este tutorial se presenta la característica de migraciones de EF Core para administrar cambios en el modelo de datos.

Cuando se desarrolla una aplicación nueva, el modelo de datos cambia con frecuencia. Cada vez que el modelo cambia, este deja de estar sincronizado con la base de datos. Esta serie de tutoriales se ha iniciado con la configuración de Entity Framework para crear la base de datos si no existe. Cada vez que cambia el modelo de datos, tiene que quitar la base de datos. La próxima vez que se ejecute la aplicación, la llamada a `EnsureCreated` vuelve a crear la base de datos para que coincida con el nuevo modelo de datos. Después, se ejecuta la clase `DbInitializer` para inicializar la nueva base de datos.

Este enfoque para mantener la base de datos sincronizada con el modelo de datos funciona bien hasta que la aplicación se implemente en producción. Cuando se ejecuta la aplicación en producción, normalmente está almacenando datos que hay que mantener. No se puede iniciar la aplicación con una base de datos de prueba cada vez que se realiza un cambio (por ejemplo, agregar una columna nueva). La característica Migraciones de EF Core soluciona este problema permitiendo a EF Core actualizar el esquema de la base de datos en lugar de crear una.

En lugar de quitar y volver a crear la base de datos cuando cambian los datos del modelo, las migraciones actualizan el esquema y conservan los datos existentes.

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a>Eliminación de la base de datos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Use **Explorador de objetos de SQL Server** (SSOX) para eliminar la base de datos, o bien ejecute el comando siguiente en la **Consola del administrador de paquetes** (PMC):

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ejecute el comando siguiente en un símbolo del sistema para instalar la CLI de EF:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* En el símbolo del sistema, vaya a la carpeta del proyecto. La carpeta del proyecto contiene el archivo *ContosoUniversity.csproj*.

* Elimine el archivo *CU.db*, o bien ejecute el comando siguiente:

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a>Crear una migración inicial

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Ejecute los comandos siguientes en la Consola del administrador de paquetes:

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Asegúrese de que el símbolo del sistema está en la carpeta del proyecto y ejecute los comandos siguientes:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a>Métodos Up y Down

El comando `migrations add` de EF Core ha generado código para crear la base de datos. Este código de migraciones se encuentra en el archivo *Migrations\<timestamp>_InitialCreate.cs*. El método `Up` de la clase `InitialCreate` crea las tablas de base de datos que se corresponden a los conjuntos de entidades del modelo de datos. El método `Down` las elimina, tal como se muestra en el ejemplo siguiente:

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

El código anterior es para la migración inicial. El código:

* Lo ha generado el comando `migrations add InitialCreate`. 
* Lo ejecuta el comando `database update`.
* Crea una base de datos para el modelo de datos especificado por la clase de contexto de base de datos.

El parámetro de nombre de la migración ("InitialCreate" en el ejemplo) se usa para el nombre de archivo. El nombre de la migración puede ser cualquier nombre de archivo válido. Es más recomendable elegir una palabra o frase que resuma lo que se hace en la migración. Por ejemplo, una migración que ha agregado una tabla de departamento podría denominarse "AddDepartmentTable".

## <a name="the-migrations-history-table"></a>La tabla de historial de migraciones

* Use SSOX o la herramienta SQLite para inspeccionar la base de datos.
* Observe la adición de una tabla `__EFMigrationsHistory`. En la tabla `__EFMigrationsHistory` se realiza el seguimiento de las migraciones que se han aplicado a la base de datos.
* Vea los datos de la tabla `__EFMigrationsHistory`. Muestra una fila para la primera migración.

## <a name="the-data-model-snapshot"></a>La instantánea del modelo de datos

Las migraciones crean una *instantánea* del modelo de datos actual en *Migrations/SchoolContextModelSnapshot.cs*. Cuando se agrega una migración, EF determina qué ha cambiado mediante la comparación del modelo de datos actual con el archivo de instantánea.

Como el archivo de instantánea realiza el seguimiento del estado del modelo de datos, no se puede eliminar una migración mediante la eliminación del archivo `<timestamp>_<migrationname>.cs`. Para quitar la migración más reciente, tiene que usar el comando `migrations remove`. Ese comando elimina la migración y garantiza que la instantánea se restablezca de forma correcta. Para obtener más información, vea [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).

## <a name="remove-ensurecreated"></a>Quitar EnsureCreated

Esta serie de tutoriales se ha iniciado con `EnsureCreated`. `EnsureCreated` no crea una tabla de historial de migraciones y, por tanto, no se puede usar con las migraciones. Está diseñado para crear prototipos rápidos o de prueba donde la base de datos se quita y se vuelve a crear con frecuencia.

A partir de este punto, en los tutoriales se usarán las migraciones.

En *Data/DBInitializer.cs*, comente la línea siguiente:

```csharp
context.Database.EnsureCreated();
```
Ejecute la aplicación y compruebe que la base de datos se ha inicializado.

## <a name="applying-migrations-in-production"></a>Aplicar las migraciones en producción

Se recomienda que las aplicaciones de producción **no** llamen a [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) al iniciar la aplicación. `Migrate` no se debe llamar desde una aplicación que se implementa en una granja de servidores. Si la aplicación se escala horizontalmente en varias instancias de servidor, es difícil garantizar que las actualizaciones del esquema de la base de datos no se realicen en varios servidores o que estén en conflicto con el acceso de lectura y escritura.

La migración de bases de datos debe realizarse como parte de la implementación y de un modo controlado. Entre los métodos de migración de base de datos de producción se incluyen:

* Uso de las migraciones para crear scripts SQL y uso de scripts SQL en la implementación.
* Ejecución de `dotnet ef database update` desde un entorno controlado.

## <a name="troubleshooting"></a>Solución de problemas

Si en la aplicación se usa SQL Server LocalDB y se muestra la excepción siguiente:

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

La solución puede ser ejecutar `dotnet ef database update` en un símbolo del sistema.

### <a name="additional-resources"></a>Recursos adicionales

* [CLI de EF Core](/ef/core/miscellaneous/cli/dotnet).
* [Consola del administrador de paquetes (Visual Studio)](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a>Pasos siguientes

En el tutorial siguiente se crea el modelo de datos y se agregan propiedades de entidad y entidades nuevas.

> [!div class="step-by-step"]
> [Tutorial anterior](xref:data/ef-rp/sort-filter-page)
> [Tutorial siguiente](xref:data/ef-rp/complex-data-model)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

En este tutorial, se usa la característica de migraciones de EF Core para administrar cambios en el modelo de datos.

Si experimenta problemas que no puede resolver, descargue la [aplicación completada](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

Cuando se desarrolla una aplicación nueva, el modelo de datos cambia con frecuencia. Cada vez que el modelo cambia, este deja de estar sincronizado con la base de datos. Este tutorial se inició con la configuración de Entity Framework para crear la base de datos si no existía. Cada vez que los datos del modelo cambian:

* Se quita la base de datos.
* EF crea una que coincide con el modelo.
* La aplicación inicializa la base de datos con datos de prueba.

Este enfoque para mantener la base de datos sincronizada con el modelo de datos funciona bien hasta que la aplicación se implemente en producción. Cuando se ejecuta la aplicación en producción, normalmente está almacenando datos que hay que mantener. No se puede iniciar la aplicación con una prueba de base de datos cada vez que se hace un cambio (por ejemplo, agregar una nueva columna). La característica Migraciones de EF Core soluciona este problema habilitando EF Core para actualizar el esquema de la base de datos en lugar de crear una.

En lugar de quitar y volver a crear la base de datos cuando los datos del modelo cambian, las migraciones actualizan el esquema y conservan los datos existentes.

## <a name="drop-the-database"></a>Eliminación de la base de datos

Use el **Explorador de objetos de SQL Server** (SSOX) o el comando `database drop`:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

En la **Consola del Administrador de paquetes** (PMC), ejecute el comando siguiente:

```powershell
Drop-Database
```

Ejecute `Get-Help about_EntityFrameworkCore` desde PMC para obtener información de ayuda.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Abra una ventana de comandos y desplácese hasta la carpeta del proyecto. La carpeta del proyecto contiene el archivo *Startup.cs*.

Escriba lo siguiente en la ventana de comandos:

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a>Creación de una migración inicial y actualización de la base de datos

Compile el proyecto y cree la primera migración.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a>Examinar los métodos Up y Down

El comando `migrations add` de EF Core ha generado código para crear la base de datos. Este código de migraciones se encuentra en el archivo *Migrations\<timestamp>_InitialCreate.cs*. El método `Up` de la clase `InitialCreate` crea las tablas de base de datos que corresponden a los conjuntos de entidades del modelo de datos. El método `Down` las elimina, tal como se muestra en el ejemplo siguiente:

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

Las migraciones llaman al método `Up` para implementar los cambios del modelo de datos para una migración. Cuando se escribe un comando para revertir la actualización, las migraciones llaman al método `Down`.

El código anterior es para la migración inicial. Ese código se creó cuando se ejecutó el comando `migrations add InitialCreate`. El parámetro de nombre de la migración ("InitialCreate" en el ejemplo) se usa para el nombre de archivo. El nombre de la migración puede ser cualquier nombre de archivo válido. Es más recomendable elegir una palabra o frase que resuma lo que se hace en la migración. Por ejemplo, una migración que ha agregado una tabla de departamento podría denominarse "AddDepartmentTable".

Si la migración inicial está creada y la base de datos existe:

* Se genera el código de creación de la base de datos.
* El código de creación de la base de datos no tiene que ejecutarse porque la base de datos ya coincide con el modelo de datos. Si el código de creación de la base de datos se está ejecutando, no hace ningún cambio porque la base de datos ya coincide con el modelo de datos.

Cuando la aplicación se implementa en un entorno nuevo, se debe ejecutar el código de creación de la base de datos para crear la base de datos.

Anteriormente, la base de datos se eliminó, de modo que ya no existe y ahora se crea mediante las migraciones.

### <a name="the-data-model-snapshot"></a>La instantánea del modelo de datos

Las migraciones crean una *instantánea* del esquema de la base de datos actual en *Migrations/SchoolContextModelSnapshot.cs*. Cuando se agrega una migración, EF determina qué ha cambiado mediante la comparación del modelo de datos con el archivo de instantánea.

Para eliminar una migración, use el comando siguiente:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Remove-Migration

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

Para obtener más información, vea [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).

---

El comando remove migrations elimina la migración y garantiza que la instantánea se restablece correctamente.

### <a name="remove-ensurecreated-and-test-the-app"></a>Eliminación de EnsureCreated y prueba de la aplicación

Para el desarrollo inicial se ha utilizado `EnsureCreated`. En este tutorial, se usan las migraciones. `EnsureCreated` tiene las siguientes limitaciones:

* Omite las migraciones y crea la base de datos y el esquema.
* No crea una tabla de migraciones.
* *No* puede usarse con las migraciones.
* Está diseñado para crear prototipos rápidos o de prueba donde se quita y vuelve a crear la base de datos con frecuencia.

Quite `EnsureCreated`:

```csharp
context.Database.EnsureCreated();
```

Ejecute la aplicación y compruebe que la base de datos se haya inicializado.

### <a name="inspect-the-database"></a>Inspección de la base de datos

Use el **Explorador de objetos de SQL Server** para inspeccionar la base de datos. Observe la adición de una tabla `__EFMigrationsHistory`. La tabla `__EFMigrationsHistory` realiza un seguimiento de las migraciones que se han aplicado a la base de datos. Examine los datos de la tabla `__EFMigrationsHistory`, muestra una fila para la primera migración. En el último registro del ejemplo de salida de la CLI anterior se muestra la instrucción INSERT que crea esta fila.

Ejecute la aplicación y compruebe que todo funciona correctamente.

## <a name="applying-migrations-in-production"></a>Aplicar las migraciones en producción

Se recomienda que las aplicaciones de producción **no** llamen a [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) al iniciar la aplicación. No debe llamarse a `Migrate` desde una aplicación en la granja de servidores. Por ejemplo, si la aplicación se ha implementado en la nube con escalado horizontal (se ejecutan varias instancias de la aplicación).

La migración de bases de datos debe realizarse como parte de la implementación y de un modo controlado. Entre los métodos de migración de base de datos de producción se incluyen:

* Uso de las migraciones para crear scripts SQL y uso de scripts SQL en la implementación.
* Ejecución de `dotnet ef database update` desde un entorno controlado.

EF Core usa la tabla `__MigrationsHistory` para ver si es necesario ejecutar las migraciones. Si la base de datos está actualizada, no se ejecuta ninguna migración.

## <a name="troubleshooting"></a>Solución de problemas

Descargue la [aplicación completada](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).

La aplicación genera la siguiente excepción:

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

Solución: Ejecute `dotnet ef database update`.

### <a name="additional-resources"></a>Recursos adicionales

* [Versión en YouTube de este tutorial](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* [CLI de .NET Core](/ef/core/miscellaneous/cli/dotnet).
* [Consola del administrador de paquetes (Visual Studio)](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> [Anterior](xref:data/ef-rp/sort-filter-page)
> [Siguiente](xref:data/ef-rp/complex-data-model)

::: moniker-end

