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
ms.openlocfilehash: d05b0f327eb1670847125e6a73ef7277ebd069b5
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551937"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Presione Ctrl+F5 para ejecutarla sin el depurador.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) y ejecuta la aplicación. En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`). Esto es así porque `localhost` es el nombre de host estándar del equipo local. Localhost solo sirve las solicitudes web del equipo local. Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.
 
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Presione **Ctrl+F5** para ejecutar sin el depurador.

  Visual Studio Code inicia [Kestrel](xref:fundamentals/servers/kestrel) y un explorador, y se desplaza hasta `http://localhost:5001`. En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`). Esto es así porque `localhost` es el nombre de host estándar del equipo local. Localhost solo sirve las solicitudes web del equipo local.

  
# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* En Visual Studio, presione **Opt-Cmd-ENTRAR** para realizar la ejecución sin el depurador. O bien, en la barra de menús, vaya a **Ejecutar > Iniciar sin depurar**.

  Visual Studio iniciará [Kestrel](xref:fundamentals/servers/kestrel) y un explorador, y se desplazará a `http://localhost:5001`.

<!-- End of VS tabs -->

---
