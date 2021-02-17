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
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552876"
---
<!-- Options common to Razor Pages and Controller -->
| <span data-ttu-id="3f1f2-101">Opcja</span><span class="sxs-lookup"><span data-stu-id="3f1f2-101">Option</span></span>               | <span data-ttu-id="3f1f2-102">Opis</span><span class="sxs-lookup"><span data-stu-id="3f1f2-102">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="3f1f2-103">--model lub-m</span><span class="sxs-lookup"><span data-stu-id="3f1f2-103">--model or -m</span></span>  | <span data-ttu-id="3f1f2-104">Klasa modelu do użycia.</span><span class="sxs-lookup"><span data-stu-id="3f1f2-104">Model class to use.</span></span> |
| <span data-ttu-id="3f1f2-105">--DataContext lub-DC</span><span class="sxs-lookup"><span data-stu-id="3f1f2-105">--dataContext or -dc</span></span>  | <span data-ttu-id="3f1f2-106">`DbContext`Klasa, która ma zostać użyta.</span><span class="sxs-lookup"><span data-stu-id="3f1f2-106">The `DbContext` class to use.</span></span> |
| <span data-ttu-id="3f1f2-107">--bootstrapVersion lub-b</span><span class="sxs-lookup"><span data-stu-id="3f1f2-107">--bootstrapVersion or -b</span></span>  | <span data-ttu-id="3f1f2-108">Określa wersję ładowania początkowego.</span><span class="sxs-lookup"><span data-stu-id="3f1f2-108">Specifies the bootstrap version.</span></span> <span data-ttu-id="3f1f2-109">Prawidłowe wartości to `3` lub `4` .</span><span class="sxs-lookup"><span data-stu-id="3f1f2-109">Valid values are `3` or `4`.</span></span> <span data-ttu-id="3f1f2-110">Wartość domyślna to `4`.</span><span class="sxs-lookup"><span data-stu-id="3f1f2-110">Default is `4`.</span></span> <span data-ttu-id="3f1f2-111">W razie konieczności i nieobecny katalog *wwwroot* zostanie utworzony, który zawiera pliki Bootstrap określonej wersji.</span><span class="sxs-lookup"><span data-stu-id="3f1f2-111">If needed and not present, a *wwwroot* directory is created that includes the bootstrap files of the specified version.</span></span> |
| <span data-ttu-id="3f1f2-112">--referenceScriptLibraries lub-scripts</span><span class="sxs-lookup"><span data-stu-id="3f1f2-112">--referenceScriptLibraries or -scripts</span></span> |  <span data-ttu-id="3f1f2-113">Odwołuj się do bibliotek skryptów w wygenerowanych widokach.</span><span class="sxs-lookup"><span data-stu-id="3f1f2-113">Reference script libraries in the generated views.</span></span> <span data-ttu-id="3f1f2-114">Dodaje `_ValidationScriptsPartial` do edycji i tworzenia stron.</span><span class="sxs-lookup"><span data-stu-id="3f1f2-114">Adds `_ValidationScriptsPartial` to Edit and Create pages.</span></span> |
| <span data-ttu-id="3f1f2-115">--układ lub-l</span><span class="sxs-lookup"><span data-stu-id="3f1f2-115">--layout or -l</span></span> | <span data-ttu-id="3f1f2-116">Strona układu niestandardowego do użycia.</span><span class="sxs-lookup"><span data-stu-id="3f1f2-116">Custom Layout page to use.</span></span> |
| <span data-ttu-id="3f1f2-117">--useDefaultLayout lub-UDL</span><span class="sxs-lookup"><span data-stu-id="3f1f2-117">--useDefaultLayout or -udl</span></span> | <span data-ttu-id="3f1f2-118">Użyj domyślnego układu dla widoków.</span><span class="sxs-lookup"><span data-stu-id="3f1f2-118">Use the default layout for the views.</span></span> |
| <span data-ttu-id="3f1f2-119">--Force lub-f</span><span class="sxs-lookup"><span data-stu-id="3f1f2-119">--force or -f</span></span> | <span data-ttu-id="3f1f2-120">Zastąp istniejące pliki.</span><span class="sxs-lookup"><span data-stu-id="3f1f2-120">Overwrite existing files.</span></span> |
| <span data-ttu-id="3f1f2-121">--relativeFolderPath lub-outDir</span><span class="sxs-lookup"><span data-stu-id="3f1f2-121">--relativeFolderPath or -outDir</span></span> | <span data-ttu-id="3f1f2-122">Ścieżka względna folderu wyjściowego z projektu, w którym jest generowany plik.</span><span class="sxs-lookup"><span data-stu-id="3f1f2-122">The relative output folder path from project where the file are generated.</span></span> <span data-ttu-id="3f1f2-123">Jeśli nie zostanie określony, pliki są generowane w folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="3f1f2-123">If not specified, files are generated in the project folder.</span></span> |