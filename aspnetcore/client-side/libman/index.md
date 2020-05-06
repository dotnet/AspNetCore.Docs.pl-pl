---
title: Pozyskiwanie biblioteki po stronie klienta w ASP.NET Core z LibMan
author: scottaddie
description: Dowiedz się, jak zainstalować zasoby biblioteki po stronie klienta w projekcie ASP.NET Core przy użyciu programu Library Manager (LibMan).
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/libman/index
ms.openlocfilehash: 8dc94bd8dc15eaf3b778a8bd2905fcce08398a95
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82770496"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a><span data-ttu-id="40bb9-103">Pozyskiwanie biblioteki po stronie klienta w ASP.NET Core z LibMan</span><span class="sxs-lookup"><span data-stu-id="40bb9-103">Client-side library acquisition in ASP.NET Core with LibMan</span></span>

<span data-ttu-id="40bb9-104">Przez [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="40bb9-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="40bb9-105">Library Manager (LibMan) to lekkie narzędzie do pozyskiwania bibliotek po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="40bb9-105">Library Manager (LibMan) is a lightweight, client-side library acquisition tool.</span></span> <span data-ttu-id="40bb9-106">LibMan pobiera popularne biblioteki i struktury z systemu plików lub z usługi [Content Delivery Network (CDN)](https://wikipedia.org/wiki/Content_delivery_network).</span><span class="sxs-lookup"><span data-stu-id="40bb9-106">LibMan downloads popular libraries and frameworks from the file system or from a [content delivery network (CDN)](https://wikipedia.org/wiki/Content_delivery_network).</span></span> <span data-ttu-id="40bb9-107">Obsługiwane sieci CDN obejmują [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/)i [unpkg](https://unpkg.com/#/).</span><span class="sxs-lookup"><span data-stu-id="40bb9-107">The supported CDNs include [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/), and [unpkg](https://unpkg.com/#/).</span></span> <span data-ttu-id="40bb9-108">Wybrane pliki biblioteki są pobierane i umieszczane w odpowiedniej lokalizacji w ramach projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="40bb9-108">The selected library files are fetched and placed in the appropriate location within the ASP.NET Core project.</span></span>

## <a name="libman-use-cases"></a><span data-ttu-id="40bb9-109">LibMan przypadki użycia</span><span class="sxs-lookup"><span data-stu-id="40bb9-109">LibMan use cases</span></span>

<span data-ttu-id="40bb9-110">LibMan oferuje następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="40bb9-110">LibMan offers the following benefits:</span></span>

* <span data-ttu-id="40bb9-111">Pobierane są tylko pliki biblioteki, które są potrzebne.</span><span class="sxs-lookup"><span data-stu-id="40bb9-111">Only the library files you need are downloaded.</span></span>
* <span data-ttu-id="40bb9-112">Dodatkowe narzędzia, takie jak [Node. js](https://nodejs.org), [npm](https://www.npmjs.com)i [WebPack](https://webpack.js.org), nie są niezbędne do uzyskania podzestawu plików w bibliotece.</span><span class="sxs-lookup"><span data-stu-id="40bb9-112">Additional tooling, such as [Node.js](https://nodejs.org), [npm](https://www.npmjs.com), and [WebPack](https://webpack.js.org), isn't necessary to acquire a subset of files in a library.</span></span>
* <span data-ttu-id="40bb9-113">Pliki można umieścić w określonej lokalizacji bez konieczności tworzenia zadań kompilacji ani ręcznego kopiowania plików.</span><span class="sxs-lookup"><span data-stu-id="40bb9-113">Files can be placed in a specific location without resorting to build tasks or manual file copying.</span></span>

<span data-ttu-id="40bb9-114">Aby uzyskać więcej informacji na temat korzyści z programu LibMan, Obejrzyj [nowoczesne projektowanie sieci Web frontonu w programie Visual Studio 2017: LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).</span><span class="sxs-lookup"><span data-stu-id="40bb9-114">For more information about LibMan's benefits, watch [Modern front-end web development in Visual Studio 2017: LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).</span></span>

<span data-ttu-id="40bb9-115">LibMan nie jest system zarządzania pakietami.</span><span class="sxs-lookup"><span data-stu-id="40bb9-115">LibMan isn't a package management system.</span></span> <span data-ttu-id="40bb9-116">Jeśli już używasz Menedżera pakietów, takiego jak npm lub [przędza](https://yarnpkg.com), Kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="40bb9-116">If you're already using a package manager, such as npm or [yarn](https://yarnpkg.com), continue doing so.</span></span> <span data-ttu-id="40bb9-117">Nie opracowano LibMan do zastępowania tych narzędzi.</span><span class="sxs-lookup"><span data-stu-id="40bb9-117">LibMan wasn't developed to replace those tools.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="40bb9-118">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="40bb9-118">Additional resources</span></span>

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="40bb9-119">Repozytorium GitHub LibMan</span><span class="sxs-lookup"><span data-stu-id="40bb9-119">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
