---
title: Sesión en ASP.NET Core
author: rick-anderson
description: Descubra formas de conservar la sesión entre las solicitudes.
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/app-state
ms.openlocfilehash: 30123e043a7c152b5719af8092b2ab42a70d2787
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2020
ms.locfileid: "86407624"
---
# <a name="session-and-state-management-in-aspnet-core"></a>Administración del estado y la sesión en ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://twitter.com/serpent5) y [Diana LaRose](https://github.com/DianaLaRose)

HTTP es un protocolo sin estado. De forma predeterminada, las solicitudes HTTP son mensajes independientes que no conservan los valores de usuario. En este artículo se describen varios enfoques para conservar los datos de usuario entre las solicitudes.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Administración de estado

El estado se puede almacenar mediante varios enfoques. Cada enfoque se describe más adelante en este tema.

| Enfoque de almacenamiento | Mecanismo de almacenamiento |
| ---------------- | ----------------- |
| [Cookies](#cookies) | Cookies HTTP. Pueden incluir los datos almacenados mediante el código de aplicación del lado servidor. |
| [Estado de sesión](#session-state) | Cookies HTTP y código de aplicación del lado servidor |
| [TempData](#tempdata) | Cookies HTTP o estado de sesión |
| [Cadenas de consulta](#query-strings) | Cadenas de consulta HTTP |
| [Campos ocultos](#hidden-fields) | Campos de formularios HTTP |
| [HttpContext.Items](#httpcontextitems) | Código de aplicación del lado servidor |
| [Caché](#cache) | Código de aplicación del lado servidor |

## <a name="cookies"></a>Cookies

Las cookies almacenan datos de todas las solicitudes. Dado que las cookies se envían con cada solicitud, su tamaño debe reducirse al mínimo. Lo ideal es que en cada cookie se almacene un solo identificador con los datos almacenados por la aplicación. La mayoría de los exploradores restringen el tamaño de las cookies a 4096 bytes. Solo hay disponible un número limitado de cookies para cada dominio.

Como las cookies están expuestas a alteraciones, deben validarse por la aplicación. Los usuarios pueden eliminar las cookies y estas pueden caducar en los clientes. Pero las cookies generalmente son la forma más duradera de persistencia de datos en el cliente.

Las cookies suelen utilizarse para personalizar el contenido ofrecido a un usuario conocido. En la mayoría de los casos, el usuario solo se identifica y no se autentica. La cookie puede almacenar el nombre de usuario, el nombre de cuenta o el identificador único del usuario (por ejemplo, un GUID). Se puede usar la cookie para tener acceso a la configuración personalizada del usuario, como su color de fondo preferido del sitio web.

Consulte el [Reglamento general de protección de datos (RGPD) de la Unión Europea](https://ec.europa.eu/info/law/law-topic/data-protection) al emitir cookies y tratar con casos relativos a la privacidad. Para obtener más información, vea [Compatibilidad con el Reglamento general de protección de datos (RGPD) en ASP.NET Core](xref:security/gdpr).

## <a name="session-state"></a>Estado de sesión

El estado de sesión es un escenario de ASP.NET Core para el almacenamiento de datos de usuario mientras el usuario examina una aplicación web. El estado de sesión usa un almacén mantenido por la aplicación para conservar los datos en las solicitudes de un cliente. Los datos de sesión están respaldados por una memoria caché y se consideran datos efímeros. El sitio debería continuar funcionando correctamente sin los datos de sesión. Los datos críticos de aplicaciones deben almacenarse en la base de datos de usuario y almacenarse en caché en la sesión solo para optimizar el rendimiento.

La sesión no se admite en aplicaciones [SignalR](xref:signalr/index) porque un [SignalRconcentrador de ](xref:signalr/hubs) se podría ejecutar con independencia de un contexto HTTP. Por ejemplo, esto puede ocurrir cuando un concentrador mantiene abierta una solicitud de sondeo larga más allá de la duración del contexto HTTP de la solicitud.

Para mantener el estado de sesión, ASP.NET Core proporciona una cookie al cliente que contiene un identificador de sesión. El identificador de sesión de la cookie:

* Se envía a la aplicación con cada solicitud.
* Lo usa la aplicación para capturar los datos de la sesión.

El estado de sesión muestra los siguientes comportamientos:

* La cookie de sesión es específica de cada explorador. Las sesiones no se comparten entre los exploradores.
* Las cookies de sesión se eliminan cuando finaliza la sesión del explorador.
* Si se recibe una cookie de una sesión que ha expirado, se crea una nueva sesión que usa la misma cookie de sesión.
* No se conservan las sesiones vacías. La sesión debe tener al menos un conjunto de valores para que se conserve en todas las solicitudes. Cuando una sesión no se conserva, se genera un nuevo identificador de sesión para cada nueva solicitud.
* La aplicación conserva una sesión durante un tiempo limitado después de la última solicitud. La aplicación especifica un tiempo de espera de sesión o usa el valor predeterminado de 20 minutos. El estado de sesión es ideal para almacenar datos de usuario:
  * Que son específicos de una sesión determinada.
  * Que no necesitan conservarse de forma permanente en las sesiones.
* Los datos de sesión se eliminan cuando se llama a la implementación <xref:Microsoft.AspNetCore.Http.ISession.Clear%2A?displayProperty=nameWithType> o cuando expira la sesión.
* No hay ningún mecanismo predeterminado para informar al código de aplicación que se ha cerrado un explorador del cliente o cuando la cookie de sesión se elimina o caduca en el cliente.
* Las cookies de estado de sesión no están marcadas como esenciales de forma predeterminada. De este modo, el estado de sesión no es funcional a menos que el visitante del sitio permita el seguimiento. Para obtener más información, vea <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> No almacene datos confidenciales en un estado de sesión. El usuario podría no cerrar el explorador y borrar la cookie de sesión. Algunos exploradores mantienen las cookies de sesión válidas en las ventanas del explorador. Es posible que una sesión no esté restringida a un único usuario. Así pues, el siguiente usuario podría continuar examinando la aplicación con la misma cookie de sesión.

El proveedor de caché en memoria almacena datos de sesión en la memoria del servidor donde reside la aplicación. En un escenario de granja de servidores:

* Use *sesiones permanentes* para asociar cada sesión a una instancia de aplicación específica en un servidor individual. [Azure App Service](https://azure.microsoft.com/services/app-service/) usa [enrutamiento de solicitud de aplicaciones (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) para exigir sesiones permanentes de forma predeterminada. Pero las sesiones permanentes pueden afectar a la escalabilidad y complicar la actualización de las aplicaciones web. Un enfoque mejor consiste en usar una memoria caché distribuida de Redis o SQL Server, que no requiere sesiones permanentes. Para obtener más información, vea <xref:performance/caching/distributed>.
* La cookie de sesión se cifra mediante <xref:Microsoft.AspNetCore.DataProtection.IDataProtector>. La protección de datos debe configurarse correctamente para que lea las cookies de sesión en cada equipo. Para más información, vea <xref:security/data-protection/introduction> y [Proveedores de almacenamiento de claves](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Configurar el estado de sesión

El paquete [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/):

* Está incluido implícitamente en el marco.
* Proporciona middleware para administrar el estado de sesión.

Para habilitar el middleware de sesión, `Startup` debe contener:

* Cualquiera de las cachés de memoria <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>. La implementación de `IDistributedCache` se usa como una memoria auxiliar para la sesión. Para obtener más información, vea <xref:performance/caching/distributed>.
* Una llamada a <xref:Microsoft.Extensions.DependencyInjection.SessionServiceCollectionExtensions.AddSession%2A> en `ConfigureServices`.
* Una llamada a <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A> en `Configure`.

El código siguiente muestra cómo configurar el proveedor de sesión en memoria con una implementación en memoria de `IDistributedCache`:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup4.cs?name=snippet1&highlight=12-19,45)]

El código anterior establece un tiempo de expiración breve para simplificar la prueba.

El orden del middleware es importante.  Llame a `UseSession` después de llamar a `UseRouting` y antes de la llamada a `UseEndpoints`. Consulte [Orden del middleware](xref:fundamentals/middleware/index#order).

[HttpContext.Session](xref:Microsoft.AspNetCore.Http.HttpContext.Session) está disponible después de configurar el estado de sesión.

No se puede acceder a `HttpContext.Session` antes de llamar a `UseSession`.

No se puede crear una nueva sesión con una cookie de sesión nueva después de que la aplicación haya empezado a escribir en la secuencia de respuesta. La excepción se registra en el registro del servidor web y no se muestra en el explorador.

### <a name="load-session-state-asynchronously"></a>Cargar de forma asincrónica el estado de sesión

El proveedor de sesión predeterminado de ASP.NET Core carga de manera asincrónica registros de sesión del almacén de respaldo <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> subyacente solo si se llama al método <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync%2A?displayProperty=nameWithType> de forma explícita antes que a los métodos <xref:Microsoft.AspNetCore.Http.ISession.TryGetValue%2A>, <xref:Microsoft.AspNetCore.Http.ISession.Set%2A> o <xref:Microsoft.AspNetCore.Http.ISession.Remove%2A>. Si primero no se llama a `LoadAsync`, el registro de sesión subyacente se carga de forma sincrónica, lo que podría conllevar una disminución del rendimiento al escalar.

Para que las aplicaciones impongan este patrón, encapsule las implementaciones <xref:Microsoft.AspNetCore.Session.DistributedSessionStore> y <xref:Microsoft.AspNetCore.Session.DistributedSession> con versiones que inicien una excepción si no se llama al método `LoadAsync` antes que a `TryGetValue`, `Set` o `Remove`. Registre las versiones ajustadas en el contenedor de servicios.

### <a name="session-options"></a>Opciones de sesión

Para reemplazar los valores predeterminados de la sesión, use <xref:Microsoft.AspNetCore.Builder.SessionOptions>.

| Opción | Descripción |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie> | Determina la configuración usada para crear la cookie. <xref:Microsoft.AspNetCore.Http.CookieBuilder.Name> se establece de forma predeterminada en <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookieName?displayProperty=nameWithType> (`.AspNetCore.Session`). <xref:Microsoft.AspNetCore.Http.CookieBuilder.Path> se establece de forma predeterminada en <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookiePath?displayProperty=nameWithType> (`/`). <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> se establece de forma predeterminada en <xref:Microsoft.AspNetCore.Http.SameSiteMode.Lax?displayProperty=nameWithType> (`1`). <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> se establece de forma predeterminada en `true`. <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> se establece de forma predeterminada en `false`. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> | `IdleTimeout` indica cuánto tiempo puede estar inactiva la sesión antes de que se abandone su contenido. Cada acceso a la sesión restablece el tiempo de espera. Este valor solo es aplicable al contenido de la sesión, no a la cookie. El valor predeterminado es de 20 minutos. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IOTimeout> | El período máximo de tiempo permitido para cargar una sesión del almacén o devolverla a él. Este valor solo es aplicable a las operaciones asincrónicas. Este tiempo de espera se puede deshabilitar mediante <xref:System.Threading.Timeout.InfiniteTimeSpan>. El valor predeterminado es 1 minuto. |

La sesión utiliza una cookie para realizar el seguimiento de las solicitudes emitidas por un solo explorador e identificarlas. De manera predeterminada, esta cookie se denomina `.AspNetCore.Session` y usa una ruta de acceso de `/`. Dado que el valor predeterminado de la cookie no especifica un dominio, no estará disponible para el script de cliente en la página (porque <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> se establece de forma predeterminada en `true`).

Para reemplazar los valores predeterminados de la sesión de cookies, use <xref:Microsoft.AspNetCore.Builder.SessionOptions>:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup2.cs?name=snippet1&highlight=5-10)]

La aplicación usa la propiedad <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> para determinar el tiempo que una sesión puede estar inactiva antes de que se abandone su contenido en la caché del servidor. Esta propiedad es independiente de la expiración de la cookie. Cada solicitud que se pasa a través del [middleware de sesión](xref:Microsoft.AspNetCore.Session.SessionMiddleware) restablece el tiempo de espera.

El estado de sesión es *no realiza bloqueo*. Si dos solicitudes intentan modificar el contenido de una sesión simultáneamente, la última solicitud reemplaza a la primera. `Session` se implementa como una *sesión coherente*, lo que significa que todo el contenido se almacena junto. Cuando dos solicitudes buscan modificar diferentes valores de sesión, la última solicitud podría reemplazar los cambios de sesión realizados por la primera.

### <a name="set-and-get-session-values"></a>Establecer y obtener valores de Session

Se tiene acceso al estado de sesión desde la clase <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> de Razor Pages o desde la clase <xref:Microsoft.AspNetCore.Mvc.Controller> de MVC con <xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType>. Esta propiedad es una implementación de <xref:Microsoft.AspNetCore.Http.ISession>.

La implementación `ISession` proporciona varios métodos de extensión para establecer y recuperar valores de cadena y enteros. Los nuevos métodos de extensión se encuentran en el espacio de nombres <xref:Microsoft.AspNetCore.Http>.

Métodos de extensión `ISession`:

* [Get(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.Get%2A)
* [GetInt32(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetInt32%2A)
* [GetString(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetString%2A)
* [SetInt32(ISession, String, Int32)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetInt32%2A)
* [SetString(ISession, String, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetString%2A)

En el ejemplo siguiente se recupera el valor de sesión de la clave `IndexModel.SessionKeyName` (`_Name` en la aplicación de ejemplo) de una página de Razor Pages:

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

En el ejemplo siguiente se muestra cómo establecer y obtener un entero y una cadena:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Todos los datos de sesión se deben serializar para habilitar un escenario de caché distribuida, incluso cuando se usa la caché en memoria. Los métodos de extensión de <xref:Microsoft.AspNetCore.Http.ISession> proporcionan serializadores de cadenas y enteros. El usuario debe serializar los tipos complejos mediante otro mecanismo, como JSON.

Use el siguiente código de ejemplo para serializar objetos:

[!code-csharp[](app-state/samples/3.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

En el ejemplo siguiente se muestra cómo establecer y obtener un objeto serializable con la clase `SessionExtensions`:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core expone [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) de Razor Pages o <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> del controlador. Esta propiedad almacena datos hasta que se leen en otra solicitud. Los métodos [Keep(String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) y [Peek(string)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) se pueden usar para examinar los datos sin eliminarlos al final de la solicitud. [Keep](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) marca todos los elementos del diccionario para su retención. `TempData` es:

* Útil para el redireccionamiento cuando se necesitan los datos de más de una única solicitud.
* Implementada por los proveedores de `TempData` mediante cookies o el estado de sesión.

## <a name="tempdata-samples"></a>Ejemplos de TempData

Considere la siguiente página que crea un cliente:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

La siguiente página muestra `TempData["Message"]`:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

En el marcado anterior, al final de la solicitud, `TempData["Message"]`**no** se elimina porque se usa `Peek`. Al actualizar la página, se muestra el contenido de `TempData["Message"]`.

El marcado siguiente es similar al código anterior, pero usa `Keep` para conservar los datos al final de la solicitud:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

La navegación entre las páginas *IndexPeek* y *IndexKeep* no eliminará `TempData["Message"]`.

El código siguiente muestra `TempData["Message"]`, pero al final de la solicitud, se elimina `TempData["Message"]`:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>Proveedores de TempData

El proveedor TempData basado en cookies se usa de forma predeterminada para almacenar TempData en cookies.

Los datos de cookies se cifran mediante <xref:Microsoft.AspNetCore.DataProtection.IDataProtector>, codificados con <xref:Microsoft.AspNetCore.WebUtilities.Base64UrlTextEncoder> para posteriormente fragmentarse. El tamaño máximo de la cookie es [4096 bytes](http://www.faqs.org/rfcs/rfc2965.html) debido al cifrado y a la fragmentación. Los datos de cookie no se comprimen porque la compresión de datos cifrados puede provocar problemas de seguridad como los ataques [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) y [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)). Para más información sobre el proveedor TempData basado en cookies, consulte <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>.

### <a name="choose-a-tempdata-provider"></a>Elegir un proveedor TempData

Elegir un proveedor TempData implica una serie de consideraciones:

* ¿La aplicación ya usa el estado de sesión? Si es así, el uso del proveedor TempData de estado de sesión no tiene costo adicional para la aplicación (excepto en el tamaño de los datos).
* ¿La aplicación usa TempData con moderación, solo para cantidades relativamente pequeñas de datos (hasta 500 bytes)? Si es así, el proveedor TempData de cookies agrega un pequeño costo a cada solicitud que transporta TempData. De lo contrario, el proveedor TempData de estado de sesión puede ser beneficioso para evitar que una gran cantidad de datos hagan un recorrido de ida y vuelta en cada solicitud hasta que se consuma TempData.
* ¿La aplicación se ejecuta en una granja de servidores en varios servidores? Si es así, no es necesaria ninguna configuración adicional para usar el proveedor de TempData de cookies además de la protección de datos (vea <xref:security/data-protection/introduction> y [Proveedores de almacenamiento de claves](xref:security/data-protection/implementation/key-storage-providers)).

La mayoría de los clientes web (por ejemplo, los exploradores web) aplican límites en el tamaño máximo de cada cookie, el número total de cookies o ambos. Cuando use el proveedor TempData de cookies, asegúrese de que la aplicación no supera [esos límites](http://www.faqs.org/rfcs/rfc2965.html). Tenga en cuenta el tamaño total de los datos. Cuenta para los aumentos de tamaño de cookie debidos a la fragmentación y el cifrado.

### <a name="configure-the-tempdata-provider"></a>Configurar el proveedor TempData

El proveedor TempData basado en cookies está habilitado de forma predeterminada.

Para habilitar el proveedor TempData basado en sesión, use el método de extensión <xref:Microsoft.Extensions.DependencyInjection.MvcViewFeaturesMvcBuilderExtensions.AddSessionStateTempDataProvider%2A>. Solo se requiere una llamada a `AddSessionStateTempDataProvider`:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup3.cs?name=snippet1&highlight=4,6,8,30)]

## <a name="query-strings"></a>Cadenas de consulta

Se puede pasar una cantidad limitada de datos de una solicitud a otra si agrega los datos a la cadena de consulta de la solicitud nueva. Esto es útil para capturar el estado de una forma persistente que permita que los vínculos con estado insertado se compartan a través del correo electrónico o las redes sociales. Dado que las cadenas de consulta de direcciones URL son públicas, nunca use las cadenas de consulta para datos confidenciales.

Además del uso compartido no intencionado, la inclusión de datos en cadenas de consulta puede exponer la aplicación a ataques de [falsificación de solicitud entre sitios (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)). Cualquier estado de sesión conservado debe protegerse contra los ataques CSRF. Para más información, vea [Preventing Cross-Site Request Forgery (XSRF/CSRF) Attacks](xref:security/anti-request-forgery) (Evitar los ataques de falsificación de solicitud entre sitios [XSRF/CSRF]).

## <a name="hidden-fields"></a>Campos ocultos

Los datos pueden guardarse en campos ocultos de formulario e incluirse de nuevo en la siguiente solicitud. Esto es habitual en los formularios de varias páginas. Dado que el cliente puede llegar a alterar los datos, la aplicación siempre debe revalidar los datos almacenados en campos ocultos.

## <a name="httpcontextitems"></a>HttpContext.Items

La colección <xref:Microsoft.AspNetCore.Http.HttpContext.Items?displayProperty=nameWithType> se usa para almacenar los datos al procesar una única solicitud. El contenido de la colección se descarta después de procesar una solicitud. A menudo se usa la colección `Items` para permitir que los componentes o el middleware se comuniquen cuando operan en distintos puntos en el tiempo durante una solicitud y no pueden pasarse parámetros de forma directa.

En el ejemplo siguiente, el [middleware](xref:fundamentals/middleware/index) agrega `isVerified` a la colección `Items`:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup.cs?name=snippet1)]

Si el middleware solo se usa en una única aplicación, se aceptan claves `string` fijas. El middleware compartido entre aplicaciones debería usar claves de objeto únicas para evitar conflictos de clave. En el ejemplo siguiente se muestra cómo usar una clave de objeto única definida en una clase de middleware:

[!code-csharp[](app-state/samples/3.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Otro código puede tener acceso al valor almacenado en `HttpContext.Items` con la clave que expone la clase de middleware:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Este enfoque también tiene la ventaja de eliminar el uso de cadenas de claves en el código.

## <a name="cache"></a>instancias y claves

El almacenamiento en caché es una manera eficaz de almacenar y recuperar datos. La aplicación puede controlar la duración de los elementos almacenados en caché. Para obtener más información, vea <xref:performance/caching/response>.

Los datos almacenados en caché no están asociados a una solicitud, usuario o sesión específicos. **Procure no almacenar en caché datos específicos de usuario que se puedan recuperar mediante las solicitudes de otros usuarios.**

Para almacenar en caché datos de toda la aplicación, consulte <xref:performance/caching/memory>.

## <a name="common-errors"></a>Errores comunes

* "No se puede resolver el servicio para el tipo 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' al intentar activar 'Microsoft.AspNetCore.Session.DistributedSessionStore'".

  Normalmente, esto se debe a que no se ha configurado al menos una implementación `IDistributedCache`. Para obtener más información, vea <xref:performance/caching/distributed> y <xref:performance/caching/memory>.

Si el middleware de sesión no puede conservar una sesión:

* El middleware registra la excepción y la solicitud se procesa con normalidad.
* Esto provoca un comportamiento imprevisible.

El middleware de sesión puede no conservar una sesión si la memoria auxiliar no está disponible. Por ejemplo, un usuario almacena un carro de la compra en la sesión. El usuario agrega un elemento al carro, pero se produce un error en la confirmación. La aplicación no se percata del error y notifica al usuario que el elemento se ha agregado al carro, lo cual no es cierto.

El enfoque recomendado para comprobar si hay errores es llamar a `await feature.Session.CommitAsync` cuando la aplicación haya terminado de escribir en la sesión. <xref:Microsoft.AspNetCore.Http.ISession.CommitAsync*> produce una excepción si la memoria auxiliar no está disponible. Si `CommitAsync` produce un error, la aplicación puede procesar la excepción. <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync*> se produce en las mismas condiciones cuando el almacén de datos no está disponible.
  
## <a name="signalr-and-session-state"></a>SignalR y estado de la sesión

Las aplicaciones SignalR no deben usar el estado de sesión para almacenar información. Las aplicaciones SignalR pueden almacenarse por estado de conexión en `Context.Items` en el concentrador. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Recursos adicionales

<xref:host-and-deploy/web-farm>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), [Diana LaRose](https://github.com/DianaLaRose) y [Luke Latham](https://github.com/guardrex)

HTTP es un protocolo sin estado. Sin realizar pasos adicionales, las solicitudes HTTP son mensajes independientes que no conservan los valores de usuario ni el estado de la aplicación. En este artículo se describen varios enfoques para conservar el estado de la aplicación y los datos de usuario entre las solicitudes.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Administración de estado

El estado se puede almacenar mediante varios enfoques. Cada enfoque se describe más adelante en este tema.

| Enfoque de almacenamiento | Mecanismo de almacenamiento |
| ---------------- | ----------------- |
| [Cookies](#cookies) | Cookies HTTP (pueden incluir los datos almacenados mediante el código de aplicación del lado servidor) |
| [Estado de sesión](#session-state) | Cookies HTTP y código de aplicación del lado servidor |
| [TempData](#tempdata) | Cookies HTTP o estado de sesión |
| [Cadenas de consulta](#query-strings) | Cadenas de consulta HTTP |
| [Campos ocultos](#hidden-fields) | Campos de formularios HTTP |
| [HttpContext.Items](#httpcontextitems) | Código de aplicación del lado servidor |
| [Caché](#cache) | Código de aplicación del lado servidor |
| [Inserción de dependencias](#dependency-injection) | Código de aplicación del lado servidor |

## <a name="cookies"></a>Cookies

Las cookies almacenan datos de todas las solicitudes. Dado que las cookies se envían con cada solicitud, su tamaño debe reducirse al mínimo. Lo ideal es que en cada cookie se almacene un solo identificador con los datos almacenados por la aplicación. La mayoría de los exploradores restringen el tamaño de las cookies a 4096 bytes. Solo hay disponible un número limitado de cookies para cada dominio.

Como las cookies están expuestas a alteraciones, deben validarse por la aplicación. Los usuarios pueden eliminar las cookies y estas pueden caducar en los clientes. Pero las cookies generalmente son la forma más duradera de persistencia de datos en el cliente.

Las cookies suelen utilizarse para personalizar el contenido ofrecido a un usuario conocido. En la mayoría de los casos, el usuario solo se identifica y no se autentica. La cookie puede almacenar el nombre de usuario, el nombre de cuenta o el id. de usuario único (por ejemplo, un GUID). Después, puede usar la cookie para tener acceso a la configuración personalizada del usuario, como su color de fondo del sitio web preferido.

Preste atención al [reglamento general de protección de datos (GDPR) de la Unión Europea](https://ec.europa.eu/info/law/law-topic/data-protection) cuando emita cookies y trate con casos de privacidad. Para obtener más información, vea [Compatibilidad con el Reglamento general de protección de datos (RGPD) en ASP.NET Core](xref:security/gdpr).

## <a name="session-state"></a>Estado de sesión

El estado de sesión es un escenario de ASP.NET Core para el almacenamiento de datos de usuario mientras el usuario examina una aplicación web. El estado de sesión usa un almacén mantenido por la aplicación para conservar los datos en las solicitudes de un cliente. Los datos de sesión están respaldados por una memoria caché y se consideran datos efímeros y el sitio debería continuar funcionando correctamente sin los datos de sesión. Los datos críticos de aplicaciones deben almacenarse en la base de datos de usuario y almacenarse en caché en la sesión solo para optimizar el rendimiento.

> [!NOTE]
> La sesión no se admite en aplicaciones [SignalR](xref:signalr/index) porque un [SignalRconcentrador de ](xref:signalr/hubs) se podría ejecutar con independencia de un contexto HTTP. Por ejemplo, esto puede ocurrir cuando un concentrador mantiene abierta una solicitud de sondeo larga más allá de la duración del contexto HTTP de la solicitud.

Para mantener el estado de sesión, ASP.NET Core proporciona una cookie al cliente que contiene un identificador de sesión, que se envía a la aplicación con cada solicitud. La aplicación usa el identificador de sesión para capturar los datos de sesión.

El estado de sesión muestra los siguientes comportamientos:

* Dado que la cookie de sesión es específica del explorador, las sesiones no se comparten entre los exploradores.
* Las cookies de sesión se eliminan cuando finaliza la sesión del explorador.
* Si se recibe una cookie de una sesión que ha expirado, se crea una nueva sesión que usa la misma cookie de sesión.
* Las sesiones vacías no se mantienen y la sesión debe tener, al menos un conjunto de valores en ella para que se conserve entre solicitudes. Cuando una sesión no se conserva, se genera un nuevo identificador de sesión para cada nueva solicitud.
* La aplicación conserva una sesión durante un tiempo limitado después de la última solicitud. La aplicación especifica un tiempo de espera de sesión o usa el valor predeterminado de 20 minutos. El estado de sesión es ideal para almacenar datos de usuario que son específicos de una sesión determinada, pero que no necesitan conservarse de forma permanente entre las sesiones.
* Los datos de sesión se eliminan cuando se llama a la implementación <xref:Microsoft.AspNetCore.Http.ISession.Clear%2A?displayProperty=nameWithType> o cuando expira la sesión.
* No hay ningún mecanismo predeterminado para informar al código de aplicación que se ha cerrado un explorador del cliente o cuando la cookie de sesión se elimina o caduca en el cliente.
* Las plantillas de Razor Pages y ASP.NET Core MVC guardan conformidad con el Reglamento general de protección de datos (RGPD). Las cookies de estado de sesión no se marcan como esenciales de forma predeterminada, por lo que el estado de sesión no será funcional a menos que el visitante del sitio permita el seguimiento. Para obtener más información, vea <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> No almacene datos confidenciales en un estado de sesión. El usuario podría no cerrar el explorador y borrar la cookie de sesión. Algunos exploradores mantienen las cookies de sesión válidas en las ventanas del explorador. Es posible que una sesión no esté restringida a un único usuario y que el siguiente usuario continúe examinando la aplicación con la misma cookie de sesión.

El proveedor de caché en memoria almacena datos de sesión en la memoria del servidor donde reside la aplicación. En un escenario de granja de servidores:

* Use *sesiones permanentes* para asociar cada sesión a una instancia de aplicación específica en un servidor individual. [Azure App Service](https://azure.microsoft.com/services/app-service/) usa [enrutamiento de solicitud de aplicaciones (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) para exigir sesiones permanentes de forma predeterminada. Pero las sesiones permanentes pueden afectar a la escalabilidad y complicar la actualización de las aplicaciones web. Un enfoque mejor consiste en usar una memoria caché distribuida de Redis o SQL Server, que no requiere sesiones permanentes. Para obtener más información, vea <xref:performance/caching/distributed>.
* La cookie de sesión se cifra mediante <xref:Microsoft.AspNetCore.DataProtection.IDataProtector>. La protección de datos debe configurarse correctamente para que lea las cookies de sesión en cada equipo. Para más información, vea <xref:security/data-protection/introduction> y [Proveedores de almacenamiento de claves](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Configurar el estado de sesión

El paquete [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/), que se incluye en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), proporciona middleware para administrar el estado de sesión. Para habilitar el middleware de sesión, `Startup` debe contener:

* Cualquiera de las cachés de memoria <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>. La implementación de `IDistributedCache` se usa como una memoria auxiliar para la sesión. Para obtener más información, vea <xref:performance/caching/distributed>.
* Una llamada a <xref:Microsoft.Extensions.DependencyInjection.SessionServiceCollectionExtensions.AddSession%2A> en `ConfigureServices`.
* Una llamada a <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A> en `Configure`.

El código siguiente muestra cómo configurar el proveedor de sesión en memoria con una implementación en memoria de `IDistributedCache`:

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=5-14,34)]

El orden del middleware es importante. En el ejemplo anterior, se produce una excepción `InvalidOperationException` cuando `UseSession` se invoca después de `UseMvc`. Para obtener más información vea [Ordenación de Middleware](xref:fundamentals/middleware/index#order).

<xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType> está disponible después de configurar el estado de sesión.

No se puede acceder a `HttpContext.Session` antes de llamar a `UseSession`.

No se puede crear una nueva sesión con una cookie de sesión nueva después de que la aplicación haya empezado a escribir en la secuencia de respuesta. La excepción se registra en el registro del servidor web y no se muestra en el explorador.

### <a name="load-session-state-asynchronously"></a>Cargar de forma asincrónica el estado de sesión

El proveedor de sesión predeterminado de ASP.NET Core carga de manera asincrónica registros de sesión del almacén de respaldo <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> subyacente solo si se llama al método <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync%2A?displayProperty=nameWithType> de forma explícita antes que a los métodos <xref:Microsoft.AspNetCore.Http.ISession.TryGetValue%2A>, <xref:Microsoft.AspNetCore.Http.ISession.Set%2A> o <xref:Microsoft.AspNetCore.Http.ISession.Remove%2A>. Si primero no se llama a `LoadAsync`, el registro de sesión subyacente se carga de forma sincrónica, lo que podría conllevar una disminución del rendimiento al escalar.

Para que las aplicaciones impongan este patrón, encapsule las implementaciones <xref:Microsoft.AspNetCore.Session.DistributedSessionStore> y <xref:Microsoft.AspNetCore.Session.DistributedSession> con versiones que inicien una excepción si no se llama al método `LoadAsync` antes que a `TryGetValue`, `Set` o `Remove`. Registre las versiones ajustadas en el contenedor de servicios.

### <a name="session-options"></a>Opciones de sesión

Para reemplazar los valores predeterminados de la sesión, use <xref:Microsoft.AspNetCore.Builder.SessionOptions>.

| Opción | Descripción |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie> | Determina la configuración usada para crear la cookie. <xref:Microsoft.AspNetCore.Http.CookieBuilder.Name> se establece de forma predeterminada en <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookieName?displayProperty=nameWithType> (`.AspNetCore.Session`). <xref:Microsoft.AspNetCore.Http.CookieBuilder.Path> se establece de forma predeterminada en <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookiePath?displayProperty=nameWithType> (`/`). <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> se establece de forma predeterminada en <xref:Microsoft.AspNetCore.Http.SameSiteMode.Lax?displayProperty=nameWithType> (`1`). <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> se establece de forma predeterminada en `true`. <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> se establece de forma predeterminada en `false`. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> | `IdleTimeout` indica cuánto tiempo puede estar inactiva la sesión antes de que se abandone su contenido. Cada acceso a la sesión restablece el tiempo de espera. Este valor solo es aplicable al contenido de la sesión, no a la cookie. El valor predeterminado es de 20 minutos. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IOTimeout> | El período máximo de tiempo permitido para cargar una sesión del almacén o devolverla a él. Este valor solo es aplicable a las operaciones asincrónicas. Este tiempo de espera se puede deshabilitar mediante <xref:System.Threading.Timeout.InfiniteTimeSpan>. El valor predeterminado es 1 minuto. |

La sesión utiliza una cookie para realizar el seguimiento de las solicitudes emitidas por un solo explorador e identificarlas. De manera predeterminada, esta cookie se denomina `.AspNetCore.Session` y usa una ruta de acceso de `/`. Dado que el valor predeterminado de la cookie no especifica un dominio, no estará disponible para el script de cliente en la página (porque <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> se establece de forma predeterminada en `true`).

Para reemplazar los valores predeterminados de la sesión de cookies, use `SessionOptions`:

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=14-19)]

La aplicación usa la propiedad <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> para determinar el tiempo que una sesión puede estar inactiva antes de que se abandone su contenido en la caché del servidor. Esta propiedad es independiente de la expiración de la cookie. Cada solicitud que se pasa a través del [middleware de sesión](xref:Microsoft.AspNetCore.Session.SessionMiddleware) restablece el tiempo de espera.

El estado de sesión es *no realiza bloqueo*. Si dos solicitudes intentan modificar el contenido de una sesión simultáneamente, la última solicitud reemplaza a la primera. `Session` se implementa como una *sesión coherente*, lo que significa que todo el contenido se almacena junto. Cuando dos solicitudes buscan modificar diferentes valores de sesión, la última solicitud podría reemplazar los cambios de sesión realizados por la primera.

### <a name="set-and-get-session-values"></a>Establecer y obtener valores de Session

Se tiene acceso al estado de sesión desde la clase <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> de Razor Pages o desde la clase <xref:Microsoft.AspNetCore.Mvc.Controller> de MVC con <xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType>. Esta propiedad es una implementación de <xref:Microsoft.AspNetCore.Http.ISession>.

La implementación `ISession` proporciona varios métodos de extensión para establecer y recuperar valores de cadena y enteros. Los métodos de extensión están en el espacio de nombres <xref:Microsoft.AspNetCore.Http> (agregue una instrucción `using Microsoft.AspNetCore.Http;` para obtener acceso a los métodos de extensión) cuando el proyecto hace referencia al paquete [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/). Ambos paquetes están incluidos en el metapaquete [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Métodos de extensión `ISession`:

* [Get(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.Get%2A)
* [GetInt32(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetInt32%2A)
* [GetString(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetString%2A)
* [SetInt32(ISession, String, Int32)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetInt32%2A)
* [SetString(ISession, String, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetString%2A)

En el ejemplo siguiente se recupera el valor de sesión de la clave `IndexModel.SessionKeyName` (`_Name` en la aplicación de ejemplo) de una página de Razor Pages:

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

En el ejemplo siguiente se muestra cómo establecer y obtener un entero y una cadena:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Todos los datos de sesión se deben serializar para habilitar un escenario de caché distribuida, incluso cuando se usa la caché en memoria. Los métodos de extensión de <xref:Microsoft.AspNetCore.Http.ISession> proporcionan serializadores de cadenas y enteros. El usuario debe serializar los tipos complejos mediante otro mecanismo, como JSON.

Agregue los siguientes métodos de extensión, para establecer y obtener objetos serializables:

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

En el ejemplo siguiente se muestra cómo establecer y obtener un objeto serializable con los método de extensión:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core expone [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) de Razor Pages o <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> del controlador. Esta propiedad almacena datos hasta que se leen en otra solicitud. Los métodos [Keep(String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) y [Peek(string)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) se pueden usar para examinar los datos sin eliminarlos al final de la solicitud. [Keep()](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) marca todos los elementos del diccionario para su retención. `TempData` es particularmente útil para el redireccionamiento cuando se necesitan los datos para más de una única solicitud. Los proveedores de `TempData` implementan `TempData` mediante cookies o estado de sesión.

## <a name="tempdata-samples"></a>Ejemplos de TempData

Considere la siguiente página que crea un cliente:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

La siguiente página muestra `TempData["Message"]`:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

En el marcado anterior, al final de la solicitud, `TempData["Message"]`**no** se elimina porque se usa `Peek`. Al actualizar la página, aparece `TempData["Message"]`.

El marcado siguiente es similar al código anterior, pero usa `Keep` para conservar los datos al final de la solicitud:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

La navegación entre las páginas *IndexPeek* y *IndexKeep* no eliminará `TempData["Message"]`.

El código siguiente muestra `TempData["Message"]`, pero al final de la solicitud, se elimina `TempData["Message"]`:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>Proveedores de TempData

El proveedor TempData basado en cookies se usa de forma predeterminada para almacenar TempData en cookies.

Los datos de cookies se cifran mediante <xref:Microsoft.AspNetCore.DataProtection.IDataProtector>, codificados con <xref:Microsoft.AspNetCore.WebUtilities.Base64UrlTextEncoder> para posteriormente fragmentarse. Como la cookie está fragmentada, no se aplica el límite de tamaño único de cookie que se encuentra en ASP.NET Core 1.x. Los datos de cookie no se comprimen porque la compresión de datos cifrados puede provocar problemas de seguridad como los ataques [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) y [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)). Para más información sobre el proveedor TempData basado en cookies, consulte <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>.

### <a name="choose-a-tempdata-provider"></a>Elegir un proveedor TempData

Elegir un proveedor TempData implica una serie de consideraciones:

1. ¿La aplicación ya usa el estado de sesión? Si es así, el uso del proveedor TempData de estado de sesión no tiene costo adicional para la aplicación (excepto en el tamaño de los datos).
2. ¿La aplicación usa TempData con moderación, solo para cantidades relativamente pequeñas de datos (hasta 500 bytes)? Si es así, el proveedor TempData de cookies agrega un pequeño costo a cada solicitud que transporta TempData. De lo contrario, el proveedor TempData de estado de sesión puede ser beneficioso para evitar que una gran cantidad de datos hagan un recorrido de ida y vuelta en cada solicitud hasta que se consuma TempData.
3. ¿La aplicación se ejecuta en una granja de servidores en varios servidores? Si es así, no es necesaria ninguna configuración adicional para usar el proveedor de TempData de cookies además de la protección de datos (vea <xref:security/data-protection/introduction> y [Proveedores de almacenamiento de claves](xref:security/data-protection/implementation/key-storage-providers)).

> [!NOTE]
> La mayoría de los clientes de web (por ejemplo, los exploradores web) aplican límites en el tamaño máximo de cada cookie, el número total de cookies o ambos. Cuando use el proveedor TempData de cookies, compruebe que la aplicación no supera esos límites. Tenga en cuenta el tamaño total de los datos. Cuenta para los aumentos de tamaño de cookie debidos a la fragmentación y el cifrado.

### <a name="configure-the-tempdata-provider"></a>Configurar el proveedor TempData

El proveedor TempData basado en cookies está habilitado de forma predeterminada.

Para habilitar el proveedor TempData basado en sesión, use el método de extensión <xref:Microsoft.Extensions.DependencyInjection.MvcViewFeaturesMvcBuilderExtensions.AddSessionStateTempDataProvider%2A>:

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

El orden del middleware es importante. En el ejemplo anterior, se produce una excepción `InvalidOperationException` cuando `UseSession` se invoca después de `UseMvc`. Para obtener más información vea [Ordenación de Middleware](xref:fundamentals/middleware/index#order).

> [!IMPORTANT]
> Si el destino es .NET Framework y usa el proveedor TempData basado en sesión, agregue el paquete [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) al proyecto.

## <a name="query-strings"></a>Cadenas de consulta

Se puede pasar una cantidad limitada de datos de una solicitud a otra si agrega los datos a la cadena de consulta de la solicitud nueva. Esto es útil para capturar el estado de una forma persistente que permita que los vínculos con estado insertado se compartan a través del correo electrónico o las redes sociales. Dado que las cadenas de consulta de direcciones URL son públicas, nunca use las cadenas de consulta para datos confidenciales.

Además del uso compartido no intencionado, la inclusión de datos en las cadenas de consulta puede propiciar ataques de [falsificación de solicitud entre sitios (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)), cuya intención es engañar a los usuarios para que visiten sitios malintencionados mientras están autenticados. Después, los atacantes pueden robar los datos de usuario de la aplicación o realizar acciones malintencionadas en nombre del usuario. Cualquier estado de sesión o aplicación conservado debe protegerse contra los ataques CSRF. Para más información, vea [Preventing Cross-Site Request Forgery (XSRF/CSRF) Attacks](xref:security/anti-request-forgery) (Evitar los ataques de falsificación de solicitud entre sitios [XSRF/CSRF]).

## <a name="hidden-fields"></a>Campos ocultos

Los datos pueden guardarse en campos ocultos de formulario e incluirse de nuevo en la siguiente solicitud. Esto es habitual en los formularios de varias páginas. Dado que el cliente puede llegar a alterar los datos, la aplicación siempre debe revalidar los datos almacenados en campos ocultos.

## <a name="httpcontextitems"></a>HttpContext.Items

La colección <xref:Microsoft.AspNetCore.Http.HttpContext.Items?displayProperty=nameWithType> se usa para almacenar los datos al procesar una única solicitud. El contenido de la colección se descarta después de procesar una solicitud. A menudo se usa la colección `Items` para permitir que los componentes o el middleware se comuniquen cuando operan en distintos puntos en el tiempo durante una solicitud y no pueden pasarse parámetros de forma directa.

En el ejemplo siguiente, [Middleware](xref:fundamentals/middleware/index) agrega `isVerified` a la colección `Items`.

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

Más adelante en la canalización, otro middleware puede acceder al valor de `isVerified`:

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

Si el middleware solo se usa en una única aplicación, se aceptan claves `string`. El middleware compartido entre instancias de aplicación debería usar claves de objeto únicas para evitar conflictos de clave. En el ejemplo siguiente se muestra cómo usar una clave de objeto única definida en una clase de middleware:

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Otro código puede tener acceso al valor almacenado en `HttpContext.Items` con la clave que expone la clase de middleware:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Este enfoque también tiene la ventaja de eliminar el uso de cadenas de claves en el código.

## <a name="cache"></a>instancias y claves

El almacenamiento en caché es una manera eficaz de almacenar y recuperar datos. La aplicación puede controlar la duración de los elementos almacenados en caché.

Los datos almacenados en caché no están asociados a una solicitud, usuario o sesión específicos. **Procure no almacenar en caché datos específicos de usuario que podrían recuperar las solicitudes de otros usuarios.**

Para obtener más información, vea <xref:performance/caching/response>.

## <a name="dependency-injection"></a>Inserción de dependencias

Use [inserción de dependencias](xref:fundamentals/dependency-injection) para que los datos estén disponibles para todos los usuarios:

1. Definir un servicio que contiene los datos. Por ejemplo, una clase denominada `MyAppData` se define:

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. Agregue la clase de servicio a `Startup.ConfigureServices`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<MyAppData>();
    }
    ```

3. Use la clase de servicio de datos:

    ```csharp
    public class IndexModel : PageModel
    {
        public IndexModel(MyAppData myService)
        {
            // Do something with the service
            //    Examples: Read data, store in a field or property
        }
    }
    ```

## <a name="common-errors"></a>Errores comunes

* "No se puede resolver el servicio para el tipo 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' al intentar activar 'Microsoft.AspNetCore.Session.DistributedSessionStore'".

  Esto puede deberse a que no se ha configurado al menos una implementación `IDistributedCache`. Para obtener más información, vea <xref:performance/caching/distributed> y <xref:performance/caching/memory>.

* En caso de que el middleware de sesión no logre conservar una sesión (por ejemplo, si la memoria auxiliar no está disponible), el middleware registra la excepción y la solicitud continúa con normalidad. Esto provoca un comportamiento imprevisible.

  Por ejemplo, un usuario almacena un carro de la compra en la sesión. El usuario agrega un elemento al carro, pero se produce un error en la confirmación. La aplicación no se percata del error y notifica al usuario que el elemento se ha agregado al carro, lo cual no es cierto.

  El enfoque recomendado para comprobar los errores es llamar a `await feature.Session.CommitAsync();` desde el código de la aplicación cuando esta haya terminado de escribir en la sesión. `CommitAsync` produce una excepción si la memoria auxiliar no está disponible. Si `CommitAsync` produce un error, la aplicación puede procesar la excepción. `LoadAsync` se produce en las mismas condiciones donde el almacén de datos no está disponible.
  
## <a name="signalr-and-session-state"></a>SignalR y estado de la sesión

Las aplicaciones SignalR no deben usar el estado de sesión para almacenar información. Las aplicaciones SignalR pueden almacenarse por estado de conexión en `Context.Items` en el concentrador. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Recursos adicionales

<xref:host-and-deploy/web-farm>
::: moniker-end
