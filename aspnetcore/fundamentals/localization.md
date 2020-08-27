---
title: Globalización y localización en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre la manera en que ASP.NET Core proporciona servicios y software intermedio para la localización de contenido en diferentes idiomas y referencias culturales.
ms.author: riande
ms.date: 11/30/2019
no-loc:
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
uid: fundamentals/localization
ms.openlocfilehash: d5be01c67f455a2706a654574ef9f5e273551e70
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865443"
---
# <a name="globalization-and-localization-in-aspnet-core"></a>Globalización y localización en ASP.NET Core

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) y [Hisham Bin Ateya](https://twitter.com/hishambinateya)

Con un sitio web multilingüe, se puede llegar a un público más amplio. ASP.NET Core proporciona servicios y software intermedio para la localización en diferentes idiomas y referencias culturales.

La internacionalización conlleva [globalización](/dotnet/api/system.globalization) y [localización](/dotnet/standard/globalization-localization/localization). La globalización es el proceso de diseñar aplicaciones que admiten diferentes referencias culturales. La globalización agrega compatibilidad con la entrada, la visualización y la salida de un conjunto definido de scripts de lenguaje relacionados con áreas geográficas específicas.

La localización es el proceso de adaptar una aplicación globalizada, que ya se ha procesado para la localizabilidad, a una determinada referencia cultural o configuración regional. Para más información, vea **Términos relacionados con la globalización y la localización** al final de este documento.

La localización de la aplicación implica lo siguiente:

1. Hacer que el contenido de la aplicación sea localizable
1. Proporcionar recursos localizados para los idiomas y las referencias culturales admitidos
1. Implementar una estrategia para seleccionar el idioma o la referencia cultural de cada solicitud

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/Localization) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>Hacer que el contenido de la aplicación sea localizable

<xref:Microsoft.Extensions.Localization.IStringLocalizer> y <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> se diseñaron para mejorar la productividad al desarrollar aplicaciones localizadas. `IStringLocalizer` usa <xref:System.Resources.ResourceManager> y <xref:System.Resources.ResourceReader> para proporcionar recursos específicos de la referencia cultural en tiempo de ejecución. La interfaz tiene un indizador y un `IEnumerable` para devolver las cadenas localizadas. `IStringLocalizer` no necesita que se almacenen las cadenas de idioma predeterminado en un archivo de recursos. Puede desarrollar una aplicación destinada a la localización sin necesidad de crear archivos de recursos al principio de la fase de desarrollo. En el código siguiente se muestra cómo ajustar la cadena "About Title" para la localización.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

En el código anterior, la implementación de `IStringLocalizer<T>` procede de la [inserción de dependencias](dependency-injection.md). Si no se encuentra el valor localizado de "About Title, se devuelve la clave de indizador, es decir, la cadena "About Title". Puede dejar las cadenas literales del idioma predeterminado en la aplicación y ajustarlas en el localizador, de modo que se pueda centrar en el desarrollo de la aplicación. Desarrolle la aplicación con el idioma predeterminado y prepárela para el proceso de localización sin necesidad de crear primero un archivo de recursos predeterminado. También puede seguir el método tradicional y proporcionar una clave para recuperar la cadena de idioma predeterminado. El nuevo flujo de trabajo, que carece de archivo *.resx* de idioma predeterminado y simplemente ajusta los literales de cadena, puede ahorrar a muchos desarrolladores la sobrecarga de localizar una aplicación. Otros desarrolladores preferirán el flujo de trabajo tradicional, ya que facilita el trabajo con literales de cadena más largos, así como la actualización de las cadenas localizadas.

Use la implementación de `IHtmlLocalizer<T>` para los recursos que contienen HTML. El HTML de `IHtmlLocalizer` codifica los argumentos a los que se da formato en la cadena de recursos, pero no codifica como HTML la cadena de recursos en sí misma. En el ejemplo que se muestra a continuación, solo está codificado en HTML el valor del parámetro `name`.

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> Por lo general, solo se localiza texto, no HTML.

En el nivel más bajo, puede obtener `IStringLocalizerFactory` de la [inserción de dependencias](dependency-injection.md):

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

En el código anterior se muestran los dos métodos factory.Create.

Puede dividir las cadenas localizadas por controlador o por área, o bien tener un solo contenedor. En la aplicación de ejemplo, se usa una clase ficticia denominada `SharedResource` para los recursos compartidos.

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

Algunos programadores usan la clase `Startup` para contener cadenas globales o compartidas. En el ejemplo siguiente, se usan los localizadores `InfoController` y `SharedResource`:

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Localización de vista

El servicio `IViewLocalizer` proporciona cadenas localizadas para una [vista](xref:mvc/views/overview). La clase `ViewLocalizer` implementa esta interfaz y busca la ubicación del recurso en la ruta de acceso del archivo de vista. En el código siguiente se muestra cómo usar la implementación predeterminada de `IViewLocalizer`:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

La implementación predeterminada de `IViewLocalizer` busca el archivo de recursos según el nombre del archivo de vista. No se puede usar un archivo de recursos compartidos global. `ViewLocalizer` implementa el localizador mediante `IHtmlLocalizer`, por lo que Razor no codifica como HTML la cadena localizada. Puede parametrizar las cadenas de recursos y `IViewLocalizer` codificará como HTML los parámetros, pero no la cadena de recursos. Observe el siguiente marcado Razor:

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Un archivo de recursos en francés podría contener lo siguiente:

| Key | Valor |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

La vista representada contendría el marcado HTML del archivo de recursos.

> [!NOTE]
> Por lo general, solo se localiza texto, no HTML.

Para usar un archivo de recursos compartido en una vista, inserte `IHtmlLocalizer<T>`:

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Localización de DataAnnotations

Los mensajes de error de DataAnnotations se localizan con `IStringLocalizer<T>`. Mediante la opción `ResourcesPath = "Resources"`, es posible almacenar los mensajes de error en `RegisterViewModel` en cualquiera de las rutas de acceso siguientes:

* *Resources/ViewModels.Account.RegisterViewModel.fr.resx*
* *Resources/ViewModels/Account/RegisterViewModel.fr.resx*

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

En ASP.NET Core MVC 1.1.0 y versiones posteriores, los atributos que no son de validación están localizados. ASP.NET Core MVC 1.0 **no** busca cadenas localizadas para los atributos que no son de validación.

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>Uso de una cadena de recursos para varias clases

En el código siguiente se muestra cómo usar una cadena de recursos para atributos de validación con varias clases:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

En el código anterior, `SharedResource` es la clase correspondiente al archivo resx donde se almacenan los mensajes de validación. Según este método, DataAnnotations solo usará `SharedResource`, en lugar del recurso de cada clase.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Proporcionar recursos localizados para los idiomas y las referencias culturales admitidos

### <a name="supportedcultures-and-supporteduicultures"></a>SupportedCultures y SupportedUICultures

ASP.NET Core permite especificar dos valores de referencia cultural, `SupportedCultures` y `SupportedUICultures`. El objeto [CultureInfo](/dotnet/api/system.globalization.cultureinfo) para `SupportedCultures` determina los resultados de funciones dependientes de la referencia cultural, como el formato de fecha, hora, número y moneda. `SupportedCultures` también determina el criterio de ordenación del texto, las convenciones sobre el uso de mayúsculas y minúsculas, y las comparaciones de cadenas. Vea [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) para obtener más información sobre la manera en que el servidor obtiene la referencia cultural. `SupportedUICultures` determina qué cadenas traducidas buscará [ResourceManager](/dotnet/api/system.resources.resourcemanager) (en archivos *.resx*). `ResourceManager` simplemente busca cadenas específicas de referencias culturales determinadas por `CurrentUICulture`. Todos los subprocesos de .NET tienen objetos `CurrentCulture` y `CurrentUICulture`. ASP.NET Core inspecciona estos valores al representar funciones dependientes de la referencia cultural. Por ejemplo, si la referencia cultural del subproceso actual está establecida en "en-US" (inglés (Estados Unidos)), `DateTime.Now.ToLongDateString()` mostrará "Thursday, February 18, 2016". En cambio, si `CurrentCulture` está establecido en "es-ES" (español (España)), la salida será "jueves, 18 de febrero de 2016".

## <a name="resource-files"></a>Archivos de recursos

Un archivo de recursos es un mecanismo útil para separar del código las cadenas localizables. Las cadenas traducidas para el idioma no predeterminado están aisladas en archivos de recursos *.resx*. Pongamos por caso que quiere crear un archivo de recursos de español denominado *Welcome.es.resx* que contenga cadenas traducidas. "es" es el código de idioma para español. Para crear este archivo de recursos en Visual Studio:

1. En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta que contendrá el archivo de recursos > **Agregar** > **Nuevo elemento**.

   ![Menú contextual anidado: en el Explorador de soluciones, se abre un menú contextual para los recursos. Se abre un segundo menú contextual, Agregar, en el que se muestra el comando Nuevo elemento resaltado.](localization/_static/newi.png)

1. En el cuadro para **buscar plantillas instaladas**, escriba "recurso" y asigne un nombre al archivo.

   ![Cuadro de diálogo Agregar nuevo elemento](localization/_static/res.png)

