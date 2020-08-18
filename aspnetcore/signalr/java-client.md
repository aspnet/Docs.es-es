---
title: Cliente de ASP.NET Core SignalR Java
author: mikaelm12
description: Aprenda a usar el cliente de SignalR Java de ASP.net Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/java-client
ms.openlocfilehash: 36994c9e579dc365de543c9b27bc8b7bfd34c81c
ms.sourcegitcommit: dfea24471f4f3d7904faa92fe60c000853bddc3b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2020
ms.locfileid: "88504780"
---
# <a name="aspnet-core-no-locsignalr-java-client"></a><span data-ttu-id="7eed9-103">Cliente de ASP.NET Core SignalR Java</span><span class="sxs-lookup"><span data-stu-id="7eed9-103">ASP.NET Core SignalR Java client</span></span>

<span data-ttu-id="7eed9-104">Por [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="7eed9-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="7eed9-105">El cliente de Java permite la conexión a un SignalR servidor de ASP.net Core desde código Java, incluidas las aplicaciones Android.</span><span class="sxs-lookup"><span data-stu-id="7eed9-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="7eed9-106">Al igual que el [cliente JavaScript](xref:signalr/javascript-client) y el [cliente .net](xref:signalr/dotnet-client), el cliente Java permite recibir y enviar mensajes a un centro en tiempo real.</span><span class="sxs-lookup"><span data-stu-id="7eed9-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="7eed9-107">El cliente de Java está disponible en ASP.NET Core 2,2 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="7eed9-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="7eed9-108">La aplicación de consola de Java de ejemplo a la que se hace referencia en este artículo usa el SignalR cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="7eed9-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="7eed9-109">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7eed9-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-java-client-package"></a><span data-ttu-id="7eed9-110">Instalar el SignalR paquete de cliente de Java</span><span class="sxs-lookup"><span data-stu-id="7eed9-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="7eed9-111">El archivo jar *signalr-1.0.0* permite que los clientes se conecten a los SignalR concentradores.</span><span class="sxs-lookup"><span data-stu-id="7eed9-111">The *signalr-1.0.0* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="7eed9-112">Para buscar el número de versión del archivo JAR más reciente, consulte los resultados de la [búsqueda de Maven](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span><span class="sxs-lookup"><span data-stu-id="7eed9-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="7eed9-113">Si usa Gradle, agregue la siguiente línea a la `dependencies` sección del archivo *Build. Gradle* :</span><span class="sxs-lookup"><span data-stu-id="7eed9-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

<span data-ttu-id="7eed9-114">Si usa Maven, agregue las líneas siguientes dentro del `<dependencies>` elemento de su *pom.xml* archivo:</span><span class="sxs-lookup"><span data-stu-id="7eed9-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="7eed9-115">Conexión a un concentrador</span><span class="sxs-lookup"><span data-stu-id="7eed9-115">Connect to a hub</span></span>

<span data-ttu-id="7eed9-116">Para establecer un `HubConnection` , `HubConnectionBuilder` se debe usar.</span><span class="sxs-lookup"><span data-stu-id="7eed9-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="7eed9-117">La dirección URL del concentrador y el nivel de registro se pueden configurar al compilar una conexión.</span><span class="sxs-lookup"><span data-stu-id="7eed9-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="7eed9-118">Configure las opciones necesarias llamando a cualquiera de los `HubConnectionBuilder` métodos antes de `build` .</span><span class="sxs-lookup"><span data-stu-id="7eed9-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="7eed9-119">Inicie la conexión con `start` .</span><span class="sxs-lookup"><span data-stu-id="7eed9-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="7eed9-120">Llamar a métodos de Hub desde el cliente</span><span class="sxs-lookup"><span data-stu-id="7eed9-120">Call hub methods from client</span></span>

<span data-ttu-id="7eed9-121">Una llamada a `send` invoca un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="7eed9-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="7eed9-122">Pase el nombre del método de concentrador y los argumentos definidos en el método de concentrador a `send` .</span><span class="sxs-lookup"><span data-stu-id="7eed9-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> <span data-ttu-id="7eed9-123">La llamada a métodos de concentrador desde un cliente solo se admite cuando se usa el SignalR servicio de Azure en el modo *predeterminado* .</span><span class="sxs-lookup"><span data-stu-id="7eed9-123">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="7eed9-124">Para obtener más información, consulte preguntas más frecuentes [(repositorio de github de Azure signalr)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="7eed9-124">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="7eed9-125">Llamar a métodos de cliente desde el concentrador</span><span class="sxs-lookup"><span data-stu-id="7eed9-125">Call client methods from hub</span></span>

<span data-ttu-id="7eed9-126">`hubConnection.on`Se usa para definir métodos en el cliente a los que puede llamar el concentrador.</span><span class="sxs-lookup"><span data-stu-id="7eed9-126">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="7eed9-127">Defina los métodos después de la compilación, pero antes de iniciar la conexión.</span><span class="sxs-lookup"><span data-stu-id="7eed9-127">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="7eed9-128">Adición de registro</span><span class="sxs-lookup"><span data-stu-id="7eed9-128">Add logging</span></span>

<span data-ttu-id="7eed9-129">El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="7eed9-129">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="7eed9-130">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="7eed9-130">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="7eed9-131">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="7eed9-131">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="7eed9-132">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="7eed9-132">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="7eed9-133">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="7eed9-133">This can safely be ignored.</span></span>

## <a name="android-development-notes"></a><span data-ttu-id="7eed9-134">Notas de desarrollo de Android</span><span class="sxs-lookup"><span data-stu-id="7eed9-134">Android development notes</span></span>

<span data-ttu-id="7eed9-135">En lo que respecta a Android SDK la compatibilidad de las SignalR características de cliente, tenga en cuenta los siguientes elementos al especificar la versión de Android SDK de destino:</span><span class="sxs-lookup"><span data-stu-id="7eed9-135">With regards to Android SDK compatibility for the SignalR client features, consider the following items when specifying your target Android SDK version:</span></span>

* <span data-ttu-id="7eed9-136">El SignalR cliente de Java se ejecutará en el nivel de API de Android 16 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="7eed9-136">The SignalR Java Client will run on Android API Level 16 and later.</span></span>
* <span data-ttu-id="7eed9-137">La conexión a través del servicio de Azure SignalR requerirá el nivel de API de Android 20 y versiones posteriores, ya que el [ SignalR servicio de azure](/azure/azure-signalr/signalr-overview) requiere TLS 1,2 y no es compatible con conjuntos de cifrado basados en SHA-1.</span><span class="sxs-lookup"><span data-stu-id="7eed9-137">Connecting through the Azure SignalR Service will require Android API Level 20 and later because the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) requires TLS 1.2 and doesn't support SHA-1-based cipher suites.</span></span> <span data-ttu-id="7eed9-138">Android [ha agregado compatibilidad con los conjuntos de cifrado SHA-256 (y versiones posteriores)](https://developer.android.com/reference/javax/net/ssl/SSLSocket) en el nivel de API 20.</span><span class="sxs-lookup"><span data-stu-id="7eed9-138">Android [added support for SHA-256 (and above) cipher suites](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API Level 20.</span></span>

## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="7eed9-139">Configurar la autenticación de token de portador</span><span class="sxs-lookup"><span data-stu-id="7eed9-139">Configure bearer token authentication</span></span>

<span data-ttu-id="7eed9-140">En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un "generador de tokens de acceso" a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="7eed9-140">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="7eed9-141">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="7eed9-141">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="7eed9-142">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="7eed9-142">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a><span data-ttu-id="7eed9-143">Restricciones conocidas</span><span class="sxs-lookup"><span data-stu-id="7eed9-143">Known limitations</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="7eed9-144">Solo se admite el protocolo JSON.</span><span class="sxs-lookup"><span data-stu-id="7eed9-144">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="7eed9-145">No se admiten la reserva de transporte ni el transporte de eventos enviados del servidor.</span><span class="sxs-lookup"><span data-stu-id="7eed9-145">Transport fallback and the Server Sent Events transport aren't supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="7eed9-146">Solo se admite el protocolo JSON.</span><span class="sxs-lookup"><span data-stu-id="7eed9-146">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="7eed9-147">Solo se admite el transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="7eed9-147">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="7eed9-148">Todavía no se admite la transmisión por secuencias.</span><span class="sxs-lookup"><span data-stu-id="7eed9-148">Streaming isn't supported yet.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7eed9-149">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="7eed9-149">Additional resources</span></span>

* [<span data-ttu-id="7eed9-150">Referencia de API de Java</span><span class="sxs-lookup"><span data-stu-id="7eed9-150">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [<span data-ttu-id="7eed9-151">Documentación sin servidor del servicio de Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="7eed9-151">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
