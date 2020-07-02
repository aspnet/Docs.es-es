---
title: 'Tutorial: Control de simultaneidad: ASP.NET MVC con EF Core'
description: Este tutorial muestra cómo tratar los conflictos cuando varios usuarios actualizan la misma entidad al mismo tiempo.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/concurrency
ms.openlocfilehash: 3038ae8f01273013e6c35694583d9674a1668bac
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401563"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a>Tutorial: Control de simultaneidad: ASP.NET MVC con EF Core

En los tutoriales anteriores, aprendió a actualizar los datos. Este tutorial muestra cómo tratar los conflictos cuando varios usuarios actualizan la misma entidad al mismo tiempo.

Podrá crear páginas web que funcionan con la entidad Department y controlan los errores de simultaneidad. Las siguientes ilustraciones muestran las páginas Edit y Delete, incluidos algunos mensajes que se muestran si se produce un conflicto de simultaneidad.

![Página Edit Department](concurrency/_static/edit-error.png)

![Página Department Delete](concurrency/_static/delete-error.png)

En este tutorial ha:

> [!div class="checklist"]
> * Obtiene información sobre los conflictos de simultaneidad
> * Agrega una propiedad de seguimiento
> * Crea un controlador y vistas de Departments
> * Actualiza la vista de índice
> * Actualiza los métodos de edición
> * Actualiza la vista de edición
> * Prueba los conflictos de simultaneidad
> * Actualizar la página Delete
> * Actualizar las vistas Details y Create

## <a name="prerequisites"></a>Requisitos previos

* [Actualización de datos relacionados](update-related-data.md)

## <a name="concurrency-conflicts"></a>Conflictos de simultaneidad

Los conflictos de simultaneidad ocurren cuando un usuario muestra los datos de una entidad para editarlos y, después, otro usuario actualiza los datos de la misma entidad antes de que el primer cambio del usuario se escriba en la base de datos. Si no habilita la detección de este tipo de conflictos, quien actualice la base de datos en último lugar sobrescribe los cambios del otro usuario. En muchas aplicaciones, el riesgo es aceptable: si hay pocos usuarios o pocas actualizaciones, o si no es realmente importante si se sobrescriben algunos cambios, el costo de programación para la simultaneidad puede superar el beneficio obtenido. En ese caso, no tendrá que configurar la aplicación para que controle los conflictos de simultaneidad.

### <a name="pessimistic-concurrency-locking"></a>Simultaneidad pesimista (bloqueo)

Si la aplicación necesita evitar la pérdida accidental de datos en casos de simultaneidad, una manera de hacerlo es usar los bloqueos de base de datos. Esto se denomina simultaneidad pesimista. Por ejemplo, antes de leer una fila de una base de datos, solicita un bloqueo de solo lectura o para acceso de actualización. Si bloquea una fila para acceso de actualización, no se permite que ningún otro usuario bloquee la fila como solo lectura o para acceso de actualización, porque recibirían una copia de los datos que se están modificando. Si bloquea una fila para acceso de solo lectura, otras personas también pueden bloquearla para acceso de solo lectura pero no para actualización.

Administrar los bloqueos tiene desventajas. Puede ser bastante complicado de programar. Se necesita un número significativo de recursos de administración de base de datos, y puede provocar problemas de rendimiento a medida que aumenta el número de usuarios de una aplicación. Por estos motivos, no todos los sistemas de administración de bases de datos admiten la simultaneidad pesimista. Entity Framework Core no proporciona ninguna compatibilidad integrada para ello y en este tutorial no se muestra cómo implementarla.

### <a name="optimistic-concurrency"></a>Simultaneidad optimista

La alternativa a la simultaneidad pesimista es la simultaneidad optimista. La simultaneidad optimista implica permitir que se produzcan conflictos de simultaneidad y reaccionar correctamente si ocurren. Por ejemplo, Jane visita la página Edit Department y cambia la cantidad de Budget para el departamento de inglés de 350.000,00 a 0,00 USD.

![Cambiar el presupuesto a 0](concurrency/_static/change-budget.png)

Antes de que Jane haga clic en **Save**, John visita la misma página y cambia el campo Start Date de 9/1/2007 a 9/1/2013.

![Cambiar la fecha de inicio a 2013](concurrency/_static/change-date.png)