1. Escriba el valor de clave (cadena nativa) en la columna **Nombre** y la cadena traducida en la columna **Valor**.

   ![Archivo Welcome.es.resx (archivo de recursos de bienvenida para el idioma español) con la palabra Hello en la columna Nombre y la palabra Hola en la columna Valor](localization/_static/hola.png)

   El archivo *Welcome.es.resx* aparece en Visual Studio.

   ![Archivo de recursos de bienvenida en español (es) en el Explorador de soluciones](localization/_static/se.png)

## <a name="resource-file-naming"></a>Nomenclatura de los archivos de recursos

El nombre de los recursos es el nombre del tipo completo de su clase menos el nombre del ensamblado. Por ejemplo, un recurso francés de un proyecto cuyo ensamblado principal sea `LocalizationWebsite.Web.dll` para la clase `LocalizationWebsite.Web.Startup` se denominará *Startup.fr.resx*. Un recurso para la clase `LocalizationWebsite.Web.Controllers.HomeController` se denominará *Controllers.HomeController.fr.resx*. Si el espacio de nombres de la clase de destino no es igual que el nombre del ensamblado, necesitará el nombre de tipo completo. Por ejemplo, en el proyecto de ejemplo, un recurso para el tipo `ExtraNamespace.Tools` se denominará *ExtraNamespace.Tools.fr.resx*.

En el proyecto de ejemplo, el método `ConfigureServices` establece `ResourcesPath` en "Resources", por lo que la ruta de acceso relativa del proyecto para el archivo de recursos en francés del controlador de inicio es *Resources/Controllers.HomeController.fr.resx*. También puede usar carpetas para organizar los archivos de recursos. Para el controlador de inicio, la ruta de acceso sería *Resources/Controllers/HomeController.fr.resx*. Si no usa la opción `ResourcesPath`, el archivo *.resx* estará en el directorio base del proyecto. El archivo de recursos para `HomeController` se denominará *Controllers.HomeController.fr.resx*. La opción de usar la convención de nomenclatura de punto o ruta de acceso depende de la manera en que quiera organizar los archivos de recursos.

| Nombre del recurso | Nomenclatura de punto o ruta de acceso |
| ------------   | ------------- |
| Resources/Controllers.HomeController.fr.resx | Punto  |
| Resources/Controllers/HomeController.fr.resx  | Ruta de acceso |

Los archivos de recursos que usan `@inject IViewLocalizer` en las vistas de Razor siguen un patrón similar. Para asignar un nombre al archivo de recursos de una vista, se puede usar la nomenclatura de punto o de ruta de acceso. Los archivos de recursos de una vista de Razor imitan la ruta de acceso de su archivo de vista asociado. Supongamos que establecemos `ResourcesPath` en "Resources". En ese caso, el archivo de recursos de francés asociado a la vista *Views/Home/About.cshtml* podría ser uno de los siguientes:

* Resources/Views/Home/About.fr.resx

* Resources/Views.Home.About.fr.resx

Si no usa la opción `ResourcesPath`, el archivo *.resx* de una vista se ubicará en la misma carpeta que la vista.

### <a name="rootnamespaceattribute"></a>RootNamespaceAttribute 

El atributo [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) proporciona el espacio de nombres raíz de un ensamblado cuando el espacio de nombres raíz del ensamblado es diferente del nombre de ensamblado. 

> [!WARNING]
> Esto puede ocurrir cuando el nombre de un proyecto no es un identificador de .NET válido. Por ejemplo `my-project-name.csproj` usará el espacio de nombres raíz `my_project_name` y el nombre de ensamblado `my-project-name`, lo que lleva a este error. 

Si el espacio de nombres raíz de un ensamblado es diferente del nombre de ensamblado:

* La localización no funciona de forma predeterminada.
* Se produce un error en la localización debido a la manera en que se buscan los recursos dentro del ensamblado. `RootNamespace` es un valor en tiempo de compilación que no está disponible para el proceso en ejecución. 

Si `RootNamespace` es diferente de `AssemblyName`, incluya lo siguiente en *AssemblyInfo.cs* (con los valores de parámetro reemplazados por los valores reales):

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

El código anterior permite la resolución correcta de los archivos resx.

## <a name="culture-fallback-behavior"></a>Comportamiento de reserva de la referencia cultural

Cuando se busca un recurso, la localización entra en un proceso conocido como "reserva de la referencia cultural". Partiendo de la referencia cultural solicitada, si esta no se encuentra, se revierte a su referencia cultural principal correspondiente. Como inciso, decir que la propiedad [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) representa la referencia cultural principal. Eso suele conllevar (aunque no siempre) la eliminación del significante nacional del código ISO. Así, por ejemplo, el dialecto de español hablado en México es "es-MX". Contiene el elemento principal "es", que corresponde a español no específico de ningún país en concreto.

Imagine que su sitio recibe una solicitud sobre un recurso "Welcome" donde se emplea la referencia cultural "fr-CA". El sistema de localización busca los recursos siguientes (en orden) y selecciona la primera coincidencia:

* *Welcome.fr-CA.resx*
* *Welcome.fr.resx*
* *Welcome.resx* (si `NeutralResourcesLanguage` es "fr-CA")

Consideremos, por ejemplo, que quita el designador de referencia cultural ".fr" y la referencia cultural está establecida en francés. En ese caso, se lee el archivo de recursos predeterminado y se localizan las cadenas. El Administrador de recursos designa un recurso predeterminado o de reserva para los casos en que nada coincida con la referencia cultural solicitada. Si quiere simplemente devolver la clave cuando falte un recurso para la referencia cultural solicitada, no debe tener un archivo de recursos predeterminado.

### <a name="generate-resource-files-with-visual-studio"></a>Generar archivos de recursos con Visual Studio

Si crea un archivo de recursos en Visual Studio sin una referencia cultural en el nombre de archivo (por ejemplo, *Welcome.resx*), Visual Studio creará una clase de C# con una propiedad para cada cadena. Normalmente esto no interesa con ASP.NET Core; por lo general, no tendrá un archivo de recursos *.resx* predeterminado (un archivo *.resx* sin el nombre de la referencia cultural). Se recomienda que cree el archivo *.resx* con un nombre de referencia cultural (por ejemplo, *Welcome.fr.resx*). Cuando cree un archivo *.resx* con un nombre de referencia cultural, Visual Studio no generará el archivo de clase.

### <a name="add-other-cultures"></a>Agregar otras referencias culturales

Cada combinación de idioma y referencia cultural (que no sea el idioma predeterminado) requiere un archivo de recursos único. Para crear archivos de recursos para otras referencias culturales y configuraciones regionales, debe crear archivos de recursos en los que los códigos de idioma ISO formen parte del nombre de archivo (por ejemplo, **en-us**, **fr-ca** y  **en-gb**). Estos códigos ISO se colocan entre el nombre de archivo y la extensión de archivo *.resx*, como en *Welcome.es-MX.resx* (español [México]).

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Implementar una estrategia para seleccionar el idioma o la referencia cultural de cada solicitud

### <a name="configure-localization"></a>Configurar la localización

La localización se configura en el método `Startup.ConfigureServices`:

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* `AddLocalization` agrega los servicios de localización al contenedor de servicios. El código anterior también establece la ruta de acceso a los recursos en "Resources".

* `AddViewLocalization` agrega compatibilidad con los archivos de vista localizados. En este ejemplo, la localización de vista se basa en el sufijo del archivo de vista. Por ejemplo, "fr" en el archivo *Index.fr.cshtml*.

* `AddDataAnnotationsLocalization` agrega compatibilidad con mensajes de validación de `DataAnnotations` localizados mediante abstracciones `IStringLocalizer`.

### <a name="localization-middleware"></a>Software intermedio de localización

La referencia cultural actual de una solicitud se establece en el [software intermedio](xref:fundamentals/middleware/index) de localización. El software intermedio de localización se habilita en el método `Startup.Configure`. El software intermedio de localización debe configurarse antes que cualquier software intermedio que pueda comprobar la referencia cultural de la solicitud (por ejemplo, `app.UseMvcWithDefaultRoute()`).

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

`UseRequestLocalization` inicializa un objeto `RequestLocalizationOptions`. En todas las solicitudes, se enumera la lista de `RequestCultureProvider` en `RequestLocalizationOptions` y se usa el primer proveedor que puede determinar correctamente la referencia cultural de la solicitud. Los proveedores predeterminados proceden de la clase `RequestLocalizationOptions`:

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

La lista predeterminada va de más específico a menos específico. Más adelante en el artículo veremos cómo puede cambiar el orden e incluso agregar un proveedor de referencia cultural personalizado. Si ninguno de los proveedores puede determinar la referencia cultural de la solicitud, se usa `DefaultRequestCulture`.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

Algunas aplicaciones usarán una cadena de consulta para establecer <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1>. Para las aplicaciones que usan el método de cookie o de encabezado Accept-Language, es útil agregar una cadena de consulta a la dirección URL para depurar y probar el código. De forma predeterminada, `QueryStringRequestCultureProvider` está registrado como primer proveedor de localización en la lista `RequestCultureProvider`. Debe pasar los parámetros de cadena de consulta `culture` y `ui-culture`. En el ejemplo siguiente, la referencia cultural específica (idioma y región) se establece en español (México):

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

Si solo pasa uno de los dos parámetros (`culture` o `ui-culture`), el proveedor de la cadena de consulta usará el valor que usted ha pasado para establecer ambos valores. Por ejemplo, si solo establece la referencia cultural, se establecerán `Culture` y `UICulture`:

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a>CookieRequestCultureProvider

