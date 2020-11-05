---
title: 'Aplicación de una directiva de seguridad de contenido para :::no-loc(Blazor)::: de ASP.NET Core'
author: guardrex
description: 'Obtenga información sobre cómo usar una directiva de seguridad de contenido con aplicaciones :::no-loc(Blazor)::: de ASP.NET Core para protegerse de ataques de scripts de sitios (XSS).'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/security/content-security-policy
ms.openlocfilehash: 66fd41abe4f85071797bacc0a5531bbab35bd227
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055599"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-no-locblazor"></a><span data-ttu-id="b14e1-103">Aplicación de una directiva de seguridad de contenido para :::no-loc(Blazor)::: de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b14e1-103">Enforce a Content Security Policy for ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="b14e1-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b14e1-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b14e1-105">Los [ataques de scripts de sitios (XSS)](xref:security/cross-site-scripting) son una vulnerabilidad de seguridad en la que un atacante coloca uno o más scripts de cliente malintencionados en el contenido representado de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="b14e1-105">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="b14e1-106">Una directiva de seguridad de contenido ayuda a protegerse de ataques XSS, ya que informa al explorador de la validez de los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="b14e1-106">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="b14e1-107">Orígenes de contenido cargado, incluidos scripts, hojas de estilo e imágenes</span><span class="sxs-lookup"><span data-stu-id="b14e1-107">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="b14e1-108">Acciones realizadas por una página, especificando los destinos de URL permitidos de formularios</span><span class="sxs-lookup"><span data-stu-id="b14e1-108">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="b14e1-109">Complementos que se pueden cargar</span><span class="sxs-lookup"><span data-stu-id="b14e1-109">Plugins that can be loaded.</span></span>

<span data-ttu-id="b14e1-110">Para aplicar una directiva de seguridad de contenido en una aplicación, el desarrollador especifica varias *directivas* de seguridad de contenido en uno o varios encabezados `Content-Security-Policy` o etiquetas `<meta>`.</span><span class="sxs-lookup"><span data-stu-id="b14e1-110">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="b14e1-111">El explorador evalúa las directivas mientras una página se carga.</span><span class="sxs-lookup"><span data-stu-id="b14e1-111">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="b14e1-112">Así, inspecciona los orígenes de la página y determina si cumplen los requisitos de las directivas de seguridad de contenido.</span><span class="sxs-lookup"><span data-stu-id="b14e1-112">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="b14e1-113">Cuando un recurso no cumple estas directivas, el explorador no lo carga.</span><span class="sxs-lookup"><span data-stu-id="b14e1-113">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="b14e1-114">Pensemos, por ejemplo, en una directiva que no permite scripts de terceros.</span><span class="sxs-lookup"><span data-stu-id="b14e1-114">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="b14e1-115">Si una página contiene una etiqueta `<script>` con un origen de terceros en el atributo `src`, el explorador impide que el script se cargue.</span><span class="sxs-lookup"><span data-stu-id="b14e1-115">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="b14e1-116">Las directivas de seguridad de contenido se admiten en la mayoría de los exploradores de escritorio y móviles de hoy día, como Chrome, Edge, Firefox, Opera y Safari.</span><span class="sxs-lookup"><span data-stu-id="b14e1-116">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="b14e1-117">Se recomienda usarlas en aplicaciones :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="b14e1-117">CSP is recommended for :::no-loc(Blazor)::: apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="b14e1-118">Directivas</span><span class="sxs-lookup"><span data-stu-id="b14e1-118">Policy directives</span></span>

