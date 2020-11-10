---
title: Cargas de archivos de ASP.NET Core Blazor
author: guardrex
description: Información sobre cómo cargar archivos en Blazor con el componente InputFile.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
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
ms.date: 10/27/2020
uid: blazor/file-uploads
ms.openlocfilehash: c0806c3a68a4d9e698925f6ec955dd2f53d7818f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056132"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a>Cargas de archivos de ASP.NET Core Blazor

De [Daniel Roth](https://github.com/danroth27) y [Pranav Krishnamoorthy](https://github.com/pranavkm)

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

Use el componente `InputFile` para leer los datos del archivo de explorador en código .NET, incluidos los relativos a las cargas de archivos.

> [!WARNING]
> Siga siempre los procedimientos recomendados de seguridad de carga de archivos. Para obtener más información, vea <xref:mvc/models/file-uploads#security-considerations>.

## <a name="inputfile-component"></a>Componente de `InputFile`

El componente `InputFile` se representa como una entrada HTML de tipo `file`.

De forma predeterminada, los usuarios seleccionan archivos individuales. Agregue el atributo `multiple` para que sea posible cargar varios archivos a la vez. Cuando un usuario selecciona uno o varios archivos, el componente `InputFile` activa un evento `OnChange` y pasa un elemento `InputFileChangeEventArgs` que proporciona acceso a la lista de archivos seleccionados y detalles de cada archivo.

Para leer datos de un archivo seleccionado por el usuario:

* Llame a `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` en el archivo y lea desde el flujo devuelto. Para obtener más información, vea la sección [Flujos de archivos](#file-streams).
* El valor <xref:System.IO.Stream> devuelto por `OpenReadStream` aplica un tamaño máximo en bytes del `Stream` que se lee. De forma predeterminada, solo se pueden leer los archivos con un tamaño inferior a 524 288 KB (512 KB) antes de que lecturas adicionales generen una excepción. Este límite está presente para evitar que los desarrolladores lean accidentalmente archivos de gran tamaño en la memoria. El parámetro `maxAllowedSize` de `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` se puede utilizar para especificar un tamaño mayor si es necesario.
* Evite leer el flujo de archivos entrantes directamente en la memoria. Por ejemplo, no copie los bytes de un archivo en un elemento <xref:System.IO.MemoryStream> o lo lea como una matriz de bytes. Estos enfoques pueden dar lugar a problemas de rendimiento y seguridad, sobre todo en Blazor Server. En su lugar, considere la posibilidad de copiar bytes de un archivo en un almacén externo, como un blob o un archivo en disco.

Un componente que recibe un archivo de imagen puede llamar al método de conveniencia `RequestImageFileAsync` en el archivo para cambiar el tamaño de los datos de imagen en el runtime de JavaScript del explorador antes de que la imagen se transmita a la aplicación.

En el ejemplo siguiente se muestra la carga de varios archivos de imagen en un componente. `InputFileChangeEventArgs.GetMultipleFiles` permite leer varios archivos. Especifique el número máximo de archivos que espera leer para evitar que un usuario malintencionado cargue más de los que la aplicación espera. `InputFileChangeEventArgs.File` permite leer el primer y único archivo si la carga de archivos no admite varios.

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

`IBrowserFile` devuelve metadatos [que expone el explorador](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) como propiedades. Estos metadatos pueden ser útiles para la validación preliminar. Por ejemplo, vea los [componentes de ejemplo `FileUpload.razor` y `FilePreview.razor`](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).

## <a name="file-streams"></a>Flujos de archivos

En una aplicación Blazor WebAssembly, los datos se transmiten directamente al código .NET dentro del explorador.

En una aplicación Blazor Server, los datos de archivo se transmiten en la conexión SignalR al código .NET en el servidor a medida que el archivo se lee desde el flujo. [`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) permite configurar las características de carga de archivos para Blazor Server.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
