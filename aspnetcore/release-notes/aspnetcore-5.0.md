---
title: Novedades de ASP.NET Core 5.0
author: rick-anderson
description: Obtenga información sobre las nuevas características de ASP.NET Core 5.0.
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
- Kestrel
uid: aspnetcore-5.0
ms.openlocfilehash: 84747e2d13275a23e83dc2dc0f666cb0c8d001b1
ms.sourcegitcommit: 827e8be18cebbcc09b467c089e17fa6f5e430cb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2020
ms.locfileid: "94634631"
---
# <a name="whats-new-in-aspnet-core-50"></a>Novedades de ASP.NET Core 5.0

En este artículo se resaltan los cambios más importantes de ASP.NET Core 5.0, con vínculos a la documentación pertinente.

## <a name="aspnet-core-mvc-and-no-locrazor-improvements"></a>Mejoras en ASP.NET Core MVC y Razor

### <a name="model-binding-datetime-as-utc"></a>Enlace de modelos DateTime como UTC

El enlace de modelos admite ahora las cadenas de hora UTC de enlace para `DateTime`. Si la solicitud contiene una cadena de hora UTC, el enlace de modelos la enlaza a una `DateTime` UTC. Por ejemplo, la siguiente cadena de tiempo está enlazada a la hora UTC `DateTime`: `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`

### <a name="model-binding-and-validation-with-c-9-record-types"></a>Enlace de modelos y validación con los nueve tipos de registro de C#

Los [nueve tipos de registros de C#](/dotnet/csharp/whats-new/csharp-9#record-types) pueden usarse con el enlace de modelos en un controlador MVC o una página Razor. Los tipos de registros son una buena manera de modelar los datos que se transmiten a través de la red.

Por ejemplo, el `PersonController` siguiente usa el tipo de registro `Person` con el enlace de modelos y la validación de formularios:

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
          // ...
   }
}
```

El archivo `Person/Index.cshtml`:

```cshtml
@model Person

Name: <input asp-for="Model.Name" />
<span asp-validation-for="Model.Name" />

Age: <input asp-for="Model.Age" />
<span asp-validation-for="Model.Age" />
```

### <a name="improvements-to-dynamicroutevaluetransformer"></a>Mejoras en DynamicRouteValueTransformer

ASP.NET Core 3.1 presentó <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> como una manera de usar un punto de conexión personalizado para seleccionar dinámicamente una acción del controlador MVC o una página Razor. ASP.NET Core 5.0 aplicaciones pueden pasar el estado a un `DynamicRouteValueTransformer` y filtrar el conjunto de puntos de conexión elegidos.

### <a name="miscellaneous"></a>Varios

* El atributo [[Compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute) se puede aplicar a las propiedades en un modelo de página de Razor.
* Los parámetros y las propiedades enlazadas desde el cuerpo se consideran necesarios de forma predeterminada. <!-- Review: How is this different from 3.1
The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a [Required] attribute.
see https://docs.microsoft.com/aspnet/core/mvc/models/validation?view=aspnetcore-3.1   
-->

## <a name="web-api"></a>Web API

### <a name="openapi-specification-on-by-default"></a>Especificación OpenAPI activada de forma predeterminada

La [especificación OpenAPI](http://spec.openapis.org/oas/v3.0.3) es un estándar del sector para describir las API HTTP e integrarlas en procesos empresariales complejos o con terceros. OpenAPI es ampliamente compatible con todos los proveedores de nube y muchos registros de API. Las aplicaciones que emiten documentos de OpenAPI desde API web tienen una variedad de nuevas oportunidades en las que se pueden usar esas API. En colaboración con los mantenedores del proyecto de código abierto [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/), la plantilla de API de ASP.NET Core contiene una dependencia de NuGet en [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore). Swashbuckle es un conocido paquete de NuGet de código abierto que envía documentos OpenAPI de forma dinámica. Swashbuckle lo hace examinando los controladores de la API y generando el documento OpenAPI en tiempo de ejecución, o en tiempo de compilación usando la CLI de Swashbuckle.

En ASP.NET Core 5.0, las plantillas de API web habilitan la compatibilidad con OpenAPI de forma predeterminada. Para deshabilitar OpenAPI:

* Desde la línea de comandos:

    ```dotnetcli
    dotnet new webapi --no-openapi true
    ```
* Desde Visual Studio: Desactive **Habilitar la compatibilidad de OpenAPI**.

Todos los archivos *.csproj* creados para los proyectos de API web contienen la referencia del paquete NuGet [Swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/).

```xml
<ItemGroup>
    <PackageReference Include="Swashbuckle.AspNetCore" Version="5.5.1" />
