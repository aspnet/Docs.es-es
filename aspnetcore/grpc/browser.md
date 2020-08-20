---
title: Uso de gRPC en aplicaciones de explorador
author: jamesnk
description: Obtenga información sobre cómo configurar servicios gRPC en ASP.NET Core a los que se puede llamar desde aplicaciones del explorador usando gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/30/2020
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
uid: grpc/browser
ms.openlocfilehash: 8d1f761731ab3840d009eba1ff5316808bafec40
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634415"
---
# <a name="use-grpc-in-browser-apps"></a>Uso de gRPC en aplicaciones de explorador

Por [James Newton-King](https://twitter.com/jamesnk)

 Obtenga información sobre cómo configurar un servicio gRPC de ASP.NET Core existente al que se puede llamar desde aplicaciones del explorador mediante el protocolo [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md). gRPC-Web permite a las aplicaciones de explorador de JavaScript y Blazor llamar a servicios gRPC. No se puede llamar a un servicio HTTP/2 gRPC desde una aplicación basada en el explorador. Los servicios gRPC hospedados en ASP.NET Core se pueden configurar para admitir gRPC-Web junto con HTTP/2 gRPC.


Para instrucciones sobre cómo agregar un servicio gRPC a una aplicación de ASP.NET Core existente, consulte [Incorporación de servicios gRPC a una aplicación de ASP.NET Core](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).

Para instrucciones sobre cómo crear un proyecto de gRPC, consulte <xref:tutorials/grpc/grpc-start>.

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a>gRPC-Web en ASP.NET Core frente a Envoy

Hay dos opciones para agregar gRPC-Web a una aplicación de ASP.NET Core:

* Compatibilidad con gRPC-Web junto con HTTP/2 gRPC en ASP.NET Core. Esta opción usa el middleware que el paquete `Grpc.AspNetCore.Web` proporciona.
* Use la compatibilidad con gRPC-Web del [proxy de Envoy](https://www.envoyproxy.io/) para traducir gRPC-Web a HTTP/2 gRPC. A continuación, la llamada traducida se reenvía a la aplicación ASP.NET Core.

Cada enfoque tiene ventajas y desventajas. Si el entorno de una aplicación ya usa Envoy como proxy, puede que tenga sentido usar Envoy para proporcionar compatibilidad con gRPC-Web. Para obtener una solución básica para gRPC-Web que solo requiera ASP.NET Core, `Grpc.AspNetCore.Web` es una buena elección.

## <a name="configure-grpc-web-in-aspnet-core"></a>Configuración de gRPC-Web en ASP.NET Core

Los servicios gRPC hospedados en ASP.NET Core se pueden configurar para admitir gRPC-Web junto con HTTP/2 gRPC. gRPC-Web no requiere ningún cambio en los servicios. La única modificación es la configuración de inicio.

Para habilitar gRPC-Web con un servicio gRPC de ASP.NET Core:

* Agregue una referencia al paquete [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web).
* Configure la aplicación para que use gRPC-Web, agregando para ello `UseGrpcWeb` y `EnableGrpcWeb` a *Startup.cs*:

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

El código anterior:

* Agrega el middleware de gRPC-Web, `UseGrpcWeb`, después del enrutamiento y antes de los puntos de conexión.
* Especifica que el método `endpoints.MapGrpcService<GreeterService>()` admite gRPC-Web con `EnableGrpcWeb`. 

Como alternativa, se puede configurar el middleware gRPC-Web de forma que todos los servicios admitan gRPC-Web de forma predeterminada y no se necesite `EnableGrpcWeb`. Especifique `new GrpcWebOptions { DefaultEnabled = true }` cuando se agregue el middleware.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> Hay un problema conocido que hace que gRPC-Web genere un error cuando está [hospedado en Http.sys](xref:fundamentals/servers/httpsys) en .NET Core 3.x.
>
> Una solución alternativa para que gRPC-Web funcione en Http.sys está disponible [aquí](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).

### <a name="grpc-web-and-cors"></a>gRPC-Web y CORS

La seguridad del explorador evita que una página web realice solicitudes a un dominio diferente del que atendió a dicha página web. Esta restricción se aplica a la hora de realizar llamadas de gRPC-Web con aplicaciones de explorador. Por ejemplo, una aplicación de explorador atendida por `https://www.contoso.com` no puede llamar a los servicios gRPC-Web hospedados en `https://services.contoso.com`. Para suavizar esta restricción, podemos recurrir al uso compartido de recursos entre orígenes (CORS).

Para permitir que una aplicación de explorador haga llamadas de gRPC-Web entre orígenes, configure [CORS en ASP.NET Core](xref:security/cors). Use la compatibilidad de CORS integrada y exponga los encabezados específicos de gRPC con <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A>.

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

El código anterior:

* Llama a `AddCors` para agregar los servicios CORS y configura una directiva CORS que expone los encabezados específicos de gRPC.
* Llama a `UseCors` para agregar el middleware de CORS después del enrutamiento y antes de los puntos de conexión.
* Especifica que el método `endpoints.MapGrpcService<GreeterService>()` admite CORS con `RequiresCors`.

### <a name="grpc-web-and-streaming"></a>gRPC-Web y streaming

El gRPC tradicional a través de HTTP/2 admite el streaming en todas las direcciones. gRPC-Web ofrece compatibilidad limitada para streaming:

* Los clientes del explorador gRPC-Web no admiten la llamada a métodos de streaming de cliente y streaming bidireccional.
* Los servicios gRPC de ASP.NET Core hospedados en Azure App Service e IIS no admiten streaming bidireccional.

Al usar gRPC-Web, solo se recomienda el uso de métodos unarios y métodos de streaming de servidor.

## <a name="call-grpc-web-from-the-browser"></a>Llamada a gRPC-Web desde el explorador

Las aplicaciones de explorador pueden usar gRPC-Web para llamar a servicios gRPC. Existen algunos requisitos y limitaciones a la hora de llamar a servicios gRPC con gRPC-Web desde el explorador:

* El servidor debe estar configurado para admitir gRPC-Web.
* No se pueden usar llamadas de streaming de cliente ni de streaming bidireccional. Sí se puede usar el streaming de servidor.
* Para llamar a servicios gRPC en otro dominio, hay que configurar [CORS](xref:security/cors) en el servidor.

### <a name="javascript-grpc-web-client"></a>Cliente gRPC-Web de JavaScript

Existe un cliente gRPC-Web de JavaScript. Para obtener instrucciones sobre cómo usar gRPC-Web en JavaScript, vea [Escribir código de cliente de JavaScript con gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>Configuración de gRPC-Web con el cliente gRPC de .NET

El cliente gRPC de .NET se puede configurar para realizar llamadas de gRPC-Web. Esto resulta útil en las aplicaciones [Blazor WebAssembly](xref:blazor/index#blazor-webassembly), que se hospedan en el explorador y tienen las mismas limitaciones HTTP de código JavaScript. Llamar a gRPC-Web con un cliente .NET es [igual que HTTP/2 gRPC](xref:grpc/client). La única modificación es cómo se crea el canal.

Para usar gRPC-Web:

* Agregue una referencia al paquete [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web).
* Asegúrese de que la referencia al paquete [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) sea 2.29.0 o superior.
* Configure el canal para que use `GrpcWebHandler`:

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

El código anterior:

* Configura un canal para que use gRPC-Web.
* Crea un cliente y realiza una llamada usando el canal.

`GrpcWebHandler` tiene las siguientes opciones de configuración:

* **InnerHandler**: elemento <xref:System.Net.Http.HttpMessageHandler> subyacente que realiza la solicitud HTTP de gRPC, por ejemplo, `HttpClientHandler`.
* **GrpcWebMode**: tipo de enumeración que especifica si el `Content-Type` de la solicitud HTTP gRPC es `application/grpc-web` o `application/grpc-web-text`.
    * `GrpcWebMode.GrpcWeb` configura el contenido que se va a enviar sin codificación. Valor predeterminado.
    * `GrpcWebMode.GrpcWebText` configura el contenido para que tenga codificación Base64. Esto es necesario en las llamadas de streaming de servidor en los exploradores.
* **HttpVersion**: elemento `Version` del protocolo HTTP que se usa para establecer [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) en la solicitud HTTP de gRPC subyacente. gRPC-Web no requiere que haya una versión específica y no invalida el valor predeterminado a menos que así se especifique.

> [!IMPORTANT]
> Los clientes de gRPC generados tienen métodos sincrónicos y asincrónicos para llamar a métodos unarios. Así, `SayHello` es sincrónico y `SayHelloAsync`, asincrónico. La llamada a un método sincrónico en una aplicación Blazor WebAssembly hace que la aplicación deje de responder. En Blazor WebAssembly, siempre se deben usar métodos asincrónicos.

## <a name="additional-resources"></a>Recursos adicionales

* [Proyecto de GitHub de gRPC para clientes web](https://github.com/grpc/grpc-web)
* <xref:security/cors>
