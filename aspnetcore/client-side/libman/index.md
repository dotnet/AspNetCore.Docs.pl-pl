---
title: Przejęcie biblioteki po stronie klienta w ASP.NET Core z LibMan
author: scottaddie
description: Dowiedz się, jak zainstalować zasoby biblioteki po stronie klienta w projekcie ASP.NET Core przy użyciu Menedżera bibliotek (LibMan).
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
uid: client-side/libman/index
ms.openlocfilehash: 87987446b7f2c625da90951510e697e06569ba36
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664669"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a><span data-ttu-id="2ec79-103">Przejęcie biblioteki po stronie klienta w ASP.NET Core z LibMan</span><span class="sxs-lookup"><span data-stu-id="2ec79-103">Client-side library acquisition in ASP.NET Core with LibMan</span></span>

<span data-ttu-id="2ec79-104">Przez [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="2ec79-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="2ec79-105">Menedżer biblioteki (LibMan) jest lekkim narzędziem do pozyskiwania bibliotek po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="2ec79-105">Library Manager (LibMan) is a lightweight, client-side library acquisition tool.</span></span> <span data-ttu-id="2ec79-106">LibMan pobiera popularne biblioteki i struktury z systemu plików lub z [sieci dostarczania zawartości (CDN).](https://wikipedia.org/wiki/Content_delivery_network)</span><span class="sxs-lookup"><span data-stu-id="2ec79-106">LibMan downloads popular libraries and frameworks from the file system or from a [content delivery network (CDN)](https://wikipedia.org/wiki/Content_delivery_network).</span></span> <span data-ttu-id="2ec79-107">Obsługiwane sieci CDN obejmują [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/)i [unpkg](https://unpkg.com/#/).</span><span class="sxs-lookup"><span data-stu-id="2ec79-107">The supported CDNs include [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/), and [unpkg](https://unpkg.com/#/).</span></span> <span data-ttu-id="2ec79-108">Wybrane pliki biblioteki są pobierane i umieszczane w odpowiednim miejscu w projekcie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2ec79-108">The selected library files are fetched and placed in the appropriate location within the ASP.NET Core project.</span></span>

## <a name="libman-use-cases"></a><span data-ttu-id="2ec79-109">Przypadki użycia LibMana</span><span class="sxs-lookup"><span data-stu-id="2ec79-109">LibMan use cases</span></span>

<span data-ttu-id="2ec79-110">LibMan oferuje następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="2ec79-110">LibMan offers the following benefits:</span></span>

* <span data-ttu-id="2ec79-111">Pobierane są tylko potrzebne pliki biblioteki.</span><span class="sxs-lookup"><span data-stu-id="2ec79-111">Only the library files you need are downloaded.</span></span>
* <span data-ttu-id="2ec79-112">Dodatkowe narzędzia, takie jak [Node.js](https://nodejs.org), [npm](https://www.npmjs.com)i [WebPack,](https://webpack.js.org)nie są konieczne do uzyskania podzbioru plików w bibliotece.</span><span class="sxs-lookup"><span data-stu-id="2ec79-112">Additional tooling, such as [Node.js](https://nodejs.org), [npm](https://www.npmjs.com), and [WebPack](https://webpack.js.org), isn't necessary to acquire a subset of files in a library.</span></span>
* <span data-ttu-id="2ec79-113">Pliki można umieszczać w określonej lokalizacji bez uciekania się do tworzenia zadań lub ręcznego kopiowania plików.</span><span class="sxs-lookup"><span data-stu-id="2ec79-113">Files can be placed in a specific location without resorting to build tasks or manual file copying.</span></span>

<span data-ttu-id="2ec79-114">Aby uzyskać więcej informacji na temat korzyści libman, obejrzyj [nowoczesne tworzenie stron internetowych front-end w programie Visual Studio 2017: segment LibMan](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).</span><span class="sxs-lookup"><span data-stu-id="2ec79-114">For more information about LibMan's benefits, watch [Modern front-end web development in Visual Studio 2017: LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).</span></span>

<span data-ttu-id="2ec79-115">LibMan nie jest systemem zarządzania pakietami.</span><span class="sxs-lookup"><span data-stu-id="2ec79-115">LibMan isn't a package management system.</span></span> <span data-ttu-id="2ec79-116">Jeśli korzystasz już z menedżera pakietów, takiego jak npm lub [przędza,](https://yarnpkg.com)kontynuuj ten problem.</span><span class="sxs-lookup"><span data-stu-id="2ec79-116">If you're already using a package manager, such as npm or [yarn](https://yarnpkg.com), continue doing so.</span></span> <span data-ttu-id="2ec79-117">LibMan nie został opracowany, aby zastąpić te narzędzia.</span><span class="sxs-lookup"><span data-stu-id="2ec79-117">LibMan wasn't developed to replace those tools.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2ec79-118">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="2ec79-118">Additional resources</span></span>

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="2ec79-119">Repozytorium LibMan GitHub</span><span class="sxs-lookup"><span data-stu-id="2ec79-119">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
