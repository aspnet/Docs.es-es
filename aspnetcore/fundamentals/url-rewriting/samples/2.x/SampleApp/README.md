# <a name="aspnet-core-url-rewriting-sample"></a><span data-ttu-id="181d2-101">Ejemplo de reescritura de URL de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="181d2-101">ASP.NET Core URL Rewriting Sample</span></span>

<span data-ttu-id="181d2-102">En este ejemplo se muestra el uso del software intermedio de reescritura de URL de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="181d2-102">This sample illustrates usage of ASP.NET Core URL Rewriting Middleware.</span></span> <span data-ttu-id="181d2-103">En la aplicación se muestran las opciones de redireccionamiento y reescritura de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="181d2-103">The app demonstrates URL redirect and URL rewriting options.</span></span>

<span data-ttu-id="181d2-104">Al ejecutar el ejemplo, las respuestas que no son archivos devuelven la dirección URL reescrita o redirigida al aplicar una de las reglas a una dirección URL de solicitud.</span><span class="sxs-lookup"><span data-stu-id="181d2-104">When running the sample, non-file responses return the rewritten or redirected URL when one of the rules is applied to a request URL.</span></span> <span data-ttu-id="181d2-105">Para los ejemplos XML y de archivo de texto, el middleware de archivos estáticos sirve el archivo después de reescribir la dirección URL de solicitud.</span><span class="sxs-lookup"><span data-stu-id="181d2-105">For the XML and text file examples, Static File Middleware serves the file after the request URL is rewritten by the middleware.</span></span>

## <a name="examples-in-this-sample"></a><span data-ttu-id="181d2-106">Ejemplos que se incluyen</span><span class="sxs-lookup"><span data-stu-id="181d2-106">Examples in this sample</span></span>

* `AddRedirect("redirect-rule/(.*)", "redirected/$1")`
  - <span data-ttu-id="181d2-107">Código de estado correcto: 302 (Encontrado)</span><span class="sxs-lookup"><span data-stu-id="181d2-107">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="181d2-108">Ejemplo (redireccionamiento): **/redirect-rule/{capture_group}** a **/redirected/{capture_group}**</span><span class="sxs-lookup"><span data-stu-id="181d2-108">Example (redirect): **/redirect-rule/{capture_group}** to **/redirected/{capture_group}**</span></span>
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - <span data-ttu-id="181d2-109">Código de estado correcto: 200 - Correcto</span><span class="sxs-lookup"><span data-stu-id="181d2-109">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="181d2-110">Ejemplo (reescritura): **/rewrite-rule/{capture_group_1}/{capture_group_2}** a **/rewritten?var1={capture_group_1}&var2={capture_group_2}**</span><span class="sxs-lookup"><span data-stu-id="181d2-110">Example (rewrite): **/rewrite-rule/{capture_group_1}/{capture_group_2}** to **/rewritten?var1={capture_group_1}&var2={capture_group_2}**</span></span>
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - <span data-ttu-id="181d2-111">Código de estado correcto: 302 (Encontrado)</span><span class="sxs-lookup"><span data-stu-id="181d2-111">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="181d2-112">Ejemplo (redireccionamiento): **/apache-mod-rules-redirect/{capture_group}** a **/redirected?id={capture_group}**</span><span class="sxs-lookup"><span data-stu-id="181d2-112">Example (redirect): **/apache-mod-rules-redirect/{capture_group}** to **/redirected?id={capture_group}**</span></span>
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - <span data-ttu-id="181d2-113">Código de estado correcto: 200 - Correcto</span><span class="sxs-lookup"><span data-stu-id="181d2-113">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="181d2-114">Ejemplo (reescritura): **/iis-rules-rewrite/{capture_group}** a **/rewritten?id={capture_group}**</span><span class="sxs-lookup"><span data-stu-id="181d2-114">Example (rewrite): **/iis-rules-rewrite/{capture_group}** to **/rewritten?id={capture_group}**</span></span>
* `Add(RedirectXmlFileRequests)`
  - <span data-ttu-id="181d2-115">Código de estado correcto: 301 - Movido definitivamente</span><span class="sxs-lookup"><span data-stu-id="181d2-115">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="181d2-116">Ejemplo (redireccionamiento): **/file.xml** a **/xmlfiles/file.xml**</span><span class="sxs-lookup"><span data-stu-id="181d2-116">Example (redirect): **/file.xml** to **/xmlfiles/file.xml**</span></span>
* `Add(RewriteTextFileRequests)`
  - <span data-ttu-id="181d2-117">Código de estado correcto: 200 - Correcto</span><span class="sxs-lookup"><span data-stu-id="181d2-117">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="181d2-118">Ejemplo (reescritura): **/un_archivo.txt** por **/archivo.txt**</span><span class="sxs-lookup"><span data-stu-id="181d2-118">Example (rewrite): **/some_file.txt** to **/file.txt**</span></span>
