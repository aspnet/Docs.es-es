---
title: 'Parte 8. Razor Pages con EF Core en ASP.NET Core: Simultaneidad'
author: rick-anderson
description: Parte 8 de la serie de tutoriales sobre Razor Pages y Entity Framework.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
no-loc:
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
uid: data/ef-rp/concurrency
ms.openlocfilehash: e03711d970c83c2b7d6cc76039cb0d556a751018
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628917"
---
# <a name="part-8-no-locrazor-pages-with-ef-core-in-aspnet-core---concurrency"></a>Parte 8. Razor Pages con EF Core en ASP.NET Core: Simultaneidad

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra) y [Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Este tutorial muestra cómo tratar los conflictos cuando varios usuarios actualizan una entidad de forma simultánea (al mismo tiempo).

## <a name="concurrency-conflicts"></a>Conflictos de simultaneidad

Un conflicto de simultaneidad se produce cuando:

* Un usuario va a la página de edición de una entidad.
* Otro usuario actualiza la misma entidad antes de que el cambio del primer usuario se escriba en la base de datos.

Si la detección de simultaneidad no está habilitada, quien actualice la base de datos en último lugar sobrescribe los cambios del otro usuario. Si este riesgo es aceptable, es posible que el costo de programar la simultaneidad supere las ventajas.

### <a name="pessimistic-concurrency-locking"></a>Simultaneidad pesimista (bloqueo)

Una manera de evitar conflictos de simultaneidad consiste en usar bloqueos de base de datos. Esto se denomina simultaneidad pesimista. Antes de que la aplicación lea una fila de base de datos que pretende actualizar, solicita un bloqueo. Una vez que una fila está bloqueada para el acceso de actualización, ningún otro usuario puede bloquear la fila hasta que se libere el primer bloqueo.

Administrar los bloqueos tiene desventajas. Puede ser complejo de programar y causar problemas de rendimiento a medida que aumente el número de usuarios. Entity Framework Core no proporciona ninguna compatibilidad integrada para ello y en este tutorial no se muestra cómo implementarla.

### <a name="optimistic-concurrency"></a>Simultaneidad optimista

La simultaneidad optimista permite que se produzcan conflictos de simultaneidad y luego reacciona correctamente si ocurren. Por ejemplo, Jane visita la página de edición de Department y cambia el presupuesto para el departamento de inglés de 350.000,00 a 0,00 USD.

![Cambiar el presupuesto a 0](concurrency/_static/change-budget30.png)

Antes de que Jane haga clic en **Save**, John visita la misma página y cambia el campo Start Date de 9/1/2007 a 9/1/2013.

![Cambiar la fecha de inicio a 2013](concurrency/_static/change-date30.png)

Primero, Jane hace clic en **Guardar** y ve que el cambio surte efecto, ya que en el explorador se muestra la página de índice con el valor cero como la cantidad del presupuesto.

John hace clic en **Save** en una página Edit que sigue mostrando un presupuesto de 350.000,00 USD. Lo que sucede después viene determinado por cómo se controlan los conflictos de simultaneidad:

* Puede realizar un seguimiento de la propiedad que ha modificado un usuario y actualizar solo las columnas correspondientes de la base de datos.

  En el escenario, no se perderá ningún dato. Los dos usuarios actualizaron diferentes propiedades. La próxima vez que un usuario examine el departamento de inglés, verá los cambios tanto de Jane como de John. Este método de actualización puede reducir el número de conflictos que pueden dar lugar a una pérdida de datos. Este enfoque presenta algunos inconvenientes:
 
  * No puede evitar la pérdida de datos si se realizan cambios paralelos a la misma propiedad.
  * Por lo general, no es práctico en una aplicación web. Requiere mantener un estado significativo para realizar un seguimiento de todos los valores capturados y nuevos. El mantenimiento de grandes cantidades de estado puede afectar al rendimiento de la aplicación.
  * Puede aumentar la complejidad de las aplicaciones en comparación con la detección de simultaneidad en una entidad.

* Puede permitir que los cambios de John sobrescriban los cambios de Jane.

  La próxima vez que un usuario examine el departamento de inglés, verá 9/1/2013 y el valor de 350.000,00 USD capturado. Este enfoque se denomina un escenario de *Prevalece el cliente* o *Prevalece el último*. (Todos los valores del cliente tienen prioridad sobre lo que aparece en el almacén de datos). Si no hace ninguna codificación para el control de la simultaneidad, Prevalece el cliente se realizará automáticamente.

* Puede evitar que el cambio de John se actualice en la base de datos. Normalmente, la aplicación podría:

  * Mostrar un mensaje de error.
  * Mostrar el estado actual de los datos.
  * Permitir al usuario volver a aplicar los cambios.

  Esto se denomina un escenario de *Prevalece el almacén*. (Los valores del almacén de datos tienen prioridad sobre los valores enviados por el cliente). En este tutorial implementará el escenario de Prevalece el almacén. Este método garantiza que ningún cambio se sobrescriba sin que se avise al usuario.

## <a name="conflict-detection-in-ef-core"></a>Detección de conflictos en EF Core

EF Core inicia excepciones `DbConcurrencyException` cuando detecta conflictos. El modelo de datos se debe configurar para habilitar la detección de conflictos. Las opciones para habilitar la detección de conflictos incluyen las siguientes:

* Configurar EF Core para incluir los valores originales de las columnas configuradas como [tokens de simultaneidad](/ef/core/modeling/concurrency) en la cláusula Where de los comandos Update y Delete.

  Cuando se llama a `SaveChanges`, la cláusula Where busca los valores originales de todas las propiedades anotadas con el atributo <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute>. La instrucción de actualización no encontrará una fila para actualizar si alguna de las propiedades de token de simultaneidad ha cambiado desde la primera vez que se ha leído la fila. EF Core interpreta que se trata de un conflicto de simultaneidad. Para las tablas de base de datos que tienen muchas columnas, este enfoque puede dar lugar a cláusulas Where muy grandes y puede requerir grandes cantidades de estado. Por tanto, generalmente este enfoque no se recomienda y no es el método usado en este tutorial.

* En la tabla de la base de datos, incluya una columna de seguimiento que pueda usarse para determinar si una fila ha cambiado.

  En una base de datos de SQL Server, el tipo de datos de la columna de seguimiento es `rowversion`. El valor `rowversion` es un número secuencial que se incrementa cada vez que se actualiza la fila. En un comando Update o Delete, la cláusula Where incluye el valor original de la columna de seguimiento (el número de versión de la fila original). Si otro usuario ha cambiado la fila que se va a actualizar, el valor de la columna `rowversion` es diferente del valor original. En ese caso, la instrucción Update o Delete no puede encontrar la fila para actualizar debido a la cláusula Where. EF Core inicia una excepción de simultaneidad cuando no hay ninguna fila afectada por un comando Update o Delete.

## <a name="add-a-tracking-property"></a>Agrega una propiedad de seguimiento

En *Models/Department.cs*, agregue una propiedad de seguimiento denominada RowVersion:

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

El atributo <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> es lo que identifica la columna como una columna de seguimiento de simultaneidad. La API fluida es una forma alternativa de especificar la propiedad de seguimiento:

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

En el caso de una base de datos de SQL Server, el atributo `[Timestamp]` de una propiedad de entidad definida como una matriz de bytes:

* Hace que la columna se incluya en las cláusulas WHERE de DELETE y UPDATE.
* Establece el tipo de columna de la base de datos en [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).

La base de datos genera un número de versión de fila secuencial que se incrementa cada vez que se actualiza la fila. En un comando `Update` o `Delete`, la cláusula `Where` incluye el valor de versión de fila capturado. Si la fila que se va a actualizar ha cambiado desde que se ha capturado:

* El valor de la versión de fila actual no coincide con el valor capturado.
* Los comandos `Update` o `Delete` no encuentran una fila porque la cláusula `Where` busca el valor de versión de la fila capturada.
* Se produce una excepción `DbUpdateConcurrencyException`.

El código siguiente muestra una parte del T-SQL generado por EF Core cuando se actualiza el nombre de Department:

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

El código resaltado anteriormente muestra la cláusula `WHERE` que contiene `RowVersion`. Si la base de datos `RowVersion` no es igual al parámetro `RowVersion` (`@p2`), no se ha actualizado ninguna fila.

El código resaltado a continuación muestra el T-SQL que comprueba que se actualizó exactamente una fila:

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) devuelve el número de filas afectadas por la última instrucción. Si no se actualiza ninguna fila, EF Core inicia una excepción `DbUpdateConcurrencyException`.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

