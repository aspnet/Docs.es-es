---
title: Información general de ASP.NET Core MVC
author: ardalis
description: Conozca ASP.NET Core MVC, un marco completo para crear aplicaciones web y varias API mediante el patrón de diseño del controlador de vista de modelos.
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
uid: mvc/overview
ms.openlocfilehash: 4f0e98057c03769a672ef350cc3ec724a4f7e721
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431074"
---
# <a name="overview-of-aspnet-core-mvc"></a>Información general de ASP.NET Core MVC

Por [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC es un completo marco de trabajo para compilar aplicaciones web y API mediante el patrón de diseño Modelo-Vista-Controlador.

## <a name="what-is-the-mvc-pattern"></a>¿Qué es el patrón de MVC?

El patrón de arquitectura del controlador de vista de modelos (MVC) separa una aplicación en tres grupos de componentes principales: modelos, vistas y controladores. Este patrón permite lograr la [separación de intereses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns). Con este patrón, las solicitudes del usuario se enrutan a un controlador que se encarga de trabajar con el modelo para realizar las acciones del usuario o recuperar los resultados de consultas. El controlador elige la vista para mostrar al usuario y proporciona cualquier dato de modelo que sea necesario.

En el siguiente diagrama se muestran los tres componentes principales y cuál hace referencia a los demás:

![Patrón de MVC](overview/_static/mvc.png)

Con esta delineación de responsabilidades es más sencillo escalar la aplicación, porque resulta más fácil codificar, depurar y probar algo (modelo, vista o controlador) que tenga un solo trabajo. Es más difícil actualizar, probar y depurar código que tenga dependencias repartidas entre dos o más de estas tres áreas. Por ejemplo, la lógica de la interfaz de usuario tiende a cambiar con mayor frecuencia que la lógica de negocios. Si el código de presentación y la lógica de negocios se combinan en un solo objeto, un objeto que contenga lógica de negocios deberá modificarse cada vez que cambie la interfaz de usuario. A menudo esto genera errores y es necesario volver a probar la lógica de negocio después de cada cambio mínimo en la interfaz de usuario.

> [!NOTE]
> La vista y el controlador dependen del modelo. Sin embargo, el modelo no depende de la vista ni del controlador.  Esta es una de las ventajas principales de la separación. Esta separación permite compilar y probar el modelo con independencia de la presentación visual.

### <a name="model-responsibilities"></a>Responsabilidades del modelo

El modelo en una aplicación de MVC representa el estado de la aplicación y cualquier lógica de negocios u operaciones que esta deba realizar. La lógica de negocios debe encapsularse en el modelo, junto con cualquier lógica de implementación para conservar el estado de la aplicación. Las vistas fuertemente tipadas normalmente usan tipos ViewModel diseñados para que contengan los datos para mostrar en esa vista. El controlador crea y rellena estas instancias de ViewModel desde el modelo.

### <a name="view-responsibilities"></a>Responsabilidades de las vistas

Las vistas se encargan de presentar el contenido a través de la interfaz de usuario. Usan el [ Razor motor de vistas](#razor-view-engine) para insertar código .net en el marcado HTML. Debería haber la mínima lógica entre las vistas y cualquier lógica en ellas debe estar relacionada con la presentación de contenido. Si ve que necesita realizar una gran cantidad de lógica en los archivos de vistas para mostrar datos de un modelo complejo, considere la opción de usar un [componente de vista](views/view-components.md), ViewModel, o una plantilla de vista para simplificar la vista.

### <a name="controller-responsibilities"></a>Responsabilidades del controlador

Los controladores son los componentes que controlan la interacción del usuario, trabajan con el modelo y, en última instancia, seleccionan una vista para representarla. En una aplicación de MVC, la vista solo muestra información; el controlador controla y responde a la interacción y los datos que introducen los usuarios. En el patrón de MVC, el controlador es el punto de entrada inicial que se encarga de seleccionar con qué tipos de modelo trabajar y qué vistas representar (de ahí su nombre, ya que controla el modo en que la aplicación responde a una determinada solicitud).

> [!NOTE]
> Los controladores no deberían complicarse con demasiadas responsabilidades. Para evitar que la lógica del controlador se vuelva demasiado compleja, saque la lógica de negocios fuera el controlador y llévela al modelo de dominio.

>[!TIP]
> Si piensa que el controlador realiza con frecuencia los mismos tipos de acciones, mueva estas acciones comunes en [filtros](#filters).

## <a name="what-is-aspnet-core-mvc"></a>Qué es ASP.NET Core MVC

El marco de ASP.NET Core MVC es un marco de presentación ligero, de código abierto y con gran capacidad de prueba, que está optimizado para usarlo con ASP.NET Core.

ASP.NET Core MVC ofrece una manera basada en patrones de crear sitios web dinámicos que permitan una clara separación de intereses. Proporciona control total sobre el marcado, admite el desarrollo controlado por pruebas (TDD) y usa los estándares web más recientes.

## <a name="features"></a>Características

ASP.NET Core MVC incluye lo siguiente:

* [Enrutamiento](#routing)
* [Enlace de modelos](#model-binding)
* [Validación de modelos](#model-validation)
* [Inserción de dependencias](../fundamentals/dependency-injection.md)
* [Filtros](#filters)
* [Áreas](#areas)
* [API web](#web-apis)
* [Capacidad de prueba](#testability)
* [Razor motor de vista](#razor-view-engine)
* [Vistas fuertemente tipadas](#strongly-typed-views)
* [Asistentes de etiquetas](#tag-helpers)
* [Ver componentes](#view-components)

### <a name="routing"></a>Enrutamiento

ASP.NET Core MVC se basa en el [enrutamiento de ASP.NET Core](../fundamentals/routing.md), un eficaz componente de asignación de URL que permite compilar aplicaciones que tengan direcciones URL comprensibles y que admitan búsquedas. Esto permite definir patrones de nomenclatura de URL de la aplicación que funcionen bien para la optimización del motor de búsqueda (SEO) y para la generación de vínculos, sin tener en cuenta cómo se organizan los archivos en el servidor web. Puede definir las rutas mediante una sintaxis de plantilla de ruta adecuada que admita las restricciones de valores de ruta, los valores predeterminados y los valores opcionales.

El *enrutamiento basado en la convención* permite definir globalmente los formatos de URL que acepta la aplicación y cómo cada uno de esos formatos se asigna a un método de acción específico en un determinado controlador. Cuando se recibe una solicitud entrante, el motor de enrutamiento analiza la URL y la hace coincidir con uno de los formatos de URL definidos. Después, llama al método de acción del controlador asociado.

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

El *enrutamiento de atributos* permite especificar información de enrutamiento mediante la asignación de atributos a los controladores y las acciones, que permiten definir las rutas de la aplicación. Esto significa que las definiciones de ruta se colocan junto al controlador y la acción con la que están asociados.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
    [HttpGet("{id}")]
    public IActionResult GetProduct(int id)
    {
      ...
    }
}
```

### <a name="model-binding"></a>Enlace de modelos

El [enlace de modelo](models/model-binding.md) de ASP.NET Core MVC convierte los datos de solicitud del cliente (valores de formulario, datos de ruta, parámetros de cadena de consulta, encabezados HTTP) en objetos que el controlador puede controlar. Como resultado, la lógica del controlador no tiene que realizar el trabajo de pensar en los datos de la solicitud entrante; simplemente tiene los datos como parámetros para los métodos de acción.

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
```

### <a name="model-validation"></a>Validación de modelos

ASP.NET Core MVC admite la [validación](models/validation.md) decorando el objeto de modelo con los atributos de validación de anotación de datos. Los atributos de validación se comprueban en el lado cliente antes de que los valores se registren en el servidor, y también en el servidor antes de llamar a la acción del controlador.

```csharp
using System.ComponentModel.DataAnnotations;
public class LoginViewModel
{
    [Required]
    [EmailAddress]
    public string Email { get; set; }

    [Required]
    [DataType(DataType.Password)]
    public string Password { get; set; }

    [Display(Name = "Remember me?")]
    public bool RememberMe { get; set; }
}
```

Una acción del controlador:

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null)
{
    if (ModelState.IsValid)
    {
      // work with the model
    }
    // At this point, something failed, redisplay form
    return View(model);
}
```

El marco administra los datos de la solicitud de validación en el cliente y en el servidor. La lógica de validación especificada en tipos de modelo se agrega a las vistas representadas como anotaciones discretas y se aplica en el explorador con [Validación de jQuery](https://jqueryvalidation.org/).

### <a name="dependency-injection"></a>Inserción de dependencias

ASP.NET Core tiene compatibilidad integrada con la [inserción de dependencias](../fundamentals/dependency-injection.md). En ASP.NET Core MVC, los [controladores](controllers/dependency-injection.md) pueden solicitar los servicios que necesiten a través de sus constructores, lo que les permite seguir el [principio de dependencias explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).

La aplicación también puede usar la [inserción de dependencias en archivos de vistas](views/dependency-injection.md), usando la directiva `@inject`:

```cshtml
@inject SomeService ServiceName

<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ServiceName.GetTitle</title>
</head>
<body>
    <h1>@ServiceName.GetTitle</h1>
</body>
</html>
```

### <a name="filters"></a>Filtros

Los [filtros](controllers/filters.md) ayudan a los desarrolladores a encapsular ciertas preocupaciones transversales, como el control de excepciones o la autorización. Los filtros habilitan la ejecución de lógica de procesamiento previo y posterior para métodos de acción y pueden configurarse para ejecutarse en ciertos puntos dentro de la canalización de ejecución para una solicitud determinada. Los filtros pueden aplicarse a controladores o acciones como atributos (o pueden ejecutarse globalmente). En el marco se incluyen varios filtros (como `Authorize`). `[Authorize]` es el atributo que se usa para crear filtros de autorización MVC.

```csharp
[Authorize]
public class AccountController : Controller
```

### <a name="areas"></a>Áreas

Las [áreas](controllers/areas.md) proporcionan una manera de crear particiones de una aplicación web MVC de gran ASP.net Core en agrupaciones funcionales más pequeñas. Un área es una estructura de MVC dentro de una aplicación. En un proyecto de MVC, los componentes lógicos como el modelo, el controlador y la vista se guardan en carpetas diferentes, y MVC usa las convenciones de nomenclatura para crear la relación entre estos componentes. Para una aplicación grande, puede ser conveniente dividir la aplicación en distintas áreas de funciones de alto nivel. Por ejemplo, una aplicación de comercio electrónico con varias unidades de negocio, como la retirada, la facturación y la búsqueda, etc. Cada una de estas unidades tiene sus propias vistas de componentes lógicas, controladores y modelos.

### <a name="web-apis"></a>API web

Además de ser una plataforma excelente para crear sitios web, ASP.NET Core MVC es muy compatible con la creación de las API web. Puede crear servicios que lleguen con facilidad a una amplia gama de clientes, incluidos exploradores y dispositivos móviles.

El marco incluye compatibilidad con la negociación de contenido HTTP y compatibilidad integrada para [aplicar formato a datos](xref:web-api/advanced/formatting) como JSON o XML. Escriba [formateadores personalizados](xref:web-api/advanced/custom-formatters) para agregar compatibilidad con sus propios formatos.

Use la generación de vínculos para habilitar la compatibilidad con hipermedios. Habilite fácilmente la compatibilidad con el [uso compartido de recursos entre orígenes (CORS)](https://www.w3.org/TR/cors/) para que las API web se pueden compartir entre varias aplicaciones web.

### <a name="testability"></a>Capacidad de prueba

Al usar interfaces e inserción de dependencias, el marco es adecuado para realizar pruebas unitarias. También incluye características (por ejemplo, un proveedor TestHost e InMemory para Entity Framework) con las que resulta muy fácil y rápido realizar [pruebas de integración](xref:test/integration-tests). Obtenga más información sobre [cómo probar la lógica del controlador](controllers/testing.md).

### <a name="no-locrazor-view-engine"></a>Razor motor de vista

[ASP.net Core vistas de MVC](views/overview.md) usan el [ Razor motor de vistas](views/razor.md) para representar las vistas. Razor es un lenguaje de marcado de plantilla compacto, expresivo y fluido para definir vistas mediante código C# incrustado. Razor se utiliza para generar dinámicamente contenido web en el servidor. Permite combinar de manera limpia el código del servidor con el código y el contenido del lado cliente.

```cshtml
<ul>
    @for (int i = 0; i < 5; i++) {
        <li>List item @i</li>
    }
</ul>
```

Con el Razor motor de vistas puede definir [diseños](views/layout.md), [vistas parciales](views/partial.md) y secciones reemplazables.

### <a name="strongly-typed-views"></a>Vistas fuertemente tipadas

Razor las vistas de MVC se pueden fuertemente tipadas en función del modelo. Los controladores pueden pasar un modelo fuertemente tipado a las vistas para que estas admitan IntelliSense y la comprobación de tipos.

Por ejemplo, en esta vista se representa un modelo de tipo `IEnumerable<Product>`:

```cshtml
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a>Asistentes de etiquetas

Las [aplicaciones auxiliares de etiquetas](views/tag-helpers/intro.md) permiten que el código del lado servidor participe en la creación y representación de elementos HTML en Razor archivos. Puede usar asistentes de etiquetas para definir etiquetas personalizadas (por ejemplo, `<environment>`) o para modificar el comportamiento de etiquetas existentes (por ejemplo, `<label>`). Los asistentes de etiquetas enlazan con elementos específicos, en función del nombre del elemento y sus atributos. Proporcionan las ventajas de la representación del lado servidor, al tiempo que se mantiene una experiencia de edición HTML.

Hay muchos asistentes de etiquetas integradas para tareas comunes (como la creación de formularios, vínculos, carga de activos, etc.) y existen muchos más a disposición en repositorios públicos de GitHub y como paquetes NuGet. Los asistentes de etiquetas se crean en C# y tienen como destino elementos HTML en función del nombre de elemento, el nombre de atributo o la etiqueta principal. Por ejemplo, la aplicación auxiliar de etiquetas integrada LinkTagHelper puede usarse para crear un vínculo a la acción `Login` de `AccountsController`:

```cshtml
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

La aplicación auxiliar de etiquetas `EnvironmentTagHelper` puede usarse para incluir distintos scripts en las vistas (por ejemplo, sin formato o reducida) según el entorno en tiempo de ejecución, como desarrollo, ensayo o producción:

```cshtml
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.1.4.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery">
    </script>
</environment>
```

Las aplicaciones auxiliares de etiquetas proporcionan una experiencia de desarrollo compatible con HTML y un completo entorno de IntelliSense para crear HTML y Razor marcado. La mayoría de los asistentes de etiquetas integrados tienen como destino elementos HTML existentes y proporcionan atributos del lado servidor para el elemento.

### <a name="view-components"></a>Componentes de vista

Los [componentes de vista](views/view-components.md) permiten empaquetar la lógica de representación y reutilizarla en toda la aplicación. Son similares a las [vistas parciales](views/partial.md), pero con lógica asociada.

## <a name="compatibility-version"></a>Versión de compatibilidad

El método <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> permite a una aplicación participar o no en los cambios de comportamiento importantes incorporados en ASP.NET Core MVC 2.1 o una versión posterior.

Para obtener más información, vea <xref:mvc/compatibility-version>.

## <a name="additional-resources"></a>Recursos adicionales

* [MyTest. AspNetCore. Mvc: biblioteca de pruebas fluidas para ASP.net Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): biblioteca de pruebas unitarias fuertemente tipadas, que proporciona una interfaz fluida para probar MVC y aplicaciones de API Web. ( *Microsoft no realiza su mantenimiento ni su soporte técnico.* )
* <xref:blazor/components/prerendering-and-integration>
