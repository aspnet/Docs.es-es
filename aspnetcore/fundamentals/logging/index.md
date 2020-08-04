---
title: Registros en .NET Core y ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo usar la plataforma de registro proporcionada por el paquete NuGet Microsoft.Extensions.Logging.
ms.author: riande
ms.custom: mvc
ms.date: 6/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: a4962c3132c60b68cb2d4b5a97e9b082aba15d15
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2020
ms.locfileid: "87330770"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>Registros en .NET Core y ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Kirk Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) y [Rick Anderson](https://twitter.com/RickAndMSFT)

.NET Core es compatible con una API de registro que funciona con una gran variedad de proveedores de registro integrados y de terceros. En este artículo se muestra cómo usar las API de registro con proveedores integrados.

La mayoría de los ejemplos de código que se muestran en este artículo son de aplicaciones de ASP.NET Core. Las partes específicas de registro de estos fragmentos de código son válidas para cualquier aplicación de .NET Core que use el [host genérico](xref:fundamentals/host/generic-host). Las plantillas de aplicación Web de ASP.NET Core usan el host genérico.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([cómo descargarlo](xref:index#how-to-download-a-sample))

<a name="lp"></a>

## <a name="logging-providers"></a>Proveedores de registro

Los proveedores de registro almacenan los registros, a excepción del proveedor `Console`, que los muestra. Por ejemplo, el proveedor de Azure Application Insights almacena los registros en Azure Application Insights. Se pueden habilitar varios proveedores.

Las plantillas de aplicación web de ASP.NET Core predeterminadas:

* usan el [host genérico](xref:fundamentals/host/generic-host);
* llaman a <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, que agrega los siguientes proveedores de registro:
  * [Consola](#console)
  * [Depurar](#debug)
  * [EventSource](#event-source)
  * [EventLog](#welog): Solo Windows

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_TemplateCode&highlight=9)]

En el código anterior se muestra la clase `Program` creada con las plantillas de aplicación web de ASP.NET Core. En las siguientes secciones se proporcionan ejemplos basados en las plantillas de aplicación web de ASP.NET Core, que usan el host genérico. Las [aplicaciones de consola que no son de host](#nhca) se explican más adelante en este documento.

Para invalidar el conjunto predeterminado de proveedores de registro agregados por `Host.CreateDefaultBuilder`, llame a `ClearProviders` y agregue los proveedores de registro necesarios. Por ejemplo, el código siguiente:

* Llama a <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> para quitar todas las instancias de <xref:Microsoft.Extensions.Logging.ILoggerProvider> del generador.
* Agrega el proveedor de registro [Console](#console).

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_AddProvider&highlight=5-6)]

Para otros proveedores, vea:

* [Proveedores de registro integrados](#bilp)
* [Proveedores de registro de terceros](#third-party-logging-providers)

## <a name="create-logs"></a>Creación de registros

Para crear registros, use un objeto <xref:Microsoft.Extensions.Logging.ILogger%601> desde la [inserción de dependencias](xref:fundamentals/dependency-injection) (DI):

En el ejemplo siguiente:

* Crea un registrador, `ILogger<AboutModel>`, que utiliza una *categoría* de registro del nombre completo del tipo `AboutModel`. La categoría de registro es una cadena que está asociada con cada registro.
* Llama a <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> para realizar el registro en el nivel de `Information`. El *nivel* de registro indica la gravedad del evento registrado.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=5,14)]

Los [niveles](#log-level) y las [categorías](#log-category) se explican con más detalle posteriormente en este artículo.

Para obtener información sobre Blazor, consulte [Creación de registros en Blazor y Blazor WebAssembly](#clib) en este documento.

[Creación de registros en Main y Startup](#clms) muestra cómo crear registros en `Main` y `Startup`.

## <a name="configure-logging"></a>registro

La configuración de registros suele proporcionarla la sección `Logging` de los archivos *appsettings*.`{Environment}` *.json*. El siguiente archivo *appsettings.Development.json* se genera mediante las plantillas de aplicación web de ASP.NET Core:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Development.json)]

En el código JSON anterior:

* Se especifican las categorías `"Default"`, `"Microsoft"` y `"Microsoft.Hosting.Lifetime"`.
* La categoría `"Microsoft"` se aplica a todas las categorías que comienzan por `"Microsoft"`. Por ejemplo, esta configuración se aplica a la categoría `"Microsoft.AspNetCore.Routing.EndpointMiddleware"`.
* La categoría `"Microsoft"` registra en el nivel de registro `Warning` y superiores.
* La categoría `"Microsoft.Hosting.Lifetime"` es más específica que la categoría `"Microsoft"`, por lo que la categoría `"Microsoft.Hosting.Lifetime"` registra en el nivel de registro "Information" y superiores.
* No se especifica un proveedor de registro específico, por lo que `LogLevel` se aplica a todos los proveedores de registro habilitados, excepto [Windows EventLog](#welog).

La propiedad `Logging` puede tener <xref:Microsoft.Extensions.Logging.LogLevel> y registrar propiedades del proveedor de registro. `LogLevel` especifica el [nivel](#log-level) mínimo que se va a registrar para las categorías seleccionadas. En el código JSON anterior, se especifican los niveles de registro `Information` y `Warning`. `LogLevel` indica la gravedad del registro y los valores están entre 0 y 6:

`Trace` = 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5 y `None` = 6.

Cuando se especifica `LogLevel`, el registro está habilitado para los mensajes tanto en el nivel especificado como en los superiores. En el código JSON anterior, se registra la categoría `Default` para `Information` y los niveles posteriores. Por ejemplo, se registran los mensajes `Information`, `Warning`, `Error` y `Critical`. Si no se especifica `LogLevel`, el nivel predeterminado del registro es `Information`. Para obtener más información, consulte [Niveles de registro](#llvl).

Una propiedad de proveedor puede especificar una propiedad de `LogLevel`. `LogLevel` en un proveedor especifica los niveles que se van a registrar para ese proveedor, e invalida la configuración de registro que no es de proveedor. Fíjese en el siguiente archivo *appsettings.json*:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Prod2.json)]

La configuración de `Logging.{providername}.LogLevel` invalida la configuración de `Logging.LogLevel`. En el código JSON anterior, el nivel de registro predeterminado del proveedor `Debug` se establece en `Information`:

`Logging:Debug:LogLevel:Default:Information`

La configuración anterior especifica el nivel de registro `Information` para cada categoría de `Logging:Debug:`, excepto `Microsoft.Hosting`. Cuando se muestra una categoría específica, esa categoría invalida la categoría predeterminada. En el JSON anterior, las categorías de `Logging:Debug:LogLevel` `"Microsoft.Hosting"` y `"Default"` invalidan la configuración de `Logging:LogLevel`

Se puede especificar el nivel de registro mínimo para:

* Proveedores específicos:  Por ejemplo, `Logging:EventSource:LogLevel:Default:Information`.
* Categorías específicas: Por ejemplo, `Logging:LogLevel:Microsoft:Warning`.
* Todos los proveedores y todas las categorías: `Logging:LogLevel:Default:Warning`

Los registros situados por debajo del nivel mínimo:

* no se pasan proveedor;
* no se registran ni se muestran.

Para suprimir todos los registros, especifique [LogLevel.None](xref:Microsoft.Extensions.Logging.LogLevel). `LogLevel.None` tiene un valor de 6, que es mayor que `LogLevel.Critical` (5).

Si un proveedor admite [ámbitos de registro](#logscopes), `IncludeScopes` indica si están habilitados. Para obtener más información, consulte [Ámbitos de registro](#logscopes).

El siguiente archivo *appSettings.json* contiene todos los proveedores habilitados de forma predeterminada:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Production.json)]

En el ejemplo anterior:

* Las categorías y los niveles no son valores sugeridos. El objetivo del ejemplo es mostrar todos los proveedores predeterminados.
* La configuración de `Logging.{providername}.LogLevel` invalida la configuración de `Logging.LogLevel`. Por ejemplo, el nivel de `Debug.LogLevel.Default` invalida el nivel de `LogLevel.Default`.
* Se usa cada *alias* de proveedor predeterminado. Cada proveedor define un *alias* que se puede utilizar en la configuración en lugar del nombre de tipo completo. Los alias de proveedores integrados son los siguientes:
  * Consola
  * Depuración
  * EventSource
  * EventLog
  * AzureAppServicesFile
  * AzureAppServicesBlob
  * ApplicationInsights

## <a name="set-log-level-by-command-line-environment-variables-and-other-configuration"></a>Establecimiento del nivel de registro mediante la línea de comandos, las variables de entorno y otra configuración

El nivel de registro se puede establecer con cualquiera de los [proveedores de configuración](xref:fundamentals/configuration/index). 

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Los siguientes comandos:

* establecen la clave de entorno `Logging:LogLevel:Microsoft` en un valor de `Information` en Windows;
* prueban la configuración cuando use una aplicación creada con las plantillas de aplicación web de ASP.NET Core. El comando `dotnet run` debe ejecutarse en el directorio del proyecto después de usar `set`.

```cmd
set Logging__LogLevel__Microsoft=Information
dotnet run
```

La configuración del entorno anterior:

* solo se establece en procesos iniciados desde la ventana de comandos en la que se establecieron;
* no podrá ser leída por los exploradores que se inician con Visual Studio.

El siguiente comando [setx](/windows-server/administration/windows-commands/setx) también establece la clave y el valor de entorno en Windows. A diferencia de `set`, la configuración de `setx` se conserva. El modificador `/M` establece la variable en el entorno del sistema. Si no se usa `/M`, se establece una variable de entorno de usuario.

```cmd
setx Logging__LogLevel__Microsoft=Information /M
```

En [Azure App Service](https://azure.microsoft.com/services/app-service/), seleccione **Nueva configuración de la aplicación** en la página **Configuración > Configuración**. Los ajustes de configuración de Azure App Service:

* Se cifran en reposo y se transmiten a través de un canal cifrado.
* Se exponen como variables de entorno.

Para más información, consulte [Aplicaciones de Azure: Invalidación de la configuración de la aplicación mediante Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

Para obtener más información sobre cómo establecer los valores de configuración de ASP.NET Core mediante variables de entorno, vea [Variables de entorno](xref:fundamentals/configuration/index#environment-variables). Para obtener información sobre el uso de otros orígenes de configuración, como la línea de comandos, Azure Key Vault, Azure App Configuration, otros formatos de archivo, etc., vea <xref:fundamentals/configuration/index>.

## <a name="how-filtering-rules-are-applied"></a>Cómo se aplican las reglas de filtro

Cuando se crea un objeto <xref:Microsoft.Extensions.Logging.ILogger%601>, el objeto <xref:Microsoft.Extensions.Logging.ILoggerFactory> selecciona una sola regla por proveedor para aplicar a ese registrador. Todos los mensajes escritos por una instancia `ILogger` se filtran según las reglas seleccionadas. De las reglas disponibles, se selecciona la más específica para cada par de categoría y proveedor.

Cuando se crea un `ILogger` para una categoría determinada, se usa el algoritmo siguiente para cada proveedor:

* Se seleccionan todas las reglas que coinciden con el proveedor o su alias. Si no se encuentra ninguna coincidencia, se seleccionan todas las reglas con un proveedor vacío.
* Del resultado del paso anterior, se seleccionan las reglas con el prefijo de categoría coincidente más largo. Si no se encuentra ninguna coincidencia, se seleccionan todas las reglas que no especifican una categoría.
* Si se seleccionan varias reglas, se toma la **última**.
* Si no se selecciona ninguna regla, se usa `MinimumLevel`.

<a name="dnrvs"></a>

## <a name="logging-output-from-dotnet-run-and-visual-studio"></a>Registro de la salida de dotnet run y Visual Studio

Se muestran los registros creados con los [proveedores de registro predeterminados](#lp):

* En Visual Studio
  * En la ventana de salida de Debug al depurar.
  * En la ventana de ASP.NET Core Web Server.
* En la ventana de la consola cuando la aplicación se ejecuta con `dotnet run`.

Los registros que comienzan por categorías de "Microsoft" pertenecen al código de marco de ASP.NET Core. ASP.NET Core y el código de la aplicación usan la misma API y los mismos proveedores de registro.

<a name="lcat"></a>

## <a name="log-category"></a>Categoría de registro

Cuando se crea un objeto `ILogger`, se especifica una *categoría*. Esa categoría se incluye con cada mensaje de registro creado por esa instancia de `ILogger`. La cadena de categoría es arbitraria, pero la convención es usar el nombre de clase. Por ejemplo, en un controlador, el nombre podría ser `"TodoApi.Controllers.TodoController"`. Las aplicaciones web de ASP.NET Core usan `ILogger<T>` para obtener automáticamente una instancia de `ILogger` que utiliza el nombre de tipo completo de `T` como la categoría:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Privacy.cshtml.cs?name=snippet)]

Para especificar explícitamente la categoría, llame a `ILoggerFactory.CreateLogger`:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Contact.cshtml.cs?name=snippet)]

`ILogger<T>` es equivale a llamar a `CreateLogger` con el nombre de tipo completo de `T`.

<a name="llvl"></a>

## <a name="log-level"></a>Nivel de registro

En la tabla siguiente se enumeran los valores de <xref:Microsoft.Extensions.Logging.LogLevel>, el método de extensión `Log{LogLevel}` oportuno y el uso sugerido:

| LogLevel | Valor | Método | Descripción |
| -------- | ----- | ------ | ----------- |
| [Seguimiento](xref:Microsoft.Extensions.Logging.LogLevel) | 0 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogTrace%2A> | Contienen los mensajes más detallados. Estos mensajes pueden contener datos confidenciales de la aplicación. Están deshabilitados de forma predeterminada y ***no*** se deben habilitar en un entorno de producción. |
| [Depurar](xref:Microsoft.Extensions.Logging.LogLevel) | 1 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug%2A> | Para depuración y desarrollo. Debido al elevado volumen, tenga precaución cuando lo use en producción. |
| [Información](xref:Microsoft.Extensions.Logging.LogLevel) | 2 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> | Realiza el seguimiento del flujo general de la aplicación. Puede tener un valor a largo plazo. |
| [Advertencia](xref:Microsoft.Extensions.Logging.LogLevel) | 3 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> | Para eventos anómalos o inesperados. Normalmente incluye errores o estados que no provocan un error en la aplicación. |
| [Error](xref:Microsoft.Extensions.Logging.LogLevel) | 4 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> | Para los errores y excepciones que no se pueden controlar. Estos mensajes indican un error en la operación o solicitud actual, no un error de toda la aplicación. |
| [Critical)](xref:Microsoft.Extensions.Logging.LogLevel) (Crítico) | 5 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogCritical%2A> | Para los errores que requieren atención inmediata. Ejemplos: escenarios de pérdida de datos, espacio en disco insuficiente. |
| [Ninguno](xref:Microsoft.Extensions.Logging.LogLevel) | 6 | | Especifica que una categoría de registro no debe escribir ningún mensaje. |

En la tabla anterior, `LogLevel` aparece de menor a mayor gravedad.

El primer parámetro del método [Log](xref:Microsoft.Extensions.Logging.LoggerExtensions), <xref:Microsoft.Extensions.Logging.LogLevel>, indica la gravedad del registro. En lugar de llamar a `Log(LogLevel, ...)`, la mayoría de los desarrolladores llaman a los métodos de extensión [Log{LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions). Los métodos de extensión `Log{LogLevel}` [llaman al método Log y especifican el LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs). Por ejemplo, las dos llamadas de registro siguientes son funcionalmente equivalentes y generan el mismo registro:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet0&highlight=6-7)]

`MyLogEvents.TestItem` es el identificador del evento. `MyLogEvents` forma parte de la aplicación de ejemplo y se muestra en la sección [Log event ID](#leid) (id. de evento de registro).

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

El siguiente código crea los registros `Information` y `Warning`:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

En el código anterior, el primer parámetro de `Log{LogLevel}`, `MyLogEvents.GetItem`, es el [identificador de evento de registro](#leid). El segundo parámetro es una plantilla de mensaje con marcadores de posición para los valores de argumento proporcionados por el resto de parámetros de método. Los parámetros de método se explican detalladamente en la sección [Plantilla de mensaje](#lmt) más adelante en este documento.

Llame al método `Log{LogLevel}` apropiado para controlar la cantidad de salida de registro que se escribe en un determinado medio de almacenamiento. Por ejemplo:

* En producción:
  * El registro en los niveles `Trace` o `Information` genera un gran volumen de mensajes de registro detallados. Para controlar los costos y no superar los límites de almacenamiento de datos, registre los mensajes de nivel `Trace` a `Information` en un almacén de datos de alto volumen y bajo costo. Considere la posibilidad de limitar `Trace` y `Information` a categorías específicas.
  * El registro entre los niveles `Warning` y `Critical` debe generar pocos mensajes de registro.
    * Los costos y los límites de almacenamiento no suelen ser un problema.
    * Cuantos menos registros haya, mayor será la flexibilidad a la hora de elegir el almacén de datos.
* En desarrollo:
  * Establézcalo en `Warning`.
  * Agregue los mensajes `Trace` o`Information` al solucionar problemas. Para limitar la salida, establezca `Trace` o `Information` solo para las categorías que se están investigando.

ASP.NET Core escribe registros de eventos de marco. Por ejemplo, tomemos la salida del registro de:

* Una aplicación de Razor Pages creada con las plantillas de ASP.NET Core.
* Registro establecido en `Logging:Console:LogLevel:Microsoft:Information`.
* Navegación a la página Privacy (privacidad):

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/Privacy
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint '/Privacy'
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[3]
      Route matched with {page = "/Privacy"}. Executing page /Privacy
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[101]
      Executing handler method DefaultRP.Pages.PrivacyModel.OnGet - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[102]
      Executed handler method OnGet, returned result .
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[103]
      Executing an implicit handler method - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[104]
      Executed an implicit handler method, returned result Microsoft.AspNetCore.Mvc.RazorPages.PageResult.
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[4]
      Executed page /Privacy in 74.5188ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint '/Privacy'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 149.3023ms 200 text/html; charset=utf-8
```

El siguiente JSON establece `Logging:Console:LogLevel:Microsoft:Information`:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<a name="leid"></a>

## <a name="log-event-id"></a>Id. de evento del registro

Cada registro se puede especificar un *id. de evento*. La aplicación de ejemplo usa la clase `MyLogEvents` para definir los identificadores de evento:

<!-- Review: to bad there is no way to use an enum for event ID's -->
[!code-csharp[](index/samples/3.x/TodoApiDTO/Models/MyLogEvents.cs?name=snippet_LoggingEvents)]

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

Un id. de evento asocia un conjunto de eventos. Por ejemplo, todos los registros relacionados con la presentación de una lista de elementos en una página podrían ser 1001.

El proveedor de registro puede almacenar el id. de evento en un campo de identificador, en el mensaje de registro o no almacenarlo. El proveedor de depuración no muestra los identificadores de evento. El proveedor de consola muestra los identificadores de evento entre corchetes después de la categoría:

```console
info: TodoApi.Controllers.TodoItemsController[1002]
      Getting item 1
warn: TodoApi.Controllers.TodoItemsController[4000]
      Get(1) NOT FOUND
```

Algunos proveedores de registro almacenan el identificador de evento en un campo, lo que permite filtrar por el id.

<a name="lmt"></a>

## <a name="log-message-template"></a>Plantilla de mensaje de registro
<!-- Review, Each log API uses a message template. -->
Cada API de registro usa una plantilla de mensaje. La plantilla de mensaje puede contener marcadores de posición para los que se proporcionan argumentos. Use los nombres de los marcadores de posición, no números.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

El orden de los marcadores de posición, no sus nombres, determina qué parámetros se usan para proporcionar sus valores. En el código siguiente, los nombres de parámetro están fuera de la secuencia en la plantilla de mensaje:

```csharp
string p1 = "param1";
string p2 = "param2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

El código anterior crea un mensaje de registro con los valores de parámetro en secuencia:

```text
Parameter values: param1, param2
```

Este enfoque permite a los proveedores de registro implementar [registro semántico o estructurado](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging). Los propios argumentos se pasan al sistema de registro, no solo a la plantilla de mensaje con formato. Esto permite a los proveedores de registro almacenar los valores de parámetro como campos. Por ejemplo, tomemos el siguiente método de registrador:

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Por ejemplo, al registrar en Azure Table Storage:

* Cada entidad de Azure Table puede tener propiedades `ID` y `RequestTime`.
* Las tablas con propiedades simplifican las consultas en los datos registrados. Por ejemplo, una consulta puede buscar todos los registros dentro de un intervalo `RequestTime` determinado sin necesidad de analizar el tiempo de espera del mensaje de texto.

## <a name="log-exceptions"></a>Registro de excepciones

Los métodos de registrador tienen sobrecargas que toman un parámetro de excepción:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Exp)]

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

El registro de excepciones es específico del proveedor.

### <a name="default-log-level"></a>Nivel de registro predeterminado

Si no se establece el nivel de registro predeterminado, su valor será `Information`.

Por ejemplo, considere la siguiente aplicación web:

* Creada con las plantillas de aplicación web de ASP.NET.
* *appsettings.json* y *appsettings.Development.json* eliminados o con el nombre cambiado.

Con la configuración anterior, al navegar a la página de privacidad o de inicio, se generan muchos mensajes de `Trace`, `Debug` y `Information` con `Microsoft` en el nombre de la categoría.

El código siguiente establece el nivel de registro predeterminado cuando este no se establece en la configuración:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_MinLevel&highlight=10)]

<!-- review required: I say this a couple times -->
En general, los niveles de registro se deben especificar en la configuración y no en el código.

### <a name="filter-function"></a>Función de filtro

Se invoca una función de filtro para todos los proveedores y las categorías que no tienen reglas asignadas mediante configuración o código:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterFunction)]

