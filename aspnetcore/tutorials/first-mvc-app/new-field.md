---
title: Parte 8. Adición de un nuevo campo a una aplicación de ASP.NET Core MVC
author: rick-anderson
description: Parte 8 de la serie de tutoriales sobre ASP.NET Core MVC.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: acd5d38f156dbcd23a82796bdded051a103880eb
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2020
ms.locfileid: "86213096"
---
# <a name="part-8-add-a-new-field-to-an-aspnet-core-mvc-app"></a>Parte 8. Adición de un nuevo campo a una aplicación de ASP.NET Core MVC

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

En esta sección, Migraciones de [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First se utiliza para:

* Agregar un campo nuevo al modelo.
* Migrar el nuevo campo a la base de datos.

Al usar Code First de EF para crear una base de datos automáticamente, Code First hace lo siguiente:

* Agrega una tabla a la base de datos para realizar un seguimiento del esquema de la base de datos.
* Comprueba que la base de datos está sincronizada con las clases del modelo desde las que se ha generado. Si no está sincronizado, EF produce una excepción. Esto facilita la detección de problemas de código o base de datos incoherentes.

## <a name="add-a-rating-property-to-the-movie-model"></a>Adición de una propiedad de clasificación al modelo Movie

Agregue una `Rating` propiedad a *Models/Movie.cs*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Compilar la aplicación

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

 Ctrl+Mayús+B

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Comando (⌘) + B

------

Dado que ha agregado un nuevo campo a la clase `Movie`, debe actualizar la lista de enlaces permitidos para que se incluya esta nueva propiedad. En *MoviesController.cs*, actualice el atributo `[Bind]` de los métodos de acción `Create` y `Edit` para incluir la propiedad `Rating`:

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

Actualice las plantillas de vista para mostrar, crear y editar la nueva propiedad `Rating` en la vista del explorador.

Edite el archivo */Views/Movies/Index.cshtml* y agregue un campo `Rating`:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

Actualice */Views/Movies/Create.cshtml* con un campo `Rating`.

# <a name="visual-studio--visual-studio-for-mac"></a>[Visual Studio/Visual Studio para Mac](#tab/visual-studio+visual-studio-mac)

Puede copiar o pegar el elemento "form group" anterior y permitir que IntelliSense le ayude a actualizar los campos. IntelliSense funciona con [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro).

![El desarrollador ha escrito la letra R para el valor del atributo de asp-for en el segundo elemento de etiqueta de la vista. Ha aparecido un menú contextual de IntelliSense que muestra los campos disponibles, incluido Rating, que se resalta en la lista automáticamente. Cuando el programador hace clic en el campo o presiona Entrar en el teclado, el valor se establece en Rating.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

Actualice el resto de las plantillas.

Actualice la clase `SeedData` para que proporcione un valor para la nueva columna. A continuación se muestra un cambio de ejemplo, aunque es conveniente realizarlo con cada `new Movie`.

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

La aplicación no funciona hasta que la base de datos se actualiza para incluir el nuevo campo. Si se ejecuta ahora, se produce la siguiente `SqlException`:

`SqlException: Invalid column name 'Rating'.`

Este error se produce porque la clase del modelo Movie actualizada es diferente a la del esquema de la tabla Movie de la base de datos existente. (No hay ninguna columna `Rating` en la tabla de la base de datos).

Este error se puede resolver de varias maneras:

1. Haga que Entity Framework quite de forma automática la base de datos y la vuelva a crear basándose en el nuevo esquema de la clase del modelo. Este enfoque resulta muy conveniente al principio del ciclo de desarrollo cuando se realiza el desarrollo activo en una base de datos de prueba; permite desarrollar rápidamente el esquema del modelo y la base de datos juntos. La desventaja es que se pierden los datos existentes en la base de datos, así que no use este enfoque en una base de datos de producción. Usar un inicializador para inicializar automáticamente una base de datos con datos de prueba suele ser una manera productiva de desarrollar una aplicación. Se trata de un buen enfoque para el desarrollo inicial y cuando se usa SQLite.

2. Modifique explícitamente el esquema de la base de datos existente para que coincida con las clases del modelo. La ventaja de este enfoque es que se conservan los datos. Puede realizar este cambio de forma manual o mediante la creación de un script de cambio de base de datos.

3. Use Migraciones de Code First para actualizar el esquema de la base de datos.

En este tutorial se usa Migraciones de Code First.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Consola del Administrador de paquetes**.

  ![Menú de PMC](adding-model/_static/pmc.png)

En PCM, escriba los siguientes comandos:

```powershell
Add-Migration Rating
Update-Database
```

El comando `Add-Migration` indica el marco de trabajo de migración para examinar el modelo `Movie` actual con el esquema de base de datos `Movie` actual y para crear el código con el que se migrará la base de datos al nuevo modelo.

El nombre "Rating" es arbitrario y se usa para asignar nombre al archivo de migración. Resulta útil emplear un nombre descriptivo para el archivo de migración.

Si se eliminan todos los registros de la base de datos, el método de inicialización inicializa la base de datos e incluye el campo `Rating`.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Elimine la base de datos y use las migraciones para volver a crear la base de datos. Para eliminar la base de datos, elimine el archivo de base de datos (*MvcMovie.db*). Luego, ejecute el comando `ef database update`:

```dotnetcli
dotnet ef database update
```

---
<!-- End of VS tabs -->

Ejecute la aplicación y compruebe que puede crear, editar o mostrar vídeos con un campo `Rating`.

> [!div class="step-by-step"]
> [Anterior](search.md)
> [Siguiente](validation.md)
