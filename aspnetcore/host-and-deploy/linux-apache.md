---
title: Hospedar ASP.NET Core en Linux con Apache
author: rick-anderson
description: Aprenda a configurar Apache como servidor proxy inverso en CentOS para redirigir el tráfico HTTP a una aplicación web ASP.NET Core que se ejecuta en Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 04/10/2020
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
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 2bf5633461996bfecaaa6b730adc9a19bb2769c4
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015561"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a>Hospedar ASP.NET Core en Linux con Apache

Por [Shayne Boyer](https://github.com/spboyer)

Mediante esta guía, aprenda a configurar [Apache](https://httpd.apache.org/) como servidor proxy inverso en [CentOS 7](https://www.centos.org/) para redirigir el tráfico HTTP a una aplicación web ASP.NET Core que se ejecuta en el servidor [Kestrel](xref:fundamentals/servers/kestrel). La [extensión mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) y los módulos relacionados crean el proxy inverso del servidor.

## <a name="prerequisites"></a>Requisitos previos

* Servidor que ejecute CentOS 7, con una cuenta de usuario estándar con privilegios sudo.
* Tener .NET Core Runtime instalado en el servidor.
   1. Visite la página [Descargar .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
   1. Seleccione la versión más reciente de .NET Core que no sea de versión preliminar.
   1. Descargue el runtime más reciente que no sea de versión preliminar en la tabla en **Ejecutar aplicaciones: runtime**.
   1. Seleccione el vínculo **Instrucciones del administrador de paquetes** de Linux y siga las instrucciones de CentOS.
* Disponer de una aplicación de ASP.NET Core existente.

En cualquier momento futuro tras actualizar el marco de trabajo compartido, reinicie las aplicaciones ASP.NET Core que hospeda el servidor.

## <a name="publish-and-copy-over-the-app"></a>Publicar y copiar en la aplicación

Configure la aplicación para una [implementación dependiente de Framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd).

Si la aplicación se ejecuta localmente y no está configurada para realizar conexiones seguras (HTTPS), adopte cualquiera de los métodos siguientes:

* Configure la aplicación para controlar las conexiones locales seguras. Para obtener más información, vea la sección [Configuración de HTTPS](#https-configuration).
* Quite `https://localhost:5001` (si existe) de la propiedad `applicationUrl` en el archivo *Properties/launchSettings.json*.

Ejecute [dotnet publish](/dotnet/core/tools/dotnet-publish) desde el entorno de desarrollo para empaquetar una aplicación en un directorio (por ejemplo, *bin/Release/&lt;target_framework_moniker&gt;/publish*) que se pueda ejecutar en el servidor:

```dotnetcli
dotnet publish --configuration Release
```

La aplicación también se puede publicar como una [implementación independiente](/dotnet/core/deploying/#self-contained-deployments-scd) si prefiere no mantener .NET Core Runtime en el servidor.

Copie la aplicación de ASP.NET Core en el servidor usando una herramienta que se integre en el flujo de trabajo de la organización (como SCP o SFTP). Es habitual encontrar las aplicaciones web en el directorio *var* (por ejemplo, *var/www/helloapp*).

> [!NOTE]
> En un escenario de implementación de producción, un flujo de trabajo de integración continua lleva a cabo la tarea de publicar la aplicación y copiar los recursos en el servidor.

## <a name="configure-a-proxy-server"></a>Configurar un servidor proxy

Un proxy inverso es una configuración común para trabajar con aplicaciones web dinámicas. El proxy inverso finaliza la solicitud HTTP y la reenvía a la aplicación ASP.NET.

Un servidor proxy es el que reenvía las solicitudes de cliente a otro servidor en lugar de realizarlas él mismo. Los proxies inversos las reenvían a un destino fijo, normalmente en nombre de clientes arbitrarios. En esta guía, Apache se configura como proxy inverso que se ejecuta en el mismo servidor en el que Kestrel atiende la aplicación ASP.NET Core.

Como el proxy inverso reenvía las solicitudes, use el [Middleware de encabezados reenviados](xref:host-and-deploy/proxy-load-balancer) del paquete [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/). El middleware actualiza `Request.Scheme`, mediante el encabezado `X-Forwarded-Proto`, para que los URI de redireccionamiento y otras directivas de seguridad funcionen correctamente.

Cualquier componente que dependa del esquema (como la autenticación, la generación de vínculos, los redireccionamientos o la geolocalización) debe colocarse después de invocar al Middleware de encabezados reenviados.

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

Invoque el método <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> que está al principio de `Startup.Configure` antes de llamar a otro middleware. Configure el middleware para reenviar los encabezados `X-Forwarded-For` y `X-Forwarded-Proto`:

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

Si no se especifica ningún valor <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> para el middleware, los encabezados predeterminados para reenviar son `None`.

Los servidores proxy que se ejecutan en direcciones de bucle invertido (127.0.0.0/8, [:: 1]), incluida la dirección de localhost (127.0.0.1) estándar, son de confianza de forma predeterminada. Si otras redes o servidores proxy de confianza de la organización tramitan solicitudes entre Internet y el servidor web, agréguelos a la lista de <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> o <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>. En el ejemplo siguiente se agrega un servidor proxy de confianza en la dirección IP 10.0.0.100 al middleware de encabezados reenviados `KnownProxies` en `Startup.ConfigureServices`:

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

Para obtener más información, vea <xref:host-and-deploy/proxy-load-balancer>.

### <a name="install-apache"></a>Instalar Apache

Actualice los paquetes de CentOS a sus versiones estables más recientes:

```bash
sudo yum update -y
```

Instale el servidor web de Apache en CentOS con un único comando `yum`:

```bash
sudo yum -y install httpd mod_ssl
```

Salida de ejemplo después de ejecutar el comando:

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> En este ejemplo, la salida refleja httpd.86_64, puesto que la versión de CentOS 7 es de 64 bits. Para comprobar dónde está instalado Apache, ejecute `whereis httpd` desde un símbolo del sistema.

### <a name="configure-apache"></a>Configurar Apache

Los archivos de configuración de Apache se encuentran en el directorio `/etc/httpd/conf.d/`. Todos los archivos que tengan la extensión *.conf* se procesan en orden alfabético, además de los archivos de configuración del módulo de `/etc/httpd/conf.modules.d/`, que contiene todos los archivos de configuración necesarios para cargar los módulos.

Cree un archivo de configuración denominado *helloapp.conf* para la aplicación:

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

El bloque `VirtualHost` puede aparecer varias veces en uno o varios archivos en un servidor. En el archivo de configuración anterior, Apache acepta tráfico público en el puerto 80. El dominio `www.example.com` se atiende y el alias `*.example.com` se resuelve en el mismo sitio web. Para más información, consulte [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html) (Compatibilidad con el host virtual basado en nombres). Las solicitudes se redirigen mediante proxy en la raíz al puerto 5000 del servidor en 127.0.0.1. Para la comunicación bidireccional, se requieren `ProxyPass` y `ProxyPassReverse`. Para cambiar la IP o el puerto de Kestrel, vea [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (Kestrel: configuración de los puntos de conexión).

> [!WARNING]
> Si no se especifica una [directiva de ServerName](https://httpd.apache.org/docs/current/mod/core.html#servername) correcta en **VirtualHost**, el bloque expone la aplicación a las vulnerabilidades de seguridad. Los enlaces de carácter comodín de subdominio (por ejemplo, `*.example.com`) no presentan este riesgo de seguridad si se controla todo el dominio primario (a diferencia de `*.com`, que sí es vulnerable). Vea la [sección 5.4 de RFC 7230](https://tools.ietf.org/html/rfc7230#section-5.4) para obtener más información.

El registro se puede configurar por `VirtualHost` con las directivas `ErrorLog` y `CustomLog`. `ErrorLog` es la ubicación donde el servidor registra los errores, y `CustomLog` establece el nombre de archivo y el formato del archivo de registro. En este caso, aquí es donde se registra la información de la solicitud. Hay una línea para cada solicitud.

Guarde el archivo y pruebe la configuración. Si se pasa todo, la respuesta debe ser `Syntax [OK]`.

```bash
sudo service httpd configtest
```

Reinicie Apache:

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a>Supervisión de la aplicación

Apache está configurado ahora para reenviar las solicitudes efectuadas a `http://localhost:80` en la aplicación ASP.NET Core que se ejecuta en Kestrel en `http://127.0.0.1:5000`. En cambio, Apache no está configurado para administrar el proceso de Kestrel. Use *systemd* y cree un archivo de servicio para iniciar y supervisar la aplicación web subyacente. *systemd* es un sistema de inicio que proporciona muchas características eficaces para iniciar, detener y administrar procesos.

### <a name="create-the-service-file"></a>Crear el archivo de servicio

Cree el archivo de definición de servicio:

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

Un archivo de servicio de ejemplo para la aplicación:

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

En el ejemplo anterior, el usuario que administra el servicio se especifica mediante la opción `User`. El usuario (`apache`) debe existir y tener la propiedad correcta de los archivos de la aplicación.

Use `TimeoutStopSec` para configurar el período de tiempo que hay que esperar para que la aplicación se apague después de que reciba la señal de interrupción inicial. Si la aplicación no se apaga en este período, se emite SIGKILL para terminar la aplicación. Proporcione el valor como segundos sin unidad (por ejemplo, `150`), un intervalo de tiempo (por ejemplo, `2min 30s`) o `infinity` para deshabilitar el tiempo de expiración. El valor predeterminado de `TimeoutStopSec` es el valor de `DefaultTimeoutStopSec` del archivo de configuración del administrador (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf* o *user.conf.d*). El tiempo de expiración predeterminado para la mayoría de las distribuciones es de 90 segundos.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Algunos valores (por ejemplo, cadenas de conexión de SQL) deben ser de escape para que los proveedores de configuración lean las variables de entorno. Use el siguiente comando para generar un valor de escape correctamente para su uso en el archivo de configuración:

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

No se admiten los separadores de dos puntos (`:`) en los nombres de variable de entorno. Use un subrayado doble (`__`) en lugar de dos puntos. El [proveedor de configuración de variables de entorno](xref:fundamentals/configuration/index#environment-variables-configuration-provider) convierte doble subrayado en dos puntos cuando las variables de entorno se leen en la configuración. En el ejemplo siguiente, la clave de la cadena de conexión `ConnectionStrings:DefaultConnection` se establece en el archivo de definición de servicio como `ConnectionStrings__DefaultConnection`:

::: moniker-end
::: moniker range="< aspnetcore-3.0"

No se admiten los separadores de dos puntos (`:`) en los nombres de variable de entorno. Use un subrayado doble (`__`) en lugar de dos puntos. El [proveedor de configuración de variables de entorno](xref:fundamentals/configuration/index#environment-variables) convierte doble subrayado en dos puntos cuando las variables de entorno se leen en la configuración. En el ejemplo siguiente, la clave de la cadena de conexión `ConnectionStrings:DefaultConnection` se establece en el archivo de definición de servicio como `ConnectionStrings__DefaultConnection`:

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

Guarde el archivo y habilite el servicio.

```bash
sudo systemctl enable kestrel-helloapp.service
```

Inicie el servicio y compruebe que se está ejecutando:

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

Con el proxy inverso configurado y Kestrel administrado mediante *systemd*, la aplicación web está completamente configurada y se puede acceder a ella desde un explorador en la máquina local en `http://localhost`. Inspeccionar los encabezados de respuesta; el encabezado **Server** indica que Kestrel atiende la aplicación ASP.NET Core:

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Visualización de registros

Dado que la aplicación web que usa Kestrel se administra mediante *systemd*, los procesos y eventos se registran en un diario centralizado. Sin embargo, este diario incluye todas las entradas de todos los servicios y procesos administrados por *systemd*. Para ver los elementos específicos de `kestrel-helloapp.service`, use el siguiente comando:

```bash
sudo journalctl -fu kestrel-helloapp.service
```

Para el filtrado de tiempo, especifique las opciones de tiempo con el comando. Por ejemplo, use `--since today` para filtrar por el día actual o `--until 1 hour ago` para ver las entradas de la hora anterior. Para más información, consulte la [página man de journalctl](https://www.unix.com/man-page/centos/1/journalctl/).

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

## <a name="secure-the-app"></a>Protección de la nube

### <a name="configure-firewall"></a>Configurar el firewall

*Firewalld* es un demonio dinámico para administrar el firewall con compatibilidad con zonas de red. Los puertos y el filtrado de paquetes se pueden seguir administrando mediante iptables. *Firewalld* debe instalarse de forma predeterminada. `yum` puede usarse para instalar el paquete o comprobar que está instalado.

```bash
sudo yum install firewalld -y
```

Use `firewalld` para abrir solo los puertos necesarios para la aplicación. En este caso se usan los puertos 80 y 443. Los siguientes comandos establecen de forma permanente que se abran los puertos 80 y 443:

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

Vuelva a cargar la configuración del firewall. Compruebe los servicios y puertos disponibles en la zona predeterminada. Hay opciones disponibles si se inspecciona `firewall-cmd -h`.

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="https-configuration"></a>Configuración de HTTPS

**Configuración de la aplicación para conexiones locales seguras (HTTPS)**

El comando [dotnet run](/dotnet/core/tools/dotnet-run) usa el archivo *Properties/launchSettings.json* de la aplicación, que configura la aplicación para que escuche en las direcciones URL proporcionadas por la propiedad `applicationUrl` (por ejemplo, `https://localhost:5001;http://localhost:5000`).

Configure la aplicación para que use un certificado en el desarrollo para el comando `dotnet run` o el entorno de desarrollo (F5 o CTRL+F5 en Visual Studio Code) mediante uno de los siguientes enfoques:

* [Reemplace el certificado predeterminado de configuración](xref:fundamentals/servers/kestrel#configuration) (*recomendado*)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

**Configure el proxy inverso para conexiones de cliente seguras (HTTPS)**

Para configurar Apache para HTTPS, se usa el módulo *mod_ssl*. Cuando se instaló el módulo *httpd*, también lo hizo el módulo *mod_ssl*. Si aún no se ha instalado, use `yum` para agregarlo a la configuración.

```bash
sudo yum install mod_ssl
```

Para usar HTTPS, instale el módulo `mod_rewrite` para habilitar la reescritura de direcciones URL:

```bash
sudo yum install mod_rewrite
```

Modifique el archivo *helloapp.conf* para permitir la reescritura de direcciones URL y proteger la comunicación en el puerto 443:

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> En este ejemplo se usa un certificado generado localmente. **SSLCertificateFile** debe ser el archivo de certificado principal para el nombre de dominio. **SSLCertificateKeyFile** debe ser el archivo de claves generado al crear el CSR. **SSLCertificateChainFile** debe ser el archivo de certificado intermedio (si existe) proporcionado por la entidad de certificación.

Guarde el archivo y pruebe la configuración.

```bash
sudo service httpd configtest
```

Reinicie Apache:

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a>Sugerencias adicionales de Apache

### <a name="restart-apps-with-shared-framework-updates"></a>Reinicio de aplicaciones con actualizaciones de marco compartidas

Después de actualizar el marco de trabajo compartido en el servidor, reinicie las aplicaciones ASP.NET Core que hospeda el servidor.

### <a name="additional-headers"></a>Encabezados adicionales

Para protegerse frente a ataques malintencionados, hay unos encabezados que se deben modificar o agregar. Asegúrese de que el módulo `mod_headers` está instalado.

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a>Protección de Apache de los ataques de secuestro de clic

El [secuestro de clic](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), también conocido como *redireccionamiento de interfaz de usuario*, es un ataque malintencionado donde al visitante de un sitio web se le engaña para que haga clic en un vínculo o botón de una página distinta de la que actualmente está visitando. Use `X-FRAME-OPTIONS` para proteger el sitio.

Para mitigar los ataques de secuestro de clic:

1. Edite el archivo *httpd.conf*.

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   Agregue la línea `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.
1. Guarde el archivo.
1. Reinicie Apache.

#### <a name="mime-type-sniffing"></a>Examen de tipo MIME

El encabezado `X-Content-Type-Options` impide que Internet Explorer *examine MIME* (de forma que el elemento `Content-Type` de un archivo se determina a partir del contenido del archivo). Si el servidor establece el encabezado `Content-Type` en `text/html` con la opción `nosniff` establecida, Internet Explorer representa el contenido como `text/html` sin tener en cuenta el contenido del archivo.

Edite el archivo *httpd.conf*.

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

Agregue la línea `Header set X-Content-Type-Options "nosniff"`. Guarde el archivo. Reinicie Apache.

### <a name="load-balancing"></a>Equilibrio de carga

En este ejemplo se muestra cómo instalar y configurar Apache en CentOS 7 y Kestrel en el mismo equipo de la instancia. Para no tener un único punto de error, el uso de *mod_proxy_balancer* y la modificación de **VirtualHost** permitirían administrar varias instancias de las aplicaciones web detrás del servidor proxy de Apache.

```bash
sudo yum install mod_proxy_balancer
```

En el archivo de configuración que se muestra a continuación, se configura una instancia adicional de `helloapp` para que se ejecute en el puerto 5001. La sección *Proxy* se establece con una configuración de equilibrador con dos miembros para equilibrar la carga *byrequests*.

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a>Límites de velocidad

Use *mod_ratelimit*, que se incluye en el módulo *httpd*; el ancho de banda de los clientes puede ser limitado:

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

En el archivo de ejemplo se limita el ancho de banda a 600 KB/s en la ubicación raíz:

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a>Campos del encabezado de solicitud más largos

La configuración predeterminada del servidor proxy normalmente limita los campos de encabezado de la solicitud a 8.190 bytes. Una aplicación puede requerir campos con una longitud mayor que la predeterminada (por ejemplo, las aplicaciones que usan [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)). Si se requieren campos más largos, es necesario ajustar la directiva [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) del servidor proxy. El valor que se aplica depende del escenario. Para obtener más información, consulte la documentación del servidor.

> [!WARNING]
> No aumente el valor predeterminado de `LimitRequestFieldSize` a menos que sea necesario. El aumento de este valor incrementa el riesgo de saturación del búfer (desbordamiento) y ataques por denegación de servicio (DoS) realizados por usuarios malintencionados.

## <a name="additional-resources"></a>Recursos adicionales

* [Requisitos previos para .NET Core en Linux](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
