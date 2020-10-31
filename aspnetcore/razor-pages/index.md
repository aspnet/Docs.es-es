---
title: Introducción a Razor Pages en ASP.NET Core
author: Rick-Anderson
description: Obtenga información sobre cómo Razor Pages en ASP.NET Core facilita la programación en escenarios centrados en páginas y mejora la productividad en comparación con el uso de MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
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
uid: razor-pages/index
ms.openlocfilehash: ff045b24c351c696566dee6046fc4b76f8f88e1a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059148"
---
# <a name="introduction-to-no-locrazor-pages-in-aspnet-core"></a>Introducción a Razor Pages en ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Ryan Nowak](https://github.com/rynowak)

Razor Pages facilita la programación de escenarios centrados en páginas y hace que resulte más productiva que con controladores y vistas.

Si busca un tutorial que use el enfoque Model-View-Controller, consulte [Introducción a ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).

En este documento se proporciona una introducción a Razor Pages. No es un tutorial paso a paso. Si encuentra que alguna sección es demasiado avanzada, consulte [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start). Para obtener información general de ASP.NET Core, vea [Introducción a ASP.NET Core](xref:index).

## <a name="prerequisites"></a>Requisitos previos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a>Crear un proyecto de Razor Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Vea [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) para obtener instrucciones detalladas sobre cómo crear un proyecto Razor Pages.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ejecute `dotnet new webapp` desde la línea de comandos.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Vea [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) para obtener instrucciones detalladas sobre cómo crear un proyecto Razor Pages.

---

## <a name="no-locrazor-pages"></a>Razor Pages

Razor Pages está habilitado en *Startup.cs* :

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

Considere la posibilidad de una página básica: <a name="OnGet"></a>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

