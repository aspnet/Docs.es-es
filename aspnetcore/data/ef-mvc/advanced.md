---
title: 'Tutorial: Información sobre escenarios avanzados: ASP.NET MVC con EF Core'
description: En este tutorial se presentan varios temas que le serán de utilidad cuando quiera ir más allá de los conceptos básicos del desarrollo de aplicaciones web ASP.NET Core que usan Entity Framework Core.
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
uid: data/ef-mvc/advanced
ms.openlocfilehash: 7233d6baf139d2ef362f4e3d1a56cf7f0e2514d2
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403123"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a>Tutorial: Información sobre escenarios avanzados: ASP.NET MVC con EF Core

En el tutorial anterior, se implementó la herencia de tabla por jerarquía. En este tutorial se presentan varios temas que es importante tener en cuenta cuando va más allá de los conceptos básicos del desarrollo de aplicaciones web ASP.NET Core que usan Entity Framework Core.

En este tutorial ha:

> [!div class="checklist"]
> * Realiza consultas SQL sin formato
> * Llama a una consulta para devolver entidades
> * Llama a una consulta para devolver otros tipos
> * Llamar a una consulta update
> * Examina consultas SQL
> * Crea una capa de abstracción
> * Obtiene información sobre la detección de cambios automática
> * Obtiene información sobre el código fuente y planes de desarrollo de Entity Framework Core
> * Obtiene información sobre cómo usar LINQ dinámico para simplificar el código

## <a name="prerequisites"></a>Requisitos previos

* [Implementación de la herencia](inheritance.md)

## <a name="perform-raw-sql-queries"></a>Realiza consultas SQL sin formato

Una de las ventajas del uso de Entity Framework es que evita enlazar el código demasiado estrechamente a un método concreto de almacenamiento de datos. Lo consigue mediante la generación de consultas SQL y comandos, lo que también le evita tener que escribirlos usted mismo. Pero hay situaciones excepcionales en las que necesita ejecutar consultas específicas de SQL que ha creado manualmente. En estos casos, la API de Entity Framework Code First incluye métodos que le permiten pasar comandos SQL directamente a la base de datos. En EF Core 1.0 dispone de las siguientes opciones:

* Use el método `DbSet.FromSql` para las consultas que devuelven tipos de entidad. Los objetos devueltos deben ser del tipo esperado por el objeto `DbSet` y se les realiza automáticamente un seguimiento mediante el contexto de base de datos a menos que se [desactive el seguimiento](crud.md#no-tracking-queries).

* Use `Database.ExecuteSqlCommand` para comandos sin consulta.

Si tiene que ejecutar una consulta que devuelve tipos que no son entidades, puede usar ADO.NET con la conexión de base de datos proporcionada por EF. No se realiza un seguimiento de los datos devueltos por el contexto de la base de datos, incluso si usa este método para recuperar tipos de entidad.

Como siempre es true cuando ejecuta comandos SQL en una aplicación web, debe tomar precauciones para proteger su sitio contra los ataques por inyección de código SQL. Una manera de hacerlo es mediante consultas parametrizadas para asegurarse de que las cadenas enviadas por una página web no se pueden interpretar como comandos SQL. En este tutorial usará las consultas con parámetros al integrar la entrada de usuario en una consulta.

## <a name="call-a-query-to-return-entities"></a>Llama a una consulta para devolver entidades

La clase `DbSet<TEntity>` proporciona un método que puede usar para ejecutar una consulta que devuelve una entidad de tipo `TEntity`. Para ver cómo funciona, cambiará el código en el método `Details` del controlador de departamento.

En *DepartmentsController.cs*, en el método `Details`, reemplace el código que recupera un departamento con una llamada al método `FromSql`, como se muestra en el código resaltado siguiente:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

Para comprobar que el nuevo código funciona correctamente, seleccione la pestaña **Departments** y, después, **Details** para uno de los departamentos.

![Detalles del departamento](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a>Llama a una consulta para devolver otros tipos

Anteriormente creó una cuadrícula de estadísticas de alumno de la página About que mostraba el número de alumnos para cada fecha de inscripción. Obtuvo los datos del conjunto de entidades Students (`_context.Students`) y usó LINQ para proyectar los resultados en una lista de objetos de modelo de vista `EnrollmentDateGroup`. Suponga que quiere escribir la instrucción SQL propia en lugar de usar LINQ. Para ello, necesita ejecutar una consulta SQL que devuelve un valor distinto de objetos entidad. En EF Core 1.0, una manera de hacerlo es escribir código de ADO.NET y obtener la conexión de base de datos de EF.

En *HomeController.cs*, reemplace el método `About` por el código siguiente:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

Agregue una instrucción using:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

Ejecute la aplicación y vaya a la página About. Muestra los mismos datos que anteriormente.

![Página About](advanced/_static/about.png)

## <a name="call-an-update-query"></a>Llamar a una consulta update

Imagine que los administradores de Contoso University quieren realizar cambios globales en la base de datos, como cambiar el número de créditos para cada curso. Si la universidad tiene un gran número de cursos, sería poco eficaz recuperarlos todos como entidades y cambiarlos de forma individual. En esta sección implementará una página web que permite al usuario especificar un factor por el cual se va a cambiar el número de créditos para todos los cursos y podrá realizar el cambio mediante la ejecución de una instrucción UPDATE de SQL. La página web tendrá el mismo aspecto que la ilustración siguiente:

![Página Update Course Credits](advanced/_static/update-credits.png)

En *CoursesContoller.cs*, agregue los métodos UpdateCourseCredits para HttpGet y HttpPost:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

Cuando el controlador procesa una solicitud HttpGet, no se devuelve nada en `ViewData["RowsAffected"]` y la vista muestra un cuadro de texto vacío y un botón de envío, tal como se muestra en la ilustración anterior.

Cuando se hace clic en el botón **Update**, se llama al método HttpPost y el multiplicador tiene el valor especificado en el cuadro de texto. A continuación, el código ejecuta la instrucción SQL que actualiza los cursos y devuelve el número de filas afectadas a la vista en `ViewData`. Cuando la vista obtiene un valor `RowsAffected`, muestra el número de filas actualizadas.

En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta *Views/Courses* y luego haga clic en **Agregar > Nuevo elemento**.

En el cuadro de diálogo **Agregar nuevo elemento**, haga clic en **ASP.NET Core** en **Instalado** en el panel izquierdo, haga clic en **Vista de Razor** y ponga el nombre *UpdateCourseCredits.cshtml* a la nueva vista.

En *Views/Courses/UpdateCourseCredits.cshtml*, reemplace el código de plantilla con el código siguiente:

[!code-html[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

Ejecute el método `UpdateCourseCredits` seleccionando la pestaña **Courses**, después, agregue "/UpdateCourseCredits" al final de la dirección URL en la barra de direcciones del explorador (por ejemplo: `http://localhost:5813/Courses/UpdateCourseCredits`). Escriba un número en el cuadro de texto:

![Página Update Course Credits](advanced/_static/update-credits.png)

Haga clic en **Actualizar**. Verá el número de filas afectadas:

![Filas afectadas de la página Update Course Credits](advanced/_static/update-credits-rows-affected.png)

Haga clic en **Volver a la lista** para ver la lista de cursos con el número de créditos revisado.

Tenga en cuenta que el código de producción garantiza que las actualizaciones siempre dan como resultado datos válidos. El código simplificado que se muestra a continuación podría multiplicar el número de créditos lo suficiente para que el resultado sea un número superior a 5. (La propiedad `Credits` tiene un atributo `[Range(0, 5)]`). La consulta update podría funcionar, pero los datos no válidos podrían provocar resultados inesperados en otras partes del sistema que asumen que el número de créditos es igual o inferior a 5.

Para obtener más información sobre las consultas SQL básicas, vea [Consultas SQL básicas](/ef/core/querying/raw-sql).

## <a name="examine-sql-queries"></a>Examina consultas SQL

A veces resulta útil poder ver las consultas SQL reales que se envían a la base de datos. EF Core usa automáticamente la funcionalidad de registro integrada para ASP.NET Core para escribir los registros que contienen el código SQL para las consultas y actualizaciones. En esta sección podrá ver algunos ejemplos de registros de SQL.

Abra *StudentsController.cs* y, en el método `Details`, establezca un punto de interrupción en la instrucción `if (student == null)`.

Ejecute la aplicación en modo de depuración y vaya a la página Details de un alumno.

Vaya a la ventana **Salida** que muestra la salida de depuración y verá la consulta:

```
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (56ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT TOP(2) [s].[ID], [s].[Discriminator], [s].[FirstName], [s].[LastName], [s].[EnrollmentDate]
FROM [Person] AS [s]
WHERE ([s].[Discriminator] = N'Student') AND ([s].[ID] = @__id_0)
ORDER BY [s].[ID]
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (122ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT [s.Enrollments].[EnrollmentID], [s.Enrollments].[CourseID], [s.Enrollments].[Grade], [s.Enrollments].[StudentID], [e.Course].[CourseID], [e.Course].[Credits], [e.Course].[DepartmentID], [e.Course].[Title]
FROM [Enrollment] AS [s.Enrollments]
INNER JOIN [Course] AS [e.Course] ON [s.Enrollments].[CourseID] = [e.Course].[CourseID]
INNER JOIN (
    SELECT TOP(1) [s0].[ID]
    FROM [Person] AS [s0]
    WHERE ([s0].[Discriminator] = N'Student') AND ([s0].[ID] = @__id_0)
    ORDER BY [s0].[ID]
) AS [t] ON [s.Enrollments].[StudentID] = [t].[ID]
ORDER BY [t].[ID]
```

Aquí verá algo que podría sorprenderle: la instrucción SQL selecciona hasta 2 filas (`TOP(2)`) de la tabla Person. El método `SingleOrDefaultAsync` no se resuelve en 1 fila en el servidor. El motivo es el siguiente:

* Si la consulta devolvería varias filas, el método devuelve NULL.
* Para determinar si la consulta devolvería varias filas, EF tiene que comprobar si devuelve al menos 2.

Tenga en cuenta que no tiene que usar el modo de depuración y parar en un punto de interrupción para obtener los resultados del registro en la ventana **Salida**. Es una manera cómoda de detener el registro en el punto que quiere ver en la salida. Si no lo hace, el registro continúa y tiene que desplazarse hacia atrás para encontrar los elementos que le interesan.

## <a name="create-an-abstraction-layer"></a>Crea una capa de abstracción

Muchos desarrolladores escriben código para implementar el repositorio y una unidad de patrones de trabajo como un contenedor en torno al código que funciona con Entity Framework. Estos patrones están previstos para crear una capa de abstracción entre la capa de acceso de datos y la capa de lógica de negocios de una aplicación. Implementar estos patrones puede ayudar a aislar la aplicación de cambios en el almacén de datos y puede facilitar la realización de pruebas unitarias automatizadas o el desarrollo controlado por pruebas (TDD). Pero escribir código adicional para implementar estos patrones no siempre es la mejor opción para las aplicaciones que usan EF, por varias razones:

* La propia clase de contexto de EF aísla el código del código específico del almacén de datos.

* La clase de contexto de EF puede actuar como una clase de unidad de trabajo para las actualizaciones de base de datos que hace con EF.

* EF incluye características para implementar TDD sin escribir código de repositorio.

Para obtener información sobre cómo implementar los patrones de repositorio y de unidad de trabajo, consulte [la versión de Entity Framework 5 de esta serie de tutoriales](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).

Entity Framework Core implementa un proveedor de base de datos en memoria que puede usarse para realizar pruebas. Para más información, vea [Pruebas con InMemory](/ef/core/miscellaneous/testing/in-memory).

## <a name="automatic-change-detection"></a>Detección de cambios automática

Entity Framework determina cómo ha cambiado una entidad (y, por tanto, las actualizaciones que hay que enviar a la base de datos) comparando los valores actuales de una entidad con los valores originales. Cuando se consulta o se adjunta la entidad, se almacenan los valores originales. Algunos de los métodos que provocan la detección de cambios automática son los siguientes:

* DbContext.SaveChanges

* DbContext.Entry

* ChangeTracker.Entries

Si está realizando el seguimiento de un gran número de entidades y llama a uno de estos métodos muchas veces en un bucle, podría obtener mejoras de rendimiento significativas si desactiva temporalmente la detección de cambios automática mediante la propiedad `ChangeTracker.AutoDetectChangesEnabled`. Por ejemplo:

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a>Código fuente y planes de desarrollo de Entity Framework Core

El origen de Entity Framework Core está en [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore). El repositorio de EF Core contiene las compilaciones nocturnas, el seguimiento de problemas, las especificaciones de características, las notas de las reuniones de diseño y [el plan de desarrollo futuro](https://github.com/dotnet/efcore/wiki/Roadmap). Puede archivar o buscar errores y contribuir.

Aunque el código fuente es abierto, Entity Framework Core es totalmente compatible como producto de Microsoft. El equipo de Microsoft Entity Framework mantiene el control sobre qué contribuciones se aceptan y comprueba todos los cambios de código para garantizar la calidad de cada versión.

## <a name="reverse-engineer-from-existing-database"></a>Ingeniería inversa desde la base de datos existente

Para usar técnicas de ingeniería inversa a un modelo de datos, incluidas las clases de entidad de una base de datos existente, use el comando [scaffold dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext). Consulte el [tutorial de introducción](/ef/core/get-started/aspnetcore/existing-db).

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a>Usar LINQ dinámico para simplificar el código

El [tercer tutorial de esta serie](sort-filter-page.md) muestra cómo escribir código LINQ mediante el codificado de forma rígida de los nombres de columna en una instrucción `switch`. Con dos columnas entre las que elegir, esto funciona bien, pero si tiene muchas columnas, el código podría considerarse detallado. Para solucionar este problema, puede usar el método `EF.Property` para especificar el nombre de la propiedad como una cadena. Para probar este enfoque, reemplace el método `Index` en `StudentsController` con el código siguiente.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a>Agradecimientos

Tom Dykstra y Rick Anderson (Twitter @RickAndMSFT) escribieron este tutorial. Rowan Miller, Diego Vega y otros miembros del equipo de Entity Framework participaron con revisiones de código y ayudaron a depurar problemas que surgieron mientras se estaba escribiendo el código para los tutoriales. John Parente y Paul Goldman trabajaron en la actualización del tutorial de ASP.NET Core 2.2.

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a>Solucionar problemas de errores comunes

### <a name="contosouniversitydll-used-by-another-process"></a>ContosoUniversity.dll usado por otro proceso

Mensaje de error:

> No se puede abrir '...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' para escribir: El proceso no puede obtener acceso al archivo '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll', otro proceso lo está usando.

Solución:

Detenga el sitio en IIS Express. Vaya a la bandeja del sistema de Windows, busque IIS Express y haga clic con el botón derecho en su icono; seleccione el sitio de Contoso University y, después, haga clic en **Detener sitio**.

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a>La migración aplicó la técnica scaffolding sin código en los métodos Up y Down

Causa posible:

Los comandos de la CLI de EF no cierran y guardan automáticamente los archivos de código. Si no ha guardado los cambios cuando ejecuta el comando `migrations add`, EF no encontrará los cambios.

Solución:

Ejecute el comando `migrations remove`, guarde los cambios de código y vuelva a ejecutar el comando `migrations add`.

### <a name="errors-while-running-database-update"></a>Errores durante la ejecución de la actualización de la base de datos

Al hacer cambios en el esquema, se pueden generar otros errores en una base de datos que contenga los datos existentes. Si se producen errores de migración que no se pueden resolver, puede cambiar el nombre de la base de datos en la cadena de conexión o eliminar la base de datos. Con una base de datos nueva, no hay ningún dato para migrar y es mucho más probable que el comando de actualización de base de datos se complete sin errores.

El enfoque más sencillo consiste en cambiar el nombre de la base de datos en *appsettings.json*. La próxima vez que ejecute `database update`, se creará una base de datos.

Para eliminar una base de datos en SSOX, haga clic con el botón derecho en la base de datos, haga clic en **Eliminar** y, después, en el cuadro de diálogo **Eliminar base de datos**, seleccione **Cerrar conexiones existentes** y haga clic en **Aceptar**.

Para eliminar una base de datos mediante el uso de la CLI, ejecute el comando de la CLI `database drop`:

```dotnetcli
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a>Error al buscar la instancia de SQL Server

Mensaje de error:

> Error relacionado con la red o específico de la instancia mientras se establecía una conexión con el servidor SQL Server. No se encontró el servidor o éste no estaba accesible. Compruebe que el nombre de la instancia es correcto y que SQL Server está configurado para admitir conexiones remotas. (proveedor: Interfaces de red SQL, error: 26: error al buscar el servidor o la instancia especificados)

Solución:

Compruebe la cadena de conexión. Si ha eliminado manualmente el archivo de base de datos, cambie el nombre de la base de datos en la cadena de construcción para volver a empezar con una base de datos nueva.

## <a name="get-the-code"></a>Obtención del código

[Descargue o vea la aplicación completa.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Recursos adicionales

Para obtener más información sobre EF Core, consulte la [documentación de Entity Framework Core](/ef/core). También hay disponible un libro: [Entity Framework Core en acción](https://www.manning.com/books/entity-framework-core-in-action).

Para obtener información sobre cómo implementar una aplicación web, consulte <xref:host-and-deploy/index>.

Para obtener información sobre otros temas relacionados con ASP.NET Core MVC, como la autenticación y autorización, vea <xref:index>.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha:

> [!div class="checklist"]
> * Realizado consultas SQL sin formato
> * Llamado a una consulta para devolver entidades
> * Llamado a una consulta para devolver otros tipos
> * Llamado a una consulta update
> * Examinado consultas SQL
> * Creado una capa de abstracción
> * Obtenido información sobre la detección de cambios automática
> * Obtenido información sobre el código fuente y planes de desarrollo de Entity Framework Core
> * Obtenido información sobre cómo usar LINQ dinámico para simplificar el código

Con esto finaliza esta serie de tutoriales sobre cómo usar Entity Framework Core en una aplicación ASP.NET Core MVC. En esta serie se ha trabajado con una base de datos nueva, pero también se pueden utilizar técnicas de ingeniería inversa en un modelo de una base de datos existente.

> [!div class="nextstepaction"]
> [Tutorial: EF Core con MVC, base de datos existente](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
