---
title: Conceptos básicos de ASP.NET Core
author: rick-anderson
description: Obtenga información sobre los conceptos básicos para crear aplicaciones de ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
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
uid: fundamentals/index
ms.openlocfilehash: 25348f8486ec6ccb53ebf527ad4519638dd5f73e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059382"
---
# <a name="aspnet-core-fundamentals"></a>Conceptos básicos de ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

En este artículo se proporciona una introducción de los temas clave para entender cómo desarrollar aplicaciones de ASP.NET Core.

## <a name="the-startup-class"></a>Clase Startup

La clase `Startup` es donde:

* Se configuran los servicios requeridos por la aplicación.
* La canalización de control de solicitudes de la aplicación se define como una serie de componentes de middleware.

Aquí tiene una clase `Startup` de ejemplo:

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

Para obtener más información, vea <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Inserción de dependencias (servicios)

ASP.NET Core incluye un marco de inserción de dependencias (DI) integrado que pone a disposición los servicios configurados a lo largo de una aplicación. Por ejemplo, un componente de registro es un servicio.

Se agrega al método `Startup.ConfigureServices` el código para configurar (o *registrar* ) servicios. Por ejemplo:

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

Los servicios se suelen resolver desde la inserción de dependencias mediante la inserción de constructores. Con la inserción de constructores, una clase declara un parámetro de constructor del tipo requerido o una interfaz. El marco de inserción de dependencias proporciona una instancia de este servicio en tiempo de ejecución.

En el ejemplo siguiente se usa la inserción de constructores para resolver un `RazorPagesMovieContext` desde la inserción de constructores:

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

Si el contenedor Inversión de control (IoC) integrado no satisface todas las necesidades de una aplicación, se puede usar en su lugar un contenedor de IoC de terceros.

Para obtener más información, vea <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Software intermedio

La canalización de control de solicitudes se compone de una serie de componentes de software intermedio. Cada componente lleva a cabo las operaciones en un contexto `HttpContext` e invoca el middleware siguiente de la canalización, o bien finaliza la solicitud.

Normalmente, se agrega un componente de software intermedio a la canalización al invocar un método de extensión `Use...` en el método `Startup.Configure`. Por ejemplo, para habilitar la representación de los archivos estáticos, llame a `UseStaticFiles`.

En el ejemplo siguiente se configura una canalización de control de solicitudes:

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

ASP.NET Core incluye un amplio conjunto de middleware integrado. También se pueden escribir componentes de middleware personalizados.

Para obtener más información, vea <xref:fundamentals/middleware/index>.

## <a name="host"></a>administrador de flujos de trabajo

En el inicio, una aplicación de ASP.NET Core compila un *host* . El host encapsula todos los recursos de la aplicación, como:

* Una implementación de servidor de HTTP
* Componentes de software intermedio
* Registro
* Servicios de inserción de dependencias (DI)
* Configuración

Hay dos hosts distintos: 

* Host genérico de .NET
* Host web de ASP.NET Core

Se recomienda el host genérico de .NET. El host web de ASP.NET Core solo está disponible para la compatibilidad con versiones anteriores.

En el ejemplo siguiente se crea un host genérico de .NET:

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

Los métodos `CreateDefaultBuilder` y `ConfigureWebHostDefaults` configuran un host con un conjunto de opciones predeterminadas, como:

