---
title: 'Tutorial: Creación de un modelo de datos complejo: ASP.NET MVC con EF Core'
description: En este tutorial agregará más entidades y relaciones, y personalizará el modelo de datos especificando reglas de formato, validación y asignación.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
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
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: a881ff28d41a272ade559c60efbd884f2a3c4e3e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587793"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a>Tutorial: Creación de un modelo de datos complejo: ASP.NET MVC con EF Core

En los tutoriales anteriores, trabajó con un modelo de datos simple que se componía de tres entidades. En este tutorial agregará más entidades y relaciones, y personalizará el modelo de datos mediante la especificación de reglas de formato, validación y asignación de base de datos.

Cuando haya terminado, las clases de entidad conformarán el modelo de datos completo que se muestra en la ilustración siguiente:

![Diagrama de entidades](complex-data-model/_static/diagram.png)

En este tutorial ha:

> [!div class="checklist"]
> * Personaliza el modelo de datos
> * Realiza cambios a la entidad Student
> * Crea la entidad Instructor
> * Crea la entidad OfficeAssignment
> * Modifica la entidad Course
> * Crea la entidad Department
> * Modifica la entidad Enrollment
> * Actualizar el contexto de base de datos
> * Inicializa la base de datos con datos de prueba
> * Agregar una migración
> * Cambiar la cadena de conexión
> * Actualizar la base de datos

## <a name="prerequisites"></a>Requisitos previos

* [Uso de migraciones de EF Core](migrations.md)

## <a name="customize-the-data-model"></a>Personaliza el modelo de datos

En esta sección verá cómo personalizar el modelo de datos mediante el uso de atributos que especifican reglas de formato, validación y asignación de base de datos. Después, en varias de las secciones siguientes, creará el modelo de datos School completo mediante la adición de atributos a las clases que ya ha creado y la creación de clases para los demás tipos de entidad del modelo.

### <a name="the-datatype-attribute"></a>El atributo DataType

Para las fechas de inscripción de estudiantes, en todas las páginas web se muestra actualmente la hora junto con la fecha, aunque todo lo que le interesa para este campo es la fecha. Mediante los atributos de anotación de datos, puede realizar un cambio de código que fijará el formato de presentación en cada vista en la que se muestren los datos. Para ver un ejemplo de cómo hacerlo, deberá agregar un atributo a la propiedad `EnrollmentDate` en la clase `Student`.

En *Models/Student.cs*, agregue una instrucción `using` para el espacio de nombres `System.ComponentModel.DataAnnotations` y los atributos `DataType` y `DisplayFormat` a la propiedad `EnrollmentDate`, como se muestra en el ejemplo siguiente:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

El atributo `DataType` se usa para especificar un tipo de datos más específico que el tipo intrínseco de base de datos. En este caso solo se quiere realizar el seguimiento de la fecha, no de la fecha y la hora. La enumeración `DataType` proporciona muchos tipos de datos, como Date (Fecha), Time (Hora), PhoneNumber (Número de teléfono), Currency (Divisa), EmailAddress (Dirección de correo electrónico) y muchos más. El atributo `DataType` también puede permitir que la aplicación proporcione automáticamente características específicas del tipo. Por ejemplo, se puede crear un vínculo `mailto:` para `DataType.EmailAddress` y se puede proporcionar un selector de datos para `DataType.Date` en exploradores compatibles con HTML5. El atributo `DataType` emite atributos `data-` de HTML 5 (se pronuncia "datos dash") que los exploradores HTML 5 pueden comprender. Los atributos `DataType` no proporcionan ninguna validación.

`DataType.Date` no especifica el formato de la fecha que se muestra. De manera predeterminada, el campo de datos se muestra según los formatos predeterminados basados en el elemento CultureInfo del servidor.

El atributo `DisplayFormat` se usa para especificar el formato de fecha de forma explícita:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

