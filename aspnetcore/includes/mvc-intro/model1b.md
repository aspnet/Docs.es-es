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
ms.openlocfilehash: 3c21d2a5604c2dfc96624fb5d161537797925fef
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552580"
---
Agregue las propiedades siguientes a la clase `Movie`:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

la clase `Movie` contiene:

* El campo `Id`, que requiere la base de datos para la clave principal.
* `[DataType(DataType.Date)]`:  el atributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) especifica el tipo de datos (`Date`). Con este atributo:

  * El usuario no tiene que especificar información horaria en el campo de fecha.
  * Solo se muestra la fecha, no información horaria.

Los elementos [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) se tratan en un tutorial posterior.