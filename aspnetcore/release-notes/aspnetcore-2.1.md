---
title: Novedades de ASP.NET Core 2.1
author: isaac2004
description: Obtenga información sobre las nuevas características de ASP.NET Core 2.1.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: aspnetcore-2.1
ms.openlocfilehash: d10fd89a3eac34e855ba92673033edc541aa6393
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632413"
---
# <a name="whats-new-in-aspnet-core-21"></a>Novedades de ASP.NET Core 2.1

En este artículo se resaltan los cambios más importantes de ASP.NET Core 2.1, con vínculos a la documentación pertinente.

## SignalR

SignalR se ha reescrito para ASP.NET Core 2.1. ASP.NET Core SignalR incluye una serie de mejoras:

* Un modelo de escalabilidad horizontal simplificado.
* Un nuevo cliente de JavaScript sin dependencias de jQuery.
* Un nuevo protocolo binario compacto basado en MessagePack.
* Compatibilidad con protocolos personalizados.
* Un nuevo modelo de respuesta de streaming.
* Compatibilidad con clientes basados en WebSockets vacíos.

Para más información, consulte [ASP.NET CoreSignalR](xref:signalr/introduction).

## <a name="no-locrazor-class-libraries"></a>Bibliotecas de clases de Razor

Con ASP.NET Core 2.1 es más fácil compilar e incluir una interfaz de usuario basada en Razor en una biblioteca y compartirla entre varios proyectos. El nuevo SDK de Razor permite compilar archivos de Razor en un proyecto de biblioteca de clases que se puede empaquetar en un paquete NuGet. Las vistas y las páginas en las bibliotecas se detectan automáticamente y se pueden reemplazar por la aplicación. Al integrar la compilación de Razor en la compilación:

* El tiempo de inicio de la aplicación es mucho más rápido.
* Sigue habiendo actualizaciones rápidas de páginas y vistas de Razor en tiempo de ejecución como parte de un flujo de trabajo de desarrollo iterativo.

Para obtener más información, vea [Creación de una interfaz de usuario reutilizable con el proyecto de biblioteca de clases de Razor](xref:razor-pages/ui-class).

## <a name="no-locidentity-ui-library--scaffolding"></a>Biblioteca de interfaz de usuario de Identity y scaffolding

ASP.NET Core 2.1 proporciona [ASP.NET Core Identity](xref:security/authentication/identity) como una [biblioteca de clases de Razor](xref:razor-pages/ui-class). Las aplicaciones que incluyen Identity pueden aplicar el nuevo proveedor de scaffolding de Identity para agregar de forma selectiva el código fuente incluido en la biblioteca de clases de Razor (RCL) de Identity. Puede que quiera generar código fuente que le permita modificar un código y cambiar el comportamiento; así, por ejemplo, podría indicar al proveedor de scaffolding que generara el código que se usa en el registro. El código generado tiene prioridad sobre el mismo código de la RCL de Identity.

Las aplicaciones que **no** incluyen autenticación pueden aplicar el proveedor de scaffolding de Identity para agregar el paquete de Identity de la RCL. Tiene la opción de seleccionar el código de Identity que se va a generar.

Para obtener más información, vea [Scaffolding Identity en proyectos de ASP.NET Core](xref:security/authentication/scaffold-identity).

## <a name="https"></a>HTTPS

En un momento en que la seguridad y la privacidad tienen cada vez más relevancia, es importante habilitar HTTPS en las aplicaciones web. El cumplimiento de HTTPS es cada vez más estricto en Internet. Los sitios que no usan HTTPS se consideran inseguros. Los exploradores (Chrome, Mozilla) están empezando a exigir el uso de las características web dentro de un contexto seguro. El [RGPD](xref:security/gdpr) exige el uso de HTTPS para proteger la privacidad de los usuarios. Usar HTTPS en la fase de producción es esencial, pero hacerlo también en la fase de desarrollo puede ayudar a evitar problemas en la implementación (por ejemplo, vínculos inseguros). ASP.NET Core 2.1 incluye diversas mejoras que hacen que sea más fácil usar HTTPS en el desarrollo y configurar el protocolo HTTPS en la producción. Para más información, vea [Aplicación de HTTPS](xref:security/enforcing-ssl).