Jane hace clic en **Save** primero y ve su cambio cuando el explorador vuelve a la página de índice.

![Presupuesto cambiado a cero](concurrency/_static/budget-zero.png)

Entonces, John hace clic en **Save** en una página Edit que sigue mostrando un presupuesto de 350.000,00 USD. Lo que sucede después viene determinado por cómo controla los conflictos de simultaneidad.

Algunas de las opciones se exponen a continuación:

* Puede realizar un seguimiento de la propiedad que ha modificado un usuario y actualizar solo las columnas correspondientes de la base de datos.

     En el escenario de ejemplo, no se perdería ningún dato porque los dos usuarios actualizaron diferentes propiedades. La próxima vez que un usuario examine el departamento de inglés, verá los cambios tanto de Jane como de John: una fecha de inicio de 9/1/2013 y un presupuesto de cero dólares. Este método de actualización puede reducir el número de conflictos que pueden dar lugar a una pérdida de datos, pero no puede evitar la pérdida de datos si se realizan cambios paralelos a la misma propiedad de una entidad. Si Entity Framework funciona de esta manera o no, depende de cómo implemente el código de actualización. A menudo no resulta práctico en una aplicación web, porque puede requerir mantener grandes cantidades de estado con el fin de realizar un seguimiento de todos los valores de propiedad originales de una entidad, así como los valores nuevos. Mantener grandes cantidades de estado puede afectar al rendimiento de la aplicación porque requiere recursos del servidor o se deben incluir en la propia página web (por ejemplo, en campos ocultos) o en una cookie.

* Puede permitir que los cambios de John sobrescriban los cambios de Jane.

     La próxima vez que un usuario examine el departamento de inglés, verá 9/1/2013 y el valor de 350.000,00 USD restaurado. Esto se denomina un escenario de *Prevalece el cliente* o *Prevalece el último*. (Todos los valores del cliente tienen prioridad sobre lo que aparece en el almacén de datos). Como se mencionó en la introducción de esta sección, si no hace ninguna codificación para el control de la simultaneidad, se realizará automáticamente.

* Puede evitar que el cambio de John se actualice en la base de datos.

     Por lo general, debería mostrar un mensaje de error, mostrarle el estado actual de los datos y permitirle volver a aplicar los cambios si quiere realizarlos. Esto se denomina un escenario de *Prevalece el almacén*. (Los valores del almacén de datos tienen prioridad sobre los valores enviados por el cliente). En este tutorial implementará el escenario de Prevalece el almacén. Este método garantiza que ningún cambio se sobrescriba sin que se avise al usuario de lo que está sucediendo.

### <a name="detecting-concurrency-conflicts"></a>Detectar los conflictos de simultaneidad

Puede resolver los conflictos controlando las excepciones `DbConcurrencyException` que inicia Entity Framework. Para saber cuándo se producen dichas excepciones, Entity Framework debe ser capaz de detectar conflictos. Por lo tanto, debe configurar correctamente la base de datos y el modelo de datos. Algunas opciones para habilitar la detección de conflictos son las siguientes:

* En la tabla de la base de datos, incluya una columna de seguimiento que pueda usarse para determinar si una fila ha cambiado. Después puede configurar Entity Framework para que incluya esa columna en la cláusula Where de los comandos Update o Delete de SQL.

     El tipo de datos de la columna de seguimiento suele ser `rowversion`. El valor `rowversion` es un número secuencial que se incrementa cada vez que se actualiza la fila. En un comando Update o Delete, la cláusula Where incluye el valor original de la columna de seguimiento (la versión de la fila original). Si otro usuario ha cambiado la fila que se está actualizando, el valor en la columna `rowversion` es diferente del valor original, por lo que la instrucción Update o Delete no puede encontrar la fila que se va a actualizar debido a la cláusula Where. Cuando Entity Framework encuentra que no se ha actualizado ninguna fila mediante el comando Update o Delete (es decir, cuando el número de filas afectadas es cero), lo interpreta como un conflicto de simultaneidad.

