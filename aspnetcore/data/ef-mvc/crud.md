---
title: 'Tutorial: Implementación de la funcionalidad CRUD: ASP.NET MVC con EF Core'
description: En este tutorial, podrá revisar y personalizar el código CRUD (crear, leer, actualizar y eliminar) que el scaffolding de MVC crea automáticamente para usted en controladores y vistas.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2019
ms.topic: tutorial
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/crud
ms.openlocfilehash: 0be63811874709db95285f4013e47bc1706050b1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401485"
---
# <a name="tutorial-implement-crud-functionality---aspnet-mvc-with-ef-core"></a>Tutorial: Implementación de la funcionalidad CRUD: ASP.NET MVC con EF Core

En el tutorial anterior, creó una aplicación MVC que almacena y muestra los datos con Entity Framework y SQL Server LocalDB. En este tutorial, podrá revisar y personalizar el código CRUD (crear, leer, actualizar y eliminar) que el scaffolding de MVC crea automáticamente para usted en controladores y vistas.

> [!NOTE]
> Es una práctica habitual implementar el modelo de repositorio con el fin de crear una capa de abstracción entre el controlador y la capa de acceso a datos. Para que estos tutoriales sean sencillos y se centren en enseñar a usar Entity Framework, no se usan repositorios. Para obtener información sobre los repositorios con EF, vea [el último tutorial de esta serie](advanced.md).

En este tutorial ha:

> [!div class="checklist"]
> * Personalizar la página de detalles
> * Actualizar la página Create
> * Actualizar la página Edit
> * Actualizar la página Delete
> * Cerrar conexiones de bases de datos

## <a name="prerequisites"></a>Requisitos previos

* [Introducción a EF Core y ASP.NET Core MVC](intro.md)

## <a name="customize-the-details-page"></a>Personalizar la página de detalles

En el código con scaffolding de la página Students Index se excluyó la propiedad `Enrollments` porque contiene una colección. En la página **Details**, se mostrará el contenido de la colección en una tabla HTML.

En *Controllers/StudentsController.cs*, el método de acción para la vista Details usa el método `SingleOrDefaultAsync` para recuperar una única entidad `Student`. Agregue código para llamar a los métodos `Include`, `ThenInclude` y `AsNoTracking`, como se muestra en el siguiente código resaltado.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Details&highlight=8-12)]

Los métodos `Include` y `ThenInclude` hacen que el contexto cargue la propiedad de navegación `Student.Enrollments` y, dentro de cada inscripción, la propiedad de navegación `Enrollment.Course`.  Obtendrá más información sobre estos métodos en el tutorial de [lectura de datos relacionados](read-related-data.md).

El método `AsNoTracking` mejora el rendimiento en casos en los que no se actualizarán las entidades devueltas en la duración del contexto actual. Obtendrá más información sobre `AsNoTracking` al final de este tutorial.

### <a name="route-data"></a>Datos de ruta

El valor de clave que se pasa al método `Details` procede de los *datos de ruta*. Los datos de ruta son los que el enlazador de modelos encuentra en un segmento de la dirección URL. Por ejemplo, la ruta predeterminada especifica los segmentos de controlador, acción e identificador:

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Route&highlight=5)]

En la dirección URL siguiente, la ruta predeterminada asigna Instructor como el controlador, Index como la acción y 1 como el identificador; estos son los valores de datos de ruta.

```
http://localhost:1230/Instructor/Index/1?courseID=2021
```

La última parte de la dirección URL ("?courseID=2021") es un valor de cadena de consulta. El enlazador de modelos también pasará el valor ID al parámetro `id` del método `Index` si se pasa como un valor de cadena de consulta:

```
http://localhost:1230/Instructor/Index?id=1&CourseID=2021
```

En la página Index, las instrucciones de la aplicación auxiliar de etiquetas crean direcciones URL de hipervínculo en la vista de Razor. En el siguiente código de Razor, el parámetro `id` coincide con la ruta predeterminada, por lo que se agrega `id` a los datos de la ruta.

```html
<a asp-action="Edit" asp-route-id="@item.ID">Edit</a>
```

Esto genera el siguiente código HTML cuando `item.ID` es 6:

```html
<a href="/Students/Edit/6">Edit</a>
```

En el siguiente código de Razor, `studentID` no coincide con ningún parámetro de la ruta predeterminada, por lo que se agrega como una cadena de consulta.