### <a name="on-by-default"></a>Activado de forma predeterminada

A fin de hacer posible un desarrollo de sitios web seguro, ahora HTTPS está habilitado de forma predeterminada. A partir de 2.1, Kestrel escucha en `https://localhost:5001` cuando hay presente un certificado de desarrollo local. Un certificado de desarrollo se crea:

* Como parte de la experiencia de primera ejecución del SDK de .NET Core, cuando el SDK se usa por primera vez.
* Manualmente, por medio de la nueva herramienta `dev-certs`.

Ejecute `dotnet dev-certs https --trust` para confiar en el certificado.

### <a name="https-redirection-and-enforcement"></a>Cumplimiento y redireccionamiento de HTTPS

Normalmente, las aplicaciones web necesitan escuchar en HTTP y HTTPS, si bien luego redirigen todo el tráfico HTTP a HTTPS. En 2.1 se ha incluido un middleware especializado de redireccionamiento de HTTPS que redirige de forma inteligente según la presencia de puertos de servidor enlazado o configuración.

El uso de HTTPS puede exigir aún más por medio del [protocolo de Seguridad de transporte estricta de HTTP (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts). HSTS indica a los exploradores que tengan acceso al sitio siempre a través de HTTPS. ASP.NET Core 2.1 agrega middleware de HSTS que contempla opciones de antigüedad máxima, subdominios y la lista de carga previa de HSTS.

### <a name="configuration-for-production"></a>Configuración para producción

En un entorno de producción, HTTPS se debe configurar explícitamente. En 2.1, se ha agregado un esquema de configuración predeterminado para configurar HTTPS para Kestrel. Las aplicaciones se pueden configurar para usar:

* Varios puntos de conexión (direcciones URL incluidas). Para más información, consulte [Kestrel web server implementation: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (Kestrel: configuración de los puntos de conexión).
* El certificado que se va a usar para HTTPS desde un archivo en disco o desde un almacén de certificados.

## <a name="gdpr"></a>RGPD

ASP.NET Core proporciona API y plantillas para cumplir algunos de los requisitos del [Reglamento general de protección de datos (RGPD) de la UE](https://www.eugdpr.org/). Para más información, vea [GDPR support in ASP.NET Core](xref:security/gdpr) (Compatibilidad con el Reglamento general de protección de datos en ASP.NET Core). Con las [aplicaciones de muestra](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) se muestra cómo usar y probar la mayor parte de las API y los puntos de extensión del RGPD que se han agregado a las plantillas de ASP.NET Core 2.1.

## <a name="integration-tests"></a>Pruebas de integración

Se ha incorporado un nuevo paquete que optimiza las tareas de creación y ejecución de pruebas. El paquete [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) se encarga de estas tareas:

* Copia el archivo de dependencia ( *\*.deps*) de la aplicación que se está probando en la carpeta *bin* del proyecto de prueba.
* Establece la raíz de contenido en la raíz de proyecto de la aplicación que se está probando, lo que permite encontrar archivos estáticos y páginas o vistas cuando se ejecutan las pruebas.
* Proporciona la clase [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) para optimizar el arranque de la aplicación que se está probando con [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).

En la siguiente prueba se usa [xUnit](https://xunit.github.io/) para comprobar que la página de índice se carga con un código de estado correcto y con el encabezado Content-Type apropiado:

```csharp
public class BasicTests
    : IClassFixture<WebApplicationFactory<RazorPagesProject.Startup>>
{
    private readonly HttpClient _client;

    public BasicTests(WebApplicationFactory<RazorPagesProject.Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetHomePage()
    {
        // Act
        var response = await _client.GetAsync("/");

        // Assert
        response.EnsureSuccessStatusCode(); // Status Code 200-299
        Assert.Equal("text/html; charset=utf-8",
            response.Content.Headers.ContentType.ToString());
    }
}
```

Para más información, vea el tema [Pruebas de integración](xref:test/integration-tests).

## <a name="apicontroller-actionresultt"></a>[ApiController], ActionResult\<T>

ASP.NET Core 2.1 presenta nuevas convenciones de programación que hacen que sea más fácil crear y limpiar API web descriptivas. `ActionResult<T>` es un nuevo tipo que se ha agregado para que una aplicación pueda devolver un tipo de respuesta o cualquier otro resultado de acción (similar a IActionResult), sin dejar de indicar el tipo de respuesta. El atributo `[ApiController]` se ha agregado también como una forma de decidir si usar convenciones y comportamientos específicos de las API web.

Para más información, vea [Compilación de API web con ASP.NET Core](xref:web-api/index).

## <a name="ihttpclientfactory"></a>IHttpClientFactory

ASP.NET Core 2.1 incluye un nuevo servicio `IHttpClientFactory` que facilita la configuración y uso de instancias de `HttpClient` en las aplicaciones. `HttpClient` ya posee el concepto de controladores de delegación, que se pueden vincular entre sí para las solicitudes HTTP salientes. Este servicio:

* Hace que el registro de instancias de `HttpClient` por cliente con nombre sea más intuitivo.
* Implementa un controlador de Polly que permite usar directivas de Polly en directivas de reintentos, de interruptores de circuitos, etc.

Para más información, vea [Inicio de solicitudes HTTP](xref:fundamentals/http-requests).

## <a name="kestrel-transport-configuration"></a>Configuración de transporte de Kestrel

Desde el lanzamiento de ASP.NET Core 2.1, el transporte predeterminado de Kestrel deja de basarse en Libuv y pasa a basarse en sockets administrados. Para más información, consulte [Kestrel web server implementation: Transport configuration](xref:fundamentals/servers/kestrel#transport-configuration) (Implementación del servidor web de Kestrel: configuración de transporte).

## <a name="generic-host-builder"></a>Generador de host genérico

Se ha incluido el generador de host genérico (`HostBuilder`), que se puede usar con aplicaciones que no procesan solicitudes HTTP (mensajería, tareas en segundo plano, etc.).

Para más información, vea [Host genérico de .NET](xref:fundamentals/host/generic-host).

## <a name="updated-spa-templates"></a>Plantillas de SPA actualizadas

Las plantillas de aplicación de página única para Angular, React y React con Redux se han actualizado y ahora usan sistemas de generación y estructuras de proyecto estándar en cada marco.

La plantilla Angular se basa en la CLI de Angular, mientras que las plantillas de React se basan en create-react-app.

Para obtener más información, consulte:

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="no-locrazor-pages-search-for-no-locrazor-assets"></a>Razor Pages busca recursos de Razor

En la versión 2.1, Razor Pages busca recursos de Razor (como diseños y líneas de código parcialmente ejecutadas) en los siguientes directorios y en el orden indicado:

1. Carpeta Current Pages
1. */Pages/Shared/*
1. */Views/Shared/*

## <a name="no-locrazor-pages-in-an-area"></a>Razor Pages en un área

Razor Pages ya admite las [áreas](xref:mvc/controllers/areas). Para ver un ejemplo de áreas, cree una nueva aplicación web de Razor Pages con cuentas de usuario individuales. Las aplicaciones web de Razor Pages con cuentas de usuario individuales incluyen */Areas/Identity/Pages*.

## <a name="mvc-compatibility-version"></a>Versión de compatibilidad de MVC

El método <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> permite a una aplicación participar o no en los cambios de comportamiento importantes incorporados en ASP.NET Core MVC 2.1 o una versión posterior.

Para obtener más información, vea <xref:mvc/compatibility-version>.

## <a name="migrate-from-20-to-21"></a>Migración de 2.0 a 2.1

Vea [Migrate from ASP.NET Core 2.0 to 2.1](xref:migration/20_21) (Migración de ASP.NET Core 2.0 a 2.1).

## <a name="additional-information"></a>Información adicional

Para ver la lista completa de cambios, vea las [notas de la versión de ASP.NET Core 2.1](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0).