En el caso de una base de datos de SQLite, el atributo `[Timestamp]` de una propiedad de entidad definida como una matriz de bytes:

* Hace que la columna se incluya en las cláusulas WHERE de DELETE y UPDATE.
* Se asigna a un tipo de columna BLOB.

Los desencadenadores de base de datos actualizan la columna RowVersion con una nueva matriz de bytes aleatoria cada vez que se actualiza una fila. En un comando `Update` o `Delete`, la cláusula `Where` incluye el valor capturado de la columna RowVersion. Si la fila que se va a actualizar ha cambiado desde que se ha capturado:

* El valor de la versión de fila actual no coincide con el valor capturado.
* El comando `Update` o `Delete` no encuentran una fila porque la cláusula `Where` busca el valor de versión de la fila original.
* Se produce una excepción `DbUpdateConcurrencyException`.

---

### <a name="update-the-database"></a>Actualizar la base de datos

Agregar la propiedad `RowVersion` cambia el modelo de datos, lo que requiere una migración.

Compile el proyecto. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ejecute el comando siguiente en la Consola del administrador de paquetes:

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ejecute el comando siguiente en un terminal:

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

Este comando:

* Crea el archivo de migración *Migrations/{marca de tiempo}_RowVersion.cs*.
* Actualizan el archivo *Migrations/SchoolContextModelSnapshot.cs*. La actualización agrega el siguiente código resaltado al método `BuildModel`:

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ejecute el comando siguiente en la Consola del administrador de paquetes:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Abra el archivo `Migrations/<timestamp>_RowVersion.cs` y agregue el código resaltado:

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  El código anterior:

  * Actualiza las filas existentes con valores de blob aleatorios.
  * Agrega desencadenadores de base de datos que establecen la columna RowVersion en un valor de blob aleatorio cada vez que se actualiza una fila.