</ItemGroup>
```

El código generado por la plantilla contiene código en `Startup.ConfigureServices` que activa la generación de documentos OpenAPI:

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet)]

El método `Startup.Configure` agrega el middleware Swashbuckle, que permite:

* El proceso de generación de documentos.
* La página de interfaz de usuario de Swagger de forma predeterminada en modo de desarrollo.

El código generado por la plantilla no expone accidentalmente la descripción de la API al publicar en producción.

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet2)]

#### <a name="azure-api-management-import"></a>Importación de Azure API Management

Cuando los proyectos API de ASP.NET Core habilitan OpenAPI, la publicación de la versión 16.8 y posteriores de Visual Studio 2019 proporciona automáticamente un paso adicional en el flujo de publicación. Los desarrolladores que usan [Azure API Management](xref:tutorials/publish-to-azure-api-management-using-vs) tienen la oportunidad de importar automáticamente las API en Azure API Management durante el flujo de publicación:

![Importación y publicación de Azure API Management](~/release-notes/static/publish-to-apim.png)

### <a name="better-launch-experience-for-web-api-projects"></a>Mejor experiencia de inicio para los proyectos de API web

Con OpenAPI habilitado de forma predeterminada, la experiencia de inicio de la aplicación (F5) para desarrolladores de API web mejora significativamente. Con ASP.NET Core 5.0, la plantilla de API web viene preconfigurada para cargar la página de la interfaz de usuario de Swagger. La página de la interfaz de usuario de Swagger proporciona la documentación agregada a la API publicada y permite probar las API con un solo clic.

![Vista swagger/index.html](~/release-notes/static/swagger-ui-page-rc1.png)

## Blazor

### <a name="performance-improvements"></a>Mejoras en el rendimiento

En el caso de .NET 5, hemos realizado mejoras significativas en el rendimiento del entorno de ejecución Blazor WebAssembly con un foco específico en la representación de la interfaz de usuario y la serialización de JSON complejos. En nuestras pruebas de rendimiento, Blazor WebAssembly en .NET 5 es dos a tres veces más rápido en la mayoría de los escenarios. Para más información, consulte el [blog de ASP.NET: Actualizaciones de ASP.NET Core en .NET 5 Release Candidate 1](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements).

### <a name="css-isolation"></a>Aislamiento de CSS

Blazor ahora admite la definición de estilos CSS cuyo ámbito es un componente determinado. Los estilos CSS específicos de los componentes facilitan el razonamiento de los estilos de una aplicación y evitan efectos secundarios no intencionados de los estilos globales. Para obtener más información, vea <xref:blazor/components/css-isolation>.

### <a name="new-inputfile-component"></a>Nuevo componente de `InputFile`

El componente `InputFile` permite leer uno o varios archivos seleccionados por un usuario para cargarlos. Para obtener más información, vea <xref:blazor/file-uploads>.

### <a name="new-inputradio-and-inputradiogroup-components"></a>Nuevos componentes `InputRadio` y `InputRadioGroup`

Blazor tiene los componentes integrados `InputRadio` y `InputRadioGroup` que simplifican el enlace de datos a grupos de botones de radio con validación integrada. Para obtener más información, vea <xref:blazor/forms-validation>.

### <a name="component-virtualization"></a>Virtualización de componentes

Mejore el rendimiento percibido de la representación de componentes usando la compatibilidad de virtualización integrada del marco Blazor. Para obtener más información, vea <xref:blazor/forms-validation#radio-buttons>.

### <a name="ontoggle-event-support"></a>Compatibilidad con eventos `ontoggle`

Los eventos Blazor ahora admiten el evento DOM `ontoggle`. Para obtener más información, vea <xref:blazor/components/event-handling#event-argument-types>.

### <a name="set-ui-focus-in-no-locblazor-apps"></a>Establecimiento del foco de la interfaz de usuario en aplicaciones Blazor

Use el método de conveniencia `FocusAsync` en las referencias de elemento para establecer el foco de la interfaz de usuario en ese elemento. Para obtener más información, vea <xref:blazor/components/event-handling#focus-an-element>.

### <a name="custom-validation-class-attributes"></a>Atributos de clase de validación personalizados

Los nombres de clase de validación personalizados son útiles al integrar con marcos de CSS, como Bootstrap. Para obtener más información, vea <xref:blazor/forms-validation#custom-validation-class-attributes>.

### <a name="iasyncdisposable-support"></a>Compatibilidad con IAsyncDisposable

Los componentes de Blazor ahora admiten la interfaz de <xref:System.IAsyncDisposable> para la versión asincrónica de los recursos asignados.

### <a name="javascript-isolation-and-object-references"></a>Aislamiento de JavaScript y referencias a objetos en 

Blazor permite el aislamiento de JavaScript en [módulos de JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) estándar. Para obtener más información, vea <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.

### <a name="form-components-support-display-name"></a>Nombre para mostrar de la compatibilidad de componentes de formulario

Los siguientes componentes integrados admiten nombres para mostrar con el parámetro `DisplayName`:

* `InputDate`
* `InputNumber`
* `InputSelect`

Para obtener más información, vea <xref:blazor/forms-validation#display-name-support>.

### <a name="catch-all-route-parameters"></a>Parámetros de ruta de captura general

Los parámetros de ruta de captura general, que capturan rutas de acceso en varios límites de carpeta, se admiten en los componentes. Para obtener más información, vea <xref:blazor/fundamentals/routing#catch-all-route-parameters>.

### <a name="debugging-improvements"></a>Mejoras en la depuración

La depuración de aplicaciones Blazor WebAssembly ha mejorado en ASP.NET Core 5.0. Además, ahora se admite la depuración en Visual Studio para Mac. Para obtener más información, vea <xref:blazor/debug>.

### <a name="microsoft-no-locidentity-v20-and-msal-v20"></a>Microsoft Identity v2.0 y MSAL v2.0

La seguridad de Blazor ahora usa Microsoft Identity v2.0 ([`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web) y [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)) y MSAL v2.0. Para más información, consulte los temas de la seguridad de [Blazor y del nodo Identity](xref:blazor/security/index).

