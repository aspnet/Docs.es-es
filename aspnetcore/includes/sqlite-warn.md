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
> <span data-ttu-id="b0dac-101">**Limitaciones de SQLite**</span><span class="sxs-lookup"><span data-stu-id="b0dac-101">**SQLite limitations**</span></span>
>
> <span data-ttu-id="b0dac-102">En este tutorial se usa la característica de *migraciones* de Entity Framework Core siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="b0dac-102">This tutorial uses the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="b0dac-103">Las migraciones actualizan el esquema de la base de datos para que coincida con los cambios en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="b0dac-103">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="b0dac-104">Pero las migraciones solo permiten los tipos de cambios que admite el motor de base de datos, y las funciones de cambio de esquema de SQLite son limitadas.</span><span class="sxs-lookup"><span data-stu-id="b0dac-104">However, migrations only does the kinds of changes that the database engine supports, and SQLite's schema change capabilities are limited.</span></span> <span data-ttu-id="b0dac-105">Por ejemplo, se permite agregar una columna, pero no eliminarla.</span><span class="sxs-lookup"><span data-stu-id="b0dac-105">For example, adding a column is supported, but removing a column is not supported.</span></span> <span data-ttu-id="b0dac-106">Si se crea una migración para quitar una columna, el comando `ef migrations add` se ejecuta correctamente, pero el comando `ef database update` produce un error.</span><span class="sxs-lookup"><span data-stu-id="b0dac-106">If a migration is created to remove a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> 
>
> <span data-ttu-id="b0dac-107">La solución alternativa para las limitaciones de SQLite consiste en escribir de forma manual el código de migraciones para llevar a cabo una recompilación de la tabla cuando cambie algún elemento de esta.</span><span class="sxs-lookup"><span data-stu-id="b0dac-107">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="b0dac-108">El código iría en los métodos `Up` y `Down` para una migración e implicaría lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b0dac-108">The code would go in the `Up` and `Down` methods for a migration and would involve:</span></span>
>
> * <span data-ttu-id="b0dac-109">Crear una tabla.</span><span class="sxs-lookup"><span data-stu-id="b0dac-109">Creating a new table.</span></span>
> * <span data-ttu-id="b0dac-110">Copiar datos de la tabla antigua a la nueva tabla.</span><span class="sxs-lookup"><span data-stu-id="b0dac-110">Copying data from the old table to the new table.</span></span>
> * <span data-ttu-id="b0dac-111">Eliminar la tabla anterior.</span><span class="sxs-lookup"><span data-stu-id="b0dac-111">Dropping the old table.</span></span>
> * <span data-ttu-id="b0dac-112">Cambiar el nombre de la tabla nueva.</span><span class="sxs-lookup"><span data-stu-id="b0dac-112">Renaming the new table.</span></span>
>
> <span data-ttu-id="b0dac-113">La escritura de código específico de la base de datos de este tipo está fuera del ámbito de este tutorial.</span><span class="sxs-lookup"><span data-stu-id="b0dac-113">Writing database-specific code of this type is outside the scope of this tutorial.</span></span> <span data-ttu-id="b0dac-114">En su lugar, en este tutorial se quita y se vuelve a crear la base de datos cada vez que se produce un error al intentar aplicar una migración.</span><span class="sxs-lookup"><span data-stu-id="b0dac-114">Instead, this tutorial drops and re-creates the database whenever an attempt to apply a migration would fail.</span></span> <span data-ttu-id="b0dac-115">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="b0dac-115">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="b0dac-116">Limitaciones del proveedor de base de datos SQLite para EF Core</span><span class="sxs-lookup"><span data-stu-id="b0dac-116">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="b0dac-117">Personalización del código de migración</span><span class="sxs-lookup"><span data-stu-id="b0dac-117">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="b0dac-118">Propagación de los datos</span><span class="sxs-lookup"><span data-stu-id="b0dac-118">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="b0dac-119">Instrucción ALTER TABLE de SQLite</span><span class="sxs-lookup"><span data-stu-id="b0dac-119">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)