* Ejecute el comando siguiente en un terminal:

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a>Scaffolding de las páginas Department

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Siga las instrucciones de [Scaffolding de las páginas Student](xref:data/ef-rp/intro#scaffold-student-pages) con las siguientes excepciones:

* Cree una carpeta *Pages/Departments*.  
* Use `Department` para la clase del modelo.
  * Use la clase de contexto existente en lugar de crear una.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Cree una carpeta *Pages/Departments*.

* Ejecute el comando siguiente para aplicar scaffolding a las páginas Department.

  **En Windows:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  **En Linux o macOS:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

Compile el proyecto.

## <a name="update-the-index-page"></a>Actualización de la página Index

La herramienta de scaffolding ha creado una columna `RowVersion` para la página de índice, pero ese campo no se debería mostrar en una aplicación de producción. En este tutorial, se muestra el último byte de `RowVersion` para ayudar a entender el funcionamiento de la simultaneidad. No se garantiza que el último byte sea único por si mismo.

Actualice la página *Pages\Departments\Index.cshtml*:

* Reemplace Index por Departments.
* Cambie el código que contiene `RowVersion` para mostrar solo el último byte de la matriz de bytes.
* Reemplace FirstMidName por FullName.

En el código siguiente se muestra la página actualizada:

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a>Actualizar el modelo de la página Edit

Actualice *Pages\Departments\Edit.cshtml.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> se actualiza con el valor `rowVersion` de la entidad cuando se capturó en el método `OnGet`. EF Core genera un comando UPDATE de SQL con una cláusula WHERE que contiene el valor `RowVersion` original. Si no hay ninguna fila afectada por el comando UPDATE (ninguna fila tiene el valor `RowVersion` original), se produce una excepción `DbUpdateConcurrencyException`.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

En el código resaltado anterior:

* El valor de `Department.RowVersion` es lo que estaba en la entidad cuando se capturó originalmente en la solicitud Get para la página Edit. El valor se proporciona al método `OnPost` por medio de un campo oculto en la página de Razor que muestra la entidad que se va a editar. El enlazador de modelos copia el valor del campo oculto en `Department.RowVersion`.
* `OriginalValue` es lo que EF Core usará en la cláusula Where. Antes de que se ejecute la línea de código resaltada, `OriginalValue` tiene el valor que estaba en la base de datos cuando se ha llamado a `FirstOrDefaultAsync` en este método, lo que podría ser diferente de lo que se mostraba en la página Edit.
* El código resaltado garantiza que EF Core usa el valor `RowVersion` original de la entidad `Department` mostrada en la cláusula Where de la instrucción UPDATE de SQL.

Cuando se produce un error de simultaneidad, el código resaltado siguiente obtiene los valores de cliente (los valores publicados para este método) y los valores de la base de datos.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

En el código siguiente se agrega un mensaje de error personalizado para cada columna que tiene valores de la base de datos diferentes a los publicados en `OnPostAsync`:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

En el código resaltado siguiente se establece el valor `RowVersion` en el nuevo valor recuperado de la base de datos. La próxima vez que el usuario haga clic en **Save**, solo se detectarán los errores de simultaneidad que se produzcan desde la última visualización de la página Edit.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

La instrucción `ModelState.Remove` es necesaria porque `ModelState` tiene el valor `RowVersion` antiguo. En la página de Razor, el valor `ModelState` de un campo tiene prioridad sobre los valores de propiedad de modelo cuando ambos están presentes.

### <a name="update-the-edit-page"></a>Actualizar la página Edit

Actualice *Pages/Departments/Edit.cshtml* con el código siguiente:

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

El código anterior:

* Se actualiza la directiva `page` de `@page` a `@page "{id:int}"`.
* Agrega una versión de fila oculta. Se debe agregar `RowVersion` para que el proceso postback enlace el valor.
* Muestra el último byte de `RowVersion` para fines de depuración.
* Reemplaza `ViewData` con `InstructorNameSL` fuertemente tipadas.

### <a name="test-concurrency-conflicts-with-the-edit-page"></a>Comprobar los conflictos de simultaneidad con la página Edit

Abra dos instancias de exploradores de Edit en el departamento de inglés:

* Ejecute la aplicación y seleccione Departments.
* Haga clic con el botón derecho en el hipervínculo **Edit** del departamento de inglés y seleccione **Abrir en nueva pestaña**.
* En la primera pestaña, haga clic en el hipervínculo **Edit** del departamento de inglés.

Las dos pestañas del explorador muestran la misma información.

Cambie el nombre en la primera pestaña del explorador y haga clic en **Save**.

![Página 1 de Department Edit después del cambio](concurrency/_static/edit-after-change-130.png)

El explorador muestra la página de índice con el valor modificado y el indicador rowVersion actualizado. Tenga en cuenta el indicador rowVersion actualizado, que se muestra en el segundo postback en la otra pestaña.

Cambie otro campo en la segunda pestaña del explorador.

![Página 2 de Department Edit después del cambio](concurrency/_static/edit-after-change-230.png)

Haga clic en **Guardar**. Verá mensajes de error para todos los campos que no coinciden con los valores de la base de datos:

![Mensaje de error de la página Department Edit](concurrency/_static/edit-error30.png)

Esta ventana del explorador no planeaba cambiar el campo Name. Copie y pegue el valor actual (Languages) en el campo Name. Presione TAB para salir del campo. La validación del lado cliente quita el mensaje de error.

Vuelva a hacer clic en **Save**. Se guarda el valor especificado en la segunda pestaña del explorador. Verá los valores guardados en la página de índice.

## <a name="update-the-delete-page-model"></a>Actualización del modelo de página Delete

Actualice *Pages/Departments/Delete.cshtml.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

La página Delete detecta los conflictos de simultaneidad cuando la entidad ha cambiado después de que se capturase. `Department.RowVersion` es la versión de fila cuando se capturó la entidad. Cuando EF Core crea el comando DELETE de SQL, incluye una cláusula WHERE con `RowVersion`. Si el comando DELETE de SQL tiene como resultado cero filas afectadas:

* El valor `RowVersion` del comando DELETE de SQL no coincide con el valor `RowVersion` de la base de datos.
* Se produce una excepción DbUpdateConcurrencyException.
* Se llama a `OnGetAsync` con el `concurrencyError`.

### <a name="update-the-delete-page"></a>Actualizar la página Delete

Actualice *Pages/Departments/Delete.cshtml* con el código siguiente:

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,42,51)]