```html
<a asp-action="Edit" asp-route-studentID="@item.ID">Edit</a>
```

Esto genera el siguiente código HTML cuando `item.ID` es 6:

```html
<a href="/Students/Edit?studentID=6">Edit</a>
```

Para obtener más información sobre los asistentes de etiquetas, vea <xref:mvc/views/tag-helpers/intro>.

### <a name="add-enrollments-to-the-details-view"></a>Agregar inscripciones a la vista de detalles

Abra *Views/Students/Details.cshtml*. Cada campo se muestra mediante los asistentes `DisplayNameFor` y `DisplayFor`, como se muestra en el ejemplo siguiente:

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=13-18&highlight=2,5)]

Después del último campo e inmediatamente antes de la etiqueta `</dl>` de cierre, agregue el código siguiente para mostrar una lista de las inscripciones:

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=31-52)]

Si la sangría de código no es correcta después de pegar el código, presione CTRL-K-D para corregirlo.

Este código recorre en bucle las entidades en la propiedad de navegación `Enrollments`. Para cada inscripción, se muestra el título del curso y la calificación. El título del curso se recupera de la entidad Course almacenada en la propiedad de navegación `Course` de la entidad Enrollments.

Ejecute la aplicación, haga clic en la pestaña **Students** y después en el vínculo **Details** de un estudiante. Verá la lista de cursos y calificaciones para el alumno seleccionado:

![Página de detalles del estudiante](crud/_static/student-details.png)

## <a name="update-the-create-page"></a>Actualizar la página Create

En *StudentsController.cs*, modifique el método HttpPost `Create` agregando un bloque try-catch y quitando ID del atributo `Bind`.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=4,6-7,14-21)]

En este código se agrega la entidad Student creada por el enlazador de modelos de ASP.NET Core MVC al conjunto de entidades Students y después se guardan los cambios en la base de datos. (El enlazador de modelos se refiere a la funcionalidad de ASP.NET Core MVC que facilita trabajar con datos enviados por un formulario; un enlazador de modelos convierte los valores de formulario enviados en tipos CLR y los pasa al método de acción en parámetros. En este caso, el enlazador de modelos crea instancias de una entidad Student mediante valores de propiedad de la colección Form).

Se ha quitado `ID` del atributo `Bind` porque ID es el valor de clave principal que SQL Server establecerá automáticamente cuando se inserte la fila. La entrada del usuario no establece el valor ID.

Aparte del atributo `Bind`, el bloque try-catch es el único cambio que se ha realizado en el código con scaffolding. Si se detecta una excepción derivada de `DbUpdateException` mientras se guardan los cambios, se muestra un mensaje de error genérico. En ocasiones, las excepciones `DbUpdateException` se deben a algo externo a la aplicación y no a un error de programación, por lo que se recomienda al usuario que vuelva a intentarlo. Aunque no se ha implementado en este ejemplo, en una aplicación de producción de calidad se debería registrar la excepción. Para obtener más información, vea la sección **Registro para obtener información** de [Supervisión y telemetría (creación de aplicaciones de nube reales con Azure)](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).

El atributo `ValidateAntiForgeryToken` ayuda a evitar ataques de falsificación de solicitud entre sitios (CSRF). El token se inserta automáticamente en la vista por medio de [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) y se incluye cuando el usuario envía el formulario. El token se valida mediante el atributo `ValidateAntiForgeryToken`. Para obtener más información sobre CSRF, vea [Prevención de ataques de falsificación de solicitud](../../security/anti-request-forgery.md).

<a id="overpost"></a>

### <a name="security-note-about-overposting"></a>Nota de seguridad sobre la publicación excesiva

El atributo `Bind` que el código con scaffolding incluye en el método `Create` es una manera de protegerse contra la publicación excesiva en escenarios de creación. Por ejemplo, suponga que la entidad Student incluye una propiedad `Secret` que no quiere que esta página web establezca.

```csharp
public class Student
{
    public int ID { get; set; }
    public string LastName { get; set; }
    public string FirstMidName { get; set; }
    public DateTime EnrollmentDate { get; set; }
    public string Secret { get; set; }
}
```

