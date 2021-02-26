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
> <span data-ttu-id="feea0-101">Para este tutorial usará la característica de las *migraciones* de Entity Framework Core siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="feea0-101">For this tutorial you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="feea0-102">Las migraciones actualizan el esquema de la base de datos para que coincida con los cambios en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="feea0-102">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="feea0-103">Aun así, las migraciones solo pueden realizar los tipos de cambios que admite el proveedor de EF Core; las capacidades del de SQLite son limitadas.</span><span class="sxs-lookup"><span data-stu-id="feea0-103">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="feea0-104">Por ejemplo, se permite agregar una columna, pero no eliminarla ni cambiarla.</span><span class="sxs-lookup"><span data-stu-id="feea0-104">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="feea0-105">Si se crea una migración para quitar o cambiar una columna, el comando `ef migrations add` se ejecuta correctamente, pero el comando `ef database update` produce un error.</span><span class="sxs-lookup"><span data-stu-id="feea0-105">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="feea0-106">Debido a estas limitaciones, este tutorial no usa las migraciones para realizar cambios en el esquema de SQLite.</span><span class="sxs-lookup"><span data-stu-id="feea0-106">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="feea0-107">En su lugar, cuando cambie el esquema, desconectará la base de datos y volverá a crearla.</span><span class="sxs-lookup"><span data-stu-id="feea0-107">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="feea0-108">La solución alternativa para las limitaciones de SQLite consiste en escribir de forma manual el código de migraciones para llevar a cabo una recompilación de la tabla cuando cambie algún elemento de esta.</span><span class="sxs-lookup"><span data-stu-id="feea0-108">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="feea0-109">La recompilación de una tabla implica lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="feea0-109">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="feea0-110">Crear una tabla.</span><span class="sxs-lookup"><span data-stu-id="feea0-110">Creating a new table.</span></span>
>* <span data-ttu-id="feea0-111">Copiar datos de la tabla antigua a la nueva tabla.</span><span class="sxs-lookup"><span data-stu-id="feea0-111">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="feea0-112">Eliminar la tabla anterior.</span><span class="sxs-lookup"><span data-stu-id="feea0-112">Dropping the old table.</span></span>
>* <span data-ttu-id="feea0-113">Cambiar el nombre de la tabla nueva.</span><span class="sxs-lookup"><span data-stu-id="feea0-113">Renaming the new table.</span></span>
>
><span data-ttu-id="feea0-114">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="feea0-114">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="feea0-115">Limitaciones del proveedor de base de datos SQLite para EF Core</span><span class="sxs-lookup"><span data-stu-id="feea0-115">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="feea0-116">Personalización del código de migración</span><span class="sxs-lookup"><span data-stu-id="feea0-116">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="feea0-117">Propagación de los datos</span><span class="sxs-lookup"><span data-stu-id="feea0-117">Data seeding</span></span>](/ef/core/modeling/data-seeding)
  * [<span data-ttu-id="feea0-118">Instrucción ALTER TABLE de SQLite</span><span class="sxs-lookup"><span data-stu-id="feea0-118">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)