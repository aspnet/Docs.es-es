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
ms.openlocfilehash: cf20722e8c8669fb17af8db032d4064ca2be2f4c
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551426"
---
> [!NOTE]
> 
> **Limitaciones de SQLite**
>
> En este tutorial se usa la característica de *migraciones* de Entity Framework Core siempre que sea posible. Las migraciones actualizan el esquema de la base de datos para que coincida con los cambios en el modelo de datos. Pero las migraciones solo permiten los tipos de cambios que admite el motor de base de datos, y las funciones de cambio de esquema de SQLite son limitadas. Por ejemplo, se permite agregar una columna, pero no eliminarla. Si se crea una migración para quitar una columna, el comando `ef migrations add` se ejecuta correctamente, pero el comando `ef database update` produce un error. 
>
> La solución alternativa para las limitaciones de SQLite consiste en escribir de forma manual el código de migraciones para llevar a cabo una recompilación de la tabla cuando cambie algún elemento de esta. El código iría en los métodos `Up` y `Down` para una migración e implicaría lo siguiente:
>
> * Crear una tabla.
> * Copiar datos de la tabla antigua a la nueva tabla.
> * Eliminar la tabla anterior.
> * Cambiar el nombre de la tabla nueva.
>
> La escritura de código específico de la base de datos de este tipo está fuera del ámbito de este tutorial. En su lugar, en este tutorial se quita y se vuelve a crear la base de datos cada vez que se produce un error al intentar aplicar una migración. Para obtener más información, vea los siguientes recursos:
>
> * [Limitaciones del proveedor de base de datos SQLite para EF Core](/ef/core/providers/sqlite/limitations)
> * [Personalización del código de migración](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Propagación de los datos](/ef/core/modeling/data-seeding)
> * [Instrucción ALTER TABLE de SQLite](https://sqlite.org/lang_altertable.html)