Aunque no tenga un campo `Secret` en la página web, un hacker podría usar una herramienta como Fiddler, o bien escribir código de JavaScript, para enviar un valor de formulario `Secret`. Sin el atributo `Bind` para limitar los campos que el enlazador de modelos usa cuando crea una instancia Student, el enlazador de modelos seleccionaría ese valor de formulario `Secret` y lo usaría para crear la instancia de la entidad Student. Después, el valor que el hacker haya especificado para el campo de formulario `Secret` se actualizaría en la base de datos. En la imagen siguiente se muestra cómo la herramienta Fiddler agrega el campo `Secret` (con el valor "OverPost") a los valores de formulario enviados.

![Campo Secret agregado por Fiddler](crud/_static/fiddler.png)

Después, el valor "OverPost" se agregaría correctamente a la propiedad `Secret` de la fila insertada, aunque no hubiera previsto que la página web pudiera establecer esa propiedad.

Puede evitar la publicación excesiva en escenarios de edición si primero lee la entidad desde la base de datos y después llama a `TryUpdateModel`, pasando una lista de propiedades permitidas de manera explícita. Es el método que se usa en estos tutoriales.

Una manera alternativa de evitar la publicación excesiva que muchos desarrolladores prefieren consiste en usar modelos de vista en lugar de clases de entidad con el enlace de modelos. Incluya en el modelo de vista solo las propiedades que quiera actualizar. Una vez que haya finalizado el enlazador de modelos de MVC, copie las propiedades del modelo de vista a la instancia de entidad, opcionalmente con una herramienta como AutoMapper. Use `_context.Entry` en la instancia de entidad para establecer su estado en `Unchanged` y, después, establezca `Property("PropertyName").IsModified` en true en todas las propiedades de entidad que se incluyan en el modelo de vista. Este método funciona tanto en escenarios de edición como de creación.

### <a name="test-the-create-page"></a>Probar la página Create

En el código de *Views/Students/Create.cshtml* se usan los asistentes de etiquetas `label`, `input` y `span` (para los mensajes de validación) en cada campo.

Ejecute la aplicación, haga clic en la pestaña **Students** y después en **Create New**.

Escriba los nombres y una fecha. Pruebe a escribir una fecha no válida si el explorador se lo permite. (Algunos exploradores le obligan a usar un selector de fecha). Después, haga clic en **Crear** para ver el mensaje de error.

![Error de validación de fecha](crud/_static/date-error.png)

Es la validación del lado servidor que obtendrá de forma predeterminada; en un tutorial posterior verá cómo agregar atributos que también generan código para la validación del lado cliente. En el siguiente código resaltado se muestra la comprobación de validación del modelo en el método `Create`.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=8)]

Cambie la fecha por un valor válido y haga clic en **Crear** para ver el alumno nuevo en la página **Index**.

## <a name="update-the-edit-page"></a>Actualizar la página Edit

En *StudentController.cs*, el método HttpGet `Edit` (el que no tiene el atributo `HttpPost`) usa el método `SingleOrDefaultAsync` para recuperar la entidad Student seleccionada, como se vio en el método `Details`. No es necesario cambiar este método.

### <a name="recommended-httppost-edit-code-read-and-update"></a>Código recomendado para HttpPost Edit: lectura y actualización

Reemplace el método de acción HttpPost Edit con el código siguiente.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ReadFirst)]

Estos cambios implementan un procedimiento recomendado de seguridad para evitar la publicación excesiva. El proveedor de scaffolding generó un atributo `Bind` y agregó la entidad creada por el enlazador de modelos a la entidad establecida con una marca `Modified`. Ese código no se recomienda para muchos escenarios porque el atributo `Bind` borra los datos ya existentes en los campos que no se enumeran en el parámetro `Include`.

El código nuevo lee la entidad existente y llama a `TryUpdateModel` para actualizar los campos en la entidad recuperada [en función de la entrada del usuario en los datos de formulario publicados](xref:mvc/models/model-binding). El seguimiento de cambios automático de Entity Framework establece la marca `Modified` en los campos que se cambian mediante la entrada de formulario. Cuando se llama al método `SaveChanges`, Entity Framework crea instrucciones SQL para actualizar la fila de la base de datos. Los conflictos de simultaneidad se ignoran y las columnas de tabla que se actualizaron por el usuario se actualizan en la base de datos. (En un tutorial posterior se muestra cómo controlar los conflictos de simultaneidad).