El código anterior muestra los registros de la consola cuando la categoría contiene `Controller` o `Microsoft` y el nivel de registro es `Information` o superior.

En general, los niveles de registro se deben especificar en la configuración y no en el código.

## <a name="aspnet-core-and-ef-core-categories"></a>Categorías ASP.NET Core y EF Core

La tabla siguiente conti9ene algunas categorías usadas por ASP.NET Core y Entity Framework Core, con notas sobre los registros:

| Categoría                            | Notas |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | Diagnósticos generales de ASP.NET Core. |
| Microsoft.AspNetCore.DataProtection | Qué claves se tuvieron en cuenta, encontraron y usaron. |
| Microsoft.AspNetCore.HostFiltering  | Hosts permitidos. |
| Microsoft.AspNetCore.Hosting        | Cuánto tiempo tardaron en completarse las solicitudes HTTP y a qué hora comenzaron. Qué ensamblados de inicio de hospedaje se cargaron. |
| Microsoft.AspNetCore.Mvc            | Diagnósticos de MVC y Razor. Enlace de modelos, ejecución de filtros, compilación de vistas y selección de acciones. |
| Microsoft.AspNetCore.Routing        | Información de coincidencia de ruta. |
| Microsoft.AspNetCore.Server         | Inicio y detención de conexión y mantener las respuestas activas. Información de certificado HTTPS. |
| Microsoft.AspNetCore.StaticFiles    | Archivos servidos. |
| Microsoft.EntityFrameworkCore       | Diagnósticos generales de Entity Framework Core. Actividad y la configuración de bases de datos, detección de cambios y migraciones. |

