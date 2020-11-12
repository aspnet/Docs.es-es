---
title: Parte 5. Trabajo con una base de datos en una aplicación de ASP.NET Core MVC
author: rick-anderson
description: Parte 5. Adición de un modelo a una aplicación de ASP.NET Core MVC
ms.author: riande
ms.date: 11/10/2020
no-loc:
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
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: c082457258346637499b8ea8000dd8792931112f
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422657"
---
# <a name="part-5-work-with-a-database-in-an-aspnet-core-mvc-app"></a>Parte 5. Trabajo con una base de datos en una aplicación de ASP.NET Core MVC

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

El objeto `MvcMovieContext` controla la tarea de conexión a la base de datos y asignación de objetos `Movie` a los registros de la base de datos. El contexto de base de datos se registra con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en el método `ConfigureServices` del archivo *Startup.cs* :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

El sistema [Configuración](xref:fundamentals/configuration/index) de ASP.NET Core lee el elemento `ConnectionString`. Para el desarrollo local, obtiene la cadena de conexión del archivo *appsettings.json* :

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

El sistema [Configuración](xref:fundamentals/configuration/index) de ASP.NET Core lee el elemento `ConnectionString`. Para el desarrollo local, obtiene la cadena de conexión del archivo *appsettings.json* :

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

Cuando la aplicación se implementa en un servidor de prueba o producción, se puede usar una variable de entorno para establecer la cadena de conexión en un servidor SQL Server de producción. Para más información, vea [Configuración](xref:fundamentals/configuration/index).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB es una versión ligera del motor de base de datos de SQL Server Express dirigida al desarrollo de programas. LocalDB se inicia a petición y se ejecuta en modo de usuario, sin necesidad de una configuración compleja. De forma predeterminada, la base de datos LocalDB crea archivos *.mdf* en el directorio *C:/Users/{usuario}* .

* En el menú **Ver** , abra **Explorador de objetos de SQL Server** (SSOX).

  ![Menú Ver](working-with-sql/_static/ssox.png)

* Haga clic con el botón derecho en la tabla `Movie` **> Diseñador de vistas**.

  ![Menú contextual abierto en la tabla Movie](working-with-sql/_static/design.png)

  ![Tabla Movie abierta en el diseñador](working-with-sql/_static/dv.png)

Observe el icono de llave junto a `ID`. De forma predeterminada, EF convierte una propiedad denominada `ID` en la clave principal.

