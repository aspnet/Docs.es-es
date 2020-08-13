---
title: 'Tutorial: Uso de la característica de migraciones: ASP.NET MVC con EF Core'
description: En este tutorial, empezará usando la característica de migraciones de EF Core para administrar cambios en el modelo de datos en una aplicación ASP.NET Core MVC.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
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
uid: data/ef-mvc/migrations
ms.openlocfilehash: ade5c41546d7f4447a6048355c8d50bd7c87118c
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018169"
---
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a>Tutorial: Uso de la característica de migraciones: ASP.NET MVC con EF Core

En este tutorial, empezará usando la característica de migraciones de EF Core para administrar cambios en el modelo de datos. En los tutoriales posteriores, agregará más migraciones a medida que cambie el modelo de datos.

En este tutorial ha:

> [!div class="checklist"]
> * Obtiene información sobre las migraciones
> * Cambiar la cadena de conexión
> * Crear una migración inicial
> * Examina los métodos Up y Down
> * Obtiene información sobre la instantánea del modelo de datos
> * Aplicar la migración

## <a name="prerequisites"></a>Requisitos previos

* [Ordenar, filtrar y paginar](sort-filter-page.md)

## <a name="about-migrations"></a>Acerca de las migraciones

Al desarrollar una aplicación nueva, el modelo de datos cambia con frecuencia y, cada vez que lo hace, se deja de sincronizar con la base de datos. Estos tutoriales se iniciaron con la configuración de Entity Framework para crear la base de datos si no existía. Después, cada vez que cambie el modelo de datos (agregar, quitar o cambiar las clases de entidad, o bien cambiar la clase DbContext), puede eliminar la base de datos y EF crea una que coincida con el modelo y la inicializa con datos de prueba.

Este método para mantener la base de datos sincronizada con el modelo de datos funciona bien hasta que la aplicación se implemente en producción. Cuando la aplicación se ejecuta en producción, normalmente almacena los datos que le interesa mantener y no querrá perderlo todo cada vez que realice un cambio, como al agregar una columna nueva. La característica Migraciones de EF Core soluciona este problema habilitando EF para actualizar el esquema de la base de datos en lugar de crear una.

