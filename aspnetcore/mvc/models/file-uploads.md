---
title: Carga de archivos en ASP.NET Core
author: rick-anderson
description: Cómo usar el enlace de modelos y el streaming para cargar archivos en ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/03/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/models/file-uploads
ms.openlocfilehash: 720da8a8fe22f0e1911fd554c094661b4465a335
ms.sourcegitcommit: d9ae1f352d372a20534b57e23646c1a1d9171af1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/21/2020
ms.locfileid: "86568839"
---
# <a name="upload-files-in-aspnet-core"></a>Carga de archivos en ASP.NET Core

Por [Steve Smith](https://ardalis.com/) y [Rutger Storm](https://github.com/rutix)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core admite la carga de uno o varios archivos mediante el enlace de modelos almacenado en búfer de archivos más pequeños y el streaming no almacenado en búfer de archivos de mayor tamaño.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Consideraciones sobre la seguridad

Tenga precaución al proporcionar a los usuarios la capacidad de cargar archivos en un servidor. Es posible que los atacantes intenten lo siguiente:

* Ejecutar ataques por [denegación de servicio](/windows-hardware/drivers/ifs/denial-of-service).
* Cargar virus o malware.
* Poner en riesgo redes y servidores de otras maneras.

Estos son algunos de los pasos de seguridad con los que se reduce la probabilidad de sufrir ataques:

* Cargue los archivos a un área de carga de archivos dedicada, preferiblemente una unidad que no sea de sistema. Una ubicación dedicada facilita la imposición de restricciones de seguridad en los archivos cargados. Deshabilite la ejecución de los permisos en la ubicación de carga de archivos.&dagger;
* Los archivos cargados **no** se deben persistir en el mismo árbol de directorio que la aplicación.&dagger;
* Use un nombre de archivo seguro determinado por la aplicación. No use un nombre de archivo proporcionado por el usuario o el nombre de archivo que no es de confianza del archivo cargado. &dagger; HTML codifica el nombre de archivo que no es de confianza al mostrarlo. Por ejemplo, registrando el nombre de archivo o mostrándose en la interfaz de usuario (la Razor salida codifica automáticamente html).
* Permita solo las extensiones de archivo aprobadas para la especificación de diseño de la aplicación.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Compruebe que las comprobaciones del lado cliente se realizan en el servidor. &dagger; Las comprobaciones del lado cliente son fáciles de eludir.
* Compruebe el tamaño de un archivo cargado. Establezca un límite de tamaño máximo para evitar cargas grandes.&dagger;
* Cuando un archivo cargado con el mismo nombre no deba sobrescribir los archivos, vuelva a comprobar el nombre de archivo en la base de datos o en el almacenamiento físico antes de cargarlo.
* **Ejecute un detector de virus o malware en el contenido cargado antes de que se almacene el archivo.**

&dagger;La aplicación de ejemplo muestra un enfoque que cumple los criterios.

> [!WARNING]
> La carga de código malintencionado en un sistema suele ser el primer paso para ejecutar código que puede:
>
> * Obtener el control completo de un sistema.
> * Sobrecargar un sistema de manera que el sistema se bloquea.
> * Poner en peligro los datos del usuario o del sistema.
> * Aplicar grafitis a una interfaz de usuario pública.
>
> Vea los siguientes recursos para más información sobre cómo reducir el área expuesta de ataques al aceptar archivos de los usuarios:
>
> * [Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carga de archivos sin restricciones)
> * [Azure Security: asegúrese de que los controles adecuados estén en vigor al aceptar archivos de usuarios](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Para más información sobre cómo implementar medidas de seguridad, incluidos ejemplos de la aplicación de ejemplo, consulte la sección [Validación](#validation).

## <a name="storage-scenarios"></a>Escenarios de almacenamiento

Las opciones de almacenamiento comunes para los archivos incluyen:

* Base de datos

  * En el caso de cargas de archivos pequeñas, una base de datos suele ser más rápida que las opciones de almacenamiento físico (sistema de archivos o recurso compartido de red).
  * Una base de datos suele ser más conveniente que las opciones de almacenamiento físico, ya que la recuperación de un registro de base de datos para los datos de usuario puede proporcionar el contenido del archivo (por ejemplo, una imagen de avatar).
  * Una base de datos puede ser más económica que usar un servicio de almacenamiento de datos.

* Almacenamiento físico (sistema de archivos o recurso compartido de red)

  * Para cargas de archivos de gran tamaño:
    * Los límites de base de datos pueden restringir el tamaño de la carga.
    * El almacenamiento físico suele ser menos económico que el almacenamiento en una base de datos.
  * El almacenamiento físico puede ser más económico que usar un servicio de almacenamiento de datos.
  * El proceso de la aplicación debe tener permisos de lectura y escritura en la ubicación de almacenamiento. **Nunca conceda el permiso de ejecución.**

* Servicio de almacenamiento de datos (por ejemplo, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))

  * Los servicios suelen ofrecer una escalabilidad y resistencia mejoradas sobre las soluciones locales que normalmente están sujetas a únicos puntos de error.
  * Los servicios pueden tener un costo menor en escenarios de infraestructura de almacenamiento de gran tamaño.

  Para obtener más información, vea [Inicio rápido: usar .net para crear un BLOB en el almacenamiento de objetos](/azure/storage/blobs/storage-quickstart-blobs-dotnet).

## <a name="file-upload-scenarios"></a>Escenarios de carga de archivos

Dos enfoques generales para cargar archivos son el almacenamiento en búfer y el streaming.

**de respuesta**

El archivo completo se lee en un <xref:Microsoft.AspNetCore.Http.IFormFile>, que es una representación de C# del archivo que se usa para procesar o guardar el archivo.

Los recursos (disco, memoria) que se usan en las cargas de archivos dependen de la cantidad y del tamaño de las cargas de archivos que se realizan simultáneamente. Si una aplicación intenta almacenar demasiadas cargas en el búfer, el sitio se bloquea cuando se queda sin memoria o sin espacio en disco. Si el tamaño o la frecuencia de las cargas de archivos está agotando los recursos de la aplicación, use el streaming.

> [!NOTE]
> Cualquier archivo almacenado en búfer único que supere los 64 KB se mueve de la memoria a un archivo temporal en el disco.

En las secciones siguientes de este tema se habla del almacenamiento en búfer de archivos pequeños:

* [Almacenamiento físico](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Base de datos](#upload-small-files-with-buffered-model-binding-to-a-database)

**Streaming**

El archivo se recibe de una solicitud de varias partes y lo procesa o guarda directamente la aplicación. El streaming no mejora considerablemente el rendimiento. El streaming reduce las demandas de memoria o espacio en disco cuando se cargan archivos.

El streaming de archivos grandes se describe en la sección [Carga de archivos de gran tamaño con streaming](#upload-large-files-with-streaming).

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Carga de archivos pequeños con enlace de modelos almacenado en búfer al almacenamiento físico

Para cargar archivos pequeños, se puede usar un formulario de varias partes o construir una solicitud POST con JavaScript.

En el ejemplo siguiente se muestra el uso de un Razor formulario de páginas para cargar un único archivo (*pages/BufferedSingleFileUploadPhysical. cshtml* en la aplicación de ejemplo):

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

El ejemplo siguiente es análogo al ejemplo anterior, salvo en que:

* ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) de JavaScript se usa para enviar los datos del formulario.
* No hay ninguna validación.

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

Para realizar la solicitud POST en JavaScript para los clientes que [no admiten Fetch API](https://caniuse.com/#feat=fetch), use uno de estos enfoques:

* Use un Fetch Polyfill (por ejemplo, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).
* Mediante `XMLHttpRequest`. Por ejemplo:

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

Para admitir las cargas de archivos, los formularios HTML deben especificar un tipo de codificación (`enctype`) de `multipart/form-data`.

Para que un elemento de entrada `files` admita al carga de varios archivos, proporcione el atributo `multiple` en el elemento `<input>`:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Es posible acceder a archivos individuales cargados en el servidor a través del [enlace de modelos](xref:mvc/models/model-binding) mediante <xref:Microsoft.AspNetCore.Http.IFormFile>. La aplicación de ejemplo muestra varias cargas de archivos almacenados en búfer para escenarios de almacenamiento físico y base de datos.

<a name="filename"></a>

> [!WARNING]
> **No** utilice la propiedad `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> para usos distintos a la presentación y el registro. Para fines de presentación y registro, codifique el nombre de archivo en HTML. Un atacante puede proporcionar un nombre de archivo malintencionado, incluidas rutas de acceso completas o relativas. Las aplicaciones deben:
>
> * Quitar la ruta de acceso del nombre de archivo proporcionado por el usuario.
> * Guardar el nombre de archivo codificado en HTML, sin la ruta de acceso para la interfaz de usuario o el registro.
> * Generar un nombre de archivo aleatorio nuevo para el almacenamiento.
>
> En el código siguiente se quita la ruta de acceso del nombre del archivo:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Los ejemplos proporcionados hasta ahora no tienen en cuenta las consideraciones de seguridad. Se proporciona información adicional en las secciones siguientes y en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Consideraciones sobre la seguridad](#security-considerations)
> * [Validación](#validation)

Al cargar archivos mediante el enlace de modelos y <xref:Microsoft.AspNetCore.Http.IFormFile>, el método de acción puede aceptar:

* Un <xref:Microsoft.AspNetCore.Http.IFormFile> único.
* Cualquiera de las colecciones siguientes que representan varios archivos:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Lista](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> El enlace coincide con los archivos de formulario por nombre. Por ejemplo, el valor `name` HTML en `<input type="file" name="formFile">` debe coincidir con la propiedad/el parámetro enlazado de C# (`FormFile`). Para más información, consulte la sección [Coincidencia del valor de atributo de nombre con el nombre del parámetro del método POST](#match-name-attribute-value-to-parameter-name-of-post-method).

En el ejemplo siguiente:

* Recorre en bucle uno o más archivos cargados.
* Usa [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) para devolver una ruta de acceso completa de un archivo, incluido el nombre de archivo. 
* Guarda los archivos en el sistema de archivos local con un nombre de archivo generado por la aplicación.
* Devuelve el número total y el tamaño de los archivos cargados.

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

Use `Path.GetRandomFileName` para generar un nombre de archivo sin una ruta de acceso. En el ejemplo siguiente, la ruta de acceso se obtiene de la configuración:

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

La ruta de acceso pasada a <xref:System.IO.FileStream> *debe* incluir el nombre de archivo. Si no se proporciona el nombre de archivo, se produce una <xref:System.UnauthorizedAccessException> en tiempo de ejecución.

Los archivos que se cargan usando la técnica <xref:Microsoft.AspNetCore.Http.IFormFile> se almacenan en búfer en memoria o en disco en el servidor web antes de procesarse. Dentro del método de acción, se puede tener acceso al contenido de <xref:Microsoft.AspNetCore.Http.IFormFile> como <xref:System.IO.Stream>. Además del sistema de archivos local, los archivos se pueden guardar en un recurso compartido de red o en un servicio de almacenamiento de archivos, como [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Para ver otro ejemplo que recorre en bucle varios archivos para cargar y usa nombres de archivo seguros, consulte *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* en la aplicación de ejemplo.

> [!WARNING]
> [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) arroja una <xref:System.IO.IOException> si se crean más de 65 535 archivos sin eliminar los archivos temporales anteriores. El límite de 65 535 archivos es un límite por servidor. Para más información sobre este límite en el sistema operativo Windows, consulte las notas en los temas siguientes:
>
> * [Función GetTempFileNameA](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Carga de archivos pequeños con enlace de modelos almacenado en búfer a una base de datos

Para almacenar datos de archivo binario en una base de datos con [Entity Framework](/ef/core/index), defina una propiedad de matriz <xref:System.Byte> en la entidad:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Especifique una propiedad de modelo de página para la clase que incluya un <xref:Microsoft.AspNetCore.Http.IFormFile>:

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
> <xref:Microsoft.AspNetCore.Http.IFormFile> se puede usar directamente como un parámetro de método de acción o como una propiedad de modelo enlazado. En el ejemplo anterior se utiliza una propiedad de modelo enlazado.

`FileUpload`Se utiliza en el Razor formulario de páginas:

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

Cuando el formulario se publique en el servidor, copie el <xref:Microsoft.AspNetCore.Http.IFormFile> en un flujo y guárdelo como matriz de bytes en la base de datos. En el ejemplo siguiente, `_dbContext` almacena el contexto de base de datos de la aplicación:

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

El ejemplo anterior es similar a un escenario que se muestra en la aplicación de ejemplo:

* *Pages/BufferedSingleFileUploadDb.cshtml*
* *Pages/BufferedSingleFileUploadDb.cshtml.cs*

> [!WARNING]
> Tenga cuidado al almacenar los datos binarios en bases de datos relacionales, ya que esto puede repercutir adversamente en el rendimiento.
>
> No se base ni confíe en la propiedad `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> sin validarla. La propiedad `FileName` solo debe usarse para fines de presentación y solo después de la codificación HTML.
>
> Los ejemplos proporcionados no tienen en cuenta las consideraciones de seguridad. Se proporciona información adicional en las secciones siguientes y en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Consideraciones sobre la seguridad](#security-considerations)
> * [Validación](#validation)

### <a name="upload-large-files-with-streaming"></a>Carga de archivos de gran tamaño con streaming

En el ejemplo siguiente se muestra cómo usar JavaScript para transmitir un archivo a una acción de controlador. El token de antifalsificación del archivo se genera por medio de un atributo de filtro personalizado y se pasa a los encabezados HTTP del cliente, en lugar de en el cuerpo de la solicitud. Dado que el método de acción procesa los datos cargados directamente, el enlace de modelos del formulario se deshabilita por otro filtro personalizado. Dentro de la acción, el contenido del formulario se lee usando un `MultipartReader` (que lee cada `MultipartSection` individual), de forma que el archivo se procesa o el contenido se almacena, según corresponda. Una vez leídas las secciones de varias partes, la acción realiza su propio enlace de modelos.

La respuesta de la página inicial carga el formulario y guarda un token de antifalsificación en una cookie (a través del atributo `GenerateAntiforgeryTokenCookieAttribute`). El atributo usa la [compatibilidad de antifalsificación](xref:security/anti-request-forgery) integrada de ASP.NET Core para establecer una cookie con un token de solicitud:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

El `DisableFormValueModelBindingAttribute` se usa para deshabilitar el enlace de modelos:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

En la aplicación de ejemplo, `GenerateAntiforgeryTokenCookieAttribute` y `DisableFormValueModelBindingAttribute` se aplican como filtros a los modelos de aplicación de página de `/StreamedSingleFileUploadDb` y `/StreamedSingleFileUploadPhysical` en `Startup.ConfigureServices` mediante [ Razor las convenciones de páginas](xref:razor-pages/razor-pages-conventions):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

Dado que el enlace de modelos no lee el formulario, los parámetros enlazados desde el formulario no se enlazan (la consulta, la ruta y el encabezado siguen funcionando). El método de acción funciona directamente con la propiedad `Request`. Se usa un elemento `MultipartReader` para leer cada sección. Los datos de clave-valor se almacenan en un `KeyValueAccumulator`. Una vez leídas las secciones de varias partes, el contenido del `KeyValueAccumulator` se usa para enlazar los datos del formulario a un tipo de modelo.

El método `StreamingController.UploadDatabase` completo para streaming a una base de datos con EF Core:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

El método `StreamingController.UploadPhysical` completo para streaming a una ubicación física:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

En la aplicación de ejemplo, las comprobaciones de validación las controla `FileHelpers.ProcessStreamedFile`.

## <a name="validation"></a>Validación

La clase `FileHelpers` de la aplicación de ejemplo muestra varias comprobaciones de cargas de archivos de streaming y <xref:Microsoft.AspNetCore.Http.IFormFile> almacenado en búfer. Para procesar cargas de archivos almacenadas en búfer de <xref:Microsoft.AspNetCore.Http.IFormFile> en la aplicación de ejemplo, consulte el método `ProcessFormFile` en el archivo *Utilities/FileHelpers.cs*. Para procesar archivos de streaming, consulte el método `ProcessStreamedFile` en el mismo archivo.

> [!WARNING]
> Los métodos de procesamiento de validación mostrados en la aplicación de ejemplo no examinan el contenido de los archivos cargados. En la mayoría de los escenarios de producción, se usa una API de analizador de virus/malware en el archivo antes de que el archivo esté disponible para los usuarios u otros sistemas.
>
> Aunque el ejemplo de tema proporciona un ejemplo funcional de técnicas de validación, no implemente la clase `FileHelpers` en una aplicación de producción a menos que:
>
> * Comprenda totalmente la implementación.
> * Modifique la implementación según corresponda en función del entorno y las especificaciones de la aplicación.
>
> **No implemente nunca de manera indiscriminada el código de seguridad en una aplicación sin abordar estos requisitos.**

### <a name="content-validation"></a>Validación del contenido

**Use una API de detección de virus/malware de terceros en el contenido cargado.**

El análisis de archivos exige recursos del servidor en escenarios de gran volumen. Si disminuye el rendimiento de procesamiento de solicitudes debido al análisis de archivos, considere la posibilidad de descargar el trabajo de análisis a un [servicio en segundo plano](xref:fundamentals/host/hosted-services), posiblemente un servicio que se ejecute en otro servidor desde el servidor de la aplicación. Habitualmente, los archivos cargados se almacenan en un área en cuarentena hasta que el programa de detección de virus en segundo plano los revisa. Cuando se pasa un archivo, se mueve a la ubicación de almacenamiento de archivos habitual. Por lo general, estos pasos se realizan junto con un registro de base de datos que indica el estado de análisis de un archivo. Mediante el uso de este enfoque, la aplicación y el servidor de aplicaciones permanecen centrados en responder a las solicitudes.

### <a name="file-extension-validation"></a>Validación de la extensión del archivo

La extensión del archivo cargado debe comprobarse con una lista de extensiones permitidas. Por ejemplo:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Validación de firma del archivo

La firma de un archivo viene determinada por los primeros bytes al principio de un archivo. Estos bytes se pueden usar para indicar si la extensión coincide con el contenido del archivo. La aplicación de ejemplo comprueba las firmas de archivo de algunos tipos de archivo comunes. En el ejemplo siguiente, la firma de archivo de una imagen JPEG se comprueba con respecto al archivo:

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

Para obtener firmas de archivo adicionales, consulte la [base de datos de firmas de archivo](https://www.filesignatures.net/) y las especificaciones de archivo oficiales.

### <a name="file-name-security"></a>Seguridad de nombre de archivo

Nunca use un nombre de archivo proporcionado por el cliente para guardar un archivo en el almacenamiento físico. Cree un nombre de archivo seguro para el archivo con [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) o [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) para crear una ruta de acceso completa (incluido el nombre de archivo) para el almacenamiento temporal.

RazorHTML codifica automáticamente los valores de propiedad para la presentación. El código siguiente es seguro de usar:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Fuera de Razor , <xref:System.Net.WebUtility.HtmlEncode*> el contenido del nombre de archivo siempre procedente de la solicitud de un usuario.

Muchas implementaciones deben incluir una comprobación de que el archivo existe; de lo contrario, el archivo se sobrescribe por un archivo con el mismo nombre. Proporcione lógica adicional para satisfacer las especificaciones de la aplicación.

### <a name="size-validation"></a>Validación del tamaño

Limite el tamaño de los archivos cargados.

En la aplicación de ejemplo, el tamaño del archivo está limitado a 2 MB (se indica en bytes). El límite se proporciona a través de [Configuración](xref:fundamentals/configuration/index) del archivo *appsettings.json*:

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit` se inserta en las clases `PageModel`:

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

Cuando el tamaño de un archivo supera el límite, se rechaza el archivo:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Coincidencia del valor de atributo de nombre con el nombre del parámetro del método POST

En los que no son Razor formularios y que envían datos de formulario `FormData` directamente, el nombre especificado en el elemento del formulario o `FormData` deben coincidir con el nombre del parámetro en la acción del controlador.

En el ejemplo siguiente:

* Cuando se usa un elemento `<input>`, el atributo `name` se establece en el valor `battlePlans`:

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Cuando se usa `FormData` en JavaScript, el nombre se establece en el valor `battlePlans`:

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Use un nombre coincidente para el parámetro del método de C# (`battlePlans`):

* Para un Razor método de controlador de página de páginas denominado `Upload` :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Para un método de acción de controlador POST de MVC:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Configuración del servidor y de la aplicación

### <a name="multipart-body-length-limit"></a>Límite de longitud del cuerpo de varias partes

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> establece el límite de la longitud de cada cuerpo de varias partes. Las secciones del formulario que superan este límite inician una <xref:System.IO.InvalidDataException> cuando se analizan. El valor predeterminado es 134 217 728 (128 MB). Personalice el límite mediante el valor <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> en `Startup.ConfigureServices`:

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

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> se utiliza para establecer el <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> para una sola página o acción.

En una Razor aplicación de páginas, aplique el filtro con una [Convención](xref:razor-pages/razor-pages-conventions) en `Startup.ConfigureServices` :

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

En una Razor aplicación de páginas o en una aplicación MVC, aplique el filtro al método de acción o modelo de página:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Tamaño máximo del cuerpo de la solicitud de Kestrel

En el caso de las aplicaciones hospedadas por Kestrel, el tamaño máximo predeterminado del cuerpo de solicitud es 30 000 000 bytes, que son aproximadamente 28,6 MB. Personalice el límite con la opción [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) del servidor de Kestrel:

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

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> se usa para establecer el valor de [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) de una sola página o acción.

En una Razor aplicación de páginas, aplique el filtro con una [Convención](xref:razor-pages/razor-pages-conventions) en `Startup.ConfigureServices` :

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

En una Razor aplicación pages o MVC, aplique el filtro a la clase de controlador de páginas o al método de acción:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

`RequestSizeLimitAttribute`También se puede aplicar mediante la [`@attribute`](xref:mvc/views/razor#attribute) Razor Directiva:

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>Otros límites de Kestrel

Otros límites de Kestrel pueden aplicarse a las aplicaciones hospedadas por Kestrel:

* [Las conexiones máximas de cliente](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Tarifas de datos de solicitud y respuesta](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a>Límite de longitud del contenido de IIS

El límite predeterminado de solicitudes (`maxAllowedContentLength`) es 30 000 000 bytes, que son aproximadamente 28,6 MB. Personalice el límite en el archivo *web.config*:

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

Esto solo ocurre en IIS; este comportamiento no sucede de forma predeterminada cuando los archivos se hospedan en Kestrel. Para obtener más información, consulte [límites \<requestLimits> de solicitudes ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Las limitaciones del módulo ASP.NET Core o la presencia del módulo de filtrado de solicitudes de IIS pueden limitar las cargas a 2 GB o 4 GB. Para obtener más información, consulte el artículo que indica que [no se pueden cargar archivos con un tamaño superior a 2 GB (aspnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).

## <a name="troubleshoot"></a>Solución de problemas

Aquí incluimos algunos problemas comunes que pueden surgir al cargar archivos, así como sus posibles soluciones.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Error No encontrado al implementar en un servidor IIS

El error siguiente indica que el archivo cargado supera la longitud configurada del contenido del servidor:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Para más información sobre cómo aumentar el límite, consulte la sección [Límite de longitud del contenido de IIS](#iis-content-length-limit).

### <a name="connection-failure"></a>Error de conexión

Un error de conexión y una conexión del servidor de restablecimiento probablemente indica que el archivo cargado supera el tamaño máximo del cuerpo de la solicitud de Kestrel. Para más información, consulte la sección [Tamaño máximo del cuerpo de la solicitud de Kestrel](#kestrel-maximum-request-body-size). Los límites de conexión del cliente de Kestrel también pueden requerir ajustes.

### <a name="null-reference-exception-with-iformfile"></a>Excepción de referencia nula con IFormFile

Si el controlador acepta archivos cargados con <xref:Microsoft.AspNetCore.Http.IFormFile>, pero el valor es `null`, confirme que el formulario HTML especifica un valor `enctype` de `multipart/form-data`. Si este atributo no está establecido en el elemento `<form>`, no se llevará a cabo la carga del archivo y cualquier argumento <xref:Microsoft.AspNetCore.Http.IFormFile> enlazado será `null`. Confirme también que la [nomenclatura de la carga en los datos de formulario coincide con la nomenclatura de la aplicación](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>El flujo era demasiado largo

Los ejemplos de este tema se basan en <xref:System.IO.MemoryStream> para almacenar el contenido del archivo cargado. El límite de tamaño de `MemoryStream` es `int.MaxValue`. Si el escenario de carga de archivos de la aplicación requiere almacenar contenido de archivo superior a 50 MB, use un enfoque alternativo que no dependa de un único valor de `MemoryStream` para almacenar el contenido de un archivo cargado.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core admite la carga de uno o varios archivos mediante el enlace de modelos almacenado en búfer de archivos más pequeños y el streaming no almacenado en búfer de archivos de mayor tamaño.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Consideraciones sobre la seguridad

Tenga precaución al proporcionar a los usuarios la capacidad de cargar archivos en un servidor. Es posible que los atacantes intenten lo siguiente:

* Ejecutar ataques por [denegación de servicio](/windows-hardware/drivers/ifs/denial-of-service).
* Cargar virus o malware.
* Poner en riesgo redes y servidores de otras maneras.

Estos son algunos de los pasos de seguridad con los que se reduce la probabilidad de sufrir ataques:

* Cargue los archivos a un área de carga de archivos dedicada, preferiblemente una unidad que no sea de sistema. Una ubicación dedicada facilita la imposición de restricciones de seguridad en los archivos cargados. Deshabilite la ejecución de los permisos en la ubicación de carga de archivos.&dagger;
* Los archivos cargados **no** se deben persistir en el mismo árbol de directorio que la aplicación.&dagger;
* Use un nombre de archivo seguro determinado por la aplicación. No use un nombre de archivo proporcionado por el usuario o el nombre de archivo que no es de confianza del archivo cargado. &dagger; HTML codifica el nombre de archivo que no es de confianza al mostrarlo. Por ejemplo, registrando el nombre de archivo o mostrándose en la interfaz de usuario (la Razor salida codifica automáticamente html).
* Permita solo las extensiones de archivo aprobadas para la especificación de diseño de la aplicación.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Compruebe que las comprobaciones del lado cliente se realizan en el servidor. &dagger; Las comprobaciones del lado cliente son fáciles de eludir.
* Compruebe el tamaño de un archivo cargado. Establezca un límite de tamaño máximo para evitar cargas grandes.&dagger;
* Cuando un archivo cargado con el mismo nombre no deba sobrescribir los archivos, vuelva a comprobar el nombre de archivo en la base de datos o en el almacenamiento físico antes de cargarlo.
* **Ejecute un detector de virus o malware en el contenido cargado antes de que se almacene el archivo.**

&dagger;La aplicación de ejemplo muestra un enfoque que cumple los criterios.

> [!WARNING]
> La carga de código malintencionado en un sistema suele ser el primer paso para ejecutar código que puede:
>
> * Obtener el control completo de un sistema.
> * Sobrecargar un sistema de manera que el sistema se bloquea.
> * Poner en peligro los datos del usuario o del sistema.
> * Aplicar grafitis a una interfaz de usuario pública.
>
> Vea los siguientes recursos para más información sobre cómo reducir el área expuesta de ataques al aceptar archivos de los usuarios:
>
> * [Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carga de archivos sin restricciones)
> * [Azure Security: asegúrese de que los controles adecuados estén en vigor al aceptar archivos de usuarios](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Para más información sobre cómo implementar medidas de seguridad, incluidos ejemplos de la aplicación de ejemplo, consulte la sección [Validación](#validation).

## <a name="storage-scenarios"></a>Escenarios de almacenamiento

Las opciones de almacenamiento comunes para los archivos incluyen:

* Base de datos

  * En el caso de cargas de archivos pequeñas, una base de datos suele ser más rápida que las opciones de almacenamiento físico (sistema de archivos o recurso compartido de red).
  * Una base de datos suele ser más conveniente que las opciones de almacenamiento físico, ya que la recuperación de un registro de base de datos para los datos de usuario puede proporcionar el contenido del archivo (por ejemplo, una imagen de avatar).
  * Una base de datos puede ser más económica que usar un servicio de almacenamiento de datos.

* Almacenamiento físico (sistema de archivos o recurso compartido de red)

  * Para cargas de archivos de gran tamaño:
    * Los límites de base de datos pueden restringir el tamaño de la carga.
    * El almacenamiento físico suele ser menos económico que el almacenamiento en una base de datos.
  * El almacenamiento físico puede ser más económico que usar un servicio de almacenamiento de datos.
  * El proceso de la aplicación debe tener permisos de lectura y escritura en la ubicación de almacenamiento. **Nunca conceda el permiso de ejecución.**

* Servicio de almacenamiento de datos (por ejemplo, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))

  * Los servicios suelen ofrecer una escalabilidad y resistencia mejoradas sobre las soluciones locales que normalmente están sujetas a únicos puntos de error.
  * Los servicios pueden tener un costo menor en escenarios de infraestructura de almacenamiento de gran tamaño.

  Para obtener más información, vea [Inicio rápido: usar .net para crear un BLOB en el almacenamiento de objetos](/azure/storage/blobs/storage-quickstart-blobs-dotnet). En el tema se muestra <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, pero se puede usar <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> para guardar un <xref:System.IO.FileStream> en el almacenamiento de blobs cuando se trabaja con un <xref:System.IO.Stream>.

## <a name="file-upload-scenarios"></a>Escenarios de carga de archivos

Dos enfoques generales para cargar archivos son el almacenamiento en búfer y el streaming.

**de respuesta**

El archivo completo se lee en un <xref:Microsoft.AspNetCore.Http.IFormFile>, que es una representación de C# del archivo que se usa para procesar o guardar el archivo.

Los recursos (disco, memoria) que se usan en las cargas de archivos dependen de la cantidad y del tamaño de las cargas de archivos que se realizan simultáneamente. Si una aplicación intenta almacenar demasiadas cargas en el búfer, el sitio se bloquea cuando se queda sin memoria o sin espacio en disco. Si el tamaño o la frecuencia de las cargas de archivos está agotando los recursos de la aplicación, use el streaming.

> [!NOTE]
> Cualquier archivo almacenado en búfer único que supere los 64 KB se mueve de la memoria a un archivo temporal en el disco.

En las secciones siguientes de este tema se habla del almacenamiento en búfer de archivos pequeños:

* [Almacenamiento físico](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Base de datos](#upload-small-files-with-buffered-model-binding-to-a-database)

**Streaming**

El archivo se recibe de una solicitud de varias partes y lo procesa o guarda directamente la aplicación. El streaming no mejora considerablemente el rendimiento. El streaming reduce las demandas de memoria o espacio en disco cuando se cargan archivos.

El streaming de archivos grandes se describe en la sección [Carga de archivos de gran tamaño con streaming](#upload-large-files-with-streaming).

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Carga de archivos pequeños con enlace de modelos almacenado en búfer al almacenamiento físico

Para cargar archivos pequeños, se puede usar un formulario de varias partes o construir una solicitud POST con JavaScript.

En el ejemplo siguiente se muestra el uso de un Razor formulario de páginas para cargar un único archivo (*pages/BufferedSingleFileUploadPhysical. cshtml* en la aplicación de ejemplo):

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

El ejemplo siguiente es análogo al ejemplo anterior, salvo en que:

* ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) de JavaScript se usa para enviar los datos del formulario.
* No hay ninguna validación.

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

Para realizar la solicitud POST en JavaScript para los clientes que [no admiten Fetch API](https://caniuse.com/#feat=fetch), use uno de estos enfoques:

* Use un Fetch Polyfill (por ejemplo, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).
* Mediante `XMLHttpRequest`. Por ejemplo:

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

Para admitir las cargas de archivos, los formularios HTML deben especificar un tipo de codificación (`enctype`) de `multipart/form-data`.

Para que un elemento de entrada `files` admita al carga de varios archivos, proporcione el atributo `multiple` en el elemento `<input>`:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Es posible acceder a archivos individuales cargados en el servidor a través del [enlace de modelos](xref:mvc/models/model-binding) mediante <xref:Microsoft.AspNetCore.Http.IFormFile>. La aplicación de ejemplo muestra varias cargas de archivos almacenados en búfer para escenarios de almacenamiento físico y base de datos.

<a name="filename2"></a>

> [!WARNING]
> **No** utilice la propiedad `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> para usos distintos a la presentación y el registro. Para fines de presentación y registro, codifique el nombre de archivo en HTML. Un atacante puede proporcionar un nombre de archivo malintencionado, incluidas rutas de acceso completas o relativas. Las aplicaciones deben:
>
> * Quitar la ruta de acceso del nombre de archivo proporcionado por el usuario.
> * Guardar el nombre de archivo codificado en HTML, sin la ruta de acceso para la interfaz de usuario o el registro.
> * Generar un nombre de archivo aleatorio nuevo para el almacenamiento.
>
> En el código siguiente se quita la ruta de acceso del nombre del archivo:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Los ejemplos proporcionados hasta ahora no tienen en cuenta las consideraciones de seguridad. Se proporciona información adicional en las secciones siguientes y en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Consideraciones sobre la seguridad](#security-considerations)
> * [Validación](#validation)

Al cargar archivos mediante el enlace de modelos y <xref:Microsoft.AspNetCore.Http.IFormFile>, el método de acción puede aceptar:

* Un <xref:Microsoft.AspNetCore.Http.IFormFile> único.
* Cualquiera de las colecciones siguientes que representan varios archivos:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Lista](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> El enlace coincide con los archivos de formulario por nombre. Por ejemplo, el valor `name` HTML en `<input type="file" name="formFile">` debe coincidir con la propiedad/el parámetro enlazado de C# (`FormFile`). Para más información, consulte la sección [Coincidencia del valor de atributo de nombre con el nombre del parámetro del método POST](#match-name-attribute-value-to-parameter-name-of-post-method).

En el ejemplo siguiente:

* Recorre en bucle uno o más archivos cargados.
* Usa [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) para devolver una ruta de acceso completa de un archivo, incluido el nombre de archivo. 
* Guarda los archivos en el sistema de archivos local con un nombre de archivo generado por la aplicación.
* Devuelve el número total y el tamaño de los archivos cargados.

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

Use `Path.GetRandomFileName` para generar un nombre de archivo sin una ruta de acceso. En el ejemplo siguiente, la ruta de acceso se obtiene de la configuración:

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

La ruta de acceso pasada a <xref:System.IO.FileStream> *debe* incluir el nombre de archivo. Si no se proporciona el nombre de archivo, se produce una <xref:System.UnauthorizedAccessException> en tiempo de ejecución.

Los archivos que se cargan usando la técnica <xref:Microsoft.AspNetCore.Http.IFormFile> se almacenan en búfer en memoria o en disco en el servidor web antes de procesarse. Dentro del método de acción, se puede tener acceso al contenido de <xref:Microsoft.AspNetCore.Http.IFormFile> como <xref:System.IO.Stream>. Además del sistema de archivos local, los archivos se pueden guardar en un recurso compartido de red o en un servicio de almacenamiento de archivos, como [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Para ver otro ejemplo que recorre en bucle varios archivos para cargar y usa nombres de archivo seguros, consulte *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* en la aplicación de ejemplo.

> [!WARNING]
> [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) arroja una <xref:System.IO.IOException> si se crean más de 65 535 archivos sin eliminar los archivos temporales anteriores. El límite de 65 535 archivos es un límite por servidor. Para más información sobre este límite en el sistema operativo Windows, consulte las notas en los temas siguientes:
>
> * [Función GetTempFileNameA](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Carga de archivos pequeños con enlace de modelos almacenado en búfer a una base de datos

Para almacenar datos de archivo binario en una base de datos con [Entity Framework](/ef/core/index), defina una propiedad de matriz <xref:System.Byte> en la entidad:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Especifique una propiedad de modelo de página para la clase que incluya un <xref:Microsoft.AspNetCore.Http.IFormFile>:

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
> <xref:Microsoft.AspNetCore.Http.IFormFile> se puede usar directamente como un parámetro de método de acción o como una propiedad de modelo enlazado. En el ejemplo anterior se utiliza una propiedad de modelo enlazado.

`FileUpload`Se utiliza en el Razor formulario de páginas:

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

Cuando el formulario se publique en el servidor, copie el <xref:Microsoft.AspNetCore.Http.IFormFile> en un flujo y guárdelo como matriz de bytes en la base de datos. En el ejemplo siguiente, `_dbContext` almacena el contexto de base de datos de la aplicación:

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

El ejemplo anterior es similar a un escenario que se muestra en la aplicación de ejemplo:

* *Pages/BufferedSingleFileUploadDb.cshtml*
* *Pages/BufferedSingleFileUploadDb.cshtml.cs*

> [!WARNING]
> Tenga cuidado al almacenar los datos binarios en bases de datos relacionales, ya que esto puede repercutir adversamente en el rendimiento.
>
> No se base ni confíe en la propiedad `FileName` de <xref:Microsoft.AspNetCore.Http.IFormFile> sin validarla. La propiedad `FileName` solo debe usarse para fines de presentación y solo después de la codificación HTML.
>
> Los ejemplos proporcionados no tienen en cuenta las consideraciones de seguridad. Se proporciona información adicional en las secciones siguientes y en la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Consideraciones sobre la seguridad](#security-considerations)
> * [Validación](#validation)

### <a name="upload-large-files-with-streaming"></a>Carga de archivos de gran tamaño con streaming

En el ejemplo siguiente se muestra cómo usar JavaScript para transmitir un archivo a una acción de controlador. El token de antifalsificación del archivo se genera por medio de un atributo de filtro personalizado y se pasa a los encabezados HTTP del cliente, en lugar de en el cuerpo de la solicitud. Dado que el método de acción procesa los datos cargados directamente, el enlace de modelos del formulario se deshabilita por otro filtro personalizado. Dentro de la acción, el contenido del formulario se lee usando un `MultipartReader` (que lee cada `MultipartSection` individual), de forma que el archivo se procesa o el contenido se almacena, según corresponda. Una vez leídas las secciones de varias partes, la acción realiza su propio enlace de modelos.

La respuesta de la página inicial carga el formulario y guarda un token de antifalsificación en una cookie (a través del atributo `GenerateAntiforgeryTokenCookieAttribute`). El atributo usa la [compatibilidad de antifalsificación](xref:security/anti-request-forgery) integrada de ASP.NET Core para establecer una cookie con un token de solicitud:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

El `DisableFormValueModelBindingAttribute` se usa para deshabilitar el enlace de modelos:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

En la aplicación de ejemplo, `GenerateAntiforgeryTokenCookieAttribute` y `DisableFormValueModelBindingAttribute` se aplican como filtros a los modelos de aplicación de página de `/StreamedSingleFileUploadDb` y `/StreamedSingleFileUploadPhysical` en `Startup.ConfigureServices` mediante [ Razor las convenciones de páginas](xref:razor-pages/razor-pages-conventions):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

Dado que el enlace de modelos no lee el formulario, los parámetros enlazados desde el formulario no se enlazan (la consulta, la ruta y el encabezado siguen funcionando). El método de acción funciona directamente con la propiedad `Request`. Se usa un elemento `MultipartReader` para leer cada sección. Los datos de clave-valor se almacenan en un `KeyValueAccumulator`. Una vez leídas las secciones de varias partes, el contenido del `KeyValueAccumulator` se usa para enlazar los datos del formulario a un tipo de modelo.

El método `StreamingController.UploadDatabase` completo para streaming a una base de datos con EF Core:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

El método `StreamingController.UploadPhysical` completo para streaming a una ubicación física:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

En la aplicación de ejemplo, las comprobaciones de validación las controla `FileHelpers.ProcessStreamedFile`.

## <a name="validation"></a>Validación

La clase `FileHelpers` de la aplicación de ejemplo muestra varias comprobaciones de cargas de archivos de streaming y <xref:Microsoft.AspNetCore.Http.IFormFile> almacenado en búfer. Para procesar cargas de archivos almacenadas en búfer de <xref:Microsoft.AspNetCore.Http.IFormFile> en la aplicación de ejemplo, consulte el método `ProcessFormFile` en el archivo *Utilities/FileHelpers.cs*. Para procesar archivos de streaming, consulte el método `ProcessStreamedFile` en el mismo archivo.

> [!WARNING]
> Los métodos de procesamiento de validación mostrados en la aplicación de ejemplo no examinan el contenido de los archivos cargados. En la mayoría de los escenarios de producción, se usa una API de analizador de virus/malware en el archivo antes de que el archivo esté disponible para los usuarios u otros sistemas.
>
> Aunque el ejemplo de tema proporciona un ejemplo funcional de técnicas de validación, no implemente la clase `FileHelpers` en una aplicación de producción a menos que:
>
> * Comprenda totalmente la implementación.
> * Modifique la implementación según corresponda en función del entorno y las especificaciones de la aplicación.
>
> **No implemente nunca de manera indiscriminada el código de seguridad en una aplicación sin abordar estos requisitos.**

### <a name="content-validation"></a>Validación del contenido

**Use una API de detección de virus/malware de terceros en el contenido cargado.**

El análisis de archivos exige recursos del servidor en escenarios de gran volumen. Si disminuye el rendimiento de procesamiento de solicitudes debido al análisis de archivos, considere la posibilidad de descargar el trabajo de análisis a un [servicio en segundo plano](xref:fundamentals/host/hosted-services), posiblemente un servicio que se ejecute en otro servidor desde el servidor de la aplicación. Habitualmente, los archivos cargados se almacenan en un área en cuarentena hasta que el programa de detección de virus en segundo plano los revisa. Cuando se pasa un archivo, se mueve a la ubicación de almacenamiento de archivos habitual. Por lo general, estos pasos se realizan junto con un registro de base de datos que indica el estado de análisis de un archivo. Mediante el uso de este enfoque, la aplicación y el servidor de aplicaciones permanecen centrados en responder a las solicitudes.

### <a name="file-extension-validation"></a>Validación de la extensión del archivo

La extensión del archivo cargado debe comprobarse con una lista de extensiones permitidas. Por ejemplo:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Validación de firma del archivo

La firma de un archivo viene determinada por los primeros bytes al principio de un archivo. Estos bytes se pueden usar para indicar si la extensión coincide con el contenido del archivo. La aplicación de ejemplo comprueba las firmas de archivo de algunos tipos de archivo comunes. En el ejemplo siguiente, la firma de archivo de una imagen JPEG se comprueba con respecto al archivo:

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

Para obtener firmas de archivo adicionales, consulte la [base de datos de firmas de archivo](https://www.filesignatures.net/) y las especificaciones de archivo oficiales.

### <a name="file-name-security"></a>Seguridad de nombre de archivo

Nunca use un nombre de archivo proporcionado por el cliente para guardar un archivo en el almacenamiento físico. Cree un nombre de archivo seguro para el archivo con [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) o [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) para crear una ruta de acceso completa (incluido el nombre de archivo) para el almacenamiento temporal.

RazorHTML codifica automáticamente los valores de propiedad para la presentación. El código siguiente es seguro de usar:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Fuera de Razor , <xref:System.Net.WebUtility.HtmlEncode*> el contenido del nombre de archivo siempre procedente de la solicitud de un usuario.

Muchas implementaciones deben incluir una comprobación de que el archivo existe; de lo contrario, el archivo se sobrescribe por un archivo con el mismo nombre. Proporcione lógica adicional para satisfacer las especificaciones de la aplicación.

### <a name="size-validation"></a>Validación del tamaño

Limite el tamaño de los archivos cargados.

En la aplicación de ejemplo, el tamaño del archivo está limitado a 2 MB (se indica en bytes). El límite se proporciona a través de [Configuración](xref:fundamentals/configuration/index) del archivo *appsettings.json*:

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit` se inserta en las clases `PageModel`:

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

Cuando el tamaño de un archivo supera el límite, se rechaza el archivo:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Coincidencia del valor de atributo de nombre con el nombre del parámetro del método POST

En los que no son Razor formularios y que envían datos de formulario `FormData` directamente, el nombre especificado en el elemento del formulario o `FormData` deben coincidir con el nombre del parámetro en la acción del controlador.

En el ejemplo siguiente:

* Cuando se usa un elemento `<input>`, el atributo `name` se establece en el valor `battlePlans`:

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Cuando se usa `FormData` en JavaScript, el nombre se establece en el valor `battlePlans`:

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Use un nombre coincidente para el parámetro del método de C# (`battlePlans`):

* Para un Razor método de controlador de página de páginas denominado `Upload` :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Para un método de acción de controlador POST de MVC:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Configuración del servidor y de la aplicación

### <a name="multipart-body-length-limit"></a>Límite de longitud del cuerpo de varias partes

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> establece el límite de la longitud de cada cuerpo de varias partes. Las secciones del formulario que superan este límite inician una <xref:System.IO.InvalidDataException> cuando se analizan. El valor predeterminado es 134 217 728 (128 MB). Personalice el límite mediante el valor <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> en `Startup.ConfigureServices`:

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

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> se utiliza para establecer el <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> para una sola página o acción.

En una Razor aplicación de páginas, aplique el filtro con una [Convención](xref:razor-pages/razor-pages-conventions) en `Startup.ConfigureServices` :

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

En una Razor aplicación de páginas o en una aplicación MVC, aplique el filtro al método de acción o modelo de página:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Tamaño máximo del cuerpo de la solicitud de Kestrel

En el caso de las aplicaciones hospedadas por Kestrel, el tamaño máximo predeterminado del cuerpo de solicitud es 30 000 000 bytes, que son aproximadamente 28,6 MB. Personalice el límite con la opción [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) del servidor de Kestrel:

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

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> se usa para establecer el valor de [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) de una sola página o acción.

En una Razor aplicación de páginas, aplique el filtro con una [Convención](xref:razor-pages/razor-pages-conventions) en `Startup.ConfigureServices` :

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

En una Razor aplicación pages o MVC, aplique el filtro a la clase de controlador de páginas o al método de acción:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a>Otros límites de Kestrel

Otros límites de Kestrel pueden aplicarse a las aplicaciones hospedadas por Kestrel:

* [Las conexiones máximas de cliente](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Tarifas de datos de solicitud y respuesta](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a>Límite de longitud del contenido de IIS

El límite predeterminado de solicitudes (`maxAllowedContentLength`) es 30 000 000 bytes, que son aproximadamente 28,6 MB. Personalice el límite en el archivo *web.config*:

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

Esto solo ocurre en IIS; este comportamiento no sucede de forma predeterminada cuando los archivos se hospedan en Kestrel. Para obtener más información, consulte [límites \<requestLimits> de solicitudes ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Las limitaciones del módulo ASP.NET Core o la presencia del módulo de filtrado de solicitudes de IIS pueden limitar las cargas a 2 GB o 4 GB. Para obtener más información, consulte el artículo que indica que [no se pueden cargar archivos con un tamaño superior a 2 GB (aspnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).

## <a name="troubleshoot"></a>Solución de problemas

Aquí incluimos algunos problemas comunes que pueden surgir al cargar archivos, así como sus posibles soluciones.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Error No encontrado al implementar en un servidor IIS

El error siguiente indica que el archivo cargado supera la longitud configurada del contenido del servidor:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Para más información sobre cómo aumentar el límite, consulte la sección [Límite de longitud del contenido de IIS](#iis-content-length-limit).

### <a name="connection-failure"></a>Error de conexión

Un error de conexión y una conexión del servidor de restablecimiento probablemente indica que el archivo cargado supera el tamaño máximo del cuerpo de la solicitud de Kestrel. Para más información, consulte la sección [Tamaño máximo del cuerpo de la solicitud de Kestrel](#kestrel-maximum-request-body-size). Los límites de conexión del cliente de Kestrel también pueden requerir ajustes.

### <a name="null-reference-exception-with-iformfile"></a>Excepción de referencia nula con IFormFile

Si el controlador acepta archivos cargados con <xref:Microsoft.AspNetCore.Http.IFormFile>, pero el valor es `null`, confirme que el formulario HTML especifica un valor `enctype` de `multipart/form-data`. Si este atributo no está establecido en el elemento `<form>`, no se llevará a cabo la carga del archivo y cualquier argumento <xref:Microsoft.AspNetCore.Http.IFormFile> enlazado será `null`. Confirme también que la [nomenclatura de la carga en los datos de formulario coincide con la nomenclatura de la aplicación](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>El flujo era demasiado largo

Los ejemplos de este tema se basan en <xref:System.IO.MemoryStream> para almacenar el contenido del archivo cargado. El límite de tamaño de `MemoryStream` es `int.MaxValue`. Si el escenario de carga de archivos de la aplicación requiere almacenar contenido de archivo superior a 50 MB, use un enfoque alternativo que no dependa de un único valor de `MemoryStream` para almacenar el contenido de un archivo cargado.

::: moniker-end


## <a name="additional-resources"></a>Recursos adicionales

* [Agotamiento de la solicitud de conexión HTTP](xref:fundamentals/servers/kestrel#http11-request-draining)
* [Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carga de archivos sin restricciones)
* [Seguridad de Azure: marco de seguridad: validación de entrada | Mitigaciones](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [Patrones de diseño en la nube de Azure: patrón de clave valet](/azure/architecture/patterns/valet-key)
