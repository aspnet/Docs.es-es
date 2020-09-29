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
# <a name="aspnet-core-no-locblazor-file-uploads"></a>Cargas de archivos de ASP.NET Core Blazor

Por [Daniel Roth](https://github.com/danroth27)

Use el componente `InputFile` para leer los datos del archivo de explorador en código .NET, incluidos los relativos a las cargas de archivos. El componente `InputFile` se representa como una entrada HTML de tipo `file`.

De forma predeterminada, los usuarios seleccionan archivos individuales. Agregue el atributo `multiple` para que sea posible cargar varios archivos a la vez. Cuando un usuario selecciona uno o varios archivos, el componente `InputFile` activa un evento `OnChange` y pasa un elemento `InputFileChangeEventArgs` que proporciona acceso a la lista de archivos seleccionados y detalles de cada archivo.

Un componente que recibe un archivo de imagen puede llamar al método de conveniencia `RequestImageFileAsync` en el archivo para cambiar el tamaño de los datos de imagen en el runtime de JavaScript del explorador antes de que la imagen se transmita a la aplicación.

En el siguiente ejemplo se muestra la carga de varios archivos de imagen en un componente:

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

Para leer datos de un archivo seleccionado por el usuario, llame a `OpenReadStream` en el archivo y lea del flujo devuelto. En una aplicación Blazor WebAssembly, los datos se transmiten directamente al código .NET dentro del explorador. En una aplicación Blazor Server, los datos de archivo se transmiten al código .NET en el servidor a medida que el archivo se lee desde la secuencia. 
