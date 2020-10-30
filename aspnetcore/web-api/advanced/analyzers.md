---
title: Uso de analizadores de API web
author: pranavkm
description: Obtenga información sobre el paquete de analizadores de la API web de ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
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
uid: web-api/advanced/analyzers
ms.openlocfilehash: cf0415e7d72e21a48db8bbeb4540f05e0b0a4198
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057926"
---
# <a name="use-web-api-analyzers"></a>Uso de analizadores de API web

ASP.NET Core 2.2 y versiones posteriores proporcionan un paquete de analizadores de MVC diseñado para su uso con proyectos de API web. Los analizadores funcionan con los controladores anotados con <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> y se compilan con [convenciones de la API web](xref:web-api/advanced/conventions).

El paquete de analizadores le informa de cualquier acción de controlador que:

* Devuelve un código de estado no declarado.
* Devuelve un resultado correcto no declarado.
* Documenta un código de estado que no se devuelve.
* Incluye una comprobación de validación del modelo explícita.

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a>Referencia al paquete del analizador

En ASP.NET Core 3.0 o versiones posteriores, los analizadores se incluyen en el SDK de .NET Core. Para habilitar el analizador en el proyecto, incluya la propiedad `IncludeOpenAPIAnalyzers` en el archivo de proyecto:

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a>Instalación del paquete

Instale el paquete de NuGet [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) con uno de los métodos siguientes:

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

En la ventana **Consola del Administrador de paquetes** :
  * Vaya a **Ver** > **otra** > **consola del administrador de paquetes** de Windows.
  * Vaya al directorio en el que esté el archivo *ApiConventions.csproj* .
  * Ejecute el comando siguiente:

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Haga clic con el botón secundario en la carpeta *paquetes* en **Panel de solución** > **agregar paquetes..** ..
* Establezca la lista desplegable **origen** de la ventana **agregar paquetes** en "Nuget.org".
* En el cuadro de búsqueda, escriba "Microsoft.AspNetCore.Mvc.Api.Analyzers".
* Seleccione el paquete "Microsoft.AspNetCore.Mvc.Api.Analyzers" en el panel de resultados y haga clic en **Agregar paquete** .

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ejecute el siguiente comando en el **terminal integrado** :

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

Ejecute el siguiente comando:

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a>Analizadores para las convenciones de API web

Los documentos de Open API contienen los códigos de estado y los tipos de respuesta que puede devolver una acción. En ASP.NET Core MVC, los atributos como <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> y <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> se usan para documentar una acción. <xref:tutorials/web-api-help-pages-using-swagger> ofrece información más detallada sobre cómo documentar su API web.

Uno de los analizadores del paquete inspecciona los controladores anotados con <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> e identifica las acciones que no documentan completamente sus respuestas. Considere el ejemplo siguiente:

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

La acción anterior documenta el tipo de valor devuelto correcto HTTP 200, pero no documenta el código de estado de error HTTP 404. El analizador informa de la documentación que falta para el código de estado 404 de HTTP como una advertencia. Se proporciona una opción para corregir el problema.

![advertencia notificada por el analizador](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a>Recursos adicionales

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
