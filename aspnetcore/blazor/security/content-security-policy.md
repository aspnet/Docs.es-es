---
title: Aplicación de una directiva de seguridad de contenido para Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo usar una directiva de seguridad de contenido con aplicaciones Blazor de ASP.NET Core para protegerse de ataques de scripts de sitios (XSS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/security/content-security-policy
ms.openlocfilehash: bbe4bf1b0999d66471743c9efa1a9a9f121f2e05
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014041"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-no-locblazor"></a>Aplicación de una directiva de seguridad de contenido para Blazor de ASP.NET Core

Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)

Los [ataques de scripts de sitios (XSS)](xref:security/cross-site-scripting) son una vulnerabilidad de seguridad en la que un atacante coloca uno o más scripts de cliente malintencionados en el contenido representado de una aplicación. Una directiva de seguridad de contenido ayuda a protegerse de ataques XSS, ya que informa al explorador de la validez de los siguientes elementos:

* Orígenes de contenido cargado, incluidos scripts, hojas de estilo e imágenes
* Acciones realizadas por una página, especificando los destinos de URL permitidos de formularios
* Complementos que se pueden cargar

Para aplicar una directiva de seguridad de contenido en una aplicación, el desarrollador especifica varias *directivas* de seguridad de contenido en uno o varios encabezados `Content-Security-Policy` o etiquetas `<meta>`.

El explorador evalúa las directivas mientras una página se carga. Así, inspecciona los orígenes de la página y determina si cumplen los requisitos de las directivas de seguridad de contenido. Cuando un recurso no cumple estas directivas, el explorador no lo carga. Pensemos, por ejemplo, en una directiva que no permite scripts de terceros. Si una página contiene una etiqueta `<script>` con un origen de terceros en el atributo `src`, el explorador impide que el script se cargue.

Las directivas de seguridad de contenido se admiten en la mayoría de los exploradores de escritorio y móviles de hoy día, como Chrome, Edge, Firefox, Opera y Safari. Se recomienda usarlas en aplicaciones Blazor.

## <a name="policy-directives"></a>Directivas

