---
title: 'Tutorial: Creación de un modelo de datos complejo: ASP.NET MVC con EF Core'
description: En este tutorial agregará más entidades y relaciones, y personalizará el modelo de datos especificando reglas de formato, validación y asignación.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: cee9e9eb4c5435f3f63f7d1d04f131d88effe9f6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054481"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a><span data-ttu-id="19405-103">Tutorial: Creación de un modelo de datos complejo: ASP.NET MVC con EF Core</span><span class="sxs-lookup"><span data-stu-id="19405-103">Tutorial: Create a complex data model - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="19405-104">En los tutoriales anteriores, trabajó con un modelo de datos simple que se componía de tres entidades.</span><span class="sxs-lookup"><span data-stu-id="19405-104">In the previous tutorials, you worked with a simple data model that was composed of three entities.</span></span> <span data-ttu-id="19405-105">En este tutorial agregará más entidades y relaciones, y personalizará el modelo de datos mediante la especificación de reglas de formato, validación y asignación de base de datos.</span><span class="sxs-lookup"><span data-stu-id="19405-105">In this tutorial, you'll add more entities and relationships and you'll customize the data model by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="19405-106">Cuando haya terminado, las clases de entidad conformarán el modelo de datos completo que se muestra en la ilustración siguiente:</span><span class="sxs-lookup"><span data-stu-id="19405-106">When you're finished, the entity classes will make up the completed data model that's shown in the following illustration:</span></span>

![Diagrama de entidades](complex-data-model/_static/diagram.png)

<span data-ttu-id="19405-108">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="19405-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="19405-109">Personaliza el modelo de datos</span><span class="sxs-lookup"><span data-stu-id="19405-109">Customize the Data model</span></span>
> * <span data-ttu-id="19405-110">Realiza cambios a la entidad Student</span><span class="sxs-lookup"><span data-stu-id="19405-110">Make changes to Student entity</span></span>
> * <span data-ttu-id="19405-111">Crea la entidad Instructor</span><span class="sxs-lookup"><span data-stu-id="19405-111">Create Instructor entity</span></span>
> * <span data-ttu-id="19405-112">Crea la entidad OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="19405-112">Create OfficeAssignment entity</span></span>
> * <span data-ttu-id="19405-113">Modifica la entidad Course</span><span class="sxs-lookup"><span data-stu-id="19405-113">Modify Course entity</span></span>
> * <span data-ttu-id="19405-114">Crea la entidad Department</span><span class="sxs-lookup"><span data-stu-id="19405-114">Create Department entity</span></span>
> * <span data-ttu-id="19405-115">Modifica la entidad Enrollment</span><span class="sxs-lookup"><span data-stu-id="19405-115">Modify Enrollment entity</span></span>
> * <span data-ttu-id="19405-116">Actualizar el contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="19405-116">Update the database context</span></span>
> * <span data-ttu-id="19405-117">Inicializa la base de datos con datos de prueba</span><span class="sxs-lookup"><span data-stu-id="19405-117">Seed database with test data</span></span>
> * <span data-ttu-id="19405-118">Agregar una migración</span><span class="sxs-lookup"><span data-stu-id="19405-118">Add a migration</span></span>
> * <span data-ttu-id="19405-119">Cambiar la cadena de conexión</span><span class="sxs-lookup"><span data-stu-id="19405-119">Change the connection string</span></span>
> * <span data-ttu-id="19405-120">Actualizar la base de datos</span><span class="sxs-lookup"><span data-stu-id="19405-120">Update the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="19405-121">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="19405-121">Prerequisites</span></span>

* [<span data-ttu-id="19405-122">Uso de migraciones de EF Core</span><span class="sxs-lookup"><span data-stu-id="19405-122">Using EF Core migrations</span></span>](migrations.md)

## <a name="customize-the-data-model"></a><span data-ttu-id="19405-123">Personaliza el modelo de datos</span><span class="sxs-lookup"><span data-stu-id="19405-123">Customize the Data model</span></span>

<span data-ttu-id="19405-124">En esta sección verá cómo personalizar el modelo de datos mediante el uso de atributos que especifican reglas de formato, validación y asignación de base de datos.</span><span class="sxs-lookup"><span data-stu-id="19405-124">In this section you'll see how to customize the data model by using attributes that specify formatting, validation, and database mapping rules.</span></span> <span data-ttu-id="19405-125">Después, en varias de las secciones siguientes, creará el modelo de datos School completo mediante la adición de atributos a las clases que ya ha creado y la creación de clases para los demás tipos de entidad del modelo.</span><span class="sxs-lookup"><span data-stu-id="19405-125">Then in several of the following sections you'll create the complete School data model by adding attributes to the classes you already created and creating new classes for the remaining entity types in the model.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="19405-126">El atributo DataType</span><span class="sxs-lookup"><span data-stu-id="19405-126">The DataType attribute</span></span>

<span data-ttu-id="19405-127">Para las fechas de inscripción de estudiantes, en todas las páginas web se muestra actualmente la hora junto con la fecha, aunque todo lo que le interesa para este campo es la fecha.</span><span class="sxs-lookup"><span data-stu-id="19405-127">For student enrollment dates, all of the web pages currently display the time along with the date, although all you care about for this field is the date.</span></span> <span data-ttu-id="19405-128">Mediante los atributos de anotación de datos, puede realizar un cambio de código que fijará el formato de presentación en cada vista en la que se muestren los datos.</span><span class="sxs-lookup"><span data-stu-id="19405-128">By using data annotation attributes, you can make one code change that will fix the display format in every view that shows the data.</span></span> <span data-ttu-id="19405-129">Para ver un ejemplo de cómo hacerlo, deberá agregar un atributo a la propiedad `EnrollmentDate` en la clase `Student`.</span><span class="sxs-lookup"><span data-stu-id="19405-129">To see an example of how to do that, you'll add an attribute to the `EnrollmentDate` property in the `Student` class.</span></span>

<span data-ttu-id="19405-130">En *Models/Student.cs* , agregue una instrucción `using` para el espacio de nombres `System.ComponentModel.DataAnnotations` y los atributos `DataType` y `DisplayFormat` a la propiedad `EnrollmentDate`, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="19405-130">In *Models/Student.cs* , add a `using` statement for the `System.ComponentModel.DataAnnotations` namespace and add `DataType` and `DisplayFormat` attributes to the `EnrollmentDate` property, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="19405-131">El atributo `DataType` se usa para especificar un tipo de datos más específico que el tipo intrínseco de base de datos.</span><span class="sxs-lookup"><span data-stu-id="19405-131">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="19405-132">En este caso solo se quiere realizar el seguimiento de la fecha, no de la fecha y la hora.</span><span class="sxs-lookup"><span data-stu-id="19405-132">In this case we only want to keep track of the date, not the date and time.</span></span> <span data-ttu-id="19405-133">La enumeración `DataType` proporciona muchos tipos de datos, como Date (Fecha), Time (Hora), PhoneNumber (Número de teléfono), Currency (Divisa), EmailAddress (Dirección de correo electrónico) y muchos más.</span><span class="sxs-lookup"><span data-stu-id="19405-133">The  `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="19405-134">El atributo `DataType` también puede permitir que la aplicación proporcione automáticamente características específicas del tipo.</span><span class="sxs-lookup"><span data-stu-id="19405-134">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="19405-135">Por ejemplo, se puede crear un vínculo `mailto:` para `DataType.EmailAddress` y se puede proporcionar un selector de datos para `DataType.Date` en exploradores compatibles con HTML5.</span><span class="sxs-lookup"><span data-stu-id="19405-135">For example, a `mailto:` link can be created for `DataType.EmailAddress`, and a date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="19405-136">El atributo `DataType` emite atributos `data-` de HTML 5 (se pronuncia "datos dash") que los exploradores HTML 5 pueden comprender.</span><span class="sxs-lookup"><span data-stu-id="19405-136">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers can understand.</span></span> <span data-ttu-id="19405-137">Los atributos `DataType` no proporcionan ninguna validación.</span><span class="sxs-lookup"><span data-stu-id="19405-137">The `DataType` attributes don't provide any validation.</span></span>

<span data-ttu-id="19405-138">`DataType.Date` no especifica el formato de la fecha que se muestra.</span><span class="sxs-lookup"><span data-stu-id="19405-138">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="19405-139">De manera predeterminada, el campo de datos se muestra según los formatos predeterminados basados en el elemento CultureInfo del servidor.</span><span class="sxs-lookup"><span data-stu-id="19405-139">By default, the data field is displayed according to the default formats based on the server's CultureInfo.</span></span>

<span data-ttu-id="19405-140">El atributo `DisplayFormat` se usa para especificar el formato de fecha de forma explícita:</span><span class="sxs-lookup"><span data-stu-id="19405-140">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="19405-141">El valor `ApplyFormatInEditMode` especifica que el formato se debe aplicar también cuando el valor se muestra en un cuadro de texto para su edición.</span><span class="sxs-lookup"><span data-stu-id="19405-141">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied when the value is displayed in a text box for editing.</span></span> <span data-ttu-id="19405-142">(Es posible que no le interese ese comportamiento para algunos campos, por ejemplo, para los valores de divisa, es posible que no quiera que el símbolo de la divisa se incluya en el cuadro de texto editable).</span><span class="sxs-lookup"><span data-stu-id="19405-142">(You might not want that for some fields -- for example, for currency values, you might not want the currency symbol in the text box for editing.)</span></span>

<span data-ttu-id="19405-143">Se puede usar el atributo `DisplayFormat` por sí solo, pero normalmente se recomienda usar también el atributo `DataType`.</span><span class="sxs-lookup"><span data-stu-id="19405-143">You can use the `DisplayFormat` attribute by itself, but it's generally a good idea to use the `DataType` attribute also.</span></span> <span data-ttu-id="19405-144">El atributo `DataType` transmite la semántica de los datos en contraposición a cómo se representan en una pantalla y ofrece las siguientes ventajas que no proporciona `DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="19405-144">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with `DisplayFormat`:</span></span>