* Use [Kestrel](#servers) como servidor web y habilite la integración de IIS.
* Cargue la configuración de *appsettings.json* , *appsettings.{Environment Name}.json* , las variables de entorno, los argumentos de línea de comandos y otros orígenes de configuración.
* Envíe la salida de registro a la consola y los proveedores de depuración.

Para obtener más información, vea <xref:fundamentals/host/generic-host>.

### <a name="non-web-scenarios"></a>Escenarios que no son web

El host genérico permite que otros tipos de aplicaciones usen las extensiones de marcos transversales, como el registro, la inserción de dependencias (DI), la configuración y la administración de la duración de la aplicación. Para obtener más información, vea <xref:fundamentals/host/generic-host> y <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Servidores

Una aplicación ASP.NET Core usa una implementación de servidor HTTP para escuchar las solicitudes HTTP. El servidor expone las solicitudes a la aplicación como un conjunto de [características de solicitud](xref:fundamentals/request-features) integradas en un contexto `HttpContext`.

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core proporciona las siguientes implementaciones de servidor:

* *Kestrel* es un servidor web multiplataforma. Kestrel se suele ejecutar en una configuración de proxy inverso con [IIS](https://www.iis.net/). En ASP.NET Core 2.0 y versiones posteriores, Kestrel puede ejecutarse como servidor perimetral de acceso público expuesto directamente a Internet.
* *Servidor HTTP de IIS* es un servidor para Windows que usa IIS. Con este servidor, la aplicación de ASP.NET Core e IIS se ejecutan en el mismo proceso.
* *HTTP.sys* es un servidor de Windows que no se usa con IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core proporciona la implementación de servidor multiplataforma *Kestrel* . En ASP.NET Core 2.0 y versiones posteriores, Kestrel se puede ejecutar como servidor perimetral de acceso público expuesto directamente a Internet. Kestrel se suele ejecutar en una configuración de proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core proporciona la implementación de servidor multiplataforma *Kestrel* . En ASP.NET Core 2.0 y versiones posteriores, Kestrel se puede ejecutar como servidor perimetral de acceso público expuesto directamente a Internet. Kestrel se suele ejecutar en una configuración de proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).

---

Para obtener más información, vea <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Configuración

ASP.NET Core proporciona un marco de configuración que obtiene la configuración como pares nombre/valor de un conjunto ordenado de proveedores de configuración. Hay disponibles proveedores de configuración integrados para una gran variedad de orígenes, como archivos *.json* y *.xml* , variables de entorno y argumentos de línea de comandos. Escriba proveedores de configuración personalizados para admitir otros orígenes.

[De manera predeterminada](xref:fundamentals/configuration/index#default), las aplicaciones de ASP.NET Core están configuradas para leer desde *appsettings.json* , variables de entorno, la línea de comandos, etc. Cuando se carga la configuración de la aplicación, los valores de las variables de entorno invalidan los valores de *appsettings.json* .

La mejor manera de leer valores de configuración relacionados es usar el [patrón de opciones](xref:fundamentals/configuration/options). Para más información, consulte [Enlace de datos de configuración jerárquica mediante el patrón de opciones](xref:fundamentals/configuration/index#optpat).

Para administrar los datos de configuración confidencial como las contraseñas, ASP.NET Core proporciona el [Administrador de secretos](xref:security/app-secrets#secret-manager). Para los secretos de producción, se recomienda [Azure Key Vault](xref:security/key-vault-configuration).

Para obtener más información, vea <xref:fundamentals/configuration/index>.

## <a name="environments"></a>Entornos

Los entornos de ejecución, como `Development`, `Staging` y `Production`, son un concepto de primera clase en ASP.NET Core. Especifique el entorno que ejecuta una aplicación estableciendo la variable de entorno `ASPNETCORE_ENVIRONMENT`. ASP.NET Core lee dicha variable de entorno al inicio de la aplicación y almacena el valor en una implementación `IWebHostEnvironment`. Esta implementación está disponible en cualquier parte de una aplicación a través de la inserción de dependencias (DI).

En el ejemplo siguiente se configura la aplicación para proporcionar información detallada del error cuando se ejecuta en el entorno de `Development`:

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

Para obtener más información, vea <xref:fundamentals/environments>.

## <a name="logging"></a>Registro

ASP.NET Core es compatible con una API de registro que funciona con una gran variedad de proveedores de registro integrados y de terceros. Los proveedores disponibles incluyen:

* Consola
* Depuración
* Seguimiento de eventos en Windows
* Registro de errores de Windows
* TraceSource
* Azure App Service
* Azure Application Insights

Para crear registros, resuelva un servicio <xref:Microsoft.Extensions.Logging.ILogger%601> a partir de la inserción de dependencias (DI) y llame a métodos de registro como <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>. Por ejemplo:

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

Los métodos de registro como `LogInformation` admiten cualquier número de campos. Por lo general, estos campos se utilizan para construir un mensaje `string`, pero algunos proveedores de registro los envían a un almacén de datos como campos independientes. Esta característica permite a los proveedores de registro implementar el [registro semántico, también conocido como registro estructurado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Para obtener más información, vea <xref:fundamentals/logging/index>.

## <a name="routing"></a>Enrutamiento

Una *ruta* es un patrón de dirección URL que se asigna a un controlador. El controlador normalmente es una página de Razor, un método de acción en un controlador MVC o un software intermedio. El enrutamiento de ASP.NET Core le permite controlar las direcciones URL usadas por la aplicación.

Para obtener más información, vea <xref:fundamentals/routing>.

## <a name="error-handling"></a>Control de errores

ASP.NET Core tiene características integradas para controlar los errores, tales como:

* Una página de excepciones para el desarrollador
* Páginas de errores personalizados
* Páginas de códigos de estado estáticos
* Control de excepciones de inicio

Para obtener más información, vea <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Realización de solicitudes HTTP

Una implementación de `IHttpClientFactory` está disponible para crear instancias de `HttpClient`. Este servicio:

* Proporciona una ubicación central para denominar y configurar instancias de `HttpClient` lógicas. Por ejemplo, registre y configure un cliente de *github* para acceder a GitHub. Registre y configure un cliente predeterminado para otros fines.
* Admite el registro y encadenamiento de varios controladores de delegación para crear una canalización de middleware de solicitud saliente. Este patrón es similar a la canalización de middleware de entrada de ASP.NET Core. Dicho patrón proporciona un mecanismo para administrar cuestiones transversales relativas a las solicitudes HTTP, como el almacenamiento en caché, el control de errores, la serialización y el registro.
* Se integra con *Polly* , una conocida biblioteca de terceros para el control de errores transitorios.
* Administra la agrupación y duración de las instancias de `HttpClientHandler` subyacentes para evitar los problemas de DNS que suelen producirse al administrar manualmente las duraciones de `HttpClient`.
* Agrega una experiencia de registro configurable a través de <xref:Microsoft.Extensions.Logging.ILogger> en todas las solicitudes enviadas a través de los clientes creados por Factory.

Para obtener más información, vea <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Raíz del contenido

La raíz del contenido es la ruta de acceso base para:

* El archivo ejecutable que hospeda la aplicación ( *.exe* ).
* Los ensamblados compilados que componen la aplicación ( *.dll* ).
* Los archivos de contenido usados por la aplicación, como:
  * Archivos de Razor ( *.cshtml* , *.razor* )
  * Archivos de configuración ( *.json* , *.xml* )
  * Archivos de datos ( *.db* )
* La [raíz web](#web-root), por lo general la carpeta *wwwroot* .

Durante el desarrollo, la raíz del contenido tiene como valor predeterminado el directorio raíz del proyecto. Este directorio también es la ruta de acceso base para los archivos de contenido de la aplicación y la [raíz web](#web-root). Especifique una raíz de contenido diferente estableciendo su ruta de acceso al [compilar el host](#host). Para obtener más información, vea [Raíz del contenido](xref:fundamentals/host/generic-host#contentroot).

## <a name="web-root"></a>Raíz web

La raíz web es la ruta de acceso base para los archivos de recursos estáticos públicos, como:

* Hojas de estilo ( *.css* )
* JavaScript ( *.js* )
* Imágenes ( *.png* , *.jpg* )

De manera predeterminada, los archivos estáticos solo se sirven desde el directorio raíz web y sus subdirectorios. La ruta de acceso raíz web se establece de manera predeterminada en *{raíz del contenido}/wwwroot* . Para especificar otra raíz web, establezca su ruta de acceso al [compilar el host](#host). Para obtener más información, vea [Raíz web](xref:fundamentals/host/generic-host#webroot).

Evite la publicación de archivos en *wwwroot* con el elemento del proyecto [\<Content>](/visualstudio/msbuild/common-msbuild-project-items#content) del archivo de proyecto. En el ejemplo siguiente se impide la publicación de contenido en *wwwroot/local* y sus subdirectorios:

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

En los archivos Razor *.cshtml* , la virgulilla (`~/`) apunta a la raíz web. Una ruta de acceso que empieza por `~/` se conoce como *ruta de acceso virtual* .

Para obtener más información, vea <xref:fundamentals/static-files>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Este artículo es una introducción de los temas clave para entender cómo desarrollar aplicaciones de ASP.NET Core.

## <a name="the-startup-class"></a>Clase Startup

La clase `Startup` es donde:

* Se configuran los servicios requeridos por la aplicación.
* Se define la solicitud de canalización.

Los *servicios* son componentes que usan la aplicación. Por ejemplo, un componente de registro es un servicio. Se agrega al método `Startup.ConfigureServices` el código para configurar (o *registrar* ) servicios.

La canalización de control de solicitudes se compone de una serie de *componentes de software intermedio* . Por ejemplo, un software intermedio podría controlar las solicitudes de archivos estáticos o redirigir las solicitudes HTTP a HTTPS. Cada software intermedio lleva a cabo las operaciones asincrónicas en un contexto `HttpContext` y, después, invoca el siguiente software intermedio de la canalización o finaliza la solicitud. Se agrega al método `Startup.Configure` el código para configurar la canalización de control de solicitudes.

Aquí tiene una clase `Startup` de ejemplo:

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

Para obtener más información, vea <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Inserción de dependencias (servicios)

ASP.NET Core tiene un marco de inserción de dependencias (DI) integrado que pone a disposición los servicios configurados para las clases de una aplicación. Una manera de obtener una instancia de un servicio en una clase es crear un constructor con un parámetro del tipo necesario. El parámetro puede ser el tipo de servicio o una interfaz. El sistema de DI proporciona el servicio en tiempo de ejecución.

Aquí tiene una clase que usa DI para obtener un objeto de contexto de Entity Framework Core. La línea resaltada es un ejemplo de inserción de constructor:

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

Aunque DI está integrada, está diseñada para permitirle conectar un contenedor de inversión de control (IoC) de terceros si lo prefiere.

Para obtener más información, vea <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Software intermedio

La canalización de control de solicitudes se compone de una serie de componentes de software intermedio. Cada componente lleva a cabo las operaciones asincrónicas en un contexto `HttpContext` y, después, invoca el siguiente software intermedio de la canalización o finaliza la solicitud.

Normalmente, se agrega un componente de software intermedio a la canalización al invocar su método de extensión `Use...` en el método `Startup.Configure`. Por ejemplo, para habilitar la representación de los archivos estáticos, llame a `UseStaticFiles`.

El código resaltado en el ejemplo siguiente configura la canalización de control de solicitudes:

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

ASP.NET Core incluye una gran variedad de software intermedio integrado. Además, puede escribir software intermedio personalizado.

Para obtener más información, vea <xref:fundamentals/middleware/index>.

## <a name="host"></a>administrador de flujos de trabajo

Una aplicación de ASP.NET Core compila un *host* durante el inicio. El host es un objeto que encapsula todos los recursos de la aplicación, como:

* Una implementación de servidor de HTTP
* Componentes de software intermedio
* Registro
* DI
* Configuración

La razón principal para incluir todos los recursos interdependientes de la aplicación en un objeto es la administración de la duración: el control sobre el inicio de la aplicación y el apagado estable.

Hay dos hosts disponibles: el host web y el host genérico. En ASP.NET Core 2.x, el host genérico es solo para escenarios que no son web.

El código para crear un host está en `Program.Main`:

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

El métodos `CreateDefaultBuilder` configura un host con opciones de uso común, como las siguientes:

* Use [Kestrel](#servers) como servidor web y habilite la integración de IIS.
* Cargue la configuración de *appsettings.json* , *appsettings.{Environment Name}.json* , las variables de entorno, los argumentos de línea de comandos y otros orígenes de configuración.
* Envíe la salida de registro a la consola y los proveedores de depuración.

Para obtener más información, vea <xref:fundamentals/host/web-host>.

### <a name="non-web-scenarios"></a>Escenarios que no son web

El host genérico permite que otros tipos de aplicaciones usen las extensiones de marcos transversales, como el registro, la inserción de dependencias (DI), la configuración y la administración de la duración de la aplicación. Para obtener más información, vea <xref:fundamentals/host/generic-host> y <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Servidores

Una aplicación ASP.NET Core usa una implementación de servidor HTTP para escuchar las solicitudes HTTP. El servidor expone las solicitudes a la aplicación como un conjunto de [características de solicitud](xref:fundamentals/request-features) integradas en un contexto `HttpContext`.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core proporciona las siguientes implementaciones de servidor:

* *Kestrel* es un servidor web multiplataforma. Kestrel se suele ejecutar en una configuración de proxy inverso con [IIS](https://www.iis.net/). Kestrel puede ejecutarse como servidor perimetral de acceso público expuesto directamente a Internet.
* Un *servidor HTTP de IIS* es un servidor para Windows que usa IIS. Con este servidor, la aplicación de ASP.NET Core e IIS se ejecutan en el mismo proceso.
* *HTTP.sys* es un servidor de Windows que no se usa con IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core proporciona la implementación de servidor multiplataforma *Kestrel* . Kestrel puede ejecutarse como servidor perimetral de acceso público expuesto directamente a Internet. Kestrel se suele ejecutar en una configuración de proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core proporciona la implementación de servidor multiplataforma *Kestrel* . Kestrel puede ejecutarse como servidor perimetral de acceso público expuesto directamente a Internet. Kestrel se suele ejecutar en una configuración de proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core proporciona las siguientes implementaciones de servidor:

* *Kestrel* es un servidor web multiplataforma. Kestrel se suele ejecutar en una configuración de proxy inverso con [IIS](https://www.iis.net/). Kestrel puede ejecutarse como servidor perimetral de acceso público expuesto directamente a Internet.
* *HTTP.sys* es un servidor de Windows que no se usa con IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core proporciona la implementación de servidor multiplataforma *Kestrel* . Kestrel puede ejecutarse como servidor perimetral de acceso público expuesto directamente a Internet. Kestrel se suele ejecutar en una configuración de proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core proporciona la implementación de servidor multiplataforma *Kestrel* . Kestrel puede ejecutarse como servidor perimetral de acceso público expuesto directamente a Internet. Kestrel se suele ejecutar en una configuración de proxy inverso con [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Para obtener más información, vea <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Configuración

ASP.NET Core proporciona un marco de configuración que obtiene la configuración como pares nombre/valor de un conjunto ordenado de proveedores de configuración. Hay proveedores de configuración integrados para una gran variedad de orígenes, como archivos *.json* y *.xml* , variables de entorno y argumentos de línea de comandos. También puede escribir proveedores de configuración personalizados.

Por ejemplo, puede especificar que la configuración procede de *appsettings.json* y las variables de entorno. Después, cuando se solicite el valor de *ConnectionString* , el marco buscará primero en el archivo *appsettings.json* . Si se encuentra el valor allí, pero también en una variable de entorno, el valor de la variable de entorno tendría prioridad.

Para administrar los datos de configuración confidencial como las contraseñas, ASP.NET Core proporciona una [herramienta de administrador secreto](xref:security/app-secrets). Para los secretos de producción, se recomienda [Azure Key Vault](xref:security/key-vault-configuration).

Para obtener más información, vea <xref:fundamentals/configuration/index>.

## <a name="options"></a>Opciones

Siempre que sea posible, ASP.NET Core sigue el *patrón de opciones* para almacenar y recuperar los valores de configuración. El patrón de opciones usa clases para representar grupos de configuraciones relacionadas.

Por ejemplo, el código siguiente define las opciones de WebSockets:

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

Para obtener más información, vea <xref:fundamentals/configuration/options>.

## <a name="environments"></a>Entornos

Los entornos de ejecución, como *desarrollo* , *almacenamiento provisional* y *Producción* , son un concepto de primera clase en ASP.NET Core. Puede especificar el entorno que ejecuta una aplicación estableciendo la variable de entorno `ASPNETCORE_ENVIRONMENT`. ASP.NET Core lee dicha variable de entorno al inicio de la aplicación y almacena el valor en una implementación `IHostingEnvironment`. El objeto de entorno está disponible en cualquier parte de la aplicación a través de DI.

El siguiente ejemplo de código desde la clase `Startup` configura la aplicación para que proporcione información detallada del error solo cuando se ejecuta en el desarrollo:

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

Para obtener más información, vea <xref:fundamentals/environments>.

## <a name="logging"></a>Registro

ASP.NET Core es compatible con una API de registro que funciona con una gran variedad de proveedores de registro integrados y de terceros. Entre los proveedores disponibles se incluyen los siguientes:

* Consola
* Depuración
* Seguimiento de eventos en Windows
* Registro de errores de Windows
* TraceSource
* Azure App Service
* Azure Application Insights

Escribir registros desde cualquier lugar en el código de una aplicación mediante la obtención de un objeto `ILogger` de DI y llamar a métodos de registro.

Aquí tiene un código de ejemplo que usa un objeto `ILogger`, con la inserción del constructor y resaltadas las llamadas del método de registro.

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

La interfaz `ILogger` le permite pasar cualquier número de campos para el proveedor de registro. Los campos habitualmente se usan para construir una cadena de mensaje, pero el proveedor también puede enviarlos como campos independientes o almacén de datos. Esta característica permite a los proveedores de registro implementar el [registro semántico, también conocido como registro estructurado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Para obtener más información, vea <xref:fundamentals/logging/index>.

## <a name="routing"></a>Enrutamiento

Una *ruta* es un patrón de dirección URL que se asigna a un controlador. El controlador normalmente es una página de Razor, un método de acción en un controlador MVC o un software intermedio. El enrutamiento de ASP.NET Core le permite controlar las direcciones URL usadas por la aplicación.

Para obtener más información, vea <xref:fundamentals/routing>.

## <a name="error-handling"></a>Control de errores

ASP.NET Core tiene características integradas para controlar los errores, tales como:

* Una página de excepciones para el desarrollador
* Páginas de errores personalizados
* Páginas de códigos de estado estáticos
* Control de excepciones de inicio

Para obtener más información, vea <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Realización de solicitudes HTTP

Una implementación de `IHttpClientFactory` está disponible para crear instancias de `HttpClient`. Este servicio:

* Proporciona una ubicación central para denominar y configurar instancias de `HttpClient` lógicas. Así, por ejemplo, se puede registrar y configurar un cliente *github* para tener acceso a GitHub. y, de igual modo, registrar otro cliente predeterminado para otros fines.
* Admite el registro y encadenamiento de varios controladores de delegación para crear una canalización de middleware de solicitud saliente. Este patrón es similar a la canalización de middleware de entrada de ASP.NET Core. Dicho patrón proporciona un mecanismo para administrar cuestiones transversales relativas a las solicitudes HTTP, como el almacenamiento en caché, el control de errores, la serialización y el registro.
* Se integra con *Polly* , una conocida biblioteca de terceros para el control de errores transitorios.
* Administra la agrupación y duración de las instancias de `HttpClientHandler` subyacentes para evitar los problemas de DNS que suelen producirse al administrar las duraciones de `HttpClient` manualmente.
* Agrega una experiencia de registro configurable (a través de `ILogger`) en todas las solicitudes enviadas a través de los clientes creados por Factory.

Para obtener más información, vea <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Raíz del contenido

La raíz del contenido es la ruta de acceso base a:

* El archivo ejecutable que hospeda la aplicación ( *.exe* ).
* Los ensamblados compilados que componen la aplicación ( *.dll* ).
* Los archivos de contenido que no son de código usados por la aplicación, como:
  * Archivos de Razor ( *.cshtml* , *.razor* )
  * Archivos de configuración ( *.json* , *.xml* )
  * Archivos de datos ( *.db* )
* [Raíz web](#web-root), normalmente la carpeta *wwwroot* publicada.

Durante el desarrollo:

* La raíz del contenido tiene como valor predeterminado el directorio raíz del proyecto.
* El directorio raíz del proyecto se usa para crear:
  * La ruta de acceso a los archivos de contenido que no son de código de la aplicación en el directorio raíz del proyecto.
  * La [raíz web](#web-root), normalmente la carpeta *wwwroot* en el directorio raíz del proyecto.

Se puede especificar una ruta raíz de contenido alternativa al [crear el host](#host). Para obtener más información, vea <xref:fundamentals/host/web-host#content-root>.

## <a name="web-root"></a>Raíz web

La raíz web es la ruta de acceso base a archivos de recursos públicos, que no son de código y estáticos, como:

* Hojas de estilo ( *.css* )
* JavaScript ( *.js* )
* Imágenes ( *.png* , *.jpg* )

De forma predeterminada, los archivos estáticos se atienden solo desde el directorio raíz web (y los subdirectorios).

El valor predeterminado de la ruta de acceso de la raíz web es *{raíz del contenido}/wwwroot* , pero se puede especificar una raíz web diferente [al crear el host](#host). Para obtener más información, vea [Raíz web](xref:fundamentals/host/web-host#web-root).

Evite la publicación de archivos en *wwwroot* con el elemento del proyecto [\<Content>](/visualstudio/msbuild/common-msbuild-project-items#content) del archivo de proyecto. En el ejemplo siguiente se impide la publicación de contenido en el directorio *wwwroot/local* y en los subdirectorios:

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

En los archivos de Razor ( *.cshtml* ), la virgulilla `~/` apunta a la raíz web. Una ruta de acceso que empieza por `~/` se conoce como *ruta de acceso virtual* .

Para obtener más información, vea <xref:fundamentals/static-files>.

::: moniker-end
