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
ms.openlocfilehash: e8c5bd00178aefb91ab0e7066c5490ceba315530
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551826"
---
> [!NOTE]
> Para este tutorial usará la característica de las *migraciones* de Entity Framework Core siempre que sea posible. Las migraciones actualizan el esquema de la base de datos para que coincida con los cambios en el modelo de datos. Aun así, las migraciones solo pueden realizar los tipos de cambios que admite el proveedor de EF Core; las capacidades del de SQLite son limitadas. Por ejemplo, se permite agregar una columna, pero no eliminarla ni cambiarla. Si se crea una migración para quitar o cambiar una columna, el comando `ef migrations add` se ejecuta correctamente, pero el comando `ef database update` produce un error. Debido a estas limitaciones, este tutorial no usa las migraciones para realizar cambios en el esquema de SQLite. En su lugar, cuando cambie el esquema, desconectará la base de datos y volverá a crearla.
>
>La solución alternativa para las limitaciones de SQLite consiste en escribir de forma manual el código de migraciones para llevar a cabo una recompilación de la tabla cuando cambie algún elemento de esta. La recompilación de una tabla implica lo siguiente:
>
>* Crear una tabla.
>* Copiar datos de la tabla antigua a la nueva tabla.
>* Eliminar la tabla anterior.
>* Cambiar el nombre de la tabla nueva.
>
>Para obtener más información, vea los siguientes recursos:
>
> * [Limitaciones del proveedor de base de datos SQLite para EF Core](/ef/core/providers/sqlite/limitations)
> * [Personalización del código de migración](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Propagación de los datos](/ef/core/modeling/data-seeding)
  * [Instrucción ALTER TABLE de SQLite](https://sqlite.org/lang_altertable.html)