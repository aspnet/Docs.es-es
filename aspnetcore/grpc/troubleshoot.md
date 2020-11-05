---
title: Solución de problemas de gRPC en .NET Core
author: jamesnk
description: Solución de problemas al usar gRPC en .NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/09/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: grpc/troubleshoot
ms.openlocfilehash: cbce85caf7ba792253ba62c6be084c8905acd00f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058719"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="61721-103">Solución de problemas de gRPC en .NET Core</span><span class="sxs-lookup"><span data-stu-id="61721-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="61721-104">Por [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="61721-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="61721-105">En este documento se describen los problemas que suelen surgir al desarrollar aplicaciones de gRPC en .NET.</span><span class="sxs-lookup"><span data-stu-id="61721-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="61721-106">Falta de coincidencia entre la configuración de cliente y servicio de SSL/TLS</span><span class="sxs-lookup"><span data-stu-id="61721-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="61721-107">La plantilla y los ejemplos de gRPC usan [Seguridad de la capa de transporte (TLS)](https://tools.ietf.org/html/rfc5246) de forma predeterminada para proteger los servicios gRPC.</span><span class="sxs-lookup"><span data-stu-id="61721-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="61721-108">Los clientes de gRPC deben usar una conexión segura para llamar correctamente a los servicios gRPC protegidos.</span><span class="sxs-lookup"><span data-stu-id="61721-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="61721-109">Puede comprobar si el servicio gRPC de ASP.NET Core usa TLS en los registros que se escriben al iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="61721-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="61721-110">El servicio escuchará en un punto de conexión HTTPS:</span><span class="sxs-lookup"><span data-stu-id="61721-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="61721-111">El cliente de .NET Core debe usar `https` en la dirección del servidor para realizar llamadas con una conexión segura:</span><span class="sxs-lookup"><span data-stu-id="61721-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="61721-112">Todas las implementaciones de cliente de gRPC admiten TLS.</span><span class="sxs-lookup"><span data-stu-id="61721-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="61721-113">Los clientes de gRPC de otros lenguajes de programación normalmente requieren que el canal esté configurado con `SslCredentials`.</span><span class="sxs-lookup"><span data-stu-id="61721-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="61721-114">`SslCredentials` especifica el certificado que usará el cliente, y debe usarse en lugar de credenciales no seguras.</span><span class="sxs-lookup"><span data-stu-id="61721-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="61721-115">Para obtener ejemplos de configuración de las distintas implementaciones de cliente de gRPC para usar TLS, vea [Autenticación en gRPC](https://www.grpc.io/docs/guides/auth/).</span><span class="sxs-lookup"><span data-stu-id="61721-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="61721-116">Llamada a un servicio gRPC con un certificado no válido o que no es de confianza</span><span class="sxs-lookup"><span data-stu-id="61721-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="61721-117">El cliente de gRPC de .NET requiere que el servicio tenga un certificado de confianza.</span><span class="sxs-lookup"><span data-stu-id="61721-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="61721-118">Cuando se llama a un servicio gRPC sin un certificado de confianza, se devuelve el siguiente mensaje de error:</span><span class="sxs-lookup"><span data-stu-id="61721-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="61721-119">Excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="61721-119">Unhandled exception.</span></span> <span data-ttu-id="61721-120">System.Net.Http.HttpRequestException: No se ha podido establecer la conexión SSL, vea la excepción interna.</span><span class="sxs-lookup"><span data-stu-id="61721-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="61721-121">---> System.Security.Authentication.AuthenticationException: El certificado remoto no es válido según el procedimiento de validación.</span><span class="sxs-lookup"><span data-stu-id="61721-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="61721-122">Puede aparecer este error si está probando la aplicación de forma local y el certificado de desarrollo HTTPS de ASP.NET Core no es de confianza.</span><span class="sxs-lookup"><span data-stu-id="61721-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="61721-123">Para instrucciones sobre cómo corregir este problema, consulte [Confiar en el certificado de desarrollo de ASP.NET Core HTTPS en Windows y macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="61721-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="61721-124">Si está llamando a un servicio gRPC en otro equipo y no puede confiar en el certificado, el cliente de gRPC se puede configurar para que omita el certificado no válido.</span><span class="sxs-lookup"><span data-stu-id="61721-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="61721-125">En el código siguiente se usa [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) para permitir llamadas sin un certificado de confianza:</span><span class="sxs-lookup"><span data-stu-id="61721-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

```csharp
var httpHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpHandler = httpHandler });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> <span data-ttu-id="61721-126">Los certificados que no son de confianza solo se deben usar durante el desarrollo de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="61721-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="61721-127">Las aplicaciones de producción siempre deben usar certificados válidos.</span><span class="sxs-lookup"><span data-stu-id="61721-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="61721-128">Llamada a servicios gRPC no seguros con el cliente de .NET Core</span><span class="sxs-lookup"><span data-stu-id="61721-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="61721-129">Cuando una aplicación usa .NET Core 3.x, se requiere una configuración adicional para llamar a servicios gRPC no seguros con el cliente de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="61721-129">When an app is using .NET Core 3.x, additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="61721-130">El cliente de gRPC debe establecer el modificador `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` en `true` y usar `http` en la dirección del servidor:</span><span class="sxs-lookup"><span data-stu-id="61721-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="61721-131">Las aplicaciones de .NET 5 no necesitan configuración adicional, pero para llamar a los servicios de gRPC no seguros deben usar la versión 2.32.0 o posterior de `Grpc.Net.Client`.</span><span class="sxs-lookup"><span data-stu-id="61721-131">.NET 5 apps don't need additional configuration, but to call insecure gRPC services they must use `Grpc.Net.Client` version 2.32.0 or later.</span></span>

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="61721-132">No se puede iniciar la aplicación gRPC de ASP.NET Core en macOS</span><span class="sxs-lookup"><span data-stu-id="61721-132">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="61721-133">Kestrel no admite HTTP/2 con TLS en macOS ni en versiones anteriores de Windows, como Windows 7.</span><span class="sxs-lookup"><span data-stu-id="61721-133">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="61721-134">La plantilla y los ejemplos de gRPC de ASP.NET Core usan TLS de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="61721-134">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="61721-135">Al intentar iniciar el servidor de gRPC, verá el siguiente mensaje de error:</span><span class="sxs-lookup"><span data-stu-id="61721-135">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="61721-136">No se puede enlazar a https://localhost:5001 en la interfaz de bucle invertido de IPv4: "No se admite HTTP/2 a través de TLS en macOS debido a la falta de compatibilidad con ALPN".</span><span class="sxs-lookup"><span data-stu-id="61721-136">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="61721-137">Para solucionar este problema, configure Kestrel y el cliente de gRPC para que use HTTP/2 *sin* TLS.</span><span class="sxs-lookup"><span data-stu-id="61721-137">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="61721-138">Esto solo debe realizarse durante el desarrollo.</span><span class="sxs-lookup"><span data-stu-id="61721-138">You should only do this during development.</span></span> <span data-ttu-id="61721-139">Si no se usa TLS, se enviarán mensajes gRPC sin cifrado.</span><span class="sxs-lookup"><span data-stu-id="61721-139">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="61721-140">Kestrel debe configurar un punto de conexión HTTP/2 sin TLS en *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="61721-140">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs* :</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="61721-141">Si un punto de conexión HTTP/2 se configura sin TLS, el valor [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) del punto de conexión debe establecerse en `HttpProtocols.Http2`.</span><span class="sxs-lookup"><span data-stu-id="61721-141">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="61721-142">`HttpProtocols.Http1AndHttp2` no se puede usar porque se requiere TLS para negociar HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="61721-142">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="61721-143">Sin TLS, se usa HTTP/1.1 de forma predeterminada para todas las conexiones al punto de conexión y se produce un error en las llamadas a gRPC.</span><span class="sxs-lookup"><span data-stu-id="61721-143">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="61721-144">El cliente de gRPC también debe estar configurado para no usar TLS.</span><span class="sxs-lookup"><span data-stu-id="61721-144">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="61721-145">Para obtener más información, consulte la sección [Llamada a servicios gRPC no seguros con el cliente de .NET Core](#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="61721-145">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="61721-146">Solo se debe usar HTTP/2 sin TLS durante el desarrollo de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="61721-146">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="61721-147">Las aplicaciones de producción siempre deben usar seguridad de transporte.</span><span class="sxs-lookup"><span data-stu-id="61721-147">Production apps should always use transport security.</span></span> <span data-ttu-id="61721-148">Para obtener más información, vea [Consideraciones de seguridad en gRPC para ASP.NET Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="61721-148">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="61721-149">Recursos de C# para gRPC no generados a partir de archivos .proto</span><span class="sxs-lookup"><span data-stu-id="61721-149">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="61721-150">Para generar código de gRPC de clientes concretos y clases base de servicio es necesario hacer referencia a los archivos y las herramientas de Protobuf desde un proyecto.</span><span class="sxs-lookup"><span data-stu-id="61721-150">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="61721-151">Debe incluir:</span><span class="sxs-lookup"><span data-stu-id="61721-151">You must include:</span></span>

* <span data-ttu-id="61721-152">Los archivos *.proto* que quiere usar en el grupo de elementos `<Protobuf>`.</span><span class="sxs-lookup"><span data-stu-id="61721-152">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="61721-153">El proyecto debe hacer referencia a los [archivos *.proto* importados](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions).</span><span class="sxs-lookup"><span data-stu-id="61721-153">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="61721-154">Una referencia de paquete al paquete de herramientas de gRPC [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="61721-154">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="61721-155">Para obtener más información sobre la generación de recursos de C# para gRPC, consulte <xref:grpc/basics>.</span><span class="sxs-lookup"><span data-stu-id="61721-155">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="61721-156">Una aplicación web de ASP.NET Core que hospeda servicios gRPC solo necesita la clase base de servicio generada:</span><span class="sxs-lookup"><span data-stu-id="61721-156">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="61721-157">Una aplicación cliente de gRPC que realiza llamadas a gRPC solo necesita el cliente concreto generado:</span><span class="sxs-lookup"><span data-stu-id="61721-157">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="61721-158">Imposibilidad para los proyectos de WPF de generar recursos de C# para gRPC a partir de archivos .proto</span><span class="sxs-lookup"><span data-stu-id="61721-158">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="61721-159">Los proyectos de WPF tienen un [problema conocido](https://github.com/dotnet/wpf/issues/810) que impide que la generación de código de gRPC se realice correctamente.</span><span class="sxs-lookup"><span data-stu-id="61721-159">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="61721-160">Los tipos de gRPC generados en un proyecto de WPF haciendo referencia a `Grpc.Tools` y a archivos *.proto* producirán errores de compilación cuando se usen:</span><span class="sxs-lookup"><span data-stu-id="61721-160">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="61721-161">Error CS0246: No se ha encontrado el tipo o el nombre del espacio de nombres "MyGrpcServices" (¿falta una directiva "using" o una referencia de ensamblado?).</span><span class="sxs-lookup"><span data-stu-id="61721-161">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="61721-162">Para solucionar este problema:</span><span class="sxs-lookup"><span data-stu-id="61721-162">You can workaround this issue by:</span></span>

1. <span data-ttu-id="61721-163">Cree un proyecto de biblioteca de clases .NET Core.</span><span class="sxs-lookup"><span data-stu-id="61721-163">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="61721-164">En el nuevo proyecto, agregue referencias para habilitar la [generación de código de C# a partir de archivos *\*.proto*](xref:grpc/basics#generated-c-assets):</span><span class="sxs-lookup"><span data-stu-id="61721-164">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="61721-165">Agregue una referencia de paquete al paquete [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="61721-165">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="61721-166">Agregue archivos *\*.proto* al grupo de elementos `<Protobuf>`.</span><span class="sxs-lookup"><span data-stu-id="61721-166">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="61721-167">En la aplicación de WPF, agregue una referencia al nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="61721-167">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="61721-168">La aplicación de WPF puede usar los tipos generados por gRPC a partir del nuevo proyecto de biblioteca de clases.</span><span class="sxs-lookup"><span data-stu-id="61721-168">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