En el código anterior se realizan los cambios siguientes:

* Se actualiza la directiva `page` de `@page` a `@page "{id:int}"`.
* Se agrega un mensaje de error.
* Se reemplaza FirstMidName por FullName en el campo **Administrator**.
* Se cambia `RowVersion` para que muestre el último byte.
* Agrega una versión de fila oculta. Se debe agregar `RowVersion` para que el proceso postback enlace el valor.

### <a name="test-concurrency-conflicts"></a>Prueba los conflictos de simultaneidad

Cree un departamento de prueba.

Abra dos instancias de exploradores de Delete en el departamento de prueba:

* Ejecute la aplicación y seleccione Departments.
* Haga clic con el botón derecho en el hipervínculo **Delete** del departamento de prueba y seleccione **Abrir en nueva pestaña**.
* Haga clic en el hipervínculo **Edit** del departamento de prueba.

Las dos pestañas del explorador muestran la misma información.

Cambie el presupuesto en la primera pestaña del explorador y haga clic en **Save**.

El explorador muestra la página de índice con el valor modificado y el indicador rowVersion actualizado. Tenga en cuenta el indicador rowVersion actualizado, que se muestra en el segundo postback en la otra pestaña.

Elimine el departamento de prueba de la segunda pestaña. Se mostrará un error de simultaneidad con los valores actuales de la base de datos. Al hacer clic en **Eliminar** se elimina la entidad, a menos que se haya actualizado `RowVersion`.