* <span data-ttu-id="19405-145">El explorador puede habilitar características de HTML5 (por ejemplo, para mostrar un control de calendario, el símbolo de divisa adecuado según la configuración regional, vínculos de correo electrónico, validación de entradas del lado cliente, etc.).</span><span class="sxs-lookup"><span data-stu-id="19405-145">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, some client-side input validation, etc.).</span></span>

* <span data-ttu-id="19405-146">De manera predeterminada, el explorador representa los datos con el formato correcto según la configuración regional.</span><span class="sxs-lookup"><span data-stu-id="19405-146">By default, the browser will render data using the correct format based on your locale.</span></span>

<span data-ttu-id="19405-147">Para más información, vea la [documentación del asistente de etiquetas \<input>](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="19405-147">For more information, see the [\<input> tag helper documentation](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span></span>

<span data-ttu-id="19405-148">Ejecute la aplicación, vaya a la página Students Index y verá que ya no se muestran las horas para las fechas de inscripción.</span><span class="sxs-lookup"><span data-stu-id="19405-148">Run the app, go to the Students Index page and notice that times are no longer displayed for the enrollment dates.</span></span> <span data-ttu-id="19405-149">Lo mismo sucede para cualquier vista en la que se use el modelo Student.</span><span class="sxs-lookup"><span data-stu-id="19405-149">The same will be true for any view that uses the Student model.</span></span>

![Página de índice de estudiantes en la que se muestran las fechas sin horas](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="19405-151">El atributo StringLength</span><span class="sxs-lookup"><span data-stu-id="19405-151">The StringLength attribute</span></span>

<span data-ttu-id="19405-152">También puede especificar reglas de validación de datos y mensajes de error de validación mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="19405-152">You can also specify data validation rules and validation error messages using attributes.</span></span> <span data-ttu-id="19405-153">El atributo `StringLength` establece la longitud máxima de la base de datos y proporciona la validación del lado cliente y el lado servidor para ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="19405-153">The `StringLength` attribute sets the maximum length  in the database and provides client side and server side validation for ASP.NET Core MVC.</span></span> <span data-ttu-id="19405-154">En este atributo también se puede especificar la longitud mínima de la cadena, pero el valor mínimo no influye en el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="19405-154">You can also specify the minimum string length in this attribute, but the minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="19405-155">Imagine que quiere asegurarse de que los usuarios no escriban más de 50 caracteres para un nombre.</span><span class="sxs-lookup"><span data-stu-id="19405-155">Suppose you want to ensure that users don't enter more than 50 characters for a name.</span></span> <span data-ttu-id="19405-156">Para agregar esta limitación, agregue atributos `StringLength` a las propiedades `LastName` y `FirstMidName`, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="19405-156">To add this limitation, add `StringLength` attributes to the `LastName` and `FirstMidName` properties, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="19405-157">El atributo `StringLength` no impedirá que un usuario escriba un espacio en blanco para un nombre.</span><span class="sxs-lookup"><span data-stu-id="19405-157">The `StringLength` attribute won't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="19405-158">Puede usar el atributo `RegularExpression` para aplicar restricciones a la entrada.</span><span class="sxs-lookup"><span data-stu-id="19405-158">You can use the `RegularExpression` attribute to apply restrictions to the input.</span></span> <span data-ttu-id="19405-159">Por ejemplo, el código siguiente requiere que el primer carácter sea una letra mayúscula y el resto de caracteres sean alfabéticos:</span><span class="sxs-lookup"><span data-stu-id="19405-159">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

<span data-ttu-id="19405-160">El atributo `MaxLength` proporciona una funcionalidad similar a la del atributo `StringLength` pero no proporciona la validación del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="19405-160">The `MaxLength` attribute provides functionality similar to the `StringLength` attribute but doesn't provide client side validation.</span></span>

<span data-ttu-id="19405-161">Ahora el modelo de base de datos ha cambiado de tal forma que se requiere un cambio en el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="19405-161">The database model has now changed in a way that requires a change in the database schema.</span></span> <span data-ttu-id="19405-162">Deberá usar migraciones para actualizar el esquema sin perder los datos que pueda haber agregado a la base de datos mediante la interfaz de usuario de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="19405-162">You'll use migrations to update the schema without losing any data that you may have added to the database by using the application UI.</span></span>

<span data-ttu-id="19405-163">Guarde los cambios y compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="19405-163">Save your changes and build the project.</span></span> <span data-ttu-id="19405-164">Después, abra la ventana de comandos en la carpeta de proyecto y escriba los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="19405-164">Then open the command window in the project folder and enter the following commands:</span></span>

```dotnetcli
dotnet ef migrations add MaxLengthOnNames
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="19405-165">El comando `migrations add` advierte de que se puede producir pérdida de datos, porque el cambio reduce la longitud máxima para dos columnas.</span><span class="sxs-lookup"><span data-stu-id="19405-165">The `migrations add` command warns that data loss may occur, because the change makes the maximum length shorter for two columns.</span></span>  <span data-ttu-id="19405-166">Las migraciones crean un archivo denominado *\<timeStamp>_MaxLengthOnNames.cs*.</span><span class="sxs-lookup"><span data-stu-id="19405-166">Migrations creates a file named *\<timeStamp>_MaxLengthOnNames.cs*.</span></span> <span data-ttu-id="19405-167">Este archivo contiene código en el método `Up` que actualizará la base de datos para que coincida con el modelo de datos actual.</span><span class="sxs-lookup"><span data-stu-id="19405-167">This file contains code in the `Up` method that will update the database to match the current data model.</span></span> <span data-ttu-id="19405-168">El comando `database update` ejecutó ese código.</span><span class="sxs-lookup"><span data-stu-id="19405-168">The `database update` command ran that code.</span></span>

<span data-ttu-id="19405-169">Entity Framework usa la marca de tiempo que precede al nombre de archivo de migraciones para ordenar las migraciones.</span><span class="sxs-lookup"><span data-stu-id="19405-169">The timestamp prefixed to the migrations file name is used by Entity Framework to order the migrations.</span></span> <span data-ttu-id="19405-170">Puede crear varias migraciones antes de ejecutar el comando de actualización de bases de datos y, después, todas las migraciones se aplican en el orden en el que se hayan creado.</span><span class="sxs-lookup"><span data-stu-id="19405-170">You can create multiple migrations before running the update-database command, and then all of the migrations are applied in the order in which they were created.</span></span>

<span data-ttu-id="19405-171">Ejecute la aplicación, haga clic en la pestaña **Students** , haga clic en **Create New** (Crear) e intente escribir cualquier nombre de más de 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="19405-171">Run the app, select the **Students** tab, click **Create New** , and try to enter either name longer than 50 characters.</span></span> <span data-ttu-id="19405-172">La aplicación debería impedir que lo haga.</span><span class="sxs-lookup"><span data-stu-id="19405-172">The application should prevent you from doing this.</span></span> 

### <a name="the-column-attribute"></a><span data-ttu-id="19405-173">El atributo Column</span><span class="sxs-lookup"><span data-stu-id="19405-173">The Column attribute</span></span>

<span data-ttu-id="19405-174">También puede usar atributos para controlar cómo se asignan las clases y propiedades a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="19405-174">You can also use attributes to control how your classes and properties are mapped to the database.</span></span> <span data-ttu-id="19405-175">Imagine que hubiera usado el nombre `FirstMidName` para el nombre de campo por la posibilidad de que el campo contenga también un segundo nombre.</span><span class="sxs-lookup"><span data-stu-id="19405-175">Suppose you had used the name `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span> <span data-ttu-id="19405-176">Pero quiere que la columna de base de datos se denomine `FirstName`, ya que los usuarios que van a escribir consultas ad hoc en la base de datos están acostumbrados a ese nombre.</span><span class="sxs-lookup"><span data-stu-id="19405-176">But you want the database column to be named `FirstName`, because users who will be writing ad-hoc queries against the database are accustomed to that name.</span></span> <span data-ttu-id="19405-177">Para realizar esta asignación, puede usar el atributo `Column`.</span><span class="sxs-lookup"><span data-stu-id="19405-177">To make this mapping, you can use the `Column` attribute.</span></span>

<span data-ttu-id="19405-178">El atributo `Column` especifica que, cuando se cree la base de datos, la columna de la tabla `Student` que se asigna a la propiedad `FirstMidName` se denominará `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="19405-178">The `Column` attribute specifies that when the database is created, the column of the `Student` table that maps to the `FirstMidName` property will be named `FirstName`.</span></span> <span data-ttu-id="19405-179">En otras palabras, cuando el código hace referencia a `Student.FirstMidName`, los datos procederán o se actualizarán en la columna `FirstName` de la tabla `Student`.</span><span class="sxs-lookup"><span data-stu-id="19405-179">In other words, when your code refers to `Student.FirstMidName`, the data will come from or be updated in the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="19405-180">Si no especifica nombres de columna, se les asigna el mismo nombre que el de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="19405-180">If you don't specify column names, they're given the same name as the property name.</span></span>

<span data-ttu-id="19405-181">En el archivo *Student.cs* , agregue una instrucción `using` para `System.ComponentModel.DataAnnotations.Schema` y agregue el atributo de nombre de columna a la propiedad `FirstMidName`, como se muestra en el código resaltado siguiente:</span><span class="sxs-lookup"><span data-stu-id="19405-181">In the *Student.cs* file, add a `using` statement for `System.ComponentModel.DataAnnotations.Schema` and add the column name attribute to the `FirstMidName` property, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="19405-182">La adición del atributo `Column` cambia el modelo de respaldo de `SchoolContext`, por lo que no coincidirá con la base de datos.</span><span class="sxs-lookup"><span data-stu-id="19405-182">The addition of the `Column` attribute changes the model backing the `SchoolContext`, so it won't match the database.</span></span>

<span data-ttu-id="19405-183">Guarde los cambios y compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="19405-183">Save your changes and build the project.</span></span> <span data-ttu-id="19405-184">Después, abra la ventana de comandos en la carpeta de proyecto y escriba los comandos siguientes para crear otra migración:</span><span class="sxs-lookup"><span data-stu-id="19405-184">Then open the command window in the project folder and enter the following commands to create another migration:</span></span>

```dotnetcli
dotnet ef migrations add ColumnFirstName
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="19405-185">En el **Explorador de objetos de SQL Server** , abra el diseñador de tablas de Student haciendo doble clic en la tabla **Student**.</span><span class="sxs-lookup"><span data-stu-id="19405-185">In **SQL Server Object Explorer** , open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabla de estudiantes en SSOX después de las migraciones](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="19405-187">Antes de aplicar las dos primeras migraciones, las columnas de nombre eran de tipo nvarchar(MAX).</span><span class="sxs-lookup"><span data-stu-id="19405-187">Before you applied the first two migrations, the name columns were of type nvarchar(MAX).</span></span> <span data-ttu-id="19405-188">Ahora son de tipo nvarchar(50) y el nombre de columna ha cambiado de FirstMidName a FirstName.</span><span class="sxs-lookup"><span data-stu-id="19405-188">They're now nvarchar(50) and the column name has changed from FirstMidName to FirstName.</span></span>

> [!Note]
> <span data-ttu-id="19405-189">Si intenta compilar antes de terminar de crear todas las clases de entidad en las secciones siguientes, es posible que se produzcan errores del compilador.</span><span class="sxs-lookup"><span data-stu-id="19405-189">If you try to compile before you finish creating all of the entity classes in the following sections, you might get compiler errors.</span></span>

## <a name="changes-to-student-entity"></a><span data-ttu-id="19405-190">Cambios a la entidad Student</span><span class="sxs-lookup"><span data-stu-id="19405-190">Changes to Student entity</span></span>

![Entidad Student](complex-data-model/_static/student-entity.png)

<span data-ttu-id="19405-192">En *Models/Student.cs* , reemplace el código que agregó anteriormente con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="19405-192">In *Models/Student.cs* , replace the code you added earlier with the following code.</span></span> <span data-ttu-id="19405-193">Los cambios aparecen resaltados.</span><span class="sxs-lookup"><span data-stu-id="19405-193">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="19405-194">El atributo Required</span><span class="sxs-lookup"><span data-stu-id="19405-194">The Required attribute</span></span>

<span data-ttu-id="19405-195">El atributo `Required` hace que las propiedades de nombre sean campos obligatorios.</span><span class="sxs-lookup"><span data-stu-id="19405-195">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="19405-196">El atributo `Required` no es necesario para los tipos que no aceptan valores NULL, como los tipos de valor (DateTime, int, double, float, etc.).</span><span class="sxs-lookup"><span data-stu-id="19405-196">The `Required` attribute isn't needed for non-nullable types such as value types (DateTime, int, double, float, etc.).</span></span> <span data-ttu-id="19405-197">Los tipos que no aceptan valores NULL se tratan automáticamente como campos obligatorios.</span><span class="sxs-lookup"><span data-stu-id="19405-197">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="19405-198">El atributo `Required` se debe usar con `MinimumLength` para que se aplique `MinimumLength`.</span><span class="sxs-lookup"><span data-stu-id="19405-198">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="19405-199">El atributo Display</span><span class="sxs-lookup"><span data-stu-id="19405-199">The Display attribute</span></span>

<span data-ttu-id="19405-200">El atributo `Display` especifica que el título de los cuadros de texto debe ser "First Name" (Nombre), "Last Name" (Apellidos), "Full Name" (Nombre completo) y "Enrollment Date" (Fecha de inscripción) en lugar del nombre de propiedad de cada instancia (que no tiene ningún espacio para dividir las palabras).</span><span class="sxs-lookup"><span data-stu-id="19405-200">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date" instead of the property name in each instance (which has no space dividing the words).</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="19405-201">La propiedad calculada FullName</span><span class="sxs-lookup"><span data-stu-id="19405-201">The FullName calculated property</span></span>

<span data-ttu-id="19405-202">`FullName` es una propiedad calculada que devuelve un valor que se crea mediante la concatenación de otras dos propiedades.</span><span class="sxs-lookup"><span data-stu-id="19405-202">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="19405-203">Por tanto, solo tiene un descriptor de acceso get y no se generará ninguna columna `FullName` en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="19405-203">Therefore it has only a get accessor, and no `FullName` column will be generated in the database.</span></span>

## <a name="create-instructor-entity"></a><span data-ttu-id="19405-204">Crea la entidad Instructor</span><span class="sxs-lookup"><span data-stu-id="19405-204">Create Instructor entity</span></span>

![La entidad Instructor](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="19405-206">Cree *Models/Instructor.cs* y reemplace el código de plantilla con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="19405-206">Create *Models/Instructor.cs* , replacing the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

<span data-ttu-id="19405-207">Tenga en cuenta que varias propiedades son las mismas en las entidades Instructor y Student.</span><span class="sxs-lookup"><span data-stu-id="19405-207">Notice that several properties are the same in the Student and Instructor entities.</span></span> <span data-ttu-id="19405-208">En el tutorial [Implementación de la herencia](inheritance.md) más adelante en esta serie, deberá refactorizar este código para eliminar la redundancia.</span><span class="sxs-lookup"><span data-stu-id="19405-208">In the [Implementing Inheritance](inheritance.md) tutorial later in this series, you'll refactor this code to eliminate the redundancy.</span></span>

<span data-ttu-id="19405-209">Puede colocar varios atributos en una línea, por lo que también puede escribir los atributos `HireDate` como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="19405-209">You can put multiple attributes on one line, so you could also write the `HireDate` attributes as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="19405-210">Las propiedades de navegación CourseAssignments y OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="19405-210">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="19405-211">`CourseAssignments` y `OfficeAssignment` son propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="19405-211">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="19405-212">Un instructor puede impartir cualquier número de cursos, por lo que `CourseAssignments` se define como una colección.</span><span class="sxs-lookup"><span data-stu-id="19405-212">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="19405-213">Si una propiedad de navegación puede contener varias entidades, su tipo debe ser una lista a la que se puedan agregar, eliminar y actualizar entradas.</span><span class="sxs-lookup"><span data-stu-id="19405-213">If a navigation property can hold multiple entities, its type must be a list in which entries can be added, deleted, and updated.</span></span>  <span data-ttu-id="19405-214">Puede especificar `ICollection<T>` o un tipo como `List<T>` o `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="19405-214">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="19405-215">Si especifica `ICollection<T>`, EF crea una colección `HashSet<T>` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="19405-215">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="19405-216">El motivo por el que se trata de entidades `CourseAssignment` se explica a continuación, en la sección sobre relaciones de varios a varios.</span><span class="sxs-lookup"><span data-stu-id="19405-216">The reason why these are `CourseAssignment` entities is explained below in the section about many-to-many relationships.</span></span>

<span data-ttu-id="19405-217">Las reglas de negocio de Contoso University establecen que un instructor solo puede tener una oficina a lo sumo, por lo que la propiedad `OfficeAssignment` contiene una única entidad OfficeAssignment (que puede ser NULL si no se asigna ninguna oficina).</span><span class="sxs-lookup"><span data-stu-id="19405-217">Contoso University business rules state that an instructor can only have at most one office, so the `OfficeAssignment` property holds a single OfficeAssignment entity (which may be null if no office is assigned).</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a><span data-ttu-id="19405-218">Crea la entidad OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="19405-218">Create OfficeAssignment entity</span></span>

![Entidad OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="19405-220">Cree *Models/OfficeAssignment.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="19405-220">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="19405-221">El atributo Key</span><span class="sxs-lookup"><span data-stu-id="19405-221">The Key attribute</span></span>

<span data-ttu-id="19405-222">Hay una relación de uno a cero o uno entre las entidades Instructor y OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="19405-222">There's a one-to-zero-or-one relationship  between the Instructor and the OfficeAssignment entities.</span></span> <span data-ttu-id="19405-223">Solo existe una asignación de oficina en relación con el instructor al que se asigna y, por tanto, su clave principal también es su clave externa para la entidad Instructor.</span><span class="sxs-lookup"><span data-stu-id="19405-223">An office assignment only exists in relation to the instructor it's assigned to, and therefore its primary key is also its foreign key to the Instructor entity.</span></span> <span data-ttu-id="19405-224">Pero Entity Framework no reconoce automáticamente InstructorID como la clave principal de esta entidad porque su nombre no sigue la convención de nomenclatura de ID o classnameID.</span><span class="sxs-lookup"><span data-stu-id="19405-224">But the Entity Framework can't automatically recognize InstructorID as the primary key of this entity because its name doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="19405-225">Por tanto, se usa el atributo `Key` para identificarla como la clave:</span><span class="sxs-lookup"><span data-stu-id="19405-225">Therefore, the `Key` attribute is used to identify it as the key:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="19405-226">También puede usar el atributo `Key` si la entidad tiene su propia clave principal, pero querrá asignar un nombre a la propiedad que no sea classnameID o ID.</span><span class="sxs-lookup"><span data-stu-id="19405-226">You can also use the `Key` attribute if the entity does have its own primary key but you want to name the property something other than classnameID or ID.</span></span>

<span data-ttu-id="19405-227">De forma predeterminada, EF trata la clave como no generada por la base de datos porque la columna es para una relación de identificación.</span><span class="sxs-lookup"><span data-stu-id="19405-227">By default, EF treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="19405-228">La propiedad de navegación Instructor</span><span class="sxs-lookup"><span data-stu-id="19405-228">The Instructor navigation property</span></span>

<span data-ttu-id="19405-229">La entidad Instructor tiene una propiedad de navegación `OfficeAssignment` que acepta valores NULL (porque es posible que no se asigne una oficina a un instructor), y la entidad OfficeAssignment tiene una propiedad de navegación `Instructor` que no acepta valores NULL (porque una asignación de oficina no puede existir sin un instructor; `InstructorID` no acepta valores NULL).</span><span class="sxs-lookup"><span data-stu-id="19405-229">The Instructor entity has a nullable `OfficeAssignment` navigation property (because an instructor might not have an office assignment), and the OfficeAssignment entity has a non-nullable `Instructor` navigation property (because an office assignment can't exist without an instructor -- `InstructorID` is non-nullable).</span></span> <span data-ttu-id="19405-230">Cuando una entidad Instructor tiene una entidad OfficeAssignment relacionada, cada entidad tendrá una referencia a la otra en su propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="19405-230">When an Instructor entity has a related OfficeAssignment entity, each entity will have a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="19405-231">Podría incluir un atributo `[Required]` en la propiedad de navegación de Instructor para especificar que debe haber un instructor relacionado, pero no es necesario hacerlo porque la clave externa `InstructorID` (que también es la clave para esta tabla) no acepta valores NULL.</span><span class="sxs-lookup"><span data-stu-id="19405-231">You could put a `[Required]` attribute on the Instructor navigation property to specify that there must be a related instructor, but you don't have to do that because the `InstructorID` foreign key (which is also the key to this table) is non-nullable.</span></span>

## <a name="modify-course-entity"></a><span data-ttu-id="19405-232">Modifica la entidad Course</span><span class="sxs-lookup"><span data-stu-id="19405-232">Modify Course entity</span></span>

![La entidad Course](complex-data-model/_static/course-entity.png)

<span data-ttu-id="19405-234">En *Models/Course.cs* , reemplace el código que agregó anteriormente con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="19405-234">In *Models/Course.cs* , replace the code you added earlier with the following code.</span></span> <span data-ttu-id="19405-235">Los cambios aparecen resaltados.</span><span class="sxs-lookup"><span data-stu-id="19405-235">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="19405-236">La entidad Course tiene una propiedad de clave externa `DepartmentID` que señala a la entidad Department relacionada y tiene una propiedad de navegación `Department`.</span><span class="sxs-lookup"><span data-stu-id="19405-236">The course entity has a foreign key property `DepartmentID` which points to the related Department entity and it has a `Department` navigation property.</span></span>

<span data-ttu-id="19405-237">Entity Framework no requiere que agregue una propiedad de clave externa al modelo de datos cuando tenga una propiedad de navegación para una entidad relacionada.</span><span class="sxs-lookup"><span data-stu-id="19405-237">The Entity Framework doesn't require you to add a foreign key property to your data model when you have a navigation property for a related entity.</span></span>  <span data-ttu-id="19405-238">EF crea automáticamente claves externas en la base de datos siempre que se necesiten y crea [propiedades reemplazadas](/ef/core/modeling/shadow-properties) para ellas.</span><span class="sxs-lookup"><span data-stu-id="19405-238">EF automatically creates foreign keys in the database wherever they're needed and creates [shadow properties](/ef/core/modeling/shadow-properties) for them.</span></span> <span data-ttu-id="19405-239">Pero tener la clave externa en el modelo de datos puede hacer que las actualizaciones sean más sencillas y eficaces.</span><span class="sxs-lookup"><span data-stu-id="19405-239">But having the foreign key in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="19405-240">Por ejemplo, al recuperar una entidad Course para modificarla, la entidad Department es NULL si no la carga, por lo que cuando se actualiza la entidad Course, deberá capturar primero la entidad Department.</span><span class="sxs-lookup"><span data-stu-id="19405-240">For example, when you fetch a course entity to edit, the  Department entity is null if you don't load it, so when you update the course entity, you would have to first fetch the Department entity.</span></span> <span data-ttu-id="19405-241">Cuando la propiedad de clave externa `DepartmentID` se incluye en el modelo de datos, no es necesario capturar la entidad Department antes de actualizar.</span><span class="sxs-lookup"><span data-stu-id="19405-241">When the foreign key property `DepartmentID` is included in the data model, you don't need to fetch the Department entity before you update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="19405-242">El atributo DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="19405-242">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="19405-243">El atributo `DatabaseGenerated` con el parámetro `None` en la propiedad `CourseID` especifica que los valores de clave principal los proporciona el usuario, en lugar de que los genere la base de datos.</span><span class="sxs-lookup"><span data-stu-id="19405-243">The `DatabaseGenerated` attribute with the `None` parameter on the `CourseID` property specifies that primary key values are provided by the user rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="19405-244">De forma predeterminada, Entity Framework da por supuesto que la base de datos genera los valores de clave principal.</span><span class="sxs-lookup"><span data-stu-id="19405-244">By default, Entity Framework assumes that primary key values are generated by the database.</span></span> <span data-ttu-id="19405-245">Es lo que le interesa en la mayoría de los escenarios.</span><span class="sxs-lookup"><span data-stu-id="19405-245">That's what you want in most scenarios.</span></span> <span data-ttu-id="19405-246">Pero para las entidades Course, usará un número de curso especificado por el usuario como una serie 1000 para un departamento, una serie 2000 para otro y así sucesivamente.</span><span class="sxs-lookup"><span data-stu-id="19405-246">However, for Course entities, you'll use a user-specified course number such as a 1000 series for one department, a 2000 series for another department, and so on.</span></span>

<span data-ttu-id="19405-247">También se puede usar el atributo `DatabaseGenerated` para generar valores predeterminados, como en el caso de las columnas de base de datos que se usan para registrar la fecha de creación o actualización de una fila.</span><span class="sxs-lookup"><span data-stu-id="19405-247">The `DatabaseGenerated` attribute can also be used to generate default values, as in the case of database columns used to record the date a row was created or updated.</span></span>  <span data-ttu-id="19405-248">Para obtener más información, vea [Propiedades generadas](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="19405-248">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="19405-249">Propiedades de clave externa y de navegación</span><span class="sxs-lookup"><span data-stu-id="19405-249">Foreign key and navigation properties</span></span>

<span data-ttu-id="19405-250">Las propiedades de clave externa y las de navegación de la entidad Course reflejan las relaciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="19405-250">The foreign key properties and navigation properties in the Course entity reflect the following relationships:</span></span>

<span data-ttu-id="19405-251">Un curso se asigna a un departamento, por lo que hay una clave externa `DepartmentID` y una propiedad de navegación `Department` por las razones mencionadas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="19405-251">A course is assigned to one department, so there's a `DepartmentID` foreign key and a `Department` navigation property for the reasons mentioned above.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="19405-252">Un curso puede tener cualquier número de alumnos inscritos en él, por lo que la propiedad de navegación `Enrollments` es una colección:</span><span class="sxs-lookup"><span data-stu-id="19405-252">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="19405-253">Un curso puede ser impartido por varios instructores, por lo que la propiedad de navegación `CourseAssignments` es una colección (el tipo `CourseAssignment` se explica [más adelante](#many-to-many-relationships)):</span><span class="sxs-lookup"><span data-stu-id="19405-253">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection (the type `CourseAssignment` is explained [later](#many-to-many-relationships)):</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a><span data-ttu-id="19405-254">Crea la entidad Department</span><span class="sxs-lookup"><span data-stu-id="19405-254">Create Department entity</span></span>

![La entidad Department](complex-data-model/_static/department-entity.png)

<span data-ttu-id="19405-256">Cree *Models/Department.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="19405-256">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="19405-257">El atributo Column</span><span class="sxs-lookup"><span data-stu-id="19405-257">The Column attribute</span></span>

<span data-ttu-id="19405-258">Anteriormente usó el atributo `Column` para cambiar la asignación de nombres de columna.</span><span class="sxs-lookup"><span data-stu-id="19405-258">Earlier you used the `Column` attribute to change column name mapping.</span></span> <span data-ttu-id="19405-259">En el código de la entidad Department, se usa el atributo `Column` para cambiar la asignación de tipos de datos de SQL para que la columna se defina con el tipo de moneda de SQL Server en la base de datos:</span><span class="sxs-lookup"><span data-stu-id="19405-259">In the code for the Department entity, the `Column` attribute is being used to change SQL data type mapping so that the column will be defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="19405-260">Por lo general, la asignación de columnas no es necesaria, dado que Entity Framework elige el tipo de datos de SQL Server adecuado en función del tipo CLR que se defina para la propiedad.</span><span class="sxs-lookup"><span data-stu-id="19405-260">Column mapping is generally not required, because the Entity Framework chooses the appropriate SQL Server data type based on the CLR type that you define for the property.</span></span> <span data-ttu-id="19405-261">El tipo CLR `decimal` se asigna a un tipo `decimal` de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="19405-261">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="19405-262">Pero en este caso se sabe que la columna va a contener cantidades de divisa, y el tipo de datos de divisa es más adecuado para eso.</span><span class="sxs-lookup"><span data-stu-id="19405-262">But in this case you know that the column will be holding currency amounts, and the money data type is more appropriate for that.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="19405-263">Propiedades de clave externa y de navegación</span><span class="sxs-lookup"><span data-stu-id="19405-263">Foreign key and navigation properties</span></span>

<span data-ttu-id="19405-264">Las propiedades de clave externa y de navegación reflejan las relaciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="19405-264">The foreign key and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="19405-265">Un departamento puede tener o no un administrador, y un administrador es siempre un instructor.</span><span class="sxs-lookup"><span data-stu-id="19405-265">A department may or may not have an administrator, and an administrator is always an instructor.</span></span> <span data-ttu-id="19405-266">Por tanto, la propiedad `InstructorID` se incluye como la clave externa de la entidad Instructor y se agrega un signo de interrogación después de la designación del tipo `int` para marcar la propiedad como que acepta valores NULL.</span><span class="sxs-lookup"><span data-stu-id="19405-266">Therefore the `InstructorID` property is included as the foreign key to the Instructor entity, and a question mark is added after the `int` type designation to mark the property as nullable.</span></span> <span data-ttu-id="19405-267">La propiedad de navegación se denomina `Administrator` pero contiene una entidad Instructor:</span><span class="sxs-lookup"><span data-stu-id="19405-267">The navigation property is named `Administrator` but holds an Instructor entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="19405-268">Un departamento puede tener varios cursos, por lo que hay una propiedad de navegación Courses:</span><span class="sxs-lookup"><span data-stu-id="19405-268">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> <span data-ttu-id="19405-269">Por convención, Entity Framework permite la eliminación en cascada para las claves externas que no aceptan valores NULL y para las relaciones de varios a varios.</span><span class="sxs-lookup"><span data-stu-id="19405-269">By convention, the Entity Framework enables cascade delete for non-nullable foreign keys and for many-to-many relationships.</span></span> <span data-ttu-id="19405-270">Esto puede dar lugar a reglas de eliminación en cascada circulares, lo que producirá una excepción al intentar agregar una migración.</span><span class="sxs-lookup"><span data-stu-id="19405-270">This can result in circular cascade delete rules, which will cause an exception when you try to add a migration.</span></span> <span data-ttu-id="19405-271">Por ejemplo, si no definió la propiedad Department.InstructorID como que acepta valores NULL, EF podría configurar una regla de eliminación en cascada para eliminar el departamento cuando se elimine el instructor, que no es lo que quiere que ocurra.</span><span class="sxs-lookup"><span data-stu-id="19405-271">For example, if you didn't define the Department.InstructorID property as nullable, EF would configure a cascade delete rule to delete the department when you delete the instructor, which isn't what you want to have happen.</span></span> <span data-ttu-id="19405-272">Si las reglas de negocio requerían que la propiedad `InstructorID` no acepte valores NULL, tendría que usar la siguiente instrucción de la API fluida para deshabilitar la eliminación en cascada en la relación:</span><span class="sxs-lookup"><span data-stu-id="19405-272">If your business rules required the `InstructorID` property to be non-nullable, you would have to use the following fluent API statement to disable cascade delete on the relationship:</span></span>
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a><span data-ttu-id="19405-273">Modifica la entidad Enrollment</span><span class="sxs-lookup"><span data-stu-id="19405-273">Modify Enrollment entity</span></span>

![La entidad Enrollment](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="19405-275">En *Models/Enrollment.cs* , reemplace el código que agregó anteriormente con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="19405-275">In *Models/Enrollment.cs* , replace the code you added earlier with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="19405-276">Propiedades de clave externa y de navegación</span><span class="sxs-lookup"><span data-stu-id="19405-276">Foreign key and navigation properties</span></span>

<span data-ttu-id="19405-277">Las propiedades de clave externa y de navegación reflejan las relaciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="19405-277">The foreign key properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="19405-278">Un registro de inscripción es para un solo curso, por lo que hay una propiedad de clave externa `CourseID` y una propiedad de navegación `Course`:</span><span class="sxs-lookup"><span data-stu-id="19405-278">An enrollment record is for a single course, so there's a `CourseID` foreign key property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="19405-279">Un registro de inscripción es para un solo estudiante, por lo que hay una propiedad de clave externa `StudentID` y una propiedad de navegación `Student`:</span><span class="sxs-lookup"><span data-stu-id="19405-279">An enrollment record is for a single student, so there's a `StudentID` foreign key property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="19405-280">Relaciones Varios a Varios</span><span class="sxs-lookup"><span data-stu-id="19405-280">Many-to-Many relationships</span></span>

<span data-ttu-id="19405-281">Hay una relación de varios a varios entre las entidades Student y Course, y la entidad Enrollment funciona como una tabla de combinación de varios a varios *con carga* en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="19405-281">There's a many-to-many relationship between the Student and Course entities, and the Enrollment entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="19405-282">"Con carga" significa que la tabla Enrollment contiene datos adicionales además de las claves externas para las tablas combinadas (en este caso, una clave principal y una propiedad Grade).</span><span class="sxs-lookup"><span data-stu-id="19405-282">"With payload" means that the Enrollment table contains additional data besides foreign keys for the joined tables (in this case, a primary key and a Grade property).</span></span>

<span data-ttu-id="19405-283">En la ilustración siguiente se muestra el aspecto de estas relaciones en un diagrama de entidades.</span><span class="sxs-lookup"><span data-stu-id="19405-283">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="19405-284">(Este diagrama se ha generado mediante Entity Framework Power Tools para EF 6.x; la creación del diagrama no forma parte del tutorial, simplemente se usa aquí como una ilustración).</span><span class="sxs-lookup"><span data-stu-id="19405-284">(This diagram was generated using the Entity Framework Power Tools for EF 6.x; creating the diagram isn't part of the tutorial, it's just being used here as an illustration.)</span></span>

![Relación de varios a varios entre estudiantes y cursos](complex-data-model/_static/student-course.png)

<span data-ttu-id="19405-286">Cada línea de relación tiene un 1 en un extremo y un asterisco (\*) en el otro, para indicar una relación uno a varios.</span><span class="sxs-lookup"><span data-stu-id="19405-286">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="19405-287">Si la tabla Enrollment no incluyera información de calificaciones, solo tendría que contener las dos claves externas CourseID y StudentID.</span><span class="sxs-lookup"><span data-stu-id="19405-287">If the Enrollment table didn't include grade information, it would only need to contain the two foreign keys CourseID and StudentID.</span></span> <span data-ttu-id="19405-288">En ese caso, sería una tabla de combinación de varios a varios sin carga (o una tabla de combinación pura) en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="19405-288">In that case, it would be a many-to-many join table without payload (or a pure join table) in the database.</span></span> <span data-ttu-id="19405-289">Las entidades Instructor y Course tienen ese tipo de relación de varios a varios, y el paso siguiente consiste en crear una clase de entidad para que funcione como una tabla de combinación sin carga.</span><span class="sxs-lookup"><span data-stu-id="19405-289">The Instructor and Course entities have that kind of many-to-many relationship, and your next step is to create an entity class to function as a join table without payload.</span></span>

<span data-ttu-id="19405-290">(EF 6.x es compatible con las tablas de combinación implícitas para relaciones de varios a varios, pero EF Core no.</span><span class="sxs-lookup"><span data-stu-id="19405-290">(EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="19405-291">Para obtener más información, vea la [explicación en el repositorio de GitHub EF Core](https://github.com/aspnet/EntityFramework/issues/1368)).</span><span class="sxs-lookup"><span data-stu-id="19405-291">For more information, see the [discussion in the EF Core GitHub repository](https://github.com/aspnet/EntityFramework/issues/1368).)</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="19405-292">La entidad CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="19405-292">The CourseAssignment entity</span></span>

![La entidad CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="19405-294">Cree *Models/CourseAssignment.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="19405-294">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a><span data-ttu-id="19405-295">Nombres de entidades de combinación</span><span class="sxs-lookup"><span data-stu-id="19405-295">Join entity names</span></span>

<span data-ttu-id="19405-296">Se requiere una tabla de combinación en la base de datos para la relación de varios a varios entre Instructor y Courses, y se tiene que representar mediante un conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="19405-296">A join table is required in the database for the Instructor-to-Courses many-to-many relationship, and it has to be represented by an entity set.</span></span> <span data-ttu-id="19405-297">Es habitual asignar el nombre `EntityName1EntityName2` a una entidad de combinación, que en este caso sería `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="19405-297">It's common to name a join entity `EntityName1EntityName2`, which in this case would be `CourseInstructor`.</span></span> <span data-ttu-id="19405-298">Pero se recomienda elegir un nombre que describa la relación.</span><span class="sxs-lookup"><span data-stu-id="19405-298">However, we recommend that you choose a name that describes the relationship.</span></span> <span data-ttu-id="19405-299">Los modelos de datos empiezan de manera sencilla y crecen, y las combinaciones sin carga suelen obtener las cargas más tarde.</span><span class="sxs-lookup"><span data-stu-id="19405-299">Data models start out simple and grow, with no-payload joins frequently getting payloads later.</span></span> <span data-ttu-id="19405-300">Si empieza con un nombre de entidad descriptivo, no tendrá que cambiarlo más adelante.</span><span class="sxs-lookup"><span data-stu-id="19405-300">If you start with a descriptive entity name, you won't have to change the name later.</span></span> <span data-ttu-id="19405-301">Idealmente, la entidad de combinación tendrá su propio nombre natural (posiblemente una sola palabra) en el dominio de empresa.</span><span class="sxs-lookup"><span data-stu-id="19405-301">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="19405-302">Por ejemplo, Books y Customers podrían vincularse a través de Ratings.</span><span class="sxs-lookup"><span data-stu-id="19405-302">For example, Books and Customers could be linked through Ratings.</span></span> <span data-ttu-id="19405-303">Para esta relación, `CourseAssignment` es una opción más adecuada que `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="19405-303">For this relationship, `CourseAssignment` is a better choice than `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="19405-304">Clave compuesta</span><span class="sxs-lookup"><span data-stu-id="19405-304">Composite key</span></span>

<span data-ttu-id="19405-305">Puesto que las claves externas no aceptan valores NULL y juntas identifican de forma única a cada fila de la tabla, una clave principal independiente no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="19405-305">Since the foreign keys are not nullable and together uniquely identify each row of the table, there's no need for a separate primary key.</span></span> <span data-ttu-id="19405-306">Las propiedades *InstructorID* y *CourseID* deben funcionar como una clave principal compuesta.</span><span class="sxs-lookup"><span data-stu-id="19405-306">The *InstructorID* and *CourseID* properties should function as a composite primary key.</span></span> <span data-ttu-id="19405-307">La única manera de identificar claves principales compuestas para EF es mediante la *API fluida* (no se puede realizar mediante el uso de atributos).</span><span class="sxs-lookup"><span data-stu-id="19405-307">The only way to identify composite primary keys to EF is by using the *fluent API* (it can't be done by using attributes).</span></span> <span data-ttu-id="19405-308">En la sección siguiente verá cómo configurar la clave principal compuesta.</span><span class="sxs-lookup"><span data-stu-id="19405-308">You'll see how to configure the composite primary key in the next section.</span></span>

<span data-ttu-id="19405-309">La clave compuesta garantiza que, aunque es posible tener varias filas para un curso y varias filas para un instructor, no se pueden tener varias filas para el mismo instructor y curso.</span><span class="sxs-lookup"><span data-stu-id="19405-309">The composite key ensures that while you can have multiple rows for one course, and multiple rows for one instructor, you can't have multiple rows for the same instructor and course.</span></span> <span data-ttu-id="19405-310">La entidad de combinación `Enrollment` define su propia clave principal, por lo que este tipo de duplicados son posibles.</span><span class="sxs-lookup"><span data-stu-id="19405-310">The `Enrollment` join entity defines its own primary key, so duplicates of this sort are possible.</span></span> <span data-ttu-id="19405-311">Para evitar estos duplicados, podría agregar un índice único en los campos de clave externa o configurar `Enrollment` con una clave principal compuesta similar a `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="19405-311">To prevent such duplicates, you could add a unique index on the foreign key fields, or configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="19405-312">Para obtener más información, vea [Índices](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="19405-312">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="19405-313">Actualizar el contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="19405-313">Update the database context</span></span>

<span data-ttu-id="19405-314">Agregue el código resaltado siguiente al archivo *Data/SchoolContext.cs* :</span><span class="sxs-lookup"><span data-stu-id="19405-314">Add the following highlighted code to the *Data/SchoolContext.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="19405-315">Este código agrega las nuevas entidades y configura la clave principal compuesta de la entidad CourseAssignment.</span><span class="sxs-lookup"><span data-stu-id="19405-315">This code adds the new entities and configures the CourseAssignment entity's composite primary key.</span></span>

## <a name="about-a-fluent-api-alternative"></a><span data-ttu-id="19405-316">Acerca de una alternativa de la API fluida</span><span class="sxs-lookup"><span data-stu-id="19405-316">About a fluent API alternative</span></span>

<span data-ttu-id="19405-317">En el código del método `OnModelCreating` de la clase `DbContext` se usa la *API fluida* para configurar el comportamiento de EF.</span><span class="sxs-lookup"><span data-stu-id="19405-317">The code in the `OnModelCreating` method of the `DbContext` class uses the *fluent API* to configure EF behavior.</span></span> <span data-ttu-id="19405-318">La API se denomina "fluida" porque a menudo se usa para encadenar una serie de llamadas de método en una única instrucción, como en este ejemplo de la [documentación de EF Core](/ef/core/modeling/#use-fluent-api-to-configure-a-model):</span><span class="sxs-lookup"><span data-stu-id="19405-318">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement, as in this example from the [EF Core documentation](/ef/core/modeling/#use-fluent-api-to-configure-a-model):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="19405-319">En este tutorial, solo se usa la API fluida para la asignación de base de datos que no se puede realizar con atributos.</span><span class="sxs-lookup"><span data-stu-id="19405-319">In this tutorial, you're using the fluent API only for database mapping that you can't do with attributes.</span></span> <span data-ttu-id="19405-320">Pero también se puede usar la API fluida para especificar casi todas las reglas de formato, validación y asignación que se pueden realizar mediante el uso de atributos.</span><span class="sxs-lookup"><span data-stu-id="19405-320">However, you can also use the fluent API to specify most of the formatting, validation, and mapping rules that you can do by using attributes.</span></span> <span data-ttu-id="19405-321">Algunos atributos como `MinimumLength` no se pueden aplicar con la API fluida.</span><span class="sxs-lookup"><span data-stu-id="19405-321">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="19405-322">Como se mencionó anteriormente, `MinimumLength` no cambia el esquema, solo aplica una regla de validación del lado cliente y del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="19405-322">As mentioned previously, `MinimumLength` doesn't change the schema, it only applies a client and server side validation rule.</span></span>

<span data-ttu-id="19405-323">Algunos desarrolladores prefieren usar la API fluida exclusivamente para mantener "limpias" las clases de entidad.</span><span class="sxs-lookup"><span data-stu-id="19405-323">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="19405-324">Si quiere, puede mezclar atributos y la API fluida, y hay algunas personalizaciones que solo se pueden realizar mediante la API fluida, pero en general el procedimiento recomendado es elegir uno de estos dos enfoques y usarlo de forma constante siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="19405-324">You can mix attributes and fluent API if you want, and there are a few customizations that can only be done by using fluent API, but in general the recommended practice is to choose one of these two approaches and use that consistently as much as possible.</span></span> <span data-ttu-id="19405-325">Si usa ambos enfoques, tenga en cuenta que siempre que hay un conflicto, la API fluida invalida los atributos.</span><span class="sxs-lookup"><span data-stu-id="19405-325">If you do use both, note that wherever there's a conflict, Fluent API overrides attributes.</span></span>

<span data-ttu-id="19405-326">Para obtener más información sobre la diferencia entre los atributos y la API fluida, vea [Métodos de configuración](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="19405-326">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="19405-327">Diagrama de entidades en el que se muestran las relaciones</span><span class="sxs-lookup"><span data-stu-id="19405-327">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="19405-328">En la siguiente ilustración se muestra el diagrama creado por Entity Framework Power Tools para el modelo School completado.</span><span class="sxs-lookup"><span data-stu-id="19405-328">The following illustration shows the diagram that the Entity Framework Power Tools create for the completed School model.</span></span>

![Diagrama de entidades](complex-data-model/_static/diagram.png)

<span data-ttu-id="19405-330">Además de las líneas de relación uno a varios (1 a \*), aquí se puede ver la línea de relación de uno a cero o uno (1 a 0..1) entre las entidades Instructor y OfficeAssignment, y la línea de relación de cero o uno a varios (0..1 a \*) entre las entidades Instructor y Department.</span><span class="sxs-lookup"><span data-stu-id="19405-330">Besides the one-to-many relationship lines (1 to \*), you can see here the one-to-zero-or-one relationship line (1 to 0..1) between the Instructor and OfficeAssignment entities and the zero-or-one-to-many relationship line (0..1 to \*) between the Instructor and Department entities.</span></span>

## <a name="seed-database-with-test-data"></a><span data-ttu-id="19405-331">Inicializa la base de datos con datos de prueba</span><span class="sxs-lookup"><span data-stu-id="19405-331">Seed database with test data</span></span>

<span data-ttu-id="19405-332">Reemplace el código del archivo *Data/DbInitializer.cs* con el código siguiente para proporcionar datos de inicialización para las nuevas entidades que ha creado.</span><span class="sxs-lookup"><span data-stu-id="19405-332">Replace the code in the *Data/DbInitializer.cs* file with the following code in order to provide seed data for the new entities you've created.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="19405-333">Como vimos en el primer tutorial, la mayor parte de este código simplemente crea objetos de entidad y carga los datos de ejemplo en propiedades según sea necesario para las pruebas.</span><span class="sxs-lookup"><span data-stu-id="19405-333">As you saw in the first tutorial, most of this code simply creates new entity objects and loads sample data into properties as required for testing.</span></span> <span data-ttu-id="19405-334">Tenga en cuenta cómo se administran las relaciones de varios a varios: el código crea relaciones mediante la creación de entidades en los conjuntos de entidades de combinación `Enrollments` y `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="19405-334">Notice how the many-to-many relationships are handled: the code creates relationships by creating entities in the `Enrollments` and `CourseAssignment` join entity sets.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="19405-335">Agregar una migración</span><span class="sxs-lookup"><span data-stu-id="19405-335">Add a migration</span></span>

<span data-ttu-id="19405-336">Guarde los cambios y compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="19405-336">Save your changes and build the project.</span></span> <span data-ttu-id="19405-337">Después, abra la ventana de comandos en la carpeta del proyecto y escriba el comando `migrations add` (no ejecute el comando update-database todavía):</span><span class="sxs-lookup"><span data-stu-id="19405-337">Then open the command window in the project folder and enter the `migrations add` command (don't do the update-database command yet):</span></span>

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

<span data-ttu-id="19405-338">Recibirá una advertencia sobre la posible pérdida de datos.</span><span class="sxs-lookup"><span data-stu-id="19405-338">You get a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="19405-339">Si ahora intentara ejecutar el comando `database update` (no lo haga todavía), obtendría el error siguiente:</span><span class="sxs-lookup"><span data-stu-id="19405-339">If you tried to run the `database update` command at this point (don't do it yet), you would get the following error:</span></span>

> <span data-ttu-id="19405-340">Instrucción ALTER TABLE en conflicto con la restricción FOREIGN KEY "FK_dbo.Course_dbo.Department_DepartmentID".</span><span class="sxs-lookup"><span data-stu-id="19405-340">The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID".</span></span> <span data-ttu-id="19405-341">El conflicto ha aparecido en la base de datos "ContosoUniversity", tabla "dbo.Department", columna "DepartmentID".</span><span class="sxs-lookup"><span data-stu-id="19405-341">The conflict occurred in database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.</span></span>

<span data-ttu-id="19405-342">En ocasiones, al ejecutar migraciones con datos existentes, debe insertar código auxiliar de los datos en la base de datos para satisfacer las restricciones de clave externa.</span><span class="sxs-lookup"><span data-stu-id="19405-342">Sometimes when you execute migrations with existing data, you need to insert stub data into the database to satisfy foreign key constraints.</span></span> <span data-ttu-id="19405-343">El código generado en el método `Up` agrega a la tabla Course una clave externa DepartmentID que no acepta valores NULL.</span><span class="sxs-lookup"><span data-stu-id="19405-343">The generated code in the `Up` method adds a non-nullable DepartmentID foreign key to the Course table.</span></span> <span data-ttu-id="19405-344">Si ya hay filas en la tabla Course cuando se ejecuta el código, se produce un error en la operación `AddColumn` porque SQL Server no sabe qué valor incluir en la columna que no puede ser NULL.</span><span class="sxs-lookup"><span data-stu-id="19405-344">If there are already rows in the Course table when the code runs, the `AddColumn` operation fails because SQL Server doesn't know what value to put in the column that can't be null.</span></span> <span data-ttu-id="19405-345">En este tutorial se va a ejecutar la migración en una base de datos nueva, pero en una aplicación de producción la migración tendría que controlar los datos existentes, por lo que en las instrucciones siguientes se muestra un ejemplo de cómo hacerlo.</span><span class="sxs-lookup"><span data-stu-id="19405-345">For this tutorial you'll run the migration on a new database, but in a production application you'd have to make the migration handle existing data, so the following directions show an example of how to do that.</span></span>

<span data-ttu-id="19405-346">Para realizar este trabajo de migración con datos existentes, tendrá que cambiar el código para asignar un valor predeterminado a la nueva columna y crear un departamento de código auxiliar denominado "Temp" para que actúe como el predeterminado.</span><span class="sxs-lookup"><span data-stu-id="19405-346">To make this migration work with existing data you have to change the code to give the new column a default value, and create a stub department named "Temp" to act as the default department.</span></span> <span data-ttu-id="19405-347">Como resultado, las filas Course existentes estarán relacionadas con el departamento "Temp" después de ejecutar el método `Up`.</span><span class="sxs-lookup"><span data-stu-id="19405-347">As a result, existing Course rows will all be related to the "Temp" department after the `Up` method runs.</span></span>

* <span data-ttu-id="19405-348">Abra el archivo *{marca_de_tiempo}_ComplexDataModel.cs*.</span><span class="sxs-lookup"><span data-stu-id="19405-348">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>

* <span data-ttu-id="19405-349">Convierta en comentario la línea de código que agrega la columna DepartmentID a la tabla Course.</span><span class="sxs-lookup"><span data-stu-id="19405-349">Comment out the line of code that adds the DepartmentID column to the Course table.</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* <span data-ttu-id="19405-350">Agregue el código resaltado siguiente después del código que crea la tabla Department:</span><span class="sxs-lookup"><span data-stu-id="19405-350">Add the following highlighted code after the code that creates the Department table:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="19405-351">En una aplicación de producción, debería escribir código o scripts para agregar filas Department y filas Course relacionadas a las nuevas filas Department.</span><span class="sxs-lookup"><span data-stu-id="19405-351">In a production application, you would write code or scripts to add Department rows and relate Course rows to the new Department rows.</span></span> <span data-ttu-id="19405-352">Después, ya no necesitaría el departamento "Temp" o el valor predeterminado en la columna Course.DepartmentID.</span><span class="sxs-lookup"><span data-stu-id="19405-352">You would then no longer need the "Temp" department or the default value on the Course.DepartmentID column.</span></span>

<span data-ttu-id="19405-353">Guarde los cambios y compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="19405-353">Save your changes and build the project.</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="19405-354">Cambiar la cadena de conexión</span><span class="sxs-lookup"><span data-stu-id="19405-354">Change the connection string</span></span>

<span data-ttu-id="19405-355">Ahora tiene código nuevo en la clase `DbInitializer` que agrega datos de inicialización para las nuevas entidades a una base de datos vacía.</span><span class="sxs-lookup"><span data-stu-id="19405-355">You now have new code in the `DbInitializer` class that adds seed data for the new entities to an empty database.</span></span> <span data-ttu-id="19405-356">Para asegurarse de que EF crea una base de datos vacía, cambie el nombre de la base de datos en la cadena de conexión en *appsettings.json* por ContosoUniversity3 u otro nombre que no haya usado en el equipo que esté usando.</span><span class="sxs-lookup"><span data-stu-id="19405-356">To make EF create a new empty database, change the name of the database in the connection string in *appsettings.json* to ContosoUniversity3 or some other name that you haven't used on the computer you're using.</span></span>

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

<span data-ttu-id="19405-357">Guarde los cambios en *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="19405-357">Save your change to *appsettings.json*.</span></span>

> [!NOTE]
> <span data-ttu-id="19405-358">Como alternativa a cambiar el nombre de la base de datos, puede eliminar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="19405-358">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="19405-359">Use el **Explorador de objetos de SQL Server** (SSOX) o el comando de la CLI `database drop`:</span><span class="sxs-lookup"><span data-stu-id="19405-359">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```dotnetcli
> dotnet ef database drop
> ```

## <a name="update-the-database"></a><span data-ttu-id="19405-360">Actualizar la base de datos</span><span class="sxs-lookup"><span data-stu-id="19405-360">Update the database</span></span>

<span data-ttu-id="19405-361">Después de cambiar el nombre de la base de datos o de eliminarla, ejecute el comando `database update` en la ventana de comandos para ejecutar las migraciones.</span><span class="sxs-lookup"><span data-stu-id="19405-361">After you have changed the database name or deleted the database, run the `database update` command in the command window to execute the migrations.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="19405-362">Ejecute la aplicación para que el método `DbInitializer.Initialize` ejecute y rellene la base de datos nueva.</span><span class="sxs-lookup"><span data-stu-id="19405-362">Run the app to cause the `DbInitializer.Initialize` method to run and populate the new database.</span></span>

<span data-ttu-id="19405-363">Abra la base de datos en SSOX como hizo anteriormente y expanda el nodo **Tablas** para ver que se han creado todas las tablas.</span><span class="sxs-lookup"><span data-stu-id="19405-363">Open the database in SSOX as you did earlier, and expand the **Tables** node to see that all of the tables have been created.</span></span> <span data-ttu-id="19405-364">(Si SSOX sigue abierto de la vez anterior, haga clic en el botón **Actualizar** ).</span><span class="sxs-lookup"><span data-stu-id="19405-364">(If you still have SSOX open from the earlier time, click the **Refresh** button.)</span></span>

![Tablas en SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="19405-366">Ejecute la aplicación para desencadenar el código de inicialización de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="19405-366">Run the app to trigger the initializer code that seeds the database.</span></span>

<span data-ttu-id="19405-367">Haga clic con el botón derecho en la tabla **CourseAssignment** y seleccione **Ver datos** para comprobar que contiene datos.</span><span class="sxs-lookup"><span data-stu-id="19405-367">Right-click the **CourseAssignment** table and select **View Data** to verify that it has data in it.</span></span>

![Datos de CourseAssignment en SSOX](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a><span data-ttu-id="19405-369">Obtención del código</span><span class="sxs-lookup"><span data-stu-id="19405-369">Get the code</span></span>

[<span data-ttu-id="19405-370">Descargue o vea la aplicación completa.</span><span class="sxs-lookup"><span data-stu-id="19405-370">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="19405-371">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="19405-371">Next steps</span></span>

<span data-ttu-id="19405-372">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="19405-372">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="19405-373">Personalizado el modelo de datos</span><span class="sxs-lookup"><span data-stu-id="19405-373">Customized the Data model</span></span>
> * <span data-ttu-id="19405-374">Realizado cambios a la entidad Student</span><span class="sxs-lookup"><span data-stu-id="19405-374">Made changes to Student entity</span></span>
> * <span data-ttu-id="19405-375">Creado la entidad Instructor</span><span class="sxs-lookup"><span data-stu-id="19405-375">Created Instructor entity</span></span>
> * <span data-ttu-id="19405-376">Creado la entidad OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="19405-376">Created OfficeAssignment entity</span></span>
> * <span data-ttu-id="19405-377">Modificado la entidad Course</span><span class="sxs-lookup"><span data-stu-id="19405-377">Modified Course entity</span></span>
> * <span data-ttu-id="19405-378">Creado la entidad Department</span><span class="sxs-lookup"><span data-stu-id="19405-378">Created Department entity</span></span>
> * <span data-ttu-id="19405-379">Modificado la entidad Enrollment</span><span class="sxs-lookup"><span data-stu-id="19405-379">Modified Enrollment entity</span></span>
> * <span data-ttu-id="19405-380">Actualizado el contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="19405-380">Updated the database context</span></span>
> * <span data-ttu-id="19405-381">Inicializado la base de datos con datos de prueba</span><span class="sxs-lookup"><span data-stu-id="19405-381">Seeded database with test data</span></span>
> * <span data-ttu-id="19405-382">Agregado una migración</span><span class="sxs-lookup"><span data-stu-id="19405-382">Added a migration</span></span>
> * <span data-ttu-id="19405-383">Cambiado la cadena de conexión</span><span class="sxs-lookup"><span data-stu-id="19405-383">Changed the connection string</span></span>
> * <span data-ttu-id="19405-384">Actualizado la base de datos</span><span class="sxs-lookup"><span data-stu-id="19405-384">Updated the database</span></span>

<span data-ttu-id="19405-385">Pase al artículo siguiente para más información sobre cómo acceder a datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="19405-385">Advance to the next tutorial to learn more about how to access related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="19405-386">Siguiente: Acceso a datos relacionados</span><span class="sxs-lookup"><span data-stu-id="19405-386">Next: Access related data</span></span>](read-related-data.md)