Para ver más categorías en la ventana de la consola, establezca **appsettings.Development.json** en lo siguiente:

[!code-json[](index/samples/3.x/MyMain/appsettings.Trace.json)]

<!-- Review: What other providers support scopes? Console is not generally used in staging/production  -->

<a name="logscopes"></a>

## <a name="log-scopes"></a>Ámbitos de registro

 Un *ámbito* puede agrupar un conjunto de operaciones lógicas. Esta agrupación se puede utilizar para adjuntar los mismos datos para cada registro que se crea como parte de un conjunto. Por ejemplo, cada registro creado como parte del procesamiento de una transacción puede incluir el identificador de dicha transacción.

Un ámbito:

* es un tipo <xref:System.IDisposable> devuelto por el método <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*>;
* se mantiene hasta que se elimina.

Los siguientes proveedores admiten ámbitos:

* `Console`
* [AzureAppServicesFile y AzureAppServicesBlob](xref:Microsoft.Extensions.Logging.AzureAppServices.BatchingLoggerOptions.IncludeScopes)

Use un ámbito encapsulando las llamadas de registrador en un bloque `using`:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Scopes)]

El JSON siguiente habilita ámbitos para el proveedor de la consola:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Scopes.json)]

El código siguiente permite ámbitos para el proveedor de la consola:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_Scopes)]

En general, el registro se debe especificar en la configuración y no en el código.

<a name="bilp"></a>

## <a name="built-in-logging-providers"></a>Proveedores de registro integrados

ASP.NET Core incluye los siguientes proveedores de registro:

* [Consola](#console)
* [Depurar](#debug)
* [EventSource](#event-source)
* [EventLog](#welog)
* [AzureAppServicesFile y AzureAppServicesBlob](#azure-app-service)
* [ApplicationInsights](#azure-application-insights)

Para obtener información sobre `stdout` y el registro de depuración con el módulo ASP.NET Core, consulte <xref:test/troubleshoot-azure-iis> y <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

### <a name="console"></a>Consola

El proveedor `Console` registra la salida en la consola. Para obtener más información sobre cómo ver los registros de `Console` en desarrollo, consulte [Registro de la salida de dotnet run y Visual Studio](#dnrvs).

### <a name="debug"></a>Depuración

El proveedor `Debug` escribe la salida del registro mediante la clase [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug). Las llamadas a `System.Diagnostics.Debug.WriteLine` escriben en el proveedor `Debug`.

En Linux, la ubicación del registro del proveedor `Debug` depende de la distribución y puede ser una de las siguientes:

* */var/log/message*
* */var/log/syslog*

### <a name="event-source"></a>Origen de eventos

El proveedor `EventSource` escribe en un origen de eventos multiplataforma con el nombre `Microsoft-Extensions-Logging`. En Windows, el proveedor utiliza [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).

#### <a name="dotnet-trace-tooling"></a>herramienta de seguimiento de dotnet

La herramienta de [seguimiento de dotnet](/dotnet/core/diagnostics/dotnet-trace) es una herramienta global de CLI multiplataforma que permite la recopilación de seguimientos de .NET Core de un proceso en ejecución. La herramienta recopila datos del proveedor <xref:Microsoft.Extensions.Logging.EventSource> mediante un <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.

Vea [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) para obtener instrucciones de instalación.

Use la herramienta de seguimiento de dotnet para recopilar un seguimiento de una aplicación:

1. Ejecute la aplicación con el comando `dotnet run`.
1. Determine el identificador del proceso (PID) de la aplicación .NET Core:
   * En Windows, siga uno de estos procedimientos:
     * Administrador de tareas (Ctrl + Alt + Supr)
     * [Comando TaskList](/windows-server/administration/windows-commands/tasklist)
     * [Comando de PowerShell Get-Process](/powershell/module/microsoft.powershell.management/get-process)
   * En Linux, use el [comando pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).

   Busque el PID del proceso que tenga el mismo nombre que el ensamblado de la aplicación.

1. Ejecute el comando `dotnet trace`.

   Sintaxis general del comando:

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   Al usar un shell de comandos de PowerShell, incluya el valor `--providers` entre comillas simples (`'`):

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   En plataformas que no sean Windows, agregue la opción `-f speedscope` para cambiar el formato del archivo de seguimiento de salida a `speedscope`.

   | Palabra clave | Descripción |
   | :-----: | ----------- |
   | 1       | Registre los eventos meta sobre el elemento `LoggingEventSource`. No registre eventos de `ILogger`). |
   | 2       | Activa el evento `Message` cuando se llama a `ILogger.Log()`. Proporciona la información mediante programación (sin formato). |
   | 4       | Activa el evento `FormatMessage` cuando se llama a `ILogger.Log()`. Proporciona la versión de cadena con formato de la información. |
   | 8       | Activa el evento `MessageJson` cuando se llama a `ILogger.Log()`. Proporciona una representación JSON de los argumentos. |

   | Nivel de evento | Descripción     |
   | :---------: | --------------- |
   | 0           | `LogAlways`     |
   | 1           | `Critical`      |
   | 2           | `Error`         |
   | 3           | `Warning`       |
   | 4           | `Informational` |
   | 5           | `Verbose`       |

   Las entradas `FilterSpecs` de `{Logger Category}` y `{Event Level}` representan condiciones de filtrado de registros adicionales. Separe las entradas `FilterSpecs` con un punto y coma (`;`).

   Ejemplo de uso de un shell de comandos de Windows (**no** hay comillas simples alrededor del valor `--providers`):

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   El comando anterior activa lo siguiente:

   * Registrador de origen del evento para generar cadenas con formato (`4`) de los errores (`2`).
   * Registro `Microsoft.AspNetCore.Hosting` en el nivel de registro `Informational` (`4`).

1. Presione la tecla Entrar o Ctrl + C para detener las herramientas de seguimiento de dotnet.

   El seguimiento se guarda con el nombre *trace.nettrace* en la carpeta en la que se ejecuta el comando `dotnet trace`.

1. Abra el seguimiento con [Perfview](#perfview). Abra el archivo *trace.nettrace* y explore los eventos de seguimiento.

Si la aplicación no compila el host con `CreateDefaultBuilder`, agregue el [proveedor de origen del evento](#event-source-provider) a la configuración de registro de la aplicación.

Para obtener más información, consulte:

* [Seguimiento de la utilidad de análisis de rendimiento (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (documentación de .NET Core)
* [Seguimiento de la utilidad de análisis de rendimiento (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (documentación del repositorio de GitHub sobre diagnóstico y dotnet)
* [Clase LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (Explorador de API de .NET)
* <xref:System.Diagnostics.Tracing.EventLevel>
* [Origen de referencia de LoggingEventSource (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): Para obtener el origen de referencia de otra versión, cambie la rama a `release/{Version}`, donde `{Version}` corresponde a la versión de ASP.NET Core deseada.
* [Perfview](#perfview): es útil para ver los seguimientos del origen de eventos.

#### <a name="perfview"></a>Perfview

Use la [utilidad PerfView](https://github.com/Microsoft/perfview) para recopilar y ver los registros. Hay otras herramientas para ver los registros ETW, pero PerfView proporciona la mejor experiencia para trabajar con los eventos ETW emitidos por ASP.NET Core.

Para configurar PerfView para la recopilación de eventos registrados por este proveedor, agregue la cadena `*Microsoft-Extensions-Logging` a la lista **Proveedores adicionales**. No olvide el símbolo `*` al principio de la cadena.

<a name="welog"></a>

### <a name="windows-eventlog"></a>Registro de eventos de Windows

El proveedor `EventLog` envía la salida del registro al Registro de eventos de Windows. A diferencia de otros proveedores, el proveedor `EventLog` ***no*** hereda la configuración de no proveedor predeterminada. Si no se especifican valores de registro de `EventLog`, [el valor predeterminado será LogLevel.Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).

Para registrar eventos inferiores a <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType>, establezca el nivel de registro de forma explícita. En el ejemplo siguiente se establece el nivel de registro predeterminado del registro de eventos en <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

Las [sobrecargas de AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) pueden pasar <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>. Si es `null` o no se especifica, se usa la siguiente configuración predeterminada:

* `LogName`: "Application"
* `SourceName`: ".NET Runtime"
* `MachineName`: se usa el nombre del equipo local.

En el código siguiente se cambia el valor predeterminado de `SourceName` (`".NET Runtime"`) por `MyLogs`:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippetEventLog)]

### <a name="azure-app-service"></a>Azure App Service

El paquete de proveedor [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) escribe los registros en archivos de texto en el sistema de archivos de una aplicación de Azure App Service y en [Blob Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) en una cuenta de Azure Storage.

El paquete del proveedor no se incluye en el marco compartido. Para usar el proveedor, agregue el paquete del proveedor al proyecto.

Para configurar las opciones de proveedor, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> y <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, tal y como se muestra en el ejemplo siguiente:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_AzLogOptions)]

Cuando se implementa en Azure App Service, la aplicación usa la configuración de la sección [Registros de App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) de la página **App Service** de Azure Portal. Cuando se actualiza la configuración siguiente, los cambios se aplican de inmediato sin necesidad de reiniciar ni de volver a implementar la aplicación.

* **Registro de la aplicación (sistema de archivos)**
* **Registro de la aplicación (blob)**

La ubicación predeterminada de los archivos de registro es la carpeta *D:\\home\\LogFiles\\Application* y el nombre de archivo predeterminado es *diagnostics-aaaammdd.txt*. El límite de tamaño de archivo predeterminado es 10 MB, y el número máximo predeterminado de archivos que se conservan es 2. El nombre de blob predeterminado es *{nombre-de-la-aplicación}{marca de tiempo}/aaaa/mm/dd/hh/{guid}-applicationLog.txt*.

El proveedor solo realiza registros cuando el proyecto se ejecuta en el entorno de Azure.

#### <a name="azure-log-streaming"></a>Secuencias de registro de Azure

El streaming de registro de Azure permiten ver la actividad de registro en tiempo real desde:

* El servidor de aplicaciones
* El servidor web
* Error del seguimiento de solicitudes

Para configurar las secuencias de registro de Azure:

* Desplácese a la página **Registros de App Service** desde la página del portal de la aplicación.
* Establezca **Registro de la aplicación (sistema de archivos)** en **Activado**.
* Elija el **Nivel** de registro. Esta configuración solo se aplica al streaming de registro de Azure.

Desplácese a la página **Secuencia de registro** para ver los registros. Los mensajes que se registran lo hacen con la interfaz `ILogger`.

### <a name="azure-application-insights"></a>Azure Application Insights

El paquete de proveedor [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) escribe los registros en [Azure Application Insights](/azure/azure-monitor/app/cloudservices). Application Insights es un servicio que supervisa una aplicación web y proporciona herramientas para consultar y analizar los datos de telemetría. Si usa este proveedor, puede consultar y analizar los registros mediante las herramientas de Application Insights.

El proveedor de registro se incluye como dependencia de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), que es el paquete que proporciona toda la telemetría disponible para ASP.NET Core. Si usa este paquete, no tiene que instalar el proveedor de paquete.

El paquete [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) es para ASP.NET 4.x, no para ASP.NET Core.

Para obtener más información, vea los siguientes recursos:

* [Información general de Application Insights](/azure/application-insights/app-insights-overview)
* [Application Insights para aplicaciones de ASP.NET Core](/azure/azure-monitor/app/asp-net-core): comience aquí si quiere implementar la variedad completa de telemetría de Application Insights junto con el registro.
* [ApplicationInsightsLoggerProvider para los registros de .NET Core ILogger](/azure/azure-monitor/app/ilogger): comience aquí si quiere implementar el proveedor de registro sin el resto de la telemetría de Application Insights.
* [Adaptadores de registro de Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)
* [Instalación, configuración e inicialización del SDK de Application Insights](/learn/modules/instrument-web-app-code-with-application-insights): tutorial interactivo en el sitio de Microsoft Learn.

## <a name="third-party-logging-providers"></a>Proveedores de registro de terceros

Plataformas de registro de terceros que funcionan con ASP.NET Core:

* [elmah.io](https://elmah.io/) ([repositorio de GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repositorio de GitHub](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([repositorio de GitHub](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([repositorio de GitHub](https://github.com/catalingavan/KissLog-net))
* [Log4Net](https://logging.apache.org/log4net/) ([repositorio de GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([repositorio de GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/) ([repositorio de GitHub](https://github.com/NLog/NLog.Extensions.Logging))
* [PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([repositorio de GitHub](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))
* [Sentry](https://sentry.io/welcome/) ([repositorio de GitHub](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([repositorio de GitHub](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repositorio de GitHub](https://github.com/googleapis/google-cloud-dotnet))

Algunas plataformas de terceros pueden realizar [registro semántico, también conocido como registro estructurado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

El uso de una plataforma de terceros es similar al uso de uno de los proveedores integrados:

1. Agregue un paquete NuGet al proyecto.
1. Llame a un método de extensión `ILoggerFactory` proporcionado por el marco de registro.

Para más información, vea la documentación de cada proveedor. Microsoft no admite los proveedores de registro de terceros.

<a name="nhca"></a>

## <a name="non-host-console-app"></a>Aplicación de consola que no es de host

Para ver un ejemplo de cómo usar el host genérico en una aplicación de consola que no sea web, vea el archivo *Program.cs* de la [aplicación de ejemplo de tareas en segundo plano](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).

El código de registro para las aplicaciones sin un host genérico es distinto en la forma en que se [agregan los proveedores](#add-providers) y [se crean los registradores](#create-logs). 

### <a name="logging-providers"></a>Proveedores de registro

En una aplicación de consola que no sea de host, llame al método de extensión `Add{provider name}` del proveedor al crear un elemento `LoggerFactory`:

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11-12)]

### <a name="create-logs"></a>Creación de registros

Para crear registros, use un objeto de <xref:Microsoft.Extensions.Logging.ILogger%601>. Use `LoggerFactory` para crear `ILogger`.

En el ejemplo siguiente, se crea un registrador con `LoggingConsoleApp.Program` como la categoría.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=14)]

En los siguientes ejemplos de ASP.NET Core, el registrador se usa para crear registros con nivel de `Information`. El *nivel* de registro indica la gravedad del evento registrado.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=15)]

Los [niveles](#log-level) y las [categorías](#log-category) se explican con más detalle en este documento.

<a name="lhc"></a>

## <a name="log-during-host-construction"></a>Registro durante la construcción del host

No se admite directamente el registro durante la construcción del host. Sin embargo, se puede usar un registrador independiente. En el ejemplo siguiente, se usa un registrador [Serilog](https://serilog.net/) para registrarse en `CreateHostBuilder`. `AddSerilog` usa la configuración estática especificada en `Log.Logger`:

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

<a name="csdi"></a>

## <a name="configure-a-service-that-depends-on-ilogger"></a>Configuración de un servicio que dependa de ILogger

La inserción del constructor de un registrador en `Startup` funciona en versiones anteriores de ASP.NET Core, ya que se crea un contenedor de inserción de dependencias independiente para el host de web. Para conocer por qué solo se crea un contenedor para el host genérico, vea el [anuncio de cambios importantes](https://github.com/aspnet/Announcements/issues/353).

Para configurar un servicio que dependa de `ILogger<T>`, use la inserción de constructores o proporcione un método Factory. Usar un Factory Method es la opción recomendada si no tiene otra alternativa. Por ejemplo, tomemos un servicio que necesita una instancia de `ILogger<T>` proporcionada por DI:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup2.cs?name=snippet_ConfigureServices&highlight=6-10)]

El código resaltado anterior es un elemento [Func](/dotnet/api/system.func-2) que se ejecuta la primera vez que el contenedor de inserción de dependencias necesita crear una instancia de `MyService`. Puede acceder a cualquiera de los servicios registrados de esta forma.

<a name="clms"></a>

## <a name="create-logs-in-main"></a>Creación de registros en Main

El código siguiente registra en `Main` mediante la obtención de una instancia de `ILogger` de inserción de dependencias después de compilar el host:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_LogProgram)]

### <a name="create-logs-in-startup"></a>Creación de registros durante el inicio

El código siguiente escribe registros en `Startup.Configure`:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Startup.cs?name=snippet_Configure)]

No se admite la escritura de registros antes de la finalización del contenedor de inserción de dependencias configurado en el método `Startup.ConfigureServices`:

* No se admite la inyección del registrador en el constructor `Startup`.
* No se admite la inyección del registrador en la signatura del método `Startup.ConfigureServices`.

El motivo de esta restricción es que los registros dependen de la inserción de dependencias y de la configuración, que a su vez depende de la inserción de dependencias. El contenedor de inserción de dependencias no se configura hasta que finaliza `ConfigureServices`.

Para obtener información sobre la configuración de un servicio que depende de `ILogger<T>` o sobre el motivo de que la inserción de constructor de un registrador en `Startup` funcionase en versiones anteriores, vea [Configuración de un servicio que depende de ILogger](#csdi)

### <a name="no-asynchronous-logger-methods"></a>No hay métodos de registrador asincrónicos

El registro debe ser tan rápido que no merezca la pena el costo de rendimiento del código asincrónico. Si el almacén de datos de registro es lento, no escriba directamente en él. Considere la posibilidad de escribir primero los mensajes de registro en un almacén rápido y, después, moverlos al almacén lento. Por ejemplo, al iniciar sesión en SQL Server, no lo haga directamente en un método `Log`, ya que los métodos `Log` son sincrónicos. En su lugar, agregue sincrónicamente mensajes de registro a una cola en memoria y haga que un trabajo en segundo plano extraiga los mensajes de la cola para realizar el trabajo asincrónico de insertar datos en SQL Server. Para obtener más información, vea [este](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problema de GitHub.

<a name="clib"></a>

## <a name="change-log-levels-in-a-running-app"></a>Cambio de los niveles de registro en una aplicación en ejecución

La API de registro no incluye un escenario que permita cambiar los niveles de registro mientras se ejecuta una aplicación. No obstante, algunos proveedores de configuración pueden volver a cargar la configuración, lo que tiene efecto inmediato en la configuración del registro. Por ejemplo, el [Proveedor de configuración de archivo](xref:fundamentals/configuration/index#file-configuration-provider) vuelve a cargar la configuración de registro de forma predeterminada. Si se cambia la configuración en el código mientras se ejecuta una aplicación, la aplicación puede llamar a [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) para actualizar la configuración de registro de la aplicación.

## <a name="ilogger-and-iloggerfactory"></a>ILogger e ILoggerFactory

Las implementaciones y las interfaces de <xref:Microsoft.Extensions.Logging.ILogger%601> y <xref:Microsoft.Extensions.Logging.ILoggerFactory> se incluyen en el SDK de .NET Core. También están disponibles en los siguientes paquetes NuGet:  

* Las interfaces están en [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).
* Las implementaciones predeterminadas se encuentran en [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

<!-- review. Why would you want to hard code filtering rules in code? -->
<a name="fric"></a>

## <a name="apply-log-filter-rules-in-code"></a>Aplicación de reglas de filtro en el código

El método preferido para establecer las reglas de filtro de registro es mediante la [Configuración](xref:fundamentals/configuration/index).

En el siguiente ejemplo se muestra cómo registrar reglas de filtro en el código:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterInCode)]

`logging.AddFilter("System", LogLevel.Debug)` especifica la categoría `System` y el nivel de registro `Debug`. El filtro se aplica a todos los proveedores porque no se ha configurado un proveedor específico.

`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)` especifica:

* El proveedor de registro `Debug`.
* Nivel de registro `Information` y superiores.
* Todas las categorías que empiezan con `"Microsoft"`.

## <a name="create-a-custom-logger"></a>Creación de un registrador personalizado

Para agregar un registrador personalizado, agregue <xref:Microsoft.Extensions.Logging.ILoggerProvider> con <xref:Microsoft.Extensions.Logging.ILoggerFactory>:

```csharp
public void Configure(
    IApplicationBuilder app,
    IWebHostEnvironment env,
    ILoggerFactory loggerFactory)
{
    loggerFactory.AddProvider(new CustomLoggerProvider(new CustomLoggerConfiguration()));
```

`ILoggerProvider` crea una o más instancias de `ILogger`. El marco de trabajo usa las instancias de `ILogger` para registrar la información.

### <a name="sample-custom-logger-configuration"></a>Configuración de registrador personalizado de ejemplo

Ejemplo:

* Está diseñado para ser un ejemplo muy básico que establece el color de la consola de registro por el identificador de evento y el nivel de registro. Normalmente, los registradores no cambian por identificador de evento y no son específicos del nivel de registro.
* Crea diferentes entradas de consola de color por nivel de registro e identificador de evento con el siguiente tipo de configuración:

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerConfiguration.cs?name=snippet)]

El código anterior establece el nivel predeterminado en `Warning` y el color en `Yellow`. Si `EventId` se establece en 0, se registrarán todos los eventos.

### <a name="create-the-custom-logger"></a>Creación del registrador personalizado

El nombre de la categoría de implementación `ILogger` es normalmente el origen del registro. Por ejemplo, el tipo en el que se crea el registrador:

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLogger.cs?name=snippet)]

El código anterior:

* Crea una instancia del registrador por nombre de categoría.
* Comprueba `logLevel == _config.LogLevel` en `IsEnabled`, por lo que cada `logLevel` tiene un registrador único. Por lo general, los registradores también se deben habilitar para todos los niveles de registro superiores:

```csharp
public bool IsEnabled(LogLevel logLevel)
{
    return logLevel >= _config.LogLevel;
}
```

### <a name="create-the-custom-loggerprovider"></a>Creación del proveedor LoggerProvider personalizado

`LoggerProvider` es la clase que crea las instancias del registrador. Tal vez no sea necesario crear una instancia del registrador por categoría, pero esto tiene sentido para algunos registradores, como NLog o log4net. Al hacerlo, también se podrán elegir diferentes destinos de salida de registro por categoría, en caso necesario:

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerProvider.cs?name=snippet)]

En el código anterior, <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> crea una única instancia de `ColorConsoleLogger` por nombre de categoría y la almacena en [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2).

### <a name="usage-and-registration-of-the-custom-logger"></a>Uso y registro del registrador personalizado

Registre el registrador en `Startup.Configure`:

[!code-csharp[](index/samples/3.x/CustomLogger/Startup.cs?name=snippet)]

Para el código anterior, proporcione al menos un método de extensión para `ILoggerFactory`:

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerExtensions.cs?name=snippet)]

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/logging/loggermessage>
* Los errores de registro deben crearse en el repositorio [github.com/dotnet/runtime/](https://github.com/dotnet/runtime/issues).
* <xref:blazor/fundamentals/logging>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Tom Dykstra](https://github.com/tdykstra) y [Steve Smith](https://ardalis.com/)

.NET Core es compatible con una API de registro que funciona con una gran variedad de proveedores de registro integrados y de terceros. En este artículo se muestra cómo usar las API de registro con proveedores integrados.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Incorporación de proveedores

Un proveedor de registro muestra o almacena registros. Por ejemplo, el proveedor de consola muestra los registros en la consola y el proveedor de Azure Application Insights los almacena en Azure Application Insights. Los registros se pueden enviar a varios destinos mediante la incorporación de varios proveedores.

Para usar un proveedor, llame al método de extensión `Add{provider name}` del proveedor en *Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

El código anterior requiere referencias a `Microsoft.Extensions.Logging` y `Microsoft.Extensions.Configuration`.

La plantilla de proyecto predeterminada llama a <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, que agrega los siguientes proveedores de registro:

* Consola
* Depuración
* EventSource (a partir de ASP.NET Core 2.2)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

Si usa `CreateDefaultBuilder`, puede reemplazar los proveedores predeterminados por sus propios valores. Llame a <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> y agregue los proveedores que desee.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

Obtenga más información sobre los [proveedores de registro integrados](#built-in-logging-providers) y los [proveedores de registro de terceros](#third-party-logging-providers) más adelante en el artículo.

## <a name="create-logs"></a>Creación de registros

Para crear registros, use un objeto de <xref:Microsoft.Extensions.Logging.ILogger%601>. En una aplicación web o servicio hospedado, obtenga un elemento `ILogger` de la inserción de dependencias. En aplicaciones de consola que no sean de host, use `LoggerFactory` para crear un elemento `ILogger`.

En el ejemplo siguiente de ASP.NET Core, se crea un registrador con `TodoApiSample.Pages.AboutModel` como la categoría. La *categoría* de registro es una cadena que está asociada con cada registro. La instancia de `ILogger<T>` proporcionada por la inserción de dependencias genera registros que tienen el nombre completo del tipo `T` como la categoría. 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

En los siguientes ejemplos de aplicación de consola y ASP.NET Core, el registrador se usa para crear registros con `Information` como el nivel. El *nivel* de registro indica la gravedad del evento registrado.

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

Los [niveles](#log-level) y las [categorías](#log-category) se explican detalladamente más adelante en este artículo.

### <a name="create-logs-in-startup"></a>Creación de registros durante el inicio

Para escribir registros en la clase `Startup`, incluya un parámetro `ILogger` en la signatura de construcción:

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a>Crear registros en la clase del programa

Para escribir registros la clase `Program`, obtenga una instancia `ILogger` de inserción de dependencias:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

No se admite directamente el registro durante la construcción del host. Sin embargo, se puede usar un registrador independiente. En el ejemplo siguiente, se usa un registrador [Serilog](https://serilog.net/) para registrarse en `CreateWebHostBuilder`. `AddSerilog` usa la configuración estática especificada en `Log.Logger`:

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="no-asynchronous-logger-methods"></a>No hay métodos de registrador asincrónicos

El registro debe ser tan rápido que no merezca la pena el costo de rendimiento del código asincrónico. Si el almacén de datos de registro es lento, no escriba directamente en él. Considere la posibilidad de escribir los mensajes de registro en un almacén rápido inicialmente y luego moverlos a la tienda lenta. Por ejemplo, si inicia sesión en SQL Server, no desea hacerlo directamente en un método `Log`, ya que los métodos `Log` son sincrónicos. En su lugar, agregue sincrónicamente mensajes de registro a una cola en memoria y haga que un trabajo en segundo plano extraiga los mensajes de la cola para realizar el trabajo asincrónico de insertar datos en SQL Server. Para obtener más información, vea [este](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problema de GitHub.

## <a name="configuration"></a>Configuración

Uno o varios proveedores de configuración proporcionan la configuración del proveedor de registro:

* Formatos de archivo (INI, JSON y XML).
* Argumentos de la línea de comandos.
* Variables de entorno.
* Objetos de .NET en memoria.
* El almacenamiento de [administrador secreto](xref:security/app-secrets) sin cifrar.
* Un almacén de usuario cifrado, como [Azure Key Vault](xref:security/key-vault-configuration).
* Proveedores personalizados (instalados o creados).

Por ejemplo, la sección `Logging` de archivos de configuración de aplicación suele proporcionar la configuración de registro. En el ejemplo siguiente se muestra el contenido de un archivo *appsettings.Development.json* típico:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

La propiedad `Logging` puede tener `LogLevel` y propiedades del proveedor de registro (se muestra la consola).

La propiedad `LogLevel` bajo `Logging` especifica el [nivel](#log-level) mínimo que se va a registrar para las categorías seleccionadas. En el ejemplo, las categorías `System` y `Microsoft` se registran en el nivel `Information` y todas las demás se registran en el nivel `Debug`.

Otras propiedades bajo `Logging` especifican proveedores de registro. El ejemplo es para el proveedor de consola. Si un proveedor admite [ámbitos de registro](#log-scopes), `IncludeScopes` indica si están habilitados. Una propiedad de proveedor (como `Console` en el ejemplo) también puede especificar una propiedad `LogLevel`. `LogLevel` en un proveedor especifica niveles de registro para ese proveedor.

Si los niveles se especifican en `Logging.{providername}.LogLevel`, invalidan todo lo establecido en `Logging.LogLevel`. Por ejemplo, tomemos el siguiente código JSON:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

En el JSON anterior, la configuración del proveedor `Console` invalida el nivel de registro anterior (predeterminado).

La API de registro no incluye un escenario que permita cambiar los niveles de registro mientras se ejecuta una aplicación. No obstante, algunos proveedores de configuración pueden volver a cargar la configuración, lo que tiene efecto inmediato en la configuración del registro. Por ejemplo, [FileConfigurationProvider](xref:fundamentals/configuration/index#file-configuration-provider) —agregado por `CreateDefaultBuilder` para leer los archivos de configuración— vuelve a cargar la configuración de registro de forma predeterminada. Si se cambia la configuración en el código mientras se ejecuta una aplicación, la aplicación puede llamar a [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) para actualizar la configuración de registro de la aplicación.

Para obtener información sobre cómo implementar proveedores de configuración, consulte <xref:fundamentals/configuration/index>.

## <a name="sample-logging-output"></a>Salida de registro de ejemplo

Con el código de ejemplo que se muestra en la sección anterior, los registros aparecen en la consola cuando la aplicación se ejecuta desde la línea de comandos. Este es un ejemplo de salida de la consola:

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

Los registros anteriores se generaron mediante la realización de una solicitud HTTP GET a la aplicación de ejemplo en `http://localhost:5000/api/todo/0`.

Este es un ejemplo de los mismos registros tal y como aparecen en la ventana de depuración cuando se ejecuta la aplicación de ejemplo en Visual Studio:

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

Los registros creados por las llamadas de `ILogger` se muestran en la sección anterior, empezando por “TodoApi”. Los registros que comienzan por categorías de "Microsoft" son del código de marco de ASP.NET Core. ASP.NET Core y el código de la aplicación usan la misma API y los mismos proveedores de registro.

En el resto de este artículo se explican algunos detalles y opciones para el registro.

## <a name="nuget-packages"></a>Paquetes NuGet

Las interfaces `ILogger` e `ILoggerFactory` se encuentran en [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), y sus implementaciones predeterminadas en [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

## <a name="log-category"></a>Categoría de registro

Cuando se crea un objeto `ILogger`, se ha especificado una *categoría* para él. Esa categoría se incluye con cada mensaje de registro creado por esa instancia de `ILogger`. La categoría puede ser cualquier cadena, pero la convención es usar el nombre de clase, como "TodoApi.Controllers.TodoController".

Use `ILogger<T>` para obtener una instancia `ILogger` que utiliza el nombre de tipo completo de `T` como la categoría:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

Para especificar explícitamente la categoría, llame a `ILoggerFactory.CreateLogger`:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

`ILogger<T>` es equivale a llamar a `CreateLogger` con el nombre de tipo completo de `T`.

## <a name="log-level"></a>Nivel de registro

Cara registro especifica un valor <xref:Microsoft.Extensions.Logging.LogLevel>. El nivel de registro indica la gravedad o importancia. Por ejemplo, podría escribir un registro `Information` cuando un método termina con normalidad y un registro `Warning` cuando un método devuelve un código de estado *404 No encontrado*.

El siguiente código crea los registros `Information` y `Warning`:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

En el código anterior, los parámetros `MyLogEvents.GetItem` y `MyLogEvents.GetItemNotFound` son el [id. de evento de registro](#log-event-id). El segundo parámetro es una plantilla de mensaje con marcadores de posición para los valores de argumento proporcionados por el resto de parámetros de método. Los parámetros de método se explican detalladamente en la [sección Plantilla de mensaje de registro](#lmt) en este artículo.

Los métodos de registro que incluyen el nivel en el nombre del método (por ejemplo `LogInformation` y `LogWarning`) son [métodos de extensión para ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions). Estos métodos llaman a un método `Log` que toma un parámetro `LogLevel`. Puede llamar directamente al método `Log` en lugar de a uno de estos métodos de extensión, pero la sintaxis es relativamente complicada. Para más información, vea la <xref:Microsoft.Extensions.Logging.ILogger> y el [código fuente de las extensiones de registrador](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).

ASP.NET Core define los niveles de registro siguientes, que aquí se ordenan de menor a mayor gravedad.

* Seguimiento = 0

  Para información que normalmente solo es útil para la depuración. Estos mensajes pueden contener datos confidenciales de la aplicación, por lo que no deben habilitarse en un entorno de producción. *Deshabilitado de forma predeterminada.*

* Depurar = 1

  Para información que puede ser útil para el desarrollo y la depuración. Ejemplo: `Entering method Configure with flag set to true.` Habilite los registros de nivel `Debug` en producción cuando esté solucionando un problema, debido al elevado volumen de registros.

* Información = 2

  Para realizar el seguimiento del flujo general de la aplicación. Estos registros suelen tener algún valor a largo plazo. Ejemplo: `Request received for path /api/todo`

* Advertencia = 3

  Para los eventos anómalos o inesperados en el flujo de la aplicación. Estos pueden incluir errores u otras condiciones que no hacen que la aplicación se detenga, pero que puede que sea necesario investigar. Las excepciones controladas son un lugar común para usar el nivel de registro `Warning`. Ejemplo: `FileNotFoundException for file quotes.txt.`

* Error = 4

  Para los errores y excepciones que no se pueden controlar. Estos mensajes indican un error en la actividad u operación actual (por ejemplo, la solicitud HTTP actual), no un error de toda la aplicación. Mensaje de registro de ejemplo: `Cannot insert record due to duplicate key violation.`

* Crítico = 5

  Para los errores que requieren atención inmediata. Ejemplos: escenarios de pérdida de datos, espacio en disco insuficiente.

Use el nivel de registro para controlar la cantidad de salida del registro que se escribe en un medio de almacenamiento determinado o ventana de presentación. Por ejemplo:

* En producción:
  * El registro en los niveles `Trace` a `Information` genera un gran volumen de mensajes de registro detallados. Para controlar los costos y no superar los límites de almacenamiento de datos, registre los mensajes de nivel `Trace` a `Information` en un almacén de datos de alto volumen y bajo costo.
  * El registro en los niveles `Warning` a `Critical` normalmente produce menos mensajes de registro y de menor tamaño. Por lo tanto, los costos y los límites de almacenamiento no suelen ser un problema, lo que da lugar a una mayor flexibilidad a la hora de elegir el almacén de datos.
* Durante el desarrollo:
  * Registre los mensajes `Warning` a `Critical` en la consola.
  * Agregue los mensajes `Trace` a `Information` al solucionar problemas.

En la sección [Filtrado del registro](#log-filtering) de este artículo se explica cómo controlar los niveles de registro que controla un proveedor.

ASP.NET Core escribe registros de eventos de marco. En los ejemplos de registro anteriores de este artículo se excluyeron los registros por debajo del nivel `Information`, por lo que no se crearon los registros de nivel `Debug` o `Trace`. Este es un ejemplo de registros de consola generados mediante la ejecución de la aplicación de ejemplo configurada para mostrar registros `Debug`:

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a>Id. de evento del registro

Cada registro se puede especificar un *id. de evento*. La aplicación de ejemplo lo hace mediante una clase `LoggingEvents` definida de forma local:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

Un id. de evento asocia un conjunto de eventos. Por ejemplo, todos los registros relacionados con la presentación de una lista de elementos en una página podrían ser 1001.

El proveedor de registro puede almacenar el id. de evento en un campo de identificador, en el mensaje de registro o no almacenarlo. El proveedor de depuración no muestra los identificadores de evento. El proveedor de consola muestra los identificadores de evento entre corchetes después de la categoría:

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>Plantilla de mensaje de registro

Cada registro especifica una plantilla de mensaje. La plantilla de mensaje puede contener marcadores de posición para los que se proporcionan argumentos. Use los nombres de los marcadores de posición, no números.

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

El orden de los marcadores de posición, no sus nombres, determina qué parámetros se usan para proporcionar sus valores. En el código siguiente, tenga en cuenta que los nombres de parámetro están fuera de la secuencia en la plantilla de mensaje:

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Este código crea un mensaje de registro con los valores de parámetro en secuencia:

```text
Parameter values: parm1, parm2
```

La plataforma de registro funciona de esta manera para que los proveedores de registro puedan implementar [el registro semántico, también conocido como registro estructurado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging). Los propios argumentos se pasan al sistema de registro, no solo a la plantilla de mensaje con formato. Esta información permite a los proveedores de registro almacenar los valores de parámetro como campos. Por ejemplo, suponga que las llamadas del método del registrador tiene el aspecto siguiente:

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Si envía los registros a Azure Table Storage, cada entidad de Azure Table puede tener propiedades `ID` y `RequestTime`, lo que simplifica las consultas en los datos de registro. Una consulta puede buscar todos los registros dentro de un intervalo `RequestTime` determinado sin analizar el tiempo de espera del mensaje de texto.

## <a name="logging-exceptions"></a>Excepciones de registro

Los métodos de registrador tienen sobrecargas que le permiten pasar una excepción, como en el ejemplo siguiente:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

Cada proveedor controla la información de la excepción de maneras diferentes. Este es un ejemplo de salida del proveedor de depuración del código mostrado anteriormente.

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>Filtrado del registro

Puede especificar un nivel de registro mínimo para un proveedor y una categoría específicos, o para todos los proveedores o todas las categorías. Los registros por debajo del nivel mínimo no se pasan a ese proveedor, de modo que no se muestran o almacenan.

Para suprimir todos los registros, especifique `LogLevel.None` como el nivel de registro mínimo. El valor entero de `LogLevel.None` es 6, que es superior a `LogLevel.Critical` (5).

### <a name="create-filter-rules-in-configuration"></a>Creación de reglas de filtro en la configuración

El código de la plantilla de proyecto llama a `CreateDefaultBuilder` para configurar el registro para los proveedores de Console, Debug y EventSource (ASP.NET Core 2.2 o versiones posteriores). El método `CreateDefaultBuilder` configura el registro para buscar la configuración en una sección de `Logging`, como se explica [anteriormente en este artículo](#configuration).

Los datos de configuración especifican niveles de registro mínimo por proveedor y categoría, como en el ejemplo siguiente:

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

Este archivo JSON crea seis reglas de filtro, una para el proveedor de depuración, cuatro para el proveedor de la consola y una para todos los proveedores. Se elige una sola regla para cada proveedor cuando se crea un objeto `ILogger`.

### <a name="filter-rules-in-code"></a>Reglas de filtro en el código

En el siguiente ejemplo se muestra cómo registrar reglas de filtro en el código:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

El segundo `AddFilter` especifica el proveedor de depuración mediante su nombre de tipo. El primer `AddFilter` se aplica a todos los proveedores, dado que no especifica un tipo de proveedor.

### <a name="how-filtering-rules-are-applied"></a>Cómo se aplican las reglas de filtro

Los datos de configuración y el código de `AddFilter` que se muestran en los ejemplos anteriores crean las reglas que se muestran en la tabla siguiente. Las seis primeras proceden del ejemplo de configuración y las dos últimas del ejemplo de código.

| número | Proveedor      | Categorías que comienzan por...          | Nivel de registro mínimo |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Depuración         | Todas las categorías                          | Información       |
| 2      | Consola       | Microsoft.AspNetCore.Mvc.Razor.Internal | Advertencia           |
| 3      | Consola       | Microsoft.AspNetCore.Mvc.Razor.Razor    | Depuración             |
| 4      | Consola       | Microsoft.AspNetCore.Mvc.Razor          | Error             |
| 5      | Consola       | Todas las categorías                          | Información       |
| 6      | Todos los proveedores | Todas las categorías                          | Depuración             |
| 7      | Todos los proveedores | Sistema                                  | Depuración             |
| 8      | Depuración         | Microsoft                               | Seguimiento             |

Cuando se crea un objeto `ILogger`, el objeto `ILoggerFactory` selecciona una sola regla por proveedor para aplicar a ese registrador. Todos los mensajes escritos por una instancia `ILogger` se filtran según las reglas seleccionadas. De las reglas disponibles se selecciona la más específica posible para cada par de categoría y proveedor.

Cuando se crea un `ILogger` para una categoría determinada, se usa el algoritmo siguiente para cada proveedor:

* Se seleccionan todas las reglas que coinciden con el proveedor o su alias. Si no se encuentra ninguna coincidencia, se seleccionan todas las reglas con un proveedor vacío.
* Del resultado del paso anterior, se seleccionan las reglas con el prefijo de categoría coincidente más largo. Si no se encuentra ninguna coincidencia, se seleccionan todas las reglas que no especifican una categoría.
* Si se seleccionan varias reglas, se toma la **última**.
* Si no se selecciona ninguna regla, se usa `MinimumLevel`.

Con la lista de reglas anterior, supongamos que crea un objeto `ILogger` para la categoría "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":

* Para el proveedor de depuración, se aplican las reglas 1, 6 y 8. La regla 8 es la más específica, por lo que se selecciona.
* Para el proveedor de la consola, se aplican las reglas 3, 4, 5 y 6. La regla 3 es la más específica.

La instancia `ILogger` resultante envía los registros de nivel `Trace` y superiores al proveedor de depuración. Los registros de nivel `Debug` y superiores se envían al proveedor de consola.

### <a name="provider-aliases"></a>Alias de proveedor

Cada proveedor define un *alias* que se puede utilizar en la configuración en lugar del nombre de tipo completo.  Para los proveedores integrados, use los alias siguientes:

* Consola
* Depuración
* EventSource
* EventLog
* TraceSource
* AzureAppServicesFile
* AzureAppServicesBlob
* ApplicationInsights

### <a name="default-minimum-level"></a>Nivel mínimo predeterminado

Hay una configuración de nivel mínimo que solo tiene efecto si no se aplica ninguna regla de configuración o código para un proveedor y una categoría determinados. En el ejemplo siguiente se muestra cómo establecer el nivel mínimo:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

Si no establece explícitamente el nivel mínimo, el valor predeterminado es `Information`, lo que significa que los registros `Trace` y `Debug` se omiten.

### <a name="filter-functions"></a>Funciones de filtro

Se invoca una función de filtro para todos los proveedores y categorías que no tienen reglas asignadas mediante configuración o código. El código de la función tiene acceso al tipo de proveedor, la categoría y el nivel de registro. Por ejemplo:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a>Niveles y categorías del sistema

Estas son algunas categorías que ASP.NET Core y Entity Framework Core usan, con notas sobre lo que los registros de espera de ellas:

| Categoría                            | Notas |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | Diagnósticos generales de ASP.NET Core. |
| Microsoft.AspNetCore.DataProtection | Qué claves se tuvieron en cuenta, encontraron y usaron. |
| Microsoft.AspNetCore.HostFiltering  | Hosts permitidos. |
| Microsoft.AspNetCore.Hosting        | Cuánto tiempo tardaron en completarse las solicitudes HTTP y a qué hora comenzaron. Qué ensamblados de inicio de hospedaje se cargaron. |
| Microsoft.AspNetCore.Mvc            | Diagnósticos de MVC y Razor. Enlace de modelos, ejecución de filtros, compilación de vistas y selección de acciones. |
| Microsoft.AspNetCore.Routing        | Información de coincidencia de ruta. |
| Microsoft.AspNetCore.Server         | Inicio y detención de conexión y mantener las respuestas activas. Información de certificado HTTPS. |
| Microsoft.AspNetCore.StaticFiles    | Archivos servidos. |
| Microsoft.EntityFrameworkCore       | Diagnósticos generales de Entity Framework Core. Actividad y la configuración de bases de datos, detección de cambios y migraciones. |

## <a name="log-scopes"></a>Ámbitos de registro

 Un *ámbito* puede agrupar un conjunto de operaciones lógicas. Esta agrupación se puede utilizar para adjuntar los mismos datos para cada registro que se crea como parte de un conjunto. Por ejemplo, cada registro creado como parte del procesamiento de una transacción puede incluir el identificador de dicha transacción.

Un ámbito es un tipo `IDisposable` devuelto por el método <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> y se conserva hasta que se elimina. Use un ámbito encapsulando las llamadas de registrador en un bloque `using`:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

El código siguiente permite ámbitos para el proveedor de la consola:

*Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> Es necesario configurar la opción del registrador de consola `IncludeScopes` para habilitar el registro basado en el ámbito.
>
> Para obtener información sobre la configuración, consulte la sección [Configuración](#configuration).

Cada mensaje de registro incluye la información de ámbito:

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>Proveedores de registro integrados

ASP.NET Core incluye los proveedores siguientes:

* [Consola](#console-provider)
* [Depurar](#debug-provider)
* [EventSource](#event-source-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

Para obtener información sobre stdout y el registro de depuración con el módulo ASP.NET Core, consulte <xref:test/troubleshoot-azure-iis> y <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

### <a name="console-provider"></a>Proveedor de la consola

El paquete de proveedor [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) envía la salida del registro a la consola. 

```csharp
logging.AddConsole();
```

Para ver una salida de registro de la consola, abra un símbolo del sistema en la carpeta del proyecto y ejecute este comando:

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a>Proveedor de depuración

El paquete de proveedor [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) escribe la salida del registro mediante la clase [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (llamadas a métodos `Debug.WriteLine`).

En Linux, este proveedor escribe registros en */var/log/message*.

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>Proveedor de origen del evento

El paquete del proveedor [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) escribe en una multiplataforma de origen del evento con el nombre `Microsoft-Extensions-Logging`. En Windows, el proveedor utiliza [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).

```csharp
logging.AddEventSourceLogger();
```

El proveedor de origen del evento se agrega automáticamente cuando se llama a `CreateDefaultBuilder` para compilar el host.

Use la [utilidad PerfView](https://github.com/Microsoft/perfview) para recopilar y ver los registros. Hay otras herramientas para ver los registros ETW, pero PerfView proporciona la mejor experiencia para trabajar con los eventos ETW emitidos por ASP.NET Core.

Para configurar PerfView para la recopilación de eventos registrados por este proveedor, agregue la cadena `*Microsoft-Extensions-Logging` a la lista **Proveedores adicionales**. (No olvide el asterisco al principio de la cadena).

![Proveedores adicionales de Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Proveedor EventLog de Windows

El paquete de proveedor [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) envía la salida del registro al Registro de eventos de Windows.

```csharp
logging.AddEventLog();
```

Las [sobrecargas de AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) permiten pasar <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>. Si es `null` o no se especifica, se usa la siguiente configuración predeterminada:

* `LogName`: "Application"
* `SourceName`: ".NET Runtime"
* `MachineName`: se usa el nombre del equipo local.

Los eventos se registran para el [Nivel de advertencia y superior](#log-level). En el ejemplo siguiente se establece el nivel de registro predeterminado del Registro de eventos en <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

### <a name="tracesource-provider"></a>Proveedor TraceSource

El paquete de proveedor [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa las bibliotecas y proveedores de <xref:System.Diagnostics.TraceSource>.

```csharp
logging.AddTraceSource(sourceSwitchName);
```

[Las sobrecargas de AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) permiten pasar un modificador de origen y un agente de escucha de seguimiento.

Para usar este proveedor, una aplicación debe ejecutarse en .NET Framework (en lugar de .NET Core). El proveedor puede enrutar mensajes a una variedad de [agentes de escucha](/dotnet/framework/debug-trace-profile/trace-listeners), como <xref:System.Diagnostics.TextWriterTraceListener> que se usa en la aplicación de ejemplo.

### <a name="azure-app-service-provider"></a>Proveedor Azure App Service

El paquete de proveedor [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) escribe los registros en archivos de texto en el sistema de archivos de una aplicación de Azure App Service y en [Blob Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) en una cuenta de Azure Storage.

```csharp
logging.AddAzureWebAppDiagnostics();
```

El paquete de proveedor no está incluido en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Si el destino es .NET Framework o hace referencia al metapaquete `Microsoft.AspNetCore.App`, agregue el paquete del proveedor al proyecto. 

Una sobrecarga <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> permite pasar <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>. El objeto de configuración puede invalidar la configuración predeterminada, como la plantilla de salida de registro, el nombre de blob y el límite de tamaño de archivo. (La *plantilla salida* es una plantilla de mensaje que se aplica a todos los registros además de que se proporciona con una llamada de método `ILogger`).

Al realizar una implementación en una aplicación de App Service, esta respeta la configuración de la sección [Registros de App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) de la página **App Service** de Azure Portal. Cuando se actualiza la configuración siguiente, los cambios se aplican de inmediato sin necesidad de reiniciar ni de volver a implementar la aplicación.

* **Registro de la aplicación (sistema de archivos)**
* **Registro de la aplicación (blob)**

La ubicación predeterminada de los archivos de registro es la carpeta *D:\\home\\LogFiles\\Application* y el nombre de archivo predeterminado es *diagnostics-aaaammdd.txt*. El límite de tamaño de archivo predeterminado es 10 MB, y el número máximo predeterminado de archivos que se conservan es 2. El nombre de blob predeterminado es *{nombre-de-la-aplicación}{marca de tiempo}/aaaa/mm/dd/hh/{guid}-applicationLog.txt*.

El proveedor solo funciona cuando el proyecto se ejecuta en el entorno de Azure. No tiene ningún efecto cuando el proyecto se ejecuta de manera local (no escribe en los archivos locales ni en el almacenamiento de desarrollo local de blobs).

#### <a name="azure-log-streaming"></a>Secuencias de registro de Azure

Las secuencias de registro de Azure permiten ver la actividad de registro en tiempo real desde:

* El servidor de aplicaciones
* El servidor web
* Error del seguimiento de solicitudes

Para configurar las secuencias de registro de Azure:

* Navegue hasta la página **Registros de App Service** desde la página de portal de la aplicación.
* Establezca **Registro de la aplicación (sistema de archivos)** en **Activado**.
* Elija el **Nivel** de registro. Este valor solo se aplica a las secuencias de registro de Azure, no a otros proveedores de registro de la aplicación.

Navegue hasta la página **Secuencia de registro** para consultar los mensajes de la aplicación. La aplicación los registra a través de la interfaz `ILogger`.

### <a name="azure-application-insights-trace-logging"></a>Registro de seguimiento de Azure Application Insights

El proveedor de paquete [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) escribe los registros en Azure Application Insights. Application Insights es un servicio que supervisa una aplicación web y proporciona herramientas para consultar y analizar los datos de telemetría. Si usa este proveedor, puede consultar y analizar los registros mediante las herramientas de Application Insights.

El proveedor de registro se incluye como dependencia de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), que es el paquete que proporciona toda la telemetría disponible para ASP.NET Core. Si usa este paquete, no tiene que instalar el proveedor de paquete.

No use el paquete [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)&mdash;que es para ASP.NET 4.x.

Para obtener más información, vea los siguientes recursos:

* [Información general de Application Insights](/azure/application-insights/app-insights-overview)
* [Application Insights para aplicaciones de ASP.NET Core](/azure/azure-monitor/app/asp-net-core): comience aquí si quiere implementar la variedad completa de telemetría de Application Insights junto con el registro.
* [ApplicationInsightsLoggerProvider para los registros de .NET Core ILogger](/azure/azure-monitor/app/ilogger): comience aquí si quiere implementar el proveedor de registro sin el resto de la telemetría de Application Insights.
* [Adaptadores de registro de Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)
* [Instalación, configuración e inicialización del SDK de Application Insights](/learn/modules/instrument-web-app-code-with-application-insights): tutorial interactivo en el sitio de Microsoft Learn.

## <a name="third-party-logging-providers"></a>Proveedores de registro de terceros

Plataformas de registro de terceros que funcionan con ASP.NET Core:

* [elmah.io](https://elmah.io/) ([repositorio de GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repositorio de GitHub](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([repositorio de GitHub](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([repositorio de GitHub](https://github.com/catalingavan/KissLog-net))
* [Log4Net](https://logging.apache.org/log4net/) ([repositorio de GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([repositorio de GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/) ([repositorio de GitHub](https://github.com/NLog/NLog.Extensions.Logging))
* [Sentry](https://sentry.io/welcome/) ([repositorio de GitHub](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([repositorio de GitHub](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repositorio de GitHub](https://github.com/googleapis/google-cloud-dotnet))

Algunas plataformas de terceros pueden realizar [registro semántico, también conocido como registro estructurado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

El uso de una plataforma de terceros es similar al uso de uno de los proveedores integrados:

1. Agregue un paquete NuGet al proyecto.
1. Llame a un método de extensión `ILoggerFactory` proporcionado por el marco de registro.

Para más información, vea la documentación de cada proveedor. Microsoft no admite los proveedores de registro de terceros.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