### <a name="protected-browser-storage-for-no-locblazor-server"></a>Almacenamiento de explorador protegido para Blazor Server

Las aplicaciones de Blazor Server ahora pueden usar la compatibilidad integrada para almacenar el estado de la aplicación en el explorador que se ha protegido contra la manipulación mediante la protección de datos de ASP.NET Core. Los datos se pueden almacenar en el almacenamiento de explorador local o en el almacenamiento de sesión. Para obtener más información, vea <xref:blazor/state-management>.

### <a name="no-locblazor-webassembly-prerendering"></a>Representación previa de Blazor WebAssembly

La integración de componentes se ha mejorado en los modelos de hospedaje y las aplicaciones de Blazor WebAssembly ahora pueden representar la salida en el servidor. <!-- UNCOMMENT AFTER https://github.com/dotnet/AspNetCore.Docs/pull/19887 MERGES: For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps> and <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>. -->

### <a name="trimminglinking-improvements"></a>Mejoras en el recorte o la vinculación

Blazor WebAssembly realiza la vinculación o recorte del lenguaje intermedio (IL) durante una compilación para recortar el lenguaje intermedio innecesario de los ensamblados de salida de la aplicación. Con la versión de ASP.NET Core 5.0, Blazor WebAssembly realiza un recorte mejorado con opciones de configuración adicionales. Para obtener más información, consulte <xref:blazor/host-and-deploy/configure-trimmer> y [Opciones de recorte](/dotnet/core/deploying/trimming-options).

### <a name="browser-compatibility-analyzer"></a>Analizador de compatibilidad con el explorador

Las aplicaciones Blazor WebAssembly tienen como destino el área expuesta de la API de .NET completa, pero no todas las API de .NET son compatibles con WebAssembly, debido a las restricciones de espacio aislado del explorador. Las API no compatibles inician la excepción <xref:System.PlatformNotSupportedException> cuando se ejecutan en WebAssembly. Un analizador de compatibilidad de plataforma advierte al desarrollador cuando la aplicación usa API que no son compatibles con las plataformas de destino de la aplicación. Para obtener más información, vea <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>.

