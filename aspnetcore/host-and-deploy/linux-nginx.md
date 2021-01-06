---
title: Hospedar ASP.NET Core en Linux con Nginx
author: rick-anderson
description: Sepa cómo configurar Nginx como un proxy inverso en Ubuntu 16.04 para reenviar el tráfico HTTP a una aplicación web de ASP.NET Core que se ejecuta en Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2020
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
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: c4e0d70b41221f272bb4b1fe82cfa531ec6fcf15
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "94431072"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a>Hospedar ASP.NET Core en Linux con Nginx

Por [Sourabh Shirhatti](https://twitter.com/sshirhatti)

En esta guía se explica cómo configurar un entorno de ASP.NET Core listo para producción en un servidor de Ubuntu 16.04. Probablemente estas instrucciones sean válidas también con versiones más recientes de Ubuntu, pero no se han probado con versiones más recientes.

Para información sobre otras distribuciones de Linux compatibles con ASP.NET Core, consulte [Requisitos previos para .NET Core en Linux](/dotnet/core/linux-prerequisites).

> [!NOTE]
> En Ubuntu 14.04, `supervisord` is es la solución recomendada para supervisar el proceso de Kestrel. `systemd` no está disponible en Ubuntu 14.04. Para obtener instrucciones de Ubuntu 14.04, vea la [versión anterior de este tema](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).

En esta guía:

* Se coloca una aplicación ASP.NET Core existente detrás de un servidor proxy inverso.
* Se configura el servidor proxy inverso para reenviar las solicitudes al servidor web de Kestrel.
* Se garantiza que la aplicación web se ejecute al inicio como un demonio.
* Se configura una herramienta de administración de procesos para ayudar a reiniciar la aplicación web.

## <a name="prerequisites"></a>Requisitos previos

1. Acceso a un servidor de Ubuntu 16.04 con una cuenta de usuario estándar con privilegios sudo.
1. Tener .NET Core Runtime instalado en el servidor.
   1. Visite la página [Descargar .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
   1. Seleccione la versión más reciente de .NET Core que no sea de versión preliminar.
   1. Descargue el runtime más reciente que no sea de versión preliminar en la tabla en **Ejecutar aplicaciones: runtime**.
   1. Seleccione el vínculo **Instrucciones del administrador de paquetes** de Linux y siga las instrucciones de Ubuntu para su versión de Ubuntu.
1. Disponer de una aplicación de ASP.NET Core existente.

En cualquier momento futuro tras actualizar el marco de trabajo compartido, reinicie las aplicaciones ASP.NET Core que hospeda el servidor.

## <a name="publish-and-copy-over-the-app"></a>Publicar y copiar en la aplicación

Configure la aplicación para una [implementación dependiente de Framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd).

Si la aplicación se ejecuta localmente y no está configurada para realizar conexiones seguras (HTTPS), adopte cualquiera de los métodos siguientes:

* Configure la aplicación para controlar las conexiones locales seguras. Para obtener más información, vea la sección [Configuración de HTTPS](#https-configuration).
* Quite `https://localhost:5001` (si existe) de la propiedad `applicationUrl` en el archivo `Properties/launchSettings.json`.

Ejecute [dotnet publish](/dotnet/core/tools/dotnet-publish) desde el entorno de desarrollo para empaquetar una aplicación en un directorio (por ejemplo, `bin/Release/{TARGET FRAMEWORK MONIKER}/publish`, donde el marcador de posición `{TARGET FRAMEWORK MONIKER}` es el moniker de la plataforma de destino, o TFM) que se pueda ejecutar en el servidor:

```dotnetcli
dotnet publish --configuration Release
```

La aplicación también se puede publicar como una [implementación independiente](/dotnet/core/deploying/#self-contained-deployments-scd) si prefiere no mantener .NET Core Runtime en el servidor.

Copie la aplicación de ASP.NET Core en el servidor usando una herramienta que se integre en el flujo de trabajo de la organización (como `SCP` o `SFTP`). Es habitual encontrar las aplicaciones web en el directorio `var` (por ejemplo, `var/www/helloapp`).

> [!NOTE]
> En un escenario de implementación de producción, un flujo de trabajo de integración continua lleva a cabo la tarea de publicar la aplicación y copiar los recursos en el servidor.

Pruebe la aplicación:

1. Desde la línea de comandos, ejecute la aplicación: `dotnet <app_assembly>.dll`.
1. En un explorador, vaya a `http://<serveraddress>:<port>` para comprobar que la aplicación funciona en Linux de forma local.

## <a name="configure-a-reverse-proxy-server"></a>Configurar un servidor proxy inverso

Un proxy inverso es una configuración común para trabajar con aplicaciones web dinámicas. Un proxy inverso finaliza la solicitud HTTP y la reenvía a la aplicación ASP.NET Core.

### <a name="use-a-reverse-proxy-server"></a>Usar un servidor proxy inverso

Kestrel resulta muy adecuado para suministrar contenido dinámico de ASP.NET Core. Sin embargo, las funcionalidades de servicio web no son tan completas como las de los servidores, como IIS, Apache o Nginx. Un servidor proxy inverso puede reducir las cargas de trabajo, por ejemplo, suministrar contenido estático, almacenar solicitudes en caché, comprimir solicitudes y finalizar HTTPS desde el servidor HTTP. Un servidor proxy inverso puede residir en un equipo dedicado o se puede implementar junto con un servidor HTTP.

Para los fines de esta guía, se usa una única instancia de Nginx. Se ejecuta en el mismo servidor, junto con el servidor HTTP. En función de requisitos, se puede elegir una configuración diferente.

Dado que el proxy inverso reenvía las solicitudes, use el [middleware de encabezados reenviados](xref:host-and-deploy/proxy-load-balancer) del paquete [`Microsoft.AspNetCore.HttpOverrides`](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides). El middleware actualiza `Request.Scheme`, mediante el encabezado `X-Forwarded-Proto`, para que los URI de redireccionamiento y otras directivas de seguridad funcionen correctamente.

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

Invoque el método <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> que está al principio de `Startup.Configure` antes de llamar a otro middleware. Configure el middleware para reenviar los encabezados `X-Forwarded-For` y `X-Forwarded-Proto`:

```csharp
using Microsoft.AspNetCore.HttpOverrides;

...

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

Si no se especifica ningún valor <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> para el middleware, los encabezados predeterminados para reenviar son `None`.

Los servidores proxy que se ejecutan en direcciones de bucle invertido (`127.0.0.0/8` o `[::1]`), incluida la dirección de localhost (`127.0.0.1`) estándar, son de confianza de forma predeterminada. Si otras redes o servidores proxy de confianza de la organización tramitan solicitudes entre Internet y el servidor web, agréguelos a la lista de <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> o <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>. En el ejemplo siguiente se agrega un servidor proxy de confianza en la dirección IP 10.0.0.100 al middleware de encabezados reenviados `KnownProxies` en `Startup.ConfigureServices`:

```csharp
using System.Net;

...

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

Para obtener más información, vea <xref:host-and-deploy/proxy-load-balancer>.

### <a name="install-nginx"></a>Instalar Nginx

Use `apt-get` para instalar Nginx. El instalador crea un script de inicio `systemd` que ejecuta Nginx como demonio al iniciarse el sistema. Siga las instrucciones de instalación para Ubuntu en [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages) (Nginx: paquetes oficiales de Debian y Ubuntu).

> [!NOTE]
> Si se necesitan módulos de Nginx opcionales, puede que haya que compilar Nginx desde el origen.

Puesto que Nginx se ha instalado por primera vez, ejecute lo siguiente para iniciarlo de forma explícita:

```bash
sudo service nginx start
```

Compruebe que un explorador muestra la página de aterrizaje predeterminada de Nginx. La página de aterrizaje está accesible en `http://<server_IP_address>/index.nginx-debian.html`.

### <a name="configure-nginx"></a>Configurar Nginx

Para configurar Nginx como un proxy inverso a fin de que reenvíe solicitudes HTTP a nuestra aplicación de ASP.NET Core, modifique `/etc/nginx/sites-available/default`. Ábralo en un editor de texto y reemplace el contenido por el siguiente:

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

Si la aplicación es una aplicación SignalR o Blazor Server, vea <xref:signalr/scale#linux-with-nginx> y <xref:blazor/host-and-deploy/server#linux-with-nginx> respectivamente para obtener más información.

Cuando no hay ninguna coincidencia de `server_name`, Nginx usa el servidor predeterminado. Si no se define ningún servidor predeterminado, el primer servidor del archivo de configuración es el servidor predeterminado. Como procedimiento recomendado, agregue un servidor predeterminado específico que devuelva un código de estado 444 en el archivo de configuración. Un ejemplo de configuración del servidor predeterminado es:

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

Con el archivo de configuración anterior y el servidor predeterminado, Nginx acepta tráfico público en el puerto 80 con el encabezado de host `example.com` o `*.example.com`. Las solicitudes que no coincidan con estos hosts no se reenviarán al Kestrel. Nginx reenvía las solicitudes coincidentes con Kestrel a `http://localhost:5000`. Para más información, consulte [How nginx processes a request](https://nginx.org/docs/http/request_processing.html) (Cómo Nginx procesa una solicitud). Para cambiar la IP o el puerto de Kestrel, vea [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (Kestrel: configuración de los puntos de conexión).

> [!WARNING]
> Si no se especifica una [directiva de server_name](https://nginx.org/docs/http/server_names.html) adecuada, su aplicación se expone a vulnerabilidades de seguridad. Los enlaces de carácter comodín de subdominio (por ejemplo, `*.example.com`) no presentan este riesgo de seguridad si se controla todo el dominio primario (a diferencia de `*.com`, que sí es vulnerable). Vea la [sección 5.4 de RFC 7230](https://tools.ietf.org/html/rfc7230#section-5.4) para obtener más información.

Una vez establecida la configuración de Nginx, ejecute `sudo nginx -t` para comprobar la sintaxis de los archivos de configuración. Si la prueba del archivo de configuración es correcta, fuerce a Nginx a recopilar los cambios mediante la ejecución de `sudo nginx -s reload`.

Para ejecutar la aplicación directamente en el servidor:

1. Vaya al directorio de la aplicación.
1. Ejecute la aplicación: `dotnet <app_assembly.dll>`, donde `app_assembly.dll` es el nombre de archivo de ensamblado de la aplicación.

Si la aplicación se ejecuta en el servidor, pero no responde a través de Internet, compruebe el firewall del servidor y confirme que el puerto 80 está abierto. Si está usando una máquina virtual Ubuntu de Azure, agregue una regla de grupo de seguridad de red que posibilite el tráfico entrante del puerto 80. No es necesario para habilitar una regla de tráfico saliente en el puerto 80, ya que dicho tráfico se concede automáticamente al habilitar la regla de entrada.

Cuando termine de probar la aplicación, ciérrela con `Ctrl+C` en el símbolo del sistema.

## <a name="monitor-the-app"></a>Supervisión de la aplicación

Nginx ahora está configurado para reenviar las solicitudes realizadas a `http://<serveraddress>:80` en la aplicación de ASP.NET Core que se ejecuta en Kestrel en `http://127.0.0.1:5000`. Sin embargo, Nginx no está configurado para administrar el proceso de Kestrel. Se puede usar `systemd` para crear un archivo de servicio para iniciar y supervisar la aplicación web subyacente. `systemd` es un sistema de inicio que proporciona muchas características eficaces para iniciar, detener y administrar procesos. 

### <a name="create-the-service-file"></a>Crear el archivo de servicio

Cree el archivo de definición de servicio:

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

Este es un archivo de servicio de ejemplo de la aplicación:

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

En el ejemplo anterior, el usuario que administra el servicio se especifica mediante la opción `User`. El usuario (`www-data`) debe existir y tener la propiedad correcta de los archivos de la aplicación.

Use `TimeoutStopSec` para configurar el período de tiempo que hay que esperar para que la aplicación se apague después de que reciba la señal de interrupción inicial. Si la aplicación no se apaga en este período, se emite SIGKILL para terminar la aplicación. Proporcione el valor como segundos sin unidad (por ejemplo, `150`), un intervalo de tiempo (por ejemplo, `2min 30s`) o `infinity` para deshabilitar el tiempo de expiración. El valor predeterminado de `TimeoutStopSec` es `DefaultTimeoutStopSec` en el archivo de configuración del administrador (`systemd-system.conf`, `system.conf.d`, `systemd-user.conf` y `user.conf.d`). El tiempo de expiración predeterminado para la mayoría de las distribuciones es de 90 segundos.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Linux tiene un sistema de archivos que distingue mayúsculas de minúsculas. Si se establece `ASPNETCORE_ENVIRONMENT` en `Production`, se busca en el archivo de configuración `appsettings.Production.json`, no en `appsettings.production.json`.

Algunos valores (por ejemplo, cadenas de conexión de SQL) deben ser de escape para que los proveedores de configuración lean las variables de entorno. Use el siguiente comando para generar un valor de escape correctamente para su uso en el archivo de configuración:

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

No se admiten los separadores de dos puntos (`:`) en los nombres de variable de entorno. Use un subrayado doble (`__`) en lugar de dos puntos. El [proveedor de configuración de variables de entorno](xref:fundamentals/configuration/index#environment-variables) convierte doble subrayado en dos puntos cuando las variables de entorno se leen en la configuración. En el ejemplo siguiente, la clave de la cadena de conexión `ConnectionStrings:DefaultConnection` se establece en el archivo de definición de servicio como `ConnectionStrings__DefaultConnection`:

::: moniker-end
::: moniker range="< aspnetcore-3.0"

No se admiten los separadores de dos puntos (`:`) en los nombres de variable de entorno. Use un subrayado doble (`__`) en lugar de dos puntos. El [proveedor de configuración de variables de entorno](xref:fundamentals/configuration/index#environment-variables-configuration-provider) convierte doble subrayado en dos puntos cuando las variables de entorno se leen en la configuración. En el ejemplo siguiente, la clave de la cadena de conexión `ConnectionStrings:DefaultConnection` se establece en el archivo de definición de servicio como `ConnectionStrings__DefaultConnection`:

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

Guarde el archivo y habilite el servicio.

```bash
sudo systemctl enable kestrel-helloapp.service
```

Inicie el servicio y compruebe que se está ejecutando.

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

Con el proxy inverso configurado y Kestrel administrado con `systemd`, la aplicación web está completamente configurada y se puede acceder a ella desde un explorador en la máquina local en `http://localhost`. También es accesible desde una máquina remota, salvo que haya algún firewall que la pueda estar bloqueando. Al inspeccionar los encabezados de respuesta, el encabezado `Server` muestra que Kestrel suministra la aplicación ASP.NET Core.

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Visualización de registros

Dado que la aplicación web que usa Kestrel se administra mediante `systemd`, todos los procesos y eventos se registran en un diario centralizado. En cambio, este diario incluye todas las entradas de todos los servicios y procesos administrados por `systemd`. Para ver los elementos específicos de `kestrel-helloapp.service`, use el siguiente comando:

```bash
sudo journalctl -fu kestrel-helloapp.service
```

Para obtener más opciones de filtrado, las opciones de tiempo como `--since today`, `--until 1 hour ago` o una combinación de estas pueden reducir la cantidad de entradas que se devuelven.

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a>Protección de datos

Hay varios [softwares intermedios](xref:fundamentals/middleware/index) de ASP.NET Core que utilizan la [pila de protección de datos de ASP.NET Core](xref:security/data-protection/introduction), incluidos los de autenticación, como el de cookies, y las protecciones de falsificación de solicitud entre sitios (CSRF). Aunque el código de usuario no llame a las API de protección de datos, esta se debe configurar para crear un [almacén de claves](xref:security/data-protection/implementation/key-management) criptográficas persistente. Si no se configura la protección de datos, las claves se conservan en memoria y se descartan cuando se reinicia la aplicación.

Si el conjunto de claves se almacena en memoria cuando se reinicia la aplicación:

* Todos los tokens de autenticación basados en cookie se invalidan.
* Los usuarios tienen que iniciar sesión de nuevo en la siguiente solicitud.
* Ya no se pueden descifrar los datos protegidos con el conjunto de claves. Esto puede incluir [tokens CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) y [cookies](xref:fundamentals/app-state#tempdata) de TempData de ASP.NET Core MVC.

Para configurar la protección de datos de modo que sea persistente y permita cifrar el anillo de claves, consulte:

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a>Campos del encabezado de solicitud más largos

La configuración predeterminada del servidor proxy normalmente limita los campos de encabezado de la solicitud a 4 K u 8 K, en función de la plataforma. Una aplicación puede requerir campos con una longitud mayor que la predeterminada (por ejemplo, las aplicaciones que usan [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)). Si se requieren campos más largos, es necesario ajustar la configuración predeterminada del servidor proxy. Los valores que se aplican dependen del escenario. Para obtener más información, consulte la documentación del servidor.

* [proxy_buffer_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [proxy_buffers](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [proxy_busy_buffers_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [large_client_header_buffers](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> No aumente los valores predeterminados de los búferes de proxy a menos que sea necesario. El aumento de estos valores incrementa el riesgo de saturación del búfer (desbordamiento) y ataques por denegación de servicio (DoS) realizados por usuarios malintencionados.

## <a name="secure-the-app"></a>Protección de la nube

### <a name="enable-apparmor"></a>Habilitar AppArmor

Linux Security Modules (LSM) es una plataforma que forma parte del kernel de Linux desde Linux 2.6. LSM admite diferentes implementaciones de los módulos de seguridad. [AppArmor](https://wiki.ubuntu.com/AppArmor) es un LSM que implementa un sistema de control de acceso obligatorio que permite restringir el programa a un conjunto limitado de recursos. Asegúrese de que AppArmor está habilitado y configurado correctamente.

### <a name="configure-the-firewall"></a>Configuración del firewall

Cierre todos los puertos externos que no estén en uso. Uncomplicated Firewall (ufw) proporciona un front-end para `iptables` al proporcionar una CLI para configurar el firewall.

> [!WARNING]
> Si tiene algún firewall activado y no está configurado correctamente, este impedirá el acceso a todo el sistema. Si usa SSH para establecer la conexión y no especifica el puerto SSH correcto, quedará bloqueado y no podrá acceder al sistema. El puerto predeterminado es el 22. Para obtener más información, consulte la [introducción a UFW](https://help.ubuntu.com/community/UFW) y el [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).

Instale `ufw` y configúrelo para permitir el tráfico en los puertos que convenga.

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a>Protección de Nginx

#### <a name="change-the-nginx-response-name"></a>Cambiar el nombre de la respuesta de Nginx

Edite `src/http/ngx_http_header_filter_module.c`:

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a>Configurar opciones

Configure el servidor con más módulos que sean necesarios. Sopese la posibilidad de usar un firewall de aplicación web como [ModSecurity](https://www.modsecurity.org/) para proteger la aplicación.

#### <a name="https-configuration"></a>Configuración de HTTPS

**Configuración de la aplicación para conexiones locales seguras (HTTPS)**

El comando [dotnet run](/dotnet/core/tools/dotnet-run) usa el archivo `Properties/launchSettings.json` de la aplicación, que configura la aplicación para que escuche en las direcciones URL proporcionadas por la propiedad `applicationUrl` (por ejemplo, `https://localhost:5001;http://localhost:5000`).

Configure la aplicación para que use un certificado en el desarrollo para el comando `dotnet run` o el entorno de desarrollo (<kbd>F5</kbd> o <kbd>Ctrl</kbd>+<kbd>F5</kbd> en Visual Studio Code) mediante uno de los siguientes enfoques:

* [Reemplace el certificado predeterminado de configuración](xref:fundamentals/servers/kestrel#configuration) (*recomendado*)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

**Configure el proxy inverso para conexiones de cliente seguras (HTTPS)**

* Configure el servidor para que escuche el tráfico HTTPS en el puerto `443`. Para ello, especifique un certificado válido emitido por una entidad de certificados (CA) de confianza.

* Refuerce la seguridad con algunos de los procedimientos descritos en el siguiente archivo `/etc/nginx/nginx.conf`. Entre los ejemplos se incluye la elección de un cifrado más seguro y el redireccionamiento de todo el tráfico a través de HTTP a HTTPS.

  > [!NOTE]
  > En entornos de desarrollo, se recomienda usar redireccionamientos temporales (302) en lugar de redireccionamientos permanentes (301). El almacenamiento en caché de vínculos puede producir un comportamiento inestable en los entornos de desarrollo.

* Agregar un encabezado `HTTP Strict-Transport-Security` (HSTS) garantiza que todas las solicitudes siguientes realizadas por el cliente son a través de HTTPS.

  Para obtener instrucciones importantes sobre HSTS, vea <xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts>.

* Si HTTPS se va a deshabilitar en el futuro, use uno de los métodos siguientes:

  * No agregue el encabezado HSTS.
  * Elija un valor de `max-age` corto.

Agregue el archivo de configuración `/etc/nginx/proxy.conf`:

[!code-nginx[](linux-nginx/proxy.conf)]

**Reemplace** el contenido del archivo de configuración `/etc/nginx/nginx.conf` con el archivo siguiente. El ejemplo contiene las dos secciones `http` y `server` en un archivo de configuración.

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

> [!NOTE]
> Las aplicaciones de Blazor WebAssembly requieren un valor de parámetro `burst` más grande para acomodar el mayor número de solicitudes hechas por una aplicación. Para obtener más información, vea <xref:blazor/host-and-deploy/webassembly#nginx>.

#### <a name="secure-nginx-from-clickjacking"></a>Proteger Nginx frente al secuestro de clic

El [secuestro de clic](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), también conocido como *redireccionamiento de interfaz de usuario*, es un ataque malintencionado donde al visitante de un sitio web se le engaña para que haga clic en un vínculo o botón de una página distinta de la que actualmente está visitando. Use `X-FRAME-OPTIONS` para proteger el sitio.

Para mitigar los ataques de secuestro de clic:

1. Edite el archivo `nginx.conf`:

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   Agregue la línea `add_header X-Frame-Options "SAMEORIGIN";`.

1. Guarde el archivo.
1. Reinicie Nginx.

#### <a name="mime-type-sniffing"></a>Examen de tipo MIME

Este encabezado evita que la mayoría de los exploradores examinen el MIME de una respuesta fuera del tipo de contenido declarado, ya que el encabezado indica al explorador que no reemplace el tipo de contenido de respuesta. Con la opción `nosniff`, si el servidor indica que el contenido es `text/html`, el explorador lo representa como `text/html`.

1. Edite el archivo `nginx.conf`:

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   Agregue la línea `add_header X-Content-Type-Options "nosniff";`.

1. Guarde el archivo.
1. Reinicie Nginx.

## <a name="additional-nginx-suggestions"></a>Sugerencias de Nginx adicionales

Después de actualizar el marco de trabajo compartido en el servidor, reinicie las aplicaciones ASP.NET Core que hospeda el servidor.

## <a name="additional-resources"></a>Recursos adicionales

* [Requisitos previos para .NET Core en Linux](/dotnet/core/linux-prerequisites)
* [Nginx: Binary Releases: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages) (Nginx: versiones de código binario: paquetes oficiales de Debian y Ubuntu)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [Nginx: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (Nginx: uso del encabezado Forwarded)