* Configure Entity Framework para que incluya los valores originales de cada columna de la tabla en la cláusula Where de los comandos Update y Delete.

     Como se muestra en la primera opción, si algo en la fila ha cambiado desde que se leyó por primera, la cláusula Where no devolverá una fila para actualizar, lo cual Entity Framework interpreta como un conflicto de simultaneidad. Para las tablas de base de datos que tienen muchas columnas, este enfoque puede dar lugar a cláusulas Where muy grandes y puede requerir mantener grandes cantidades de estado. Tal y como se indicó anteriormente, el mantenimiento de grandes cantidades de estado puede afectar al rendimiento de la aplicación. Por tanto, generalmente este enfoque no se recomienda y no es el método usado en este tutorial.

     Si quiere implementar este enfoque para la simultaneidad, tendrá que marcar todas las propiedades de clave no principal de la entidad de la que quiere realizar un seguimiento de simultaneidad agregándoles el atributo `ConcurrencyCheck`. Este cambio permite que Entity Framework incluya todas las columnas en la cláusula Where de SQL de las instrucciones Update y Delete.

En el resto de este tutorial agregará una propiedad de seguimiento `rowversion` para la entidad Department, creará un controlador y vistas, y comprobará que todo funciona correctamente.

## <a name="add-a-tracking-property"></a>Agrega una propiedad de seguimiento

En *Models/Department.cs*, agregue una propiedad de seguimiento denominada RowVersion:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

El atributo `Timestamp` especifica que esta columna se incluirá en la cláusula Where de los comandos Update y Delete enviados a la base de datos. El atributo se denomina `Timestamp` porque las versiones anteriores de SQL Server usaban un tipo de datos `timestamp` antes de que la `rowversion` de SQL lo sustituyera por otro. El tipo .NET de `rowversion` es una matriz de bytes.

Si prefiere usar la API fluida, puede usar el método `IsConcurrencyToken` (en *Data/SchoolContext.cs*) para especificar la propiedad de seguimiento, tal como se muestra en el ejemplo siguiente:

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

Al agregar una propiedad cambió el modelo de base de datos, por lo que necesita realizar otra migración.

Guarde los cambios, compile el proyecto y, después, escriba los siguientes comandos en la ventana de comandos:

```dotnetcli
dotnet ef migrations add RowVersion
```

