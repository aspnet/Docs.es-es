---
title: Trabajar con el modelo de aplicación en ASP.NET Core
author: ardalis
description: Obtenga información sobre cómo leer y manipular el modelo de aplicación para modificar la forma en que los elementos de MVC se comportan en ASP.NET Core.
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
uid: mvc/controllers/application-model
ms.openlocfilehash: de831a8b5012a2fe61642887ad816466df7a1bcb
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019903"
---
# <a name="work-with-the-application-model-in-aspnet-core"></a>Trabajar con el modelo de aplicación en ASP.NET Core

Por [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC define un *modelo de aplicación* que representa los componentes de una aplicación MVC. Puede leer y manipular este modelo para modificar la manera en que se comportan los elementos de MVC. De forma predeterminada, MVC sigue ciertas convenciones para determinar qué clases se consideran controladores, qué métodos de esas clases son acciones y cómo se comportan los parámetros y el enrutamiento. Puede personalizar este comportamiento para adaptarlo a las necesidades de su aplicación. Para ello, basta con que cree sus propias convenciones y las aplique globalmente o como atributos.

## <a name="models-and-providers"></a>Modelos y proveedores

El modelo de aplicación de ASP.NET Core MVC incluye interfaces abstractas y clases de implementación concretas que describen una aplicación MVC. Este modelo es el resultado de la detección por parte de MVC de los controladores, las acciones, los parámetros de acción, las rutas y los filtros de la aplicación de acuerdo con las convenciones predeterminadas. Cuando trabaje con el modelo de aplicación, puede modificar la aplicación para que siga convenciones diferentes del comportamiento predeterminado de MVC. Los parámetros, nombres, rutas y filtros se usan como datos de configuración para las acciones y los controladores.

El modelo de aplicación de ASP.NET Core MVC tiene la estructura siguiente:

* ApplicationModel
  * Controladores (ControllerModel)
    * Acciones (ActionModel)
      * Parámetros (ParameterModel)

Cada nivel del modelo tiene acceso a una colección `Properties` común, y los niveles inferiores pueden tener acceso a los valores de propiedad establecidos por los niveles superiores de la jerarquía y sobrescribirlos. Las propiedades se conservan en `ActionDescriptor.Properties` cuando se crean las acciones. Después, cuando se controla una solicitud, se puede obtener acceso a través de `ActionContext.ActionDescriptor.Properties` a todas las propiedades que agregue o modifique una convención. El uso de propiedades es una manera excelente de configurar por acción los filtros, los enlazadores de modelos, etc.

> [!NOTE]
> La colección `ActionDescriptor.Properties` no es segura para subprocesos (para escrituras) una vez que el inicio de la aplicación haya finalizado. Las convenciones son la mejor manera de agregar datos de forma segura a esta colección.

### <a name="iapplicationmodelprovider"></a>IApplicationModelProvider

ASP.NET Core MVC carga el modelo de aplicación mediante un patrón de proveedor definido por la interfaz [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider). En esta sección se describen algunos detalles de implementación interna relacionados con el funcionamiento de este proveedor. Se trata de un tema avanzado, ya que la mayoría de las aplicaciones que aprovechan el modelo de aplicación deberían hacerlo mediante convenciones.

Las implementaciones de la interfaz `IApplicationModelProvider` "se encapsulan" entre sí, y cada implementación llama a `OnProvidersExecuting` en orden ascendente en función de su propiedad `Order`. Después, se llama al método `OnProvidersExecuted` en orden inverso. El marco de trabajo define varios proveedores:

Primero, (`Order=-1000`):

* [`DefaultApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.defaultapplicationmodelprovider)

Después, (`Order=-990`):

* [`AuthorizationApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.authorizationapplicationmodelprovider)
* [`CorsApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.cors.internal.corsapplicationmodelprovider)

> [!NOTE]
> El orden en que se llama a dos proveedores con el mismo valor para `Order` no está definido y, por tanto, no se debe confiar en él.

> [!NOTE]
> `IApplicationModelProvider` es un concepto avanzado pensado para que los autores del marco de trabajo lo extiendan. En general, las aplicaciones deben usar convenciones y los marcos de trabajo deben usar proveedores. La diferencia clave es que los proveedores siempre se ejecutan antes que las convenciones.

`DefaultApplicationModelProvider` establece muchos de los comportamientos predeterminados que usa ASP.NET Core MVC. Entre sus responsabilidades se incluyen las siguientes:

* Agregar filtros globales al contexto
* Agregar controladores al contexto
* Agregar métodos de controlador públicos como acciones
* Agregar parámetros de métodos de acción al contexto
* Aplicar la ruta y otros atributos

Algunos comportamientos integrados se implementan mediante `DefaultApplicationModelProvider`. Este proveedor es responsable de construir [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel) , que a su vez hace referencia a las [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel) instancias de, [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel) y [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel) . La clase `DefaultApplicationModelProvider` es un detalle de implementación del marco de trabajo interno que cambiará en el futuro. 

`AuthorizationApplicationModelProvider` se encarga de aplicar el comportamiento asociado a los atributos `AuthorizeFilter` y `AllowAnonymousFilter`. [Más información sobre estos atributos](xref:security/authorization/simple).

`CorsApplicationModelProvider` implementa el comportamiento asociado a `IEnableCorsAttribute`, `IDisableCorsAttribute` y `DisableCorsAuthorizationFilter`. [Más información sobre CORS](xref:security/cors).

## <a name="conventions"></a>Convenciones

El modelo de aplicación define abstracciones de convenciones que proporcionan una forma más sencilla de personalizar el comportamiento de los modelos que invalidan el modelo completo o el proveedor. Se recomienda el uso de estas abstracciones para modificar el comportamiento de la aplicación. Las convenciones ofrecen una manera de escribir código que aplica dinámicamente las personalizaciones. Mientras los [filtros](xref:mvc/controllers/filters) proporcionan una forma de modificar el comportamiento del marco de trabajo, las personalizaciones permiten controlar cómo funciona la aplicación en su conjunto.

Están disponibles las convenciones siguientes:

* [`IApplicationModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelconvention)
* [`IControllerModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.icontrollermodelconvention)
* [`IActionModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iactionmodelconvention)
* [`IParameterModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iparametermodelconvention)

Las convenciones se aplican agregándolas a las opciones de MVC o implementando `Attribute` s y aplicándolos a los controladores, acciones o parámetros de acción (similar a [`Filters`](xref:mvc/controllers/filters) ). A diferencia de los filtros, las convenciones solo se ejecutan cuando se inicia la aplicación, no como parte de cada solicitud.

### <a name="sample-modifying-the-applicationmodel"></a>Ejemplo: modificar ApplicationModel

La siguiente convención se usa para agregar una propiedad al modelo de aplicación. 

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

Las convenciones del modelo de aplicación se aplican como opciones cuando se agrega MVC en `ConfigureServices` en `Startup`.

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

Las propiedades son accesibles desde la colección de propiedades `ActionDescriptor` dentro de las acciones de controlador:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

### <a name="sample-modifying-the-controllermodel-description"></a>Ejemplo: modificar la descripción de ControllerModel

Como en el ejemplo anterior, el modelo de controlador también se puede modificar para incluir propiedades personalizadas. Estos invalidará las propiedades existentes con el mismo nombre especificado en el modelo de aplicación. El atributo de convención siguiente agrega una descripción en el nivel de controlador:

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

Esta convención se aplica como un atributo en un controlador.

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

Se tiene acceso a la propiedad "description" de la misma manera que en ejemplos anteriores.

### <a name="sample-modifying-the-actionmodel-description"></a>Ejemplo: modificar la descripción de ActionModel

Se puede aplicar una convención de atributo independiente a acciones individuales, con lo que se invalida el comportamiento que ya se haya aplicado en el nivel de aplicación o controlador.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

Si se aplica a una acción dentro del controlador del ejemplo anterior se puede ver cómo invalida la convención en el nivel de controlador:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

### <a name="sample-modifying-the-parametermodel"></a>Ejemplo: modificar ParameterModel

La convención siguiente se puede aplicar a parámetros de acción para modificar su `BindingInfo`. La convención siguiente requiere que el parámetro sea un parámetro de ruta. Se ignorarán todos los demás orígenes de enlace posibles (por ejemplo, valores de cadena de consulta).

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

El atributo se puede aplicar a cualquier parámetro de acción:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

### <a name="sample-modifying-the-actionmodel-name"></a>Ejemplo: modificar el nombre de ActionModel

La convención siguiente modifica el valor de `ActionModel` para actualizar el *nombre* de la acción a la que se aplica. El nuevo nombre se proporciona como un parámetro al atributo. El enrutamiento usará este nuevo nombre, lo que afectará a la ruta usada para llegar a este método de acción.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

Este atributo se aplica a un método de acción en `HomeController`:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

Aunque el nombre del método es `SomeName`, el atributo invalida la convención de MVC de usar el nombre del método y reemplaza el nombre de la acción por `MyCoolAction`. De este modo, la ruta usada para llegar a esta acción es `/Home/MyCoolAction`.

> [!NOTE]
> Este ejemplo básicamente equivale a usar el atributo [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute) integrado.

### <a name="sample-custom-routing-convention"></a>Ejemplo: convención de enrutamiento personalizado

Puede usar `IApplicationModelConvention` para personalizar cómo funciona el enrutamiento. Por ejemplo, la convención siguiente incorporará espacios de nombres de controladores en sus rutas, al reemplazar `.` en el espacio de nombres por `/` en la ruta:

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

La convención se agrega como una opción en Startup.

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> Para agregar convenciones a su [software intermedio](xref:fundamentals/middleware/index), obtenga acceso a `MvcOptions` con `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`.

En este ejemplo se aplica esta convención a las rutas que no usan el enrutamiento de atributos donde el controlador contiene "Namespace" en su nombre. En el controlador siguiente se muestra esta convención:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

## <a name="application-model-usage-in-webapicompatshim"></a>Uso del modelo de aplicación en WebApiCompatShim

ASP.NET Core MVC usa un conjunto diferente de convenciones de ASP.NET Web API 2. Mediante el uso de convenciones personalizadas, puede modificar el comportamiento de una aplicación ASP.NET Core MVC para que sea coherente con el de una aplicación Web API. Microsoft suministra [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) específicamente para este propósito.

> [!NOTE]
> Obtenga más información sobre la [migración desde ASP.NET Web API](xref:migration/webapi).

Para usar las correcciones de compatibilidad (shim) de Web API, debe agregar el paquete al proyecto y, después, agregar las convenciones a MVC mediante una llamada a `AddWebApiConventions` en `Startup`:

```csharp
services.AddMvc().AddWebApiConventions();
```

Las convenciones proporcionadas por las correcciones de compatibilidad solo se aplican a las partes de la aplicación a las que se han aplicado ciertos atributos. Los cuatro atributos siguientes se usan para controlar en qué controladores se deben modificar las convenciones mediante las convenciones de las correcciones de compatibilidad:

* [UseWebApiActionConventionsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiactionconventionsattribute)
* [UseWebApiOverloadingAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapioverloadingattribute)
* [UseWebApiParameterConventionsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiparameterconventionsattribute)
* [UseWebApiRoutesAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiroutesattribute)

### <a name="action-conventions"></a>Convenciones de acción

`UseWebApiActionConventionsAttribute` se usa para asignar el método HTTP a las acciones según su nombre (por ejemplo, `Get` se asignaría a `HttpGet`). Solo se aplica a las acciones que no usan el enrutamiento de atributos.

### <a name="overloading"></a>Sobrecarga

`UseWebApiOverloadingAttribute` se usa para aplicar la convención `WebApiOverloadingApplicationModelConvention`. Esta convención agrega `OverloadActionConstraint` al proceso de selección de acciones, que limita las acciones candidatas a aquellas en las que la solicitud cumple todos los parámetros no opcionales.

### <a name="parameter-conventions"></a>Convenciones de parámetro

`UseWebApiParameterConventionsAttribute` se usa para aplicar la convención de acción `WebApiParameterConventionsApplicationModelConvention`. Esta convención especifica que los tipos simples usados como parámetros de acción se enlazan desde el URI de forma predeterminada, mientras que los tipos complejos se enlazan desde el cuerpo de la solicitud.

### <a name="routes"></a>Rutas

`UseWebApiRoutesAttribute` controla si se ha aplicado la convención de controlador `WebApiApplicationModelConvention`. Cuando se habilita, esta convención se usa para agregar a la ruta compatibilidad con [áreas](xref:mvc/controllers/areas).

Además de un conjunto de convenciones, el paquete de compatibilidad incluye una clase base `System.Web.Http.ApiController` que reemplaza la que proporciona Web API. Esto permite que los controladores escritos para Web API y que heredan de `ApiController` funcionen de conformidad con su diseño, mientras se ejecutan en ASP.NET Core MVC. Todos los atributos `UseWebApi*` enumerados anteriormente se aplican a la clase de controlador base. `ApiController` expone propiedades, métodos y tipos de resultados compatibles con los que se encuentran en Web API.

## <a name="using-apiexplorer-to-document-your-app"></a>Uso de ApiExplorer para documentar la aplicación

El modelo de aplicación expone una [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) propiedad en cada nivel que se puede usar para recorrer la estructura de la aplicación. Esto se puede usar para [generar páginas de ayuda para las Web API mediante el uso de herramientas como Swagger](xref:tutorials/web-api-help-pages-using-swagger). La propiedad `ApiExplorer` expone una propiedad `IsVisible` que se puede establecer para especificar qué partes del modelo de la aplicación deben exponerse. Puede configurar esta opción mediante una convención:

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

Mediante el uso de este enfoque (y de convenciones adicionales si es necesario), puede habilitar o deshabilitar las visibilidad de API en cualquier nivel dentro de la aplicación. 
