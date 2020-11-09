---
title: Carga de archivos en ASP.NET Core
author: rick-anderson
description: Cómo usar el enlace de modelos y el streaming para cargar archivos en ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/21/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/models/file-uploads
ms.openlocfilehash: 14561bace565c104d0a9c926cad3105c4865e72a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061176"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="8a080-103">Carga de archivos en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8a080-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="8a080-104">Por [Steve Smith](https://ardalis.com/) y [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="8a080-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="8a080-105">ASP.NET Core admite la carga de uno o varios archivos mediante el enlace de modelos almacenado en búfer de archivos más pequeños y el streaming no almacenado en búfer de archivos de mayor tamaño.</span><span class="sxs-lookup"><span data-stu-id="8a080-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="8a080-106">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8a080-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="8a080-107">Consideraciones sobre la seguridad</span><span class="sxs-lookup"><span data-stu-id="8a080-107">Security considerations</span></span>

<span data-ttu-id="8a080-108">Tenga precaución al proporcionar a los usuarios la capacidad de cargar archivos en un servidor.</span><span class="sxs-lookup"><span data-stu-id="8a080-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="8a080-109">Es posible que los atacantes intenten lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a080-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="8a080-110">Ejecutar ataques por [denegación de servicio](/windows-hardware/drivers/ifs/denial-of-service).</span><span class="sxs-lookup"><span data-stu-id="8a080-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="8a080-111">Cargar virus o malware.</span><span class="sxs-lookup"><span data-stu-id="8a080-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="8a080-112">Poner en riesgo redes y servidores de otras maneras.</span><span class="sxs-lookup"><span data-stu-id="8a080-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="8a080-113">Estos son algunos de los pasos de seguridad con los que se reduce la probabilidad de sufrir ataques:</span><span class="sxs-lookup"><span data-stu-id="8a080-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="8a080-114">Cargue los archivos a un área de carga de archivos dedicada, preferiblemente una unidad que no sea de sistema.</span><span class="sxs-lookup"><span data-stu-id="8a080-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="8a080-115">Una ubicación dedicada facilita la imposición de restricciones de seguridad en los archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="8a080-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="8a080-116">Deshabilite la ejecución de los permisos en la ubicación de carga de archivos.&dagger;</span><span class="sxs-lookup"><span data-stu-id="8a080-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="8a080-117">Los archivos cargados **no** se deben persistir en el mismo árbol de directorio que la aplicación.&dagger;</span><span class="sxs-lookup"><span data-stu-id="8a080-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="8a080-118">Use un nombre de archivo seguro determinado por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a080-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="8a080-119">No use un nombre de archivo proporcionado por el usuario o el nombre de archivo que no es de confianza del archivo cargado. &dagger; HTML codifica el nombre de archivo que no es de confianza al mostrarlo.</span><span class="sxs-lookup"><span data-stu-id="8a080-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="8a080-120">Por ejemplo, registrando el nombre de archivo o mostrándose en la interfaz de usuario (la Razor salida codifica automáticamente html).</span><span class="sxs-lookup"><span data-stu-id="8a080-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="8a080-121">Permita solo las extensiones de archivo aprobadas para la especificación de diseño de la aplicación.&dagger;</span><span class="sxs-lookup"><span data-stu-id="8a080-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="8a080-122">Compruebe que las comprobaciones del lado cliente se realizan en el servidor. &dagger; Las comprobaciones del lado cliente son fáciles de eludir.</span><span class="sxs-lookup"><span data-stu-id="8a080-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="8a080-123">Compruebe el tamaño de un archivo cargado.</span><span class="sxs-lookup"><span data-stu-id="8a080-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="8a080-124">Establezca un límite de tamaño máximo para evitar cargas grandes.&dagger;</span><span class="sxs-lookup"><span data-stu-id="8a080-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="8a080-125">Cuando un archivo cargado con el mismo nombre no deba sobrescribir los archivos, vuelva a comprobar el nombre de archivo en la base de datos o en el almacenamiento físico antes de cargarlo.</span><span class="sxs-lookup"><span data-stu-id="8a080-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="8a080-126">**Ejecute un detector de virus o malware en el contenido cargado antes de que se almacene el archivo.**</span><span class="sxs-lookup"><span data-stu-id="8a080-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="8a080-127">&dagger;La aplicación de ejemplo muestra un enfoque que cumple los criterios.</span><span class="sxs-lookup"><span data-stu-id="8a080-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="8a080-128">La carga de código malintencionado en un sistema suele ser el primer paso para ejecutar código que puede:</span><span class="sxs-lookup"><span data-stu-id="8a080-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="8a080-129">Obtener el control completo de un sistema.</span><span class="sxs-lookup"><span data-stu-id="8a080-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="8a080-130">Sobrecargar un sistema de manera que el sistema se bloquea.</span><span class="sxs-lookup"><span data-stu-id="8a080-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="8a080-131">Poner en peligro los datos del usuario o del sistema.</span><span class="sxs-lookup"><span data-stu-id="8a080-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="8a080-132">Aplicar grafitis a una interfaz de usuario pública.</span><span class="sxs-lookup"><span data-stu-id="8a080-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="8a080-133">Vea los siguientes recursos para más información sobre cómo reducir el área expuesta de ataques al aceptar archivos de los usuarios:</span><span class="sxs-lookup"><span data-stu-id="8a080-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="8a080-134">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carga de archivos sin restricciones)</span><span class="sxs-lookup"><span data-stu-id="8a080-134">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="8a080-135">Azure Security: asegúrese de que los controles adecuados estén en vigor al aceptar archivos de usuarios</span><span class="sxs-lookup"><span data-stu-id="8a080-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="8a080-136">Para más información sobre cómo implementar medidas de seguridad, incluidos ejemplos de la aplicación de ejemplo, consulte la sección [Validación](#validation).</span><span class="sxs-lookup"><span data-stu-id="8a080-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="8a080-137">Escenarios de almacenamiento</span><span class="sxs-lookup"><span data-stu-id="8a080-137">Storage scenarios</span></span>

<span data-ttu-id="8a080-138">Las opciones de almacenamiento comunes para los archivos incluyen:</span><span class="sxs-lookup"><span data-stu-id="8a080-138">Common storage options for files include:</span></span>