* Haga clic con el botón derecho en la tabla `Movie` **> Ver datos**

  ![Menú contextual abierto en la tabla Movie](working-with-sql/_static/ssox2.png)

  ![Tabla Movie abierta mostrando datos de la tabla](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a>Inicializar la base de datos

Cree una nueva clase denominada `SeedData` en la carpeta *Models*. Reemplace el código generado con el siguiente:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

Si hay alguna película en la base de datos, se devuelve el inicializador y no se agrega ninguna película.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Agregar el inicializador

Reemplace el contenido de *Program.cs* por el código siguiente:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

Prueba de la aplicación

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Elimine todos los registros de la base de datos. Puede hacerlo con los vínculos de eliminación en el explorador o desde SSOX.
* Obligue a la aplicación a inicializarse (llame a los métodos de la clase `Startup`) para que se ejecute el método de inicialización. Para forzar la inicialización, se debe detener y reiniciar IIS Express. Puede hacerlo con cualquiera de los siguientes enfoques:

  * Haga clic con el botón derecho en el icono Bandeja del sistema de IIS Express del área de notificación y pulse en **Salir** o en **Detener sitio**.

    ![Icono Bandeja del sistema de IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Menú contextual](working-with-sql/_static/stopIIS.png)

    * Si está ejecutando VS en modo de no depuración, presione F5 para ejecutar en modo de depuración
    * Si está ejecutando VS en modo de depuración, detenga el depurador y presione F5

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Elimine todos los registros de la base de datos (para que se ejecute el método de inicialización). Detenga e inicie la aplicación para inicializar la base de datos.

---

La aplicación muestra los datos inicializados.

![La aplicación Movie de MVC se abre en Microsoft Edge y muestra datos de la película](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> [Anterior](adding-model.md)
> [Siguiente](controller-methods-views.md)
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

El objeto `MvcMovieContext` controla la tarea de conexión a la base de datos y asignación de objetos `Movie` a los registros de la base de datos. El contexto de base de datos se registra con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en el método `ConfigureServices` del archivo *Startup.cs* :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

El sistema [Configuración](xref:fundamentals/configuration/index) de ASP.NET Core lee el elemento `ConnectionString`. Para el desarrollo local, obtiene la cadena de conexión del archivo *appsettings.json* :

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

El sistema [Configuración](xref:fundamentals/configuration/index) de ASP.NET Core lee el elemento `ConnectionString`. Para el desarrollo local, obtiene la cadena de conexión del archivo *appsettings.json* :

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

Al implementar la aplicación en un servidor de producción o de prueba, puede usar una variable de entorno u otro enfoque para establecer la cadena de conexión en una instancia real de SQL Server. Para más información, vea [Configuración](xref:fundamentals/configuration/index).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB es una versión ligera del motor de base de datos de SQL Server Express dirigida al desarrollo de programas. LocalDB se inicia a petición y se ejecuta en modo de usuario, sin necesidad de una configuración compleja. De forma predeterminada, la base de datos LocalDB crea archivos *.mdf* en el directorio *C:/Users/{usuario}* .

* En el menú **Ver** , abra **Explorador de objetos de SQL Server** (SSOX).

  ![Menú Ver](working-with-sql/_static/ssox.png)

* Haga clic con el botón derecho en la tabla `Movie` **> Diseñador de vistas**.

  ![Menú contextual abierto en la tabla Movie](working-with-sql/_static/design.png)

  ![Tabla Movie abierta en el diseñador](working-with-sql/_static/dv.png)

Observe el icono de llave junto a `ID`. De forma predeterminada, EF convierte una propiedad denominada `ID` en la clave principal.

* Haga clic con el botón derecho en la tabla `Movie` **> Ver datos**

  ![Menú contextual abierto en la tabla Movie](working-with-sql/_static/ssox2.png)

  ![Tabla Movie abierta mostrando datos de la tabla](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a>Inicializar la base de datos

Cree una nueva clase denominada `SeedData` en la carpeta *Models*. Reemplace el código generado con el siguiente:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

Si hay alguna película en la base de datos, se devuelve el inicializador y no se agrega ninguna película.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Agregar el inicializador

Reemplace el contenido de *Program.cs* por el código siguiente:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

Prueba de la aplicación

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Elimine todos los registros de la base de datos. Puede hacerlo con los vínculos de eliminación en el explorador o desde SSOX.
* Obligue a la aplicación a inicializarse (llame a los métodos de la clase `Startup`) para que se ejecute el método de inicialización. Para forzar la inicialización, se debe detener y reiniciar IIS Express. Puede hacerlo con cualquiera de los siguientes enfoques:

  * Haga clic con el botón derecho en el icono Bandeja del sistema de IIS Express del área de notificación y pulse en **Salir** o en **Detener sitio**.

    ![Icono Bandeja del sistema de IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Menú contextual](working-with-sql/_static/stopIIS.png)

    * Si está ejecutando VS en modo de no depuración, presione F5 para ejecutar en modo de depuración
    * Si está ejecutando VS en modo de depuración, detenga el depurador y presione F5

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Elimine todos los registros de la base de datos (para que se ejecute el método de inicialización). Detenga e inicie la aplicación para inicializar la base de datos.

---

La aplicación muestra los datos inicializados.

![La aplicación Movie de MVC se abre en Microsoft Edge y muestra datos de la película](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> [Anterior](adding-model.md)
> [Siguiente](controller-methods-views.md)

::: moniker-end