Como procedimiento recomendado para evitar la publicación excesiva, los campos que quiera que se puedan actualizar por la página **Edit** se incluyen en la lista de permitidos en los parámetros `TryUpdateModel`. (La cadena vacía que precede a la lista de campos en la lista de parámetros es para el prefijo que se usa con los nombres de campos de formulario). Actualmente no se está protegiendo ningún campo adicional, pero enumerar los campos que quiere que el enlazador de modelos enlace garantiza que, si en el futuro agrega campos al modelo de datos, se protejan automáticamente hasta que los agregue aquí de forma explícita.

Como resultado de estos cambios, la firma de método del método HttpPost `Edit` es la misma que la del método HttpGet `Edit`; por tanto, se ha cambiado el nombre del método `EditPost`.

### <a name="alternative-httppost-edit-code-create-and-attach"></a>Código alternativo para HttpPost Edit: crear y adjuntar

El código recomendado para HttpPost Edit garantiza que solo se actualicen las columnas cambiadas y conserva los datos de las propiedades que no quiere que se incluyan para el enlace de modelos. Pero el enfoque de primera lectura requiere una operación de lectura adicional de la base de datos y puede dar lugar a código más complejo para controlar los conflictos de simultaneidad. Una alternativa consiste en adjuntar una entidad creada por el enlazador de modelos en el contexto de EF y marcarla como modificada. (No actualice el proyecto con este código, solo se muestra para ilustrar un enfoque opcional).

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_CreateAndAttach)]

Puede usar este enfoque cuando la interfaz de usuario de la página web incluya todos los campos de la entidad y puede actualizar cualquiera de ellos.

En el código con scaffolding se usa el enfoque de crear y adjuntar, pero solo se detectan las excepciones `DbUpdateConcurrencyException` y se devuelven códigos de error 404.  En el ejemplo mostrado se detecta cualquier excepción de actualización de base de datos y se muestra un mensaje de error.

### <a name="entity-states"></a>Estados de entidad

El contexto de la base de datos realiza el seguimiento de si las entidades en memoria están sincronizadas con sus filas correspondientes en la base de datos, y esta información determina lo que ocurre cuando se llama al método `SaveChanges`. Por ejemplo, cuando se pasa una nueva entidad al método `Add`, el estado de esa entidad se establece en `Added`. Después, cuando se llama al método `SaveChanges`, el contexto de la base de datos emite un comando INSERT de SQL.

Una entidad puede estar en uno de los estados siguientes:

* `Added`. La entidad no existe todavía en la base de datos. El método `SaveChanges` emite una instrucción INSERT.

* `Unchanged`. No es necesario hacer nada con esta entidad mediante el método `SaveChanges`. Al leer una entidad de la base de datos, la entidad empieza con este estado.

* `Modified`. Se han modificado algunos o todos los valores de propiedad de la entidad. El método `SaveChanges` emite una instrucción UPDATE.

* `Deleted`. La entidad se ha marcado para su eliminación. El método `SaveChanges` emite una instrucción DELETE.

* `Detached`. El contexto de base de datos no está realizando el seguimiento de la entidad.

En una aplicación de escritorio, los cambios de estado normalmente se establecen de forma automática. Lea una entidad y realice cambios en algunos de sus valores de propiedad. Esto hace que su estado de entidad cambie automáticamente a `Modified`. Después, cuando se llama a `SaveChanges`, Entity Framework genera una instrucción UPDATE de SQL que solo actualiza las propiedades reales que se hayan cambiado.

En una aplicación web, el `DbContext` que inicialmente lee una entidad y muestra sus datos para que se puedan modificar se elimina después de representar una página. Cuando se llama al método de acción HttpPost `Edit`, se realiza una nueva solicitud web y se obtiene una nueva instancia de `DbContext`. Si vuelve a leer la entidad en ese contexto nuevo, simulará el procesamiento de escritorio.

Pero si no quiere realizar la operación de lectura adicional, tendrá que usar el objeto de entidad creado por el enlazador de modelos.  La manera más sencilla de hacerlo consiste en establecer el estado de la entidad en Modified tal y como se hace en el código HttpPost Edit alternativo mostrado anteriormente. Después, cuando se llama a `SaveChanges`, Entity Framework actualiza todas las columnas de la fila de la base de datos, porque el contexto no tiene ninguna manera de saber qué propiedades se han cambiado.