<span data-ttu-id="b14e1-119">Especifique como mínimo las siguientes directivas y orígenes en las aplicaciones :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="b14e1-119">Minimally, specify the following directives and sources for :::no-loc(Blazor)::: apps.</span></span> <span data-ttu-id="b14e1-120">Agregue más directivas y orígenes según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="b14e1-120">Add additional directives and sources as needed.</span></span> <span data-ttu-id="b14e1-121">Las siguientes directivas se usan en la sección [Aplicación de la directiva](#apply-the-policy) de este artículo, donde se proporcionan directivas de seguridad de ejemplo para :::no-loc(Blazor WebAssembly)::: y :::no-loc(Blazor Server)::::</span><span class="sxs-lookup"><span data-stu-id="b14e1-121">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for :::no-loc(Blazor WebAssembly)::: and :::no-loc(Blazor Server)::: are provided:</span></span>

* <span data-ttu-id="b14e1-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): restringe las direcciones URL según la etiqueta `<base>` de una página.</span><span class="sxs-lookup"><span data-stu-id="b14e1-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="b14e1-123">Especifique `self` para indicar que el origen de la aplicación (incluido el esquema y el número de puerto) es un origen válido.</span><span class="sxs-lookup"><span data-stu-id="b14e1-123">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="b14e1-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): impide la carga de contenido HTTP y HTTPS combinado.</span><span class="sxs-lookup"><span data-stu-id="b14e1-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="b14e1-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): indica una reserva de las directivas de origen que no se han especificado expresamente en la directiva.</span><span class="sxs-lookup"><span data-stu-id="b14e1-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="b14e1-126">Especifique `self` para indicar que el origen de la aplicación (incluido el esquema y el número de puerto) es un origen válido.</span><span class="sxs-lookup"><span data-stu-id="b14e1-126">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="b14e1-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): indica los orígenes de imágenes válidos.</span><span class="sxs-lookup"><span data-stu-id="b14e1-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): Indicates valid sources for images.</span></span>
  * <span data-ttu-id="b14e1-128">Especifique `data:` para permitir la carga de imágenes de direcciones URL `data:`.</span><span class="sxs-lookup"><span data-stu-id="b14e1-128">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="b14e1-129">Especifique `https:` para permitir la carga de imágenes desde puntos de conexión HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b14e1-129">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="b14e1-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): indica los orígenes válidos de las etiquetas `<object>`, `<embed>` y `<applet>`.</span><span class="sxs-lookup"><span data-stu-id="b14e1-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="b14e1-131">Especifique `none` para no permitir ningún origen de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="b14e1-131">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="b14e1-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): indica los orígenes de scripts válidos.</span><span class="sxs-lookup"><span data-stu-id="b14e1-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="b14e1-133">Especifique el origen de host `https://stackpath.bootstrapcdn.com/` para los scripts de arranque.</span><span class="sxs-lookup"><span data-stu-id="b14e1-133">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="b14e1-134">Especifique `self` para indicar que el origen de la aplicación (incluido el esquema y el número de puerto) es un origen válido.</span><span class="sxs-lookup"><span data-stu-id="b14e1-134">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="b14e1-135">En una aplicación :::no-loc(Blazor WebAssembly)::::</span><span class="sxs-lookup"><span data-stu-id="b14e1-135">In a :::no-loc(Blazor WebAssembly)::: app:</span></span>
    * <span data-ttu-id="b14e1-136">Especifique los siguientes hash para permitir la carga de scripts en línea :::no-loc(Blazor WebAssembly)::: necesarios:</span><span class="sxs-lookup"><span data-stu-id="b14e1-136">Specify the following hashes to permit the required :::no-loc(Blazor WebAssembly)::: inline scripts to load:</span></span>
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * <span data-ttu-id="b14e1-137">Especifique `unsafe-eval` para usar `eval()` y métodos para crear código a partir de cadenas.</span><span class="sxs-lookup"><span data-stu-id="b14e1-137">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="b14e1-138">En una aplicación :::no-loc(Blazor Server):::, especifique el hash `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` para el script en línea que realiza la detección de reserva en hojas de estilos.</span><span class="sxs-lookup"><span data-stu-id="b14e1-138">In a :::no-loc(Blazor Server)::: app, specify the `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` hash for the inline script that performs fallback detection for stylesheets.</span></span>
* <span data-ttu-id="b14e1-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): indica los orígenes de hojas de estilo válidos.</span><span class="sxs-lookup"><span data-stu-id="b14e1-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="b14e1-140">Especifique el origen de host `https://stackpath.bootstrapcdn.com/` para las hojas de estilo de arranque.</span><span class="sxs-lookup"><span data-stu-id="b14e1-140">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="b14e1-141">Especifique `self` para indicar que el origen de la aplicación (incluido el esquema y el número de puerto) es un origen válido.</span><span class="sxs-lookup"><span data-stu-id="b14e1-141">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="b14e1-142">Especifique `unsafe-inline` para permitir el uso de estilos en línea.</span><span class="sxs-lookup"><span data-stu-id="b14e1-142">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="b14e1-143">La declaración en línea es necesaria en la interfaz de usuario de las aplicaciones :::no-loc(Blazor Server)::: para volver a conectar el cliente y el servidor después de la solicitud inicial.</span><span class="sxs-lookup"><span data-stu-id="b14e1-143">The inline declaration is required for the UI in :::no-loc(Blazor Server)::: apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="b14e1-144">En una versión futura, es posible que los estilos en línea se eliminen, de forma que `unsafe-inline` ya no sea necesario.</span><span class="sxs-lookup"><span data-stu-id="b14e1-144">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="b14e1-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): indica que las direcciones URL de contenido de orígenes (HTTP) no seguros deben adquirirse de forma segura a través de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b14e1-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="b14e1-146">Todos los exploradores, excepto Microsoft Internet Explorer, admiten las directivas anteriores.</span><span class="sxs-lookup"><span data-stu-id="b14e1-146">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="b14e1-147">Para obtener los hash SHA de otros scripts en línea:</span><span class="sxs-lookup"><span data-stu-id="b14e1-147">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="b14e1-148">Aplique la directiva de seguridad de contenido que aparece en la sección [Aplicación de la directiva](#apply-the-policy).</span><span class="sxs-lookup"><span data-stu-id="b14e1-148">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="b14e1-149">Acceda a la consola de herramientas de desarrollo del explorador mientras ejecuta la aplicación localmente.</span><span class="sxs-lookup"><span data-stu-id="b14e1-149">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="b14e1-150">El explorador calcula y muestra los hash de los scripts bloqueados cuando existe un encabezado de directiva de seguridad de contenido o una etiqueta `meta`.</span><span class="sxs-lookup"><span data-stu-id="b14e1-150">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="b14e1-151">Copie los hash proporcionados por el explorador en los orígenes `script-src`.</span><span class="sxs-lookup"><span data-stu-id="b14e1-151">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="b14e1-152">Inserte cada hash entre comillas simples.</span><span class="sxs-lookup"><span data-stu-id="b14e1-152">Use single quotes around each hash.</span></span>