* `Add(new RedirectImageRequests(".png", "/png-images")))`<br>`Add(new RedirectImageRequests(".jpg", "/jpg-images")))`
  - <span data-ttu-id="181d2-119">Código de estado correcto: 301 - Movido definitivamente</span><span class="sxs-lookup"><span data-stu-id="181d2-119">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="181d2-120">Ejemplo (redireccionamiento): **/image.png** a **/png-images/image.png**</span><span class="sxs-lookup"><span data-stu-id="181d2-120">Example (redirect): **/image.png** to **/png-images/image.png**</span></span>
  - <span data-ttu-id="181d2-121">Ejemplo (redireccionamiento): **/image.jpg** a **/jpg-images/image.jpg**</span><span class="sxs-lookup"><span data-stu-id="181d2-121">Example (redirect): **/image.jpg** to **/jpg-images/image.jpg**</span></span>

## <a name="use-a-physicalfileprovider"></a><span data-ttu-id="181d2-122">Uso de PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="181d2-122">Use a PhysicalFileProvider</span></span>

<span data-ttu-id="181d2-123">También puede obtener `IFileProvider` mediante la creación de un `PhysicalFileProvider` que se pasará a los métodos `AddApacheModRewrite()` y `AddIISUrlRewrite()`:</span><span class="sxs-lookup"><span data-stu-id="181d2-123">You can also obtain an `IFileProvider` by creating a `PhysicalFileProvider` to pass into the `AddApacheModRewrite()` and `AddIISUrlRewrite()` methods:</span></span>

```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```

## <a name="secure-redirection-extensions"></a><span data-ttu-id="181d2-124">Extensiones de redireccionamiento seguro</span><span class="sxs-lookup"><span data-stu-id="181d2-124">Secure redirection extensions</span></span>

<span data-ttu-id="181d2-125">En este ejemplo se incluye la configuración de `WebHostBuilder` para que la aplicación use direcciones URL (`https://localhost:5001`, `https://localhost`) y un certificado de prueba (*testCert.pfx*) para ayudar a explorar los métodos de redireccionamiento seguro.</span><span class="sxs-lookup"><span data-stu-id="181d2-125">This sample includes `WebHostBuilder` configuration for the app to use URLs (`https://localhost:5001`, `https://localhost`) and a test certificate (*testCert.pfx*) to assist in exploring the secure redirect methods.</span></span> <span data-ttu-id="181d2-126">Si el servidor ya tiene el puerto 443 asignado o está en uso, el ejemplo `https://localhost` no funciona; quite el elemento `ListenOptions` para el puerto 443 en el método `CreateWebHostBuilder` del archivo *Program.cs* o desenlace el puerto 443 en el servidor para que Kestrel pueda usarlo.</span><span class="sxs-lookup"><span data-stu-id="181d2-126">If the server already has port 443 assigned or in use, the `https://localhost` example doesn't work&mdash;remove the `ListenOptions` for port 443 in the `CreateWebHostBuilder` method of the *Program.cs* file or unbind port 443 on the server so that Kestrel can use the port.</span></span>

| <span data-ttu-id="181d2-127">Método</span><span class="sxs-lookup"><span data-stu-id="181d2-127">Method</span></span>                           | <span data-ttu-id="181d2-128">Código de estado</span><span class="sxs-lookup"><span data-stu-id="181d2-128">Status Code</span></span> |    <span data-ttu-id="181d2-129">Puerto</span><span class="sxs-lookup"><span data-stu-id="181d2-129">Port</span></span>    |
| -------------------------------- | :---------: | :--------: |
| `.AddRedirectToHttpsPermanent()` |     <span data-ttu-id="181d2-130">301</span><span class="sxs-lookup"><span data-stu-id="181d2-130">301</span></span>     | <span data-ttu-id="181d2-131">null (465)</span><span class="sxs-lookup"><span data-stu-id="181d2-131">null (465)</span></span> |
| `.AddRedirectToHttps()`          |     <span data-ttu-id="181d2-132">302</span><span class="sxs-lookup"><span data-stu-id="181d2-132">302</span></span>     | <span data-ttu-id="181d2-133">null (465)</span><span class="sxs-lookup"><span data-stu-id="181d2-133">null (465)</span></span> |
| `.AddRedirectToHttps(301)`       |     <span data-ttu-id="181d2-134">301</span><span class="sxs-lookup"><span data-stu-id="181d2-134">301</span></span>     | <span data-ttu-id="181d2-135">null (465)</span><span class="sxs-lookup"><span data-stu-id="181d2-135">null (465)</span></span> |
| `.AddRedirectToHttps(301, 5001)` |     <span data-ttu-id="181d2-136">301</span><span class="sxs-lookup"><span data-stu-id="181d2-136">301</span></span>     |    <span data-ttu-id="181d2-137">5001</span><span class="sxs-lookup"><span data-stu-id="181d2-137">5001</span></span>    |