* <span data-ttu-id="8a080-139">Base de datos</span><span class="sxs-lookup"><span data-stu-id="8a080-139">Database</span></span>

  * <span data-ttu-id="8a080-140">En el caso de cargas de archivos pequeñas, una base de datos suele ser más rápida que las opciones de almacenamiento físico (sistema de archivos o recurso compartido de red).</span><span class="sxs-lookup"><span data-stu-id="8a080-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="8a080-141">Una base de datos suele ser más conveniente que las opciones de almacenamiento físico, ya que la recuperación de un registro de base de datos para los datos de usuario puede proporcionar el contenido del archivo (por ejemplo, una imagen de avatar).</span><span class="sxs-lookup"><span data-stu-id="8a080-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="8a080-142">Una base de datos puede ser más económica que usar un servicio de almacenamiento de datos.</span><span class="sxs-lookup"><span data-stu-id="8a080-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="8a080-143">Almacenamiento físico (sistema de archivos o recurso compartido de red)</span><span class="sxs-lookup"><span data-stu-id="8a080-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="8a080-144">Para cargas de archivos de gran tamaño:</span><span class="sxs-lookup"><span data-stu-id="8a080-144">For large file uploads:</span></span>
    * <span data-ttu-id="8a080-145">Los límites de base de datos pueden restringir el tamaño de la carga.</span><span class="sxs-lookup"><span data-stu-id="8a080-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="8a080-146">El almacenamiento físico suele ser menos económico que el almacenamiento en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="8a080-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="8a080-147">El almacenamiento físico puede ser más económico que usar un servicio de almacenamiento de datos.</span><span class="sxs-lookup"><span data-stu-id="8a080-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="8a080-148">El proceso de la aplicación debe tener permisos de lectura y escritura en la ubicación de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="8a080-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="8a080-149">**Nunca conceda el permiso de ejecución.**</span><span class="sxs-lookup"><span data-stu-id="8a080-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="8a080-150">Servicio de almacenamiento de datos (por ejemplo, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="8a080-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="8a080-151">Los servicios suelen ofrecer una escalabilidad y resistencia mejoradas sobre las soluciones locales que normalmente están sujetas a únicos puntos de error.</span><span class="sxs-lookup"><span data-stu-id="8a080-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="8a080-152">Los servicios pueden tener un costo menor en escenarios de infraestructura de almacenamiento de gran tamaño.</span><span class="sxs-lookup"><span data-stu-id="8a080-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="8a080-153">Para obtener más información, vea [Inicio rápido: usar .net para crear un BLOB en el almacenamiento de objetos](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="8a080-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="8a080-154">Escenarios de carga de archivos</span><span class="sxs-lookup"><span data-stu-id="8a080-154">File upload scenarios</span></span>

<span data-ttu-id="8a080-155">Dos enfoques generales para cargar archivos son el almacenamiento en búfer y el streaming.</span><span class="sxs-lookup"><span data-stu-id="8a080-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="8a080-156">**de respuesta**</span><span class="sxs-lookup"><span data-stu-id="8a080-156">**Buffering**</span></span>

<span data-ttu-id="8a080-157">El archivo completo se lee en un <xref:Microsoft.AspNetCore.Http.IFormFile>, que es una representación de C# del archivo que se usa para procesar o guardar el archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="8a080-158">Los recursos (disco, memoria) que se usan en las cargas de archivos dependen de la cantidad y del tamaño de las cargas de archivos que se realizan simultáneamente.</span><span class="sxs-lookup"><span data-stu-id="8a080-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="8a080-159">Si una aplicación intenta almacenar demasiadas cargas en el búfer, el sitio se bloquea cuando se queda sin memoria o sin espacio en disco.</span><span class="sxs-lookup"><span data-stu-id="8a080-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="8a080-160">Si el tamaño o la frecuencia de las cargas de archivos está agotando los recursos de la aplicación, use el streaming.</span><span class="sxs-lookup"><span data-stu-id="8a080-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="8a080-161">Cualquier archivo almacenado en búfer único que supere los 64 KB se mueve de la memoria a un archivo temporal en el disco.</span><span class="sxs-lookup"><span data-stu-id="8a080-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="8a080-162">En las secciones siguientes de este tema se habla del almacenamiento en búfer de archivos pequeños:</span><span class="sxs-lookup"><span data-stu-id="8a080-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="8a080-163">Almacenamiento físico</span><span class="sxs-lookup"><span data-stu-id="8a080-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="8a080-164">Base de datos</span><span class="sxs-lookup"><span data-stu-id="8a080-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="8a080-165">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="8a080-165">**Streaming**</span></span>

<span data-ttu-id="8a080-166">El archivo se recibe de una solicitud de varias partes y lo procesa o guarda directamente la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a080-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="8a080-167">El streaming no mejora considerablemente el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="8a080-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="8a080-168">El streaming reduce las demandas de memoria o espacio en disco cuando se cargan archivos.</span><span class="sxs-lookup"><span data-stu-id="8a080-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="8a080-169">El streaming de archivos grandes se describe en la sección [Carga de archivos de gran tamaño con streaming](#upload-large-files-with-streaming).</span><span class="sxs-lookup"><span data-stu-id="8a080-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="8a080-170">Carga de archivos pequeños con enlace de modelos almacenado en búfer al almacenamiento físico</span><span class="sxs-lookup"><span data-stu-id="8a080-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="8a080-171">Para cargar archivos pequeños, se puede usar un formulario de varias partes o construir una solicitud POST con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8a080-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="8a080-172">En el ejemplo siguiente se muestra el uso de un Razor formulario de páginas para cargar un único archivo ( *pages/BufferedSingleFileUploadPhysical. cshtml* en la aplicación de ejemplo):</span><span class="sxs-lookup"><span data-stu-id="8a080-172">The following example demonstrates the use of a Razor Pages form to upload a single file ( *Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="8a080-173">El ejemplo siguiente es análogo al ejemplo anterior, salvo en que:</span><span class="sxs-lookup"><span data-stu-id="8a080-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="8a080-174">([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) de JavaScript se usa para enviar los datos del formulario.</span><span class="sxs-lookup"><span data-stu-id="8a080-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="8a080-175">No hay ninguna validación.</span><span class="sxs-lookup"><span data-stu-id="8a080-175">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="8a080-176">Para realizar la solicitud POST en JavaScript para los clientes que [no admiten Fetch API](https://caniuse.com/#feat=fetch), use uno de estos enfoques:</span><span class="sxs-lookup"><span data-stu-id="8a080-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="8a080-177">Use un Fetch Polyfill (por ejemplo, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="8a080-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="8a080-178">Use `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="8a080-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="8a080-179">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8a080-179">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="8a080-180">Para admitir las cargas de archivos, los formularios HTML deben especificar un tipo de codificación (`enctype`) de `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="8a080-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="8a080-181">Para que un elemento de entrada `files` admita al carga de varios archivos, proporcione el atributo `multiple` en el elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="8a080-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="8a080-182">Es posible acceder a archivos individuales cargados en el servidor a través del [enlace de modelos](xref:mvc/models/model-binding) mediante <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="8a080-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="8a080-183">La aplicación de ejemplo muestra varias cargas de archivos almacenados en búfer para escenarios de almacenamiento físico y base de datos.</span><span class="sxs-lookup"><span data-stu-id="8a080-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="8a080-184">**No** utilice la propiedad `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> para usos distintos a la presentación y el registro.</span><span class="sxs-lookup"><span data-stu-id="8a080-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="8a080-185">Para fines de presentación y registro, codifique el nombre de archivo en HTML.</span><span class="sxs-lookup"><span data-stu-id="8a080-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="8a080-186">Un atacante puede proporcionar un nombre de archivo malintencionado, incluidas rutas de acceso completas o relativas.</span><span class="sxs-lookup"><span data-stu-id="8a080-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="8a080-187">Las aplicaciones deben:</span><span class="sxs-lookup"><span data-stu-id="8a080-187">Applications should:</span></span>
>
> * <span data-ttu-id="8a080-188">Quitar la ruta de acceso del nombre de archivo proporcionado por el usuario.</span><span class="sxs-lookup"><span data-stu-id="8a080-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="8a080-189">Guardar el nombre de archivo codificado en HTML, sin la ruta de acceso para la interfaz de usuario o el registro.</span><span class="sxs-lookup"><span data-stu-id="8a080-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="8a080-190">Generar un nombre de archivo aleatorio nuevo para el almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="8a080-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="8a080-191">En el código siguiente se quita la ruta de acceso del nombre del archivo:</span><span class="sxs-lookup"><span data-stu-id="8a080-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="8a080-192">Los ejemplos proporcionados hasta ahora no tienen en cuenta las consideraciones de seguridad.</span><span class="sxs-lookup"><span data-stu-id="8a080-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="8a080-193">Se proporciona información adicional en las secciones siguientes y en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="8a080-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="8a080-194">Consideraciones de seguridad</span><span class="sxs-lookup"><span data-stu-id="8a080-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="8a080-195">Validación</span><span class="sxs-lookup"><span data-stu-id="8a080-195">Validation</span></span>](#validation)

<span data-ttu-id="8a080-196">Al cargar archivos mediante el enlace de modelos y <xref:Microsoft.AspNetCore.Http.IFormFile>, el método de acción puede aceptar:</span><span class="sxs-lookup"><span data-stu-id="8a080-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="8a080-197">Un <xref:Microsoft.AspNetCore.Http.IFormFile> único.</span><span class="sxs-lookup"><span data-stu-id="8a080-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="8a080-198">Cualquiera de las colecciones siguientes que representan varios archivos:</span><span class="sxs-lookup"><span data-stu-id="8a080-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="8a080-199">[Lista](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="8a080-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="8a080-200">El enlace coincide con los archivos de formulario por nombre.</span><span class="sxs-lookup"><span data-stu-id="8a080-200">Binding matches form files by name.</span></span> <span data-ttu-id="8a080-201">Por ejemplo, el valor `name` HTML en `<input type="file" name="formFile">` debe coincidir con la propiedad/el parámetro enlazado de C# (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="8a080-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="8a080-202">Para más información, consulte la sección [Coincidencia del valor de atributo de nombre con el nombre del parámetro del método POST](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="8a080-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="8a080-203">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a080-203">The following example:</span></span>

* <span data-ttu-id="8a080-204">Recorre en bucle uno o más archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="8a080-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="8a080-205">Usa [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) para devolver una ruta de acceso completa de un archivo, incluido el nombre de archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="8a080-206">Guarda los archivos en el sistema de archivos local con un nombre de archivo generado por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a080-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="8a080-207">Devuelve el número total y el tamaño de los archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="8a080-207">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="8a080-208">Use `Path.GetRandomFileName` para generar un nombre de archivo sin una ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="8a080-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="8a080-209">En el ejemplo siguiente, la ruta de acceso se obtiene de la configuración:</span><span class="sxs-lookup"><span data-stu-id="8a080-209">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="8a080-210">La ruta de acceso pasada a <xref:System.IO.FileStream> *debe* incluir el nombre de archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="8a080-211">Si no se proporciona el nombre de archivo, se produce una <xref:System.UnauthorizedAccessException> en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="8a080-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="8a080-212">Los archivos que se cargan usando la técnica <xref:Microsoft.AspNetCore.Http.IFormFile> se almacenan en búfer en memoria o en disco en el servidor web antes de procesarse.</span><span class="sxs-lookup"><span data-stu-id="8a080-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="8a080-213">Dentro del método de acción, se puede tener acceso al contenido de <xref:Microsoft.AspNetCore.Http.IFormFile> como <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="8a080-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="8a080-214">Además del sistema de archivos local, los archivos se pueden guardar en un recurso compartido de red o en un servicio de almacenamiento de archivos, como [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="8a080-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="8a080-215">Para ver otro ejemplo que recorre en bucle varios archivos para cargar y usa nombres de archivo seguros, consulte *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="8a080-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="8a080-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) arroja una <xref:System.IO.IOException> si se crean más de 65 535 archivos sin eliminar los archivos temporales anteriores.</span><span class="sxs-lookup"><span data-stu-id="8a080-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="8a080-217">El límite de 65 535 archivos es un límite por servidor.</span><span class="sxs-lookup"><span data-stu-id="8a080-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="8a080-218">Para más información sobre este límite en el sistema operativo Windows, consulte las notas en los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="8a080-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="8a080-219">Función GetTempFileNameA</span><span class="sxs-lookup"><span data-stu-id="8a080-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="8a080-220">Carga de archivos pequeños con enlace de modelos almacenado en búfer a una base de datos</span><span class="sxs-lookup"><span data-stu-id="8a080-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="8a080-221">Para almacenar datos de archivo binario en una base de datos con [Entity Framework](/ef/core/index), defina una propiedad de matriz <xref:System.Byte> en la entidad:</span><span class="sxs-lookup"><span data-stu-id="8a080-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="8a080-222">Especifique una propiedad de modelo de página para la clase que incluya un <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="8a080-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="8a080-223"><xref:Microsoft.AspNetCore.Http.IFormFile> se puede usar directamente como un parámetro de método de acción o como una propiedad de modelo enlazado.</span><span class="sxs-lookup"><span data-stu-id="8a080-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="8a080-224">En el ejemplo anterior se utiliza una propiedad de modelo enlazado.</span><span class="sxs-lookup"><span data-stu-id="8a080-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="8a080-225">`FileUpload`Se utiliza en el Razor formulario de páginas:</span><span class="sxs-lookup"><span data-stu-id="8a080-225">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="8a080-226">Cuando el formulario se publique en el servidor, copie el <xref:Microsoft.AspNetCore.Http.IFormFile> en un flujo y guárdelo como matriz de bytes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8a080-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="8a080-227">En el ejemplo siguiente, `_dbContext` almacena el contexto de base de datos de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="8a080-227">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="8a080-228">El ejemplo anterior es similar a un escenario que se muestra en la aplicación de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8a080-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="8a080-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8a080-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="8a080-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="8a080-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="8a080-231">Tenga cuidado al almacenar los datos binarios en bases de datos relacionales, ya que esto puede repercutir adversamente en el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="8a080-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="8a080-232">No se base ni confíe en la propiedad `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> sin validarla.</span><span class="sxs-lookup"><span data-stu-id="8a080-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="8a080-233">La propiedad `FileName` solo debe usarse para fines de presentación y solo después de la codificación HTML.</span><span class="sxs-lookup"><span data-stu-id="8a080-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="8a080-234">Los ejemplos proporcionados no tienen en cuenta las consideraciones de seguridad.</span><span class="sxs-lookup"><span data-stu-id="8a080-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="8a080-235">Se proporciona información adicional en las secciones siguientes y en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="8a080-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="8a080-236">Consideraciones de seguridad</span><span class="sxs-lookup"><span data-stu-id="8a080-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="8a080-237">Validación</span><span class="sxs-lookup"><span data-stu-id="8a080-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="8a080-238">Carga de archivos de gran tamaño con streaming</span><span class="sxs-lookup"><span data-stu-id="8a080-238">Upload large files with streaming</span></span>

<span data-ttu-id="8a080-239">En el ejemplo siguiente se muestra cómo usar JavaScript para transmitir un archivo a una acción de controlador.</span><span class="sxs-lookup"><span data-stu-id="8a080-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="8a080-240">El token de antifalsificación del archivo se genera por medio de un atributo de filtro personalizado y se pasa a los encabezados HTTP del cliente, en lugar de en el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="8a080-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="8a080-241">Dado que el método de acción procesa los datos cargados directamente, el enlace de modelos del formulario se deshabilita por otro filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="8a080-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="8a080-242">Dentro de la acción, el contenido del formulario se lee usando un `MultipartReader` (que lee cada `MultipartSection` individual), de forma que el archivo se procesa o el contenido se almacena, según corresponda.</span><span class="sxs-lookup"><span data-stu-id="8a080-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="8a080-243">Una vez leídas las secciones de varias partes, la acción realiza su propio enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="8a080-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="8a080-244">La respuesta de página inicial carga el formulario y guarda un token antifalsificación en un cookie (mediante el `GenerateAntiforgeryTokenCookieAttribute` atributo).</span><span class="sxs-lookup"><span data-stu-id="8a080-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="8a080-245">El atributo usa la compatibilidad con la [antifalsificación](xref:security/anti-request-forgery) integrada de ASP.net Core para establecer un cookie con un token de solicitud:</span><span class="sxs-lookup"><span data-stu-id="8a080-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="8a080-246">El `DisableFormValueModelBindingAttribute` se usa para deshabilitar el enlace de modelos:</span><span class="sxs-lookup"><span data-stu-id="8a080-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="8a080-247">En la aplicación de ejemplo, `GenerateAntiforgeryTokenCookieAttribute` y `DisableFormValueModelBindingAttribute` se aplican como filtros a los modelos de aplicación de página de `/StreamedSingleFileUploadDb` y `/StreamedSingleFileUploadPhysical` en `Startup.ConfigureServices` mediante [ Razor las convenciones de páginas](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="8a080-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="8a080-248">Dado que el enlace de modelos no lee el formulario, los parámetros enlazados desde el formulario no se enlazan (la consulta, la ruta y el encabezado siguen funcionando).</span><span class="sxs-lookup"><span data-stu-id="8a080-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="8a080-249">El método de acción funciona directamente con la propiedad `Request`.</span><span class="sxs-lookup"><span data-stu-id="8a080-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="8a080-250">Se usa un elemento `MultipartReader` para leer cada sección.</span><span class="sxs-lookup"><span data-stu-id="8a080-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="8a080-251">Los datos de clave-valor se almacenan en un `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="8a080-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="8a080-252">Una vez leídas las secciones de varias partes, el contenido del `KeyValueAccumulator` se usa para enlazar los datos del formulario a un tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="8a080-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="8a080-253">El método `StreamingController.UploadDatabase` completo para streaming a una base de datos con EF Core:</span><span class="sxs-lookup"><span data-stu-id="8a080-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="8a080-254">`MultipartRequestHelper` ( *Utilities/MultipartRequestHelper.cs* ):</span><span class="sxs-lookup"><span data-stu-id="8a080-254">`MultipartRequestHelper` ( *Utilities/MultipartRequestHelper.cs* ):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="8a080-255">El método `StreamingController.UploadPhysical` completo para streaming a una ubicación física:</span><span class="sxs-lookup"><span data-stu-id="8a080-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="8a080-256">En la aplicación de ejemplo, las comprobaciones de validación las controla `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="8a080-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="8a080-257">Validación</span><span class="sxs-lookup"><span data-stu-id="8a080-257">Validation</span></span>

<span data-ttu-id="8a080-258">La clase `FileHelpers` de la aplicación de ejemplo muestra varias comprobaciones de cargas de archivos de streaming y <xref:Microsoft.AspNetCore.Http.IFormFile> almacenado en búfer.</span><span class="sxs-lookup"><span data-stu-id="8a080-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="8a080-259">Para procesar cargas de archivos almacenadas en búfer de <xref:Microsoft.AspNetCore.Http.IFormFile> en la aplicación de ejemplo, consulte el método `ProcessFormFile` en el archivo *Utilities/FileHelpers.cs* .</span><span class="sxs-lookup"><span data-stu-id="8a080-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="8a080-260">Para procesar archivos de streaming, consulte el método `ProcessStreamedFile` en el mismo archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="8a080-261">Los métodos de procesamiento de validación mostrados en la aplicación de ejemplo no examinan el contenido de los archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="8a080-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="8a080-262">En la mayoría de los escenarios de producción, se usa una API de analizador de virus/malware en el archivo antes de que el archivo esté disponible para los usuarios u otros sistemas.</span><span class="sxs-lookup"><span data-stu-id="8a080-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="8a080-263">Aunque el ejemplo de tema proporciona un ejemplo funcional de técnicas de validación, no implemente la clase `FileHelpers` en una aplicación de producción a menos que:</span><span class="sxs-lookup"><span data-stu-id="8a080-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="8a080-264">Comprenda totalmente la implementación.</span><span class="sxs-lookup"><span data-stu-id="8a080-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="8a080-265">Modifique la implementación según corresponda en función del entorno y las especificaciones de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a080-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="8a080-266">**No implemente nunca de manera indiscriminada el código de seguridad en una aplicación sin abordar estos requisitos.**</span><span class="sxs-lookup"><span data-stu-id="8a080-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="8a080-267">Validación de contenido</span><span class="sxs-lookup"><span data-stu-id="8a080-267">Content validation</span></span>

<span data-ttu-id="8a080-268">**Use una API de detección de virus/malware de terceros en el contenido cargado.**</span><span class="sxs-lookup"><span data-stu-id="8a080-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="8a080-269">El análisis de archivos exige recursos del servidor en escenarios de gran volumen.</span><span class="sxs-lookup"><span data-stu-id="8a080-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="8a080-270">Si disminuye el rendimiento de procesamiento de solicitudes debido al análisis de archivos, considere la posibilidad de descargar el trabajo de análisis a un [servicio en segundo plano](xref:fundamentals/host/hosted-services), posiblemente un servicio que se ejecute en otro servidor desde el servidor de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a080-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="8a080-271">Habitualmente, los archivos cargados se almacenan en un área en cuarentena hasta que el programa de detección de virus en segundo plano los revisa.</span><span class="sxs-lookup"><span data-stu-id="8a080-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="8a080-272">Cuando se pasa un archivo, se mueve a la ubicación de almacenamiento de archivos habitual.</span><span class="sxs-lookup"><span data-stu-id="8a080-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="8a080-273">Por lo general, estos pasos se realizan junto con un registro de base de datos que indica el estado de análisis de un archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="8a080-274">Mediante el uso de este enfoque, la aplicación y el servidor de aplicaciones permanecen centrados en responder a las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="8a080-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="8a080-275">Validación de la extensión del archivo</span><span class="sxs-lookup"><span data-stu-id="8a080-275">File extension validation</span></span>

<span data-ttu-id="8a080-276">La extensión del archivo cargado debe comprobarse con una lista de extensiones permitidas.</span><span class="sxs-lookup"><span data-stu-id="8a080-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="8a080-277">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8a080-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="8a080-278">Validación de firma del archivo</span><span class="sxs-lookup"><span data-stu-id="8a080-278">File signature validation</span></span>

<span data-ttu-id="8a080-279">La firma de un archivo viene determinada por los primeros bytes al principio de un archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="8a080-280">Estos bytes se pueden usar para indicar si la extensión coincide con el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="8a080-281">La aplicación de ejemplo comprueba las firmas de archivo de algunos tipos de archivo comunes.</span><span class="sxs-lookup"><span data-stu-id="8a080-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="8a080-282">En el ejemplo siguiente, la firma de archivo de una imagen JPEG se comprueba con respecto al archivo:</span><span class="sxs-lookup"><span data-stu-id="8a080-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="8a080-283">Para obtener firmas de archivo adicionales, consulte la [base de datos de firmas de archivo](https://www.filesignatures.net/) y las especificaciones de archivo oficiales.</span><span class="sxs-lookup"><span data-stu-id="8a080-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="8a080-284">Seguridad de nombre de archivo</span><span class="sxs-lookup"><span data-stu-id="8a080-284">File name security</span></span>

<span data-ttu-id="8a080-285">Nunca use un nombre de archivo proporcionado por el cliente para guardar un archivo en el almacenamiento físico.</span><span class="sxs-lookup"><span data-stu-id="8a080-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="8a080-286">Cree un nombre de archivo seguro para el archivo con [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) o [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) para crear una ruta de acceso completa (incluido el nombre de archivo) para el almacenamiento temporal.</span><span class="sxs-lookup"><span data-stu-id="8a080-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="8a080-287">Razor HTML codifica automáticamente los valores de propiedad para la presentación.</span><span class="sxs-lookup"><span data-stu-id="8a080-287">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="8a080-288">El código siguiente es seguro de usar:</span><span class="sxs-lookup"><span data-stu-id="8a080-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="8a080-289">Fuera de Razor , <xref:System.Net.WebUtility.HtmlEncode*> el contenido del nombre de archivo siempre procedente de la solicitud de un usuario.</span><span class="sxs-lookup"><span data-stu-id="8a080-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="8a080-290">Muchas implementaciones deben incluir una comprobación de que el archivo existe; de lo contrario, el archivo se sobrescribe por un archivo con el mismo nombre.</span><span class="sxs-lookup"><span data-stu-id="8a080-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="8a080-291">Proporcione lógica adicional para satisfacer las especificaciones de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a080-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="8a080-292">Validación del tamaño</span><span class="sxs-lookup"><span data-stu-id="8a080-292">Size validation</span></span>

<span data-ttu-id="8a080-293">Limite el tamaño de los archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="8a080-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="8a080-294">En la aplicación de ejemplo, el tamaño del archivo está limitado a 2 MB (se indica en bytes).</span><span class="sxs-lookup"><span data-stu-id="8a080-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="8a080-295">El límite se proporciona a través de la [configuración](xref:fundamentals/configuration/index) del *appsettings.json* archivo:</span><span class="sxs-lookup"><span data-stu-id="8a080-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="8a080-296">`FileSizeLimit` se inserta en las clases `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="8a080-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="8a080-297">Cuando el tamaño de un archivo supera el límite, se rechaza el archivo:</span><span class="sxs-lookup"><span data-stu-id="8a080-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="8a080-298">Coincidencia del valor de atributo de nombre con el nombre del parámetro del método POST</span><span class="sxs-lookup"><span data-stu-id="8a080-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="8a080-299">En los que no son Razor formularios y que envían datos de formulario `FormData` directamente, el nombre especificado en el elemento del formulario o `FormData` deben coincidir con el nombre del parámetro en la acción del controlador.</span><span class="sxs-lookup"><span data-stu-id="8a080-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="8a080-300">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a080-300">In the following example:</span></span>

* <span data-ttu-id="8a080-301">Cuando se usa un elemento `<input>`, el atributo `name` se establece en el valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="8a080-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="8a080-302">Cuando se usa `FormData` en JavaScript, el nombre se establece en el valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="8a080-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="8a080-303">Use un nombre coincidente para el parámetro del método de C# (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="8a080-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="8a080-304">Para un Razor método de controlador de página de páginas denominado `Upload` :</span><span class="sxs-lookup"><span data-stu-id="8a080-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="8a080-305">Para un método de acción de controlador POST de MVC:</span><span class="sxs-lookup"><span data-stu-id="8a080-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="8a080-306">Configuración del servidor y de la aplicación</span><span class="sxs-lookup"><span data-stu-id="8a080-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="8a080-307">Límite de longitud del cuerpo de varias partes</span><span class="sxs-lookup"><span data-stu-id="8a080-307">Multipart body length limit</span></span>

<span data-ttu-id="8a080-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> establece el límite de la longitud de cada cuerpo de varias partes.</span><span class="sxs-lookup"><span data-stu-id="8a080-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="8a080-309">Las secciones del formulario que superan este límite inician una <xref:System.IO.InvalidDataException> cuando se analizan.</span><span class="sxs-lookup"><span data-stu-id="8a080-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="8a080-310">El valor predeterminado es 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="8a080-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="8a080-311">Personalice el límite mediante el valor <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="8a080-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="8a080-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> se utiliza para establecer el <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> para una sola página o acción.</span><span class="sxs-lookup"><span data-stu-id="8a080-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="8a080-313">En una Razor aplicación de páginas, aplique el filtro con una [Convención](xref:razor-pages/razor-pages-conventions) en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8a080-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

<span data-ttu-id="8a080-314">En una Razor aplicación de páginas o en una aplicación MVC, aplique el filtro al método de acción o modelo de página:</span><span class="sxs-lookup"><span data-stu-id="8a080-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="8a080-315">Tamaño máximo del cuerpo de la solicitud de Kestrel</span><span class="sxs-lookup"><span data-stu-id="8a080-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="8a080-316">En el caso de las aplicaciones hospedadas por Kestrel, el tamaño máximo predeterminado del cuerpo de solicitud es 30 000 000 bytes, que son aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="8a080-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="8a080-317">Personalice el límite con la opción [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) del servidor de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="8a080-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="8a080-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> se usa para establecer el valor de [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) de una sola página o acción.</span><span class="sxs-lookup"><span data-stu-id="8a080-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="8a080-319">En una Razor aplicación de páginas, aplique el filtro con una [Convención](xref:razor-pages/razor-pages-conventions) en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8a080-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

<span data-ttu-id="8a080-320">En una Razor aplicación pages o MVC, aplique el filtro a la clase de controlador de páginas o al método de acción:</span><span class="sxs-lookup"><span data-stu-id="8a080-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="8a080-321">`RequestSizeLimitAttribute`También se puede aplicar mediante la [`@attribute`](xref:mvc/views/razor#attribute) Razor Directiva:</span><span class="sxs-lookup"><span data-stu-id="8a080-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="8a080-322">Otros límites de Kestrel</span><span class="sxs-lookup"><span data-stu-id="8a080-322">Other Kestrel limits</span></span>

<span data-ttu-id="8a080-323">Otros límites de Kestrel pueden aplicarse a las aplicaciones hospedadas por Kestrel:</span><span class="sxs-lookup"><span data-stu-id="8a080-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="8a080-324">Las conexiones máximas de cliente</span><span class="sxs-lookup"><span data-stu-id="8a080-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="8a080-325">Tarifas de datos de solicitud y respuesta</span><span class="sxs-lookup"><span data-stu-id="8a080-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="8a080-326">IIS</span><span class="sxs-lookup"><span data-stu-id="8a080-326">IIS</span></span>

<span data-ttu-id="8a080-327">El límite de solicitud predeterminado ( `maxAllowedContentLength` ) es de 30 millones bytes, que es aproximadamente de 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="8a080-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="8a080-328">Personalice el límite en el `web.config` archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-328">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="8a080-329">En el ejemplo siguiente, el límite se establece en 50 MB (52.428.800 bytes):</span><span class="sxs-lookup"><span data-stu-id="8a080-329">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="8a080-330">La `maxAllowedContentLength` configuración solo se aplica a IIS.</span><span class="sxs-lookup"><span data-stu-id="8a080-330">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="8a080-331">Para obtener más información, consulte [límites `<requestLimits>` de solicitudes ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="8a080-331">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="8a080-332">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="8a080-332">Troubleshoot</span></span>

<span data-ttu-id="8a080-333">Aquí incluimos algunos problemas comunes que pueden surgir al cargar archivos, así como sus posibles soluciones.</span><span class="sxs-lookup"><span data-stu-id="8a080-333">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="8a080-334">Error No encontrado al implementar en un servidor IIS</span><span class="sxs-lookup"><span data-stu-id="8a080-334">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="8a080-335">El error siguiente indica que el archivo cargado supera la longitud configurada del contenido del servidor:</span><span class="sxs-lookup"><span data-stu-id="8a080-335">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="8a080-336">Para más información, consulte la sección sobre [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="8a080-336">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="8a080-337">Error de conexión</span><span class="sxs-lookup"><span data-stu-id="8a080-337">Connection failure</span></span>

<span data-ttu-id="8a080-338">Un error de conexión y una conexión del servidor de restablecimiento probablemente indica que el archivo cargado supera el tamaño máximo del cuerpo de la solicitud de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a080-338">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="8a080-339">Para más información, consulte la sección [Tamaño máximo del cuerpo de la solicitud de Kestrel](#kestrel-maximum-request-body-size).</span><span class="sxs-lookup"><span data-stu-id="8a080-339">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="8a080-340">Los límites de conexión del cliente de Kestrel también pueden requerir ajustes.</span><span class="sxs-lookup"><span data-stu-id="8a080-340">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="8a080-341">Excepción de referencia nula con IFormFile</span><span class="sxs-lookup"><span data-stu-id="8a080-341">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="8a080-342">Si el controlador acepta archivos cargados con <xref:Microsoft.AspNetCore.Http.IFormFile>, pero el valor es `null`, confirme que el formulario HTML especifica un valor `enctype` de `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="8a080-342">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="8a080-343">Si este atributo no está establecido en el elemento `<form>`, no se llevará a cabo la carga del archivo y cualquier argumento <xref:Microsoft.AspNetCore.Http.IFormFile> enlazado será `null`.</span><span class="sxs-lookup"><span data-stu-id="8a080-343">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="8a080-344">Confirme también que la [nomenclatura de la carga en los datos de formulario coincide con la nomenclatura de la aplicación](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="8a080-344">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="8a080-345">El flujo era demasiado largo</span><span class="sxs-lookup"><span data-stu-id="8a080-345">Stream was too long</span></span>

<span data-ttu-id="8a080-346">Los ejemplos de este tema se basan en <xref:System.IO.MemoryStream> para almacenar el contenido del archivo cargado.</span><span class="sxs-lookup"><span data-stu-id="8a080-346">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="8a080-347">El límite de tamaño de `MemoryStream` es `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="8a080-347">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="8a080-348">Si el escenario de carga de archivos de la aplicación requiere almacenar contenido de archivo superior a 50 MB, use un enfoque alternativo que no dependa de un único valor de `MemoryStream` para almacenar el contenido de un archivo cargado.</span><span class="sxs-lookup"><span data-stu-id="8a080-348">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="8a080-349">ASP.NET Core admite la carga de uno o varios archivos mediante el enlace de modelos almacenado en búfer de archivos más pequeños y el streaming no almacenado en búfer de archivos de mayor tamaño.</span><span class="sxs-lookup"><span data-stu-id="8a080-349">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="8a080-350">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8a080-350">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="8a080-351">Consideraciones sobre la seguridad</span><span class="sxs-lookup"><span data-stu-id="8a080-351">Security considerations</span></span>

<span data-ttu-id="8a080-352">Tenga precaución al proporcionar a los usuarios la capacidad de cargar archivos en un servidor.</span><span class="sxs-lookup"><span data-stu-id="8a080-352">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="8a080-353">Es posible que los atacantes intenten lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a080-353">Attackers may attempt to:</span></span>

* <span data-ttu-id="8a080-354">Ejecutar ataques por [denegación de servicio](/windows-hardware/drivers/ifs/denial-of-service).</span><span class="sxs-lookup"><span data-stu-id="8a080-354">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="8a080-355">Cargar virus o malware.</span><span class="sxs-lookup"><span data-stu-id="8a080-355">Upload viruses or malware.</span></span>
* <span data-ttu-id="8a080-356">Poner en riesgo redes y servidores de otras maneras.</span><span class="sxs-lookup"><span data-stu-id="8a080-356">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="8a080-357">Estos son algunos de los pasos de seguridad con los que se reduce la probabilidad de sufrir ataques:</span><span class="sxs-lookup"><span data-stu-id="8a080-357">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="8a080-358">Cargue los archivos a un área de carga de archivos dedicada, preferiblemente una unidad que no sea de sistema.</span><span class="sxs-lookup"><span data-stu-id="8a080-358">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="8a080-359">Una ubicación dedicada facilita la imposición de restricciones de seguridad en los archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="8a080-359">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="8a080-360">Deshabilite la ejecución de los permisos en la ubicación de carga de archivos.&dagger;</span><span class="sxs-lookup"><span data-stu-id="8a080-360">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="8a080-361">Los archivos cargados **no** se deben persistir en el mismo árbol de directorio que la aplicación.&dagger;</span><span class="sxs-lookup"><span data-stu-id="8a080-361">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="8a080-362">Use un nombre de archivo seguro determinado por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a080-362">Use a safe file name determined by the app.</span></span> <span data-ttu-id="8a080-363">No use un nombre de archivo proporcionado por el usuario o el nombre de archivo que no es de confianza del archivo cargado. &dagger; HTML codifica el nombre de archivo que no es de confianza al mostrarlo.</span><span class="sxs-lookup"><span data-stu-id="8a080-363">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="8a080-364">Por ejemplo, registrando el nombre de archivo o mostrándose en la interfaz de usuario (la Razor salida codifica automáticamente html).</span><span class="sxs-lookup"><span data-stu-id="8a080-364">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="8a080-365">Permita solo las extensiones de archivo aprobadas para la especificación de diseño de la aplicación.&dagger;</span><span class="sxs-lookup"><span data-stu-id="8a080-365">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="8a080-366">Compruebe que las comprobaciones del lado cliente se realizan en el servidor. &dagger; Las comprobaciones del lado cliente son fáciles de eludir.</span><span class="sxs-lookup"><span data-stu-id="8a080-366">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="8a080-367">Compruebe el tamaño de un archivo cargado.</span><span class="sxs-lookup"><span data-stu-id="8a080-367">Check the size of an uploaded file.</span></span> <span data-ttu-id="8a080-368">Establezca un límite de tamaño máximo para evitar cargas grandes.&dagger;</span><span class="sxs-lookup"><span data-stu-id="8a080-368">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="8a080-369">Cuando un archivo cargado con el mismo nombre no deba sobrescribir los archivos, vuelva a comprobar el nombre de archivo en la base de datos o en el almacenamiento físico antes de cargarlo.</span><span class="sxs-lookup"><span data-stu-id="8a080-369">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="8a080-370">**Ejecute un detector de virus o malware en el contenido cargado antes de que se almacene el archivo.**</span><span class="sxs-lookup"><span data-stu-id="8a080-370">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="8a080-371">&dagger;La aplicación de ejemplo muestra un enfoque que cumple los criterios.</span><span class="sxs-lookup"><span data-stu-id="8a080-371">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="8a080-372">La carga de código malintencionado en un sistema suele ser el primer paso para ejecutar código que puede:</span><span class="sxs-lookup"><span data-stu-id="8a080-372">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="8a080-373">Obtener el control completo de un sistema.</span><span class="sxs-lookup"><span data-stu-id="8a080-373">Completely gain control of a system.</span></span>
> * <span data-ttu-id="8a080-374">Sobrecargar un sistema de manera que el sistema se bloquea.</span><span class="sxs-lookup"><span data-stu-id="8a080-374">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="8a080-375">Poner en peligro los datos del usuario o del sistema.</span><span class="sxs-lookup"><span data-stu-id="8a080-375">Compromise user or system data.</span></span>
> * <span data-ttu-id="8a080-376">Aplicar grafitis a una interfaz de usuario pública.</span><span class="sxs-lookup"><span data-stu-id="8a080-376">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="8a080-377">Vea los siguientes recursos para más información sobre cómo reducir el área expuesta de ataques al aceptar archivos de los usuarios:</span><span class="sxs-lookup"><span data-stu-id="8a080-377">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="8a080-378">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carga de archivos sin restricciones)</span><span class="sxs-lookup"><span data-stu-id="8a080-378">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="8a080-379">Azure Security: asegúrese de que los controles adecuados estén en vigor al aceptar archivos de usuarios</span><span class="sxs-lookup"><span data-stu-id="8a080-379">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="8a080-380">Para más información sobre cómo implementar medidas de seguridad, incluidos ejemplos de la aplicación de ejemplo, consulte la sección [Validación](#validation).</span><span class="sxs-lookup"><span data-stu-id="8a080-380">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="8a080-381">Escenarios de almacenamiento</span><span class="sxs-lookup"><span data-stu-id="8a080-381">Storage scenarios</span></span>

<span data-ttu-id="8a080-382">Las opciones de almacenamiento comunes para los archivos incluyen:</span><span class="sxs-lookup"><span data-stu-id="8a080-382">Common storage options for files include:</span></span>

* <span data-ttu-id="8a080-383">Base de datos</span><span class="sxs-lookup"><span data-stu-id="8a080-383">Database</span></span>

  * <span data-ttu-id="8a080-384">En el caso de cargas de archivos pequeñas, una base de datos suele ser más rápida que las opciones de almacenamiento físico (sistema de archivos o recurso compartido de red).</span><span class="sxs-lookup"><span data-stu-id="8a080-384">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="8a080-385">Una base de datos suele ser más conveniente que las opciones de almacenamiento físico, ya que la recuperación de un registro de base de datos para los datos de usuario puede proporcionar el contenido del archivo (por ejemplo, una imagen de avatar).</span><span class="sxs-lookup"><span data-stu-id="8a080-385">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="8a080-386">Una base de datos puede ser más económica que usar un servicio de almacenamiento de datos.</span><span class="sxs-lookup"><span data-stu-id="8a080-386">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="8a080-387">Almacenamiento físico (sistema de archivos o recurso compartido de red)</span><span class="sxs-lookup"><span data-stu-id="8a080-387">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="8a080-388">Para cargas de archivos de gran tamaño:</span><span class="sxs-lookup"><span data-stu-id="8a080-388">For large file uploads:</span></span>
    * <span data-ttu-id="8a080-389">Los límites de base de datos pueden restringir el tamaño de la carga.</span><span class="sxs-lookup"><span data-stu-id="8a080-389">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="8a080-390">El almacenamiento físico suele ser menos económico que el almacenamiento en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="8a080-390">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="8a080-391">El almacenamiento físico puede ser más económico que usar un servicio de almacenamiento de datos.</span><span class="sxs-lookup"><span data-stu-id="8a080-391">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="8a080-392">El proceso de la aplicación debe tener permisos de lectura y escritura en la ubicación de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="8a080-392">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="8a080-393">**Nunca conceda el permiso de ejecución.**</span><span class="sxs-lookup"><span data-stu-id="8a080-393">**Never grant execute permission.**</span></span>

* <span data-ttu-id="8a080-394">Servicio de almacenamiento de datos (por ejemplo, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="8a080-394">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="8a080-395">Los servicios suelen ofrecer una escalabilidad y resistencia mejoradas sobre las soluciones locales que normalmente están sujetas a únicos puntos de error.</span><span class="sxs-lookup"><span data-stu-id="8a080-395">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="8a080-396">Los servicios pueden tener un costo menor en escenarios de infraestructura de almacenamiento de gran tamaño.</span><span class="sxs-lookup"><span data-stu-id="8a080-396">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="8a080-397">Para obtener más información, vea [Inicio rápido: usar .net para crear un BLOB en el almacenamiento de objetos](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="8a080-397">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="8a080-398">Escenarios de carga de archivos</span><span class="sxs-lookup"><span data-stu-id="8a080-398">File upload scenarios</span></span>

<span data-ttu-id="8a080-399">Dos enfoques generales para cargar archivos son el almacenamiento en búfer y el streaming.</span><span class="sxs-lookup"><span data-stu-id="8a080-399">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="8a080-400">**de respuesta**</span><span class="sxs-lookup"><span data-stu-id="8a080-400">**Buffering**</span></span>

<span data-ttu-id="8a080-401">El archivo completo se lee en un <xref:Microsoft.AspNetCore.Http.IFormFile>, que es una representación de C# del archivo que se usa para procesar o guardar el archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-401">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="8a080-402">Los recursos (disco, memoria) que se usan en las cargas de archivos dependen de la cantidad y del tamaño de las cargas de archivos que se realizan simultáneamente.</span><span class="sxs-lookup"><span data-stu-id="8a080-402">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="8a080-403">Si una aplicación intenta almacenar demasiadas cargas en el búfer, el sitio se bloquea cuando se queda sin memoria o sin espacio en disco.</span><span class="sxs-lookup"><span data-stu-id="8a080-403">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="8a080-404">Si el tamaño o la frecuencia de las cargas de archivos está agotando los recursos de la aplicación, use el streaming.</span><span class="sxs-lookup"><span data-stu-id="8a080-404">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="8a080-405">Cualquier archivo almacenado en búfer único que supere los 64 KB se mueve de la memoria a un archivo temporal en el disco.</span><span class="sxs-lookup"><span data-stu-id="8a080-405">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="8a080-406">En las secciones siguientes de este tema se habla del almacenamiento en búfer de archivos pequeños:</span><span class="sxs-lookup"><span data-stu-id="8a080-406">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="8a080-407">Almacenamiento físico</span><span class="sxs-lookup"><span data-stu-id="8a080-407">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="8a080-408">Base de datos</span><span class="sxs-lookup"><span data-stu-id="8a080-408">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="8a080-409">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="8a080-409">**Streaming**</span></span>

<span data-ttu-id="8a080-410">El archivo se recibe de una solicitud de varias partes y lo procesa o guarda directamente la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a080-410">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="8a080-411">El streaming no mejora considerablemente el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="8a080-411">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="8a080-412">El streaming reduce las demandas de memoria o espacio en disco cuando se cargan archivos.</span><span class="sxs-lookup"><span data-stu-id="8a080-412">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="8a080-413">El streaming de archivos grandes se describe en la sección [Carga de archivos de gran tamaño con streaming](#upload-large-files-with-streaming).</span><span class="sxs-lookup"><span data-stu-id="8a080-413">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="8a080-414">Carga de archivos pequeños con enlace de modelos almacenado en búfer al almacenamiento físico</span><span class="sxs-lookup"><span data-stu-id="8a080-414">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="8a080-415">Para cargar archivos pequeños, se puede usar un formulario de varias partes o construir una solicitud POST con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8a080-415">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="8a080-416">En el ejemplo siguiente se muestra el uso de un Razor formulario de páginas para cargar un único archivo ( *pages/BufferedSingleFileUploadPhysical. cshtml* en la aplicación de ejemplo):</span><span class="sxs-lookup"><span data-stu-id="8a080-416">The following example demonstrates the use of a Razor Pages form to upload a single file ( *Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="8a080-417">El ejemplo siguiente es análogo al ejemplo anterior, salvo en que:</span><span class="sxs-lookup"><span data-stu-id="8a080-417">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="8a080-418">([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) de JavaScript se usa para enviar los datos del formulario.</span><span class="sxs-lookup"><span data-stu-id="8a080-418">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="8a080-419">No hay ninguna validación.</span><span class="sxs-lookup"><span data-stu-id="8a080-419">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="8a080-420">Para realizar la solicitud POST en JavaScript para los clientes que [no admiten Fetch API](https://caniuse.com/#feat=fetch), use uno de estos enfoques:</span><span class="sxs-lookup"><span data-stu-id="8a080-420">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="8a080-421">Use un Fetch Polyfill (por ejemplo, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="8a080-421">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="8a080-422">Use `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="8a080-422">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="8a080-423">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8a080-423">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="8a080-424">Para admitir las cargas de archivos, los formularios HTML deben especificar un tipo de codificación (`enctype`) de `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="8a080-424">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="8a080-425">Para que un elemento de entrada `files` admita al carga de varios archivos, proporcione el atributo `multiple` en el elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="8a080-425">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="8a080-426">Es posible acceder a archivos individuales cargados en el servidor a través del [enlace de modelos](xref:mvc/models/model-binding) mediante <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="8a080-426">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="8a080-427">La aplicación de ejemplo muestra varias cargas de archivos almacenados en búfer para escenarios de almacenamiento físico y base de datos.</span><span class="sxs-lookup"><span data-stu-id="8a080-427">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="8a080-428">**No** utilice la propiedad `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> para usos distintos a la presentación y el registro.</span><span class="sxs-lookup"><span data-stu-id="8a080-428">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="8a080-429">Para fines de presentación y registro, codifique el nombre de archivo en HTML.</span><span class="sxs-lookup"><span data-stu-id="8a080-429">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="8a080-430">Un atacante puede proporcionar un nombre de archivo malintencionado, incluidas rutas de acceso completas o relativas.</span><span class="sxs-lookup"><span data-stu-id="8a080-430">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="8a080-431">Las aplicaciones deben:</span><span class="sxs-lookup"><span data-stu-id="8a080-431">Applications should:</span></span>
>
> * <span data-ttu-id="8a080-432">Quitar la ruta de acceso del nombre de archivo proporcionado por el usuario.</span><span class="sxs-lookup"><span data-stu-id="8a080-432">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="8a080-433">Guardar el nombre de archivo codificado en HTML, sin la ruta de acceso para la interfaz de usuario o el registro.</span><span class="sxs-lookup"><span data-stu-id="8a080-433">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="8a080-434">Generar un nombre de archivo aleatorio nuevo para el almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="8a080-434">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="8a080-435">En el código siguiente se quita la ruta de acceso del nombre del archivo:</span><span class="sxs-lookup"><span data-stu-id="8a080-435">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="8a080-436">Los ejemplos proporcionados hasta ahora no tienen en cuenta las consideraciones de seguridad.</span><span class="sxs-lookup"><span data-stu-id="8a080-436">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="8a080-437">Se proporciona información adicional en las secciones siguientes y en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="8a080-437">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="8a080-438">Consideraciones de seguridad</span><span class="sxs-lookup"><span data-stu-id="8a080-438">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="8a080-439">Validación</span><span class="sxs-lookup"><span data-stu-id="8a080-439">Validation</span></span>](#validation)

<span data-ttu-id="8a080-440">Al cargar archivos mediante el enlace de modelos y <xref:Microsoft.AspNetCore.Http.IFormFile>, el método de acción puede aceptar:</span><span class="sxs-lookup"><span data-stu-id="8a080-440">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="8a080-441">Un <xref:Microsoft.AspNetCore.Http.IFormFile> único.</span><span class="sxs-lookup"><span data-stu-id="8a080-441">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="8a080-442">Cualquiera de las colecciones siguientes que representan varios archivos:</span><span class="sxs-lookup"><span data-stu-id="8a080-442">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="8a080-443">[Lista](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="8a080-443">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="8a080-444">El enlace coincide con los archivos de formulario por nombre.</span><span class="sxs-lookup"><span data-stu-id="8a080-444">Binding matches form files by name.</span></span> <span data-ttu-id="8a080-445">Por ejemplo, el valor `name` HTML en `<input type="file" name="formFile">` debe coincidir con la propiedad/el parámetro enlazado de C# (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="8a080-445">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="8a080-446">Para más información, consulte la sección [Coincidencia del valor de atributo de nombre con el nombre del parámetro del método POST](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="8a080-446">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="8a080-447">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a080-447">The following example:</span></span>

* <span data-ttu-id="8a080-448">Recorre en bucle uno o más archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="8a080-448">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="8a080-449">Usa [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) para devolver una ruta de acceso completa de un archivo, incluido el nombre de archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-449">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="8a080-450">Guarda los archivos en el sistema de archivos local con un nombre de archivo generado por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a080-450">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="8a080-451">Devuelve el número total y el tamaño de los archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="8a080-451">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="8a080-452">Use `Path.GetRandomFileName` para generar un nombre de archivo sin una ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="8a080-452">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="8a080-453">En el ejemplo siguiente, la ruta de acceso se obtiene de la configuración:</span><span class="sxs-lookup"><span data-stu-id="8a080-453">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="8a080-454">La ruta de acceso pasada a <xref:System.IO.FileStream> *debe* incluir el nombre de archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-454">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="8a080-455">Si no se proporciona el nombre de archivo, se produce una <xref:System.UnauthorizedAccessException> en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="8a080-455">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="8a080-456">Los archivos que se cargan usando la técnica <xref:Microsoft.AspNetCore.Http.IFormFile> se almacenan en búfer en memoria o en disco en el servidor web antes de procesarse.</span><span class="sxs-lookup"><span data-stu-id="8a080-456">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="8a080-457">Dentro del método de acción, se puede tener acceso al contenido de <xref:Microsoft.AspNetCore.Http.IFormFile> como <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="8a080-457">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="8a080-458">Además del sistema de archivos local, los archivos se pueden guardar en un recurso compartido de red o en un servicio de almacenamiento de archivos, como [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="8a080-458">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="8a080-459">Para ver otro ejemplo que recorre en bucle varios archivos para cargar y usa nombres de archivo seguros, consulte *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="8a080-459">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="8a080-460">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) arroja una <xref:System.IO.IOException> si se crean más de 65 535 archivos sin eliminar los archivos temporales anteriores.</span><span class="sxs-lookup"><span data-stu-id="8a080-460">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="8a080-461">El límite de 65 535 archivos es un límite por servidor.</span><span class="sxs-lookup"><span data-stu-id="8a080-461">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="8a080-462">Para más información sobre este límite en el sistema operativo Windows, consulte las notas en los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="8a080-462">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="8a080-463">Función GetTempFileNameA</span><span class="sxs-lookup"><span data-stu-id="8a080-463">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="8a080-464">Carga de archivos pequeños con enlace de modelos almacenado en búfer a una base de datos</span><span class="sxs-lookup"><span data-stu-id="8a080-464">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="8a080-465">Para almacenar datos de archivo binario en una base de datos con [Entity Framework](/ef/core/index), defina una propiedad de matriz <xref:System.Byte> en la entidad:</span><span class="sxs-lookup"><span data-stu-id="8a080-465">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="8a080-466">Especifique una propiedad de modelo de página para la clase que incluya un <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="8a080-466">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="8a080-467"><xref:Microsoft.AspNetCore.Http.IFormFile> se puede usar directamente como un parámetro de método de acción o como una propiedad de modelo enlazado.</span><span class="sxs-lookup"><span data-stu-id="8a080-467"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="8a080-468">En el ejemplo anterior se utiliza una propiedad de modelo enlazado.</span><span class="sxs-lookup"><span data-stu-id="8a080-468">The prior example uses a bound model property.</span></span>

<span data-ttu-id="8a080-469">`FileUpload`Se utiliza en el Razor formulario de páginas:</span><span class="sxs-lookup"><span data-stu-id="8a080-469">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="8a080-470">Cuando el formulario se publique en el servidor, copie el <xref:Microsoft.AspNetCore.Http.IFormFile> en un flujo y guárdelo como matriz de bytes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8a080-470">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="8a080-471">En el ejemplo siguiente, `_dbContext` almacena el contexto de base de datos de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="8a080-471">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="8a080-472">El ejemplo anterior es similar a un escenario que se muestra en la aplicación de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8a080-472">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="8a080-473">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8a080-473">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="8a080-474">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="8a080-474">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="8a080-475">Tenga cuidado al almacenar los datos binarios en bases de datos relacionales, ya que esto puede repercutir adversamente en el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="8a080-475">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="8a080-476">No se base ni confíe en la propiedad `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> sin validarla.</span><span class="sxs-lookup"><span data-stu-id="8a080-476">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="8a080-477">La propiedad `FileName` solo debe usarse para fines de presentación y solo después de la codificación HTML.</span><span class="sxs-lookup"><span data-stu-id="8a080-477">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="8a080-478">Los ejemplos proporcionados no tienen en cuenta las consideraciones de seguridad.</span><span class="sxs-lookup"><span data-stu-id="8a080-478">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="8a080-479">Se proporciona información adicional en las secciones siguientes y en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="8a080-479">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="8a080-480">Consideraciones de seguridad</span><span class="sxs-lookup"><span data-stu-id="8a080-480">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="8a080-481">Validación</span><span class="sxs-lookup"><span data-stu-id="8a080-481">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="8a080-482">Carga de archivos de gran tamaño con streaming</span><span class="sxs-lookup"><span data-stu-id="8a080-482">Upload large files with streaming</span></span>

<span data-ttu-id="8a080-483">En el ejemplo siguiente se muestra cómo usar JavaScript para transmitir un archivo a una acción de controlador.</span><span class="sxs-lookup"><span data-stu-id="8a080-483">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="8a080-484">El token de antifalsificación del archivo se genera por medio de un atributo de filtro personalizado y se pasa a los encabezados HTTP del cliente, en lugar de en el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="8a080-484">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="8a080-485">Dado que el método de acción procesa los datos cargados directamente, el enlace de modelos del formulario se deshabilita por otro filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="8a080-485">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="8a080-486">Dentro de la acción, el contenido del formulario se lee usando un `MultipartReader` (que lee cada `MultipartSection` individual), de forma que el archivo se procesa o el contenido se almacena, según corresponda.</span><span class="sxs-lookup"><span data-stu-id="8a080-486">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="8a080-487">Una vez leídas las secciones de varias partes, la acción realiza su propio enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="8a080-487">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="8a080-488">La respuesta de página inicial carga el formulario y guarda un token antifalsificación en un cookie (mediante el `GenerateAntiforgeryTokenCookieAttribute` atributo).</span><span class="sxs-lookup"><span data-stu-id="8a080-488">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="8a080-489">El atributo usa la compatibilidad con la [antifalsificación](xref:security/anti-request-forgery) integrada de ASP.net Core para establecer un cookie con un token de solicitud:</span><span class="sxs-lookup"><span data-stu-id="8a080-489">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="8a080-490">El `DisableFormValueModelBindingAttribute` se usa para deshabilitar el enlace de modelos:</span><span class="sxs-lookup"><span data-stu-id="8a080-490">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="8a080-491">En la aplicación de ejemplo, `GenerateAntiforgeryTokenCookieAttribute` y `DisableFormValueModelBindingAttribute` se aplican como filtros a los modelos de aplicación de página de `/StreamedSingleFileUploadDb` y `/StreamedSingleFileUploadPhysical` en `Startup.ConfigureServices` mediante [ Razor las convenciones de páginas](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="8a080-491">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="8a080-492">Dado que el enlace de modelos no lee el formulario, los parámetros enlazados desde el formulario no se enlazan (la consulta, la ruta y el encabezado siguen funcionando).</span><span class="sxs-lookup"><span data-stu-id="8a080-492">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="8a080-493">El método de acción funciona directamente con la propiedad `Request`.</span><span class="sxs-lookup"><span data-stu-id="8a080-493">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="8a080-494">Se usa un elemento `MultipartReader` para leer cada sección.</span><span class="sxs-lookup"><span data-stu-id="8a080-494">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="8a080-495">Los datos de clave-valor se almacenan en un `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="8a080-495">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="8a080-496">Una vez leídas las secciones de varias partes, el contenido del `KeyValueAccumulator` se usa para enlazar los datos del formulario a un tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="8a080-496">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="8a080-497">El método `StreamingController.UploadDatabase` completo para streaming a una base de datos con EF Core:</span><span class="sxs-lookup"><span data-stu-id="8a080-497">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="8a080-498">`MultipartRequestHelper` ( *Utilities/MultipartRequestHelper.cs* ):</span><span class="sxs-lookup"><span data-stu-id="8a080-498">`MultipartRequestHelper` ( *Utilities/MultipartRequestHelper.cs* ):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="8a080-499">El método `StreamingController.UploadPhysical` completo para streaming a una ubicación física:</span><span class="sxs-lookup"><span data-stu-id="8a080-499">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="8a080-500">En la aplicación de ejemplo, las comprobaciones de validación las controla `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="8a080-500">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="8a080-501">Validación</span><span class="sxs-lookup"><span data-stu-id="8a080-501">Validation</span></span>

<span data-ttu-id="8a080-502">La clase `FileHelpers` de la aplicación de ejemplo muestra varias comprobaciones de cargas de archivos de streaming y <xref:Microsoft.AspNetCore.Http.IFormFile> almacenado en búfer.</span><span class="sxs-lookup"><span data-stu-id="8a080-502">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="8a080-503">Para procesar cargas de archivos almacenadas en búfer de <xref:Microsoft.AspNetCore.Http.IFormFile> en la aplicación de ejemplo, consulte el método `ProcessFormFile` en el archivo *Utilities/FileHelpers.cs* .</span><span class="sxs-lookup"><span data-stu-id="8a080-503">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="8a080-504">Para procesar archivos de streaming, consulte el método `ProcessStreamedFile` en el mismo archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-504">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="8a080-505">Los métodos de procesamiento de validación mostrados en la aplicación de ejemplo no examinan el contenido de los archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="8a080-505">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="8a080-506">En la mayoría de los escenarios de producción, se usa una API de analizador de virus/malware en el archivo antes de que el archivo esté disponible para los usuarios u otros sistemas.</span><span class="sxs-lookup"><span data-stu-id="8a080-506">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="8a080-507">Aunque el ejemplo de tema proporciona un ejemplo funcional de técnicas de validación, no implemente la clase `FileHelpers` en una aplicación de producción a menos que:</span><span class="sxs-lookup"><span data-stu-id="8a080-507">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="8a080-508">Comprenda totalmente la implementación.</span><span class="sxs-lookup"><span data-stu-id="8a080-508">Fully understand the implementation.</span></span>
> * <span data-ttu-id="8a080-509">Modifique la implementación según corresponda en función del entorno y las especificaciones de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a080-509">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="8a080-510">**No implemente nunca de manera indiscriminada el código de seguridad en una aplicación sin abordar estos requisitos.**</span><span class="sxs-lookup"><span data-stu-id="8a080-510">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="8a080-511">Validación de contenido</span><span class="sxs-lookup"><span data-stu-id="8a080-511">Content validation</span></span>

<span data-ttu-id="8a080-512">**Use una API de detección de virus/malware de terceros en el contenido cargado.**</span><span class="sxs-lookup"><span data-stu-id="8a080-512">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="8a080-513">El análisis de archivos exige recursos del servidor en escenarios de gran volumen.</span><span class="sxs-lookup"><span data-stu-id="8a080-513">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="8a080-514">Si disminuye el rendimiento de procesamiento de solicitudes debido al análisis de archivos, considere la posibilidad de descargar el trabajo de análisis a un [servicio en segundo plano](xref:fundamentals/host/hosted-services), posiblemente un servicio que se ejecute en otro servidor desde el servidor de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a080-514">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="8a080-515">Habitualmente, los archivos cargados se almacenan en un área en cuarentena hasta que el programa de detección de virus en segundo plano los revisa.</span><span class="sxs-lookup"><span data-stu-id="8a080-515">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="8a080-516">Cuando se pasa un archivo, se mueve a la ubicación de almacenamiento de archivos habitual.</span><span class="sxs-lookup"><span data-stu-id="8a080-516">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="8a080-517">Por lo general, estos pasos se realizan junto con un registro de base de datos que indica el estado de análisis de un archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-517">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="8a080-518">Mediante el uso de este enfoque, la aplicación y el servidor de aplicaciones permanecen centrados en responder a las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="8a080-518">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="8a080-519">Validación de la extensión del archivo</span><span class="sxs-lookup"><span data-stu-id="8a080-519">File extension validation</span></span>

<span data-ttu-id="8a080-520">La extensión del archivo cargado debe comprobarse con una lista de extensiones permitidas.</span><span class="sxs-lookup"><span data-stu-id="8a080-520">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="8a080-521">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8a080-521">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="8a080-522">Validación de firma del archivo</span><span class="sxs-lookup"><span data-stu-id="8a080-522">File signature validation</span></span>

<span data-ttu-id="8a080-523">La firma de un archivo viene determinada por los primeros bytes al principio de un archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-523">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="8a080-524">Estos bytes se pueden usar para indicar si la extensión coincide con el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-524">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="8a080-525">La aplicación de ejemplo comprueba las firmas de archivo de algunos tipos de archivo comunes.</span><span class="sxs-lookup"><span data-stu-id="8a080-525">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="8a080-526">En el ejemplo siguiente, la firma de archivo de una imagen JPEG se comprueba con respecto al archivo:</span><span class="sxs-lookup"><span data-stu-id="8a080-526">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="8a080-527">Para obtener firmas de archivo adicionales, consulte la [base de datos de firmas de archivo](https://www.filesignatures.net/) y las especificaciones de archivo oficiales.</span><span class="sxs-lookup"><span data-stu-id="8a080-527">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="8a080-528">Seguridad de nombre de archivo</span><span class="sxs-lookup"><span data-stu-id="8a080-528">File name security</span></span>

<span data-ttu-id="8a080-529">Nunca use un nombre de archivo proporcionado por el cliente para guardar un archivo en el almacenamiento físico.</span><span class="sxs-lookup"><span data-stu-id="8a080-529">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="8a080-530">Cree un nombre de archivo seguro para el archivo con [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) o [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) para crear una ruta de acceso completa (incluido el nombre de archivo) para el almacenamiento temporal.</span><span class="sxs-lookup"><span data-stu-id="8a080-530">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="8a080-531">Razor HTML codifica automáticamente los valores de propiedad para la presentación.</span><span class="sxs-lookup"><span data-stu-id="8a080-531">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="8a080-532">El código siguiente es seguro de usar:</span><span class="sxs-lookup"><span data-stu-id="8a080-532">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="8a080-533">Fuera de Razor , <xref:System.Net.WebUtility.HtmlEncode*> el contenido del nombre de archivo siempre procedente de la solicitud de un usuario.</span><span class="sxs-lookup"><span data-stu-id="8a080-533">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="8a080-534">Muchas implementaciones deben incluir una comprobación de que el archivo existe; de lo contrario, el archivo se sobrescribe por un archivo con el mismo nombre.</span><span class="sxs-lookup"><span data-stu-id="8a080-534">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="8a080-535">Proporcione lógica adicional para satisfacer las especificaciones de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a080-535">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="8a080-536">Validación del tamaño</span><span class="sxs-lookup"><span data-stu-id="8a080-536">Size validation</span></span>

<span data-ttu-id="8a080-537">Limite el tamaño de los archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="8a080-537">Limit the size of uploaded files.</span></span>

<span data-ttu-id="8a080-538">En la aplicación de ejemplo, el tamaño del archivo está limitado a 2 MB (se indica en bytes).</span><span class="sxs-lookup"><span data-stu-id="8a080-538">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="8a080-539">El límite se proporciona a través de la [configuración](xref:fundamentals/configuration/index) del *appsettings.json* archivo:</span><span class="sxs-lookup"><span data-stu-id="8a080-539">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="8a080-540">`FileSizeLimit` se inserta en las clases `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="8a080-540">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="8a080-541">Cuando el tamaño de un archivo supera el límite, se rechaza el archivo:</span><span class="sxs-lookup"><span data-stu-id="8a080-541">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="8a080-542">Coincidencia del valor de atributo de nombre con el nombre del parámetro del método POST</span><span class="sxs-lookup"><span data-stu-id="8a080-542">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="8a080-543">En los que no son Razor formularios y que envían datos de formulario `FormData` directamente, el nombre especificado en el elemento del formulario o `FormData` deben coincidir con el nombre del parámetro en la acción del controlador.</span><span class="sxs-lookup"><span data-stu-id="8a080-543">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="8a080-544">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a080-544">In the following example:</span></span>

* <span data-ttu-id="8a080-545">Cuando se usa un elemento `<input>`, el atributo `name` se establece en el valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="8a080-545">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="8a080-546">Cuando se usa `FormData` en JavaScript, el nombre se establece en el valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="8a080-546">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="8a080-547">Use un nombre coincidente para el parámetro del método de C# (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="8a080-547">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="8a080-548">Para un Razor método de controlador de página de páginas denominado `Upload` :</span><span class="sxs-lookup"><span data-stu-id="8a080-548">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="8a080-549">Para un método de acción de controlador POST de MVC:</span><span class="sxs-lookup"><span data-stu-id="8a080-549">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="8a080-550">Configuración del servidor y de la aplicación</span><span class="sxs-lookup"><span data-stu-id="8a080-550">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="8a080-551">Límite de longitud del cuerpo de varias partes</span><span class="sxs-lookup"><span data-stu-id="8a080-551">Multipart body length limit</span></span>

<span data-ttu-id="8a080-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> establece el límite de la longitud de cada cuerpo de varias partes.</span><span class="sxs-lookup"><span data-stu-id="8a080-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="8a080-553">Las secciones del formulario que superan este límite inician una <xref:System.IO.InvalidDataException> cuando se analizan.</span><span class="sxs-lookup"><span data-stu-id="8a080-553">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="8a080-554">El valor predeterminado es 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="8a080-554">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="8a080-555">Personalice el límite mediante el valor <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="8a080-555">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="8a080-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> se utiliza para establecer el <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> para una sola página o acción.</span><span class="sxs-lookup"><span data-stu-id="8a080-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="8a080-557">En una Razor aplicación de páginas, aplique el filtro con una [Convención](xref:razor-pages/razor-pages-conventions) en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8a080-557">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

<span data-ttu-id="8a080-558">En una Razor aplicación de páginas o en una aplicación MVC, aplique el filtro al método de acción o modelo de página:</span><span class="sxs-lookup"><span data-stu-id="8a080-558">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="8a080-559">Tamaño máximo del cuerpo de la solicitud de Kestrel</span><span class="sxs-lookup"><span data-stu-id="8a080-559">Kestrel maximum request body size</span></span>

<span data-ttu-id="8a080-560">En el caso de las aplicaciones hospedadas por Kestrel, el tamaño máximo predeterminado del cuerpo de solicitud es 30 000 000 bytes, que son aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="8a080-560">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="8a080-561">Personalice el límite con la opción [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) del servidor de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="8a080-561">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="8a080-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> se usa para establecer el valor de [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) de una sola página o acción.</span><span class="sxs-lookup"><span data-stu-id="8a080-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="8a080-563">En una Razor aplicación de páginas, aplique el filtro con una [Convención](xref:razor-pages/razor-pages-conventions) en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8a080-563">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

<span data-ttu-id="8a080-564">En una Razor aplicación pages o MVC, aplique el filtro a la clase de controlador de páginas o al método de acción:</span><span class="sxs-lookup"><span data-stu-id="8a080-564">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="8a080-565">`RequestSizeLimitAttribute`También se puede aplicar mediante la [`@attribute`](xref:mvc/views/razor#attribute) Razor Directiva:</span><span class="sxs-lookup"><span data-stu-id="8a080-565">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="8a080-566">Otros límites de Kestrel</span><span class="sxs-lookup"><span data-stu-id="8a080-566">Other Kestrel limits</span></span>

<span data-ttu-id="8a080-567">Otros límites de Kestrel pueden aplicarse a las aplicaciones hospedadas por Kestrel:</span><span class="sxs-lookup"><span data-stu-id="8a080-567">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="8a080-568">Las conexiones máximas de cliente</span><span class="sxs-lookup"><span data-stu-id="8a080-568">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="8a080-569">Tarifas de datos de solicitud y respuesta</span><span class="sxs-lookup"><span data-stu-id="8a080-569">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="8a080-570">IIS</span><span class="sxs-lookup"><span data-stu-id="8a080-570">IIS</span></span>

<span data-ttu-id="8a080-571">El límite de solicitud predeterminado ( `maxAllowedContentLength` ) es de 30 millones bytes, que es aproximadamente de 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="8a080-571">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="8a080-572">Personalice el límite en el `web.config` archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-572">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="8a080-573">En el ejemplo siguiente, el límite se establece en 50 MB (52.428.800 bytes):</span><span class="sxs-lookup"><span data-stu-id="8a080-573">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="8a080-574">La `maxAllowedContentLength` configuración solo se aplica a IIS.</span><span class="sxs-lookup"><span data-stu-id="8a080-574">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="8a080-575">Para obtener más información, consulte [límites `<requestLimits>` de solicitudes ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="8a080-575">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="8a080-576">Aumente el tamaño máximo del cuerpo de solicitud para la solicitud HTTP estableciendo <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8a080-576">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8a080-577">En el ejemplo siguiente, el límite se establece en 50 MB (52.428.800 bytes):</span><span class="sxs-lookup"><span data-stu-id="8a080-577">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="8a080-578">Para obtener más información, vea <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="8a080-578">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="8a080-579">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="8a080-579">Troubleshoot</span></span>

<span data-ttu-id="8a080-580">Aquí incluimos algunos problemas comunes que pueden surgir al cargar archivos, así como sus posibles soluciones.</span><span class="sxs-lookup"><span data-stu-id="8a080-580">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="8a080-581">Error No encontrado al implementar en un servidor IIS</span><span class="sxs-lookup"><span data-stu-id="8a080-581">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="8a080-582">El error siguiente indica que el archivo cargado supera la longitud configurada del contenido del servidor:</span><span class="sxs-lookup"><span data-stu-id="8a080-582">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="8a080-583">Para más información, consulte la sección sobre [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="8a080-583">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="8a080-584">Error de conexión</span><span class="sxs-lookup"><span data-stu-id="8a080-584">Connection failure</span></span>

<span data-ttu-id="8a080-585">Un error de conexión y una conexión del servidor de restablecimiento probablemente indica que el archivo cargado supera el tamaño máximo del cuerpo de la solicitud de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a080-585">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="8a080-586">Para más información, consulte la sección [Tamaño máximo del cuerpo de la solicitud de Kestrel](#kestrel-maximum-request-body-size).</span><span class="sxs-lookup"><span data-stu-id="8a080-586">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="8a080-587">Los límites de conexión del cliente de Kestrel también pueden requerir ajustes.</span><span class="sxs-lookup"><span data-stu-id="8a080-587">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="8a080-588">Excepción de referencia nula con IFormFile</span><span class="sxs-lookup"><span data-stu-id="8a080-588">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="8a080-589">Si el controlador acepta archivos cargados con <xref:Microsoft.AspNetCore.Http.IFormFile>, pero el valor es `null`, confirme que el formulario HTML especifica un valor `enctype` de `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="8a080-589">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="8a080-590">Si este atributo no está establecido en el elemento `<form>`, no se llevará a cabo la carga del archivo y cualquier argumento <xref:Microsoft.AspNetCore.Http.IFormFile> enlazado será `null`.</span><span class="sxs-lookup"><span data-stu-id="8a080-590">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="8a080-591">Confirme también que la [nomenclatura de la carga en los datos de formulario coincide con la nomenclatura de la aplicación](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="8a080-591">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="8a080-592">El flujo era demasiado largo</span><span class="sxs-lookup"><span data-stu-id="8a080-592">Stream was too long</span></span>

<span data-ttu-id="8a080-593">Los ejemplos de este tema se basan en <xref:System.IO.MemoryStream> para almacenar el contenido del archivo cargado.</span><span class="sxs-lookup"><span data-stu-id="8a080-593">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="8a080-594">El límite de tamaño de `MemoryStream` es `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="8a080-594">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="8a080-595">Si el escenario de carga de archivos de la aplicación requiere almacenar contenido de archivo superior a 50 MB, use un enfoque alternativo que no dependa de un único valor de `MemoryStream` para almacenar el contenido de un archivo cargado.</span><span class="sxs-lookup"><span data-stu-id="8a080-595">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8a080-596">ASP.NET Core admite la carga de uno o varios archivos mediante el enlace de modelos almacenado en búfer de archivos más pequeños y el streaming no almacenado en búfer de archivos de mayor tamaño.</span><span class="sxs-lookup"><span data-stu-id="8a080-596">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="8a080-597">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8a080-597">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="8a080-598">Consideraciones sobre la seguridad</span><span class="sxs-lookup"><span data-stu-id="8a080-598">Security considerations</span></span>

<span data-ttu-id="8a080-599">Tenga precaución al proporcionar a los usuarios la capacidad de cargar archivos en un servidor.</span><span class="sxs-lookup"><span data-stu-id="8a080-599">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="8a080-600">Es posible que los atacantes intenten lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a080-600">Attackers may attempt to:</span></span>

* <span data-ttu-id="8a080-601">Ejecutar ataques por [denegación de servicio](/windows-hardware/drivers/ifs/denial-of-service).</span><span class="sxs-lookup"><span data-stu-id="8a080-601">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="8a080-602">Cargar virus o malware.</span><span class="sxs-lookup"><span data-stu-id="8a080-602">Upload viruses or malware.</span></span>
* <span data-ttu-id="8a080-603">Poner en riesgo redes y servidores de otras maneras.</span><span class="sxs-lookup"><span data-stu-id="8a080-603">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="8a080-604">Estos son algunos de los pasos de seguridad con los que se reduce la probabilidad de sufrir ataques:</span><span class="sxs-lookup"><span data-stu-id="8a080-604">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="8a080-605">Cargue los archivos a un área de carga de archivos dedicada, preferiblemente una unidad que no sea de sistema.</span><span class="sxs-lookup"><span data-stu-id="8a080-605">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="8a080-606">Una ubicación dedicada facilita la imposición de restricciones de seguridad en los archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="8a080-606">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="8a080-607">Deshabilite la ejecución de los permisos en la ubicación de carga de archivos.&dagger;</span><span class="sxs-lookup"><span data-stu-id="8a080-607">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="8a080-608">Los archivos cargados **no** se deben persistir en el mismo árbol de directorio que la aplicación.&dagger;</span><span class="sxs-lookup"><span data-stu-id="8a080-608">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="8a080-609">Use un nombre de archivo seguro determinado por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a080-609">Use a safe file name determined by the app.</span></span> <span data-ttu-id="8a080-610">No use un nombre de archivo proporcionado por el usuario o el nombre de archivo que no es de confianza del archivo cargado. &dagger; HTML codifica el nombre de archivo que no es de confianza al mostrarlo.</span><span class="sxs-lookup"><span data-stu-id="8a080-610">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="8a080-611">Por ejemplo, registrando el nombre de archivo o mostrándose en la interfaz de usuario (la Razor salida codifica automáticamente html).</span><span class="sxs-lookup"><span data-stu-id="8a080-611">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="8a080-612">Permita solo las extensiones de archivo aprobadas para la especificación de diseño de la aplicación.&dagger;</span><span class="sxs-lookup"><span data-stu-id="8a080-612">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="8a080-613">Compruebe que las comprobaciones del lado cliente se realizan en el servidor. &dagger; Las comprobaciones del lado cliente son fáciles de eludir.</span><span class="sxs-lookup"><span data-stu-id="8a080-613">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="8a080-614">Compruebe el tamaño de un archivo cargado.</span><span class="sxs-lookup"><span data-stu-id="8a080-614">Check the size of an uploaded file.</span></span> <span data-ttu-id="8a080-615">Establezca un límite de tamaño máximo para evitar cargas grandes.&dagger;</span><span class="sxs-lookup"><span data-stu-id="8a080-615">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="8a080-616">Cuando un archivo cargado con el mismo nombre no deba sobrescribir los archivos, vuelva a comprobar el nombre de archivo en la base de datos o en el almacenamiento físico antes de cargarlo.</span><span class="sxs-lookup"><span data-stu-id="8a080-616">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="8a080-617">**Ejecute un detector de virus o malware en el contenido cargado antes de que se almacene el archivo.**</span><span class="sxs-lookup"><span data-stu-id="8a080-617">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="8a080-618">&dagger;La aplicación de ejemplo muestra un enfoque que cumple los criterios.</span><span class="sxs-lookup"><span data-stu-id="8a080-618">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="8a080-619">La carga de código malintencionado en un sistema suele ser el primer paso para ejecutar código que puede:</span><span class="sxs-lookup"><span data-stu-id="8a080-619">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="8a080-620">Obtener el control completo de un sistema.</span><span class="sxs-lookup"><span data-stu-id="8a080-620">Completely gain control of a system.</span></span>
> * <span data-ttu-id="8a080-621">Sobrecargar un sistema de manera que el sistema se bloquea.</span><span class="sxs-lookup"><span data-stu-id="8a080-621">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="8a080-622">Poner en peligro los datos del usuario o del sistema.</span><span class="sxs-lookup"><span data-stu-id="8a080-622">Compromise user or system data.</span></span>
> * <span data-ttu-id="8a080-623">Aplicar grafitis a una interfaz de usuario pública.</span><span class="sxs-lookup"><span data-stu-id="8a080-623">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="8a080-624">Vea los siguientes recursos para más información sobre cómo reducir el área expuesta de ataques al aceptar archivos de los usuarios:</span><span class="sxs-lookup"><span data-stu-id="8a080-624">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="8a080-625">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carga de archivos sin restricciones)</span><span class="sxs-lookup"><span data-stu-id="8a080-625">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="8a080-626">Azure Security: asegúrese de que los controles adecuados estén en vigor al aceptar archivos de usuarios</span><span class="sxs-lookup"><span data-stu-id="8a080-626">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="8a080-627">Para más información sobre cómo implementar medidas de seguridad, incluidos ejemplos de la aplicación de ejemplo, consulte la sección [Validación](#validation).</span><span class="sxs-lookup"><span data-stu-id="8a080-627">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="8a080-628">Escenarios de almacenamiento</span><span class="sxs-lookup"><span data-stu-id="8a080-628">Storage scenarios</span></span>

<span data-ttu-id="8a080-629">Las opciones de almacenamiento comunes para los archivos incluyen:</span><span class="sxs-lookup"><span data-stu-id="8a080-629">Common storage options for files include:</span></span>

* <span data-ttu-id="8a080-630">Base de datos</span><span class="sxs-lookup"><span data-stu-id="8a080-630">Database</span></span>

  * <span data-ttu-id="8a080-631">En el caso de cargas de archivos pequeñas, una base de datos suele ser más rápida que las opciones de almacenamiento físico (sistema de archivos o recurso compartido de red).</span><span class="sxs-lookup"><span data-stu-id="8a080-631">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="8a080-632">Una base de datos suele ser más conveniente que las opciones de almacenamiento físico, ya que la recuperación de un registro de base de datos para los datos de usuario puede proporcionar el contenido del archivo (por ejemplo, una imagen de avatar).</span><span class="sxs-lookup"><span data-stu-id="8a080-632">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="8a080-633">Una base de datos puede ser más económica que usar un servicio de almacenamiento de datos.</span><span class="sxs-lookup"><span data-stu-id="8a080-633">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="8a080-634">Almacenamiento físico (sistema de archivos o recurso compartido de red)</span><span class="sxs-lookup"><span data-stu-id="8a080-634">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="8a080-635">Para cargas de archivos de gran tamaño:</span><span class="sxs-lookup"><span data-stu-id="8a080-635">For large file uploads:</span></span>
    * <span data-ttu-id="8a080-636">Los límites de base de datos pueden restringir el tamaño de la carga.</span><span class="sxs-lookup"><span data-stu-id="8a080-636">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="8a080-637">El almacenamiento físico suele ser menos económico que el almacenamiento en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="8a080-637">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="8a080-638">El almacenamiento físico puede ser más económico que usar un servicio de almacenamiento de datos.</span><span class="sxs-lookup"><span data-stu-id="8a080-638">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="8a080-639">El proceso de la aplicación debe tener permisos de lectura y escritura en la ubicación de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="8a080-639">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="8a080-640">**Nunca conceda el permiso de ejecución.**</span><span class="sxs-lookup"><span data-stu-id="8a080-640">**Never grant execute permission.**</span></span>

* <span data-ttu-id="8a080-641">Servicio de almacenamiento de datos (por ejemplo, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="8a080-641">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="8a080-642">Los servicios suelen ofrecer una escalabilidad y resistencia mejoradas sobre las soluciones locales que normalmente están sujetas a únicos puntos de error.</span><span class="sxs-lookup"><span data-stu-id="8a080-642">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="8a080-643">Los servicios pueden tener un costo menor en escenarios de infraestructura de almacenamiento de gran tamaño.</span><span class="sxs-lookup"><span data-stu-id="8a080-643">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="8a080-644">Para obtener más información, vea [Inicio rápido: usar .net para crear un BLOB en el almacenamiento de objetos](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="8a080-644">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="8a080-645">En el tema se muestra <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, pero se puede usar <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> para guardar un <xref:System.IO.FileStream> en el almacenamiento de blobs cuando se trabaja con un <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="8a080-645">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="8a080-646">Escenarios de carga de archivos</span><span class="sxs-lookup"><span data-stu-id="8a080-646">File upload scenarios</span></span>

<span data-ttu-id="8a080-647">Dos enfoques generales para cargar archivos son el almacenamiento en búfer y el streaming.</span><span class="sxs-lookup"><span data-stu-id="8a080-647">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="8a080-648">**de respuesta**</span><span class="sxs-lookup"><span data-stu-id="8a080-648">**Buffering**</span></span>

<span data-ttu-id="8a080-649">El archivo completo se lee en un <xref:Microsoft.AspNetCore.Http.IFormFile>, que es una representación de C# del archivo que se usa para procesar o guardar el archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-649">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="8a080-650">Los recursos (disco, memoria) que se usan en las cargas de archivos dependen de la cantidad y del tamaño de las cargas de archivos que se realizan simultáneamente.</span><span class="sxs-lookup"><span data-stu-id="8a080-650">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="8a080-651">Si una aplicación intenta almacenar demasiadas cargas en el búfer, el sitio se bloquea cuando se queda sin memoria o sin espacio en disco.</span><span class="sxs-lookup"><span data-stu-id="8a080-651">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="8a080-652">Si el tamaño o la frecuencia de las cargas de archivos está agotando los recursos de la aplicación, use el streaming.</span><span class="sxs-lookup"><span data-stu-id="8a080-652">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="8a080-653">Cualquier archivo almacenado en búfer único que supere los 64 KB se mueve de la memoria a un archivo temporal en el disco.</span><span class="sxs-lookup"><span data-stu-id="8a080-653">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="8a080-654">En las secciones siguientes de este tema se habla del almacenamiento en búfer de archivos pequeños:</span><span class="sxs-lookup"><span data-stu-id="8a080-654">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="8a080-655">Almacenamiento físico</span><span class="sxs-lookup"><span data-stu-id="8a080-655">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="8a080-656">Base de datos</span><span class="sxs-lookup"><span data-stu-id="8a080-656">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="8a080-657">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="8a080-657">**Streaming**</span></span>

<span data-ttu-id="8a080-658">El archivo se recibe de una solicitud de varias partes y lo procesa o guarda directamente la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a080-658">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="8a080-659">El streaming no mejora considerablemente el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="8a080-659">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="8a080-660">El streaming reduce las demandas de memoria o espacio en disco cuando se cargan archivos.</span><span class="sxs-lookup"><span data-stu-id="8a080-660">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="8a080-661">El streaming de archivos grandes se describe en la sección [Carga de archivos de gran tamaño con streaming](#upload-large-files-with-streaming).</span><span class="sxs-lookup"><span data-stu-id="8a080-661">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="8a080-662">Carga de archivos pequeños con enlace de modelos almacenado en búfer al almacenamiento físico</span><span class="sxs-lookup"><span data-stu-id="8a080-662">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="8a080-663">Para cargar archivos pequeños, se puede usar un formulario de varias partes o construir una solicitud POST con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8a080-663">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="8a080-664">En el ejemplo siguiente se muestra el uso de un Razor formulario de páginas para cargar un único archivo ( *pages/BufferedSingleFileUploadPhysical. cshtml* en la aplicación de ejemplo):</span><span class="sxs-lookup"><span data-stu-id="8a080-664">The following example demonstrates the use of a Razor Pages form to upload a single file ( *Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="8a080-665">El ejemplo siguiente es análogo al ejemplo anterior, salvo en que:</span><span class="sxs-lookup"><span data-stu-id="8a080-665">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="8a080-666">([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) de JavaScript se usa para enviar los datos del formulario.</span><span class="sxs-lookup"><span data-stu-id="8a080-666">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="8a080-667">No hay ninguna validación.</span><span class="sxs-lookup"><span data-stu-id="8a080-667">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="8a080-668">Para realizar la solicitud POST en JavaScript para los clientes que [no admiten Fetch API](https://caniuse.com/#feat=fetch), use uno de estos enfoques:</span><span class="sxs-lookup"><span data-stu-id="8a080-668">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="8a080-669">Use un Fetch Polyfill (por ejemplo, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="8a080-669">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="8a080-670">Use `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="8a080-670">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="8a080-671">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8a080-671">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="8a080-672">Para admitir las cargas de archivos, los formularios HTML deben especificar un tipo de codificación (`enctype`) de `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="8a080-672">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="8a080-673">Para que un elemento de entrada `files` admita al carga de varios archivos, proporcione el atributo `multiple` en el elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="8a080-673">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="8a080-674">Es posible acceder a archivos individuales cargados en el servidor a través del [enlace de modelos](xref:mvc/models/model-binding) mediante <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="8a080-674">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="8a080-675">La aplicación de ejemplo muestra varias cargas de archivos almacenados en búfer para escenarios de almacenamiento físico y base de datos.</span><span class="sxs-lookup"><span data-stu-id="8a080-675">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="8a080-676">**No** utilice la propiedad `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> para usos distintos a la presentación y el registro.</span><span class="sxs-lookup"><span data-stu-id="8a080-676">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="8a080-677">Para fines de presentación y registro, codifique el nombre de archivo en HTML.</span><span class="sxs-lookup"><span data-stu-id="8a080-677">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="8a080-678">Un atacante puede proporcionar un nombre de archivo malintencionado, incluidas rutas de acceso completas o relativas.</span><span class="sxs-lookup"><span data-stu-id="8a080-678">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="8a080-679">Las aplicaciones deben:</span><span class="sxs-lookup"><span data-stu-id="8a080-679">Applications should:</span></span>
>
> * <span data-ttu-id="8a080-680">Quitar la ruta de acceso del nombre de archivo proporcionado por el usuario.</span><span class="sxs-lookup"><span data-stu-id="8a080-680">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="8a080-681">Guardar el nombre de archivo codificado en HTML, sin la ruta de acceso para la interfaz de usuario o el registro.</span><span class="sxs-lookup"><span data-stu-id="8a080-681">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="8a080-682">Generar un nombre de archivo aleatorio nuevo para el almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="8a080-682">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="8a080-683">En el código siguiente se quita la ruta de acceso del nombre del archivo:</span><span class="sxs-lookup"><span data-stu-id="8a080-683">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="8a080-684">Los ejemplos proporcionados hasta ahora no tienen en cuenta las consideraciones de seguridad.</span><span class="sxs-lookup"><span data-stu-id="8a080-684">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="8a080-685">Se proporciona información adicional en las secciones siguientes y en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="8a080-685">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="8a080-686">Consideraciones de seguridad</span><span class="sxs-lookup"><span data-stu-id="8a080-686">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="8a080-687">Validación</span><span class="sxs-lookup"><span data-stu-id="8a080-687">Validation</span></span>](#validation)

<span data-ttu-id="8a080-688">Al cargar archivos mediante el enlace de modelos y <xref:Microsoft.AspNetCore.Http.IFormFile>, el método de acción puede aceptar:</span><span class="sxs-lookup"><span data-stu-id="8a080-688">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="8a080-689">Un <xref:Microsoft.AspNetCore.Http.IFormFile> único.</span><span class="sxs-lookup"><span data-stu-id="8a080-689">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="8a080-690">Cualquiera de las colecciones siguientes que representan varios archivos:</span><span class="sxs-lookup"><span data-stu-id="8a080-690">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="8a080-691">[Lista](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="8a080-691">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="8a080-692">El enlace coincide con los archivos de formulario por nombre.</span><span class="sxs-lookup"><span data-stu-id="8a080-692">Binding matches form files by name.</span></span> <span data-ttu-id="8a080-693">Por ejemplo, el valor `name` HTML en `<input type="file" name="formFile">` debe coincidir con la propiedad/el parámetro enlazado de C# (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="8a080-693">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="8a080-694">Para más información, consulte la sección [Coincidencia del valor de atributo de nombre con el nombre del parámetro del método POST](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="8a080-694">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="8a080-695">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a080-695">The following example:</span></span>

* <span data-ttu-id="8a080-696">Recorre en bucle uno o más archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="8a080-696">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="8a080-697">Usa [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) para devolver una ruta de acceso completa de un archivo, incluido el nombre de archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-697">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="8a080-698">Guarda los archivos en el sistema de archivos local con un nombre de archivo generado por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a080-698">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="8a080-699">Devuelve el número total y el tamaño de los archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="8a080-699">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="8a080-700">Use `Path.GetRandomFileName` para generar un nombre de archivo sin una ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="8a080-700">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="8a080-701">En el ejemplo siguiente, la ruta de acceso se obtiene de la configuración:</span><span class="sxs-lookup"><span data-stu-id="8a080-701">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="8a080-702">La ruta de acceso pasada a <xref:System.IO.FileStream> *debe* incluir el nombre de archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-702">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="8a080-703">Si no se proporciona el nombre de archivo, se produce una <xref:System.UnauthorizedAccessException> en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="8a080-703">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="8a080-704">Los archivos que se cargan usando la técnica <xref:Microsoft.AspNetCore.Http.IFormFile> se almacenan en búfer en memoria o en disco en el servidor web antes de procesarse.</span><span class="sxs-lookup"><span data-stu-id="8a080-704">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="8a080-705">Dentro del método de acción, se puede tener acceso al contenido de <xref:Microsoft.AspNetCore.Http.IFormFile> como <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="8a080-705">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="8a080-706">Además del sistema de archivos local, los archivos se pueden guardar en un recurso compartido de red o en un servicio de almacenamiento de archivos, como [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="8a080-706">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="8a080-707">Para ver otro ejemplo que recorre en bucle varios archivos para cargar y usa nombres de archivo seguros, consulte *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="8a080-707">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="8a080-708">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) arroja una <xref:System.IO.IOException> si se crean más de 65 535 archivos sin eliminar los archivos temporales anteriores.</span><span class="sxs-lookup"><span data-stu-id="8a080-708">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="8a080-709">El límite de 65 535 archivos es un límite por servidor.</span><span class="sxs-lookup"><span data-stu-id="8a080-709">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="8a080-710">Para más información sobre este límite en el sistema operativo Windows, consulte las notas en los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="8a080-710">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="8a080-711">Función GetTempFileNameA</span><span class="sxs-lookup"><span data-stu-id="8a080-711">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="8a080-712">Carga de archivos pequeños con enlace de modelos almacenado en búfer a una base de datos</span><span class="sxs-lookup"><span data-stu-id="8a080-712">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="8a080-713">Para almacenar datos de archivo binario en una base de datos con [Entity Framework](/ef/core/index), defina una propiedad de matriz <xref:System.Byte> en la entidad:</span><span class="sxs-lookup"><span data-stu-id="8a080-713">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="8a080-714">Especifique una propiedad de modelo de página para la clase que incluya un <xref:Microsoft.AspNetCore.Http.IFormFile>:</span><span class="sxs-lookup"><span data-stu-id="8a080-714">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="8a080-715"><xref:Microsoft.AspNetCore.Http.IFormFile> se puede usar directamente como un parámetro de método de acción o como una propiedad de modelo enlazado.</span><span class="sxs-lookup"><span data-stu-id="8a080-715"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="8a080-716">En el ejemplo anterior se utiliza una propiedad de modelo enlazado.</span><span class="sxs-lookup"><span data-stu-id="8a080-716">The prior example uses a bound model property.</span></span>

<span data-ttu-id="8a080-717">`FileUpload`Se utiliza en el Razor formulario de páginas:</span><span class="sxs-lookup"><span data-stu-id="8a080-717">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="8a080-718">Cuando el formulario se publique en el servidor, copie el <xref:Microsoft.AspNetCore.Http.IFormFile> en un flujo y guárdelo como matriz de bytes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8a080-718">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="8a080-719">En el ejemplo siguiente, `_dbContext` almacena el contexto de base de datos de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="8a080-719">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="8a080-720">El ejemplo anterior es similar a un escenario que se muestra en la aplicación de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8a080-720">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="8a080-721">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8a080-721">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="8a080-722">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="8a080-722">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="8a080-723">Tenga cuidado al almacenar los datos binarios en bases de datos relacionales, ya que esto puede repercutir adversamente en el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="8a080-723">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="8a080-724">No se base ni confíe en la propiedad `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> sin validarla.</span><span class="sxs-lookup"><span data-stu-id="8a080-724">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="8a080-725">La propiedad `FileName` solo debe usarse para fines de presentación y solo después de la codificación HTML.</span><span class="sxs-lookup"><span data-stu-id="8a080-725">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="8a080-726">Los ejemplos proporcionados no tienen en cuenta las consideraciones de seguridad.</span><span class="sxs-lookup"><span data-stu-id="8a080-726">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="8a080-727">Se proporciona información adicional en las secciones siguientes y en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="8a080-727">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="8a080-728">Consideraciones de seguridad</span><span class="sxs-lookup"><span data-stu-id="8a080-728">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="8a080-729">Validación</span><span class="sxs-lookup"><span data-stu-id="8a080-729">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="8a080-730">Carga de archivos de gran tamaño con streaming</span><span class="sxs-lookup"><span data-stu-id="8a080-730">Upload large files with streaming</span></span>

<span data-ttu-id="8a080-731">En el ejemplo siguiente se muestra cómo usar JavaScript para transmitir un archivo a una acción de controlador.</span><span class="sxs-lookup"><span data-stu-id="8a080-731">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="8a080-732">El token de antifalsificación del archivo se genera por medio de un atributo de filtro personalizado y se pasa a los encabezados HTTP del cliente, en lugar de en el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="8a080-732">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="8a080-733">Dado que el método de acción procesa los datos cargados directamente, el enlace de modelos del formulario se deshabilita por otro filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="8a080-733">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="8a080-734">Dentro de la acción, el contenido del formulario se lee usando un `MultipartReader` (que lee cada `MultipartSection` individual), de forma que el archivo se procesa o el contenido se almacena, según corresponda.</span><span class="sxs-lookup"><span data-stu-id="8a080-734">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="8a080-735">Una vez leídas las secciones de varias partes, la acción realiza su propio enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="8a080-735">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="8a080-736">La respuesta de página inicial carga el formulario y guarda un token antifalsificación en un cookie (mediante el `GenerateAntiforgeryTokenCookieAttribute` atributo).</span><span class="sxs-lookup"><span data-stu-id="8a080-736">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="8a080-737">El atributo usa la compatibilidad con la [antifalsificación](xref:security/anti-request-forgery) integrada de ASP.net Core para establecer un cookie con un token de solicitud:</span><span class="sxs-lookup"><span data-stu-id="8a080-737">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="8a080-738">El `DisableFormValueModelBindingAttribute` se usa para deshabilitar el enlace de modelos:</span><span class="sxs-lookup"><span data-stu-id="8a080-738">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="8a080-739">En la aplicación de ejemplo, `GenerateAntiforgeryTokenCookieAttribute` y `DisableFormValueModelBindingAttribute` se aplican como filtros a los modelos de aplicación de página de `/StreamedSingleFileUploadDb` y `/StreamedSingleFileUploadPhysical` en `Startup.ConfigureServices` mediante [ Razor las convenciones de páginas](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="8a080-739">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="8a080-740">Dado que el enlace de modelos no lee el formulario, los parámetros enlazados desde el formulario no se enlazan (la consulta, la ruta y el encabezado siguen funcionando).</span><span class="sxs-lookup"><span data-stu-id="8a080-740">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="8a080-741">El método de acción funciona directamente con la propiedad `Request`.</span><span class="sxs-lookup"><span data-stu-id="8a080-741">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="8a080-742">Se usa un elemento `MultipartReader` para leer cada sección.</span><span class="sxs-lookup"><span data-stu-id="8a080-742">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="8a080-743">Los datos de clave-valor se almacenan en un `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="8a080-743">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="8a080-744">Una vez leídas las secciones de varias partes, el contenido del `KeyValueAccumulator` se usa para enlazar los datos del formulario a un tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="8a080-744">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="8a080-745">El método `StreamingController.UploadDatabase` completo para streaming a una base de datos con EF Core:</span><span class="sxs-lookup"><span data-stu-id="8a080-745">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="8a080-746">`MultipartRequestHelper` ( *Utilities/MultipartRequestHelper.cs* ):</span><span class="sxs-lookup"><span data-stu-id="8a080-746">`MultipartRequestHelper` ( *Utilities/MultipartRequestHelper.cs* ):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="8a080-747">El método `StreamingController.UploadPhysical` completo para streaming a una ubicación física:</span><span class="sxs-lookup"><span data-stu-id="8a080-747">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="8a080-748">En la aplicación de ejemplo, las comprobaciones de validación las controla `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="8a080-748">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="8a080-749">Validación</span><span class="sxs-lookup"><span data-stu-id="8a080-749">Validation</span></span>

<span data-ttu-id="8a080-750">La clase `FileHelpers` de la aplicación de ejemplo muestra varias comprobaciones de cargas de archivos de streaming y <xref:Microsoft.AspNetCore.Http.IFormFile> almacenado en búfer.</span><span class="sxs-lookup"><span data-stu-id="8a080-750">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="8a080-751">Para procesar cargas de archivos almacenadas en búfer de <xref:Microsoft.AspNetCore.Http.IFormFile> en la aplicación de ejemplo, consulte el método `ProcessFormFile` en el archivo *Utilities/FileHelpers.cs* .</span><span class="sxs-lookup"><span data-stu-id="8a080-751">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="8a080-752">Para procesar archivos de streaming, consulte el método `ProcessStreamedFile` en el mismo archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-752">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="8a080-753">Los métodos de procesamiento de validación mostrados en la aplicación de ejemplo no examinan el contenido de los archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="8a080-753">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="8a080-754">En la mayoría de los escenarios de producción, se usa una API de analizador de virus/malware en el archivo antes de que el archivo esté disponible para los usuarios u otros sistemas.</span><span class="sxs-lookup"><span data-stu-id="8a080-754">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="8a080-755">Aunque el ejemplo de tema proporciona un ejemplo funcional de técnicas de validación, no implemente la clase `FileHelpers` en una aplicación de producción a menos que:</span><span class="sxs-lookup"><span data-stu-id="8a080-755">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="8a080-756">Comprenda totalmente la implementación.</span><span class="sxs-lookup"><span data-stu-id="8a080-756">Fully understand the implementation.</span></span>
> * <span data-ttu-id="8a080-757">Modifique la implementación según corresponda en función del entorno y las especificaciones de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a080-757">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="8a080-758">**No implemente nunca de manera indiscriminada el código de seguridad en una aplicación sin abordar estos requisitos.**</span><span class="sxs-lookup"><span data-stu-id="8a080-758">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="8a080-759">Validación de contenido</span><span class="sxs-lookup"><span data-stu-id="8a080-759">Content validation</span></span>

<span data-ttu-id="8a080-760">**Use una API de detección de virus/malware de terceros en el contenido cargado.**</span><span class="sxs-lookup"><span data-stu-id="8a080-760">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="8a080-761">El análisis de archivos exige recursos del servidor en escenarios de gran volumen.</span><span class="sxs-lookup"><span data-stu-id="8a080-761">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="8a080-762">Si disminuye el rendimiento de procesamiento de solicitudes debido al análisis de archivos, considere la posibilidad de descargar el trabajo de análisis a un [servicio en segundo plano](xref:fundamentals/host/hosted-services), posiblemente un servicio que se ejecute en otro servidor desde el servidor de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a080-762">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="8a080-763">Habitualmente, los archivos cargados se almacenan en un área en cuarentena hasta que el programa de detección de virus en segundo plano los revisa.</span><span class="sxs-lookup"><span data-stu-id="8a080-763">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="8a080-764">Cuando se pasa un archivo, se mueve a la ubicación de almacenamiento de archivos habitual.</span><span class="sxs-lookup"><span data-stu-id="8a080-764">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="8a080-765">Por lo general, estos pasos se realizan junto con un registro de base de datos que indica el estado de análisis de un archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-765">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="8a080-766">Mediante el uso de este enfoque, la aplicación y el servidor de aplicaciones permanecen centrados en responder a las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="8a080-766">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="8a080-767">Validación de la extensión del archivo</span><span class="sxs-lookup"><span data-stu-id="8a080-767">File extension validation</span></span>

<span data-ttu-id="8a080-768">La extensión del archivo cargado debe comprobarse con una lista de extensiones permitidas.</span><span class="sxs-lookup"><span data-stu-id="8a080-768">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="8a080-769">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8a080-769">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="8a080-770">Validación de firma del archivo</span><span class="sxs-lookup"><span data-stu-id="8a080-770">File signature validation</span></span>

<span data-ttu-id="8a080-771">La firma de un archivo viene determinada por los primeros bytes al principio de un archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-771">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="8a080-772">Estos bytes se pueden usar para indicar si la extensión coincide con el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-772">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="8a080-773">La aplicación de ejemplo comprueba las firmas de archivo de algunos tipos de archivo comunes.</span><span class="sxs-lookup"><span data-stu-id="8a080-773">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="8a080-774">En el ejemplo siguiente, la firma de archivo de una imagen JPEG se comprueba con respecto al archivo:</span><span class="sxs-lookup"><span data-stu-id="8a080-774">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="8a080-775">Para obtener firmas de archivo adicionales, consulte la [base de datos de firmas de archivo](https://www.filesignatures.net/) y las especificaciones de archivo oficiales.</span><span class="sxs-lookup"><span data-stu-id="8a080-775">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="8a080-776">Seguridad de nombre de archivo</span><span class="sxs-lookup"><span data-stu-id="8a080-776">File name security</span></span>

<span data-ttu-id="8a080-777">Nunca use un nombre de archivo proporcionado por el cliente para guardar un archivo en el almacenamiento físico.</span><span class="sxs-lookup"><span data-stu-id="8a080-777">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="8a080-778">Cree un nombre de archivo seguro para el archivo con [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) o [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) para crear una ruta de acceso completa (incluido el nombre de archivo) para el almacenamiento temporal.</span><span class="sxs-lookup"><span data-stu-id="8a080-778">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="8a080-779">Razor HTML codifica automáticamente los valores de propiedad para la presentación.</span><span class="sxs-lookup"><span data-stu-id="8a080-779">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="8a080-780">El código siguiente es seguro de usar:</span><span class="sxs-lookup"><span data-stu-id="8a080-780">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="8a080-781">Fuera de Razor , <xref:System.Net.WebUtility.HtmlEncode*> el contenido del nombre de archivo siempre procedente de la solicitud de un usuario.</span><span class="sxs-lookup"><span data-stu-id="8a080-781">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="8a080-782">Muchas implementaciones deben incluir una comprobación de que el archivo existe; de lo contrario, el archivo se sobrescribe por un archivo con el mismo nombre.</span><span class="sxs-lookup"><span data-stu-id="8a080-782">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="8a080-783">Proporcione lógica adicional para satisfacer las especificaciones de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8a080-783">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="8a080-784">Validación del tamaño</span><span class="sxs-lookup"><span data-stu-id="8a080-784">Size validation</span></span>

<span data-ttu-id="8a080-785">Limite el tamaño de los archivos cargados.</span><span class="sxs-lookup"><span data-stu-id="8a080-785">Limit the size of uploaded files.</span></span>

<span data-ttu-id="8a080-786">En la aplicación de ejemplo, el tamaño del archivo está limitado a 2 MB (se indica en bytes).</span><span class="sxs-lookup"><span data-stu-id="8a080-786">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="8a080-787">El límite se proporciona a través de la [configuración](xref:fundamentals/configuration/index) del *appsettings.json* archivo:</span><span class="sxs-lookup"><span data-stu-id="8a080-787">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="8a080-788">`FileSizeLimit` se inserta en las clases `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="8a080-788">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="8a080-789">Cuando el tamaño de un archivo supera el límite, se rechaza el archivo:</span><span class="sxs-lookup"><span data-stu-id="8a080-789">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="8a080-790">Coincidencia del valor de atributo de nombre con el nombre del parámetro del método POST</span><span class="sxs-lookup"><span data-stu-id="8a080-790">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="8a080-791">En los que no son Razor formularios y que envían datos de formulario `FormData` directamente, el nombre especificado en el elemento del formulario o `FormData` deben coincidir con el nombre del parámetro en la acción del controlador.</span><span class="sxs-lookup"><span data-stu-id="8a080-791">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="8a080-792">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="8a080-792">In the following example:</span></span>

* <span data-ttu-id="8a080-793">Cuando se usa un elemento `<input>`, el atributo `name` se establece en el valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="8a080-793">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="8a080-794">Cuando se usa `FormData` en JavaScript, el nombre se establece en el valor `battlePlans`:</span><span class="sxs-lookup"><span data-stu-id="8a080-794">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="8a080-795">Use un nombre coincidente para el parámetro del método de C# (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="8a080-795">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="8a080-796">Para un Razor método de controlador de página de páginas denominado `Upload` :</span><span class="sxs-lookup"><span data-stu-id="8a080-796">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="8a080-797">Para un método de acción de controlador POST de MVC:</span><span class="sxs-lookup"><span data-stu-id="8a080-797">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="8a080-798">Configuración del servidor y de la aplicación</span><span class="sxs-lookup"><span data-stu-id="8a080-798">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="8a080-799">Límite de longitud del cuerpo de varias partes</span><span class="sxs-lookup"><span data-stu-id="8a080-799">Multipart body length limit</span></span>

<span data-ttu-id="8a080-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> establece el límite de la longitud de cada cuerpo de varias partes.</span><span class="sxs-lookup"><span data-stu-id="8a080-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="8a080-801">Las secciones del formulario que superan este límite inician una <xref:System.IO.InvalidDataException> cuando se analizan.</span><span class="sxs-lookup"><span data-stu-id="8a080-801">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="8a080-802">El valor predeterminado es 134 217 728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="8a080-802">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="8a080-803">Personalice el límite mediante el valor <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="8a080-803">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="8a080-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> se utiliza para establecer el <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> para una sola página o acción.</span><span class="sxs-lookup"><span data-stu-id="8a080-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="8a080-805">En una Razor aplicación de páginas, aplique el filtro con una [Convención](xref:razor-pages/razor-pages-conventions) en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8a080-805">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="8a080-806">En una Razor aplicación de páginas o en una aplicación MVC, aplique el filtro al método de acción o modelo de página:</span><span class="sxs-lookup"><span data-stu-id="8a080-806">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="8a080-807">Tamaño máximo del cuerpo de la solicitud de Kestrel</span><span class="sxs-lookup"><span data-stu-id="8a080-807">Kestrel maximum request body size</span></span>

<span data-ttu-id="8a080-808">En el caso de las aplicaciones hospedadas por Kestrel, el tamaño máximo predeterminado del cuerpo de solicitud es 30 000 000 bytes, que son aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="8a080-808">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="8a080-809">Personalice el límite con la opción [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) del servidor de Kestrel:</span><span class="sxs-lookup"><span data-stu-id="8a080-809">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<span data-ttu-id="8a080-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> se usa para establecer el valor de [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) de una sola página o acción.</span><span class="sxs-lookup"><span data-stu-id="8a080-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="8a080-811">En una Razor aplicación de páginas, aplique el filtro con una [Convención](xref:razor-pages/razor-pages-conventions) en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8a080-811">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="8a080-812">En una Razor aplicación pages o MVC, aplique el filtro a la clase de controlador de páginas o al método de acción:</span><span class="sxs-lookup"><span data-stu-id="8a080-812">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="8a080-813">Otros límites de Kestrel</span><span class="sxs-lookup"><span data-stu-id="8a080-813">Other Kestrel limits</span></span>

<span data-ttu-id="8a080-814">Otros límites de Kestrel pueden aplicarse a las aplicaciones hospedadas por Kestrel:</span><span class="sxs-lookup"><span data-stu-id="8a080-814">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="8a080-815">Las conexiones máximas de cliente</span><span class="sxs-lookup"><span data-stu-id="8a080-815">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="8a080-816">Tarifas de datos de solicitud y respuesta</span><span class="sxs-lookup"><span data-stu-id="8a080-816">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="8a080-817">IIS</span><span class="sxs-lookup"><span data-stu-id="8a080-817">IIS</span></span>

<span data-ttu-id="8a080-818">El límite de solicitud predeterminado ( `maxAllowedContentLength` ) es de 30 millones bytes, que es aproximadamente de 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="8a080-818">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="8a080-819">Personalice el límite en el `web.config` archivo.</span><span class="sxs-lookup"><span data-stu-id="8a080-819">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="8a080-820">En el ejemplo siguiente, el límite se establece en 50 MB (52.428.800 bytes):</span><span class="sxs-lookup"><span data-stu-id="8a080-820">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="8a080-821">La `maxAllowedContentLength` configuración solo se aplica a IIS.</span><span class="sxs-lookup"><span data-stu-id="8a080-821">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="8a080-822">Para obtener más información, consulte [límites `<requestLimits>` de solicitudes ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="8a080-822">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="8a080-823">Aumente el tamaño máximo del cuerpo de solicitud para la solicitud HTTP estableciendo <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8a080-823">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8a080-824">En el ejemplo siguiente, el límite se establece en 50 MB (52.428.800 bytes):</span><span class="sxs-lookup"><span data-stu-id="8a080-824">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="8a080-825">Para obtener más información, vea <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="8a080-825">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="8a080-826">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="8a080-826">Troubleshoot</span></span>

<span data-ttu-id="8a080-827">Aquí incluimos algunos problemas comunes que pueden surgir al cargar archivos, así como sus posibles soluciones.</span><span class="sxs-lookup"><span data-stu-id="8a080-827">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="8a080-828">Error No encontrado al implementar en un servidor IIS</span><span class="sxs-lookup"><span data-stu-id="8a080-828">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="8a080-829">El error siguiente indica que el archivo cargado supera la longitud configurada del contenido del servidor:</span><span class="sxs-lookup"><span data-stu-id="8a080-829">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="8a080-830">Para más información, consulte la sección sobre [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="8a080-830">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="8a080-831">Error de conexión</span><span class="sxs-lookup"><span data-stu-id="8a080-831">Connection failure</span></span>

<span data-ttu-id="8a080-832">Un error de conexión y una conexión del servidor de restablecimiento probablemente indica que el archivo cargado supera el tamaño máximo del cuerpo de la solicitud de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="8a080-832">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="8a080-833">Para más información, consulte la sección [Tamaño máximo del cuerpo de la solicitud de Kestrel](#kestrel-maximum-request-body-size).</span><span class="sxs-lookup"><span data-stu-id="8a080-833">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="8a080-834">Los límites de conexión del cliente de Kestrel también pueden requerir ajustes.</span><span class="sxs-lookup"><span data-stu-id="8a080-834">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="8a080-835">Excepción de referencia nula con IFormFile</span><span class="sxs-lookup"><span data-stu-id="8a080-835">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="8a080-836">Si el controlador acepta archivos cargados con <xref:Microsoft.AspNetCore.Http.IFormFile>, pero el valor es `null`, confirme que el formulario HTML especifica un valor `enctype` de `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="8a080-836">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="8a080-837">Si este atributo no está establecido en el elemento `<form>`, no se llevará a cabo la carga del archivo y cualquier argumento <xref:Microsoft.AspNetCore.Http.IFormFile> enlazado será `null`.</span><span class="sxs-lookup"><span data-stu-id="8a080-837">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="8a080-838">Confirme también que la [nomenclatura de la carga en los datos de formulario coincide con la nomenclatura de la aplicación](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="8a080-838">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="8a080-839">El flujo era demasiado largo</span><span class="sxs-lookup"><span data-stu-id="8a080-839">Stream was too long</span></span>

<span data-ttu-id="8a080-840">Los ejemplos de este tema se basan en <xref:System.IO.MemoryStream> para almacenar el contenido del archivo cargado.</span><span class="sxs-lookup"><span data-stu-id="8a080-840">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="8a080-841">El límite de tamaño de `MemoryStream` es `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="8a080-841">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="8a080-842">Si el escenario de carga de archivos de la aplicación requiere almacenar contenido de archivo superior a 50 MB, use un enfoque alternativo que no dependa de un único valor de `MemoryStream` para almacenar el contenido de un archivo cargado.</span><span class="sxs-lookup"><span data-stu-id="8a080-842">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="8a080-843">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="8a080-843">Additional resources</span></span>

* [<span data-ttu-id="8a080-844">Agotamiento de la solicitud de conexión HTTP</span><span class="sxs-lookup"><span data-stu-id="8a080-844">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* <span data-ttu-id="8a080-845">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carga de archivos sin restricciones)</span><span class="sxs-lookup"><span data-stu-id="8a080-845">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
* [<span data-ttu-id="8a080-846">Seguridad de Azure: marco de seguridad: validación de entrada | Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="8a080-846">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="8a080-847">Patrones de diseño en la nube de Azure: patrón de clave valet</span><span class="sxs-lookup"><span data-stu-id="8a080-847">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