## <a name="additional-resources"></a>Recursos adicionales

* [Tokens de simultaneidad en EF Core](/ef/core/modeling/concurrency)
* [Controlar la simultaneidad en EF Core](/ef/core/saving/concurrency)
* [Depuración del código fuente de ASP.NET Core 2.x](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a>Pasos siguientes

Este es el último tutorial de la serie. En la [versión para MVC de esta serie de tutoriales](xref:data/ef-mvc/index) se describen temas adicionales.

> [!div class="step-by-step"]
> [Tutorial anterior](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Este tutorial muestra cómo tratar los conflictos cuando varios usuarios actualizan una entidad de forma simultánea (al mismo tiempo). Si experimenta problemas que no puede resolver, [descargue o vea la aplicación completada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). [Instrucciones de descarga](xref:index#how-to-download-a-sample).

## <a name="concurrency-conflicts"></a>Conflictos de simultaneidad

Un conflicto de simultaneidad se produce cuando:

* Un usuario va a la página de edición de una entidad.
* Otro usuario actualiza la misma entidad antes de que el cambio del primer usuario se escriba en la base de datos.

Si no está habilitada la detección de simultaneidad, cuando se produzcan actualizaciones simultáneas:

* Prevalece la última actualización. Es decir, los últimos valores de actualización se guardan en la base de datos.
* La primera de las actualizaciones actuales se pierde.

### <a name="optimistic-concurrency"></a>Simultaneidad optimista

La simultaneidad optimista permite que se produzcan conflictos de simultaneidad y luego reacciona correctamente si ocurren. Por ejemplo, Jane visita la página de edición de Department y cambia el presupuesto para el departamento de inglés de 350.000,00 a 0,00 USD.

![Cambiar el presupuesto a 0](concurrency/_static/change-budget.png)

Antes de que Jane haga clic en **Save**, John visita la misma página y cambia el campo Start Date de 9/1/2007 a 9/1/2013.

![Cambiar la fecha de inicio a 2013](concurrency/_static/change-date.png)

Jane hace clic en **Save** primero y ve su cambio cuando el explorador muestra la página de índice.

![Presupuesto cambiado a cero](concurrency/_static/budget-zero.png)

John hace clic en **Save** en una página Edit que sigue mostrando un presupuesto de 350.000,00 USD. Lo que sucede después viene determinado por cómo controla los conflictos de simultaneidad.

La simultaneidad optimista incluye las siguientes opciones:

* Puede realizar un seguimiento de la propiedad que ha modificado un usuario y actualizar solo las columnas correspondientes de la base de datos.

  En el escenario, no se perderá ningún dato. Los dos usuarios actualizaron diferentes propiedades. La próxima vez que un usuario examine el departamento de inglés, verá los cambios tanto de Jane como de John. Este método de actualización puede reducir el número de conflictos que pueden dar lugar a una pérdida de datos. Este enfoque:
 
  * No puede evitar la pérdida de datos si se realizan cambios paralelos a la misma propiedad.
  * Por lo general, no es práctico en una aplicación web. Requiere mantener un estado significativo para realizar un seguimiento de todos los valores capturados y nuevos. El mantenimiento de grandes cantidades de estado puede afectar al rendimiento de la aplicación.
  * Puede aumentar la complejidad de las aplicaciones en comparación con la detección de simultaneidad en una entidad.

* Puede permitir que los cambios de John sobrescriban los cambios de Jane.

  La próxima vez que un usuario examine el departamento de inglés, verá 9/1/2013 y el valor de 350.000,00 USD capturado. Este enfoque se denomina un escenario de *Prevalece el cliente* o *Prevalece el último*. (Todos los valores del cliente tienen prioridad sobre lo que aparece en el almacén de datos). Si no hace ninguna codificación para el control de la simultaneidad, Prevalece el cliente se realizará automáticamente.

* Puede evitar que el cambio de John se actualice en la base de datos. Normalmente, la aplicación podría:

  * Mostrar un mensaje de error.
  * Mostrar el estado actual de los datos.
  * Permitir al usuario volver a aplicar los cambios.

  Esto se denomina un escenario de *Prevalece el almacén*. (Los valores del almacén de datos tienen prioridad sobre los valores enviados por el cliente). En este tutorial implementará el escenario de Prevalece el almacén. Este método garantiza que ningún cambio se sobrescriba sin que se avise al usuario.

## <a name="handling-concurrency"></a>Administrar la simultaneidad 

Cuando una propiedad se configura como un [token de simultaneidad](/ef/core/modeling/concurrency):

* EF Core comprueba que no se ha modificado la propiedad después de que se capturase. La comprobación se produce cuando se llama a [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) o [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_).
* Si se ha cambiado la propiedad después de haberla capturado, se produce una excepción [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0). 

Deben configurarse el modelo de datos y la base de datos para que admitan producir una excepción `DbUpdateConcurrencyException`.

### <a name="detecting-concurrency-conflicts-on-a-property"></a>Detectar conflictos de simultaneidad en una propiedad

Se pueden detectar conflictos de simultaneidad en el nivel de propiedad con el atributo [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0). El atributo se puede aplicar a varias propiedades en el modelo. Para obtener más información, consulte [Anotaciones de datos: ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).

El atributo `[ConcurrencyCheck]` no se usa en este tutorial.

### <a name="detecting-concurrency-conflicts-on-a-row"></a>Detectar conflictos de simultaneidad en una fila

Para detectar conflictos de simultaneidad, se agrega al modelo una columna de seguimiento [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).  `rowversion` :

* Es específico de SQL Server. Otras bases de datos podrían no proporcionar una característica similar.
* Se usa para determinar que no se ha cambiado una entidad desde que se capturó de la base de datos. 

La base de datos genera un número `rowversion` secuencial que se incrementa cada vez que se actualiza la fila. En un comando `Update` o `Delete`, la cláusula `Where` incluye el valor capturado de `rowversion`. Si la fila que se está actualizando ha cambiado:

* `rowversion` no coincide con el valor capturado.
* Los comandos `Update` o `Delete` no encuentran una fila porque la cláusula `Where` incluye la `rowversion` capturada.
* Se produce una excepción `DbUpdateConcurrencyException`.

En EF Core, cuando un comando `Update` o `Delete` no han actualizado ninguna fila, se produce una excepción de simultaneidad.

### <a name="add-a-tracking-property-to-the-department-entity"></a>Agregar una propiedad de seguimiento a la entidad Department

En *Models/Department.cs*, agregue una propiedad de seguimiento denominada RowVersion:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

El atributo [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) especifica que esta columna se incluye en la cláusula `Where` de los comandos `Update` y `Delete`. El atributo se denomina `Timestamp` porque las versiones anteriores de SQL Server usaban un tipo de datos `timestamp` antes de que el tipo `rowversion` de SQL lo sustituyera por otro.

La API fluida también puede especificar la propiedad de seguimiento:

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

El código siguiente muestra una parte del T-SQL generado por EF Core cuando se actualiza el nombre de Department:

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

El código resaltado anteriormente muestra la cláusula `WHERE` que contiene `RowVersion`. Si la base de datos `RowVersion` no es igual al parámetro `RowVersion` (`@p2`), no se ha actualizado ninguna fila.

El código resaltado a continuación muestra el T-SQL que comprueba que se actualizó exactamente una fila:

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) devuelve el número de filas afectadas por la última instrucción. Si no se actualiza ninguna fila, EF Core produce una excepción `DbUpdateConcurrencyException`.

Puede ver el T-SQL que genera EF Core en la ventana de salida de Visual Studio.

### <a name="update-the-db"></a>Actualizar la base de datos

Agregar la propiedad `RowVersion` cambia el modelo de base de datos, lo que requiere una migración.

Compile el proyecto. Escriba lo siguiente en una ventana de comandos:

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

Los comandos anteriores:

* Agregan el archivo de migración *Migrations/{time stamp}_RowVersion.cs*.
* Actualizan el archivo *Migrations/SchoolContextModelSnapshot.cs*. La actualización agrega el siguiente código resaltado al método `BuildModel`:

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* Ejecutan las migraciones para actualizar la base de datos.

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a>Aplicar la técnica scaffolding al modelo Departments

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

Siga las instrucciones que encontrará en [Aplicación de scaffolding al modelo de alumnos](xref:data/ef-rp/intro#scaffold-student-pages) y use `Department` para la clase de modelo.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Ejecute el siguiente comando:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

El comando anterior aplica scaffolding al modelo `Department`. Abra el proyecto en Visual Studio.

Compile el proyecto.

### <a name="update-the-departments-index-page"></a>Actualizar la página de índice de Departments

El motor de scaffolding creó una columna `RowVersion` para la página de índice, pero ese campo no debería mostrarse. En este tutorial, el último byte de la `RowVersion` se muestra para ayudar a entender la simultaneidad. No se garantiza que el último byte sea único. Una aplicación real no mostraría `RowVersion` ni el último byte de `RowVersion`.

Actualice la página Index:

* Reemplace Index por Departments.
* Reemplace el marcado que contiene `RowVersion` por el último byte de `RowVersion`.
* Reemplace FirstMidName por FullName.

El marcado siguiente muestra la página actualizada:

[!code-cshtml[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a>Actualizar el modelo de la página Edit

Actualice *Pages\Departments\Edit.cshtml.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

Para detectar un problema de simultaneidad, el [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) se actualiza con el valor `rowVersion` de la entidad de la que se capturó. EF Core genera un comando UPDATE de SQL con una cláusula WHERE que contiene el valor `RowVersion` original. Si no hay ninguna fila afectada por el comando UPDATE (ninguna fila tiene el valor `RowVersion` original), se produce una excepción `DbUpdateConcurrencyException`.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

En el código anterior, `Department.RowVersion` es el valor cuando se capturó la entidad. `OriginalValue` es el valor de la base de datos cuando se llamó a `FirstOrDefaultAsync` en este método.

El código siguiente obtiene los valores de cliente (es decir, los valores registrados en este método) y los valores de la base de datos:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

El código siguiente agrega un mensaje de error personalizado para cada columna que tiene valores de la base de datos diferentes de lo que publicado en `OnPostAsync`:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

El código resaltado a continuación establece el valor `RowVersion` para el nuevo valor recuperado de la base de datos. La próxima vez que el usuario haga clic en **Save**, solo se detectarán los errores de simultaneidad que se produzcan desde la última visualización de la página Edit.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

La instrucción `ModelState.Remove` es necesaria porque `ModelState` tiene el valor `RowVersion` antiguo. En la página de Razor, el valor `ModelState` de un campo tiene prioridad sobre los valores de propiedad de modelo cuando ambos están presentes.

## <a name="update-the-edit-page"></a>Actualizar la página Edit

Actualice *Pages/Departments/Edit.cshtml* con el siguiente marcado:

[!code-cshtml[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

El marcado anterior:

* Actualiza la directiva `page` de `@page` a `@page "{id:int}"`.
* Agrega una versión de fila oculta. Se debe agregar `RowVersion` para que la devolución enlace el valor.
* Muestra el último byte de `RowVersion` para fines de depuración.
* Reemplaza `ViewData` con `InstructorNameSL` fuertemente tipadas.

## <a name="test-concurrency-conflicts-with-the-edit-page"></a>Comprobar los conflictos de simultaneidad con la página Edit

Abra dos instancias de exploradores de Edit en el departamento de inglés:

* Ejecute la aplicación y seleccione Departments.
* Haga clic con el botón derecho en el hipervínculo **Edit** del departamento de inglés y seleccione **Abrir en nueva pestaña**.
* En la primera pestaña, haga clic en el hipervínculo **Edit** del departamento de inglés.

Las dos pestañas del explorador muestran la misma información.

Cambie el nombre en la primera pestaña del explorador y haga clic en **Save**.

![Página 1 de Department Edit después del cambio](concurrency/_static/edit-after-change-1.png)

El explorador muestra la página de índice con el valor modificado y el indicador rowVersion actualizado. Tenga en cuenta el indicador rowVersion actualizado, que se muestra en el segundo postback en la otra pestaña.

Cambie otro campo en la segunda pestaña del explorador.

![Página 2 de Department Edit después del cambio](concurrency/_static/edit-after-change-2.png)

Haga clic en **Guardar**. Verá mensajes de error para todos los campos que no coinciden con los valores de la base de datos:

![Mensaje de error de la página Department Edit](concurrency/_static/edit-error.png)

Esta ventana del explorador no planeaba cambiar el campo Name. Copie y pegue el valor actual (Languages) en el campo Name. Presione TAB para salir del campo. La validación del lado cliente quita el mensaje de error.

![Mensaje de error de la página Department Edit](concurrency/_static/cv.png)

Vuelva a hacer clic en **Save**. Se guarda el valor especificado en la segunda pestaña del explorador. Verá los valores guardados en la página de índice.

## <a name="update-the-delete-page"></a>Actualizar la página Delete

Actualice el modelo de la página Delete con el código siguiente:

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

La página Delete detecta los conflictos de simultaneidad cuando la entidad ha cambiado después de que se capturase. `Department.RowVersion` es la versión de fila cuando se capturó la entidad. Cuando EF Core crea el comando DELETE de SQL, incluye una cláusula WHERE con `RowVersion`. Si el comando DELETE de SQL tiene como resultado cero filas afectadas:

* La `RowVersion` del comando DELETE de SQL no coincide con la `RowVersion` de la base de datos.
* Se produce una excepción DbUpdateConcurrencyException.
* Se llama a `OnGetAsync` con el `concurrencyError`.

### <a name="update-the-delete-page"></a>Actualizar la página Delete

Actualice *Pages/Departments/Delete.cshtml* con el código siguiente:

[!code-cshtml[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

En el código anterior se realizan los cambios siguientes:

* Se actualiza la directiva `page` de `@page` a `@page "{id:int}"`.
* Se agrega un mensaje de error.
* Se reemplaza FirstMidName por FullName en el campo **Administrator**.
* Se cambia `RowVersion` para que muestre el último byte.
* Se agrega una versión de fila oculta. Se debe agregar `RowVersion` para que la devolución enlace el valor.

### <a name="test-concurrency-conflicts-with-the-delete-page"></a>Comprobar los conflictos de simultaneidad con la página Delete

Cree un departamento de prueba.

Abra dos instancias de exploradores de Delete en el departamento de prueba:

* Ejecute la aplicación y seleccione Departments.
* Haga clic con el botón derecho en el hipervínculo **Delete** del departamento de prueba y seleccione **Abrir en nueva pestaña**.
* Haga clic en el hipervínculo **Edit** del departamento de prueba.

Las dos pestañas del explorador muestran la misma información.

Cambie el presupuesto en la primera pestaña del explorador y haga clic en **Save**.

El explorador muestra la página de índice con el valor modificado y el indicador rowVersion actualizado. Tenga en cuenta el indicador rowVersion actualizado, que se muestra en el segundo postback en la otra pestaña.

Elimine el departamento de prueba de la segunda pestaña. Se mostrará un error de simultaneidad con los valores actuales de la base de datos. Al hacer clic en **Eliminar** se elimina la entidad, a menos que se haya actualizado `RowVersion`.

Vea [Herencia](xref:data/ef-mvc/inheritance) para obtener información sobre cómo se hereda un modelo de datos.

### <a name="additional-resources"></a>Recursos adicionales

* [Tokens de simultaneidad en EF Core](/ef/core/modeling/concurrency)
* [Controlar la simultaneidad en EF Core](/ef/core/saving/concurrency)
* [Versión de YouTube de este tutorial (gestión de conflictos de simultaneidad)](https://youtu.be/EosxHTFgYps)
* [Versión en YouTube de este tutorial (parte 2)](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [Versión en YouTube de este tutorial (parte 3)](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [Anterior](xref:data/ef-rp/update-related-data)

::: moniker-end

