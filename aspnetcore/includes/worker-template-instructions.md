---
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
ms.openlocfilehash: 34e12afd6bc7015e4609fbdf773259ed5545809b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552958"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Cree un nuevo proyecto.
1. Seleccione **Worker Service** (Servicio de Worker). Seleccione **Siguiente**.
1. Proporcione un nombre para el proyecto en el campo **Nombre del proyecto** o acepte el predeterminado. Seleccione **Crear**.
1. En el cuadro de diálogo **Crear un servicio de Worker**, seleccione **Crear**.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Cree un nuevo proyecto.
1. Seleccione **Aplicación** en **.NET Core** en la barra lateral.
1. Seleccione **Trabajador** en **ASP.NET Core**. Seleccione **Next** (Siguiente).
1. Seleccione **.NET Core 3.0** o una versión posterior para **Marco de trabajo de destino**. Seleccione **Next** (Siguiente).
1. Proporcione un nombre en el campo **Nombre del proyecto**. Seleccione **Crear**.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

Desde un shell de comandos, use la plantilla Worker Service (`worker`) con el comando [dotnet new](/dotnet/core/tools/dotnet-new). En el ejemplo siguiente, se crea una aplicación Worker Service llamada `ContosoWorker`. Al ejecutar el comando, se crea automáticamente una carpeta para la aplicación `ContosoWorker`.

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
