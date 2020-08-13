---
title: 'Tutorial: Implementación de la herencia: ASP.NET MVC con EF Core'
description: En este tutorial se explica cómo implementar la herencia en el modelo de datos con Entity Framework Core en una aplicación de ASP.NET Core.
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
uid: data/ef-mvc/inheritance
ms.openlocfilehash: b57adeb2712eb58907b53809cbd938e8ec0c1f6f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88012844"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a>Tutorial: Implementación de la herencia: ASP.NET MVC con EF Core

En el tutorial anterior, se trataron las excepciones de simultaneidad. En este tutorial se muestra cómo implementar la herencia en el modelo de datos.

En la programación orientada a objetos, puede usar la herencia para facilitar la reutilización del código. En este tutorial, cambiará las clases `Instructor` y `Student` para que deriven de una clase base `Person` que contenga propiedades como `LastName`, que son comunes tanto para los instructores como para los alumnos. No tendrá que agregar ni cambiar ninguna página web, sino que cambiará parte del código y esos cambios se reflejarán automáticamente en la base de datos.

En este tutorial ha:

> [!div class="checklist"]
> * Asigna la herencia a la base de datos
> * Creación de la clase Person
> * Actualiza Instructor y Student
> * Agrega Person al modelo
> * Crea y actualiza migraciones
> * Prueba la implementación

## <a name="prerequisites"></a>Requisitos previos

* [Control de la simultaneidad](concurrency.md)

## <a name="map-inheritance-to-database"></a>Asigna la herencia a la base de datos

Las clases `Instructor` y `Student` del modelo de datos School tienen varias propiedades idénticas:

![Clases Student e Instructor](inheritance/_static/no-inheritance.png)

Imagine que quiere eliminar el código redundante de las propiedades que comparten las entidades `Instructor` y `Student`. O que quiere escribir un servicio que pueda dar formato a los nombres sin preocuparse de si el nombre es de un instructor o de un alumno. Puede crear una clase base `Person` que solo contenga las propiedades compartidas y después hacer que las clases `Instructor` y `Student` hereden de esa clase base, como se muestra en la siguiente ilustración:

![Clases Student e Instructor derivadas de la clase Person](inheritance/_static/inheritance.png)

Esta estructura de herencia se puede representar de varias formas en la base de datos. Puede tener una sola tabla Person que incluya información sobre los alumnos y los instructores. Algunas de las columnas podrían aplicarse solo a los instructores (HireDate), otras solo a los alumnos (EnrollmentDate) y otras a ambos (LastName, FirstName). Lo más común sería que tuviera una columna discriminadora para indicar qué tipo representa cada fila. Por ejemplo, en la columna discriminadora podría aparecer "Instructor" para los instructores y "Student" para los alumnos.

![Ejemplo de tabla por jerarquía](inheritance/_static/tph.png)

Este patrón, que consiste en generar una estructura de herencia de la entidad a partir de una tabla de base de datos única, se denomina herencia de tabla por jerarquía (TPH).

Una alternativa consiste en hacer que la base de datos se parezca más a la estructura de herencia. Por ejemplo, podría tener solo los campos de nombre en la tabla Person y tener tablas Instructor y Student independientes con los campos de fecha.

![Herencia de tabla por tipo](inheritance/_static/tpt.png)

Este patrón, que consiste en crear una tabla de base de datos para cada clase de entidad, se denomina herencia de tabla por tipo (TPT).

Y todavía otra opción pasa por asignar todos los tipos no abstractos a tablas individuales. Todas las propiedades de una clase, incluidas las propiedades heredadas, se asignan a columnas de la tabla correspondiente. Este patrón se denomina herencia de tabla por clase concreta (TPC). Si implementara la herencia de TPC en las clases Person, Student e Instructor tal como se ha explicado anteriormente, las tablas Student e Instructor se verían igual antes que después de implementar la herencia.

Los patrones de herencia de TPC y TPH acostumbran a tener un mejor rendimiento que los patrones de herencia de TPT, porque estos pueden provocar consultas de combinaciones complejas.

Este tutorial muestra cómo implementar la herencia de TPH. TPH es el único patrón de herencia que Entity Framework Core admite.  Lo que tiene que hacer es crear una clase `Person`, cambiar las clases `Instructor` y `Student` que deriven de `Person`, agregar la nueva clase a `DbContext` y crear una migración.

> [!TIP]
> Considere la posibilidad de guardar una copia del proyecto antes de realizar los siguientes cambios.  De este modo, si tiene problemas y necesita volver a empezar, le resultará más fácil comenzar desde el proyecto guardado que tener que revertir los pasos realizados en este tutorial o volver al principio de toda la serie.

## <a name="create-the-person-class"></a>Creación de la clase Person