### <a name="lazy-load-assemblies"></a>Ensamblados de carga diferida

Se puede mejorar el rendimiento de inicio de la aplicación Blazor WebAssembly aplazando la carga de algunos ensamblados de la aplicación hasta que sean necesarios. Para obtener más información, vea <xref:blazor/webassembly-lazy-load-assemblies>.

### <a name="updated-globalization-support"></a>Compatibilidad de globalización actualizada

La compatibilidad con la globalización está disponible para Blazor WebAssembly basada en componentes internacionales para Unicode (ICU). Para obtener más información, vea <xref:blazor/globalization-localization>.

## <a name="grpc"></a>gRPC

Se han realizado muchas mejoras de conformidad en [gRPC](https://grpc.io/). Para más información, consulte [Mejoras de rendimiento de gRPC en .NET 5](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/).

Para más información sobre gRPC, consulte <xref:grpc/index>.

## SignalR

### <a name="no-locsignalr-hub-filters"></a>Filtros de concentrador de SignalR

Los filtros del concentrador SignalR, denominados canalizaciones de concentrador en ASP.NET SignalR, es una característica que permite que el código se ejecute antes y después de que se llame a los métodos de concentrador. Ejecutar el código antes y después de llamar a los métodos de concentrador es similar a cómo el middleware tiene la capacidad de ejecutar el código antes y después de una solicitud HTTP. Entre los usos habituales se incluyen el registro, el control de errores y la validación de argumentos.

Para más información, consulte [Uso de filtros del concentrador en ASP.NETSignalR](xref:signalr/hub-filters).

### <a name="no-locsignalr-parallel-hub-invocations"></a>Invocaciones de concentrador paralelo de SignalR

SignalR de ASP.NET Core ahora es capaz de controlar las invocaciones del concentrador en paralelo. Se puede cambiar el comportamiento predeterminado para permitir que los clientes invoquen más de un método de concentrador a la vez:

[!code-csharp[](~/release-notes/sample/StartupSignalRhubs.cs?name=snippet)]

### <a name="added-messagepack-support-in-no-locsignalr-java-client"></a>Se ha agregado compatibilidad con Messagepack en el cliente de Java SignalR.

Un nuevo paquete, [com.microsoft.signalr.messagepack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack), agrega compatibilidad con MessagePack al cliente de Java SignalR. Para usar el protocolo del concentrador de MessagePack, agregue `.withHubProtocol(new MessagePackHubProtocol())` al generador de conexiones:

```java
HubConnection hubConnection = HubConnectionBuilder.create(
                           "http://localhost:53353/MyHub")
               .withHubProtocol(new MessagePackHubProtocol())
               .build();
```

<!--
See [Update SignalR code](xref:migration/31-to-50#signalr) for migration instructions.
-->

## Kestrel

* Los puntos de conexión recargables mediante la configuración Kestrel puede detectar cambios en la configuración pasada a [KestrelServerOptions.Configure](xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Configure%2A) y desenlace de los puntos de conexión existentes y enlace a nuevos puntos de conexión sin necesidad de reiniciar la aplicación cuando el parámetro `reloadOnChange` es `true`. De forma predeterminada, cuando se usa <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, Kestrel se enlaza a la subsección de la configuración ["Kestrel"](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) con `reloadOnChange` habilitado. Las aplicaciones deben pasar `reloadOnChange: true` al llamar a `KestrelServerOptions.Configure` manualmente para obtener los puntos de conexión que se van a cargar.
* Mejoras de los encabezados de respuesta HTTP/2 Para más información, consulte [Mejoras de rendimiento](#performance-improvements) en la sección siguiente.
* Compatibilidad con tipos de puntos de conexión adicionales en el transporte de sockets: Al agregar a la nueva API introducida en <xref:System.Net.Sockets?displayProperty=nameWithType>, el transporte predeterminado de sockets en [Kestrel](xref:fundamentals/servers/kestrel) permite el enlace tanto a los identificadores de archivo existentes como a los sockets de dominio de Unix. La compatibilidad con el enlace a los identificadores de archivos existentes permite el uso de la integración de `Systemd` existente sin necesidad del transporte de `libuv`.
* Descodificación de encabezado personalizada en Kestrel: Las aplicaciones pueden especificar qué propiedad <xref:System.Text.Encoding> se va a usar para interpretar los encabezados entrantes basándose en el nombre del encabezado en lugar de establecerse en UTF-8 de forma predeterminada. Pase el conmutador  <!--<xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector> --> Propiedad `Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector` para especificar la codificación que se va a usar:
 
  ```csharp
  public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.RequestHeaderEncodingSelector = encoding =>
                {
                      return encoding switch
                      {
                          "Host" => System.Text.Encoding.Latin1,
                          _ => System.Text.Encoding.UTF8,
                      };
                };
            });
            webBuilder.UseStartup<Startup>();
        });
  ```

### <a name="no-lockestrel-endpoint-specific-options-via-configuration"></a>Opciones específicas del punto de conexión de Kestrel mediante configuración

Se ha agregado compatibilidad con la configuración de las opciones específicas del punto de conexión de Kestrel mediante [configuración](xref:fundamentals/configuration/index). Las configuraciones específicas del punto de conexión incluyen:

* Protocolos HTTP usados
* Protocolos TLS
* Certificado seleccionado
* Modo de certificado de cliente

La configuración permite especificar qué certificado se selecciona en función del nombre de servidor especificado. El nombre del servidor forma parte de la extensión de Indicación de nombre de servidor (SNI) al protocolo TLS, como indica el cliente. La configuración de Kestrel también admite un prefijo de carácter comodín en el nombre de host.

En el ejemplo siguiente se muestra cómo especificar un punto de conexión específico mediante un archivo de configuración:

```json
{
  "Kestrel": {
    "Endpoints": {
      "EndpointName": {
        "Url": "https://*",
        "Sni": {
          "a.example.org": {
            "Protocols": "Http1AndHttp2",
            "SslProtocols": [ "Tls11", "Tls12"],
            "Certificate": {
              "Path": "testCert.pfx",
              "Password": "testPassword"
            },
            "ClientCertificateMode" : "NoCertificate"
          },
          "*.example.org": {
            "Certificate": {
              "Path": "testCert2.pfx",
              "Password": "testPassword"
            }
          },
          "*": {
            // At least one sub-property needs to exist per
            // SNI section or it cannot be discovered via
            // IConfiguration
            "Protocols": "Http1",
          }
        }
      }
    }
  }
}
```

Indicación de nombre de servidor (SNI) es una extensión de TLS para incluir un dominio virtual como parte de la negociación SSL. Lo que esto significa efectivamente es que el nombre de dominio virtual, o un nombre de host, puede utilizarse para identificar el punto de conexión de la red.

## <a name="performance-improvements"></a>Mejoras en el rendimiento

### <a name="http2"></a>HTTP/2

* Reducciones significativas en las asignaciones en la ruta de acceso del código HTTP/2.
* Compatibilidad con la [compresión dinámica HPack](https://tools.ietf.org/html/rfc7541) de encabezados de respuesta HTTP/2 en [Kestrel](xref:fundamentals/servers/kestrel). Para más información, consulte [Tamaño de tabla de encabezado](xref:fundamentals/servers/kestrel#header-table-size) y [HPACK: la característica silenciosa de HTTP/2](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/).
* Envío de tramas PING HTTP/2: HTTP/2 tiene un mecanismo para enviar tramas de PING para asegurarse de que sigue siendo funcional una conexión inactiva. Asegurarse de que una conexión viable es especialmente útil cuando se trabaja con flujos de larga duración que suelen estar inactivos, pero solo se ven de forma intermitente las actividades, por ejemplo, las secuencias de gRPC. Las aplicaciones pueden enviar tramas de PING periódicas en [Kestrel](xref:fundamentals/servers/kestrel) estableciendo límites en <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions>:

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.Limits.Http2.KeepAlivePingInterval =
                                               TimeSpan.FromSeconds(10);
                options.Limits.Http2.KeepAlivePingTimeout =
                                               TimeSpan.FromSeconds(1);
            });
            webBuilder.UseStartup<Startup>();
        });
   ```
   <!-- review: KeepAlivePingInterval not found in RC1. Try testing with RC1. See https://github.com/dotnet/aspnetcore/pull/22565/files see C:/Users/riande/source/repos/WebApplication128/WebApplication128 -->

### <a name="containers"></a>Contenedores

Antes de .NET 5.0, la creación y publicación de un *Dockerfile* para una aplicación ASP.NET Core necesitaba extraer toda la SDK de .NET Core y la imagen de ASP.NET Core. Con esta versión, se reduce el número de bytes de las imágenes del SDK y los bytes extraídos para la imagen de ASP.NET Core se eliminan en gran medida. Para más información, consulte [este comentario de un problema de GitHub](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750).

## <a name="authentication-and-authorization"></a>Autenticación y autorización

### <a name="azure-active-directory-authentication-with-microsoftno-locidentityweb"></a>Autenticación de Azure Active Directory con Microsoft.Identity.Web

Las plantillas de proyecto de ASP.NET Core ahora se integran con <xref:Microsoft.Identity.Web?displayProperty=fullName> para controlar la autenticación con [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD). El [paquete Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) proporciona:

* Una mejor experiencia para la autenticación a través de Azure AD.
* Una manera más sencilla de acceder a los recursos de Azure en nombre de los usuarios, incluido [Microsoft Graph](/graph/overview). Consulte el [ejemplo Microsoft.Identity.Web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2), que comienza con un inicio de sesión básico y avanza a través de varios inquilinos, mediante el uso de las API de Azure, el uso de Microsoft Graph y la protección de sus propias API. `Microsoft.Identity.Web` está disponible junto con .NET 5.

### <a name="allow-anonymous-access-to-an-endpoint"></a>Acceso anónimo permitido a un punto de conexión

El método de extensión `AllowAnonymous` permite el acceso anónimo a un punto de conexión:

[!code-csharp[](~/release-notes/sample/StartupAnonEndpoint.cs?name=snippet)]

### <a name="custom-handling-of-authorization-failures"></a>Control personalizado de errores de autorización

El control personalizado de los errores de autorización es ahora más fácil con la nueva interfaz [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs) que invoca la [autorización](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) [Middleware](xref:fundamentals/middleware/index). La implementación predeterminada sigue siendo la misma, pero se puede registrar un controlador personalizado en [inserción de dependencias, que permite respuestas HTTP personalizadas basadas en el motivo del error de la autorización. Consulte [este ejemplo](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs) que muestra el uso de la `IAuthorizationMiddlewareResultHandler`.

### <a name="authorization-when-using-endpoint-routing"></a>Autorización al usar el enrutamiento de punto de conexión

La autorización al usar el enrutamiento del punto de conexión ahora recibe `HttpContext` en lugar de la instancia del punto de conexión. Esto permite que el middleware de autorización tenga acceso a `RouteData` y otras propiedades de `HttpContext` a las que no se pudo tener acceso a través de la clase <xref:Microsoft.AspNetCore.Http.Endpoint>. El punto de conexión se puede capturar desde el contexto mediante [context.GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A).

### <a name="role-based-access-control-with-kerberos-authentication-and-ldap-on-linux"></a>Control de acceso basado en roles con autenticación Kerberos y LDAP en Linux

Consulte [Autenticación Kerberos y control de acceso basado en rol (RBAC)](xref:security/authentication/windowsauth#rbac)

## <a name="api-improvements"></a>Mejoras en la API

### <a name="json-extension-methods-for-httprequest-and-httpresponse"></a>Métodos de extensión JSON para HttpRequest y HttpResponse

Los datos JSON se pueden leer y escribir desde un `HttpRequest` y `HttpResponse` mediante los nuevos métodos de extensión <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> y `WriteAsJsonAsync`. Estos métodos de extensión utilizan el serializador [System.Text.JSON](xref:System.Text.Json) para controlar los datos JSON. El nuevo método de extensión de `HasJsonContentType` también puede comprobar si una solicitud tiene un tipo de contenido JSON.

Los métodos de extensión JSON se pueden combinar con el [enrutamiento de punto de conexión](xref:fundamentals/routing) para crear las API JSON en un estilo de programación que llamamos ***ruta al código** _. Es una opción nueva para los desarrolladores que desean crear las API JSON básicas de una manera ligera. Por ejemplo, una aplicación web que tiene solo unos cuantos puntos de conexión puede optar por usar la ruta al código en lugar de la funcionalidad completa de ASP.NET Core MVC:

```csharp
endpoints.MapGet("/weather/{city:alpha}", async context =>
{
    var city = (string)context.Request.RouteValues["city"];
    var weather = GetFromDatabase(city);

    await context.Response.WriteAsJsonAsync(weather);
});
```

Para más información sobre los nuevos métodos de extensión JSON y cómo enrutar al código, consulte [este programa de .NET](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code).

### <a name="systemdiagnosticsactivity"></a>System.Diagnostics.Activity

El formato predeterminado de <xref:System.Diagnostics.Activity?displayProperty=fullName> ahora tiene como valor predeterminado el formato W3C. Esto hace que la compatibilidad con la traza distribuida en ASP.NET Core interoperable con más marcos de trabajo de forma predeterminada.

### <a name="frombodyattribute"></a>FromBodyAttribute

<xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> ahora admite la configuración de una opción que permite que estos parámetros o propiedades se consideren opcionales:

```csharp
public IActionResult Post([FromBody(EmptyBodyBehavior = EmptyBodyBehavior.Allow)]
                          MyModel model)
{
    ...
}
```

## <a name="miscellaneous-improvements"></a>Otras mejoras

Hemos empezado a aplicar las [anotaciones que aceptan valores NULL](/dotnet/csharp/nullable-references#attributes-describe-apis) a ensamblados ASP.NET Core. Tenemos previsto anotar la mayor parte de la superficie común de la API pública de .NET 5 Framework. <!-- Review: what's the impact of this? How does it work? Need more info.  Check the link I added -->

### <a name="control-startup-class-activation"></a>Control de la activación de la clase de inicio

Se ha agregado una sobrecarga de <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A> adicional que permite que una aplicación proporcione un Factory Method para controlar la activación de la clase `Startup`. El control de la activación de la clase `Startup` es útil para pasar parámetros adicionales a `Startup` que se inicializan junto con el host:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var logger = CreateLogger();
        var host = Host.CreateDefaultBuilder()
            .ConfigureWebHost(builder =>
            {
                builder.UseStartup(context => new Startup(logger));
            })
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="auto-refresh-with-dotnet-watch"></a>Actualización automática con dotnet watch

En .NET 5, la ejecución de [dotnet watch](xref:tutorials/dotnet-watch) en un proyecto de ASP.NET Core inicia el explorador predeterminado y actualiza automáticamente el explorador a medida que se realizan cambios en el código. Esto significa que puede:

_ Abrir un proyecto de ASP.NET Core en un editor de texto.
* Ejecute `dotnet watch`.
* Céntrese en los cambios de código mientras las herramientas controlan la reconstrucción, el reinicio y la recarga de la aplicación.

### <a name="console-logger-formatter"></a>Formateador del registrador de consola

Se han realizado mejoras en el proveedor de registro de la consola en la biblioteca `Microsoft.Extensions.Logging`. Los desarrolladores ahora pueden implementar un `ConsoleFormatter` personalizado para ejercer el control completo sobre el formato y el color de la salida de la consola. Las API de formateador permiten un formato enriquecido mediante la implementación de un subconjunto de las secuencias de escape VT-100. VT-100 es compatible con la mayoría de los terminales modernos. El registrador de consola puede analizar las secuencias de escape en los terminales no compatibles, lo que permite a los desarrolladores crear un solo formateador para todos los terminales.

### <a name="json-console-logger"></a>Registrador de la consola JSON

Además de admitir los formateadores personalizados, también hemos agregado un formateador JSON integrado que emite registros de JSON estructurados en la consola. En el código siguiente se muestra cómo cambiar del registrador predeterminado a JSON:

[!code-csharp[](~/release-notes/sample/ProgramJsonLog.cs?name=snippet)]

Los mensajes de registro que se emiten a la consola tienen formato JSON:

```json
{
  "EventId": 0,
  "LogLevel": "Information",
  "Category": "Microsoft.Hosting.Lifetime",
  "Message": "Now listening on: https://localhost:5001",
  "State": {
    "Message": "Now listening on: https://localhost:5001",
    "address": "https://localhost:5001",
    "{OriginalFormat}": "Now listening on: {address}"
  }
}
```