Si quiere evitar el enfoque de primera lectura pero también que la instrucción UPDATE de SQL actualice solo los campos que el usuario ha cambiado realmente, el código es más complejo. Debe guardar los valores originales de alguna manera (por ejemplo mediante campos ocultos) para que estén disponibles cuando se llame al método HttpPost `Edit`. Después puede crear una entidad Student con los valores originales, llamar al método `Attach` con esa versión original de la entidad, actualizar los valores de la entidad con los valores nuevos y luego llamar a `SaveChanges`.

### <a name="test-the-edit-page"></a>Probar la página Edit

Ejecute la aplicación, haga clic en la pestaña **Students** y después en un hipervínculo **Edit**.

![Página de edición de estudiantes](crud/_static/student-edit.png)

Cambie algunos de los datos y haga clic en **Guardar**. Se abrirá la página **Index** y verá los datos modificados.

## <a name="update-the-delete-page"></a>Actualizar la página Delete

En *StudentController.cs*, el código de plantilla para el método HttpGet `Delete` usa el método `SingleOrDefaultAsync` para recuperar la entidad Student seleccionada, como se vio en los métodos Details y Edit. Pero para implementar un mensaje de error personalizado cuando se produce un error en la llamada a `SaveChanges`, agregará funcionalidad a este método y su vista correspondiente.

Como se vio para las operaciones de actualización y creación, las operaciones de eliminación requieren dos métodos de acción. El método que se llama en respuesta a una solicitud GET muestra una vista que proporciona al usuario la oportunidad de aprobar o cancelar la operación de eliminación. Si el usuario la aprueba, se crea una solicitud POST. Cuando esto ocurre, se llama al método HttpPost `Delete` y, después, ese método es el que realiza la operación de eliminación.

Agregará un bloque try-catch al método HttpPost `Delete` para controlar los errores que puedan producirse cuando se actualice la base de datos. Si se produce un error, el método HttpPost Delete llama al método HttpGet Delete, pasando un parámetro que indica que se ha producido un error. Después, el método HttpGet Delete vuelve a mostrar la página de confirmación junto con el mensaje de error, dando al usuario la oportunidad de cancelar o volver a intentarlo.

Reemplace el método de acción HttpGet `Delete` con el código siguiente, que administra los informes de errores.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteGet&highlight=1,9,16-21)]

Este código acepta un parámetro opcional que indica si se llamó al método después de un error al guardar los cambios. Este parámetro es false cuando se llama al método HttpGet `Delete` sin un error anterior. Cuando se llama por medio del método HttpPost `Delete` en respuesta a un error de actualización de base de datos, el parámetro es true y se pasa un mensaje de error a la vista.

### <a name="the-read-first-approach-to-httppost-delete"></a>El enfoque de primera lectura para HttpPost Delete

Reemplace el método de acción HttpPost `Delete` (denominado `DeleteConfirmed`) con el código siguiente, que realiza la operación de eliminación y captura los errores de actualización de base de datos.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithReadFirst&highlight=6-9,11-12,16-21)]

Este código recupera la entidad seleccionada y después llama al método `Remove` para establecer el estado de la entidad en `Deleted`. Cuando se llama a `SaveChanges`, se genera un comando DELETE de SQL.

### <a name="the-create-and-attach-approach-to-httppost-delete"></a>El enfoque de crear y adjuntar para HttpPost Delete

Si mejorar el rendimiento de una aplicación de gran volumen es una prioridad, podría evitar una consulta SQL innecesaria creando instancias de una entidad Student solo con el valor de clave principal y después estableciendo el estado de la entidad en `Deleted`. Eso es todo lo que necesita Entity Framework para eliminar la entidad. (No incluya este código en el proyecto; únicamente se muestra para ilustrar una alternativa).

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithoutReadFirst&highlight=7-8)]

Si la entidad tiene datos relacionados que también se deban eliminar, asegúrese de configurar la eliminación en cascada en la base de datos. Con este enfoque de eliminación de entidades, es posible que EF no sepa que hay entidades relacionadas para eliminar.

### <a name="update-the-delete-view"></a>Actualizar la vista Delete

En *Views/Student/Delete.cshtml*, agregue un mensaje de error entre los títulos h2 y h3, como se muestra en el ejemplo siguiente:

[!code-html[](intro/samples/cu/Views/Students/Delete.cshtml?range=7-9&highlight=2)]

Ejecute la aplicación, haga clic en la pestaña **Students** y después en un hipervínculo **Delete**:

![Página de confirmación de la eliminación](crud/_static/student-delete.png)

Haga clic en **Eliminar**. Se mostrará la página de índice sin el estudiante eliminado. (Verá un ejemplo del código de control de errores en funcionamiento en el tutorial sobre la simultaneidad).

## <a name="close-database-connections"></a>Cerrar conexiones de bases de datos

Para liberar los recursos que contiene una conexión de base de datos, la instancia de contexto debe eliminarse tan pronto como sea posible cuando haya terminado con ella. La [inserción de dependencias](../../fundamentals/dependency-injection.md) integrada de ASP.NET Core se encarga de esa tarea.

En *Startup.cs*, se llama al [método de extensión AddDbContext](https://github.com/aspnet/EntityFrameworkCore/blob/03bcb5122e3f577a84498545fcf130ba79a3d987/src/Microsoft.EntityFrameworkCore/EntityFrameworkServiceCollectionExtensions.cs) para aprovisionar la clase `DbContext` en el contenedor de inserción de dependencias de ASP.NET Core. Ese método establece la duración del servicio en `Scoped` de forma predeterminada. `Scoped` significa que la duración del objeto de contexto coincide con la duración de la solicitud web, y el método `Dispose` se llamará automáticamente al final de la solicitud web.

## <a name="handle-transactions"></a>Controlar transacciones

De forma predeterminada, Entity Framework implementa las transacciones de manera implícita. En escenarios donde se realizan cambios en varias filas o tablas, y después se llama a `SaveChanges`, Entity Framework se asegura automáticamente de que todos los cambios se realicen correctamente o se produzca un error en todos ellos. Si primero se realizan algunos cambios y después se produce un error, los cambios se revierten automáticamente. Para escenarios donde se necesita más control, por ejemplo, si se quieren incluir operaciones realizadas fuera de Entity Framework en una transacción, vea [Transacciones](/ef/core/saving/transactions).

## <a name="no-tracking-queries"></a>Consultas de no seguimiento

Cuando un contexto de base de datos recupera las filas de tabla y crea objetos de entidad que las representa, de forma predeterminada realiza el seguimiento de si las entidades en memoria están sincronizadas con el contenido de la base de datos. Los datos en memoria actúan como una caché y se usan cuando se actualiza una entidad. Este almacenamiento en caché suele ser necesario en una aplicación web porque las instancias de contexto normalmente son de corta duración (para cada solicitud se crea una y se elimina) y el contexto que lee una entidad normalmente se elimina antes de volver a usar esa entidad.

Puede deshabilitar el seguimiento de los objetos de entidad en memoria mediante una llamada al método `AsNoTracking`. Los siguientes son escenarios típicos en los que es posible que quiera hacer esto:

* Durante la vigencia del contexto no es necesario actualizar ninguna entidad ni que EF [cargue automáticamente las propiedades de navegación con las entidades recuperadas por consultas independientes](read-related-data.md). Estas condiciones se cumplen frecuentemente en los métodos de acción HttpGet del controlador.

* Se ejecuta una consulta que recupera un gran volumen de datos y solo se actualiza una pequeña parte de los datos devueltos. Puede ser más eficaz desactivar el seguimiento de la consulta de gran tamaño y ejecutar una consulta más adelante para las pocas entidades que deban actualizarse.

* Se quiere adjuntar una entidad para actualizarla, pero antes se recuperó la misma entidad para un propósito diferente. Como el contexto de base de datos ya está realizando el seguimiento de la entidad, no se puede adjuntar la entidad que se quiere cambiar. Una manera de controlar esta situación consiste en llamar a `AsNoTracking` en la consulta anterior.

Para obtener más información, vea [Tracking vs. No-Tracking](/ef/core/querying/tracking) (Diferencia entre consultas de seguimiento y no seguimiento).

## <a name="get-the-code"></a>Obtención del código

[Descargue o vea la aplicación completa.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha:

> [!div class="checklist"]
> * Personalizado la página de detalles
> * Actualizado la página Create
> * Actualizado la página Edit
> * Actualizado la página Delete
> * Cerrado conexiones de bases de datos

Pase al tutorial siguiente para obtener información sobre cómo expandir la funcionalidad de la página **Index** mediante la adición de ordenación, filtrado y paginación.

> [!div class="nextstepaction"]
> [Siguiente: Ordenación, filtrado y paginación](sort-filter-page.md)
