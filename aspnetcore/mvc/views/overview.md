---
title: Vistas de ASP.NET Core MVC
author: ardalis
description: Obtenga información sobre la forma en que las vistas controlan la presentación de datos de la aplicación y la interacción del usuario en ASP.NET Core MVC.
ms.author: riande
ms.date: 12/05/2019
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
uid: mvc/views/overview
ms.openlocfilehash: 8630df0ad8ea556c6edf0ab251b3c86493f751e2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020878"
---
# <a name="views-in-aspnet-core-mvc"></a>Vistas de ASP.NET Core MVC

Por [Steve Smith](https://ardalis.com/)

En este documento se explican las vistas utilizadas en las aplicaciones de ASP.NET Core MVC. Para obtener información sobre Razor las páginas, consulte [Introducción a Razor las páginas](xref:razor-pages/index).

En el patrón de controlador de vista de modelos (MVC), la *vista* se encarga de la presentación de los datos y de la interacción del usuario. Una vista es una plantilla HTML con [ Razor marcado](xref:mvc/views/razor)incrustado. Razorel marcado es código que interactúa con el marcado HTML para generar una página web que se envía al cliente.

En ASP.NET Core MVC, las vistas son archivos *. cshtml* que usan el [lenguaje de programación C#](/dotnet/csharp/) en el Razor marcado. Por lo general, los archivos de vistas se agrupan en carpetas con el nombre de cada uno de los [controladores](xref:mvc/controllers/actions) de la aplicación. Las carpetas se almacenan en una carpeta llamada *Views* que está ubicada en la raíz de la aplicación:

![Carpeta Views del Explorador de soluciones de Visual Studio abierta con la carpeta Home mostrando los archivos About.cshtml, Contact.cshtml y Index.cshtml](overview/_static/views_solution_explorer.png)

El controlador *Home* está representado por una carpeta *Home* situada dentro de la carpeta *Views*. La carpeta *Home* contiene las vistas correspondientes a las páginas web *About*, *Contact* e *Index* (página principal). Cuando un usuario solicita una de estas tres páginas web, las acciones del controlador *Home* determinan cuál de las tres vistas se usa para crear y devolver una página web al usuario.

Use [diseños](xref:mvc/views/layout) para cohesionar las secciones de la página web y reducir la repetición del código. Los diseños suelen contener encabezado, elementos de menú y navegación y pie de página. El encabezado y el pie de página suelen contener marcado reutilizable para muchos elementos de metadatos y vínculos a recursos de script y estilo. Los diseños ayudan a evitar este marcado reutilizable en las vistas.

Las [vistas parciales](xref:mvc/views/partial) administran las partes reutilizables de las vistas para reducir la duplicación de código. Por ejemplo, resulta útil usar una vista parcial en la biografía de un autor que aparece en varias vistas de un sitio web de blog. Una biografía de autor es contenido de vista normal y no requiere que se ejecute código para generar el contenido de la página web. El contenido de la biografía del autor está disponible para la vista simplemente mediante un enlace de modelos, por lo que resulta ideal usar una vista parcial para este tipo de contenido.

Los [componentes de la vista](xref:mvc/views/view-components) se asemejan a las vistas parciales en que permiten reducir el código repetitivo, pero son adecuados para ver contenido que requiere la ejecución de código en el servidor con el fin de representar la página web. Los componentes de la vista son útiles cuando el contenido representado requiere una interacción con la base de datos, por ejemplo, en el carro de la compra de un sitio web. Los componentes de la vista no se limitan a un enlace de modelos para generar la salida de la página web.

## <a name="benefits-of-using-views"></a>Ventajas de utilizar las vistas

Las vistas separan el marcado de la interfaz de usuario de otras partes de la aplicación con el fin de ayudar a establecer una [separación de intereses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) dentro de una aplicación MVC. Diseñar respetando el principio SoC hace que la aplicación sea modular, lo que ofrece varias ventajas:

* La aplicación es más fácil de mantener, ya que está mejor organizada. Las vistas generalmente se agrupan por característica de la aplicación. Esto facilita la búsqueda de vistas relacionadas cuando se trabaja en una característica.
* Las partes de la aplicación están acopladas de forma ligera. Las vistas de la aplicación se pueden compilar y actualizar por separado de los componentes de la lógica de negocios y el acceso a datos. Las vistas de la aplicación se pueden modificar sin necesidad de tener que actualizar otras partes de la aplicación.
* Es más fácil probar los elementos de la interfaz de usuario de la aplicación, ya que las vistas son unidades independientes.
* Dada la mejora en la organización, es menos probable que se repitan secciones de la interfaz de usuario de forma accidental.

## <a name="creating-a-view"></a>Creación de una vista

Las vistas que son específicas de un controlador se crean en la carpeta *Views/[nombreDelControlador]*. Las vistas compartidas entre controladores se colocan en la carpeta *Views/Shared*. Para crear una vista, agregue un archivo nuevo y asígnele el mismo nombre que a la acción del controlador asociada con la extensión de archivo *.cshtml*. Para crear una vista que se corresponda con la acción *About* del controlador *Home*, cree un archivo *About.cshtml* en la carpeta *Views/Home*:

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

*Razor* el marcado comienza con el `@` símbolo. Ejecute instrucciones de C# colocando código C# dentro de [ Razor bloques de código](xref:mvc/views/razor#razor-code-blocks) establecidos por llaves ( `{ ... }` ). Por ejemplo, vea la asignación de "About" en `ViewData["Title"]` mostrada anteriormente. Para mostrar valores en HTML, simplemente haga referencia al valor con el símbolo `@`. Ver el contenido de los elementos `<h2>` y `<h3>` anteriores.

El contenido de la vista mostrado anteriormente es solo una parte de toda la página web que se presenta al usuario. El resto del diseño de la página y otros aspectos comunes de la vista se especifican en otros archivos de vista. Para obtener más información, consulte el [tema Diseño](xref:mvc/views/layout).

## <a name="how-controllers-specify-views"></a>Cómo especifican los controladores las vistas

Las vistas normalmente las devuelven acciones como [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), que es un tipo de [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult). El método de acción puede crear y devolver `ViewResult` directamente, pero no es lo más común. Puesto que la mayoría de los controladores heredan de [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), simplemente se usa el método del asistente `View` para devolver `ViewResult`:

*HomeController.cs*

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

Cuando esta acción devuelve un resultado, la vista *About.cshtml* mostrada en la última sección se representa como la página web siguiente:

![Página Acerca de representada en el explorador Edge](overview/_static/about-page.png)

El método del asistente `View` tiene varias sobrecargas. También puede especificar:

* Una vista explícita para devolver:

  ```csharp
  return View("Orders");
  ```

* Un [modelo](xref:mvc/models/model-binding) para pasar a la vista:

  ```csharp
  return View(Orders);
  ```

* Una vista y un modelo:

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a>Detección de vista

Cuando una acción devuelve una vista, tiene lugar un proceso llamado *detección de vista*. Este proceso determina qué archivo de vista se utiliza en función del nombre de la vista. 

El comportamiento predeterminado del método `View` (`return View();`) es devolver una vista con el mismo nombre que el método de acción desde el que se llama. Por ejemplo, el nombre de método *About* `ActionResult` del controlador se usa para buscar un archivo de vista denominado *About.cshtml*. En primer lugar, el runtime busca la vista en la carpeta *Views/[nombreDelControlador]*. Si no encuentra una vista que coincida, busca la vista en la carpeta *Shared*.

Da igual si se devuelve implícitamente `ViewResult` con `return View();` o si se pasa explícitamente el nombre de la vista al método `View` con `return View("<ViewName>");`. En ambos casos, la detección de vista busca un archivo de vista coincidente en este orden:

   1. *Views/\[nombreDeControlador]/\[nombreDeVista].cshtml*
   1. *Views/Shared/\[nombreDeVista].cshtml*

En lugar del nombre de una vista, se puede proporcionar la ruta de acceso del archivo de vista. Si se utiliza una ruta de acceso absoluta que comience en la raíz de la aplicación (también puede empezar con "/" o "~/"), debe especificarse la extensión *.cshtml*:

```csharp
return View("Views/Home/About.cshtml");
```

También se puede usar una ruta de acceso relativa para especificar vistas de directorios distintos sin la extensión *.cshtml*. Dentro de `HomeController`, se puede devolver la vista *Index* de las vistas *Manage* con una ruta de acceso relativa:

```csharp
return View("../Manage/Index");
```

De forma similar, se puede indicar el directorio específico del controlador actual con el prefijo "./":

```csharp
return View("./About");
```

[Las vistas parciales](xref:mvc/views/partial) y los [componentes de vista](xref:mvc/views/view-components) usan mecanismos de detección similares (aunque no idénticos).

Puede personalizar la convención predeterminada para la localización de las vistas en la aplicación si utiliza una interfaz [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander) personalizada.

La detección de vistas se basa en la búsqueda de archivos de vista por nombre de archivo. Si el sistema de archivos subyacente distingue mayúsculas de minúsculas, es probable que también lo hagan los nombres de las vistas. Para que haya compatibilidad entre sistemas operativos, haga coincidir las letras mayúsculas y minúsculas de los nombres de controlador y acción con los nombres de archivo y carpetas de vista asociados. Si mientras trabaja con un sistema de archivos que distingue mayúsculas de minúsculas se produce un error que le indica que no se encuentra un archivo de vista, confirme que el archivo de vista solicitado y el nombre de archivo de vista real coinciden en el uso de mayúsculas.

Siga el procedimiento recomendado de organizar la estructura de archivos de vistas de modo que refleje las relaciones existentes entre controladores, acciones y vistas para una mayor claridad y un mantenimiento más sencillo.

## <a name="passing-data-to-views"></a>Paso de datos a las vistas

Se pueden pasar datos a vistas con varios métodos:

* Datos fuertemente tipados: ViewModel
* Datos débilmente tipados
  * `ViewData` (`ViewDataAttribute`)
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a>Datos fuertemente tipados (ViewModel)

El enfoque más eficaz consiste en especificar un tipo de [modelo](xref:mvc/models/model-binding) en la vista. Este modelo se conoce normalmente como *viewmodel* (modelo de vista) y en él se pasa una instancia de tipo viewmodel a la vista de la acción.

La utilización de un modelo de vista para pasar datos a una vista permite que la vista se beneficie de las ventajas de la comprobación de tipos *seguros*. El término *establecimiento fuerte de tipos* (o *fuertemente tipado*) significa que cada variable y constante tienen un tipo definido explícitamente, por ejemplo, `string`, `int` o `DateTime`. La validez de los tipos usados en una vista se comprueba en tiempo de compilación.

[Visual Studio](https://visualstudio.microsoft.com) y [Visual Studio Code](https://code.visualstudio.com/) enumeran los miembros de clase fuertemente tipados mediante una característica denominada [IntelliSense](/visualstudio/ide/using-intellisense). Si quiere ver las propiedades de un modelo de vista, escriba el nombre de variable del modelo de vista seguido por un punto (`.`). Esto ayuda a escribir código más rápidamente y con menos errores.

Especifique un modelo con la directiva `@model`. Utilice el modelo con `@Model`:

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Para proporcionar el modelo a la vista, el controlador lo pasa como un parámetro:

```csharp
public IActionResult Contact()
{
    ViewData["Message"] = "Your contact page.";

    var viewModel = new Address()
    {
        Name = "Microsoft",
        Street = "One Microsoft Way",
        City = "Redmond",
        State = "WA",
        PostalCode = "98052-6399"
    };

    return View(viewModel);
}
```

No hay ninguna restricción sobre los tipos de modelo que se pueden proporcionar a una vista. Se recomienda usar modelos de vista POCO (objeto CRL estándar) con poco o ningún comportamiento (métodos) definido. Por lo general, las clases de modelo de vista se almacenan en la carpeta *Models* o en otra carpeta *ViewModels* en la raíz de la aplicación. El modelo de vista *Address* usado en el ejemplo anterior es un modelo de vista POCO almacenado en un archivo denominado *Address.cs*:

```csharp
namespace WebApplication1.ViewModels
{
    public class Address
    {
        public string Name { get; set; }
        public string Street { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string PostalCode { get; set; }
    }
}
```

Nada le impide usar las mismas clases tanto para los tipos de modelo de vista como para los de modelo de negocio. Pero el uso de modelos independientes permite que las vistas varíen de forma independiente de las partes de lógica de negocios y acceso de datos de la aplicación. La separación de los modelos y los modelos de vista también ofrece ventajas de seguridad cuando los modelos utilizan [enlace de modelo](xref:mvc/models/model-binding) y [validación](xref:mvc/models/validation) para los datos enviados a la aplicación por el usuario.

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a>Datos débilmente tipados (ViewData, atributo ViewData y ViewBag)

`ViewBag`*no está disponible en Razor Páginas.*

Además de las vistas fuertemente tipadas, las vistas tienen acceso a una colección de datos *débilmente tipados*, también denominados *imprecisos*. A diferencia de los tipos fuertes, en los *tipos débiles* (o *débilmente tipados*) no se declara explícitamente el tipo de datos que se está utilizando. Puede usar la colección de datos débilmente tipados para pasar pequeñas cantidades de datos de los controladores y las vistas, tanto en dirección de entrada como de salida.

| Pasar datos entre...                        | Ejemplo                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| Un controlador y una vista                             | Rellenar una lista desplegable con datos.                                          |
| Una vista y una [vista de diseño](xref:mvc/views/layout)   | Establecer el **\<title>** contenido del elemento en la vista de diseño desde un archivo de vista.  |
| Una [vista parcial](xref:mvc/views/partial) y una vista | Un widget que muestra datos basados en la página web que el usuario solicitó.      |

Puede hacer referencia a esta colección a través de las propiedades `ViewData` o `ViewBag` en controladores y vistas. La propiedad `ViewData` es un diccionario de objetos débilmente tipados. La propiedad `ViewBag` es un contenedor alrededor de `ViewData` que proporciona propiedades dinámicas para la colección `ViewData` subyacente. Nota: las búsquedas de clave no distinguen mayúsculas de minúsculas para `ViewData` y `ViewBag` .

`ViewData` y `ViewBag` se resuelven de forma dinámica en tiempo de ejecución. Debido a que no ofrecen la comprobación de tipos en tiempo de compilación, ambas son generalmente más propensas a errores que el uso de un modelo de vista. Por esta razón, algunos desarrolladores prefieren prescindir de `ViewData` y `ViewBag` o usarlos lo menos posible.

<a name="VD"></a>

**ViewData**

`ViewData` es un objeto [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) al que se accede a través de claves `string`. Los datos de cadena se pueden almacenar y utilizar directamente sin necesidad de una conversión, pero primero debe convertir otros valores de objeto `ViewData` a tipos específicos cuando los extrae. Se puede usar `ViewData` para pasar datos de los controladores a las vistas y dentro de las vistas, incluidas las [vistas parciales](xref:mvc/views/partial) y los [diseños](xref:mvc/views/layout).

En el ejemplo siguiente se usa `ViewData` en una acción para establecer los valores de saludo y dirección:

```csharp
public IActionResult SomeAction()
{
    ViewData["Greeting"] = "Hello";
    ViewData["Address"]  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

Trabajar con los datos en una vista:

```cshtml
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```

::: moniker range=">= aspnetcore-2.1"

**Atributo ViewData**

Otro método en el que se usa [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) es [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute). Las propiedades de los controladores o Razor modelos de página marcados con el `[ViewData]` atributo tienen sus valores almacenados y cargados desde el diccionario.

En el siguiente ejemplo, el controlador Home contiene una propiedad `Title` marcada con `[ViewData]`. El método `About` establece el título de la vista About:

```csharp
public class HomeController : Controller
{
    [ViewData]
    public string Title { get; set; }

    public IActionResult About()
    {
        Title = "About Us";
        ViewData["Message"] = "Your application description page.";

        return View();
    }
}
```

En el diseño, el título se lee desde el diccionario ViewData:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

**ViewBag**

`ViewBag`*no está disponible en Razor Páginas.*

`ViewBag` es un objeto [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) que proporciona acceso dinámico a los objetos almacenados en `ViewData`. `ViewBag` puede ser más cómodo de trabajar con él, ya que no requiere conversión. En el ejemplo siguiente se muestra cómo usar `ViewBag` con el mismo resultado que al usar `ViewData` anteriormente:

```csharp
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```cshtml
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```

**Uso simultáneo de ViewData y ViewBag**

`ViewBag`*no está disponible en Razor Páginas.*

Puesto que `ViewData` y `ViewBag` hacen referencia a la misma colección `ViewData` subyacente, se pueden utilizar `ViewData` y `ViewBag`, y combinarlos entre ellos al leer y escribir valores.

Establezca el título con `ViewBag` y la descripción con `ViewData` en la parte superior de una vista *About.cshtml*:

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

Lea las propiedades pero invierta el uso de `ViewData` y `ViewBag`. En el archivo *_Layout.cshtml*, obtenga el título con `ViewData` y la descripción con `ViewBag`:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

Recuerde que las cadenas no requieren una conversión para `ViewData`. Puede usar `@ViewData["Title"]` sin conversión alguna.

Es posible utilizar `ViewData` y `ViewBag` al mismo tiempo, al igual que combinar la lectura y escritura de propiedades. Se representa el marcado siguiente:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

**Resumen de las diferencias entre ViewData y ViewBag**

 `ViewBag`no está disponible en las Razor páginas.

* `ViewData`
  * Se deriva de [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), por lo que tiene propiedades de diccionario que pueden ser útiles, como `ContainsKey` ,, `Add` `Remove` y `Clear` .
  * Las claves del diccionario son cadenas, por lo que se permiten espacios en blanco. Ejemplo: `ViewData["Some Key With Whitespace"]`
  * Todos los tipos excepto `string` deben convertirse en la vista que usa `ViewData`.
* `ViewBag`
  * Se deriva de [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), por lo que permite la creación de propiedades dinámicas mediante la notación de puntos (`@ViewBag.SomeKey = <value or object>`), y no se requiere ninguna conversión. La sintaxis de `ViewBag` hace que sea más rápido de agregar a controladores y vistas.
  * Es más sencillo comprobar si hay valores null. Ejemplo: `@ViewBag.Person?.Name`

**Cuándo utilizar ViewData o ViewBag**

`ViewData` y `ViewBag` son dos enfoques igualmente válidos para pasar pequeñas cantidades de datos entre controladores y vistas. La elección de cuál utilizar se basa en la preferencia. Aunque se pueden combinar objetos `ViewData` y `ViewBag`, el código resulta más fácil de leer y mantener si se utiliza un único enfoque de forma sistemática. Ambos enfoques se resuelven dinámicamente en tiempo de ejecución y, por tanto, son propensos a generar errores en tiempo de ejecución. Algunos equipos de desarrollo los evitan.

### <a name="dynamic-views"></a>Vistas dinámicas

Las vistas que no declaran un tipo modelo con `@model`, pero que tienen una instancia de modelo que se les pasa (por ejemplo, `return View(Address);`), pueden hacer referencia a las propiedades de la instancia dinámicamente:

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Esta característica proporciona la flexibilidad, pero no ofrece protección de compilación ni IntelliSense. Si la propiedad no existe, se produce un error en la generación de la página web en tiempo de ejecución.

## <a name="more-view-features"></a>Más características de vista

Los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) permiten agregar fácilmente el comportamiento del lado servidor a las etiquetas HTML existentes. El uso de asistentes de etiquetas evita la necesidad de escribir código personalizado o asistentes en las vistas. Los asistentes de etiquetas se aplican como atributos a elementos HTML y son ignoradas por los editores que no pueden procesarlas. Esto permite editar y representar el marcado de la vista con varias herramientas.

Hay muchos asistentes de HTML integradas que permiten generar marcado HTML personalizado. La lógica más compleja de la interfaz de usuario se puede administrar mediante [componentes de vista](xref:mvc/views/view-components). Los componentes de vista proporcionan la misma SoC que los controladores y las vistas. En este sentido, pueden eliminar la necesidad de acciones y vistas que se encargan de los datos utilizados por elementos comunes de la interfaz de usuario.

Al igual que muchos otros aspectos de ASP.NET Core, las vistas admiten [inserción de dependencias](xref:fundamentals/dependency-injection), lo que permite que los servicios se puedan [insertar en las vistas](xref:mvc/views/dependency-injection).
