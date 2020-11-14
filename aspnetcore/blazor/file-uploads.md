---
title: 'Cargas de archivos de ASP.NET Core Blazor'
author: guardrex
description: 'Información sobre cómo cargar archivos en Blazor con el componente InputFile.'
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
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
ms.date: 10/27/2020
uid: blazor/file-uploads
ms.openlocfilehash: c0806c3a68a4d9e698925f6ec955dd2f53d7818f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056132"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="21087-103">Cargas de archivos de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="21087-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="21087-104">De [Daniel Roth](https://github.com/danroth27) y [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="21087-104">By [Daniel Roth](https://github.com/danroth27) and [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="21087-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="21087-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="21087-106">Use el componente `InputFile` para leer los datos del archivo de explorador en código .NET, incluidos los relativos a las cargas de archivos.</span><span class="sxs-lookup"><span data-stu-id="21087-106">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span>

> [!WARNING]
> <span data-ttu-id="21087-107">Siga siempre los procedimientos recomendados de seguridad de carga de archivos.</span><span class="sxs-lookup"><span data-stu-id="21087-107">Always follow file upload security best practices.</span></span> <span data-ttu-id="21087-108">Para obtener más información, vea <xref:mvc/models/file-uploads#security-considerations>.</span><span class="sxs-lookup"><span data-stu-id="21087-108">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

## <a name="inputfile-component"></a><span data-ttu-id="21087-109">Componente de `InputFile`</span><span class="sxs-lookup"><span data-stu-id="21087-109">`InputFile` component</span></span>

<span data-ttu-id="21087-110">El componente `InputFile` se representa como una entrada HTML de tipo `file`.</span><span class="sxs-lookup"><span data-stu-id="21087-110">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="21087-111">De forma predeterminada, los usuarios seleccionan archivos individuales.</span><span class="sxs-lookup"><span data-stu-id="21087-111">By default, the user selects single files.</span></span> <span data-ttu-id="21087-112">Agregue el atributo `multiple` para que sea posible cargar varios archivos a la vez.</span><span class="sxs-lookup"><span data-stu-id="21087-112">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="21087-113">Cuando un usuario selecciona uno o varios archivos, el componente `InputFile` activa un evento `OnChange` y pasa un elemento `InputFileChangeEventArgs` que proporciona acceso a la lista de archivos seleccionados y detalles de cada archivo.</span><span class="sxs-lookup"><span data-stu-id="21087-113">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="21087-114">Para leer datos de un archivo seleccionado por el usuario:</span><span class="sxs-lookup"><span data-stu-id="21087-114">To read data from a user-selected file:</span></span>

* <span data-ttu-id="21087-115">Llame a `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` en el archivo y lea desde el flujo devuelto.</span><span class="sxs-lookup"><span data-stu-id="21087-115">Call `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="21087-116">Para obtener más información, vea la sección [Flujos de archivos](#file-streams).</span><span class="sxs-lookup"><span data-stu-id="21087-116">For more information, see the [File streams](#file-streams) section.</span></span>
* <span data-ttu-id="21087-117">El valor <xref:System.IO.Stream> devuelto por `OpenReadStream` aplica un tamaño máximo en bytes del `Stream` que se lee.</span><span class="sxs-lookup"><span data-stu-id="21087-117">The <xref:System.IO.Stream> returned by `OpenReadStream` enforces a maximum size in bytes of the `Stream` being read.</span></span> <span data-ttu-id="21087-118">De forma predeterminada, solo se pueden leer los archivos con un tamaño inferior a 524 288 KB (512 KB) antes de que lecturas adicionales generen una excepción.</span><span class="sxs-lookup"><span data-stu-id="21087-118">By default, only files smaller than 524,288 KB (512 KB) in size are allowed to be read before any further reads would result in an exception.</span></span> <span data-ttu-id="21087-119">Este límite está presente para evitar que los desarrolladores lean accidentalmente archivos de gran tamaño en la memoria.</span><span class="sxs-lookup"><span data-stu-id="21087-119">This limit is present to prevent developers from accidentally reading large files in to memory.</span></span> <span data-ttu-id="21087-120">El parámetro `maxAllowedSize` de `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` se puede utilizar para especificar un tamaño mayor si es necesario.</span><span class="sxs-lookup"><span data-stu-id="21087-120">The `maxAllowedSize` parameter on `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` can be used to specify a larger size if required.</span></span>
* <span data-ttu-id="21087-121">Evite leer el flujo de archivos entrantes directamente en la memoria.</span><span class="sxs-lookup"><span data-stu-id="21087-121">Avoid reading the incoming file stream directly into memory.</span></span> <span data-ttu-id="21087-122">Por ejemplo, no copie los bytes de un archivo en un elemento <xref:System.IO.MemoryStream> o lo lea como una matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="21087-122">For example, don't copy file bytes into a <xref:System.IO.MemoryStream> or read as a byte array.</span></span> <span data-ttu-id="21087-123">Estos enfoques pueden dar lugar a problemas de rendimiento y seguridad, sobre todo en Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="21087-123">These approaches can result in performance and security problems, especially in Blazor Server.</span></span> <span data-ttu-id="21087-124">En su lugar, considere la posibilidad de copiar bytes de un archivo en un almacén externo, como un blob o un archivo en disco.</span><span class="sxs-lookup"><span data-stu-id="21087-124">Instead, consider copying file bytes to an external store, such as a a blob or a file on disk.</span></span>

<span data-ttu-id="21087-125">Un componente que recibe un archivo de imagen puede llamar al método de conveniencia `RequestImageFileAsync` en el archivo para cambiar el tamaño de los datos de imagen en el runtime de JavaScript del explorador antes de que la imagen se transmita a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="21087-125">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="21087-126">En el ejemplo siguiente se muestra la carga de varios archivos de imagen en un componente.</span><span class="sxs-lookup"><span data-stu-id="21087-126">The following example demonstrates multiple image file upload in a component.</span></span> <span data-ttu-id="21087-127">`InputFileChangeEventArgs.GetMultipleFiles` permite leer varios archivos.</span><span class="sxs-lookup"><span data-stu-id="21087-127">`InputFileChangeEventArgs.GetMultipleFiles` allows reading multiple files.</span></span> <span data-ttu-id="21087-128">Especifique el número máximo de archivos que espera leer para evitar que un usuario malintencionado cargue más de los que la aplicación espera.</span><span class="sxs-lookup"><span data-stu-id="21087-128">Specify the maximum number of files you expect to read to prevent a malicious user from uploading a larger number of files than the app expects.</span></span> <span data-ttu-id="21087-129">`InputFileChangeEventArgs.File` permite leer el primer y único archivo si la carga de archivos no admite varios.</span><span class="sxs-lookup"><span data-stu-id="21087-129">`InputFileChangeEventArgs.File` allows reading the first and only file if the file upload does not support multiple files.</span></span>

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h4>Images</h4>

    <div class="card" style="width:30rem;">
        <div class="card-body">
            @foreach (var imageDataUrl in imageDataUrls)
            {
                <img class="rounded m-1" src="@imageDataUrl" />
            }
        </div>
    </div>
}

@code {
    IList<string> imageDataUrls = new List<string>();

    private async Task OnInputFileChange(InputFileChangeEventArgs e)
    {
        var maxAllowedFiles = 3;
        var format = "image/png";

        foreach (var imageFile in e.GetMultipleFiles(maxAllowedFiles))
        {
            var resizedImageFile = await imageFile.RequestImageFileAsync(format, 
                100, 100);
            var buffer = new byte[resizedImageFile.Size];
            await resizedImageFile.OpenReadStream().ReadAsync(buffer);
            var imageDataUrl = 
                $"data:{format};base64,{Convert.ToBase64String(buffer)}";
            imageDataUrls.Add(imageDataUrl);
        }
    }
}
```

<span data-ttu-id="21087-130">`IBrowserFile` devuelve metadatos [que expone el explorador](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) como propiedades.</span><span class="sxs-lookup"><span data-stu-id="21087-130">`IBrowserFile` returns metadata [exposed by the browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) as properties.</span></span> <span data-ttu-id="21087-131">Estos metadatos pueden ser útiles para la validación preliminar.</span><span class="sxs-lookup"><span data-stu-id="21087-131">This metadata can be useful to preliminary validation.</span></span> <span data-ttu-id="21087-132">Por ejemplo, vea los [componentes de ejemplo `FileUpload.razor` y `FilePreview.razor`](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="21087-132">For example, see the [`FileUpload.razor` and `FilePreview.razor` sample components](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span></span>

## <a name="file-streams"></a><span data-ttu-id="21087-133">Flujos de archivos</span><span class="sxs-lookup"><span data-stu-id="21087-133">File streams</span></span>

<span data-ttu-id="21087-134">En una aplicación Blazor WebAssembly, los datos se transmiten directamente al código .NET dentro del explorador.</span><span class="sxs-lookup"><span data-stu-id="21087-134">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span>

<span data-ttu-id="21087-135">En una aplicación Blazor Server, los datos de archivo se transmiten en la conexión SignalR al código .NET en el servidor a medida que el archivo se lee desde el flujo.</span><span class="sxs-lookup"><span data-stu-id="21087-135">In a Blazor Server app, the file data is streamed over the SignalR connection into .NET code on the server as the file is read from the stream.</span></span> <span data-ttu-id="21087-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) permite configurar las características de carga de archivos para Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="21087-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) allows configuring file upload characteristics for Blazor Server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="21087-137">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="21087-137">Additional resources</span></span>

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
