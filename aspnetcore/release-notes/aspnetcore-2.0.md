---
title: Novedades de ASP.NET Core 2.0
author: rick-anderson
description: Obtenga información sobre las nuevas características de ASP.NET Core 2.0.
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
uid: aspnetcore-2.0
ms.openlocfilehash: 9d8c33ef10825baa212466fe683d4c1dc582f7eb
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632465"
---
# <a name="whats-new-in-aspnet-core-20"></a>Novedades de ASP.NET Core 2.0

En este artículo se resaltan los cambios más importantes de ASP.NET Core 2.0, con vínculos a la documentación pertinente.

## <a name="no-locrazor-pages"></a>Razor Pages

Razor Pages es una nueva característica de ASP.NET Core MVC que facilita la codificación de escenarios centrados en páginas y hace que sea más productiva.

Para más información, vea la introducción y el tutorial:

* [Introducción a Razor Pages](xref:razor-pages/index)
* [Primeros pasos con Razor Pages](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a>Metapaquete de ASP.NET Core

Hay un nuevo metapaquete de ASP.NET Core que incluye todos los paquetes creados y que son compatibles con los equipos de ASP.NET Core y Entity Framework Core, junto con sus dependencias internas y de terceros. Ya no tiene que elegir características concretas de ASP.NET Core por paquete. Todas las características se incluyen en el paquete [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All). Las plantillas predeterminadas usan este paquete.

Para más información, vea [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0](xref:fundamentals/metapackage) (Metapaquete Microsoft.AspNetCore.All para ASP.NET Core 2.0).

## <a name="runtime-store"></a>Almacén en tiempo de ejecución

Las aplicaciones que usan el metapaquete `Microsoft.AspNetCore.All` pueden aprovechar automáticamente el nuevo almacén en tiempo de ejecución de .NET Core. El almacén contiene todos los recursos en tiempo de ejecución necesarios para ejecutar aplicaciones de ASP.NET Core 2.0. Al usar el metapaquete `Microsoft.AspNetCore.All`, no se implementa ningún recurso de los paquetes NuGet de ASP.NET Core referenciados con la aplicación, porque ya residen en el sistema de destino. Los recursos del almacén en tiempo de ejecución también se precompilan para mejorar el tiempo de inicio de la aplicación.

Para más información, vea [Runtime store](/dotnet/core/deploying/runtime-store) (Almacén en tiempo de ejecución).

## <a name="net-standard-20"></a>.NET Standard 2.0

Los paquetes de ASP.NET Core 2.0 tienen como destino .NET Standard 2.0. Se puede hacer referencia a los paquetes mediante otras bibliotecas de .NET Standard 2.0 y se pueden ejecutar en implementaciones compatibles con .NET Standard 2.0 de. NET, como .NET Core 2.0 y .NET Framework 4.6.1. 

El metapaquete `Microsoft.AspNetCore.All` tiene como destino únicamente .NET Core 2.0, ya que está pensado para usarse con el almacén en tiempo de ejecución de .NET Core 2.0.

## <a name="configuration-update"></a>Actualización de la configuración

En ASP.NET Core 2.0 se agrega de forma predeterminada una instancia `IConfiguration` al contenedor de servicios. La instancia `IConfiguration` del contenedor de servicios facilita que las aplicaciones recuperen los valores de configuración del contenedor.

Para información sobre el estado de la documentación planeada, vea este [problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3387).

## <a name="logging-update"></a>Actualización del registro

En ASP.NET Core 2.0, el registro se incorpora de forma predeterminada en el sistema de inserción de dependencias (DI). Debe agregar proveedores y configurar el filtrado en el archivo *Program.cs*, y no en el archivo *Startup.cs*. El `ILoggerFactory` predeterminado admite el filtrado de una forma que le permite usar un enfoque flexible para el filtrado de varios proveedores y el filtrado de proveedor específico.

Para más información, vea [Introduction to Logging](xref:fundamentals/logging/index) (Introducción al registro).

## <a name="authentication-update"></a>Actualización de la autenticación

Hay un nuevo modelo de autenticación que facilita la configuración de la autenticación de una aplicación mediante la inserción de dependencias.

Hay plantillas nuevas disponibles para configurar la autenticación de aplicaciones web y API web con [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).

Para información sobre el estado de la documentación planeada, vea este [problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3054).

## <a name="no-locidentity-update"></a>Actualización de Identity

Se ha facilitado la compilación de API web seguras mediante Identity de ASP.NET Core 2.0. Puede adquirir tokens de acceso para obtener acceso a las API web mediante la [Biblioteca de autenticación de Microsoft (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).

Para más información sobre los cambios de autenticación en la versión 2.0, vea los siguientes recursos:

* [Confirmación de las cuentas y recuperación de contraseñas en ASP.NET Core](xref:security/authentication/accconfirm)
* [Habilitar la generación de códigos QR para las aplicaciones de autenticación en ASP.NET Core](xref:security/authentication/identity-enable-qrcodes)
* [Migración de autenticación e Identity a ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a>Plantillas de SPA

Hay disponibles plantillas de proyectos de Single-Page Application (SPA) para Angular, Aurelia, Knockout.js, React.js y React.js con Redux. La plantilla de Angular se ha actualizado a Angular 4. Las plantillas de Angular y de React están disponibles de forma predeterminada. Para obtener información sobre cómo obtener las otras plantillas, vea [Creating a new SPA project](xref:client-side/spa-services#create-a-new-project) (Crear un proyecto de SPA). Para obtener información acerca de cómo crear una SPA en ASP.NET Core, vea <xref:client-side/spa-services>.

## <a name="kestrel-improvements"></a>Mejoras en Kestrel

El servidor web de Kestrel tiene nuevas características que lo hacen más adecuado como servidor con conexión a Internet. Se ha agregado una serie de opciones de configuración de restricción del servidor en la nueva propiedad `Limits` de la clase `KestrelServerOptions`. Agregue límites para:

* Las conexiones máximas de cliente
* El tamaño máximo del cuerpo de solicitud
* La velocidad mínima de los datos del cuerpo de solicitud.

Para más información, vea [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel) (Implementación del servidor web de Kestrel en ASP.NET Core).

## <a name="weblistener-renamed-to-httpsys"></a>WebListener pasa a denominarse HTTP.sys

Los paquetes `Microsoft.AspNetCore.Server.WebListener` y `Microsoft.Net.Http.Server` se han combinado en un nuevo paquete, `Microsoft.AspNetCore.Server.HttpSys`. Los espacios de nombres se han actualizado para que coincidan.

Para más información, vea [HTTP.sys web server implementation in ASP.NET Core](xref:fundamentals/servers/httpsys) (Implementaciones del servidor web de HTTP.sys en ASP.NET Core).

## <a name="enhanced-http-header-support"></a>Compatibilidad mejorada de los encabezados HTTP

Al usar MVC para transmitir un `FileStreamResult` o un `FileContentResult`, ahora tiene la opción de establecer una `ETag` o una fecha `LastModified` en el contenido que se transmite. Puede establecer estos valores en el contenido devuelto con un código similar al siguiente:

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

El archivo devuelto a los visitantes incluye los encabezados HTTP adecuados para los valores `ETag` y `LastModified`.

Si un visitante de la aplicación solicita el contenido con un encabezado de solicitud de intervalo, ASP.NET Core reconoce la solicitud y controla ese encabezado. Si el contenido solicitado se puede entregar de forma parcial, ASP.NET Core lo omite debidamente y solo devuelve el conjunto de bytes solicitado. No es necesario que escriba ningún controlador especial en los métodos para adaptar o controlar esta característica, ya que se controla automáticamente.

## <a name="hosting-startup-and-application-insights"></a>Inicio del hospedaje y Application Insights

Ahora, los entornos de hospedaje pueden insertar dependencias de paquetes adicionales y ejecutar código durante el inicio de la aplicación sin que la aplicación tenga que tomar una dependencia explícitamente o llamar a ningún método. Esta característica se puede usar para habilitar ciertos entornos y activar características únicas de ese entorno sin que la aplicación tenga que saberlo de antemano. 

En ASP.NET Core 2.0, esta característica se usa para habilitar automáticamente los diagnósticos de Application Insights al efectuar una depuración en Visual Studio y (tras la participación) al ejecutarse en Azure App Services. Como resultado, las plantillas del proyecto ya no agregan de forma predeterminada el código ni los paquetes de Application Insights.

Para información sobre el estado de la documentación planeada, vea este [problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3389).

## <a name="automatic-use-of-anti-forgery-tokens"></a>Uso automático de tokens antifalsificación

ASP.NET Core siempre ha ayudado a codificar en HTML el contenido de forma predeterminada, pero con la nueva versión estamos dando un paso más para impedir ataques de falsificación de solicitud entre sitios (CSRF). A partir de ahora, ASP.NET Core emitirá tokens antifalsificación de forma predeterminada y los validará en las páginas y acciones POST de formulario sin tener que aplicar ninguna configuración adicional.

Para obtener más información, vea <xref:security/anti-request-forgery>.

## <a name="automatic-precompilation"></a>Precompilación automática

La precompilación de vistas de Razor se habilita de forma predeterminada durante la publicación, lo que reduce el tamaño de salida de la publicación y el tiempo de inicio de la aplicación.

Para obtener más información, vea [Precompilación y compilación de vistas de Razor en ASP.NET Core](xref:mvc/views/view-compilation).

## <a name="no-locrazor-support-for-c-71"></a>Compatibilidad de Razor con C# 7.1

El motor de vistas de Razor se ha actualizado para poder funcionar con el nuevo compilador Roslyn. Incluye compatibilidad con características de C# 7.1, como las expresiones predeterminadas, los nombres de tupla inferidos y la coincidencia de patrones con genéricos. Para usar C# 7.1 en el proyecto, agregue la siguiente propiedad al archivo del proyecto y, luego, vuelva a cargar la solución:

```xml
<LangVersion>latest</LangVersion>
```

Para información sobre el estado de las características de C# 7.1, vea [el repositorio de GitHub para Roslyn](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).

## <a name="other-documentation-updates-for-20"></a>Otras actualizaciones de documentación para la versión 2.0

* [Perfiles de publicación de Visual Studio para el desarrollo de aplicaciones ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles)
* [Administración de claves](xref:security/data-protection/implementation/key-management)
* [Configurar la autenticación de Facebook](xref:security/authentication/facebook-logins)
* [Configurar la autenticación de Twitter](xref:security/authentication/twitter-logins)
* [Configurar la autenticación de Google](xref:security/authentication/google-logins)
* [Configurar la autenticación de la cuenta Microsoft](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a>Guía de migración

Para obtener instrucciones sobre cómo migrar aplicaciones de ASP.NET Core 1.x a ASP.NET Core 2.0, vea los siguientes recursos:

* [Migración de ASP.NET Core 1.x a ASP.NET Core 2.0](xref:migration/1x-to-2x/index)
* [Migración de autenticación e Identity a ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a>Información adicional

Para ver la lista completa de cambios, consulte las [notas de la versión de ASP.NET Core 2.0](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).

Para estar en contacto con el progreso y los planes del equipo de desarrollo de ASP.NET Core, sintonice [ASP.NET Community Standup](https://live.asp.net/).