Las aplicaciones de producción suelen proporcionar un mecanismo para establecer la referencia cultural con la cookie de la referencia cultural de ASP.NET Core. Use el método `MakeCookieValue` para crear una cookie.

`CookieRequestCultureProvider` `DefaultCookieName` devuelve el nombre de cookie predeterminado usado para realizar un seguimiento de la información de la referencia cultural preferida del usuario. El nombre de cookie predeterminado es `.AspNetCore.Culture`.

El formato de la cookie es `c=%LANGCODE%|uic=%LANGCODE%`, donde `c` es `Culture` y `uic` es `UICulture`, por ejemplo:

```
c=en-UK|uic=en-US
```

Si solo especifica uno de los dos valores, ya sea la información de la referencia cultural o la referencia cultural de la interfaz de usuario, la referencia cultural especificada se usará tanto para la información de la referencia cultural como para la referencia cultural de la interfaz de usuario.

### <a name="the-accept-language-http-header"></a>Encabezado HTTP Accept-Language

El [encabezado Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) se puede establecer en la mayoría de los exploradores y está diseñado inicialmente para especificar el idioma del usuario. Este valor indica qué debe enviar el explorador o qué ha heredado del sistema operativo subyacente. El encabezado HTTP Accept-Language de una solicitud del explorador no es un método infalible para detectar el idioma preferido del usuario (vea [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php) [Establecer las preferencias de idioma en un explorador]). Una aplicación de producción debe ofrecer al usuario una manera de personalizar su opción de referencia cultural.

### <a name="set-the-accept-language-http-header-in-ie"></a>Establecer el encabezado HTTP Accept-Language en Internet Explorer

1. En el icono de engranaje, pulse **Opciones de Internet**.

1. Haga clic en **Lenguajes**.

   ![Opciones de Internet](localization/_static/lang.png)

1. Haga clic en **Establecer preferencias de idioma**.

1. Haga clic en **Agregar un idioma**.

1. Agregue el idioma.

1. Haga clic en el idioma y, después, en **Subir**.

### <a name="use-a-custom-provider"></a>Usar un proveedor personalizado

Imagine que quiere permitir que los clientes almacenen su idioma y su referencia cultural en las bases de datos. Puede escribir un proveedor para que busque estos valores para el usuario. En el código siguiente se muestra cómo agregar un proveedor personalizado:

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

Use `RequestLocalizationOptions` para agregar o quitar proveedores de localización.

### <a name="set-the-culture-programmatically"></a>Establecer la referencia cultural mediante programación

