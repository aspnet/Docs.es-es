---
title: Migración de ASP.NET a ASP.NET Core
author: isaac2004
description: Obtenga instrucciones para migrar aplicaciones existentes de ASP.NET MVC o API web a ASP.NET Core.
ms.author: scaddie
ms.date: 10/18/2019
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
uid: migration/proper-to-2x/index
ms.openlocfilehash: 059ddc18d0c531efaba8aab916ddbb27b42b5e2c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053558"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a>Migración de ASP.NET a ASP.NET Core

Por [Isaac Levin](https://isaaclevin.com)

Este artículo sirve de guía de referencia para migrar aplicaciones de ASP.NET a ASP.NET Core.

## <a name="prerequisites"></a>Requisitos previos

[.NET Core SDK 2.2 o posterior](https://dotnet.microsoft.com/download)

## <a name="target-frameworks"></a>Versiones de .NET Framework de destino

Los proyectos de ASP.NET Core proporcionan a los desarrolladores la flexibilidad de usar la versión .NET Core, .NET Framework o ambas. Vea [Selección entre .NET Core y .NET Framework para aplicaciones de servidor](/dotnet/standard/choosing-core-framework-server) para determinar qué plataforma de destino es más adecuada.

Cuando se usa la versión .NET Framework, es necesario que los proyectos hagan referencia a paquetes de NuGet individuales.

Cuando se usa .NET Core, se pueden eliminar numerosas referencias explícitas del paquete gracias al [metapaquete](xref:fundamentals/metapackage-app) de ASP.NET Core. Instale el metapaquete `Microsoft.AspNetCore.App` en el proyecto:

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

Cuando se usa el metapaquete, con la aplicación no se implementa ningún paquete al que se hace referencia en el metapaquete. El almacén de tiempo de ejecución de .NET Core incluye estos activos que están compilados previamente para mejorar el rendimiento. Consulte [Metapaquete Microsoft.AspNetCore.App para ASP.NET Core](xref:fundamentals/metapackage-app) para más información.

## <a name="project-structure-differences"></a>Diferencias en la estructura de proyecto

El formato de archivo *.csproj* se ha simplificado en ASP.NET Core. Estos son algunos cambios importantes:

- La inclusión explícita de archivos no es necesaria para que se consideren parte del proyecto. Esto reduce el riesgo de conflictos al fusionar XML cuando se trabaja en equipos grandes.
- No hay referencias de GUID a otros proyectos, lo cual mejora la legibilidad del archivo.
- El archivo se puede editar sin descargarlo en Visual Studio:

    ![Edición de la opción de menú contextual CSPROJ en Visual Studio de 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a>Reemplazo del archivo Global.asax

ASP.NET Core introdujo un nuevo mecanismo para arrancar una aplicación. El punto de entrada para las aplicaciones ASP.NET es el archivo *Global.asax* . En el archivo *Global.asax* se controlan tareas como la configuración de enrutamiento y los registros de filtro y de área.

[!code-csharp[](samples/globalasax-sample.cs)]

Este enfoque acopla la aplicación y el servidor en el que está implementada de forma que interfiere con la implementación. En un esfuerzo por desacoplar, se introdujo [OWIN](https://owin.org/) para ofrecer una forma más limpia de usar varios marcos de trabajo de manera conjunta. OWIN proporciona una canalización para agregar solo los módulos necesarios. El entorno de hospedaje toma una función de [inicio](xref:fundamentals/startup) para configurar servicios y la canalización de solicitud de la aplicación. `Startup` registra un conjunto de middleware en la aplicación. Para cada solicitud, la aplicación llama a cada uno de los componentes de middleware con el puntero principal de una lista vinculada a un conjunto de controladores existente. Cada componente de middleware puede agregar uno o varios controladores a la canalización de control de la solicitud. Esto se consigue mediante la devolución de una referencia al controlador que ahora es el primero de la lista. Cada controlador se encarga de recordar e invocar el controlador siguiente en la lista. Con ASP.NET Core, el punto de entrada a una aplicación es `Startup` y ya no se tiene dependencia de *Global.asax* . Cuando utilice OWIN con .NET Framework, use algo parecido a lo siguiente como canalización:

[!code-csharp[](samples/webapi-owin.cs)]

Esto configura las rutas predeterminadas y tiene como valor predeterminado XmlSerialization a través de Json. Agregue otro middleware a esta canalización según sea necesario (carga de servicios, opciones de configuración, archivos estáticos, etcétera).

ASP.NET Core usa un enfoque similar, pero no depende de OWIN para controlar la entrada. En su lugar, se usa el método `Main` de *Program.cs* (similar a las aplicaciones de consola), y `Startup` se carga a través de ahí.

[!code-csharp[](samples/program.cs)]

`Startup` debe incluir un método `Configure`. En `Configure`, agregue el middleware necesario a la canalización. En el ejemplo siguiente (de la plantilla de sitio web predeterminada), los métodos de extensión configuran la canalización con compatibilidad para:

- Páginas de error
- Seguridad de transporte estricta de HTTP
- Redireccionamiento HTTP a HTTPS
- ASP.NET Core MVC

[!code-csharp[](samples/startup.cs)]

El host y la aplicación se han desacoplado, lo que proporciona la flexibilidad de pasar a una plataforma diferente en el futuro.

> [!NOTE]
> Para acceder a referencias más detalladas sobre el inicio de ASP.NET Core y middleware, consulte [Inicio de la aplicación en ASP.NET Core](xref:fundamentals/startup).

## <a name="store-configurations"></a>Almacenamiento de valores de configuración

ASP.NET admite el almacenamiento de valores de configuración. Estos valores de configuración se usan, por ejemplo, para admitir el entorno donde se implementan las aplicaciones. Antes se solían almacenar todos los pares de clave y valor personalizados en la sección `<appSettings>` del archivo *Web.config* :

[!code-xml[](samples/webconfig-sample.xml)]

Las aplicaciones leen esta configuración mediante la colección `ConfigurationManager.AppSettings` en el espacio de nombres `System.Configuration`:

[!code-csharp[](samples/read-webconfig.cs)]

ASP.NET Core puede almacenar datos de configuración para la aplicación en cualquier archivo y cargarlos como parte del arranque de middleware. El archivo predeterminado que se usa en las plantillas de proyecto es *appsettings.json* :

[!code-json[](samples/appsettings-sample.json)]

La carga de este archivo en una instancia de `IConfiguration` dentro de la aplicación se realiza en *Startup.cs* :

[!code-csharp[](samples/startup-builder.cs)]

La aplicación lee de `Configuration` para obtener la configuración:

[!code-csharp[](samples/read-appsettings.cs)]

Existen extensiones de este enfoque para lograr que el proceso sea más sólido, como el uso de [inserción de dependencias](xref:fundamentals/dependency-injection) para cargar un servicio con estos valores. El enfoque de la inserción de dependencias proporciona un conjunto fuertemente tipado de objetos de configuración.

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

> [!NOTE]
> Para acceder a referencias más detalladas sobre la configuración de ASP.NET Core, consulte [Configuración en ASP.NET Core](xref:fundamentals/configuration/index).

## <a name="native-dependency-injection"></a>Inserción de dependencias nativa

Un objetivo importante al compilar aplicaciones grandes y escalables es lograr el acoplamiento flexible de los componentes y los servicios. La [inserción de dependencias](xref:fundamentals/dependency-injection) es una técnica popular para lograrlo y se trata de un componente nativo de ASP.NET Core.

En las aplicaciones ASP.NET, los desarrolladores se sirven de una biblioteca de terceros para implementar la inserción de dependencias. Una de estas bibliotecas es [Unity](https://github.com/unitycontainer/unity), suministrada por Microsoft Patterns & Practices.

Un ejemplo de configuración de la inserción de dependencias con Unity consiste en implementar `IDependencyResolver` que encapsula un `UnityContainer`:

[!code-csharp[](samples/sample8.cs)]

Cree una instancia de `UnityContainer`, registre el servicio y establezca la resolución de dependencias de `HttpConfiguration` en la nueva instancia de `UnityResolver` para el contenedor:

[!code-csharp[](samples/sample9.cs)]

Inserte `IProductRepository` cuando sea necesario:

[!code-csharp[](samples/sample5.cs)]

Dado que la inserción de dependencia forma parte de ASP.NET Core, puede agregar el servicio en el método `ConfigureServices` de *Startup.cs* :

[!code-csharp[](samples/configure-services.cs)]

El repositorio se puede insertar en cualquier lugar, como ocurría con Unity.

> [!NOTE]
> Para más información sobre la inserción de dependencias, vea [Inserción de dependencias](xref:fundamentals/dependency-injection).

## <a name="serve-static-files"></a>Proporcionar archivos estáticos

Una parte importante del desarrollo web es la capacidad de trabajar con activos estáticos de cliente. Los ejemplos más comunes de archivos estáticos son HTML, CSS, JavaScript e imágenes. Estos archivos deben guardarse en la ubicación de publicación de la aplicación (o la red de entrega de contenido) y es necesario hacer referencia a ellos para que una solicitud los pueda cargar. Este proceso ha cambiado en ASP.NET Core.

En ASP.NET, los archivos estáticos se almacenan en directorios distintos y se hace referencia a ellos en las vistas.

En ASP.NET Core, los archivos estáticos se almacenan en la "raíz web" ( *&lt;raíz del contenido&gt;/wwwroot* ), a menos que se configuren de otra manera. Los archivos se cargan en la canalización de solicitud mediante la invocación del método de extensión `UseStaticFiles` desde `Startup.Configure`:

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> Si el destino es .NET Framework, debe instalarse el paquete de NuGet `Microsoft.AspNetCore.StaticFiles`.

Por ejemplo, el explorador puede acceder a un recurso de imagen en la carpeta *wwwroot/images* en una ubicación como `http://<app>/images/<imageFileName>`.

> [!NOTE]
> Para acceder a referencias más detalladas sobre cómo trabajar con archivos estáticos en ASP.NET Core, consulte [Archivos estáticos](xref:fundamentals/static-files).

## <a name="multi-value-no-loccookies"></a>cookies de varios valores

[cookies](xref:System.Web.HttpCookie.Values) de varios valores no se admiten en ASP.NET Core. Cree una cookie por valor.

## <a name="partial-app-migration"></a>Migración parcial de aplicaciones

Un enfoque para la migración parcial de aplicaciones consiste en crear una subaplicación de IIS y solo trasladar determinadas rutas de ASP.NET 4.x a ASP.NET Core, al tiempo que se conserva la estructura de la dirección URL de la aplicación. Por ejemplo, considere la estructura de dirección URL de la aplicación en el archivo *applicationHost.config* :

```xml
<sites>
    <site name="Default Web Site" id="1" serverAutoStart="true">
        <application path="/">
            <virtualDirectory path="/" physicalPath="D:\sites\MainSite\" />
        </application>
        <application path="/api" applicationPool="DefaultAppPool">
            <virtualDirectory path="/" physicalPath="D:\sites\netcoreapi" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:80:" />
            <binding protocol="https" bindingInformation="*:443:" sslFlags="0" />
        </bindings>
    </site>
    ...
</sites>
```

Estructura de directorios:

```
.
├── MainSite
│   ├── ...
│   └── Web.config
└── NetCoreApi
    ├── ...
    └── web.config
```

## <a name="bind-and-input-formatters"></a>[BIND] y formateadores de entrada

[Las versiones anteriores de ASP.NET](/aspnet/mvc/overview/getting-started/introduction/examining-the-edit-methods-and-edit-view) usaban el atributo `[Bind]` para protegerse frente a ataques de publicación excesiva. Los [formateadores de entrada](xref:mvc/models/model-binding#input-formatters) funcionan de manera diferente en ASP.NET Core. El atributo `[Bind]` ya no está diseñado para evitar la publicación excesiva cuando se usa con formateadores de entrada para analizar JSON o XML. Estos atributos afectan al enlace de modelos cuando el origen de datos es un formulario de datos publicado con el tipo de contenido `x-www-form-urlencoded`.

En el caso de las aplicaciones que envían información de JSON a los controladores y usan formateadores de entrada JSON para analizar los datos, se recomienda reemplazar el atributo `[Bind]` por un modelo de vista que coincida con las propiedades definidas por el atributo `[Bind]`.

## <a name="additional-resources"></a>Recursos adicionales

- [Traslado a .NET Core: bibliotecas](/dotnet/core/porting/libraries)
