---
title: Parte 9. Adición de validación a una aplicación de ASP.NET Core MVC
author: rick-anderson
description: Parte 9 de la serie de tutoriales sobre ASP.NET Core MVC.
ms.author: riande
ms.date: 04/13/2017
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/validation
ms.openlocfilehash: 9a04beab6c00c24e47acca11cfa61d7ca59ab333
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407933"
---
# <a name="part-9-add-validation-to-an-aspnet-core-mvc-app"></a>Parte 9. Adición de validación a una aplicación de ASP.NET Core MVC

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

En esta sección:

* Se agrega lógica de validación al modelo `Movie`.
* Asegúrese de que las reglas de validación se aplican cada vez que un usuario crea o edita una película.

## <a name="keeping-things-dry"></a>Respetar el principio DRY

Uno de los principios de diseño de MVC es [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("Una vez y solo una"). ASP.NET Core MVC le anima a que especifique la funcionalidad o el comportamiento una sola vez y a que luego los refleje en el resto de la aplicación. Esto reduce la cantidad de código que necesita escribir y hace que el código que escribe sea menos propenso a errores, así como más fácil probar y de mantener.

La compatibilidad de validación proporcionada por MVC y Entity Framework Core Code First es un buen ejemplo del principio DRY. Puede especificar las reglas de validación mediante declaración en un lugar (en la clase del modelo) y las reglas se aplican en toda la aplicación.

[!INCLUDE[](~/includes/RP-MVC/validation.md)]

## <a name="validation-error-ui"></a>UI de error de validación

Ejecute la aplicación y navegue al controlador Movies.

Pulse el vínculo **Crear nueva** para agregar una nueva película. Rellene el formulario con algunos valores no válidos. En cuanto la validación del lado cliente de jQuery detecta el problema, muestra un mensaje de error.

![Formulario de vista de películas con varios errores de validación del lado cliente de jQuery](~/tutorials/first-mvc-app/validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

Observe cómo el formulario presenta automáticamente un mensaje de error de validación adecuado en cada campo que contiene un valor no válido. Los errores se aplican en el lado cliente (con JavaScript y jQuery) y en el lado servidor (cuando un usuario tiene JavaScript deshabilitado).

Una ventaja importante es que no fue necesario cambiar ni una sola línea de código en la clase `MoviesController` o en la vista *Create.cshtml* para habilitar esta interfaz de usuario de validación. El controlador y las vistas que creó en pasos anteriores de este tutorial seleccionaron automáticamente las reglas de validación que especificó mediante atributos de validación en las propiedades de la clase del modelo `Movie`. Pruebe la aplicación mediante el método de acción `Edit` y se aplicará la misma validación.

Los datos del formulario no se enviarán al servidor hasta que dejen de producirse errores de validación de cliente. Puede comprobarlo colocando un punto de interrupción en el método `HTTP Post` mediante la [herramienta Fiddler](https://www.telerik.com/fiddler) o las [herramientas de desarrollo F12](/microsoft-edge/devtools-guide).

## <a name="how-validation-works"></a>Cómo funciona la validación

Tal vez se pregunte cómo se generó la validación de la interfaz de usuario sin actualizar el código en el controlador o las vistas. En el código siguiente se muestran los dos métodos `Create`.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippetCreate)]

El primer método de acción `Create` (HTTP GET) muestra el formulario de creación inicial. La segunda versión (`[HttpPost]`) controla el envío de formulario. El segundo método `Create` (la versión `[HttpPost]`) llama a `ModelState.IsValid` para comprobar si la película tiene errores de validación. Al llamar a este método se evalúan todos los atributos de validación que se hayan aplicado al objeto. Si el objeto tiene errores de validación, el método `Create` vuelve a mostrar el formulario. Si no hay ningún error, el método guarda la nueva película en la base de datos. En nuestro ejemplo de película, el formulario no se publica en el servidor si se detectan errores de validación del lado cliente; cuando hay errores de validación en el lado cliente, no se llama nunca al segundo método `Create`. Si deshabilita JavaScript en el explorador, se deshabilita también la validación del cliente y puede probar si el método `Create` HTTP POST `ModelState.IsValid` detecta errores de validación.

Puede establecer un punto de interrupción en el método `[HttpPost] Create` y comprobar si nunca se llama al método. La validación del lado cliente no enviará los datos del formulario si se detectan errores de validación. Si deshabilita JavaScript en el explorador y después envía el formulario con errores, se alcanzará el punto de interrupción. Puede seguir obteniendo validación completa sin JavaScript. 

En la siguiente imagen se muestra cómo deshabilitar JavaScript en el explorador Firefox.

![Firefox: en la pestaña Contenido de la sección Opciones, desactive la casilla Habilitar JavaScript.](~/tutorials/first-mvc-app/validation/_static/ff.png)

En la siguiente imagen se muestra cómo deshabilitar JavaScript en el explorador Chrome.

![Google Chrome: en la sección JavaScript de Configuración de contenido, seleccione la opción para no permitir a ningún sitio ejecutar JavaScript.](~/tutorials/first-mvc-app/validation/_static/chrome.png)

Después de deshabilitar JavaScript, publique los datos no válidos y siga los pasos del depurador.

![Durante la depuración en una publicación de datos no válidos, Intellisense en ModelState.IsValid muestra que el valor es falso.](~/tutorials/first-mvc-app/validation/_static/ms.png)

La parte de la plantilla de visualización *Create.cshtml* se muestra en el marcado siguiente:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/CreateRatingBrevity.html)]

Los métodos de acción utilizan el marcado anterior para mostrar el formulario inicial y para volver a mostrarlo en caso de error.