Este proyecto **Localization.StarterWeb** de ejemplo de [GitHub](https://github.com/aspnet/entropy) contiene una interfaz de usuario para establecer el valor `Culture`. El archivo *Views/Shared/_SelectLanguagePartial.cshtml* le permite seleccionar la referencia cultural de la lista de referencias culturales admitidas:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

El archivo *Views/Shared/_SelectLanguagePartial.cshtml* se agrega a la sección `footer` del archivo de diseño para que esté disponible para todas las vistas:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

El método `SetLanguage` establece la cookie de la referencia cultural.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

No se puede conectar el archivo *_SelectLanguagePartial.cshtml* con código de ejemplo para este proyecto. El proyecto **Localization.StarterWeb** de [GitHub](https://github.com/aspnet/entropy) tiene código para enviar `RequestLocalizationOptions` a una vista parcial de Razor a través del contenedor de [inserción de dependencias](dependency-injection.md).

## <a name="model-binding-route-data-and-query-strings"></a>Datos de ruta y cadenas de consulta de enlace de modelos

Vea [Comportamiento de globalización de los datos de ruta de enlace de modelos y las cadenas de consulta](xref:mvc/models/model-binding#glob).

## <a name="globalization-and-localization-terms"></a>Términos relacionados con la globalización y la localización

Para localizar una aplicación también es necesario contar con unos conocimientos básicos sobre los juegos de caracteres pertinentes que se usan en el desarrollo de software moderno y sobre los problemas asociados. Aunque todos los equipos almacenan texto como números (códigos), cada sistema almacena el mismo texto con números diferentes. El proceso de localización consiste en traducir la interfaz de usuario (IU) de la aplicación a una referencia cultural o configuración regional específica.

La [localizabilidad](/dotnet/standard/globalization-localization/localizability-review) es un proceso intermedio para comprobar que una aplicación globalizada está preparada para la localización.

El formato [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) para el nombre de la referencia cultural es `<languagecode2>-<country/regioncode2>`, donde `<languagecode2>` es el código de idioma y `<country/regioncode2>` es el código de la referencia cultural secundaria. Por ejemplo, `es-CL` para español (Chile), `en-US` para inglés (Estados Unidos) y `en-AU` para inglés (Australia). [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) es una combinación de un código de referencia cultural ISO 639 de dos letras en minúsculas asociado con un idioma y un código de referencia cultural secundaria ISO 3166 de dos letras en mayúsculas asociado con un país o región. Vea <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.

"Internacionalización" suele abreviarse como "I18N". La abreviatura toma la primera y la última letra de la palabra, y el número de letras que hay entre ellas, es decir, el 18 representa el número de letras que hay entre la "I" inicial y la "N" final. Lo mismo se puede decir de "globalización" (G11N) y "localización" (L10N).

Términos:

* Globalización (G11N): es el proceso de hacer que una aplicación admita diferentes idiomas y regiones.
* Localización (L10N): es el proceso de personalizar una aplicación para un idioma y región determinados.
* Internacionalización (I18N): hace referencia a la globalización y la localización.
* Referencia cultural: es un idioma y, opcionalmente, una región.
* Referencia cultural neutra: se trata de una referencia cultural que tiene un idioma especificado, pero no una región. (por ejemplo, "en" y "es").
* Referencia cultural específica: es una referencia cultural que tiene un idioma y una región especificados (por ejemplo, "en-US", "en-GB" y "es-CL").
* Referencia cultural principal: se trata de la referencia cultural neutra que contiene una referencia cultural específica. (por ejemplo, "en" es la referencia cultural principal de "en-US" y "en-GB").
* Configuración regional: la configuración regional es lo mismo que la referencia cultural.

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* [Proyecto Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) usado en el artículo
* [Globalización y localización de aplicaciones .NET](/dotnet/standard/globalization-localization/index)
* [Recursos en archivos .resx](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Kit de herramientas de aplicaciones multilingüe de Microsoft](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [Localización y genéricos](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) y [Hisham Bin Ateya](https://twitter.com/hishambinateya)

Con un sitio web multilingüe, se puede llegar a un público más amplio. ASP.NET Core proporciona servicios y software intermedio para la localización en diferentes idiomas y referencias culturales.

La internacionalización conlleva [globalización](/dotnet/api/system.globalization) y [localización](/dotnet/standard/globalization-localization/localization). La globalización es el proceso de diseñar aplicaciones que admiten diferentes referencias culturales. La globalización agrega compatibilidad con la entrada, la visualización y la salida de un conjunto definido de scripts de lenguaje relacionados con áreas geográficas específicas.

La localización es el proceso de adaptar una aplicación globalizada, que ya se ha procesado para la localizabilidad, a una determinada referencia cultural o configuración regional. Para más información, vea **Términos relacionados con la globalización y la localización** al final de este documento.

La localización de la aplicación implica lo siguiente:

1. Hacer que el contenido de la aplicación sea localizable
1. Proporcionar recursos localizados para los idiomas y las referencias culturales admitidos
1. Implementar una estrategia para seleccionar el idioma o la referencia cultural de cada solicitud

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>Hacer que el contenido de la aplicación sea localizable

<xref:Microsoft.Extensions.Localization.IStringLocalizer> y <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> se diseñaron para mejorar la productividad al desarrollar aplicaciones localizadas. `IStringLocalizer` usa <xref:System.Resources.ResourceManager> y <xref:System.Resources.ResourceReader> para proporcionar recursos específicos de la referencia cultural en tiempo de ejecución. La interfaz tiene un indizador y un `IEnumerable` para devolver las cadenas localizadas. `IStringLocalizer` no necesita que se almacenen las cadenas de idioma predeterminado en un archivo de recursos. Puede desarrollar una aplicación destinada a la localización sin necesidad de crear archivos de recursos al principio de la fase de desarrollo. En el código siguiente se muestra cómo ajustar la cadena "About Title" para la localización.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

En el código anterior, la implementación de `IStringLocalizer<T>` procede de la [inserción de dependencias](dependency-injection.md). Si no se encuentra el valor localizado de "About Title, se devuelve la clave de indizador, es decir, la cadena "About Title". Puede dejar las cadenas literales del idioma predeterminado en la aplicación y ajustarlas en el localizador, de modo que se pueda centrar en el desarrollo de la aplicación. Desarrolle la aplicación con el idioma predeterminado y prepárela para el proceso de localización sin necesidad de crear primero un archivo de recursos predeterminado. También puede seguir el método tradicional y proporcionar una clave para recuperar la cadena de idioma predeterminado. El nuevo flujo de trabajo, que carece de archivo *.resx* de idioma predeterminado y simplemente ajusta los literales de cadena, puede ahorrar a muchos desarrolladores la sobrecarga de localizar una aplicación. Otros desarrolladores preferirán el flujo de trabajo tradicional, ya que facilita el trabajo con literales de cadena más largos, así como la actualización de las cadenas localizadas.

Use la implementación de `IHtmlLocalizer<T>` para los recursos que contienen HTML. El HTML de `IHtmlLocalizer` codifica los argumentos a los que se da formato en la cadena de recursos, pero no codifica como HTML la cadena de recursos en sí misma. En el ejemplo que se muestra a continuación, solo está codificado en HTML el valor del parámetro `name`.

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> Por lo general, solo se localiza texto, no HTML.

En el nivel más bajo, puede obtener `IStringLocalizerFactory` de la [inserción de dependencias](dependency-injection.md):

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

En el código anterior se muestran los dos métodos factory.Create.

Puede dividir las cadenas localizadas por controlador o por área, o bien tener un solo contenedor. En la aplicación de ejemplo, se usa una clase ficticia denominada `SharedResource` para los recursos compartidos.

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

Algunos programadores usan la clase `Startup` para contener cadenas globales o compartidas. En el ejemplo siguiente, se usan los localizadores `InfoController` y `SharedResource`:

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Localización de vista

El servicio `IViewLocalizer` proporciona cadenas localizadas para una [vista](xref:mvc/views/overview). La clase `ViewLocalizer` implementa esta interfaz y busca la ubicación del recurso en la ruta de acceso del archivo de vista. En el código siguiente se muestra cómo usar la implementación predeterminada de `IViewLocalizer`:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

La implementación predeterminada de `IViewLocalizer` busca el archivo de recursos según el nombre del archivo de vista. No se puede usar un archivo de recursos compartidos global. `ViewLocalizer` implementa el localizador mediante `IHtmlLocalizer`, por lo que Razor no codifica como HTML la cadena localizada. Puede parametrizar las cadenas de recursos y `IViewLocalizer` codificará como HTML los parámetros, pero no la cadena de recursos. Observe el siguiente marcado Razor:

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Un archivo de recursos en francés podría contener lo siguiente:

| Key | Valor |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

La vista representada contendría el marcado HTML del archivo de recursos.

> [!NOTE]
> Por lo general, solo se localiza texto, no HTML.

Para usar un archivo de recursos compartido en una vista, inserte `IHtmlLocalizer<T>`:

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Localización de DataAnnotations

Los mensajes de error de DataAnnotations se localizan con `IStringLocalizer<T>`. Mediante la opción `ResourcesPath = "Resources"`, es posible almacenar los mensajes de error en `RegisterViewModel` en cualquiera de las rutas de acceso siguientes:

* *Resources/ViewModels.Account.RegisterViewModel.fr.resx*
* *Resources/ViewModels/Account/RegisterViewModel.fr.resx*

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

En ASP.NET Core MVC 1.1.0 y versiones posteriores, los atributos que no son de validación están localizados. ASP.NET Core MVC 1.0 **no** busca cadenas localizadas para los atributos que no son de validación.

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>Uso de una cadena de recursos para varias clases

En el código siguiente se muestra cómo usar una cadena de recursos para atributos de validación con varias clases:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

En el código anterior, `SharedResource` es la clase correspondiente al archivo resx donde se almacenan los mensajes de validación. Según este método, DataAnnotations solo usará `SharedResource`, en lugar del recurso de cada clase.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Proporcionar recursos localizados para los idiomas y las referencias culturales admitidos

### <a name="supportedcultures-and-supporteduicultures"></a>SupportedCultures y SupportedUICultures

ASP.NET Core permite especificar dos valores de referencia cultural, `SupportedCultures` y `SupportedUICultures`. El objeto [CultureInfo](/dotnet/api/system.globalization.cultureinfo) para `SupportedCultures` determina los resultados de funciones dependientes de la referencia cultural, como el formato de fecha, hora, número y moneda. `SupportedCultures` también determina el criterio de ordenación del texto, las convenciones sobre el uso de mayúsculas y minúsculas, y las comparaciones de cadenas. Vea [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) para obtener más información sobre la manera en que el servidor obtiene la referencia cultural. `SupportedUICultures` determina qué cadenas traducidas buscará [ResourceManager](/dotnet/api/system.resources.resourcemanager) (en archivos *.resx*). `ResourceManager` simplemente busca cadenas específicas de referencias culturales determinadas por `CurrentUICulture`. Todos los subprocesos de .NET tienen objetos `CurrentCulture` y `CurrentUICulture`. ASP.NET Core inspecciona estos valores al representar funciones dependientes de la referencia cultural. Por ejemplo, si la referencia cultural del subproceso actual está establecida en "en-US" (inglés (Estados Unidos)), `DateTime.Now.ToLongDateString()` mostrará "Thursday, February 18, 2016". En cambio, si `CurrentCulture` está establecido en "es-ES" (español (España)), la salida será "jueves, 18 de febrero de 2016".

## <a name="resource-files"></a>Archivos de recursos

Un archivo de recursos es un mecanismo útil para separar del código las cadenas localizables. Las cadenas traducidas para el idioma no predeterminado están aisladas en archivos de recursos *.resx*. Pongamos por caso que quiere crear un archivo de recursos de español denominado *Welcome.es.resx* que contenga cadenas traducidas. "es" es el código de idioma para español. Para crear este archivo de recursos en Visual Studio:

1. En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta que contendrá el archivo de recursos > **Agregar** > **Nuevo elemento**.

   ![Menú contextual anidado: en el Explorador de soluciones, se abre un menú contextual para los recursos. Se abre un segundo menú contextual, Agregar, en el que se muestra el comando Nuevo elemento resaltado.](localization/_static/newi.png)

1. En el cuadro para **buscar plantillas instaladas**, escriba "recurso" y asigne un nombre al archivo.

   ![Cuadro de diálogo Agregar nuevo elemento](localization/_static/res.png)

1. Escriba el valor de clave (cadena nativa) en la columna **Nombre** y la cadena traducida en la columna **Valor**.

   ![Archivo Welcome.es.resx (archivo de recursos de bienvenida para el idioma español) con la palabra Hello en la columna Nombre y la palabra Hola en la columna Valor](localization/_static/hola.png)

   El archivo *Welcome.es.resx* aparece en Visual Studio.

   ![Archivo de recursos de bienvenida en español (es) en el Explorador de soluciones](localization/_static/se.png)

## <a name="resource-file-naming"></a>Nomenclatura de los archivos de recursos

El nombre de los recursos es el nombre del tipo completo de su clase menos el nombre del ensamblado. Por ejemplo, un recurso francés de un proyecto cuyo ensamblado principal sea `LocalizationWebsite.Web.dll` para la clase `LocalizationWebsite.Web.Startup` se denominará *Startup.fr.resx*. Un recurso para la clase `LocalizationWebsite.Web.Controllers.HomeController` se denominará *Controllers.HomeController.fr.resx*. Si el espacio de nombres de la clase de destino no es igual que el nombre del ensamblado, necesitará el nombre de tipo completo. Por ejemplo, en el proyecto de ejemplo, un recurso para el tipo `ExtraNamespace.Tools` se denominará *ExtraNamespace.Tools.fr.resx*.

En el proyecto de ejemplo, el método `ConfigureServices` establece `ResourcesPath` en "Resources", por lo que la ruta de acceso relativa del proyecto para el archivo de recursos en francés del controlador de inicio es *Resources/Controllers.HomeController.fr.resx*. También puede usar carpetas para organizar los archivos de recursos. Para el controlador de inicio, la ruta de acceso sería *Resources/Controllers/HomeController.fr.resx*. Si no usa la opción `ResourcesPath`, el archivo *.resx* estará en el directorio base del proyecto. El archivo de recursos para `HomeController` se denominará *Controllers.HomeController.fr.resx*. La opción de usar la convención de nomenclatura de punto o ruta de acceso depende de la manera en que quiera organizar los archivos de recursos.

| Nombre del recurso | Nomenclatura de punto o ruta de acceso |
| ------------   | ------------- |
| Resources/Controllers.HomeController.fr.resx | Punto  |
| Resources/Controllers/HomeController.fr.resx  | Ruta de acceso |

Los archivos de recursos que usan `@inject IViewLocalizer` en las vistas de Razor siguen un patrón similar. Para asignar un nombre al archivo de recursos de una vista, se puede usar la nomenclatura de punto o de ruta de acceso. Los archivos de recursos de una vista de Razor imitan la ruta de acceso de su archivo de vista asociado. Supongamos que establecemos `ResourcesPath` en "Resources". En ese caso, el archivo de recursos de francés asociado a la vista *Views/Home/About.cshtml* podría ser uno de los siguientes:

* Resources/Views/Home/About.fr.resx

* Resources/Views.Home.About.fr.resx

Si no usa la opción `ResourcesPath`, el archivo *.resx* de una vista se ubicará en la misma carpeta que la vista.

### <a name="rootnamespaceattribute"></a>RootNamespaceAttribute 

El atributo [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) proporciona el espacio de nombres raíz de un ensamblado cuando el espacio de nombres raíz del ensamblado es diferente del nombre de ensamblado. 

> [!WARNING]
> Esto puede ocurrir cuando el nombre de un proyecto no es un identificador de .NET válido. Por ejemplo `my-project-name.csproj` usará el espacio de nombres raíz `my_project_name` y el nombre de ensamblado `my-project-name`, lo que lleva a este error. 

Si el espacio de nombres raíz de un ensamblado es diferente del nombre de ensamblado:

* La localización no funciona de forma predeterminada.
* Se produce un error en la localización debido a la manera en que se buscan los recursos dentro del ensamblado. `RootNamespace` es un valor en tiempo de compilación que no está disponible para el proceso en ejecución. 

Si `RootNamespace` es diferente de `AssemblyName`, incluya lo siguiente en *AssemblyInfo.cs* (con los valores de parámetro reemplazados por los valores reales):

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

El código anterior permite la resolución correcta de los archivos resx.

## <a name="culture-fallback-behavior"></a>Comportamiento de reserva de la referencia cultural

Cuando se busca un recurso, la localización entra en un proceso conocido como "reserva de la referencia cultural". Partiendo de la referencia cultural solicitada, si esta no se encuentra, se revierte a su referencia cultural principal correspondiente. Como inciso, decir que la propiedad [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) representa la referencia cultural principal. Eso suele conllevar (aunque no siempre) la eliminación del significante nacional del código ISO. Así, por ejemplo, el dialecto de español hablado en México es "es-MX". Contiene el elemento principal "es", que corresponde a español no específico de ningún país en concreto.

Imagine que su sitio recibe una solicitud sobre un recurso "Welcome" donde se emplea la referencia cultural "fr-CA". El sistema de localización busca los recursos siguientes (en orden) y selecciona la primera coincidencia:

* *Welcome.fr-CA.resx*
* *Welcome.fr.resx*
* *Welcome.resx* (si `NeutralResourcesLanguage` es "fr-CA")

Consideremos, por ejemplo, que quita el designador de referencia cultural ".fr" y la referencia cultural está establecida en francés. En ese caso, se lee el archivo de recursos predeterminado y se localizan las cadenas. El Administrador de recursos designa un recurso predeterminado o de reserva para los casos en que nada coincida con la referencia cultural solicitada. Si quiere simplemente devolver la clave cuando falte un recurso para la referencia cultural solicitada, no debe tener un archivo de recursos predeterminado.

### <a name="generate-resource-files-with-visual-studio"></a>Generar archivos de recursos con Visual Studio

Si crea un archivo de recursos en Visual Studio sin una referencia cultural en el nombre de archivo (por ejemplo, *Welcome.resx*), Visual Studio creará una clase de C# con una propiedad para cada cadena. Normalmente esto no interesa con ASP.NET Core; por lo general, no tendrá un archivo de recursos *.resx* predeterminado (un archivo *.resx* sin el nombre de la referencia cultural). Se recomienda que cree el archivo *.resx* con un nombre de referencia cultural (por ejemplo, *Welcome.fr.resx*). Cuando cree un archivo *.resx* con un nombre de referencia cultural, Visual Studio no generará el archivo de clase.

### <a name="add-other-cultures"></a>Agregar otras referencias culturales

Cada combinación de idioma y referencia cultural (que no sea el idioma predeterminado) requiere un archivo de recursos único. Para crear archivos de recursos para otras referencias culturales y configuraciones regionales, debe crear archivos de recursos en los que los códigos de idioma ISO formen parte del nombre de archivo (por ejemplo, **en-us**, **fr-ca** y  **en-gb**). Estos códigos ISO se colocan entre el nombre de archivo y la extensión de archivo *.resx*, como en *Welcome.es-MX.resx* (español [México]).

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Implementar una estrategia para seleccionar el idioma o la referencia cultural de cada solicitud

### <a name="configure-localization"></a>Configurar la localización

La localización se configura en el método `Startup.ConfigureServices`:

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* `AddLocalization` agrega los servicios de localización al contenedor de servicios. El código anterior también establece la ruta de acceso a los recursos en "Resources".

* `AddViewLocalization` agrega compatibilidad con los archivos de vista localizados. En este ejemplo, la localización de vista se basa en el sufijo del archivo de vista. Por ejemplo, "fr" en el archivo *Index.fr.cshtml*.

* `AddDataAnnotationsLocalization` agrega compatibilidad con mensajes de validación de `DataAnnotations` localizados mediante abstracciones `IStringLocalizer`.

### <a name="localization-middleware"></a>Software intermedio de localización

La referencia cultural actual de una solicitud se establece en el [software intermedio](xref:fundamentals/middleware/index) de localización. El software intermedio de localización se habilita en el método `Startup.Configure`. El software intermedio de localización debe configurarse antes que cualquier software intermedio que pueda comprobar la referencia cultural de la solicitud (por ejemplo, `app.UseMvcWithDefaultRoute()`).

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

`UseRequestLocalization` inicializa un objeto `RequestLocalizationOptions`. En todas las solicitudes, se enumera la lista de `RequestCultureProvider` en `RequestLocalizationOptions` y se usa el primer proveedor que puede determinar correctamente la referencia cultural de la solicitud. Los proveedores predeterminados proceden de la clase `RequestLocalizationOptions`:

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

La lista predeterminada va de más específico a menos específico. Más adelante en el artículo veremos cómo puede cambiar el orden e incluso agregar un proveedor de referencia cultural personalizado. Si ninguno de los proveedores puede determinar la referencia cultural de la solicitud, se usa `DefaultRequestCulture`.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

Algunas aplicaciones usarán una cadena de consulta para establecer <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1>. Para las aplicaciones que usan el método de cookie o de encabezado Accept-Language, es útil agregar una cadena de consulta a la dirección URL para depurar y probar el código. De forma predeterminada, `QueryStringRequestCultureProvider` está registrado como primer proveedor de localización en la lista `RequestCultureProvider`. Debe pasar los parámetros de cadena de consulta `culture` y `ui-culture`. En el ejemplo siguiente, la referencia cultural específica (idioma y región) se establece en español (México):

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

Si solo pasa uno de los dos parámetros (`culture` o `ui-culture`), el proveedor de la cadena de consulta usará el valor que usted ha pasado para establecer ambos valores. Por ejemplo, si solo establece la referencia cultural, se establecerán `Culture` y `UICulture`:

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a>CookieRequestCultureProvider

Las aplicaciones de producción suelen proporcionar un mecanismo para establecer la referencia cultural con la cookie de la referencia cultural de ASP.NET Core. Use el método `MakeCookieValue` para crear una cookie.

`CookieRequestCultureProvider` `DefaultCookieName` devuelve el nombre de cookie predeterminado usado para realizar un seguimiento de la información de la referencia cultural preferida del usuario. El nombre de cookie predeterminado es `.AspNetCore.Culture`.

El formato de la cookie es `c=%LANGCODE%|uic=%LANGCODE%`, donde `c` es `Culture` y `uic` es `UICulture`, por ejemplo:

```
c=en-UK|uic=en-US
```

Si solo especifica uno de los dos valores, ya sea la información de la referencia cultural o la referencia cultural de la interfaz de usuario, la referencia cultural especificada se usará tanto para la información de la referencia cultural como para la referencia cultural de la interfaz de usuario.

### <a name="the-accept-language-http-header"></a>Encabezado HTTP Accept-Language

El [encabezado Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) se puede establecer en la mayoría de los exploradores y está diseñado inicialmente para especificar el idioma del usuario. Este valor indica qué debe enviar el explorador o qué ha heredado del sistema operativo subyacente. El encabezado HTTP Accept-Language de una solicitud del explorador no es un método infalible para detectar el idioma preferido del usuario (vea [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php) [Establecer las preferencias de idioma en un explorador]). Una aplicación de producción debe ofrecer al usuario una manera de personalizar su opción de referencia cultural.

### <a name="set-the-accept-language-http-header-in-ie"></a>Establecer el encabezado HTTP Accept-Language en Internet Explorer

1. En el icono de engranaje, pulse **Opciones de Internet**.

1. Haga clic en **Lenguajes**.

   ![Opciones de Internet](localization/_static/lang.png)

1. Haga clic en **Establecer preferencias de idioma**.

1. Haga clic en **Agregar un idioma**.

1. Agregue el idioma.

1. Haga clic en el idioma y, después, en **Subir**.

### <a name="use-a-custom-provider"></a>Usar un proveedor personalizado

Imagine que quiere permitir que los clientes almacenen su idioma y su referencia cultural en las bases de datos. Puede escribir un proveedor para que busque estos valores para el usuario. En el código siguiente se muestra cómo agregar un proveedor personalizado:

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

Use `RequestLocalizationOptions` para agregar o quitar proveedores de localización.

### <a name="set-the-culture-programmatically"></a>Establecer la referencia cultural mediante programación

Este proyecto **Localization.StarterWeb** de ejemplo de [GitHub](https://github.com/aspnet/entropy) contiene una interfaz de usuario para establecer el valor `Culture`. El archivo *Views/Shared/_SelectLanguagePartial.cshtml* le permite seleccionar la referencia cultural de la lista de referencias culturales admitidas:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

El archivo *Views/Shared/_SelectLanguagePartial.cshtml* se agrega a la sección `footer` del archivo de diseño para que esté disponible para todas las vistas:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

El método `SetLanguage` establece la cookie de la referencia cultural.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

No se puede conectar el archivo *_SelectLanguagePartial.cshtml* con código de ejemplo para este proyecto. El proyecto **Localization.StarterWeb** de [GitHub](https://github.com/aspnet/entropy) tiene código para enviar `RequestLocalizationOptions` a una vista parcial de Razor a través del contenedor de [inserción de dependencias](dependency-injection.md).

## <a name="model-binding-route-data-and-query-strings"></a>Datos de ruta y cadenas de consulta de enlace de modelos

Vea [Comportamiento de globalización de los datos de ruta de enlace de modelos y las cadenas de consulta](xref:mvc/models/model-binding#glob).

## <a name="globalization-and-localization-terms"></a>Términos relacionados con la globalización y la localización

Para localizar una aplicación también es necesario contar con unos conocimientos básicos sobre los juegos de caracteres pertinentes que se usan en el desarrollo de software moderno y sobre los problemas asociados. Aunque todos los equipos almacenan texto como números (códigos), cada sistema almacena el mismo texto con números diferentes. El proceso de localización consiste en traducir la interfaz de usuario (IU) de la aplicación a una referencia cultural o configuración regional específica.

La [localizabilidad](/dotnet/standard/globalization-localization/localizability-review) es un proceso intermedio para comprobar que una aplicación globalizada está preparada para la localización.

El formato [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) para el nombre de la referencia cultural es `<languagecode2>-<country/regioncode2>`, donde `<languagecode2>` es el código de idioma y `<country/regioncode2>` es el código de la referencia cultural secundaria. Por ejemplo, `es-CL` para español (Chile), `en-US` para inglés (Estados Unidos) y `en-AU` para inglés (Australia). [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) es una combinación de un código de referencia cultural ISO 639 de dos letras en minúsculas asociado con un idioma y un código de referencia cultural secundaria ISO 3166 de dos letras en mayúsculas asociado con un país o región. Vea <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.

"Internacionalización" suele abreviarse como "I18N". La abreviatura toma la primera y la última letra de la palabra, y el número de letras que hay entre ellas, es decir, el 18 representa el número de letras que hay entre la "I" inicial y la "N" final. Lo mismo se puede decir de "globalización" (G11N) y "localización" (L10N).

Términos:

* Globalización (G11N): es el proceso de hacer que una aplicación admita diferentes idiomas y regiones.
* Localización (L10N): es el proceso de personalizar una aplicación para un idioma y región determinados.
* Internacionalización (I18N): hace referencia a la globalización y la localización.
* Referencia cultural: es un idioma y, opcionalmente, una región.
* Referencia cultural neutra: se trata de una referencia cultural que tiene un idioma especificado, pero no una región. (por ejemplo, "en" y "es").
* Referencia cultural específica: es una referencia cultural que tiene un idioma y una región especificados (por ejemplo, "en-US", "en-GB" y "es-CL").
* Referencia cultural principal: se trata de la referencia cultural neutra que contiene una referencia cultural específica. (por ejemplo, "en" es la referencia cultural principal de "en-US" y "en-GB").
* Configuración regional: la configuración regional es lo mismo que la referencia cultural.

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* [Proyecto Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) usado en el artículo
* [Globalización y localización de aplicaciones .NET](/dotnet/standard/globalization-localization/index)
* [Recursos en archivos .resx](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Kit de herramientas de aplicaciones multilingüe de Microsoft](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [Localización y genéricos](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) y [Hisham Bin Ateya](https://twitter.com/hishambinateya)

Con un sitio web multilingüe, se puede llegar a un público más amplio. ASP.NET Core proporciona servicios y software intermedio para la localización en diferentes idiomas y referencias culturales.

La internacionalización conlleva [globalización](/dotnet/api/system.globalization) y [localización](/dotnet/standard/globalization-localization/localization). La globalización es el proceso de diseñar aplicaciones que admiten diferentes referencias culturales. La globalización agrega compatibilidad con la entrada, la visualización y la salida de un conjunto definido de scripts de lenguaje relacionados con áreas geográficas específicas.

La localización es el proceso de adaptar una aplicación globalizada, que ya se ha procesado para la localizabilidad, a una determinada referencia cultural o configuración regional. Para más información, vea **Términos relacionados con la globalización y la localización** al final de este documento.

La localización de la aplicación implica lo siguiente:

1. Hacer que el contenido de la aplicación sea localizable
1. Proporcionar recursos localizados para los idiomas y las referencias culturales admitidos
1. Implementar una estrategia para seleccionar el idioma o la referencia cultural de cada solicitud

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>Hacer que el contenido de la aplicación sea localizable

<xref:Microsoft.Extensions.Localization.IStringLocalizer> y <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> se diseñaron para mejorar la productividad al desarrollar aplicaciones localizadas. `IStringLocalizer` usa <xref:System.Resources.ResourceManager> y <xref:System.Resources.ResourceReader> para proporcionar recursos específicos de la referencia cultural en tiempo de ejecución. La interfaz tiene un indizador y un `IEnumerable` para devolver las cadenas localizadas. `IStringLocalizer` no necesita que se almacenen las cadenas de idioma predeterminado en un archivo de recursos. Puede desarrollar una aplicación destinada a la localización sin necesidad de crear archivos de recursos al principio de la fase de desarrollo. En el código siguiente se muestra cómo ajustar la cadena "About Title" para la localización.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

En el código anterior, la implementación de `IStringLocalizer<T>` procede de la [inserción de dependencias](dependency-injection.md). Si no se encuentra el valor localizado de "About Title, se devuelve la clave de indizador, es decir, la cadena "About Title". Puede dejar las cadenas literales del idioma predeterminado en la aplicación y ajustarlas en el localizador, de modo que se pueda centrar en el desarrollo de la aplicación. Desarrolle la aplicación con el idioma predeterminado y prepárela para el proceso de localización sin necesidad de crear primero un archivo de recursos predeterminado. También puede seguir el método tradicional y proporcionar una clave para recuperar la cadena de idioma predeterminado. El nuevo flujo de trabajo, que carece de archivo *.resx* de idioma predeterminado y simplemente ajusta los literales de cadena, puede ahorrar a muchos desarrolladores la sobrecarga de localizar una aplicación. Otros desarrolladores preferirán el flujo de trabajo tradicional, ya que facilita el trabajo con literales de cadena más largos, así como la actualización de las cadenas localizadas.

Use la implementación de `IHtmlLocalizer<T>` para los recursos que contienen HTML. El HTML de `IHtmlLocalizer` codifica los argumentos a los que se da formato en la cadena de recursos, pero no codifica como HTML la cadena de recursos en sí misma. En el ejemplo que se muestra a continuación, solo está codificado en HTML el valor del parámetro `name`.

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> Por lo general, solo se localiza texto, no HTML.

En el nivel más bajo, puede obtener `IStringLocalizerFactory` de la [inserción de dependencias](dependency-injection.md):

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

En el código anterior se muestran los dos métodos factory.Create.

Puede dividir las cadenas localizadas por controlador o por área, o bien tener un solo contenedor. En la aplicación de ejemplo, se usa una clase ficticia denominada `SharedResource` para los recursos compartidos.

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

Algunos programadores usan la clase `Startup` para contener cadenas globales o compartidas. En el ejemplo siguiente, se usan los localizadores `InfoController` y `SharedResource`:

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Localización de vista

El servicio `IViewLocalizer` proporciona cadenas localizadas para una [vista](xref:mvc/views/overview). La clase `ViewLocalizer` implementa esta interfaz y busca la ubicación del recurso en la ruta de acceso del archivo de vista. En el código siguiente se muestra cómo usar la implementación predeterminada de `IViewLocalizer`:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

La implementación predeterminada de `IViewLocalizer` busca el archivo de recursos según el nombre del archivo de vista. No se puede usar un archivo de recursos compartidos global. `ViewLocalizer` implementa el localizador mediante `IHtmlLocalizer`, por lo que Razor no codifica como HTML la cadena localizada. Puede parametrizar las cadenas de recursos y `IViewLocalizer` codificará como HTML los parámetros, pero no la cadena de recursos. Observe el siguiente marcado Razor:

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Un archivo de recursos en francés podría contener lo siguiente:

| Key | Valor |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

La vista representada contendría el marcado HTML del archivo de recursos.

> [!NOTE]
> Por lo general, solo se localiza texto, no HTML.

Para usar un archivo de recursos compartido en una vista, inserte `IHtmlLocalizer<T>`:

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Localización de DataAnnotations

Los mensajes de error de DataAnnotations se localizan con `IStringLocalizer<T>`. Mediante la opción `ResourcesPath = "Resources"`, es posible almacenar los mensajes de error en `RegisterViewModel` en cualquiera de las rutas de acceso siguientes:

* *Resources/ViewModels.Account.RegisterViewModel.fr.resx*
* *Resources/ViewModels/Account/RegisterViewModel.fr.resx*

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

En ASP.NET Core MVC 1.1.0 y versiones posteriores, los atributos que no son de validación están localizados. ASP.NET Core MVC 1.0 **no** busca cadenas localizadas para los atributos que no son de validación.

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>Uso de una cadena de recursos para varias clases

En el código siguiente se muestra cómo usar una cadena de recursos para atributos de validación con varias clases:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

En el código anterior, `SharedResource` es la clase correspondiente al archivo resx donde se almacenan los mensajes de validación. Según este método, DataAnnotations solo usará `SharedResource`, en lugar del recurso de cada clase.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Proporcionar recursos localizados para los idiomas y las referencias culturales admitidos

### <a name="supportedcultures-and-supporteduicultures"></a>SupportedCultures y SupportedUICultures

ASP.NET Core permite especificar dos valores de referencia cultural, `SupportedCultures` y `SupportedUICultures`. El objeto [CultureInfo](/dotnet/api/system.globalization.cultureinfo) para `SupportedCultures` determina los resultados de funciones dependientes de la referencia cultural, como el formato de fecha, hora, número y moneda. `SupportedCultures` también determina el criterio de ordenación del texto, las convenciones sobre el uso de mayúsculas y minúsculas, y las comparaciones de cadenas. Vea [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) para obtener más información sobre la manera en que el servidor obtiene la referencia cultural. `SupportedUICultures` determina qué cadenas traducidas buscará [ResourceManager](/dotnet/api/system.resources.resourcemanager) (en archivos *.resx*). `ResourceManager` simplemente busca cadenas específicas de referencias culturales determinadas por `CurrentUICulture`. Todos los subprocesos de .NET tienen objetos `CurrentCulture` y `CurrentUICulture`. ASP.NET Core inspecciona estos valores al representar funciones dependientes de la referencia cultural. Por ejemplo, si la referencia cultural del subproceso actual está establecida en "en-US" (inglés (Estados Unidos)), `DateTime.Now.ToLongDateString()` mostrará "Thursday, February 18, 2016". En cambio, si `CurrentCulture` está establecido en "es-ES" (español (España)), la salida será "jueves, 18 de febrero de 2016".

## <a name="resource-files"></a>Archivos de recursos

Un archivo de recursos es un mecanismo útil para separar del código las cadenas localizables. Las cadenas traducidas para el idioma no predeterminado están aisladas en archivos de recursos *.resx*. Pongamos por caso que quiere crear un archivo de recursos de español denominado *Welcome.es.resx* que contenga cadenas traducidas. "es" es el código de idioma para español. Para crear este archivo de recursos en Visual Studio:

1. En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta que contendrá el archivo de recursos > **Agregar** > **Nuevo elemento**.

   ![Menú contextual anidado: en el Explorador de soluciones, se abre un menú contextual para los recursos. Se abre un segundo menú contextual, Agregar, en el que se muestra el comando Nuevo elemento resaltado.](localization/_static/newi.png)

1. En el cuadro para **buscar plantillas instaladas**, escriba "recurso" y asigne un nombre al archivo.

   ![Cuadro de diálogo Agregar nuevo elemento](localization/_static/res.png)

1. Escriba el valor de clave (cadena nativa) en la columna **Nombre** y la cadena traducida en la columna **Valor**.

   ![Archivo Welcome.es.resx (archivo de recursos de bienvenida para el idioma español) con la palabra Hello en la columna Nombre y la palabra Hola en la columna Valor](localization/_static/hola.png)

   El archivo *Welcome.es.resx* aparece en Visual Studio.

   ![Archivo de recursos de bienvenida en español (es) en el Explorador de soluciones](localization/_static/se.png)

## <a name="resource-file-naming"></a>Nomenclatura de los archivos de recursos

El nombre de los recursos es el nombre del tipo completo de su clase menos el nombre del ensamblado. Por ejemplo, un recurso francés de un proyecto cuyo ensamblado principal sea `LocalizationWebsite.Web.dll` para la clase `LocalizationWebsite.Web.Startup` se denominará *Startup.fr.resx*. Un recurso para la clase `LocalizationWebsite.Web.Controllers.HomeController` se denominará *Controllers.HomeController.fr.resx*. Si el espacio de nombres de la clase de destino no es igual que el nombre del ensamblado, necesitará el nombre de tipo completo. Por ejemplo, en el proyecto de ejemplo, un recurso para el tipo `ExtraNamespace.Tools` se denominará *ExtraNamespace.Tools.fr.resx*.

En el proyecto de ejemplo, el método `ConfigureServices` establece `ResourcesPath` en "Resources", por lo que la ruta de acceso relativa del proyecto para el archivo de recursos en francés del controlador de inicio es *Resources/Controllers.HomeController.fr.resx*. También puede usar carpetas para organizar los archivos de recursos. Para el controlador de inicio, la ruta de acceso sería *Resources/Controllers/HomeController.fr.resx*. Si no usa la opción `ResourcesPath`, el archivo *.resx* estará en el directorio base del proyecto. El archivo de recursos para `HomeController` se denominará *Controllers.HomeController.fr.resx*. La opción de usar la convención de nomenclatura de punto o ruta de acceso depende de la manera en que quiera organizar los archivos de recursos.

| Nombre del recurso | Nomenclatura de punto o ruta de acceso |
| ------------   | ------------- |
| Resources/Controllers.HomeController.fr.resx | Punto  |
| Resources/Controllers/HomeController.fr.resx  | Ruta de acceso |

Los archivos de recursos que usan `@inject IViewLocalizer` en las vistas de Razor siguen un patrón similar. Para asignar un nombre al archivo de recursos de una vista, se puede usar la nomenclatura de punto o de ruta de acceso. Los archivos de recursos de una vista de Razor imitan la ruta de acceso de su archivo de vista asociado. Supongamos que establecemos `ResourcesPath` en "Resources". En ese caso, el archivo de recursos de francés asociado a la vista *Views/Home/About.cshtml* podría ser uno de los siguientes:

* Resources/Views/Home/About.fr.resx

* Resources/Views.Home.About.fr.resx

Si no usa la opción `ResourcesPath`, el archivo *.resx* de una vista se ubicará en la misma carpeta que la vista.

### <a name="rootnamespaceattribute"></a>RootNamespaceAttribute 

El atributo [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) proporciona el espacio de nombres raíz de un ensamblado cuando el espacio de nombres raíz del ensamblado es diferente del nombre de ensamblado. 

> [!WARNING]
> Esto puede ocurrir cuando el nombre de un proyecto no es un identificador de .NET válido. Por ejemplo `my-project-name.csproj` usará el espacio de nombres raíz `my_project_name` y el nombre de ensamblado `my-project-name`, lo que lleva a este error. 

Si el espacio de nombres raíz de un ensamblado es diferente del nombre de ensamblado:

* La localización no funciona de forma predeterminada.
* Se produce un error en la localización debido a la manera en que se buscan los recursos dentro del ensamblado. `RootNamespace` es un valor en tiempo de compilación que no está disponible para el proceso en ejecución. 

Si `RootNamespace` es diferente de `AssemblyName`, incluya lo siguiente en *AssemblyInfo.cs* (con los valores de parámetro reemplazados por los valores reales):

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

El código anterior permite la resolución correcta de los archivos resx.

## <a name="culture-fallback-behavior"></a>Comportamiento de reserva de la referencia cultural

Cuando se busca un recurso, la localización entra en un proceso conocido como "reserva de la referencia cultural". Partiendo de la referencia cultural solicitada, si esta no se encuentra, se revierte a su referencia cultural principal correspondiente. Como inciso, decir que la propiedad [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) representa la referencia cultural principal. Eso suele conllevar (aunque no siempre) la eliminación del significante nacional del código ISO. Así, por ejemplo, el dialecto de español hablado en México es "es-MX". Contiene el elemento principal "es", que corresponde a español no específico de ningún país en concreto.

Imagine que su sitio recibe una solicitud sobre un recurso "Welcome" donde se emplea la referencia cultural "fr-CA". El sistema de localización busca los recursos siguientes (en orden) y selecciona la primera coincidencia:

* *Welcome.fr-CA.resx*
* *Welcome.fr.resx*
* *Welcome.resx* (si `NeutralResourcesLanguage` es "fr-CA")

Consideremos, por ejemplo, que quita el designador de referencia cultural ".fr" y la referencia cultural está establecida en francés. En ese caso, se lee el archivo de recursos predeterminado y se localizan las cadenas. El Administrador de recursos designa un recurso predeterminado o de reserva para los casos en que nada coincida con la referencia cultural solicitada. Si quiere simplemente devolver la clave cuando falte un recurso para la referencia cultural solicitada, no debe tener un archivo de recursos predeterminado.

### <a name="generate-resource-files-with-visual-studio"></a>Generar archivos de recursos con Visual Studio

Si crea un archivo de recursos en Visual Studio sin una referencia cultural en el nombre de archivo (por ejemplo, *Welcome.resx*), Visual Studio creará una clase de C# con una propiedad para cada cadena. Normalmente esto no interesa con ASP.NET Core; por lo general, no tendrá un archivo de recursos *.resx* predeterminado (un archivo *.resx* sin el nombre de la referencia cultural). Se recomienda que cree el archivo *.resx* con un nombre de referencia cultural (por ejemplo, *Welcome.fr.resx*). Cuando cree un archivo *.resx* con un nombre de referencia cultural, Visual Studio no generará el archivo de clase.

### <a name="add-other-cultures"></a>Agregar otras referencias culturales

Cada combinación de idioma y referencia cultural (que no sea el idioma predeterminado) requiere un archivo de recursos único. Para crear archivos de recursos para otras referencias culturales y configuraciones regionales, debe crear archivos de recursos en los que los códigos de idioma ISO formen parte del nombre de archivo (por ejemplo, **en-us**, **fr-ca** y  **en-gb**). Estos códigos ISO se colocan entre el nombre de archivo y la extensión de archivo *.resx*, como en *Welcome.es-MX.resx* (español [México]).

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Implementar una estrategia para seleccionar el idioma o la referencia cultural de cada solicitud

### <a name="configure-localization"></a>Configurar la localización

La localización se configura en el método `Startup.ConfigureServices`:

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* `AddLocalization` agrega los servicios de localización al contenedor de servicios. El código anterior también establece la ruta de acceso a los recursos en "Resources".

* `AddViewLocalization` agrega compatibilidad con los archivos de vista localizados. En este ejemplo, la localización de vista se basa en el sufijo del archivo de vista. Por ejemplo, "fr" en el archivo *Index.fr.cshtml*.

* `AddDataAnnotationsLocalization` agrega compatibilidad con mensajes de validación de `DataAnnotations` localizados mediante abstracciones `IStringLocalizer`.

### <a name="localization-middleware"></a>Software intermedio de localización

La referencia cultural actual de una solicitud se establece en el [software intermedio](xref:fundamentals/middleware/index) de localización. El software intermedio de localización se habilita en el método `Startup.Configure`. El software intermedio de localización debe configurarse antes que cualquier software intermedio que pueda comprobar la referencia cultural de la solicitud (por ejemplo, `app.UseMvcWithDefaultRoute()`).

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

`UseRequestLocalization` inicializa un objeto `RequestLocalizationOptions`. En todas las solicitudes, se enumera la lista de `RequestCultureProvider` en `RequestLocalizationOptions` y se usa el primer proveedor que puede determinar correctamente la referencia cultural de la solicitud. Los proveedores predeterminados proceden de la clase `RequestLocalizationOptions`:

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

La lista predeterminada va de más específico a menos específico. Más adelante en el artículo veremos cómo puede cambiar el orden e incluso agregar un proveedor de referencia cultural personalizado. Si ninguno de los proveedores puede determinar la referencia cultural de la solicitud, se usa `DefaultRequestCulture`.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

Algunas aplicaciones usarán una cadena de consulta para establecer <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1>. Para las aplicaciones que usan el método de cookie o de encabezado Accept-Language, es útil agregar una cadena de consulta a la dirección URL para depurar y probar el código. De forma predeterminada, `QueryStringRequestCultureProvider` está registrado como primer proveedor de localización en la lista `RequestCultureProvider`. Debe pasar los parámetros de cadena de consulta `culture` y `ui-culture`. En el ejemplo siguiente, la referencia cultural específica (idioma y región) se establece en español (México):

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

Si solo pasa uno de los dos parámetros (`culture` o `ui-culture`), el proveedor de la cadena de consulta usará el valor que usted ha pasado para establecer ambos valores. Por ejemplo, si solo establece la referencia cultural, se establecerán `Culture` y `UICulture`:

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a>CookieRequestCultureProvider

Las aplicaciones de producción suelen proporcionar un mecanismo para establecer la referencia cultural con la cookie de la referencia cultural de ASP.NET Core. Use el método `MakeCookieValue` para crear una cookie.

`CookieRequestCultureProvider` `DefaultCookieName` devuelve el nombre de cookie predeterminado usado para realizar un seguimiento de la información de la referencia cultural preferida del usuario. El nombre de cookie predeterminado es `.AspNetCore.Culture`.

El formato de la cookie es `c=%LANGCODE%|uic=%LANGCODE%`, donde `c` es `Culture` y `uic` es `UICulture`, por ejemplo:

```
c=en-UK|uic=en-US
```

Si solo especifica uno de los dos valores, ya sea la información de la referencia cultural o la referencia cultural de la interfaz de usuario, la referencia cultural especificada se usará tanto para la información de la referencia cultural como para la referencia cultural de la interfaz de usuario.

### <a name="the-accept-language-http-header"></a>Encabezado HTTP Accept-Language

El [encabezado Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) se puede establecer en la mayoría de los exploradores y está diseñado inicialmente para especificar el idioma del usuario. Este valor indica qué debe enviar el explorador o qué ha heredado del sistema operativo subyacente. El encabezado HTTP Accept-Language de una solicitud del explorador no es un método infalible para detectar el idioma preferido del usuario (vea [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php) [Establecer las preferencias de idioma en un explorador]). Una aplicación de producción debe ofrecer al usuario una manera de personalizar su opción de referencia cultural.

### <a name="set-the-accept-language-http-header-in-ie"></a>Establecer el encabezado HTTP Accept-Language en Internet Explorer

1. En el icono de engranaje, pulse **Opciones de Internet**.

1. Haga clic en **Lenguajes**.

   ![Opciones de Internet](localization/_static/lang.png)

1. Haga clic en **Establecer preferencias de idioma**.

1. Haga clic en **Agregar un idioma**.

1. Agregue el idioma.

1. Haga clic en el idioma y, después, en **Subir**.

### <a name="the-content-language-http-header"></a>El encabezado HTTP Content-Language

El encabezado de entidad [Content-Language](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Language):

* Se usa para describir los lenguajes destinados a la audiencia.
* Permite a un usuario diferenciar según el propio idioma preferido de los usuarios.

Los encabezados de entidad se usan en las solicitudes y respuestas HTTP.

Se puede agregar el encabezado `Content-Language` mediante el establecimiento de la propiedad `ApplyCurrentCultureToResponseHeaders`.

La adición del encabezado `Content-Language`:

* Permite que RequestLocalizationMiddleware establezca el encabezado `Content-Language` con `CurrentUICulture`.
* Elimina la necesidad de establecer el encabezado de respuesta `Content-Language` explícitamente.

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a>Usar un proveedor personalizado

Imagine que quiere permitir que los clientes almacenen su idioma y su referencia cultural en las bases de datos. Puede escribir un proveedor para que busque estos valores para el usuario. En el código siguiente se muestra cómo agregar un proveedor personalizado:

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

Use `RequestLocalizationOptions` para agregar o quitar proveedores de localización.

### <a name="set-the-culture-programmatically"></a>Establecer la referencia cultural mediante programación

Este proyecto **Localization.StarterWeb** de ejemplo de [GitHub](https://github.com/aspnet/entropy) contiene una interfaz de usuario para establecer el valor `Culture`. El archivo *Views/Shared/_SelectLanguagePartial.cshtml* le permite seleccionar la referencia cultural de la lista de referencias culturales admitidas:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

El archivo *Views/Shared/_SelectLanguagePartial.cshtml* se agrega a la sección `footer` del archivo de diseño para que esté disponible para todas las vistas:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

El método `SetLanguage` establece la cookie de la referencia cultural.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

No se puede conectar el archivo *_SelectLanguagePartial.cshtml* con código de ejemplo para este proyecto. El proyecto **Localization.StarterWeb** de [GitHub](https://github.com/aspnet/entropy) tiene código para enviar `RequestLocalizationOptions` a una vista parcial de Razor a través del contenedor de [inserción de dependencias](dependency-injection.md).

## <a name="model-binding-route-data-and-query-strings"></a>Datos de ruta y cadenas de consulta de enlace de modelos

Vea [Comportamiento de globalización de los datos de ruta de enlace de modelos y las cadenas de consulta](xref:mvc/models/model-binding#glob).

## <a name="globalization-and-localization-terms"></a>Términos relacionados con la globalización y la localización

Para localizar una aplicación también es necesario contar con unos conocimientos básicos sobre los juegos de caracteres pertinentes que se usan en el desarrollo de software moderno y sobre los problemas asociados. Aunque todos los equipos almacenan texto como números (códigos), cada sistema almacena el mismo texto con números diferentes. El proceso de localización consiste en traducir la interfaz de usuario (IU) de la aplicación a una referencia cultural o configuración regional específica.

La [localizabilidad](/dotnet/standard/globalization-localization/localizability-review) es un proceso intermedio para comprobar que una aplicación globalizada está preparada para la localización.

El formato [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) para el nombre de la referencia cultural es `<languagecode2>-<country/regioncode2>`, donde `<languagecode2>` es el código de idioma y `<country/regioncode2>` es el código de la referencia cultural secundaria. Por ejemplo, `es-CL` para español (Chile), `en-US` para inglés (Estados Unidos) y `en-AU` para inglés (Australia). [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) es una combinación de un código de referencia cultural ISO 639 de dos letras en minúsculas asociado con un idioma y un código de referencia cultural secundaria ISO 3166 de dos letras en mayúsculas asociado con un país o región. Vea <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.

"Internacionalización" suele abreviarse como "I18N". La abreviatura toma la primera y la última letra de la palabra, y el número de letras que hay entre ellas, es decir, el 18 representa el número de letras que hay entre la "I" inicial y la "N" final. Lo mismo se puede decir de "globalización" (G11N) y "localización" (L10N).

Términos:

* Globalización (G11N): es el proceso de hacer que una aplicación admita diferentes idiomas y regiones.
* Localización (L10N): es el proceso de personalizar una aplicación para un idioma y región determinados.
* Internacionalización (I18N): hace referencia a la globalización y la localización.
* Referencia cultural: es un idioma y, opcionalmente, una región.
* Referencia cultural neutra: se trata de una referencia cultural que tiene un idioma especificado, pero no una región. (por ejemplo, "en" y "es").
* Referencia cultural específica: es una referencia cultural que tiene un idioma y una región especificados (por ejemplo, "en-US", "en-GB" y "es-CL").
* Referencia cultural principal: se trata de la referencia cultural neutra que contiene una referencia cultural específica. (por ejemplo, "en" es la referencia cultural principal de "en-US" y "en-GB").
* Configuración regional: la configuración regional es lo mismo que la referencia cultural.

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* [Proyecto Localization.StarterWeb](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) usado en el artículo
* [Globalización y localización de aplicaciones .NET](/dotnet/standard/globalization-localization/index)
* [Recursos en archivos .resx](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Kit de herramientas de aplicaciones multilingüe de Microsoft](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [Localización y genéricos](http://hishambinateya.com/localization-and-generics)

::: moniker-end