<span data-ttu-id="b14e1-153">Para obtener una matriz de compatibilidad de exploradores de nivel 2 de directiva de seguridad de contenido, vea [¿Puedo usar una directiva de seguridad de contenido de nivel 2?](https://www.caniuse.com/#feat=contentsecuritypolicy2)</span><span class="sxs-lookup"><span data-stu-id="b14e1-153">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="b14e1-154">Aplicación de la directiva</span><span class="sxs-lookup"><span data-stu-id="b14e1-154">Apply the policy</span></span>

<span data-ttu-id="b14e1-155">Use una etiqueta `<meta>` para aplicar la directiva:</span><span class="sxs-lookup"><span data-stu-id="b14e1-155">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="b14e1-156">Establezca el valor del atributo `http-equiv` en `Content-Security-Policy`.</span><span class="sxs-lookup"><span data-stu-id="b14e1-156">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="b14e1-157">Coloque las directivas en el valor de atributo `content`.</span><span class="sxs-lookup"><span data-stu-id="b14e1-157">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="b14e1-158">Separe las directivas con un punto y coma (`;`).</span><span class="sxs-lookup"><span data-stu-id="b14e1-158">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="b14e1-159">Coloque siempre la etiqueta `meta` en el contenido de `<head>`.</span><span class="sxs-lookup"><span data-stu-id="b14e1-159">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="b14e1-160">En las secciones siguientes se muestran las directivas de ejemplo para :::no-loc(Blazor WebAssembly)::: y :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="b14e1-160">The following sections show example policies for :::no-loc(Blazor WebAssembly)::: and :::no-loc(Blazor Server):::.</span></span> <span data-ttu-id="b14e1-161">Estos ejemplos pertenecen a la versión de :::no-loc(Blazor)::: correspondiente a este artículo.</span><span class="sxs-lookup"><span data-stu-id="b14e1-161">These examples are versioned with this article for each release of :::no-loc(Blazor):::.</span></span> <span data-ttu-id="b14e1-162">Para usar la versión adecuada en su caso, seleccione la versión del documento usando el selector desplegable **Versión** de esta página web.</span><span class="sxs-lookup"><span data-stu-id="b14e1-162">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="b14e1-163">En el contenido de `<head>` de la página host `wwwroot/index.html`, aplique las directivas descritas en la sección [Directivas](#policy-directives):</span><span class="sxs-lookup"><span data-stu-id="b14e1-163">In the `<head>` content of the `wwwroot/index.html` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

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

### :::no-loc(Blazor Server):::

<span data-ttu-id="b14e1-164">En el contenido de `<head>` de la página host `Pages/_Host.cshtml`, aplique las directivas descritas en la sección [Directivas](#policy-directives):</span><span class="sxs-lookup"><span data-stu-id="b14e1-164">In the `<head>` content of the `Pages/_Host.cshtml` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

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

## <a name="meta-tag-limitations"></a><span data-ttu-id="b14e1-165">Limitaciones de la etiqueta meta</span><span class="sxs-lookup"><span data-stu-id="b14e1-165">Meta tag limitations</span></span>

<span data-ttu-id="b14e1-166">Una directiva de etiqueta `<meta>` no admite las siguientes directivas:</span><span class="sxs-lookup"><span data-stu-id="b14e1-166">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="b14e1-167">frame-ancestors</span><span class="sxs-lookup"><span data-stu-id="b14e1-167">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="b14e1-168">report-to</span><span class="sxs-lookup"><span data-stu-id="b14e1-168">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="b14e1-169">report-uri</span><span class="sxs-lookup"><span data-stu-id="b14e1-169">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="b14e1-170">sandbox</span><span class="sxs-lookup"><span data-stu-id="b14e1-170">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="b14e1-171">Para admitir las directivas anteriores, use un encabezado denominado `Content-Security-Policy`.</span><span class="sxs-lookup"><span data-stu-id="b14e1-171">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="b14e1-172">La cadena de directiva es el valor del encabezado.</span><span class="sxs-lookup"><span data-stu-id="b14e1-172">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="b14e1-173">Comprobación de una directiva y recepción de informes de infracciones</span><span class="sxs-lookup"><span data-stu-id="b14e1-173">Test a policy and receive violation reports</span></span>

<span data-ttu-id="b14e1-174">Las comprobaciones ayudan a confirmar que no se han bloqueado scripts de terceros por error al crear una directiva inicial.</span><span class="sxs-lookup"><span data-stu-id="b14e1-174">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="b14e1-175">Para comprobar una directiva durante un período de tiempo sin aplicar directivas, establezca el atributo `http-equiv` de la etiqueta `<meta>` o el nombre de encabezado de una directiva basada en encabezado en `Content-Security-Policy-Report-Only`.</span><span class="sxs-lookup"><span data-stu-id="b14e1-175">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="b14e1-176">Los informes de errores se envían como documentos JSON a la dirección URL que se especifique.</span><span class="sxs-lookup"><span data-stu-id="b14e1-176">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="b14e1-177">Para obtener más información, vea [Documentación web de MDN: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span><span class="sxs-lookup"><span data-stu-id="b14e1-177">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="b14e1-178">Para obtener informes de infracciones mientras una directiva está activa, vea los siguientes artículos:</span><span class="sxs-lookup"><span data-stu-id="b14e1-178">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="b14e1-179">report-to</span><span class="sxs-lookup"><span data-stu-id="b14e1-179">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="b14e1-180">report-uri</span><span class="sxs-lookup"><span data-stu-id="b14e1-180">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="b14e1-181">Aunque ya no se recomienda el uso de `report-uri`, ambas directivas deben usarse hasta que todos los exploradores principales admitan `report-to`.</span><span class="sxs-lookup"><span data-stu-id="b14e1-181">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="b14e1-182">No use `report-uri` única y exclusivamente, ya que la compatibilidad con `report-uri` tiene visos de eliminarse de los exploradores *en cualquier momento*.</span><span class="sxs-lookup"><span data-stu-id="b14e1-182">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="b14e1-183">Quite la compatibilidad con `report-uri` de sus directivas cuando `report-to` sea totalmente compatible.</span><span class="sxs-lookup"><span data-stu-id="b14e1-183">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="b14e1-184">Para realizar un seguimiento del uso de `report-to`, vea [¿Puedo usar report-to?](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to)</span><span class="sxs-lookup"><span data-stu-id="b14e1-184">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="b14e1-185">Compruebe y actualice la directiva de una aplicación en cada versión.</span><span class="sxs-lookup"><span data-stu-id="b14e1-185">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="b14e1-186">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="b14e1-186">Troubleshoot</span></span>

* <span data-ttu-id="b14e1-187">Aparecen errores en la consola de herramientas de desarrollo del explorador.</span><span class="sxs-lookup"><span data-stu-id="b14e1-187">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="b14e1-188">Los exploradores proporcionan información sobre lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b14e1-188">Browsers provide information about:</span></span>
  * <span data-ttu-id="b14e1-189">Elementos que no cumplen la directiva</span><span class="sxs-lookup"><span data-stu-id="b14e1-189">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="b14e1-190">Cómo modificar la directiva para permitir un elemento bloqueado</span><span class="sxs-lookup"><span data-stu-id="b14e1-190">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="b14e1-191">Una directiva es totalmente efectiva únicamente cuando el explorador del cliente admite todas las directivas incluidas.</span><span class="sxs-lookup"><span data-stu-id="b14e1-191">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="b14e1-192">Para obtener una matriz de compatibilidad de exploradores actuales, vea [¿Puedo usar Content-Security-Policy?](https://caniuse.com/#search=Content-Security-Policy)</span><span class="sxs-lookup"><span data-stu-id="b14e1-192">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b14e1-193">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="b14e1-193">Additional resources</span></span>

* [<span data-ttu-id="b14e1-194">Documentación web de MDN: Content-Security-Policy</span><span class="sxs-lookup"><span data-stu-id="b14e1-194">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="b14e1-195">Directiva de seguridad de contenido de nivel 2</span><span class="sxs-lookup"><span data-stu-id="b14e1-195">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="b14e1-196">Evaluador de directivas de seguridad de contenido de Google</span><span class="sxs-lookup"><span data-stu-id="b14e1-196">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)
