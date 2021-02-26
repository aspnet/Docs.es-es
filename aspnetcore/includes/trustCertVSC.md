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
ms.openlocfilehash: df4a9a7db8097ea765b2d0f404305b771f994ddf
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552567"
---
* Para confiar en el certificado de desarrollo de HTTPS, ejecute el comando siguiente:

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  El comando anterior no funciona en Linux. Vea la documentación de su distribución de Linux para confiar en un certificado.

  El comando anterior muestra el siguiente cuadro de diálogo:

  ![Cuadro de diálogo de advertencia de seguridad](~/getting-started/_static/cert.png)

* Si acepta confiar en el certificado de desarrollo, seleccione **Sí**.

  Para obtener más información, vea [Confiar en el certificado de desarrollo de ASP.NET Core HTTPS ](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).
  
[!INCLUDE[trust FF](~/includes/trust-ff.md)]