El [asistente de etiquetas de entrada](xref:mvc/views/working-with-forms) usa los atributos [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) y genera los atributos HTML necesarios para la validación de jQuery en el lado cliente. El [asistente de etiquetas de validación](xref:mvc/views/working-with-forms#the-validation-tag-helpers) muestra errores de validación. Para más información, vea [Introduction to model validation in ASP.NET Core MVC](xref:mvc/models/validation) (Introducción a la validación de modelos en ASP.NET Core MVC).

Lo realmente bueno de este enfoque es que ni el controlador ni la plantilla de vista `Create` saben que las reglas de validación actuales se están aplicando ni conocen los mensajes de error específicos que se muestran. Las reglas de validación y las cadenas de error solo se especifican en la clase `Movie`. Estas mismas reglas de validación se aplican automáticamente a la vista `Edit` y a cualquier otra vista de plantillas creada que edite el modelo.

Cuando necesite cambiar la lógica de validación, puede hacerlo exactamente en un solo lugar mediante la adición de atributos de validación al modelo (en este ejemplo, la clase `Movie`). No tendrá que preocuparse de que diferentes partes de la aplicación sean incoherentes con el modo en que se aplican las reglas: toda la lógica de validación se definirá en un solo lugar y se usará en todas partes. Esto mantiene el código muy limpio y hace que sea fácil de mantener y evolucionar. También significa que respeta totalmente el principio DRY.

## <a name="using-datatype-attributes"></a>Uso de atributos DataType

Abra el archivo *Movie.cs* y examine la clase `Movie`. El espacio de nombres `System.ComponentModel.DataAnnotations` proporciona atributos de formato además del conjunto integrado de atributos de validación. Ya hemos aplicado un valor de enumeración `DataType` en la fecha de lanzamiento y los campos de precio. En el código siguiente se muestran las propiedades `ReleaseDate` y `Price` con el atributo `DataType` adecuado.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

Los atributos `DataType` solo proporcionan sugerencias para que el motor de vista aplique formato a los datos (y ofrece atributos o elementos como `<a>` para las direcciones URL y `<a href="mailto:EmailAddress.com">` para el correo electrónico). Use el atributo `RegularExpression` para validar el formato de los datos. El atributo `DataType` no es un atributo de validación, sino que se usa para especificar un tipo de datos más específico que el tipo intrínseco de la base de datos. En este caso solo queremos realizar un seguimiento de la fecha, no la hora. La enumeración `DataType` proporciona muchos tipos de datos, como Date (Fecha), Time (Hora), PhoneNumber (Número de teléfono), Currency (Moneda), EmailAddress (Dirección de correo electrónico), etc. El atributo `DataType` también puede permitir que la aplicación proporcione automáticamente características específicas del tipo. Por ejemplo, se puede crear un vínculo `mailto:` para `DataType.EmailAddress` y se puede proporcionar un selector de datos para `DataType.Date` en exploradores compatibles con HTML5. Los atributos `DataType` emiten atributos HTML 5 `data-` (se pronuncia con el guion) que los exploradores HTML 5 pueden comprender. Los atributos `DataType`**no** proporcionan ninguna validación.

`DataType.Date` no especifica el formato de la fecha que se muestra. De manera predeterminada, el campo de datos se muestra según los formatos predeterminados basados en el elemento `CultureInfo` del servidor.

El atributo `DisplayFormat` se usa para especificar el formato de fecha de forma explícita:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

El valor `ApplyFormatInEditMode` especifica que el formato se debe aplicar también cuando el valor se muestra en un cuadro de texto para su edición. En algunos campos este comportamiento puede no ser conveniente. Por poner un ejemplo, es probable que con valores de moneda no se quiera que el símbolo de la divisa se incluya en el cuadro de texto editable.

El atributo `DisplayFormat` puede usarse por sí solo, pero normalmente se recomienda usar el atributo `DataType`. El atributo `DataType` transmite la semántica de los datos en contraposición a cómo se representa en una pantalla y ofrece las siguientes ventajas que no proporciona DisplayFormat:

* El explorador puede habilitar características de HTML5 (por ejemplo, mostrar un control de calendario, el símbolo de moneda adecuado según la configuración regional, vínculos de correo electrónico, etc.).

* De manera predeterminada, el explorador representa los datos con el formato correcto según la configuración regional.

* El atributo `DataType` puede habilitar MVC para que elija la plantilla de campo adecuada para representar los datos (`DisplayFormat`, si se usa por sí solo, usa la plantilla de cadena).

> [!NOTE]
> La validación de jQuery no funciona con el atributo `Range` ni `DateTime`. Por ejemplo, el código siguiente siempre muestra un error de validación del lado cliente, incluso cuando la fecha está en el intervalo especificado:
>
> `[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]`

Debe deshabilitar la validación de fechas de jQuery para usar el atributo `Range` con `DateTime`. Por lo general no se recomienda compilar fechas fijas en los modelos, así que desaconseja usar el atributo `Range` y `DateTime`.

El código siguiente muestra la combinación de atributos en una línea:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDAmult.cs?name=snippet1)]

En la siguiente parte de la serie de tutoriales, revisaremos la aplicación y realizaremos algunas mejoras a los métodos `Details` y `Delete` generados automáticamente.

## <a name="additional-resources"></a>Recursos adicionales

* [Trabajar con formularios](xref:mvc/views/working-with-forms)
* [Globalización y localización](xref:fundamentals/localization)
* [Introducción a los asistentes de etiquetas](xref:mvc/views/tag-helpers/intro)
* [Creación de asistentes de etiquetas](xref:mvc/views/tag-helpers/authoring)

> [!div class="step-by-step"]
> [Anterior](new-field.md)
> [Siguiente](details.md)  
