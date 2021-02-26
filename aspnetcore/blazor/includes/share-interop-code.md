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
ms.openlocfilehash: 378c74c930f6e3f9565f408a2761a8ed867450d3
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551821"
---
## <a name="share-interop-code-in-a-class-library"></a>Uso compartido de código de interoperabilidad en una biblioteca de clases

El código de interoperabilidad de JS se puede incluir en una biblioteca de clases, lo que permite compartir el código en un paquete NuGet.

La biblioteca de clases controla la inserción de recursos de JavaScript en el ensamblado compilado. Los archivos de JavaScript se colocan en la carpeta `wwwroot`. Las herramientas se encargan de insertar los recursos cuando se compila la biblioteca.

Se hace referencia al paquete NuGet compilado en el archivo de proyecto de la aplicación de la misma manera que se hace referencia a cualquier paquete NuGet. Una vez restaurado el paquete, el código de la aplicación puede llamar a JavaScript como si fuera C#.

Para obtener más información, vea <xref:blazor/components/class-libraries>.
