---
title: Cargas de archivos de ASP.NET Core Blazor
author: guardrex
description: Información sobre cómo cargar archivos en Blazor con el componente InputFile.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
no-loc:
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
uid: blazor/file-uploads
ms.openlocfilehash: de4654f2efc401143e066628b096052efa65d7a0
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722960"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="5fabb-103">Cargas de archivos de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="5fabb-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="5fabb-104">Por [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="5fabb-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="5fabb-105">Use el componente `InputFile` para leer los datos del archivo de explorador en código .NET, incluidos los relativos a las cargas de archivos.</span><span class="sxs-lookup"><span data-stu-id="5fabb-105">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span> <span data-ttu-id="5fabb-106">El componente `InputFile` se representa como una entrada HTML de tipo `file`.</span><span class="sxs-lookup"><span data-stu-id="5fabb-106">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="5fabb-107">De forma predeterminada, los usuarios seleccionan archivos individuales.</span><span class="sxs-lookup"><span data-stu-id="5fabb-107">By default, the user selects single files.</span></span> <span data-ttu-id="5fabb-108">Agregue el atributo `multiple` para que sea posible cargar varios archivos a la vez.</span><span class="sxs-lookup"><span data-stu-id="5fabb-108">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="5fabb-109">Cuando un usuario selecciona uno o varios archivos, el componente `InputFile` activa un evento `OnChange` y pasa un elemento `InputFileChangeEventArgs` que proporciona acceso a la lista de archivos seleccionados y detalles de cada archivo.</span><span class="sxs-lookup"><span data-stu-id="5fabb-109">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="5fabb-110">Un componente que recibe un archivo de imagen puede llamar al método de conveniencia `RequestImageFileAsync` en el archivo para cambiar el tamaño de los datos de imagen en el runtime de JavaScript del explorador antes de que la imagen se transmita a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5fabb-110">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="5fabb-111">En el siguiente ejemplo se muestra la carga de varios archivos de imagen en un componente:</span><span class="sxs-lookup"><span data-stu-id="5fabb-111">The following example demonstrates multiple image file upload in a component:</span></span>

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h3>Images</h3>

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
        var imageFiles = e.GetMultipleFiles();
        var format = "image/png";

        foreach (var imageFile in imageFiles)
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

<span data-ttu-id="5fabb-112">Para leer datos de un archivo seleccionado por el usuario, llame a `OpenReadStream` en el archivo y lea del flujo devuelto.</span><span class="sxs-lookup"><span data-stu-id="5fabb-112">To read data from a user-selected file, call `OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="5fabb-113">En una aplicación Blazor WebAssembly, los datos se transmiten directamente al código .NET dentro del explorador.</span><span class="sxs-lookup"><span data-stu-id="5fabb-113">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span> <span data-ttu-id="5fabb-114">En una aplicación Blazor Server, los datos de archivo se transmiten al código .NET en el servidor a medida que el archivo se lee desde la secuencia.</span><span class="sxs-lookup"><span data-stu-id="5fabb-114">In a Blazor Server app, the file data is streamed into .NET code on the server as the file is read from the stream.</span></span> 