Especifique como mínimo las siguientes directivas y orígenes en las aplicaciones Blazor. Agregue más directivas y orígenes según sea necesario. Las siguientes directivas se usan en la sección [Aplicación de la directiva](#apply-the-policy) de este artículo, donde se proporcionan directivas de seguridad de ejemplo para Blazor WebAssembly y Blazor Server:

* [base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): restringe las direcciones URL según la etiqueta `<base>` de una página. Especifique `self` para indicar que el origen de la aplicación (incluido el esquema y el número de puerto) es un origen válido.
* [block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): impide la carga de contenido HTTP y HTTPS combinado.
* [default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): indica una reserva de las directivas de origen que no se han especificado expresamente en la directiva. Especifique `self` para indicar que el origen de la aplicación (incluido el esquema y el número de puerto) es un origen válido.
* [img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): indica los orígenes de imágenes válidos.
  * Especifique `data:` para permitir la carga de imágenes de direcciones URL `data:`.
  * Especifique `https:` para permitir la carga de imágenes desde puntos de conexión HTTPS.
* [object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): indica los orígenes válidos de las etiquetas `<object>`, `<embed>` y `<applet>`. Especifique `none` para no permitir ningún origen de dirección URL.
* [script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): indica los orígenes de scripts válidos.
  * Especifique el origen de host `https://stackpath.bootstrapcdn.com/` para los scripts de arranque.
  * Especifique `self` para indicar que el origen de la aplicación (incluido el esquema y el número de puerto) es un origen válido.
  * En una aplicación Blazor WebAssembly:
    * Especifique los siguientes hash para permitir la carga de scripts en línea Blazor WebAssembly necesarios:
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * Especifique `unsafe-eval` para usar `eval()` y métodos para crear código a partir de cadenas.
  * En una aplicación Blazor Server, especifique el hash `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` para el script en línea que realiza la detección de reserva en hojas de estilos.
* [style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): indica los orígenes de hojas de estilo válidos.
  * Especifique el origen de host `https://stackpath.bootstrapcdn.com/` para las hojas de estilo de arranque.
  * Especifique `self` para indicar que el origen de la aplicación (incluido el esquema y el número de puerto) es un origen válido.
  * Especifique `unsafe-inline` para permitir el uso de estilos en línea. La declaración en línea es necesaria en la interfaz de usuario de las aplicaciones Blazor Server para volver a conectar el cliente y el servidor después de la solicitud inicial. En una versión futura, es posible que los estilos en línea se eliminen, de forma que `unsafe-inline` ya no sea necesario.
* [upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): indica que las direcciones URL de contenido de orígenes (HTTP) no seguros deben adquirirse de forma segura a través de HTTPS.

Todos los exploradores, excepto Microsoft Internet Explorer, admiten las directivas anteriores.

Para obtener los hash SHA de otros scripts en línea:

* Aplique la directiva de seguridad de contenido que aparece en la sección [Aplicación de la directiva](#apply-the-policy).
* Acceda a la consola de herramientas de desarrollo del explorador mientras ejecuta la aplicación localmente. El explorador calcula y muestra los hash de los scripts bloqueados cuando existe un encabezado de directiva de seguridad de contenido o una etiqueta `meta`.
* Copie los hash proporcionados por el explorador en los orígenes `script-src`. Inserte cada hash entre comillas simples.

Para obtener una matriz de compatibilidad de exploradores de nivel 2 de directiva de seguridad de contenido, vea [¿Puedo usar una directiva de seguridad de contenido de nivel 2?](https://www.caniuse.com/#feat=contentsecuritypolicy2)

## <a name="apply-the-policy"></a>Aplicación de la directiva

Use una etiqueta `<meta>` para aplicar la directiva:

* Establezca el valor del atributo `http-equiv` en `Content-Security-Policy`.
* Coloque las directivas en el valor de atributo `content`. Separe las directivas con un punto y coma (`;`).
* Coloque siempre la etiqueta `meta` en el contenido de `<head>`.

En las secciones siguientes se muestran las directivas de ejemplo para Blazor WebAssembly y Blazor Server. Estos ejemplos pertenecen a la versión de Blazor correspondiente a este artículo. Para usar la versión adecuada en su caso, seleccione la versión del documento usando el selector desplegable **Versión** de esta página web.

### Blazor WebAssembly

En el contenido de `<head>` de la página host `wwwroot/index.html`, aplique las directivas descritas en la sección [Directivas](#policy-directives):

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

### Blazor Server

En el contenido de `<head>` de la página host `Pages/_Host.cshtml`, aplique las directivas descritas en la sección [Directivas](#policy-directives):

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

## <a name="meta-tag-limitations"></a>Limitaciones de la etiqueta meta

Una directiva de etiqueta `<meta>` no admite las siguientes directivas:

* [frame-ancestors](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [report-to](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [report-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [sandbox](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

Para admitir las directivas anteriores, use un encabezado denominado `Content-Security-Policy`. La cadena de directiva es el valor del encabezado.

## <a name="test-a-policy-and-receive-violation-reports"></a>Comprobación de una directiva y recepción de informes de infracciones

Las comprobaciones ayudan a confirmar que no se han bloqueado scripts de terceros por error al crear una directiva inicial.

Para comprobar una directiva durante un período de tiempo sin aplicar directivas, establezca el atributo `http-equiv` de la etiqueta `<meta>` o el nombre de encabezado de una directiva basada en encabezado en `Content-Security-Policy-Report-Only`. Los informes de errores se envían como documentos JSON a la dirección URL que se especifique. Para obtener más información, vea [Documentación web de MDN: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).

Para obtener informes de infracciones mientras una directiva está activa, vea los siguientes artículos:

* [report-to](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [report-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

Aunque ya no se recomienda el uso de `report-uri`, ambas directivas deben usarse hasta que todos los exploradores principales admitan `report-to`. No use `report-uri` única y exclusivamente, ya que la compatibilidad con `report-uri` tiene visos de eliminarse de los exploradores *en cualquier momento*. Quite la compatibilidad con `report-uri` de sus directivas cuando `report-to` sea totalmente compatible. Para realizar un seguimiento del uso de `report-to`, vea [¿Puedo usar report-to?](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to)

Compruebe y actualice la directiva de una aplicación en cada versión.

## <a name="troubleshoot"></a>Solucionar problemas

* Aparecen errores en la consola de herramientas de desarrollo del explorador. Los exploradores proporcionan información sobre lo siguiente:
  * Elementos que no cumplen la directiva
  * Cómo modificar la directiva para permitir un elemento bloqueado
* Una directiva es totalmente efectiva únicamente cuando el explorador del cliente admite todas las directivas incluidas. Para obtener una matriz de compatibilidad de exploradores actuales, vea [¿Puedo usar Content-Security-Policy?](https://caniuse.com/#search=Content-Security-Policy)

## <a name="additional-resources"></a>Recursos adicionales

* [Documentación web de MDN: Content-Security-Policy](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [Directiva de seguridad de contenido de nivel 2](https://www.w3.org/TR/CSP2/)
* [Evaluador de directivas de seguridad de contenido de Google](https://csp-evaluator.withgoogle.com/)
