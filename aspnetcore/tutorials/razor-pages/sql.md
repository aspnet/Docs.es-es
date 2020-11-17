---
title: Parte 4, trabajo con una base de datos
author: rick-anderson
description: Parte 4 de la serie de tutoriales sobre Razor Pages.
ms.author: riande
ms.date: 09/26/2020
no-loc:
- Index
- Create
- Delete
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 3e78b5b6dab7145413ae8612bfeb352f328ec86a
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360734"
---
# <a name="part-4-work-with-a-database-and-aspnet-core"></a>Parte 4, trabajo con una base de datos y ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Joe Audette](https://twitter.com/joeaudette)

::: moniker range=">= aspnetcore-5.0"

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([cómo descargarlo](xref:index#how-to-download-a-sample)).

El objeto `RazorPagesMovieContext` controla la tarea de conexión a la base de datos y asignación de objetos `Movie` a los registros de la base de datos. El contexto de base de datos se registra con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en el método `ConfigureServices` de *Startup.cs*:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

El sistema [Configuración](xref:fundamentals/configuration/index) de ASP.NET Core lee la clave `ConnectionString`. Para el desarrollo local, la configuración obtiene la cadena de conexión del archivo *appsettings.json* .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

La cadena de conexión generada será similar a la siguiente:

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Cuando la aplicación se implementa en un servidor de prueba o producción, se puede utilizar una variable de entorno para establecer la cadena de conexión en un servidor de base de datos de prueba o producción. Para obtener más información, vea [Configuración](xref:fundamentals/configuration/index).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB es una versión ligera del motor de base de datos de SQL Server Express dirigida al desarrollo de programas. LocalDB se inicia a petición y se ejecuta en modo de usuario, sin necesidad de una configuración compleja. De forma predeterminada, la base de datos LocalDB crea archivos `*.mdf` en el directorio `C:\Users\<user>\`.

<a name="ssox"></a>
1. En el menú **Ver**, abra **Explorador de objetos de SQL Server** (SSOX).

   ![Menú Ver](sql/_static/5/ssox.png)

1. Haga clic con el botón derecho en la tabla `Movie` y seleccione **Diseñador de vistas**:

   ![Menús contextuales abiertos en la tabla Movie](sql/_static/5/design.png)

   ![Tablas Movie abiertas en el diseñador](sql/_static/dv.png)

   Observe el icono de llave junto a `ID`. De forma predeterminada, EF crea una propiedad denominada `ID` para la clave principal.

1. Haga clic con el botón derecho en la tabla `Movie` y seleccione **Ver datos**:

   ![Tabla Movie abierta mostrando datos de la tabla](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a>SQLite

Según la información del sitio web de [SQLite](https://www.sqlite.org/):

> SQLite es un motor de base de datos SQL independiente, de alta confiabilidad, insertado, con características completas y dominio público. SQLite es el motor de base de datos más usado en el mundo.

Existen muchas herramientas de terceros que se pueden descargar para administrar y ver una base de datos de SQLite. La imagen de abajo es de [DB Browser for SQLite](https://sqlitebrowser.org/). Si tiene una herramienta favorita de SQLite, deje un comentario sobre lo que le gusta de ella.

![DB Browser for SQLite mostrando una base de datos de películas](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> Para este tutorial, se usa la característica de las *migraciones* de Entity Framework Core siempre que sea posible. Las migraciones actualizan el esquema de la base de datos para que coincida con los cambios en el modelo de datos. Aun así, las migraciones solo pueden realizar los tipos de cambios que admite el proveedor de EF Core; las capacidades del de SQLite son limitadas. Por ejemplo, se permite agregar una columna, pero no eliminarla ni cambiarla. Si se crea una migración para quitar o cambiar una columna, el comando `ef migrations add` se ejecuta correctamente, pero el comando `ef database update` produce un error. Debido a estas limitaciones, este tutorial no usa las migraciones para realizar cambios en el esquema de SQLite. En su lugar, cuando cambie el esquema, la base de datos se quitará y se volverá a crear.
>
>La solución alternativa para las limitaciones de SQLite consiste en escribir de forma manual el código de migraciones para llevar a cabo una recompilación de la tabla cuando cambie algún elemento de esta. La recompilación de una tabla implica lo siguiente:
>
>* Crear una tabla.
>* Copiar datos de la tabla antigua a la nueva tabla.
>* Eliminar la tabla anterior.
>* Cambiar el nombre de la tabla nueva.
>
>Para obtener más información, vea los siguientes recursos:
> * [Limitaciones del proveedor de base de datos SQLite para EF Core](/ef/core/providers/sqlite/limitations)
> * [Personalización del código de migración](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Propagación de los datos](/ef/core/modeling/data-seeding)
> * [Instrucción ALTER TABLE de SQLite](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a>Inicializar la base de datos

Create una nueva clase denominada `SeedData` en la carpeta *Models* con el código siguiente:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

Si hay alguna película en la base de datos, se devuelve el inicializador y no se agrega ninguna película.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Agregar el inicializador

Reemplace su contenido por el código siguiente:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Program.cs)]

En el código anterior, el método `Main` se ha modificado para hacer lo siguiente:

* Obtener una instancia del contexto de base de datos desde el contenedor de inserción de dependencias.
* Llame al método `seedData.Initialize`, pasándole la instancia de contexto de la base de datos.
* Eliminar el contexto cuando el método de inicialización finalice. La [instrucción using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) garantiza que se elimine el contexto.

La siguiente excepción se produce cuando no se ha ejecutado `Update-Database`:

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>Prueba de la aplicación

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Delete todos los registros de la base de datos. Use los vínculos de eliminación en el explorador o en [SSOX](xref:tutorials/razor-pages/new-field#ssox).

1. Obligue a la aplicación a inicializarse (llame a los métodos de la clase `Startup`) para que se ejecute el método de inicialización. Para forzar la inicialización, se debe detener y reiniciar IIS Express. Detenga y reinicie IIS con cualquiera de los métodos siguientes:

   1. Haga clic con el botón derecho en el icono Bandeja del sistema de IIS Express del área de notificación y seleccione **Salir** o **Detener sitio**:

      ![Icono Bandeja del sistema de IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![Menú contextual](sql/_static/stopIIS.png)

   1. Si está ejecutando la aplicación en modo de no depuración, presione <kbd>F5</kbd> para ejecutar en modo de depuración.
   1. Si la aplicación está en modo de depuración, detenga el depurador y presione <kbd>F5</kbd>.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Delete todos los registros de la base de datos, para que se ejecute el método de inicialización. Detenga e inicie la aplicación para inicializar la base de datos.

---

La aplicación muestra los datos inicializados:

![Aplicación de películas abierta en el explorador donde se muestran los datos de películas](sql/_static/5/m55.png)

## <a name="additional-resources"></a>Recursos adicionales

> [!div class="step-by-step"]
> [Anterior: Razor Pages con scaffolding](xref:tutorials/razor-pages/page)
> [Siguiente: Actualización de las páginas](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([cómo descargarlo](xref:index#how-to-download-a-sample)).

El objeto `RazorPagesMovieContext` controla la tarea de conexión a la base de datos y asignación de objetos `Movie` a los registros de la base de datos. El contexto de base de datos se registra con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en el método `ConfigureServices` de *Startup.cs*:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

El sistema [Configuración](xref:fundamentals/configuration/index) de ASP.NET Core lee la clave `ConnectionString`. Para el desarrollo local, la configuración obtiene la cadena de conexión del archivo *appsettings.json* .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

La cadena de conexión generada será similar a la siguiente:

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Cuando la aplicación se implementa en un servidor de prueba o producción, se puede utilizar una variable de entorno para establecer la cadena de conexión en un servidor de base de datos de prueba o producción. Para más información, vea [Configuración](xref:fundamentals/configuration/index).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB es una versión ligera del motor de base de datos de SQL Server Express dirigida al desarrollo de programas. LocalDB se inicia a petición y se ejecuta en modo de usuario, sin necesidad de una configuración compleja. De forma predeterminada, la base de datos LocalDB crea archivos `*.mdf` en el directorio `C:\Users\<user>\`.

<a name="ssox"></a>
* En el menú **Ver**, abra **Explorador de objetos de SQL Server** (SSOX).

  ![Menú Ver](sql/_static/ssox.png)

* Haga clic con el botón derecho en la tabla `Movie` y seleccione **Diseñador de vistas**:

  ![Menús contextuales abiertos en la tabla Movie](sql/_static/design.png)

  ![Tablas Movie abiertas en el diseñador](sql/_static/dv.png)

Observe el icono de llave junto a `ID`. De forma predeterminada, EF crea una propiedad denominada `ID` para la clave principal.

* Haga clic con el botón derecho en la tabla `Movie` y seleccione **Ver datos**:

  ![Tabla Movie abierta mostrando datos de la tabla](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a>SQLite

Según la información del sitio web de [SQLite](https://www.sqlite.org/):

> SQLite es un motor de base de datos SQL independiente, de alta confiabilidad, insertado, con características completas y dominio público. SQLite es el motor de base de datos más usado en el mundo.

Existen muchas herramientas de terceros que se pueden descargar para administrar y ver una base de datos de SQLite. La imagen de abajo es de [DB Browser for SQLite](https://sqlitebrowser.org/). Si tiene una herramienta favorita de SQLite, deje un comentario sobre lo que le gusta de ella.

![DB Browser for SQLite mostrando una base de datos de películas](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> Para este tutorial, se usa la característica de las *migraciones* de Entity Framework Core siempre que sea posible. Las migraciones actualizan el esquema de la base de datos para que coincida con los cambios en el modelo de datos. Aun así, las migraciones solo pueden realizar los tipos de cambios que admite el proveedor de EF Core; las capacidades del de SQLite son limitadas. Por ejemplo, se permite agregar una columna, pero no eliminarla ni cambiarla. Si se crea una migración para quitar o cambiar una columna, el comando `ef migrations add` se ejecuta correctamente, pero el comando `ef database update` produce un error. Debido a estas limitaciones, este tutorial no usa las migraciones para realizar cambios en el esquema de SQLite. En su lugar, cuando cambie el esquema, la base de datos se quitará y se volverá a crear.
>
>La solución alternativa para las limitaciones de SQLite consiste en escribir de forma manual el código de migraciones para llevar a cabo una recompilación de la tabla cuando cambie algún elemento de esta. La recompilación de una tabla implica lo siguiente:
>
>* Crear una tabla.
>* Copiar datos de la tabla antigua a la nueva tabla.
>* Eliminar la tabla anterior.
>* Cambiar el nombre de la tabla nueva.
>
>Para obtener más información, vea los siguientes recursos:
> * [Limitaciones del proveedor de base de datos SQLite para EF Core](/ef/core/providers/sqlite/limitations)
> * [Personalización del código de migración](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Propagación de los datos](/ef/core/modeling/data-seeding)
> * [Instrucción ALTER TABLE de SQLite](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a>Inicializar la base de datos

Create una nueva clase denominada `SeedData` en la carpeta *Models* con el código siguiente:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

Si hay alguna película en la base de datos, se devuelve el inicializador y no se agrega ninguna película.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Agregar el inicializador

Reemplace su contenido por el código siguiente:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

En el código anterior, el método `Main` se ha modificado para hacer lo siguiente:

* Obtener una instancia del contexto de base de datos desde el contenedor de inserción de dependencias.
* Llame al método `seedData.Initialize`, pasándole la instancia de contexto de la base de datos.
* Eliminar el contexto cuando el método de inicialización finalice. La [instrucción using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) garantiza que se elimine el contexto.

La siguiente excepción se produce cuando no se ha ejecutado `Update-Database`:

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>Prueba de la aplicación

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Delete todos los registros de la base de datos. Use los vínculos de eliminación en el explorador o en [SSOX](xref:tutorials/razor-pages/new-field#ssox).
* Obligue a la aplicación a inicializarse (llame a los métodos de la clase `Startup`) para que se ejecute el método de inicialización. Para forzar la inicialización, se debe detener y reiniciar IIS Express. Detenga y reinicie IIS con cualquiera de los métodos siguientes:

  * Haga clic con el botón derecho en el icono Bandeja del sistema de IIS Express del área de notificación y pulse en **Salir** o en **Detener sitio**:

    ![Icono Bandeja del sistema de IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menú contextual](sql/_static/stopIIS.png)

    * Si está ejecutando la aplicación en modo de no depuración, presione <kbd>F5</kbd> para ejecutar en modo de depuración.
    * Si la aplicación está en modo de depuración, detenga el depurador y presione <kbd>F5</kbd>.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Delete todos los registros de la base de datos, para que se ejecute el método de inicialización. Detenga e inicie la aplicación para inicializar la base de datos.

---

La aplicación muestra los datos inicializados:

![La aplicación Movie se abre en Chrome y muestra datos de la película](sql/_static/m55https.png)

## <a name="additional-resources"></a>Recursos adicionales

> [!div class="step-by-step"]
> [Anterior: Razor Pages con scaffolding](xref:tutorials/razor-pages/page)
> [Siguiente: Actualización de las páginas](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([cómo descargarlo](xref:index#how-to-download-a-sample)).

El objeto `RazorPagesMovieContext` controla la tarea de conexión a la base de datos y asignación de objetos `Movie` a los registros de la base de datos. El contexto de base de datos se registra con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en el método `ConfigureServices` de *Startup.cs*:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

Para más información sobre los modelos empleados en `ConfigureServices`, vea:

* [Compatibilidad con el Reglamento general de protección de datos (RGPD) en ASP.NET Core](xref:security/gdpr) para `CookiePolicyOptions`.
* [SetCompatibilityVersion](xref:mvc/compatibility-version)

El sistema [Configuración](xref:fundamentals/configuration/index) de ASP.NET Core lee la clave `ConnectionString`. Para el desarrollo local, la configuración obtiene la cadena de conexión del archivo *appsettings.json* .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

La cadena de conexión generada será similar a la siguiente:

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Cuando la aplicación se implementa en un servidor de prueba o producción, se puede utilizar una variable de entorno para establecer la cadena de conexión en un servidor de base de datos de prueba o producción. Para más información, vea [Configuración](xref:fundamentals/configuration/index).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB es una versión ligera del motor de base de datos de SQL Server Express dirigida al desarrollo de programas. LocalDB se inicia a petición y se ejecuta en modo de usuario, sin necesidad de una configuración compleja. De forma predeterminada, la base de datos LocalDB crea archivos `*.mdf` en el directorio `C:/Users/<user/>`.

<a name="ssox"></a>
* En el menú **Ver**, abra **Explorador de objetos de SQL Server** (SSOX).

  ![Menú Ver](sql/_static/ssox.png)

* Haga clic con el botón derecho en la tabla `Movie` y seleccione **Diseñador de vistas**:

  ![Menú contextual abierto en la tabla Movie](sql/_static/design.png)

  ![Tabla Movie abierta en el diseñador](sql/_static/dv.png)

Observe el icono de llave junto a `ID`. De forma predeterminada, EF crea una propiedad denominada `ID` para la clave principal.

* Haga clic con el botón derecho en la tabla `Movie` y seleccione **Ver datos**:

  ![Tabla Movie abierta mostrando datos de la tabla](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Inicializar la base de datos

Create una nueva clase denominada `SeedData` en la carpeta *Models* con el código siguiente:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

Si hay alguna película en la base de datos, se devuelve el inicializador y no se agrega ninguna película.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Agregar el inicializador

Reemplace su contenido por el código siguiente:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

En el código anterior, el método `Main` se ha modificado para hacer lo siguiente:

* Obtener una instancia del contexto de base de datos desde el contenedor de inserción de dependencias.
* Llame al método `seedData.Initialize`, pasándole la instancia de contexto de la base de datos.
* Eliminar el contexto cuando el método de inicialización finalice. La [instrucción using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) garantiza que se elimine el contexto.

Una aplicación de producción no llamaría a `Database.Migrate`. Se agrega al código anterior para evitar que se produzca la siguiente excepción cuando `Update-Database` no se ha ejecutado:

SqlException: No se puede abrir la base de datos "RazorPagesMovieContext-21" solicitada por el inicio de sesión. Error de inicio de sesión.
Error de inicio de sesión del usuario <nombre de usuario>.

### <a name="test-the-app"></a>Prueba de la aplicación

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Delete todos los registros de la base de datos. Puede hacerlo con los vínculos de eliminación en el explorador o desde [SSOX](xref:tutorials/razor-pages/new-field#ssox).
* Obligue a la aplicación a inicializarse (llame a los métodos de la clase `Startup`) para que se ejecute el método de inicialización. Para forzar la inicialización, se debe detener y reiniciar IIS Express. Puede hacerlo con cualquiera de los siguientes enfoques:

  * Haga clic con el botón derecho en el icono Bandeja del sistema de IIS Express del área de notificación y pulse en **Salir** o en **Detener sitio**:

    ![Icono Bandeja del sistema de IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menú contextual](sql/_static/stopIIS.png)

    * Si está ejecutando la aplicación en modo de no depuración, presione <kbd>F5</kbd> para ejecutar en modo de depuración.
    * Si la aplicación está en modo de depuración, detenga el depurador y presione <kbd>F5</kbd>.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Delete todos los registros de la base de datos, para que se ejecute el método de inicialización. Detenga e inicie la aplicación para inicializar la base de datos.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Delete todos los registros de la base de datos, para que se ejecute el método de inicialización. Detenga e inicie la aplicación para inicializar la base de datos.

---

La aplicación muestra los datos inicializados:

![La aplicación Movie se abre en Chrome y muestra datos de la película](sql/_static/m55https.png)

El siguiente tutorial limpia la presentación de los datos.

## <a name="additional-resources"></a>Recursos adicionales

* [Versión en YouTube de este tutorial](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [Anterior: Razor Pages con scaffolding](xref:tutorials/razor-pages/page)
> [Siguiente: Actualización de las páginas](xref:tutorials/razor-pages/da1)

::: moniker-end