El código anterior se parece mucho a un [archivo de vista de Razor](xref:tutorials/first-mvc-app/adding-view) que se utiliza en una aplicación ASP.NET Core con controladores y vistas. La directiva [`@page`](xref:mvc/views/razor#page) lo hace diferente. `@page` transforma el archivo en una acción de MVC, lo que significa que administra las solicitudes directamente, sin tener que pasar a través de un controlador. `@page` debe ser la primera directiva de Razorde una página. `@page` afecta al comportamiento de otras construcciones de [Razor](xref:mvc/views/razor). Los nombres de archivo de Razor Pages tienen el sufijo *.cshtml* .

Una página similar, con una clase `PageModel`, se muestra en los dos archivos siguientes. El archivo *Pages/Index2.cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

Modelo de página *Pages/Index2.cshtml.cs* :

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Por convención, el archivo de clase `PageModel` tiene el mismo nombre que el archivo de Razor Pages con *.cs* anexado. Por ejemplo, la instancia de Razor Pages anterior es *Pages/Index2.cshtml* . El archivo que contiene la clase `PageModel` se denomina *Pages/Index2.cshtml.cs* .

Las asociaciones de rutas de dirección URL a páginas se determinan según la ubicación de la página en el sistema de archivos. En la tabla siguiente, se muestra una ruta de acceso Razor Pages y la dirección URL correspondiente:

| Ruta de acceso y nombre de archivo               | URL correspondiente |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` o `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` o `/Store/Index` |

Notas:

* El entorno de ejecución busca archivos de páginas de Razor en la carpeta *Pages* de forma predeterminada.
* `Index` es la página predeterminada cuando una URL no incluye una página.

## <a name="write-a-basic-form"></a>Escritura de un formulario básico

Razor Pages está diseñado para facilitar la implementación de patrones comunes que se usan con exploradores web al compilar una aplicación. Los [enlaces de modelos](xref:mvc/models/model-binding), los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) y los asistentes de HTML *simplemente funcionan* con las propiedades definidas en una clase de Razor Pages. Considere la posibilidad de una página que implementa un formulario básico del estilo "Póngase en contacto con nosotros" para el modelo `Contact`:

Para los ejemplos de este documento, `DbContext` se inicializa en el archivo [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24).

La base de datos en memoria requiere el paquete NuGet `Microsoft.EntityFrameworkCore.InMemory`.

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

El modelo de datos:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

El contexto de la base de datos:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

El archivo de vista *Pages/Create.cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

Modelo de página *Pages/Create.cshtml.cs* :

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

Por convención, la clase `PageModel` se denomina `<PageName>Model` y se encuentra en el mismo espacio de nombres que la página.

La clase `PageModel` permite la separación de la lógica de una página de su presentación. Define los controladores de página para solicitudes que se envían a la página y los datos que usan para representar la página. Esta separación permite lo siguiente:

* Administración de dependencias de página mediante la [inserción de dependencias](xref:fundamentals/dependency-injection).
* [Pruebas unitarias](xref:test/razor-pages-tests)

La página tiene un *método de controlador* `OnPostAsync`, que se ejecuta en solicitudes `POST` (cuando un usuario envía el formulario). Se pueden agregar métodos de controlador para cualquier verbo HTTP. Los controladores más comunes son:

* `OnGet` para inicializar el estado necesario para la página. En el código anterior, el método `OnGet` muestra la instancia *CreateModel.cshtml* de Razor Pages.
* `OnPost` para controlar los envíos del formulario.

El sufijo de nombre `Async` es opcional, pero se usa a menudo por convención para funciones asincrónicas. El código anterior es típico de Razor Pages.

Si está familiarizado con las aplicaciones de ASP.NET con controladores y vistas:

* El código `OnPostAsync` del ejemplo anterior es similar al típico código de controlador.
* La mayoría de los primitivos de MVC, como el [enlace de modelos](xref:mvc/models/model-binding), la [validación](xref:mvc/models/validation) y los resultados de acciones, funcionan del mismo modo con los controladores y Razor Pages. 

El método `OnPostAsync` anterior:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

El flujo básico de `OnPostAsync`:

Compruebe los errores de validación.

* Si no hay ningún error, guarde los datos y redirija.
* Si hay errores, muestre la página de nuevo con mensajes de validación. En muchos casos, los errores de validación se detectan en el cliente y nunca se envían al servidor.

El archivo de vista *Pages/Create.cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

El código HTML representado de *Pages/Create.cshtml* :

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

En el código anterior, la publicación del formulario:

* Con datos válidos:

  * El método del controlador `OnPostAsync` llama al método auxiliar <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*>. `RedirectToPage` devuelve una instancia de <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>. `RedirectToPage`:

    * Es el resultado de una acción.
    * Es similar a `RedirectToAction` o `RedirectToRoute` (se usa en controladores y vistas).
    * Se ha personalizado para las páginas. En el ejemplo anterior, redirige a la página de índice raíz (`/Index`). `RedirectToPage` se detalla en la sección [Generación de direcciones URL para las páginas](#url_gen).

* Con errores de validación que se pasan al servidor:

  * El método del controlador `OnPostAsync` llama al método auxiliar <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*>. `Page` devuelve una instancia de <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>. Devolver `Page` es similar a cómo las acciones en los controladores devuelven `View`. `PageResult` es el tipo de valor devuelto predeterminado para un método de controlador. Un método de controlador que devuelve `void` representa la página.
  * En el ejemplo anterior, la publicación del formulario sin valores hace que se devuelva [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) como false. En este ejemplo, no se muestra ningún error de validación en el cliente. La entrega de errores de validación se trata más adelante en este documento.

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* Con errores de validación detectados mediante la validación del lado cliente:

  * Los datos **no** se publican en el servidor.
  * La validación del lado cliente se explica más adelante en este documento.

La propiedad `Customer` usa el atributo [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) para participar en el enlace de modelos:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

`[BindProperty]`**no** debe usarse en modelos que contengan propiedades que el cliente no debe cambiar. Para más información, consulte [Publicación excesiva](xref:data/ef-rp/crud#overposting).

De forma predeterminada, Razor Pages enlaza propiedades solo con verbos que no sean `GET`. El enlace a propiedades elimina la necesidad de escribir código para convertir los datos HTTP en el tipo de modelo. Enlazar reduce el código al usar la misma propiedad para representar los campos de formulario (`<input asp-for="Customer.Name">`) y aceptar la entrada.

[!INCLUDE[](~/includes/bind-get.md)]

Revisión del archivo de vista *Pages/Create.cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* En el código anterior, el [asistente de etiquetas de entrada](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` enlaza el elemento `<input>` HTML con la expresión del modelo `Customer.Name`.
* [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) hace que los asistentes de etiquetas estén disponibles.

### <a name="the-home-page"></a>La página principal

*Index.cshtml* es la página principal:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

La clase `PageModel` asociada ( *Index.cshtml.cs* ):

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

El archivo *Index.cshtml* contiene el siguiente marcado:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

El `<a /a>` [asistente de etiquetas delimitadoras](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ha usado el atributo `asp-route-{value}` para generar un vínculo a la página de edición. El vínculo contiene datos de ruta con el identificador del contacto. Por ejemplo: `https://localhost:5001/Edit/1`. Las [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) permiten que el código de servidor participe en la creación y la representación de elementos HTML en archivos de Razor.

El archivo *index.cshtml* contiene marcado para crear un botón de eliminar para cada contacto de cliente:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

El código HTML representado:

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

Al representar el botón de eliminar en HTML, el elemento [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) incluye parámetros para los siguientes elementos:

* Id. de contacto de cliente especificado mediante el atributo `asp-route-id`.
* `handler` especificado mediante el atributo `asp-page-handler`.

Al seleccionar el botón, se envía una solicitud de formulario `POST` al servidor. De forma predeterminada, el nombre del método de control se selecciona de acuerdo con el valor del parámetro `handler` y según el esquema `OnPost[handler]Async`.

Como en este ejemplo `handler` es `delete`, el método de control `OnPostDeleteAsync` se usa para procesar la solicitud `POST`. Si `asp-page-handler` se establece en otro valor, como `remove`, se seleccionará un método de controlador llamado `OnPostRemoveAsync`.

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

El método `OnPostDeleteAsync` realiza las acciones siguientes:

* Obtiene el elemento `id` de la cadena de consulta.
* Realiza una consulta a la base de datos del contacto de cliente con `FindAsync`.
* Si se encuentra el contacto del cliente, este se quita y se actualiza la base de datos.
* Llama a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> para redirigir la página Index raíz (`/Index`).

### <a name="the-editcshtml-file"></a>El archivo Edit.cshtml

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

La primera línea contiene la directiva `@page "{id:int}"`. La restricción de enrutamiento `"{id:int}"` indica a la página que acepte las solicitudes a la página que contienen datos de ruta `int`. Si una solicitud a la página no contiene datos de ruta que se puedan convertir en `int`, el tiempo de ejecución devuelve un error HTTP 404 (no encontrado). Para que el identificador sea opcional, anexe `?` a la restricción de ruta:

 ```cshtml
@page "{id:int?}"
```

El archivo *Edit.cshtml.cs* :

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a>Validación

Reglas de validación:

* Se especifican mediante declaración en la clase de modelo.
* Se aplican en toda la aplicación.

El espacio de nombres <xref:System.ComponentModel.DataAnnotations> proporciona un conjunto de atributos de validación integrados que se aplican mediante declaración a una clase o propiedad. DataAnnotations también contiene atributos de formato como [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute), que ayudan a aplicar formato y no proporcionan ninguna validación.

Considere el modelo `Customer`:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Con el siguiente archivo de vista *Create.cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

El código anterior:

* Incluye scripts de validación de jQuery y jQuery.
* Usa los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) `<div />` y `<span />` para habilitar lo siguiente:

  * Validación del lado cliente.
  * Representación del error de validación.

* Se genera el siguiente código HTML:

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

Al publicar el formulario de creación sin un valor de nombre, se muestra el mensaje de error "El campo Nombre es obligatorio". en el formulario. Si JavaScript está habilitado en el cliente, el explorador muestra el error sin realizar la publicación en el servidor.

El atributo `[StringLength(10)]` genera `data-val-length-max="10"` en el código HTML representado. `data-val-length-max` impide que los exploradores superen la longitud máxima especificada al escribir. Si se usa una herramienta como [Fiddler](https://www.telerik.com/fiddler) para editar y reproducir la publicación:

* Con el nombre de más de 10 caracteres.
* Se devolverá el mensaje de error "El nombre del campo debe ser una cadena con una longitud máxima de 10 caracteres". .

Considere el modelo `Movie` siguiente:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Los atributos de validación especifican el comportamiento que se exigirá a las propiedades del modelo al que se aplican:

* Los atributos `Required` y `MinimumLength` indican que una propiedad debe tener un valor, pero nada impide al usuario escribir espacios en blanco para satisfacer esta validación.
* El atributo `RegularExpression` se usa para limitar los caracteres que se pueden escribir. En el código anterior, "Género":

  * Solo debe usar letras.
  * La primera letra debe estar en mayúsculas. No se permiten espacios en blanco, números ni caracteres especiales.

* La "Clasificación" de `RegularExpression`:

  * Requiere que el primer carácter sea una letra mayúscula.
  * Permite caracteres especiales y números en los espacios posteriores. "PG-13" es válido para una "Clasificación", pero se produce un error en un "Género".

* El atributo `Range` restringe un valor a un intervalo determinado.
* El atributo `StringLength` establece la longitud máxima de una propiedad de cadena y, opcionalmente, su longitud mínima.
* Los tipos de valor (como `decimal`, `int`, `float`, `DateTime`) son intrínsecamente necesarios y no necesitan el atributo `[Required]`.

La página de creación del modelo `Movie` muestra errores de visualización con valores no válidos:

![Formulario de vista de película con varios errores de validación de cliente de jQuery](~/tutorials/razor-pages/validation/_static/val.png)

Para obtener más información, consulte:

* [Adición de validación a la aplicación Película](xref:tutorials/razor-pages/validation)
* [Validación de modelos en ASP.NET Core](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Control de solicitudes HEAD con un controlador OnGet de reserva

Las solicitudes `HEAD` permiten recuperar los encabezados de un recurso específico. A diferencia de las solicitudes `GET`, las solicitudes `HEAD` no devuelven un cuerpo de respuesta.

Normalmente, se crea un controlador `OnHead` al que se llama para las solicitudes `HEAD`:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

Razor Pages recurre a una llamada al controlador `OnGet` si no se define ningún controlador `OnHead`.

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a>XSRF/CSRF y Razor Pages

Razor Pages está protegido mediante [validación antifalsificación](xref:security/anti-request-forgery). El elemento [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) inserta tokens antifalsificación en los elementos de formulario HTML.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a>Usar diseños, parciales, plantillas y asistentes de etiquetas con Razor Pages

Las páginas funcionan con todas las características del motor de vista de Razor. Los diseños, parciales, plantillas, asistentes de etiquetas, *_ViewStart.cshtml* y *_ViewImports.cshtml* funcionan de la misma manera que lo hacen con las vistas de Razor convencionales.

Para simplificar esta página, aprovecharemos algunas de esas características.

Agregue una [página de diseño](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

El [diseño](xref:mvc/views/layout):

* Controla el diseño de cada página (a no ser que la página no tenga diseño).
* Importa las estructuras HTML como JavaScript y hojas de estilos.
* El contenido de la página de Razor se representa donde se llama a `@RenderBody()`.

Para más información, consulte la [página de diseño](xref:mvc/views/layout).

La propiedad [Layout](xref:mvc/views/layout#specifying-a-layout) se establece en *Pages/_ViewStart.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

El diseño está en la carpeta *Pages/Shared* . Las páginas buscan otras vistas (diseños, plantillas, parciales) de forma jerárquica, a partir de la misma carpeta que la página actual. Un diseño en la carpeta *Pages/Shared* se puede usar desde cualquier página de Razor en la carpeta *Pages* .

El archivo de diseño debería ir en la carpeta *Pages/Shared* .

Le recomendamos que **no** coloque el archivo de diseño en la carpeta *Views/Shared* . *Views/Shared* es un patrón de vistas de MVC. Razor Pages está diseñado para basarse en la jerarquía de carpetas, no en las convenciones de ruta de acceso.

La búsqueda de vistas de una instancia de Razor Pages incluye la carpeta *Pages* . Los diseños, plantillas y parciales que se usan con los controladores de MVC y las vistas de Razor convencionales *simplemente funcionan* .

Agregue un archivo *Pages/_ViewImports.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace` se explica más adelante en el tutorial. La directiva `@addTagHelper` pone los [asistentes de etiquetas integradas](xref:mvc/views/tag-helpers/builtin-th/Index) en todas las páginas de la carpeta *Pages* .

<a name="namespace"></a>

La directiva `@namespace` establecida en una página:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

La directiva `@namespace` establece el espacio de nombres de la página. La directiva `@model` no necesita incluir el espacio de nombres.

Cuando la directiva `@namespace` se encuentra en *_ViewImports.cshtml* , el espacio de nombres especificado proporciona el prefijo del espacio de nombres generado en la página que importa la directiva `@namespace`. El resto del espacio de nombres generado (la parte del sufijo) es la ruta de acceso relativa separada por puntos entre la carpeta que contiene *_ViewImports.cshtml* y la carpeta que contiene la página.

Por ejemplo, la clase `PageModel`*Pages/Customers/Edit.cshtml.cs* establece explícitamente el espacio de nombres:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

El archivo *Pages/_ViewImports.cshtml* establece el espacio de nombres siguiente:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

El espacio de nombres generado para la página de Razor *Pages/Customers/Edit.cshtml* es el mismo que la clase `PageModel`.

`@namespace` *también funciona con vistas de Razor convencionales.*

Considere el archivo de vista *Pages/Create.cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

Archivo de vista actualizado *Pages/Create.cshtml* con *_ViewImports.cshtml* y el archivo de distribución anterior:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

En el código anterior, el elemento *_ViewImports. cshtml* importó el espacio de nombres y los asistentes de etiquetas. El archivo de distribución importó los archivos JavaScript.

El [proyecto de inicio de Razor Pages](#rpvs17) contiene *Pages/_ValidationScriptsPartial.cshtml* , que enlaza la validación del lado cliente.

Para más información sobre las vistas parciales, vea <xref:mvc/views/partial>.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Generación de direcciones URL para las páginas

La página `Create`, mostrada anteriormente, usa `RedirectToPage`:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

La aplicación tiene la siguiente estructura de archivos o carpetas:

* */Pages*

  * *Index.cshtml*
  * *Privacy.cshtml*
  * */Customers*

    * *Create.cshtml*
    * *Edit.cshtml*
    * *Index.cshtml*

Las páginas *Pages/Customers/Create.cshtml* y *Pages/Customers/Edit.cshtml* redirigen a *Pages/Customers/Index.cshtml* si la operación se realiza correctamente. La cadena `./Index` es un nombre de página relativo que se usa para acceder a la página anterior. Se usa para generar direcciones URL a la página *Pages/Customers/Index.cshtml* . Por ejemplo:

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

El nombre de página absoluto `/Index` se usa para generar direcciones URL a la página *Pages/Index.cshtml* . Por ejemplo:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

El nombre de página es la ruta de acceso a la página de la carpeta raíz */Pages* , incluido un `/` inicial, por ejemplo `/Index`. Los ejemplos anteriores de generación de URL ofrecen opciones mejoradas y capacidades funcionales en comparación con la escritura a mano de estas. La generación de direcciones URL usa el [enrutamiento](xref:mvc/controllers/routing) y puede generar y codificar parámetros según cómo se defina la ruta en la ruta de acceso de destino.

La generación de direcciones URL para las páginas admite nombres relativos. En la siguiente tabla, se muestra qué página de índice está seleccionada con diferentes parámetros `RedirectToPage` en *Pages/Customers/Create.cshtml* .

| RedirectToPage(x)| Página |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Pages/Index* |
| RedirectToPage("./Index"); | *Pages/Customers/Index* |
| RedirectToPage("../Index") | *Pages/Index* |
| RedirectToPage("Index")  | *Pages/Customers/Index* |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

`RedirectToPage("Index")`, `RedirectToPage("./Index")` y `RedirectToPage("../Index")` son *nombres relativos* . El parámetro `RedirectToPage` se *combina* con la ruta de acceso de la página actual para calcular el nombre de la página de destino.

Vincular el nombre relativo es útil al crear sitios con una estructura compleja. Cuando se usan nombres relativos para el vínculo entre las páginas de una carpeta:

* Al cambiar el nombre de una carpeta, no se rompen los vínculos relativos.
* Los vínculos no se rompen porque no incluyen el nombre de la carpeta.

Para redirigir a una página en otra [área](xref:mvc/controllers/areas), especifique el área:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Para obtener más información, vea <xref:mvc/controllers/areas> y <xref:razor-pages/razor-pages-conventions>.

## <a name="viewdata-attribute"></a>Atributo ViewData

Se pueden pasar datos a una página con <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>. Las propiedades con el atributo `[ViewData]` tienen sus valores almacenados y cargados desde el elemento <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.

En el ejemplo siguiente, el elemento `AboutModel` aplica el atributo `[ViewData]` a la propiedad `Title`:

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

En la página Acerca de, acceda a la propiedad `Title` como propiedad de modelo:

```cshtml
<h1>@Model.Title</h1>
```

En el diseño, el título se lee desde el diccionario ViewData:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a>TempData

ASP.NET Core expone el elemento <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>. Esta propiedad almacena datos hasta que se leen. Los métodos <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> y <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> se pueden usar para examinar los datos sin que se eliminen. `TempData` es útil para el redireccionamiento cuando se necesitan los datos de más de una única solicitud.

El siguiente código establece el valor de `Message` mediante `TempData`:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

El siguiente marcado en el archivo *Pages/Customers/Index.cshtml* muestra el valor de `Message` mediante `TempData`.

```cshtml
<h3>Msg: @Model.Message</h3>
```

El modelo de página *Pages/Customers/Index.cshtml.cs* aplica el atributo `[TempData]` a la propiedad `Message`.

```csharp
[TempData]
public string Message { get; set; }
```

Para más información, consulte [TempData](xref:fundamentals/app-state#tempdata).

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Varios controladores por página

En la página siguiente se usa el asistente de etiquetas `asp-page-handler` para generar marcado para dos controladores:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

El formulario del ejemplo anterior tiene dos botones de envío, y cada uno de ellos usa `FormActionTagHelper` para enviar a una dirección URL diferente. El atributo `asp-page-handler` es un complemento de `asp-page`. `asp-page-handler` genera direcciones URL que envían a cada uno de los métodos de controlador definidos por una página. `asp-page` no se especifica porque el ejemplo se vincula a la página actual.

Modelo de página:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

El código anterior usa *métodos de controlador con nombre* . Los métodos de controlador con nombre se crean tomando el texto en el nombre después de `On<HTTP Verb>` y antes de `Async` (si existe). En el ejemplo anterior, los métodos de página son OnPost **JoinList** Async y OnPost **JoinListUC** Async. Quitando *OnPost* y *Async* , los nombres de controlador son `JoinList` y `JoinListUC`.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Al usar el código anterior, la ruta de dirección URL que envía a `OnPostJoinListAsync` es `https://localhost:5001/Customers/CreateFATH?handler=JoinList`. La ruta de dirección URL que envía a `OnPostJoinListUCAsync` es `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.

## <a name="custom-routes"></a>Rutas personalizadas

Use la directiva `@page` para:

* Especificar una ruta personalizada a una página. Por ejemplo, la ruta a la página Acerca de se puede establecer en `/Some/Other/Path` con `@page "/Some/Other/Path"`.
* Anexar segmentos a la ruta predeterminada de una página. Por ejemplo, se puede agregar un segmento "item" a la ruta predeterminada de una página con `@page "item"`.
* Anexar parámetros a la ruta predeterminada de una página. Por ejemplo, un parámetro de identificador, `id`, puede ser necesario para una página con `@page "{id}"`.

Se admite una ruta de acceso relativa raíz designada por una tilde (`~`) al principio de la ruta de acceso. Por ejemplo, `@page "~/Some/Other/Path"` es lo mismo que `@page "/Some/Other/Path"`.

Si no le gusta la cadena de consulta `?handler=JoinList` en la dirección URL, puede cambiar la ruta para poner el nombre del controlador en la parte de la ruta de la dirección URL. Para personalizar la ruta, se puede agregar una plantilla de ruta entre comillas dobles después de la directiva `@page`.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Al usar el código anterior, la ruta de dirección URL que envía a `OnPostJoinListAsync` es `https://localhost:5001/Customers/CreateFATH/JoinList`. La ruta de dirección URL que envía a `OnPostJoinListUCAsync` es `https://localhost:5001/Customers/CreateFATH/JoinListUC`.

El signo `?` que sigue a `handler` significa que el parámetro de ruta es opcional.

## <a name="advanced-configuration-and-settings"></a>Valores de configuración avanzados

La mayoría de las aplicaciones no requieren la configuración de las siguientes secciones.

Para configurar las opciones avanzadas, use la sobrecarga <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> que configura <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

Use el elemento <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> para establecer el directorio raíz de páginas o agregar convenciones de modelo de aplicación para las páginas. Para obtener más información sobre las convenciones, vea [Convenciones de autorización de Razor Pages](xref:security/authorization/razor-pages-authorization).

Para precompilar vistas, consulte la sección sobre la [compilación de vistas de Razor](xref:mvc/views/view-compilation).

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a>Especificación de Razor Pages en la raíz del contenido

De forma predeterminada, Razor Pages se encuentra en la raíz del directorio */Pages* . Agregue <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> para especificar que sus instancias de Razor Pages se encuentran en la [raíz de contenido](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) de la aplicación:

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a>Especificación de Razor Pages en un directorio raíz personalizado

Agregue <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> para especificar que Razor Pages se encuentra en un directorio raíz personalizado en la aplicación (proporcione una ruta de acceso relativa):

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a>Recursos adicionales

* Consulte [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start), que se basa en esta introducción.
* [Autorización de atributos y Razor Pages](xref:security/authorization/simple#aarp)
* [Descargue o vea el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample).
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Ryan Nowak](https://github.com/rynowak)

RazorPages es una nueva característica de ASP.NET Core MVC que facilita la codificación de escenarios centrados en páginas y hace que sea más productiva.

Si busca un tutorial que use el enfoque Model-View-Controller, consulte [Introducción a ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).

En este documento se proporciona una introducción a Razor Pages. No es un tutorial paso a paso. Si encuentra que alguna sección es demasiado avanzada, consulte [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start). Para obtener información general de ASP.NET Core, vea [Introducción a ASP.NET Core](xref:index).

## <a name="prerequisites"></a>Requisitos previos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a>Crear un proyecto de Razor Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Vea [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) para obtener instrucciones detalladas sobre cómo crear un proyecto Razor Pages.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Ejecute `dotnet new webapp` desde la línea de comandos.

Abra el archivo *.csproj* generado desde Visual Studio para Mac.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ejecute `dotnet new webapp` desde la línea de comandos.

---

## <a name="no-locrazor-pages"></a>Razor Pages

Razor Pages está habilitado en *Startup.cs* :

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

Considere la posibilidad de una página básica: <a name="OnGet"></a>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

El código anterior se parece mucho a un [archivo de vista de Razor](xref:tutorials/first-mvc-app/adding-view) que se utiliza en una aplicación ASP.NET Core con controladores y vistas. La directiva `@page` lo hace diferente. `@page` transforma el archivo en una acción de MVC, lo que significa que administra las solicitudes directamente, sin tener que pasar a través de un controlador. `@page` debe ser la primera directiva de Razor de una página. `@page` afecta al comportamiento de otras construcciones de Razor.

Una página similar, con una clase `PageModel`, se muestra en los dos archivos siguientes. El archivo *Pages/Index2.cshtml* :

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

Modelo de página *Pages/Index2.cshtml.cs* :

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Por convención, el archivo de clase `PageModel` tiene el mismo nombre que el archivo de Razor Pages con *.cs* anexado. Por ejemplo, la instancia de Razor Pages anterior es *Pages/Index2.cshtml* . El archivo que contiene la clase `PageModel` se denomina *Pages/Index2.cshtml.cs* .

Las asociaciones de rutas de dirección URL a páginas se determinan según la ubicación de la página en el sistema de archivos. En la tabla siguiente, se muestra una ruta de acceso Razor Pages y la dirección URL correspondiente:

| Ruta de acceso y nombre de archivo               | URL correspondiente |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` o `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` o `/Store/Index` |

Notas:

* El entorno de ejecución busca archivos de páginas de Razor en la carpeta *Pages* de forma predeterminada.
* `Index` es la página predeterminada cuando una URL no incluye una página.

## <a name="write-a-basic-form"></a>Escritura de un formulario básico

Razor Pages está diseñado para facilitar la implementación de patrones comunes que se usan con exploradores web al compilar una aplicación. Los [enlaces de modelos](xref:mvc/models/model-binding), los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro) y los asistentes de HTML *simplemente funcionan* con las propiedades definidas en una clase de Razor Pages. Considere la posibilidad de una página que implementa un formulario básico del estilo "Póngase en contacto con nosotros" para el modelo `Contact`:

Para los ejemplos de este documento, `DbContext` se inicializa en el archivo [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

El modelo de datos:

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

El contexto de la base de datos:

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

El archivo de vista *Pages/Create.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

Modelo de página *Pages/Create.cshtml.cs* :

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

Por convención, la clase `PageModel` se denomina `<PageName>Model` y se encuentra en el mismo espacio de nombres que la página.

La clase `PageModel` permite la separación de la lógica de una página de su presentación. Define los controladores de página para solicitudes que se envían a la página y los datos que usan para representar la página. Esta separación permite lo siguiente:

* Administración de dependencias de página mediante la [inserción de dependencias](xref:fundamentals/dependency-injection).
* [Pruebas](xref:test/razor-pages-tests) unitarias de las páginas.

La página tiene un *método de controlador* `OnPostAsync`, que se ejecuta en solicitudes `POST` (cuando un usuario envía el formulario). Puede agregar métodos de controlador para cualquier verbo HTTP. Los controladores más comunes son:

* `OnGet` para inicializar el estado necesario para la página. Ejemplo [OnGet](#OnGet).
* `OnPost` para controlar los envíos del formulario.

El sufijo de nombre `Async` es opcional, pero se usa a menudo por convención para funciones asincrónicas. El código anterior es típico de Razor Pages.

Si está familiarizado con las aplicaciones de ASP.NET con controladores y vistas:

* El código `OnPostAsync` del ejemplo anterior es similar al típico código de controlador.
* La mayoría de los primitivos MVC como el [enlace de modelos](xref:mvc/models/model-binding), la [validación](xref:mvc/models/validation), la [validación](xref:mvc/models/validation) y los resultados de acciones se comparten.

El método `OnPostAsync` anterior:

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

El flujo básico de `OnPostAsync`:

Compruebe los errores de validación.

* Si no hay ningún error, guarde los datos y redirija.
* Si hay errores, muestre la página de nuevo con mensajes de validación. La validación del lado cliente es idéntica a las aplicaciones de ASP.NET Core MVC tradicionales. En muchos casos, los errores de validación se detectan en el cliente y nunca se envían al servidor.

Cuando los datos se escriben correctamente, el método del controlador `OnPostAsync` llama al método del asistente `RedirectToPage` para devolver una instancia de `RedirectToPageResult`. `RedirectToPage` es un resultado de acción nueva, similar a `RedirectToAction` o `RedirectToRoute`, pero personalizada para las páginas. En el ejemplo anterior, redirige a la página de índice raíz (`/Index`). `RedirectToPage` se detalla en la sección [Generación de direcciones URL para las páginas](#url_gen).

Cuando el formulario enviado tiene errores de validación (que se pasan al servidor), el método del controlador `OnPostAsync` llama al método del asistente `Page`. `Page` devuelve una instancia de `PageResult`. Devolver `Page` es similar a cómo las acciones en los controladores devuelven `View`. `PageResult` es el tipo de valor devuelto predeterminado para un método de controlador. Un método de controlador que devuelve `void` representa la página.

La propiedad `Customer` usa el atributo `[BindProperty]` para participar en el enlace de modelos.

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

De forma predeterminada, Razor Pages enlaza propiedades solo con verbos que no sean `GET`. Enlazar a propiedades puede reducir la cantidad de código que se debe escribir. Enlazar reduce el código al usar la misma propiedad para representar los campos de formulario (`<input asp-for="Customer.Name">`) y aceptar la entrada.

[!INCLUDE[](~/includes/bind-get.md)]

La página principal ( *Index.cshtml* ):

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

La clase `PageModel` asociada ( *Index.cshtml.cs* ):

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

El archivo *Index.cshtml* contiene el siguiente marcado para crear un vínculo de edición para cada contacto:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

El `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [asistente de etiquetas delimitadoras](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ha usado el atributo `asp-route-{value}` para generar un vínculo a la página de edición. El vínculo contiene datos de ruta con el identificador del contacto. Por ejemplo: `https://localhost:5001/Edit/1`. Las [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) permiten que el código de servidor participe en la creación y la representación de elementos HTML en archivos de Razor. Los asistentes de etiquetas se habilitan mediante `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

El archivo *Pages/Edit.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

La primera línea contiene la directiva `@page "{id:int}"`. La restricción de enrutamiento `"{id:int}"` indica a la página que acepte las solicitudes a la página que contienen datos de ruta `int`. Si una solicitud a la página no contiene datos de ruta que se puedan convertir en `int`, el tiempo de ejecución devuelve un error HTTP 404 (no encontrado). Para que el identificador sea opcional, anexe `?` a la restricción de ruta:

 ```cshtml
@page "{id:int?}"
```

El archivo *Pages/Edit.cshtml.cs* :

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

El archivo *index.cshtml* también contiene una marca para crear un botón de eliminar para cada contacto de cliente:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

Al representar dicho botón de eliminar en HTML, `formaction` incluye parámetros para:

* Id. de contacto de cliente especificado mediante el atributo `asp-route-id`.
* `handler` especificado mediante el atributo `asp-page-handler`.

Aquí tiene un ejemplo de un botón de eliminar representado con un id. de contacto de cliente de `1`:

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

Al seleccionar el botón, se envía una solicitud de formulario `POST` al servidor. De forma predeterminada, el nombre del método de control se selecciona de acuerdo con el valor del parámetro `handler` y según el esquema `OnPost[handler]Async`.

Como en este ejemplo `handler` es `delete`, el método de control `OnPostDeleteAsync` se usa para procesar la solicitud `POST`. Si `asp-page-handler` se establece en otro valor, como `remove`, se seleccionará un método de controlador llamado `OnPostRemoveAsync`. En el código siguiente se muestra el controlador `OnPostDeleteAsync`:

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

El método `OnPostDeleteAsync` realiza las acciones siguientes:

* Acepta el elemento `id` de la cadena de consulta. Si la directiva de página *Index.cshtml* contuviera la restricción de enrutamiento `"{id:int?}"`, `id` provendría de los datos de la ruta. Los datos de la ruta de `id` se especifican en el URI. Por ejemplo, `https://localhost:5001/Customers/2`.
* Realiza una consulta a la base de datos del contacto de cliente con `FindAsync`.
* Si encuentra dicho contacto de cliente, se quita de la lista correspondiente. Luego, se actualiza la base de datos.
* Llama a `RedirectToPage` para redirigir la página Index raíz (`/Index`).

## <a name="mark-page-properties-as-required"></a>Marcado de las propiedades de página según sea necesario

Las propiedades de un valor `PageModel` se pueden marcar con el atributo [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute):

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

Para obtener más información, vea [Validación de modelos](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Control de solicitudes HEAD con un controlador OnGet de reserva

Las solicitudes `HEAD` permiten recuperar los encabezados de un recurso específico. A diferencia de las solicitudes `GET`, las solicitudes `HEAD` no devuelven un cuerpo de respuesta.

Normalmente, se crea un controlador `OnHead` al que se llama para las solicitudes `HEAD`: 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

En ASP.NET Core 2.1 o versiones posteriores, Razor Pages recurre a una llamada al controlador `OnGet` si no se define ningún controlador `OnHead`. Este comportamiento se habilita mediante la llamada a [SetCompatibilityVersion](xref:mvc/compatibility-version) en `Startup.ConfigureServices`:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

Las plantillas predeterminadas generan la llamada `SetCompatibilityVersion` en ASP.NET Core 2.1 y 2.2. `SetCompatibilityVersion` define con eficacia la opción de Razor Pages `true` como `AllowMappingHeadRequestsToGetHandler`.

En lugar de aceptar todos los comportamientos con `SetCompatibilityVersion`, puede aceptar explícitamente comportamientos *específicos* . El código siguiente permite que las solicitudes `HEAD` se asignen al controlador `OnGet`:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a>XSRF/CSRF y Razor Pages

No tiene que escribir ningún código para la [validación antifalsificación](xref:security/anti-request-forgery). La validación y generación de tokens antifalsificación se incluyen automáticamente en Razor Pages.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a>Usar diseños, parciales, plantillas y asistentes de etiquetas con Razor Pages

Las páginas funcionan con todas las características del motor de vista de Razor. Los diseños, parciales, plantillas, asistentes de etiquetas, *_ViewStart.cshtml* , *_ViewImports.cshtml* funcionan de la misma manera que lo hacen con las vistas de Razor convencionales.

Para simplificar esta página, aprovecharemos algunas de esas características.

Agregue una [página de diseño](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

El [diseño](xref:mvc/views/layout):

* Controla el diseño de cada página (a no ser que la página no tenga diseño).
* Importa las estructuras HTML como JavaScript y hojas de estilos.

Vea [Layout page](xref:mvc/views/layout) (Página de diseño) para obtener más información.

La propiedad [Layout](xref:mvc/views/layout#specifying-a-layout) se establece en *Pages/_ViewStart.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

El diseño está en la carpeta *Pages/Shared* . Las páginas buscan otras vistas (diseños, plantillas, parciales) de forma jerárquica, a partir de la misma carpeta que la página actual. Un diseño en la carpeta *Pages/Shared* se puede usar desde cualquier página de Razor en la carpeta *Pages* .

El archivo de diseño debería ir en la carpeta *Pages/Shared* .

Le recomendamos que **no** coloque el archivo de diseño en la carpeta *Views/Shared* . *Views/Shared* es un patrón de vistas de MVC. Razor Pages está diseñado para basarse en la jerarquía de carpetas, no en las convenciones de ruta de acceso.

La búsqueda de vistas de instancia de Razor Pages incluye la carpeta *Pages* . Los diseños, plantillas y parciales que usa con los controladores de MVC y las vistas de Razor convencionales *simplemente funcionan* .

Agregue un archivo *Pages/_ViewImports.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace` se explica más adelante en el tutorial. La directiva `@addTagHelper` pone los [asistentes de etiquetas integradas](xref:mvc/views/tag-helpers/builtin-th/Index) en todas las páginas de la carpeta *Pages* .

<a name="namespace"></a>

Cuando la directiva `@namespace` se usa explícitamente en una página:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

La directiva establece el espacio de nombres de la página. La directiva `@model` no necesita incluir el espacio de nombres.

Cuando la directiva `@namespace` se encuentra en *_ViewImports.cshtml* , el espacio de nombres especificado proporciona el prefijo del espacio de nombres generado en la página que importa la directiva `@namespace`. El resto del espacio de nombres generado (la parte del sufijo) es la ruta de acceso relativa separada por puntos entre la carpeta que contiene *_ViewImports.cshtml* y la carpeta que contiene la página.

Por ejemplo, la clase `PageModel`*Pages/Customers/Edit.cshtml.cs* establece explícitamente el espacio de nombres:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

El archivo *Pages/_ViewImports.cshtml* establece el espacio de nombres siguiente:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

El espacio de nombres generado para la página de Razor *Pages/Customers/Edit.cshtml* es el mismo que la clase `PageModel`.

`@namespace` *también funciona con vistas de Razor convencionales.*

El archivo de vista *Pages/Create.cshtml* original:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

El archivo de vista *Pages/Create.cshtml* actualizado:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

El [proyecto de inicio de Razor Pages](#rpvs17) contiene *Pages/_ValidationScriptsPartial.cshtml* , que enlaza la validación del lado cliente.

Para más información sobre las vistas parciales, vea <xref:mvc/views/partial>.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Generación de direcciones URL para las páginas

La página `Create`, mostrada anteriormente, usa `RedirectToPage`:

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

La aplicación tiene la siguiente estructura de archivos o carpetas:

* */Pages*

  * *Index.cshtml*
  * */Customers*

    * *Create.cshtml*
    * *Edit.cshtml*
    * *Index.cshtml*

Las páginas *Pages/Customers/Create.cshtml* y *Pages/Customers/Edit.cshtml* redirigen a *Pages/Index.cshtml* si se realiza correctamente. La cadena `/Index` forma parte del URI para tener acceso a la página anterior. La cadena `/Index` puede usarse para generar los URI para la página *Pages/Index.cshtml* . Por ejemplo:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

El nombre de página es la ruta de acceso a la página de la carpeta raíz */Pages* , incluido un `/` inicial, por ejemplo `/Index`. Los ejemplos anteriores de generación de URL ofrecen opciones mejoradas y capacidades funcionales en comparación con la escritura a mano de estas. La generación de direcciones URL usa el [enrutamiento](xref:mvc/controllers/routing) y puede generar y codificar parámetros según cómo se defina la ruta en la ruta de acceso de destino.

La generación de direcciones URL para las páginas admite nombres relativos. En la siguiente tabla, se muestra qué página de índice está seleccionada con diferentes parámetros `RedirectToPage` de *Pages/Customers/Create.cshtml* :

| RedirectToPage(x)| Página |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Pages/Index* |
| RedirectToPage("./Index"); | *Pages/Customers/Index* |
| RedirectToPage("../Index") | *Pages/Index* |
| RedirectToPage("Index")  | *Pages/Customers/Index* |

`RedirectToPage("Index")`, `RedirectToPage("./Index")` y `RedirectToPage("../Index")` son *nombres relativos* . El parámetro `RedirectToPage` se *combina* con la ruta de acceso de la página actual para calcular el nombre de la página de destino.  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

Vincular el nombre relativo es útil al crear sitios con una estructura compleja. Si usa nombres relativos para vincular entre páginas en una carpeta, puede cambiar el nombre de esa carpeta. Todos los vínculos seguirán funcionando (porque no incluyen el nombre de carpeta).

Para redirigir a una página en otra [área](xref:mvc/controllers/areas), especifique el área:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Para obtener más información, vea <xref:mvc/controllers/areas>.

## <a name="viewdata-attribute"></a>Atributo ViewData

Se pueden pasar datos a una página con [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute). Los valores de las propiedades de los controladores o los modelos de Razor Pages con el atributo `[ViewData]` se almacenan y cargan desde [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).

En el ejemplo siguiente, el valor `AboutModel` contiene una propiedad `Title` marcada con `[ViewData]`. La propiedad `Title` se establece en el título de la página Acerca de:

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

En la página Acerca de, acceda a la propiedad `Title` como propiedad de modelo:

```cshtml
<h1>@Model.Title</h1>
```

En el diseño, el título se lee desde el diccionario ViewData:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a>TempData

ASP.NET Core expone la propiedad [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) en un [controlador](/dotnet/api/microsoft.aspnetcore.mvc.controller). Esta propiedad almacena datos hasta que se leen. Los métodos `Keep` y `Peek` se pueden usar para examinar los datos sin que se eliminen. `TempData` es útil para el redireccionamiento cuando se necesitan los datos de más de una única solicitud.

El siguiente código establece el valor de `Message` mediante `TempData`:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

El siguiente marcado en el archivo *Pages/Customers/Index.cshtml* muestra el valor de `Message` mediante `TempData`.

```cshtml
<h3>Msg: @Model.Message</h3>
```

El modelo de página *Pages/Customers/Index.cshtml.cs* aplica el atributo `[TempData]` a la propiedad `Message`.

```csharp
[TempData]
public string Message { get; set; }
```

Para obtener más información, vea [TempData](xref:fundamentals/app-state#tempdata).

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Varios controladores por página

En la página siguiente se usa el asistente de etiquetas `asp-page-handler` para generar marcado para dos controladores:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

El formulario del ejemplo anterior tiene dos botones de envío, y cada uno de ellos usa `FormActionTagHelper` para enviar a una dirección URL diferente. El atributo `asp-page-handler` es un complemento de `asp-page`. `asp-page-handler` genera direcciones URL que envían a cada uno de los métodos de controlador definidos por una página. `asp-page` no se especifica porque el ejemplo se vincula a la página actual.

Modelo de página:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

El código anterior usa *métodos de controlador con nombre* . Los métodos de controlador con nombre se crean tomando el texto en el nombre después de `On<HTTP Verb>` y antes de `Async` (si existe). En el ejemplo anterior, los métodos de página son OnPost **JoinList** Async y OnPost **JoinListUC** Async. Quitando *OnPost* y *Async* , los nombres de controlador son `JoinList` y `JoinListUC`.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Al usar el código anterior, la ruta de dirección URL que envía a `OnPostJoinListAsync` es `https://localhost:5001/Customers/CreateFATH?handler=JoinList`. La ruta de dirección URL que envía a `OnPostJoinListUCAsync` es `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.

## <a name="custom-routes"></a>Rutas personalizadas

Use la directiva `@page` para:

* Especificar una ruta personalizada a una página. Por ejemplo, la ruta a la página Acerca de se puede establecer en `/Some/Other/Path` con `@page "/Some/Other/Path"`.
* Anexar segmentos a la ruta predeterminada de una página. Por ejemplo, se puede agregar un segmento "item" a la ruta predeterminada de una página con `@page "item"`.
* Anexar parámetros a la ruta predeterminada de una página. Por ejemplo, un parámetro de identificador, `id`, puede ser necesario para una página con `@page "{id}"`.

Se admite una ruta de acceso relativa raíz designada por una tilde (`~`) al principio de la ruta de acceso. Por ejemplo, `@page "~/Some/Other/Path"` es lo mismo que `@page "/Some/Other/Path"`.

Si no le gusta la cadena de consulta `?handler=JoinList` en la dirección URL, puede cambiar la ruta para poner el nombre del controlador en la parte de la ruta de la dirección URL. Para personalizar la ruta, se puede agregar una plantilla de ruta entre comillas dobles después de la directiva `@page`.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Al usar el código anterior, la ruta de dirección URL que envía a `OnPostJoinListAsync` es `https://localhost:5001/Customers/CreateFATH/JoinList`. La ruta de dirección URL que envía a `OnPostJoinListUCAsync` es `https://localhost:5001/Customers/CreateFATH/JoinListUC`.

El signo `?` que sigue a `handler` significa que el parámetro de ruta es opcional.

## <a name="configuration-and-settings"></a>Valores de configuración

Para configurar opciones avanzadas, use el método de extensión `AddRazorPagesOptions` en el generador de MVC:

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

Actualmente, puede usar `RazorPagesOptions` para establecer el directorio raíz de páginas, o agregar convenciones de modelo de aplicación a las páginas. En el futuro habilitaremos más extensibilidad de este modo.

Para precompilar vistas, consulte la sección sobre la [compilación de vistas de Razor](xref:mvc/views/view-compilation).

[Descargue o vea el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).

Consulte [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start), que se basa en esta introducción.

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a>Especificación de Razor Pages en la raíz del contenido

De forma predeterminada, Razor Pages se encuentra en la raíz del directorio */Pages* . Agregue [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) a [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) para especificar que Razor Pages se encuentra en la [raíz del contenido](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) de la aplicación:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a>Especificación de Razor Pages en un directorio raíz personalizado

Agregue [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) a [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) para especificar que Razor Pages se encuentra en un directorio raíz personalizado de la aplicación (proporcione la ruta de acceso relativa):

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a>Recursos adicionales

* [Autorización de atributos y Razor Pages](xref:security/authorization/simple#aarp)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