El valor `ApplyFormatInEditMode` especifica que el formato se debe aplicar también cuando el valor se muestra en un cuadro de texto para su edición. (Es posible que no le interese ese comportamiento para algunos campos, por ejemplo, para los valores de divisa, es posible que no quiera que el símbolo de la divisa se incluya en el cuadro de texto editable).

Se puede usar el atributo `DisplayFormat` por sí solo, pero normalmente se recomienda usar también el atributo `DataType`. El atributo `DataType` transmite la semántica de los datos en contraposición a cómo se representan en una pantalla y ofrece las siguientes ventajas que no proporciona `DisplayFormat`:

* El explorador puede habilitar características de HTML5 (por ejemplo, para mostrar un control de calendario, el símbolo de divisa adecuado según la configuración regional, vínculos de correo electrónico, validación de entradas del lado cliente, etc.).

* De manera predeterminada, el explorador representa los datos con el formato correcto según la configuración regional.

Para más información, vea la [documentación del asistente de etiquetas \<input>](../../mvc/views/working-with-forms.md#the-input-tag-helper).

Ejecute la aplicación, vaya a la página Students Index y verá que ya no se muestran las horas para las fechas de inscripción. Lo mismo sucede para cualquier vista en la que se use el modelo Student.

![Página de índice de estudiantes en la que se muestran las fechas sin horas](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>El atributo StringLength

También puede especificar reglas de validación de datos y mensajes de error de validación mediante atributos. El atributo `StringLength` establece la longitud máxima de la base de datos y proporciona la validación del lado cliente y el lado servidor para ASP.NET Core MVC. En este atributo también se puede especificar la longitud mínima de la cadena, pero el valor mínimo no influye en el esquema de la base de datos.

Imagine que quiere asegurarse de que los usuarios no escriban más de 50 caracteres para un nombre. Para agregar esta limitación, agregue atributos `StringLength` a las propiedades `LastName` y `FirstMidName`, como se muestra en el ejemplo siguiente:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

El atributo `StringLength` no impedirá que un usuario escriba un espacio en blanco para un nombre. Puede usar el atributo `RegularExpression` para aplicar restricciones a la entrada. Por ejemplo, el código siguiente requiere que el primer carácter sea una letra mayúscula y el resto de caracteres sean alfabéticos:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

El atributo `MaxLength` proporciona una funcionalidad similar a la del atributo `StringLength` pero no proporciona la validación del lado cliente.

Ahora el modelo de base de datos ha cambiado de tal forma que se requiere un cambio en el esquema de la base de datos. Deberá usar migraciones para actualizar el esquema sin perder los datos que pueda haber agregado a la base de datos mediante la interfaz de usuario de la aplicación.

Guarde los cambios y compile el proyecto. Después, abra la ventana de comandos en la carpeta de proyecto y escriba los comandos siguientes:

```dotnetcli
dotnet ef migrations add MaxLengthOnNames
```

```dotnetcli
dotnet ef database update
```

El comando `migrations add` advierte de que se puede producir pérdida de datos, porque el cambio reduce la longitud máxima para dos columnas.  Las migraciones crean un archivo denominado *\<timeStamp>_MaxLengthOnNames.cs*. Este archivo contiene código en el método `Up` que actualizará la base de datos para que coincida con el modelo de datos actual. El comando `database update` ejecutó ese código.

Entity Framework usa la marca de tiempo que precede al nombre de archivo de migraciones para ordenar las migraciones. Puede crear varias migraciones antes de ejecutar el comando de actualización de bases de datos y, después, todas las migraciones se aplican en el orden en el que se hayan creado.

Ejecute la aplicación, haga clic en la pestaña **Students**, haga clic en **Create New** (Crear) e intente escribir cualquier nombre de más de 50 caracteres. La aplicación debería impedir que lo haga. 

### <a name="the-column-attribute"></a>El atributo Column

También puede usar atributos para controlar cómo se asignan las clases y propiedades a la base de datos. Imagine que hubiera usado el nombre `FirstMidName` para el nombre de campo por la posibilidad de que el campo contenga también un segundo nombre. Pero quiere que la columna de base de datos se denomine `FirstName`, ya que los usuarios que van a escribir consultas ad hoc en la base de datos están acostumbrados a ese nombre. Para realizar esta asignación, puede usar el atributo `Column`.

El atributo `Column` especifica que, cuando se cree la base de datos, la columna de la tabla `Student` que se asigna a la propiedad `FirstMidName` se denominará `FirstName`. En otras palabras, cuando el código hace referencia a `Student.FirstMidName`, los datos procederán o se actualizarán en la columna `FirstName` de la tabla `Student`. Si no especifica nombres de columna, se les asigna el mismo nombre que el de la propiedad.

En el archivo *Student.cs*, agregue una instrucción `using` para `System.ComponentModel.DataAnnotations.Schema` y agregue el atributo de nombre de columna a la propiedad `FirstMidName`, como se muestra en el código resaltado siguiente:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

La adición del atributo `Column` cambia el modelo de respaldo de `SchoolContext`, por lo que no coincidirá con la base de datos.

Guarde los cambios y compile el proyecto. Después, abra la ventana de comandos en la carpeta de proyecto y escriba los comandos siguientes para crear otra migración:

```dotnetcli
dotnet ef migrations add ColumnFirstName
```

```dotnetcli
dotnet ef database update
```

En el **Explorador de objetos de SQL Server**, abra el diseñador de tablas de Student haciendo doble clic en la tabla **Student**.

![Tabla de estudiantes en SSOX después de las migraciones](complex-data-model/_static/ssox-after-migration.png)

Antes de aplicar las dos primeras migraciones, las columnas de nombre eran de tipo nvarchar(MAX). Ahora son de tipo nvarchar(50) y el nombre de columna ha cambiado de FirstMidName a FirstName.

> [!Note]
> Si intenta compilar antes de terminar de crear todas las clases de entidad en las secciones siguientes, es posible que se produzcan errores del compilador.

## <a name="changes-to-student-entity"></a>Cambios a la entidad Student

![Entidad Student](complex-data-model/_static/student-entity.png)

En *Models/Student.cs*, reemplace el código que agregó anteriormente con el código siguiente. Los cambios aparecen resaltados.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>El atributo Required

El atributo `Required` hace que las propiedades de nombre sean campos obligatorios. El atributo `Required` no es necesario para los tipos que no aceptan valores NULL, como los tipos de valor (DateTime, int, double, float, etc.). Los tipos que no aceptan valores NULL se tratan automáticamente como campos obligatorios.

El atributo `Required` se debe usar con `MinimumLength` para que se aplique `MinimumLength`.

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>El atributo Display

El atributo `Display` especifica que el título de los cuadros de texto debe ser "First Name" (Nombre), "Last Name" (Apellidos), "Full Name" (Nombre completo) y "Enrollment Date" (Fecha de inscripción) en lugar del nombre de propiedad de cada instancia (que no tiene ningún espacio para dividir las palabras).

### <a name="the-fullname-calculated-property"></a>La propiedad calculada FullName

`FullName` es una propiedad calculada que devuelve un valor que se crea mediante la concatenación de otras dos propiedades. Por tanto, solo tiene un descriptor de acceso get y no se generará ninguna columna `FullName` en la base de datos.

## <a name="create-instructor-entity"></a>Crea la entidad Instructor

![La entidad Instructor](complex-data-model/_static/instructor-entity.png)

Cree *Models/Instructor.cs* y reemplace el código de plantilla con el código siguiente:

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

Tenga en cuenta que varias propiedades son las mismas en las entidades Instructor y Student. En el tutorial [Implementación de la herencia](inheritance.md) más adelante en esta serie, deberá refactorizar este código para eliminar la redundancia.

Puede colocar varios atributos en una línea, por lo que también puede escribir los atributos `HireDate` como se indica a continuación:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>Las propiedades de navegación CourseAssignments y OfficeAssignment

`CourseAssignments` y `OfficeAssignment` son propiedades de navegación.

Un instructor puede impartir cualquier número de cursos, por lo que `CourseAssignments` se define como una colección.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Si una propiedad de navegación puede contener varias entidades, su tipo debe ser una lista a la que se puedan agregar, eliminar y actualizar entradas.  Puede especificar `ICollection<T>` o un tipo como `List<T>` o `HashSet<T>`. Si especifica `ICollection<T>`, EF crea una colección `HashSet<T>` de forma predeterminada.

El motivo por el que se trata de entidades `CourseAssignment` se explica a continuación, en la sección sobre relaciones de varios a varios.

Las reglas de negocio de Contoso University establecen que un instructor solo puede tener una oficina a lo sumo, por lo que la propiedad `OfficeAssignment` contiene una única entidad OfficeAssignment (que puede ser NULL si no se asigna ninguna oficina).

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a>Crea la entidad OfficeAssignment

![Entidad OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

Cree *Models/OfficeAssignment.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>El atributo Key

Hay una relación de uno a cero o uno entre las entidades Instructor y OfficeAssignment. Solo existe una asignación de oficina en relación con el instructor al que se asigna y, por tanto, su clave principal también es su clave externa para la entidad Instructor. Pero Entity Framework no reconoce automáticamente InstructorID como la clave principal de esta entidad porque su nombre no sigue la convención de nomenclatura de ID o classnameID. Por tanto, se usa el atributo `Key` para identificarla como la clave:

```csharp
[Key]
public int InstructorID { get; set; }
```

También puede usar el atributo `Key` si la entidad tiene su propia clave principal, pero querrá asignar un nombre a la propiedad que no sea classnameID o ID.

De forma predeterminada, EF trata la clave como no generada por la base de datos porque la columna es para una relación de identificación.

### <a name="the-instructor-navigation-property"></a>La propiedad de navegación Instructor

La entidad Instructor tiene una propiedad de navegación `OfficeAssignment` que acepta valores NULL (porque es posible que no se asigne una oficina a un instructor), y la entidad OfficeAssignment tiene una propiedad de navegación `Instructor` que no acepta valores NULL (porque una asignación de oficina no puede existir sin un instructor; `InstructorID` no acepta valores NULL). Cuando una entidad Instructor tiene una entidad OfficeAssignment relacionada, cada entidad tendrá una referencia a la otra en su propiedad de navegación.

Podría incluir un atributo `[Required]` en la propiedad de navegación de Instructor para especificar que debe haber un instructor relacionado, pero no es necesario hacerlo porque la clave externa `InstructorID` (que también es la clave para esta tabla) no acepta valores NULL.

## <a name="modify-course-entity"></a>Modifica la entidad Course

![La entidad Course](complex-data-model/_static/course-entity.png)

En *Models/Course.cs*, reemplace el código que agregó anteriormente con el código siguiente. Los cambios aparecen resaltados.

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

La entidad Course tiene una propiedad de clave externa `DepartmentID` que señala a la entidad Department relacionada y tiene una propiedad de navegación `Department`.

Entity Framework no requiere que agregue una propiedad de clave externa al modelo de datos cuando tenga una propiedad de navegación para una entidad relacionada.  EF crea automáticamente claves externas en la base de datos siempre que se necesiten y crea [propiedades reemplazadas](/ef/core/modeling/shadow-properties) para ellas. Pero tener la clave externa en el modelo de datos puede hacer que las actualizaciones sean más sencillas y eficaces. Por ejemplo, al recuperar una entidad Course para modificarla, la entidad Department es NULL si no la carga, por lo que cuando se actualiza la entidad Course, deberá capturar primero la entidad Department. Cuando la propiedad de clave externa `DepartmentID` se incluye en el modelo de datos, no es necesario capturar la entidad Department antes de actualizar.

### <a name="the-databasegenerated-attribute"></a>El atributo DatabaseGenerated

El atributo `DatabaseGenerated` con el parámetro `None` en la propiedad `CourseID` especifica que los valores de clave principal los proporciona el usuario, en lugar de que los genere la base de datos.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

De forma predeterminada, Entity Framework da por supuesto que la base de datos genera los valores de clave principal. Es lo que le interesa en la mayoría de los escenarios. Pero para las entidades Course, usará un número de curso especificado por el usuario como una serie 1000 para un departamento, una serie 2000 para otro y así sucesivamente.

También se puede usar el atributo `DatabaseGenerated` para generar valores predeterminados, como en el caso de las columnas de base de datos que se usan para registrar la fecha de creación o actualización de una fila.  Para obtener más información, vea [Propiedades generadas](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Propiedades de clave externa y de navegación

Las propiedades de clave externa y las de navegación de la entidad Course reflejan las relaciones siguientes:

Un curso se asigna a un departamento, por lo que hay una clave externa `DepartmentID` y una propiedad de navegación `Department` por las razones mencionadas anteriormente.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Un curso puede tener cualquier número de alumnos inscritos en él, por lo que la propiedad de navegación `Enrollments` es una colección:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Un curso puede ser impartido por varios instructores, por lo que la propiedad de navegación `CourseAssignments` es una colección (el tipo `CourseAssignment` se explica [más adelante](#many-to-many-relationships)):

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a>Crea la entidad Department

![La entidad Department](complex-data-model/_static/department-entity.png)

Cree *Models/Department.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>El atributo Column

Anteriormente usó el atributo `Column` para cambiar la asignación de nombres de columna. En el código de la entidad Department, se usa el atributo `Column` para cambiar la asignación de tipos de datos de SQL para que la columna se defina con el tipo de moneda de SQL Server en la base de datos:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Por lo general, la asignación de columnas no es necesaria, dado que Entity Framework elige el tipo de datos de SQL Server adecuado en función del tipo CLR que se defina para la propiedad. El tipo CLR `decimal` se asigna a un tipo `decimal` de SQL Server. Pero en este caso se sabe que la columna va a contener cantidades de divisa, y el tipo de datos de divisa es más adecuado para eso.

### <a name="foreign-key-and-navigation-properties"></a>Propiedades de clave externa y de navegación

Las propiedades de clave externa y de navegación reflejan las relaciones siguientes:

Un departamento puede tener o no un administrador, y un administrador es siempre un instructor. Por tanto, la propiedad `InstructorID` se incluye como la clave externa de la entidad Instructor y se agrega un signo de interrogación después de la designación del tipo `int` para marcar la propiedad como que acepta valores NULL. La propiedad de navegación se denomina `Administrator` pero contiene una entidad Instructor:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Un departamento puede tener varios cursos, por lo que hay una propiedad de navegación Courses:

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> Por convención, Entity Framework permite la eliminación en cascada para las claves externas que no aceptan valores NULL y para las relaciones de varios a varios. Esto puede dar lugar a reglas de eliminación en cascada circulares, lo que producirá una excepción al intentar agregar una migración. Por ejemplo, si no definió la propiedad Department.InstructorID como que acepta valores NULL, EF podría configurar una regla de eliminación en cascada para eliminar el departamento cuando se elimine el instructor, que no es lo que quiere que ocurra. Si las reglas de negocio requerían que la propiedad `InstructorID` no acepte valores NULL, tendría que usar la siguiente instrucción de la API fluida para deshabilitar la eliminación en cascada en la relación:
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a>Modifica la entidad Enrollment

![La entidad Enrollment](complex-data-model/_static/enrollment-entity.png)

En *Models/Enrollment.cs*, reemplace el código que agregó anteriormente con el código siguiente:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Propiedades de clave externa y de navegación

Las propiedades de clave externa y de navegación reflejan las relaciones siguientes:

Un registro de inscripción es para un solo curso, por lo que hay una propiedad de clave externa `CourseID` y una propiedad de navegación `Course`:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Un registro de inscripción es para un solo estudiante, por lo que hay una propiedad de clave externa `StudentID` y una propiedad de navegación `Student`:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Relaciones Varios a Varios

Hay una relación de varios a varios entre las entidades Student y Course, y la entidad Enrollment funciona como una tabla de combinación de varios a varios *con carga* en la base de datos. "Con carga" significa que la tabla Enrollment contiene datos adicionales además de las claves externas para las tablas combinadas (en este caso, una clave principal y una propiedad Grade).

En la ilustración siguiente se muestra el aspecto de estas relaciones en un diagrama de entidades. (Este diagrama se ha generado mediante Entity Framework Power Tools para EF 6.x; la creación del diagrama no forma parte del tutorial, simplemente se usa aquí como una ilustración).

![Relación de varios a varios entre estudiantes y cursos](complex-data-model/_static/student-course.png)

Cada línea de relación tiene un 1 en un extremo y un asterisco (*) en el otro, para indicar una relación uno a varios.

Si la tabla Enrollment no incluyera información de calificaciones, solo tendría que contener las dos claves externas CourseID y StudentID. En ese caso, sería una tabla de combinación de varios a varios sin carga (o una tabla de combinación pura) en la base de datos. Las entidades Instructor y Course tienen ese tipo de relación de varios a varios, y el paso siguiente consiste en crear una clase de entidad para que funcione como una tabla de combinación sin carga.

(EF 6.x es compatible con las tablas de combinación implícitas para relaciones de varios a varios, pero EF Core no. Para obtener más información, vea la [explicación en el repositorio de GitHub EF Core](https://github.com/aspnet/EntityFramework/issues/1368)).

## <a name="the-courseassignment-entity"></a>La entidad CourseAssignment

![La entidad CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

Cree *Models/CourseAssignment.cs* con el código siguiente:

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a>Nombres de entidades de combinación

Se requiere una tabla de combinación en la base de datos para la relación de varios a varios entre Instructor y Courses, y se tiene que representar mediante un conjunto de entidades. Es habitual asignar el nombre `EntityName1EntityName2` a una entidad de combinación, que en este caso sería `CourseInstructor`. Pero se recomienda elegir un nombre que describa la relación. Los modelos de datos empiezan de manera sencilla y crecen, y las combinaciones sin carga suelen obtener las cargas más tarde. Si empieza con un nombre de entidad descriptivo, no tendrá que cambiarlo más adelante. Idealmente, la entidad de combinación tendrá su propio nombre natural (posiblemente una sola palabra) en el dominio de empresa. Por ejemplo, Books y Customers podrían vincularse a través de Ratings. Para esta relación, `CourseAssignment` es una opción más adecuada que `CourseInstructor`.

### <a name="composite-key"></a>Clave compuesta

Puesto que las claves externas no aceptan valores NULL y juntas identifican de forma única a cada fila de la tabla, una clave principal independiente no es necesaria. Las propiedades *InstructorID* y *CourseID* deben funcionar como una clave principal compuesta. La única manera de identificar claves principales compuestas para EF es mediante la *API fluida* (no se puede realizar mediante el uso de atributos). En la sección siguiente verá cómo configurar la clave principal compuesta.

La clave compuesta garantiza que, aunque es posible tener varias filas para un curso y varias filas para un instructor, no se pueden tener varias filas para el mismo instructor y curso. La entidad de combinación `Enrollment` define su propia clave principal, por lo que este tipo de duplicados son posibles. Para evitar estos duplicados, podría agregar un índice único en los campos de clave externa o configurar `Enrollment` con una clave principal compuesta similar a `CourseAssignment`. Para obtener más información, vea [Índices](/ef/core/modeling/indexes).

## <a name="update-the-database-context"></a>Actualizar el contexto de base de datos

Agregue el código resaltado siguiente al archivo *Data/SchoolContext.cs*:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Este código agrega las nuevas entidades y configura la clave principal compuesta de la entidad CourseAssignment.

## <a name="about-a-fluent-api-alternative"></a>Acerca de una alternativa de la API fluida

En el código del método `OnModelCreating` de la clase `DbContext` se usa la *API fluida* para configurar el comportamiento de EF. La API se denomina "fluida" porque a menudo se usa para encadenar una serie de llamadas de método en una única instrucción, como en este ejemplo de la [documentación de EF Core](/ef/core/modeling/#use-fluent-api-to-configure-a-model):

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

En este tutorial, solo se usa la API fluida para la asignación de base de datos que no se puede realizar con atributos. Pero también se puede usar la API fluida para especificar casi todas las reglas de formato, validación y asignación que se pueden realizar mediante el uso de atributos. Algunos atributos como `MinimumLength` no se pueden aplicar con la API fluida. Como se mencionó anteriormente, `MinimumLength` no cambia el esquema, solo aplica una regla de validación del lado cliente y del lado servidor.

Algunos desarrolladores prefieren usar la API fluida exclusivamente para mantener "limpias" las clases de entidad. Si quiere, puede mezclar atributos y la API fluida, y hay algunas personalizaciones que solo se pueden realizar mediante la API fluida, pero en general el procedimiento recomendado es elegir uno de estos dos enfoques y usarlo de forma constante siempre que sea posible. Si usa ambos enfoques, tenga en cuenta que siempre que hay un conflicto, la API fluida invalida los atributos.

Para obtener más información sobre la diferencia entre los atributos y la API fluida, vea [Métodos de configuración](/ef/core/modeling/).

## <a name="entity-diagram-showing-relationships"></a>Diagrama de entidades en el que se muestran las relaciones

En la siguiente ilustración se muestra el diagrama creado por Entity Framework Power Tools para el modelo School completado.

![Diagrama de entidades](complex-data-model/_static/diagram.png)

Además de las líneas de relación uno a varios (1 a \*), aquí se puede ver la línea de relación de uno a cero o uno (1 a 0..1) entre las entidades Instructor y OfficeAssignment, y la línea de relación de cero o uno a varios (0..1 a *) entre las entidades Instructor y Department.

## <a name="seed-database-with-test-data"></a>Inicializa la base de datos con datos de prueba

Reemplace el código del archivo *Data/DbInitializer.cs* con el código siguiente para proporcionar datos de inicialización para las nuevas entidades que ha creado.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

Como vimos en el primer tutorial, la mayor parte de este código simplemente crea objetos de entidad y carga los datos de ejemplo en propiedades según sea necesario para las pruebas. Tenga en cuenta cómo se administran las relaciones de varios a varios: el código crea relaciones mediante la creación de entidades en los conjuntos de entidades de combinación `Enrollments` y `CourseAssignment`.

## <a name="add-a-migration"></a>Agregar una migración

Guarde los cambios y compile el proyecto. Después, abra la ventana de comandos en la carpeta del proyecto y escriba el comando `migrations add` (no ejecute el comando update-database todavía):

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

Recibirá una advertencia sobre la posible pérdida de datos.

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

Si ahora intentara ejecutar el comando `database update` (no lo haga todavía), obtendría el error siguiente:

> Instrucción ALTER TABLE en conflicto con la restricción FOREIGN KEY "FK_dbo.Course_dbo.Department_DepartmentID". El conflicto ha aparecido en la base de datos "ContosoUniversity", tabla "dbo.Department", columna "DepartmentID".

En ocasiones, al ejecutar migraciones con datos existentes, debe insertar código auxiliar de los datos en la base de datos para satisfacer las restricciones de clave externa. El código generado en el método `Up` agrega a la tabla Course una clave externa DepartmentID que no acepta valores NULL. Si ya hay filas en la tabla Course cuando se ejecuta el código, se produce un error en la operación `AddColumn` porque SQL Server no sabe qué valor incluir en la columna que no puede ser NULL. En este tutorial se va a ejecutar la migración en una base de datos nueva, pero en una aplicación de producción la migración tendría que controlar los datos existentes, por lo que en las instrucciones siguientes se muestra un ejemplo de cómo hacerlo.

Para realizar este trabajo de migración con datos existentes, tendrá que cambiar el código para asignar un valor predeterminado a la nueva columna y crear un departamento de código auxiliar denominado "Temp" para que actúe como el predeterminado. Como resultado, las filas Course existentes estarán relacionadas con el departamento "Temp" después de ejecutar el método `Up`.

* Abra el archivo *{marca_de_tiempo}_ComplexDataModel.cs*.

* Convierta en comentario la línea de código que agrega la columna DepartmentID a la tabla Course.

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* Agregue el código resaltado siguiente después del código que crea la tabla Department:

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

En una aplicación de producción, debería escribir código o scripts para agregar filas Department y filas Course relacionadas a las nuevas filas Department. Después, ya no necesitaría el departamento "Temp" o el valor predeterminado en la columna Course.DepartmentID.

Guarde los cambios y compile el proyecto.

## <a name="change-the-connection-string"></a>Cambiar la cadena de conexión

Ahora tiene código nuevo en la clase `DbInitializer` que agrega datos de inicialización para las nuevas entidades a una base de datos vacía. Para asegurarse de que EF crea una base de datos vacía, cambie el nombre de la base de datos en la cadena de conexión en *appsettings.json* por ContosoUniversity3 u otro nombre que no haya usado en el equipo que esté usando.

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

Guarde los cambios en *appsettings.json* .

> [!NOTE]
> Como alternativa a cambiar el nombre de la base de datos, puede eliminar la base de datos. Use el **Explorador de objetos de SQL Server** (SSOX) o el comando de la CLI `database drop`:
>
> ```dotnetcli
> dotnet ef database drop
> ```

## <a name="update-the-database"></a>Actualizar la base de datos

Después de cambiar el nombre de la base de datos o de eliminarla, ejecute el comando `database update` en la ventana de comandos para ejecutar las migraciones.

```dotnetcli
dotnet ef database update
```

Ejecute la aplicación para que el método `DbInitializer.Initialize` ejecute y rellene la base de datos nueva.

Abra la base de datos en SSOX como hizo anteriormente y expanda el nodo **Tablas** para ver que se han creado todas las tablas. (Si SSOX sigue abierto de la vez anterior, haga clic en el botón **Actualizar**).

![Tablas en SSOX](complex-data-model/_static/ssox-tables.png)

Ejecute la aplicación para desencadenar el código de inicialización de la base de datos.

Haga clic con el botón derecho en la tabla **CourseAssignment** y seleccione **Ver datos** para comprobar que contiene datos.

![Datos de CourseAssignment en SSOX](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a>Obtención del código

[Descargue o vea la aplicación completa.](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha:

> [!div class="checklist"]
> * Personalizado el modelo de datos
> * Realizado cambios a la entidad Student
> * Creado la entidad Instructor
> * Creado la entidad OfficeAssignment
> * Modificado la entidad Course
> * Creado la entidad Department
> * Modificado la entidad Enrollment
> * Actualizado el contexto de base de datos
> * Inicializado la base de datos con datos de prueba
> * Agregado una migración
> * Cambiado la cadena de conexión
> * Actualizado la base de datos

Pase al artículo siguiente para más información sobre cómo acceder a datos relacionados.

> [!div class="nextstepaction"]
> [Siguiente: Acceso a datos relacionados](read-related-data.md)