Para trabajar con las migraciones, puede usar la **Consola del Administrador de paquetes** (PMC) o la CLI.  En estos tutoriales se muestra cómo usar los comandos de la CLI. [Al final de este tutorial](#pmc) encontrará información sobre la PMC.

## <a name="change-the-connection-string"></a>Cambiar la cadena de conexión

En el archivo *appsettings.json*, cambie el nombre de la base de datos en la cadena de conexión por ContosoUniversity2 u otro nombre que no haya usado en el equipo que esté usando.

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

Este cambio configura el proyecto para que la primera migración cree una base de datos. Esto no es necesario para comenzar a usar las migraciones, pero más adelante se verá por qué es una buena idea.

> [!NOTE]
> Como alternativa a cambiar el nombre de la base de datos, puede eliminar la base de datos. Use el **Explorador de objetos de SQL Server** (SSOX) o el comando de la CLI `database drop`:
>
> ```dotnetcli
> dotnet ef database drop
> ```
>
> En la siguiente sección se explica cómo ejecutar comandos de la CLI.

## <a name="create-an-initial-migration"></a>Crear una migración inicial

Guarde los cambios y compile el proyecto. Después, abra una ventana de comandos y desplácese hasta la carpeta del proyecto. Esta es una forma rápida de hacerlo:

* En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y elija **Abrir la carpeta en el Explorador de archivos** en el menú contextual.

  ![Elemento de menú Abrir en el Explorador de archivos](migrations/_static/open-in-file-explorer.png)

* Escriba "cmd" en la barra de direcciones y presione Entrar.

  ![Abrir ventana de comandos](migrations/_static/open-command-window.png)

Escriba el siguiente comando en la ventana de comandos:

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet ef migrations add InitialCreate
```

`dotnet tool install --global dotnet-ef` instala `dotnet ef` como [herramienta global](/ef/core/miscellaneous/cli/dotnet).

En los comandos anteriores, se muestra una salida similar a la siguiente:

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

Si ve un mensaje de error "*No se puede obtener acceso al archivo... ContosoUniversity.dll porque lo está usando otro proceso.* ", busque el icono de IIS Express en la bandeja del sistema de Windows, haga clic con el botón derecho en él y, después, haga clic en **ContosoUniversity > Detener sitio**.

## <a name="examine-up-and-down-methods"></a>Examina los métodos Up y Down

Cuando ejecutó el comando `migrations add`, EF generó el código que va a crear la base de datos desde cero. Este código está en la carpeta *Migraciones*, en el archivo denominado *\<timestamp>_InitialCreate.cs*. El método `Up` de la clase `InitialCreate` crea las tablas de base de datos que corresponden a los conjuntos de entidades del modelo de datos y el método `Down` las elimina, como se muestra en el ejemplo siguiente.

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

Las migraciones llaman al método `Up` para implementar los cambios del modelo de datos para una migración. Cuando se escribe un comando para revertir la actualización, las migraciones llaman al método `Down`.

Este código es para la migración inicial que se creó cuando se escribió el comando `migrations add InitialCreate`. El parámetro de nombre de la migración ("InitialCreate" en el ejemplo) se usa para el nombre de archivo y puede ser lo que quiera. Es más recomendable elegir una palabra o frase que resuma lo que se hace en la migración. Por ejemplo, podría denominar "AddDepartmentTable" a una migración posterior.

Si creó la migración inicial cuando la base de datos ya existía, se genera el código de creación de la base de datos pero no es necesario ejecutarlo porque la base de datos ya coincide con el modelo de datos. Al implementar la aplicación en otro entorno donde la base de datos todavía no existe, se ejecutará este código para crear la base de datos, por lo que es recomendable probarlo primero. Por ese motivo se cambió antes el nombre de la base de datos en la cadena de conexión, para que las migraciones puedan crear uno desde cero.

## <a name="the-data-model-snapshot"></a>La instantánea del modelo de datos

Las migraciones crean una *instantánea* del esquema de la base de datos actual en *Migrations/SchoolContextModelSnapshot.cs*. Cuando se agrega una migración, EF determina qué ha cambiado mediante la comparación del modelo de datos con el archivo de instantánea.

Use el comando [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) para quitar una migración. `dotnet ef migrations remove` elimina la migración y garantiza que la instantánea se restablece correctamente. Si `dotnet ef migrations remove` produce un error, use `dotnet ef migrations remove -v` para obtener más información sobre el error.

Vea [Migraciones en entornos de equipo](/ef/core/managing-schemas/migrations/teams) para más información sobre cómo se usa el archivo de instantánea.

## <a name="apply-the-migration"></a>Aplicar la migración

En la ventana de comandos, escriba el comando siguiente para crear la base de datos y tablas en su interior.

```dotnetcli
dotnet ef database update
```

El resultado del comando es similar al comando `migrations add`, con la excepción de que verá registros para los comandos SQL que configuran la base de datos. La mayoría de los registros se omite en la siguiente salida de ejemplo. Si prefiere no ver este nivel de detalle en los mensajes de registro, puede cambiarlo en el archivo *appsettings.Development.json*. Para obtener más información, vea <xref:fundamentals/logging/index>.

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (274ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (60ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      IF SERVERPROPERTY('EngineEdition') <> 5
      BEGIN
          ALTER DATABASE [ContosoUniversity2] SET READ_COMMITTED_SNAPSHOT ON;
      END;
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (15ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20190327172701_InitialCreate', N'2.2.0-rtm-35687');
Done.
```

Use el **Explorador de objetos de SQL Server** para inspeccionar la base de datos como hizo en el primer tutorial.  Observará la adición de una tabla \_\_EFMigrationsHistory que realiza el seguimiento de las migraciones que se han aplicado a la base de datos. Si examina los datos de esa tabla, verá una fila para la primera migración. (En el último registro del ejemplo de salida de la CLI anterior se muestra la instrucción INSERT que crea esta fila).

Ejecute la aplicación para comprobar que todo funciona igual que antes.

![Página de índice de Students](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a>Comparar CLI y PMC

Las herramientas de EF para la administración de migraciones están disponibles desde los comandos de la CLI de .NET Core o los cmdlets de PowerShell en la ventana **Consola del Administrador de paquetes** (PMC) de Visual Studio. En este tutorial se muestra cómo usar la CLI, pero puede usar la PMC si lo prefiere.

Los comandos de EF para los comandos de la PMC están en el paquete [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools). Este paquete se incluye en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), por lo que no es necesario agregar una referencia de paquete si la aplicación tiene una para `Microsoft.AspNetCore.App`.

**Importante:** Este no es el mismo paquete que el que se instala para la CLI mediante la edición del archivo *.csproj*. El nombre de este paquete termina en `Tools`, a diferencia del nombre de paquete de la CLI que termina en `Tools.DotNet`.

Para obtener más información sobre los comandos de la CLI, vea [CLI de .NET Core](/ef/core/miscellaneous/cli/dotnet).

Para obtener más información sobre los comandos de la PMC, vea [Consola del Administrador de paquetes (Visual Studio)](/ef/core/miscellaneous/cli/powershell).

## <a name="get-the-code"></a>Obtención del código

[Descargue o vea la aplicación completa.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a>Paso siguiente

En este tutorial ha:

> [!div class="checklist"]
> * Obtenido información sobre las migraciones
> * Obtenido información sobre los paquetes de migración de NuGet
> * Cambiado la cadena de conexión
> * Creado una migración inicial
> * Examinado los métodos Up y Down
> * Obtenido información sobre la instantánea del modelo de datos
> * Aplicado la migración

Pase al tutorial siguiente para comenzar a examinar temas más avanzados sobre la expansión del modelo de datos. Por el camino, podrá crear y aplicar migraciones adicionales.

> [!div class="nextstepaction"]
> [Creación y aplicación de migraciones adicionales](complex-data-model.md)
