---
title: Middleware de reescritura de URL en ASP.NET Core
author: rick-anderson
description: Aprenda a reescribir y redireccionar URL con el middleware de reescritura de URL en aplicaciones ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/16/2019
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
uid: fundamentals/url-rewriting
ms.openlocfilehash: e7bd5f4d61661dd23eb0907f896d0d32b7799aac
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061306"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a>Middleware de reescritura de URL en ASP.NET Core

Por [Mikael Mengistu](https://github.com/mikaelm12)

::: moniker range=">= aspnetcore-3.0"

En este documento se ofrece una introducción a la reescritura de URL e instrucciones sobre cómo usar el middleware de reescritura de URL en aplicaciones ASP.NET Core.

La reescritura de URL consiste en modificar varias URL de solicitud basadas en una o varias reglas predefinidas. La reescritura de URL crea una abstracción entre las ubicaciones de recursos y sus direcciones para que las ubicaciones y las direcciones no estén estrechamente vinculadas. La reescritura de URL es útil en varios escenarios para:

* Mover o reemplazar recursos del servidor de forma temporal o permanente a la vez que se mantienen localizadores estables para esos recursos.
* Dividir el procesamiento de solicitudes entre otras aplicaciones o entre áreas de una aplicación.
* Quitar, agregar o reorganizar segmentos de dirección URL en solicitudes entrantes.
* Optimizar direcciones URL públicas para la optimización del motor de búsqueda (SEO).
* Permitir el uso de direcciones URL públicas descriptivas para ayudar a los visitantes a predecir el contenido devuelto mediante la solicitud de un recurso.
* Redirigir solicitudes no seguras a puntos de conexión seguros.
* Evitar la vinculación activa, en la que un sitio externo usa un recurso estático hospedado en otro sitio mediante la vinculación del recurso a su propio contenido.

> [!NOTE]
> La reescritura de URL puede reducir el rendimiento de una aplicación. Cuando sea factible, limite el número y la complejidad de las reglas.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="url-redirect-and-url-rewrite"></a>Redireccionamiento y reescritura de URL

La diferencia entre los términos *redirección de URL* y *reescritura de URL* es sutil, pero tiene importantes implicaciones para proporcionar recursos a los clientes. El middleware de reescritura de URL de ASP.NET Core es capaz de satisfacer las necesidades de ambos.

Una *redirección de URL* implica una operación del lado cliente, donde al cliente se le indica que acceda a un recurso en una dirección distinta a la que ha solicitado originalmente. Esto requiere un recorrido de ida y vuelta al servidor. La URL de redireccionamiento que se devuelve al cliente aparece en la barra de direcciones del explorador cuando el cliente realiza una nueva solicitud para el recurso.

Si `/resource` se *redirige* a `/different-resource`, el servidor responde que el cliente debe obtener el recurso en `/different-resource` con un código de estado que indica que la redirección es temporal o permanente.

![Se cambió temporalmente un punto de conexión del servicio WebAPI de la versión 1 (v1) a la versión 2 (v2) en el servidor. Un cliente realiza una solicitud al servicio en la ruta de acceso de la versión 1 /v1/api. El servidor devuelve una respuesta 302 (Encontrado) con la nueva ruta temporal para el servicio en la versión 2 /v2/api. El cliente realiza una segunda solicitud al servicio en la URL de redireccionamiento. El servidor responde con un código de estado 200 (Correcto).](url-rewriting/_static/url_redirect.png)

Al redirigir las solicitudes a otra dirección URL, se indica si la redirección es permanente o temporal mediante la especificación del código de estado con la respuesta:

* El código de estado *301 - Movido definitivamente* se usa cuando el recurso tiene una nueva dirección URL permanente y se quiere indicar al cliente que todas las solicitudes futuras para el recurso deben usar la nueva dirección URL. *El cliente puede almacenar en caché la respuesta cuando se recibe un código de estado 301.*

* El código de estado *302: encontrado* se usa cuando el redireccionamiento es temporal o en general está sujeto a cambios. El código de estado 302 indica al cliente que no almacene la dirección URL y la use en el futuro.

Para obtener más información sobre los códigos de estado, consulte [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (RFC 2616: definiciones de los códigos de estado).

La *reescritura de URL* es una operación del lado servidor que proporciona un recurso desde una dirección de recursos distinta a la que el cliente ha solicitado. La reescritura de una dirección URL no requiere un recorrido de ida y vuelta al servidor. La dirección URL reescrita no se devuelve al cliente y no aparece en la barra de direcciones del explorador.

Si `/resource` se *reescribe* como `/different-resource`, el servidor la obtiene *internamente* y devuelve el recurso en `/different-resource`.

Aunque es posible que el cliente pueda recuperar el recurso en la dirección URL reescrita, no se le informa de que el recurso existe en la dirección URL reescrita cuando realiza su solicitud y recibe la respuesta.

![Se cambió un punto de conexión del servicio WebAPI de la versión 1 (v1) a la versión 2 (v2) en el servidor. Un cliente realiza una solicitud al servicio en la ruta de acceso de la versión 1 /v1/api. La URL de la solicitud se reescribe para acceder al servicio en la ruta de acceso de la versión 2 /v2/api. El servicio responde al cliente con un código de estado 200 (Correcto).](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a>Aplicación de ejemplo de reescritura de URL

Puede explorar las características del middleware de reescritura de URL con la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/). La aplicación emplea las reglas de redireccionamiento y reescritura, y muestra la URL redirigida o reescrita para varios escenarios.

## <a name="when-to-use-url-rewriting-middleware"></a>Cuándo usar el middleware de reescritura de URL

Use el middleware de reescritura de URL cuando no pueda usar los enfoques siguientes:

* [Módulo de reescritura de URL con IIS en Windows Server](https://www.iis.net/downloads/microsoft/url-rewrite)
* [Módulo mod_rewrite de Apache en el servidor Apache](https://httpd.apache.org/docs/2.4/rewrite/)
* [Reescritura de URL en Nginx](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

Además, use el middleware cuando la aplicación se hospede en el [servidor HTTP.sys](xref:fundamentals/servers/httpsys) (anteriormente denominado WebListener).

Las principales razones para usar la tecnologías de reescritura de URL basadas en servidor en IIS, Apache y Nginx son las siguientes:

* El middleware no es compatible con todas las características de estos módulos.

  Algunas de las características de los módulos de servidor no funcionan con proyectos de ASP.NET Core, como las restricciones `IsFile` y `IsDirectory` del módulo de reescritura de IIS. En estos casos, es mejor usar el middleware.
* El rendimiento del middleware probablemente no coincida con el de los módulos.

  La única manera de saber con certeza con qué enfoque el rendimiento disminuye más, o si la disminución de este es insignificante, es mediante una comparación.

## <a name="package"></a>Package

El middleware de reescritura de URL está disponible en el paquete [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite), incluido implícitamente en las aplicaciones ASP.NET Core.

## <a name="extension-and-options"></a>Extensión y opciones

Establezca las reglas de reescritura y redirección de URL mediante la creación de una instancia de la clase [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) con métodos de extensión para cada una de las reglas de reescritura. Encadene varias reglas en el orden que quiera procesarlas. Las `RewriteOptions` se pasan al middleware de reescritura de URL cuando se agrega a la canalización de solicitudes con <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a>Redirigir solicitudes que distintas de www a www

Hay tres opciones que permiten a la aplicación redirigir solicitudes distintas de `www` a `www`:

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: redirige permanentemente la solicitud al subdominio `www` si la solicitud es distinta de `www`. Se redirige con un código de estado [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect).

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: redirige la solicitud al subdominio `www` si la solicitud de entrada es distinta de `www`. Se redirige con un código de estado [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect). Una sobrecarga permite proporcionar el código de estado para la respuesta. Use un campo de la clase <xref:Microsoft.AspNetCore.Http.StatusCodes> para una asignación de código de estado.

### <a name="url-redirect"></a>Redirección de URL

Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> para redirigir las solicitudes. El primer parámetro contiene la expresión regular para hacer coincidir la ruta de acceso de la URL entrante. El segundo parámetro es la cadena de reemplazo. El tercer parámetro, si está presente, especifica el código de estado. Si no se especifica el código de estado, el valor predeterminado es *302 - Encontrado* , lo que indica que el recurso se ha movido o reemplazado temporalmente.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

En un explorador con herramientas de desarrollo habilitadas, realice una solicitud a la aplicación de ejemplo con la ruta de acceso `/redirect-rule/1234/5678`. La expresión regular coincide con la ruta de acceso de la solicitud en `redirect-rule/(.*)` y la ruta de acceso se reemplaza con `/redirected/1234/5678`. La dirección URL de redireccionamiento se devuelve al cliente con un código de estado *302 - Encontrado*. El explorador realiza una solicitud nueva en la URL de redireccionamiento, que aparece en la barra de direcciones del explorador. Como ninguna de las reglas de la aplicación de ejemplo coincide con la dirección URL de redireccionamiento:

* La segunda solicitud recibe una respuesta *200: correcto* de la aplicación.
* En el cuerpo de la respuesta se muestra la dirección URL de redireccionamiento.

Se realiza un recorrido de ida y vuelta al servidor cuando se *redirige* una dirección URL.

> [!WARNING]
> Tenga cuidado al establecer las reglas de redirección. Las reglas de redirección se evalúan en cada solicitud enviada a la aplicación, incluso después de una redirección. Es fácil crear por error un *bucle de redirecciones infinitas*.

Solicitud original: `/redirect-rule/1234/5678`

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_redirect.png)

La parte de la expresión incluida entre paréntesis se denomina *grupo de capturas*. El punto (`.`) de la expresión significa *buscar cualquier carácter*. El asterisco (`*`) indica *buscar el carácter precedente cero o más veces*. Por tanto, el grupo de capturas `(.*)` busca los dos últimos segmentos de la ruta de acceso de la URL, `1234/5678`. Cualquier valor que se proporcione en la URL de la solicitud después de `redirect-rule/` es capturado por este grupo de capturas único.

En la cadena de reemplazo, se insertan los grupos capturados en la cadena con el signo de dólar (`$`) seguido del número de secuencia de la captura. Se obtiene el valor del primer grupo de capturas con `$1`, el segundo con `$2`, y así siguen en secuencia para los grupos de capturas de la expresión regular. Solo hay un grupo capturado en la expresión regular de la regla de redirección de la aplicación de ejemplo, por lo que solo hay un grupo insertado en la cadena de reemplazo, que es `$1`. Cuando se aplica la regla, la URL se convierte en `/redirected/1234/5678`.

### <a name="url-redirect-to-a-secure-endpoint"></a>Redirección de URL a un punto de conexión segura

Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> para redirigir las solicitudes HTTP al mismo host y ruta de acceso mediante el protocolo HTTPS. Si no se proporciona el código de estado, el middleware muestra de forma predeterminada *302 - Encontrado*. Si no se proporciona el puerto:

* El middleware tiene como valor predeterminado `null`.
* El esquema cambia a `https` (protocolo HTTPS), y el cliente accede al recurso en el puerto 443.

En el ejemplo siguiente se muestra cómo establecer el código de estado en *301 - Movido definitivamente* y cambiar el puerto a 5001.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> para redirigir las solicitudes no seguras al mismo host y ruta de acceso mediante el protocolo HTTPS seguro en el puerto 443. El middleware establece el código de estado en *301 - Movido definitivamente*.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> Al redirigir a un punto de conexión seguro sin la necesidad de reglas de redirección adicionales, se recomienda usar el Middleware de redirección de HTTPS. Para más información, vea el tema [Aplicación de HTTPS](xref:security/enforcing-ssl#require-https).

La aplicación de ejemplo es capaz de mostrar cómo usar `AddRedirectToHttps` o `AddRedirectToHttpsPermanent`. Agregue el método de extensión a `RewriteOptions`. Realice una solicitud poco segura a la aplicación en cualquier URL. Descarte la advertencia de seguridad del explorador que indica que el certificado autofirmado no es de confianza o cree una excepción para confiar en el certificado en cuestión.

Solicitud original mediante `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_redirect_to_https.png)

Solicitud original mediante `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>Reescritura de URL

Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> para crear una regla para reescribir URL. El primer parámetro contiene la expresión regular para buscar coincidencias en la ruta de dirección de URL entrante. El segundo parámetro es la cadena de reemplazo. El tercer parámetro, `skipRemainingRules: {true|false}`, indica al middleware si al aplicar la regla actual tiene que omitir o no alguna regla de redirección adicional.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

Solicitud original: `/rewrite-rule/1234/5678`

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_rewrite.png)

El acento circunflejo (`^`) al principio de la expresión significa que la búsqueda de coincidencias empieza al principio de la ruta de dirección URL.

En el ejemplo anterior de la regla de redireccionamiento (`redirect-rule/(.*)`), no hay ningún acento circunflejo (`^`) al principio de la expresión regular. Por tanto, cualquier carácter puede preceder a `redirect-rule/` en la ruta de acceso para que la coincidencia sea correcta.

| Ruta de acceso                               | Coincidir con |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | Sí   |
| `/my-cool-redirect-rule/1234/5678` | Sí   |
| `/anotherredirect-rule/1234/5678`  | Sí   |

La regla de reescritura, `^rewrite-rule/(\d+)/(\d+)`, solo encuentra rutas de acceso que empiezan con `rewrite-rule/`. En la tabla siguiente, observe la diferencia de coincidencia.

| Ruta de acceso                              | Coincidir con |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | Sí   |
| `/my-cool-rewrite-rule/1234/5678` | No    |
| `/anotherrewrite-rule/1234/5678`  | No    |

Después de la parte `^rewrite-rule/` de la expresión, hay dos grupos de captura, `(\d+)/(\d+)`. `\d` significa *buscar un dígito (número)* . El signo más (`+`) significa *buscar uno o más de los caracteres anteriores*. Por tanto, la URL debe contener un número seguido de una barra diagonal, seguida de otro número. Estos grupos de capturas se insertan en la URL de reescritura como `$1` y `$2`. La cadena de reemplazo de la regla de reescritura coloca los grupos capturados en la cadena de consulta. La ruta de acceso solicitada de `/rewrite-rule/1234/5678` se reescribe para obtener el recurso en `/rewritten?var1=1234&var2=5678`. Si una cadena de consulta está presente en la solicitud original, se conserva cuando se reescribe la dirección URL.

No hay ningún recorrido de ida y vuelta al servidor para obtener el recurso. Si el recurso existe, se captura y se devuelve al cliente con un código de estado *200 - Correcto*. Como el cliente no se redirige, la dirección URL no cambia en la barra de direcciones del explorador. Los clientes no pueden detectar que se ha producido una operación de reescritura de URL en el servidor.

> [!NOTE]
> Use `skipRemainingRules: true` siempre que sea posible, ya que las reglas de coincidencia consumen muchos recursos y aumentan el tiempo de respuesta de aplicación. Para obtener la respuesta más rápida de la aplicación:
>
> * Ordene las reglas de reescritura desde la que coincida con más frecuencia a la que coincida con menos frecuencia.
> * Omita el procesamiento de las reglas restantes cuando se produzca una coincidencia; no es necesario ningún procesamiento de reglas adicional.

### <a name="apache-mod_rewrite"></a>mod_rewrite de Apache

Aplique reglas mod_rewrite de Apache con <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>. Asegúrese de que el archivo de reglas se implementa con la aplicación. Para obtener más información y ejemplos de reglas mod_rewrite, vea [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/) (mod_rewrite de Apache).

Se usa <xref:System.IO.StreamReader> para leer las reglas del archivo de reglas *ApacheModRewrite.txt* :

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

La aplicación de ejemplo redirige las solicitudes de `/apache-mod-rules-redirect/(.\*)` a `/redirected?id=$1`. El código de estado de la respuesta es *302: Encontrado*.

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

Solicitud original: `/apache-mod-rules-redirect/1234`

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_apache_mod_redirect.png)

El middleware admite las siguientes variables de servidor mod_rewrite de Apache:

* CONN_REMOTE_ADDR
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_FORWARDED
* HTTP_HOST
* HTTP_REFERER
* HTTP_USER_AGENT
* HTTPS
* IPV6
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_METHOD
* REQUEST_SCHEME
* REQUEST_URI
* SCRIPT_FILENAME
* SERVER_ADDR
* SERVER_PORT
* SERVER_PROTOCOL
* TIME
* TIME_DAY
* TIME_HOUR
* TIME_MIN
* TIME_MON
* TIME_SEC
* TIME_WDAY
* TIME_YEAR

### <a name="iis-url-rewrite-module-rules"></a>Reglas del Módulo URL Rewrite para IIS

Para usar el mismo conjunto de reglas que se aplica al módulo URL Rewrite para IIS, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>. Asegúrese de que el archivo de reglas se implementa con la aplicación. No dirija el middleware para que use el archivo *web.config* de la aplicación cuando se ejecute en Windows Server IIS. Con IIS, estas reglas se deben almacenar fuera del archivo *web.config* de la aplicación para evitar conflictos con el Módulo URL Rewrite para IIS. Para obtener más información y ejemplos de reglas del Módulo URL Rewrite para IIS, vea [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) (Uso del Módulo URL Rewrite 2.0) y [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference) (Referencia de configuración del Módulo URL Rewrite).

Se usa <xref:System.IO.StreamReader> para leer las reglas del archivo de reglas *IISUrlRewrite.xml* :

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

La aplicación de ejemplo reescribe las solicitudes de `/iis-rules-rewrite/(.*)` a `/rewritten?id=$1`. La respuesta se envía al cliente con un código de estado *200 - Correcto*.

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

Solicitud original: `/iis-rules-rewrite/1234`

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_iis_url_rewrite.png)

Si tiene un Módulo URL Rewrite para IIS activo con reglas configuradas en el nivel de servidor que podrían afectar a la aplicación de manera no deseada, puede deshabilitar el Módulo URL Rewrite para IIS para una aplicación. Para más información, vea [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules) (Deshabilitación de módulos de IIS).

#### <a name="unsupported-features"></a>Características no admitidas

El middleware publicado con ASP.NET Core 2.x no admite las siguientes características de Módulo URL Rewrite para IIS:

* Reglas de salida
* Variables de servidor personalizadas
* Caracteres comodín
* LogRewrittenUrl

#### <a name="supported-server-variables"></a>Variables de servidor compatibles

El middleware admite las siguientes variables de servidor del Módulo URL Rewrite para IIS:

* CONTENT_LENGTH
* CONTENT_TYPE
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_HOST
* HTTP_REFERER
* HTTP_URL
* HTTP_USER_AGENT
* HTTPS
* LOCAL_ADDR
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_URI

> [!NOTE]
> También puede obtener <xref:Microsoft.Extensions.FileProviders.IFileProvider> a través de <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>. Con este enfoque logrará mayor flexibilidad para la ubicación de los archivos de reglas de reescritura. Asegúrese de que los archivos de reglas de reescritura se implementan en el servidor en la ruta de acceso que proporcione.
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>Regla basada en métodos

Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> para implementar su propia lógica de la regla en un método. `Add` expone el elemento <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, lo que hace que <xref:Microsoft.AspNetCore.Http.HttpContext> esté disponible para usarlo en el método. [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determina cómo se administra el procesamiento adicional en la canalización. Establezca el valor en uno de los campos <xref:Microsoft.AspNetCore.Rewrite.RuleResult> que se describen en la tabla siguiente.

| Volver a escribir el resultado del contexto               | Acción                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| `RuleResult.ContinueRules` (valor predeterminado) | Continuar aplicando las reglas.                                         |
| `RuleResult.EndResponse`             | Dejar de aplicar las reglas y enviar la respuesta.                       |
| `RuleResult.SkipRemainingRules`      | Dejar de aplicar las reglas y enviar el contexto al middleware siguiente. |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

La aplicación de ejemplo muestra un método que redirige las solicitudes para las rutas de acceso que terminen con *.xml*. Si se realiza una solicitud de `/file.xml`, la solicitud se redirige a `/xmlfiles/file.xml`. El código de estado se establece en *301 - Movido definitivamente*. Cuando el explorador realiza una solicitud nueva a */xmlfiles/file.xml* , el middleware de archivos estáticos sirve el archivo al cliente desde la carpeta *wwwroot/xmlfiles*. Para un redireccionamiento, debe establecer de forma explícita el código de estado de la respuesta. En caso contrario, se devuelve un código de estado *200: correcto* y no se produce el redireccionamiento en el cliente.

*RewriteRules.cs* :

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

Este enfoque también permite volver a escribir las solicitudes. En la aplicación de ejemplo se muestra cómo volver a escribir la ruta de acceso para cualquier solicitud de archivo de texto para proporcionar el archivo de texto *file.txt* desde la carpeta *wwwroot*. El middleware de archivos estáticos proporciona el archivo en función de la ruta de acceso de la solicitud actualizada:

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

*RewriteRules.cs* :

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a>Regla basada en IRule

Utilice <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> para usar la lógica de reglas en una clase que implemente la interfaz <xref:Microsoft.AspNetCore.Rewrite.IRule>. `IRule` proporciona mayor flexibilidad que si se usa el enfoque de reglas basadas en métodos. La clase de implementación puede incluir un constructor que permita pasar parámetros para el método <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*>.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

Se comprueba que los valores de los parámetros en la aplicación de ejemplo para `extension` y `newPath` cumplen ciertas condiciones. `extension` debe contener un valor, que debe ser *.png* , *.jpg* o *.gif*. Si `newPath` no es válido, se genera <xref:System.ArgumentException> . Si se realiza una solicitud de *image.png* , la solicitud se redirige a `/png-images/image.png`. Si se realiza una solicitud de *image.jpg* , la solicitud se redirige a `/jpg-images/image.jpg`. El código de estado se establece en *301 - Movido definitivamente* y se establece `context.Result` para detener el procesamiento de las reglas y enviar la respuesta.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

Solicitud original: `/image.png`

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas para image.png](url-rewriting/_static/add_redirect_png_requests.png)

Solicitud original: `/image.jpg`

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas para image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>Ejemplos de expresiones regulares

| Objetivo | Cadena de expresión regular &<br>Ejemplo de coincidencia | Cadena de reemplazo &<br>Ejemplo de resultado |
| ---- | ------------------------------- | -------------------------------------- |
| Ruta de acceso de reescritura en la cadena de consulta | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| Quitar barra diagonal final | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| Exigir barra diagonal final | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| Evitar reescritura de solicitudes específicas | `^(.*)(?<!\.axd)$` o `^(?!.*\.axd$)(.*)$`<br>Sí: `/resource.htm`<br>No: `/resource.axd` | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| Reorganizar los segmentos de URL | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| Reemplazar un segmento de URL | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

En este documento se ofrece una introducción a la reescritura de URL e instrucciones sobre cómo usar el middleware de reescritura de URL en aplicaciones ASP.NET Core.

La reescritura de URL consiste en modificar varias URL de solicitud basadas en una o varias reglas predefinidas. La reescritura de URL crea una abstracción entre las ubicaciones de recursos y sus direcciones para que las ubicaciones y las direcciones no estén estrechamente vinculadas. La reescritura de URL es útil en varios escenarios para:

* Mover o reemplazar recursos del servidor de forma temporal o permanente a la vez que se mantienen localizadores estables para esos recursos.
* Dividir el procesamiento de solicitudes entre otras aplicaciones o entre áreas de una aplicación.
* Quitar, agregar o reorganizar segmentos de dirección URL en solicitudes entrantes.
* Optimizar direcciones URL públicas para la optimización del motor de búsqueda (SEO).
* Permitir el uso de direcciones URL públicas descriptivas para ayudar a los visitantes a predecir el contenido devuelto mediante la solicitud de un recurso.
* Redirigir solicitudes no seguras a puntos de conexión seguros.
* Evitar la vinculación activa, en la que un sitio externo usa un recurso estático hospedado en otro sitio mediante la vinculación del recurso a su propio contenido.

> [!NOTE]
> La reescritura de URL puede reducir el rendimiento de una aplicación. Cuando sea factible, limite el número y la complejidad de las reglas.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="url-redirect-and-url-rewrite"></a>Redireccionamiento y reescritura de URL

La diferencia entre los términos *redirección de URL* y *reescritura de URL* es sutil, pero tiene importantes implicaciones para proporcionar recursos a los clientes. El middleware de reescritura de URL de ASP.NET Core es capaz de satisfacer las necesidades de ambos.

Una *redirección de URL* implica una operación del lado cliente, donde al cliente se le indica que acceda a un recurso en una dirección distinta a la que ha solicitado originalmente. Esto requiere un recorrido de ida y vuelta al servidor. La URL de redireccionamiento que se devuelve al cliente aparece en la barra de direcciones del explorador cuando el cliente realiza una nueva solicitud para el recurso.

Si `/resource` se *redirige* a `/different-resource`, el servidor responde que el cliente debe obtener el recurso en `/different-resource` con un código de estado que indica que la redirección es temporal o permanente.

![Se cambió temporalmente un punto de conexión del servicio WebAPI de la versión 1 (v1) a la versión 2 (v2) en el servidor. Un cliente realiza una solicitud al servicio en la ruta de acceso de la versión 1 /v1/api. El servidor devuelve una respuesta 302 (Encontrado) con la nueva ruta temporal para el servicio en la versión 2 /v2/api. El cliente realiza una segunda solicitud al servicio en la URL de redireccionamiento. El servidor responde con un código de estado 200 (Correcto).](url-rewriting/_static/url_redirect.png)

Al redirigir las solicitudes a otra dirección URL, se indica si la redirección es permanente o temporal mediante la especificación del código de estado con la respuesta:

* El código de estado *301 - Movido definitivamente* se usa cuando el recurso tiene una nueva dirección URL permanente y se quiere indicar al cliente que todas las solicitudes futuras para el recurso deben usar la nueva dirección URL. *El cliente puede almacenar en caché la respuesta cuando se recibe un código de estado 301.*

* El código de estado *302: encontrado* se usa cuando el redireccionamiento es temporal o en general está sujeto a cambios. El código de estado 302 indica al cliente que no almacene la dirección URL y la use en el futuro.

Para obtener más información sobre los códigos de estado, consulte [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (RFC 2616: definiciones de los códigos de estado).

La *reescritura de URL* es una operación del lado servidor que proporciona un recurso desde una dirección de recursos distinta a la que el cliente ha solicitado. La reescritura de una dirección URL no requiere un recorrido de ida y vuelta al servidor. La dirección URL reescrita no se devuelve al cliente y no aparece en la barra de direcciones del explorador.

Si `/resource` se *reescribe* como `/different-resource`, el servidor la obtiene *internamente* y devuelve el recurso en `/different-resource`.

Aunque es posible que el cliente pueda recuperar el recurso en la dirección URL reescrita, no se le informa de que el recurso existe en la dirección URL reescrita cuando realiza su solicitud y recibe la respuesta.

![Se cambió un punto de conexión del servicio WebAPI de la versión 1 (v1) a la versión 2 (v2) en el servidor. Un cliente realiza una solicitud al servicio en la ruta de acceso de la versión 1 /v1/api. La URL de la solicitud se reescribe para acceder al servicio en la ruta de acceso de la versión 2 /v2/api. El servicio responde al cliente con un código de estado 200 (Correcto).](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a>Aplicación de ejemplo de reescritura de URL

Puede explorar las características del middleware de reescritura de URL con la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/). La aplicación emplea las reglas de redireccionamiento y reescritura, y muestra la URL redirigida o reescrita para varios escenarios.

## <a name="when-to-use-url-rewriting-middleware"></a>Cuándo usar el middleware de reescritura de URL

Use el middleware de reescritura de URL cuando no pueda usar los enfoques siguientes:

* [Módulo de reescritura de URL con IIS en Windows Server](https://www.iis.net/downloads/microsoft/url-rewrite)
* [Módulo mod_rewrite de Apache en el servidor Apache](https://httpd.apache.org/docs/2.4/rewrite/)
* [Reescritura de URL en Nginx](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

Además, use el middleware cuando la aplicación se hospede en el [servidor HTTP.sys](xref:fundamentals/servers/httpsys) (anteriormente denominado WebListener).

Las principales razones para usar la tecnologías de reescritura de URL basadas en servidor en IIS, Apache y Nginx son las siguientes:

* El middleware no es compatible con todas las características de estos módulos.

  Algunas de las características de los módulos de servidor no funcionan con proyectos de ASP.NET Core, como las restricciones `IsFile` y `IsDirectory` del módulo de reescritura de IIS. En estos casos, es mejor usar el middleware.
* El rendimiento del middleware probablemente no coincida con el de los módulos.

  La única manera de saber con certeza con qué enfoque el rendimiento disminuye más, o si la disminución de este es insignificante, es mediante una comparación.

## <a name="package"></a>Package

Para incluir el middleware en el proyecto, agregue una referencia de paquete al [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) del archivo de proyecto, que contiene el paquete [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite).

Si no se usa el metapaquete `Microsoft.AspNetCore.App`, agregue una referencia de proyecto al paquete `Microsoft.AspNetCore.Rewrite`.

## <a name="extension-and-options"></a>Extensión y opciones

Establezca las reglas de reescritura y redirección de URL mediante la creación de una instancia de la clase [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) con métodos de extensión para cada una de las reglas de reescritura. Encadene varias reglas en el orden que quiera procesarlas. Las `RewriteOptions` se pasan al middleware de reescritura de URL cuando se agrega a la canalización de solicitudes con <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a>Redirigir solicitudes que distintas de www a www

Hay tres opciones que permiten a la aplicación redirigir solicitudes distintas de `www` a `www`:

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: redirige permanentemente la solicitud al subdominio `www` si la solicitud es distinta de `www`. Se redirige con un código de estado [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect).

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: redirige la solicitud al subdominio `www` si la solicitud de entrada es distinta de `www`. Se redirige con un código de estado [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect). Una sobrecarga permite proporcionar el código de estado para la respuesta. Use un campo de la clase <xref:Microsoft.AspNetCore.Http.StatusCodes> para una asignación de código de estado.

### <a name="url-redirect"></a>Redirección de URL

Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> para redirigir las solicitudes. El primer parámetro contiene la expresión regular para hacer coincidir la ruta de acceso de la URL entrante. El segundo parámetro es la cadena de reemplazo. El tercer parámetro, si está presente, especifica el código de estado. Si no se especifica el código de estado, el valor predeterminado es *302 - Encontrado* , lo que indica que el recurso se ha movido o reemplazado temporalmente.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

En un explorador con herramientas de desarrollo habilitadas, realice una solicitud a la aplicación de ejemplo con la ruta de acceso `/redirect-rule/1234/5678`. La expresión regular coincide con la ruta de acceso de la solicitud en `redirect-rule/(.*)` y la ruta de acceso se reemplaza con `/redirected/1234/5678`. La dirección URL de redireccionamiento se devuelve al cliente con un código de estado *302 - Encontrado*. El explorador realiza una solicitud nueva en la URL de redireccionamiento, que aparece en la barra de direcciones del explorador. Como ninguna de las reglas de la aplicación de ejemplo coincide con la dirección URL de redireccionamiento:

* La segunda solicitud recibe una respuesta *200: correcto* de la aplicación.
* En el cuerpo de la respuesta se muestra la dirección URL de redireccionamiento.

Se realiza un recorrido de ida y vuelta al servidor cuando se *redirige* una dirección URL.

> [!WARNING]
> Tenga cuidado al establecer las reglas de redirección. Las reglas de redirección se evalúan en cada solicitud enviada a la aplicación, incluso después de una redirección. Es fácil crear por error un *bucle de redirecciones infinitas*.

Solicitud original: `/redirect-rule/1234/5678`

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_redirect.png)

La parte de la expresión incluida entre paréntesis se denomina *grupo de capturas*. El punto (`.`) de la expresión significa *buscar cualquier carácter*. El asterisco (`*`) indica *buscar el carácter precedente cero o más veces*. Por tanto, el grupo de capturas `(.*)` busca los dos últimos segmentos de la ruta de acceso de la URL, `1234/5678`. Cualquier valor que se proporcione en la URL de la solicitud después de `redirect-rule/` es capturado por este grupo de capturas único.

En la cadena de reemplazo, se insertan los grupos capturados en la cadena con el signo de dólar (`$`) seguido del número de secuencia de la captura. Se obtiene el valor del primer grupo de capturas con `$1`, el segundo con `$2`, y así siguen en secuencia para los grupos de capturas de la expresión regular. Solo hay un grupo capturado en la expresión regular de la regla de redirección de la aplicación de ejemplo, por lo que solo hay un grupo insertado en la cadena de reemplazo, que es `$1`. Cuando se aplica la regla, la URL se convierte en `/redirected/1234/5678`.

### <a name="url-redirect-to-a-secure-endpoint"></a>Redirección de URL a un punto de conexión segura

Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> para redirigir las solicitudes HTTP al mismo host y ruta de acceso mediante el protocolo HTTPS. Si no se proporciona el código de estado, el middleware muestra de forma predeterminada *302 - Encontrado*. Si no se proporciona el puerto:

* El middleware tiene como valor predeterminado `null`.
* El esquema cambia a `https` (protocolo HTTPS), y el cliente accede al recurso en el puerto 443.

En el ejemplo siguiente se muestra cómo establecer el código de estado en *301 - Movido definitivamente* y cambiar el puerto a 5001.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> para redirigir las solicitudes no seguras al mismo host y ruta de acceso mediante el protocolo HTTPS seguro en el puerto 443. El middleware establece el código de estado en *301 - Movido definitivamente*.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> Al redirigir a un punto de conexión seguro sin la necesidad de reglas de redirección adicionales, se recomienda usar el Middleware de redirección de HTTPS. Para más información, vea el tema [Aplicación de HTTPS](xref:security/enforcing-ssl#require-https).

La aplicación de ejemplo es capaz de mostrar cómo usar `AddRedirectToHttps` o `AddRedirectToHttpsPermanent`. Agregue el método de extensión a `RewriteOptions`. Realice una solicitud poco segura a la aplicación en cualquier URL. Descarte la advertencia de seguridad del explorador que indica que el certificado autofirmado no es de confianza o cree una excepción para confiar en el certificado en cuestión.

Solicitud original mediante `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_redirect_to_https.png)

Solicitud original mediante `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>Reescritura de URL

Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> para crear una regla para reescribir URL. El primer parámetro contiene la expresión regular para buscar coincidencias en la ruta de dirección de URL entrante. El segundo parámetro es la cadena de reemplazo. El tercer parámetro, `skipRemainingRules: {true|false}`, indica al middleware si al aplicar la regla actual tiene que omitir o no alguna regla de redirección adicional.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

Solicitud original: `/rewrite-rule/1234/5678`

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_rewrite.png)

El acento circunflejo (`^`) al principio de la expresión significa que la búsqueda de coincidencias empieza al principio de la ruta de dirección URL.

En el ejemplo anterior de la regla de redireccionamiento (`redirect-rule/(.*)`), no hay ningún acento circunflejo (`^`) al principio de la expresión regular. Por tanto, cualquier carácter puede preceder a `redirect-rule/` en la ruta de acceso para que la coincidencia sea correcta.

| Ruta de acceso                               | Coincidir con |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | Sí   |
| `/my-cool-redirect-rule/1234/5678` | Sí   |
| `/anotherredirect-rule/1234/5678`  | Sí   |

La regla de reescritura, `^rewrite-rule/(\d+)/(\d+)`, solo encuentra rutas de acceso que empiezan con `rewrite-rule/`. En la tabla siguiente, observe la diferencia de coincidencia.

| Ruta de acceso                              | Coincidir con |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | Sí   |
| `/my-cool-rewrite-rule/1234/5678` | No    |
| `/anotherrewrite-rule/1234/5678`  | No    |

Después de la parte `^rewrite-rule/` de la expresión, hay dos grupos de captura, `(\d+)/(\d+)`. `\d` significa *buscar un dígito (número)* . El signo más (`+`) significa *buscar uno o más de los caracteres anteriores*. Por tanto, la URL debe contener un número seguido de una barra diagonal, seguida de otro número. Estos grupos de capturas se insertan en la URL de reescritura como `$1` y `$2`. La cadena de reemplazo de la regla de reescritura coloca los grupos capturados en la cadena de consulta. La ruta de acceso solicitada de `/rewrite-rule/1234/5678` se reescribe para obtener el recurso en `/rewritten?var1=1234&var2=5678`. Si una cadena de consulta está presente en la solicitud original, se conserva cuando se reescribe la dirección URL.

No hay ningún recorrido de ida y vuelta al servidor para obtener el recurso. Si el recurso existe, se captura y se devuelve al cliente con un código de estado *200 - Correcto*. Como el cliente no se redirige, la dirección URL no cambia en la barra de direcciones del explorador. Los clientes no pueden detectar que se ha producido una operación de reescritura de URL en el servidor.

> [!NOTE]
> Use `skipRemainingRules: true` siempre que sea posible, ya que las reglas de coincidencia consumen muchos recursos y aumentan el tiempo de respuesta de aplicación. Para obtener la respuesta más rápida de la aplicación:
>
> * Ordene las reglas de reescritura desde la que coincida con más frecuencia a la que coincida con menos frecuencia.
> * Omita el procesamiento de las reglas restantes cuando se produzca una coincidencia; no es necesario ningún procesamiento de reglas adicional.

### <a name="apache-mod_rewrite"></a>mod_rewrite de Apache

Aplique reglas mod_rewrite de Apache con <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>. Asegúrese de que el archivo de reglas se implementa con la aplicación. Para obtener más información y ejemplos de reglas mod_rewrite, vea [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/) (mod_rewrite de Apache).

Se usa <xref:System.IO.StreamReader> para leer las reglas del archivo de reglas *ApacheModRewrite.txt* :

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

La aplicación de ejemplo redirige las solicitudes de `/apache-mod-rules-redirect/(.\*)` a `/redirected?id=$1`. El código de estado de la respuesta es *302: Encontrado*.

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

Solicitud original: `/apache-mod-rules-redirect/1234`

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_apache_mod_redirect.png)

El middleware admite las siguientes variables de servidor mod_rewrite de Apache:

* CONN_REMOTE_ADDR
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_FORWARDED
* HTTP_HOST
* HTTP_REFERER
* HTTP_USER_AGENT
* HTTPS
* IPV6
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_METHOD
* REQUEST_SCHEME
* REQUEST_URI
* SCRIPT_FILENAME
* SERVER_ADDR
* SERVER_PORT
* SERVER_PROTOCOL
* TIME
* TIME_DAY
* TIME_HOUR
* TIME_MIN
* TIME_MON
* TIME_SEC
* TIME_WDAY
* TIME_YEAR

### <a name="iis-url-rewrite-module-rules"></a>Reglas del Módulo URL Rewrite para IIS

Para usar el mismo conjunto de reglas que se aplica al módulo URL Rewrite para IIS, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>. Asegúrese de que el archivo de reglas se implementa con la aplicación. No dirija el middleware para que use el archivo *web.config* de la aplicación cuando se ejecute en Windows Server IIS. Con IIS, estas reglas se deben almacenar fuera del archivo *web.config* de la aplicación para evitar conflictos con el Módulo URL Rewrite para IIS. Para obtener más información y ejemplos de reglas del Módulo URL Rewrite para IIS, vea [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) (Uso del Módulo URL Rewrite 2.0) y [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference) (Referencia de configuración del Módulo URL Rewrite).

Se usa <xref:System.IO.StreamReader> para leer las reglas del archivo de reglas *IISUrlRewrite.xml* :

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

La aplicación de ejemplo reescribe las solicitudes de `/iis-rules-rewrite/(.*)` a `/rewritten?id=$1`. La respuesta se envía al cliente con un código de estado *200 - Correcto*.

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

Solicitud original: `/iis-rules-rewrite/1234`

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_iis_url_rewrite.png)

Si tiene un Módulo URL Rewrite para IIS activo con reglas configuradas en el nivel de servidor que podrían afectar a la aplicación de manera no deseada, puede deshabilitar el Módulo URL Rewrite para IIS para una aplicación. Para más información, vea [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules) (Deshabilitación de módulos de IIS).

#### <a name="unsupported-features"></a>Características no admitidas

El middleware publicado con ASP.NET Core 2.x no admite las siguientes características de Módulo URL Rewrite para IIS:

* Reglas de salida
* Variables de servidor personalizadas
* Caracteres comodín
* LogRewrittenUrl

#### <a name="supported-server-variables"></a>Variables de servidor compatibles

El middleware admite las siguientes variables de servidor del Módulo URL Rewrite para IIS:

* CONTENT_LENGTH
* CONTENT_TYPE
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_HOST
* HTTP_REFERER
* HTTP_URL
* HTTP_USER_AGENT
* HTTPS
* LOCAL_ADDR
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_URI

> [!NOTE]
> También puede obtener <xref:Microsoft.Extensions.FileProviders.IFileProvider> a través de <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>. Con este enfoque logrará mayor flexibilidad para la ubicación de los archivos de reglas de reescritura. Asegúrese de que los archivos de reglas de reescritura se implementan en el servidor en la ruta de acceso que proporcione.
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>Regla basada en métodos

Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> para implementar su propia lógica de la regla en un método. `Add` expone el elemento <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, lo que hace que <xref:Microsoft.AspNetCore.Http.HttpContext> esté disponible para usarlo en el método. [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determina cómo se administra el procesamiento adicional en la canalización. Establezca el valor en uno de los campos <xref:Microsoft.AspNetCore.Rewrite.RuleResult> que se describen en la tabla siguiente.

| Volver a escribir el resultado del contexto               | Acción                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| `RuleResult.ContinueRules` (valor predeterminado) | Continuar aplicando las reglas.                                         |
| `RuleResult.EndResponse`             | Dejar de aplicar las reglas y enviar la respuesta.                       |
| `RuleResult.SkipRemainingRules`      | Dejar de aplicar las reglas y enviar el contexto al middleware siguiente. |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

La aplicación de ejemplo muestra un método que redirige las solicitudes para las rutas de acceso que terminen con *.xml*. Si se realiza una solicitud de `/file.xml`, la solicitud se redirige a `/xmlfiles/file.xml`. El código de estado se establece en *301 - Movido definitivamente*. Cuando el explorador realiza una solicitud nueva a */xmlfiles/file.xml* , el middleware de archivos estáticos sirve el archivo al cliente desde la carpeta *wwwroot/xmlfiles*. Para un redireccionamiento, debe establecer de forma explícita el código de estado de la respuesta. En caso contrario, se devuelve un código de estado *200: correcto* y no se produce el redireccionamiento en el cliente.

*RewriteRules.cs* :

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

Este enfoque también permite volver a escribir las solicitudes. En la aplicación de ejemplo se muestra cómo volver a escribir la ruta de acceso para cualquier solicitud de archivo de texto para proporcionar el archivo de texto *file.txt* desde la carpeta *wwwroot*. El middleware de archivos estáticos proporciona el archivo en función de la ruta de acceso de la solicitud actualizada:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

*RewriteRules.cs* :

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a>Regla basada en IRule

Utilice <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> para usar la lógica de reglas en una clase que implemente la interfaz <xref:Microsoft.AspNetCore.Rewrite.IRule>. `IRule` proporciona mayor flexibilidad que si se usa el enfoque de reglas basadas en métodos. La clase de implementación puede incluir un constructor que permita pasar parámetros para el método <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*>.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

Se comprueba que los valores de los parámetros en la aplicación de ejemplo para `extension` y `newPath` cumplen ciertas condiciones. `extension` debe contener un valor, que debe ser *.png* , *.jpg* o *.gif*. Si `newPath` no es válido, se genera <xref:System.ArgumentException> . Si se realiza una solicitud de *image.png* , la solicitud se redirige a `/png-images/image.png`. Si se realiza una solicitud de *image.jpg* , la solicitud se redirige a `/jpg-images/image.jpg`. El código de estado se establece en *301 - Movido definitivamente* y se establece `context.Result` para detener el procesamiento de las reglas y enviar la respuesta.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

Solicitud original: `/image.png`

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas para image.png](url-rewriting/_static/add_redirect_png_requests.png)

Solicitud original: `/image.jpg`

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas para image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>Ejemplos de expresiones regulares

| Objetivo | Cadena de expresión regular &<br>Ejemplo de coincidencia | Cadena de reemplazo &<br>Ejemplo de resultado |
| ---- | ------------------------------- | -------------------------------------- |
| Ruta de acceso de reescritura en la cadena de consulta | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| Quitar barra diagonal final | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| Exigir barra diagonal final | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| Evitar reescritura de solicitudes específicas | `^(.*)(?<!\.axd)$` o `^(?!.*\.axd$)(.*)$`<br>Sí: `/resource.htm`<br>No: `/resource.axd` | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| Reorganizar los segmentos de URL | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| Reemplazar un segmento de URL | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Expresiones regulares en .NET](/dotnet/articles/standard/base-types/regular-expressions)
* [Lenguaje de expresiones regulares: referencia rápida](/dotnet/articles/standard/base-types/quick-ref)
* [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/) (mod_rewrite de Apache)
* [Using Url Rewrite Module 2.0 (for IIS)](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) (Uso del Módulo URL Rewrite 2.0 para IIS)
* [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference) (Referencia de configuración del Módulo URL Rewrite)
* [IIS URL Rewrite Module Forum](https://forums.iis.net/1152.aspx) (Foro del Módulo URL Rewrite para IIS)
* [Cómo simplificar la estructura de direcciones URL](https://support.google.com/webmasters/answer/76329?hl=en)
* [10 URL Rewriting Tips and Tricks](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/) (10 trucos y consejos para reescritura de URL)
* [To slash or not to slash](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html) (Usar la barra diagonal o no)