```dotnetcli
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a>Crea un controlador y vistas de Departments

Aplique la técnica scaffolding a un controlador y vistas de Departments como lo hizo anteriormente para Students, Courses e Instructors.

![Aplicar la técnica scaffolding a Department](concurrency/_static/add-departments-controller.png)

En el archivo *DepartmentsController.cs*, cambie las cuatro repeticiones de "FirstMidName" a "FullName" para que las listas desplegables del administrador del departamento contengan el nombre completo del instructor en lugar de simplemente el apellido.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a>Actualiza la vista de índice

El motor de scaffolding creó una columna RowVersion en la vista Index, pero ese campo no debería mostrarse.

Reemplace el código de *Views/Departments/Index.cshtml* con el código siguiente.

[!code-html[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

Esto cambia el encabezado por "Departments", elimina la columna RowVersion y muestra el nombre completo en lugar del nombre del administrador.

## <a name="update-edit-methods"></a>Actualiza los métodos de edición

En el método `Edit` de HttpGet y el método `Details`, agregue `AsNoTracking`. En el método `Edit` de HttpGet, agregue carga diligente para el administrador.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

Sustituya el código existente para el método `Edit` de HttpPost por el siguiente código:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

El código comienza por intentar leer el departamento que se va a actualizar. Si el método `FirstOrDefaultAsync` devuelve NULL, otro usuario eliminó el departamento. En ese caso, el código usa los valores del formulario expuesto para crear una entidad Department, por lo que puede volver a mostrarse la página Edit con un mensaje de error. Como alternativa, no tendrá que volver a crear la entidad Department si solo muestra un mensaje de error sin volver a mostrar los campos del departamento.

La vista almacena el valor `RowVersion` original en un campo oculto, y este método recibe ese valor en el parámetro `rowVersion`. Antes de llamar a `SaveChanges`, tendrá que colocar dicho valor de propiedad `RowVersion` original en la colección `OriginalValues` para la entidad.

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

Cuando Entity Framework crea un comando UPDATE de SQL, ese comando incluirá una cláusula WHERE que comprueba si hay una fila que tenga el valor `RowVersion` original. Si no hay ninguna fila afectada por el comando UPDATE (ninguna fila tiene el valor `RowVersion` original), Entity Framework inicia una excepción `DbUpdateConcurrencyException`.

El código del bloque catch de esa excepción obtiene la entidad Department afectada que tiene los valores actualizados de la propiedad `Entries` del objeto de excepción.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

La colección `Entries` contará con un solo objeto `EntityEntry`.  Puede usar dicho objeto para obtener los nuevos valores especificados por el usuario y los valores actuales de la base de datos.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

El código agrega un mensaje de error personalizado para cada columna que tenga valores de base de datos diferentes de lo que el usuario especificó en la página Edit (aquí solo se muestra un campo por razones de brevedad).

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

Por último, el código establece el valor `RowVersion` de `departmentToUpdate` para el nuevo valor recuperado de la base de datos. Este nuevo valor `RowVersion` se almacenará en el campo oculto cuando se vuelva a mostrar la página Edit y, la próxima vez que el usuario haga clic en **Save**, solo se detectarán los errores de simultaneidad que se produzcan desde que se vuelva a mostrar la página Edit.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

La instrucción `ModelState.Remove` es necesaria porque `ModelState` tiene el valor `RowVersion` antiguo. En la vista, el valor `ModelState` de un campo tiene prioridad sobre los valores de propiedad de modelo cuando ambos están presentes.

## <a name="update-edit-view"></a>Actualiza la vista de edición

En *Views/Departments/Edit.cshtml*, realice los cambios siguientes:

* Agregue un campo oculto para guardar el valor de propiedad `RowVersion`, inmediatamente después de un campo oculto para la propiedad `DepartmentID`.

* Agregue una opción "Select Administrator" a la lista desplegable.

[!code-html[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a>Prueba los conflictos de simultaneidad

Ejecute la aplicación y vaya a la página de índice de Departments. Haga clic con el botón derecho en el hipervínculo **Edit** del departamento de inglés, seleccione **Abrir en nueva pestaña** y, después, haga clic en el hipervínculo **Edit** del departamento de inglés. Las dos pestañas del explorador ahora muestran la misma información.

Cambie un campo en la primera pestaña del explorador y haga clic en **Save**.

![Página 1 de Department Edit después del cambio](concurrency/_static/edit-after-change-1.png)

El explorador muestra la página de índice con el valor modificado.

Cambie un campo en la segunda pestaña del explorador.

![Página 2 de Department Edit después del cambio](concurrency/_static/edit-after-change-2.png)

Haga clic en **Guardar**. Verá un mensaje de error:

![Mensaje de error de la página Department Edit](concurrency/_static/edit-error.png)

Vuelva a hacer clic en **Save**. Se guarda el valor especificado en la segunda pestaña del explorador. Verá los valores guardados cuando aparezca la página de índice.

## <a name="update-the-delete-page"></a>Actualizar la página Delete

Para la página Delete, Entity Framework detecta los conflictos de simultaneidad causados por una persona que edita el departamento de forma similar. Cuando el método `Delete` de HttpGet muestra la vista de confirmación, la vista incluye el valor `RowVersion` original en un campo oculto. Dicho valor está entonces disponible para el método `Delete` de HttpPost al que se llama cuando el usuario confirma la eliminación. Cuando Entity Framework crea el comando DELETE de SQL, incluye una cláusula WHERE con el valor `RowVersion` original. Si el comando tiene como resultado cero filas afectadas (es decir, la fila se cambió después de que se muestre la página de confirmación de eliminación), se produce una excepción de simultaneidad y el método `Delete` de HttpGet se llama con una marca de error establecida en true para volver a mostrar la página de confirmación con un mensaje de error. También es posible que se vieran afectadas cero filas porque otro usuario eliminó la fila, por lo que en ese caso no se muestra ningún mensaje de error.

### <a name="update-the-delete-methods-in-the-departments-controller"></a>Actualizar los métodos Delete en el controlador de Departments

En *DepartmentsController.cs*, reemplace el método `Delete` de HttpGet por el código siguiente:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

El método acepta un parámetro opcional que indica si la página volverá a aparecer después de un error de simultaneidad. Si esta marca es true y el departamento especificado ya no existe, significa que otro usuario lo eliminó. En ese caso, el código redirige a la página de índice.  Si esta marca es true y el departamento existe, significa que otro usuario lo modificó. En ese caso, el código envía un mensaje de error a la vista mediante `ViewData`.

Reemplace el código en el método `Delete` de HttpPost (denominado `DeleteConfirmed`) con el código siguiente:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

En el código al que se aplicó la técnica scaffolding que acaba de reemplazar, este método solo acepta un identificador de registro:

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

Ha cambiado este parámetro en una instancia de la entidad Department creada por el enlazador de modelos. Esto proporciona a EF acceso al valor de la propiedad RowVersion, además de la clave de registro.

```csharp
public async Task<IActionResult> Delete(Department department)
```

También ha cambiado el nombre del método de acción de `DeleteConfirmed` a `Delete`. El código al que se aplicó la técnica scaffolding usa el nombre `DeleteConfirmed` para proporcionar al método HttpPost una firma única. (El CLR requiere métodos sobrecargados para tener parámetros de método diferentes). Ahora que las firmas son únicas, puede ceñirse a la convención MVC y usar el mismo nombre para los métodos de eliminación de HttpPost y HttpGet.

Si ya se ha eliminado el departamento, el método `AnyAsync` devuelve false y la aplicación simplemente vuelve al método de índice.

Si se detecta un error de simultaneidad, el código vuelve a mostrar la página de confirmación de Delete y proporciona una marca que indica que se debería mostrar un mensaje de error de simultaneidad.

### <a name="update-the-delete-view"></a>Actualizar la vista Delete

En *Views/Departments/Delete.cshtml*, reemplace el código al que se aplicó la técnica scaffolding con el siguiente código, que agrega un campo de mensaje de error y campos ocultos para las propiedades DepartmentID y RowVersion. Los cambios aparecen resaltados.

[!code-html[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

Esto realiza los cambios siguientes:

* Agrega un mensaje de error entre los encabezados `h2` y `h3`.

* Reemplaza FirstMidName por FullName en el campo **Administrator**.

* Quita el campo RowVersion.

* Agrega un campo oculto para la propiedad `RowVersion`.

Ejecute la aplicación y vaya a la página de índice de Departments. Haga clic con el botón derecho en el hipervínculo **Delete** del departamento de inglés, seleccione **Abrir en nueva pestaña** y, después, en la primera pestaña, haga clic en el hipervínculo **Edit** del departamento de inglés.

En la primera ventana, cambie uno de los valores y haga clic en **Save**:

![Página Department Edit después del cambio antes de eliminar](concurrency/_static/edit-after-change-for-delete.png)

En la segunda pestaña, haga clic en **Delete**. Verá el mensaje de error de simultaneidad y se actualizarán los valores de Department con lo que está actualmente en la base de datos.

![Página de confirmación de Department Delete con error de simultaneidad](concurrency/_static/delete-error.png)

Si vuelve a hacer clic en **Delete**, se le redirigirá a la página de índice, que muestra que se ha eliminado el departamento.

## <a name="update-details-and-create-views"></a>Actualizar las vistas Details y Create

Si quiere, puede limpiar el código al que se ha aplicado la técnica scaffolding en las vistas Details y Create.

Reemplace el código de *Views/Departments/Details.cshtml* para eliminar la columna RowVersion y mostrar el nombre completo del administrador.

[!code-html[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

Reemplace el código de *Views/Departments/Create.cshtml* para agregar una opción Select en la lista desplegable.

[!code-html[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a>Obtención del código

[Descargue o vea la aplicación completa.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Recursos adicionales

 Para obtener más información sobre cómo administrar la simultaneidad en EF Core, vea [Concurrency conflicts](/ef/core/saving/concurrency) (Conflictos de simultaneidad).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha:

> [!div class="checklist"]
> * Obtenido información sobre los conflictos de simultaneidad
> * Agregado una propiedad de seguimiento
> * Creado un controlador y vistas de Departments
> * Actualizado la vista de índice
> * Actualizado los métodos de edición
> * Actualizado la vista de edición
> * Probado los conflictos de simultaneidad
> * Actualizado la página Delete
> * Actualizado las vistas Details y Create

Pase al tutorial siguiente para obtener información sobre cómo implementar la herencia de tabla por jerarquía para las entidades Instructor y Student.

> [!div class="nextstepaction"]
> [Siguiente: Implementación de la herencia de tabla por jerarquía](inheritance.md)