En la carpeta Models, cree Person.cs y reemplace el código de plantilla por el código siguiente:

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a>Actualiza Instructor y Student

En *Instructor.cs*, derive la clase Instructor de la clase Person y quite los campos de clave y nombre. El código tendrá un aspecto similar al ejemplo siguiente:

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

Realice los mismos cambios en *Student.cs*.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a>Agrega Person al modelo

Agregue el tipo de entidad Person a *SchoolContext.cs*. Se resaltan las líneas nuevas.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

Esto es todo lo que Entity Framework necesita para configurar la herencia de tabla por jerarquía. Como verá, cuando la base de datos esté actualizada, tendrá una tabla Person en lugar de las tablas Student e Instructor.

## <a name="create-and-update-migrations"></a>Crea y actualiza migraciones

Guarde los cambios y compile el proyecto. A continuación, abra la ventana de comandos en la carpeta de proyecto y escriba el siguiente comando:

```dotnetcli
dotnet ef migrations add Inheritance
```

No ejecute el comando `database update` todavía. Este comando provocará la pérdida de datos porque colocará la tabla Instructor y cambiará el nombre de la tabla Student por Person. Deberá proporcionar código personalizado para conservar los datos existentes.

Abra *Migrations/\<timestamp>_Inheritance.cs* y reemplace el método `Up` por el código siguiente:

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

Este código se encarga de las siguientes tareas de actualización de la base de datos:

* Quita las restricciones de la clave externa y los índices que apuntan a la tabla Student.

* Cambia el nombre de la tabla Instructor por Person y realiza los cambios necesarios para que pueda almacenar datos de los alumnos:

* Agrega EnrollmentDate que acepta valores NULL para alumnos.

* Agrega la columna discriminadora para indicar si una fila es para un alumno o para un instructor.

* Hace que HireDate admita un valor NULL, puesto que las filas de alumnos no dispondrán de fechas de contratación.

* Agrega un campo temporal que se usará para actualizar las claves externas que apuntan a los alumnos. Cuando copie alumnos en la tabla Person, obtendrán nuevos valores de clave principal.

* Copia datos de la tabla Student a la tabla Person. Esto hace que los alumnos se asignen a nuevos valores de clave principal.

* Corrige los valores de clave externa correspondientes a los alumnos.

* Vuelve a crear las restricciones y los índices de la clave externa, pero ahora los dirige a la tabla Person.

(Si hubiera usado el GUID en lugar de un número entero como tipo de clave principal, los valores de la clave principal de alumno no tendrían que cambiar y algunos de estos pasos se podrían haber omitido).

Ejecute el comando `database update`:

```dotnetcli
dotnet ef database update
```

(En un sistema de producción haría los cambios correspondientes en el método `Down` por si alguna vez tuviera que usarlo para volver a la versión anterior de la base de datos. Para este tutorial, no se usará el método `Down`).

> [!NOTE]
> Al hacer cambios en el esquema, se pueden generar otros errores en una base de datos que contenga los datos existentes. Si se producen errores de migración que no se pueden resolver, puede cambiar el nombre de la base de datos en la cadena de conexión o eliminar la base de datos. Con una nueva base de datos, no habrá ningún dato para migrar y es más probable que el comando de actualización de base de datos se complete sin errores. Para eliminar la base de datos, use SSOX o ejecute el comando `database drop` de la CLI.

## <a name="test-the-implementation"></a>Prueba la implementación

Ejecute la aplicación y haga la prueba en distintas páginas. Todo funciona igual que antes.

En **Explorador de objetos de SQL Server**, expanda **Conexiones de datos/SchoolContext** y después **Tables**, y verá que las tablas Student e Instructor se han reemplazado por una tabla Person. Abra el diseñador de la tabla Person y verá que contiene todas las columnas que solía haber en las tablas Student e Instructor.

![Tabla Person en SSOX](inheritance/_static/ssox-person-table.png)

Haga clic con el botón derecho en la tabla Person y después haga clic en **Mostrar datos de tabla** para ver la columna discriminadora.

![Tabla Person en SSOX: datos de la tabla](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a>Obtención del código

[Descargue o vea la aplicación completa.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Recursos adicionales

Para obtener más información sobre la herencia en Entity Framework Core, consulte [Herencia](/ef/core/modeling/inheritance).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha:

> [!div class="checklist"]
> * Asignado la herencia a la base de datos
> * Creado la clase Person
> * Actualizado Instructor y Student
> * Agregado Person al modelo
> * Creado y actualizado migraciones
> * Probado la implementación

Pase al tutorial siguiente para obtener información sobre cómo controlar una serie de escenarios de Entity Framework relativamente avanzados.

> [!div class="nextstepaction"]
> [Siguiente: Temas avanzados](advanced.md)
