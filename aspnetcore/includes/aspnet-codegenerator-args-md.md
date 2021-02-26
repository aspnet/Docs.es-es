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
ms.openlocfilehash: 41d4fd2e746e08d32d9f666faab55acc56817be2
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552877"
---
<!-- Options common to Razor Pages and Controller -->
| <span data-ttu-id="f01d4-101">Opción</span><span class="sxs-lookup"><span data-stu-id="f01d4-101">Option</span></span>               | <span data-ttu-id="f01d4-102">Descripción</span><span class="sxs-lookup"><span data-stu-id="f01d4-102">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="f01d4-103">--model o -m</span><span class="sxs-lookup"><span data-stu-id="f01d4-103">--model or -m</span></span>  | <span data-ttu-id="f01d4-104">Clase de modelo que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="f01d4-104">Model class to use.</span></span> |
| <span data-ttu-id="f01d4-105">--dataContext o -dc</span><span class="sxs-lookup"><span data-stu-id="f01d4-105">--dataContext or -dc</span></span>  | <span data-ttu-id="f01d4-106">La clase `DbContext` que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="f01d4-106">The `DbContext` class to use.</span></span> |
| <span data-ttu-id="f01d4-107">--bootstrapVersion o -b</span><span class="sxs-lookup"><span data-stu-id="f01d4-107">--bootstrapVersion or -b</span></span>  | <span data-ttu-id="f01d4-108">Especifica la versión de arranque.</span><span class="sxs-lookup"><span data-stu-id="f01d4-108">Specifies the bootstrap version.</span></span> <span data-ttu-id="f01d4-109">Los valores válidos son `3` y `4`.</span><span class="sxs-lookup"><span data-stu-id="f01d4-109">Valid values are `3` or `4`.</span></span> <span data-ttu-id="f01d4-110">El valor predeterminado es `4`.</span><span class="sxs-lookup"><span data-stu-id="f01d4-110">Default is `4`.</span></span> <span data-ttu-id="f01d4-111">Si es necesario y no existe, se crea un directorio *wwwroot* que incluye los archivos de arranque de la versión especificada.</span><span class="sxs-lookup"><span data-stu-id="f01d4-111">If needed and not present, a *wwwroot* directory is created that includes the bootstrap files of the specified version.</span></span> |
| <span data-ttu-id="f01d4-112">--referenceScriptLibraries o -scripts</span><span class="sxs-lookup"><span data-stu-id="f01d4-112">--referenceScriptLibraries or -scripts</span></span> |  <span data-ttu-id="f01d4-113">Bibliotecas de scripts de referencia en las vistas generadas.</span><span class="sxs-lookup"><span data-stu-id="f01d4-113">Reference script libraries in the generated views.</span></span> <span data-ttu-id="f01d4-114">Agrega `_ValidationScriptsPartial` a las páginas Editar y Crear.</span><span class="sxs-lookup"><span data-stu-id="f01d4-114">Adds `_ValidationScriptsPartial` to Edit and Create pages.</span></span> |
| <span data-ttu-id="f01d4-115">--layout o -l</span><span class="sxs-lookup"><span data-stu-id="f01d4-115">--layout or -l</span></span> | <span data-ttu-id="f01d4-116">Página de diseño personalizado que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="f01d4-116">Custom Layout page to use.</span></span> |
| <span data-ttu-id="f01d4-117">--useDefaultLayout o -udl</span><span class="sxs-lookup"><span data-stu-id="f01d4-117">--useDefaultLayout or -udl</span></span> | <span data-ttu-id="f01d4-118">Usa el diseño predeterminado de las vistas.</span><span class="sxs-lookup"><span data-stu-id="f01d4-118">Use the default layout for the views.</span></span> |
| <span data-ttu-id="f01d4-119">--force o -f</span><span class="sxs-lookup"><span data-stu-id="f01d4-119">--force or -f</span></span> | <span data-ttu-id="f01d4-120">Sobrescribe los archivos existentes.</span><span class="sxs-lookup"><span data-stu-id="f01d4-120">Overwrite existing files.</span></span> |
| <span data-ttu-id="f01d4-121">--relativeFolderPath o -outDir</span><span class="sxs-lookup"><span data-stu-id="f01d4-121">--relativeFolderPath or -outDir</span></span> | <span data-ttu-id="f01d4-122">Ruta de acceso relativa a la carpeta de salida del proyecto donde se genera el archivo.</span><span class="sxs-lookup"><span data-stu-id="f01d4-122">The relative output folder path from project where the file are generated.</span></span> <span data-ttu-id="f01d4-123">Si no se especifica, los archivos se generan en la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="f01d4-123">If not specified, files are generated in the project folder.</span></span> |