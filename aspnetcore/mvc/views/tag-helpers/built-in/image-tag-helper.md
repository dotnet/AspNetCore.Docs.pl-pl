---
title: Pomocnik tagu obrazu w ASP.NET Core
author: pkellner
description: Pokazuje, jak korzystać z pomocnika tagów obrazu.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: a877078356b54a3d0bba6a1be24307e6e1c075c8
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018601"
---
# <a name="image-tag-helper-in-aspnet-core"></a><span data-ttu-id="24101-103">Pomocnik tagu obrazu w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="24101-103">Image Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="24101-104">Według [Peterowi Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="24101-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="24101-105">Pomocnik tagu obrazu ulepsza tag, `<img>` Aby zapewnić zachowanie Busting pamięci podręcznej dla statycznych plików obrazu.</span><span class="sxs-lookup"><span data-stu-id="24101-105">The Image Tag Helper enhances the `<img>` tag to provide cache-busting behavior for static image files.</span></span>

<span data-ttu-id="24101-106">Busting pamięci podręcznej jest unikatową wartością reprezentującą skrót pliku obrazu statycznego dołączonego do adresu URL zasobu.</span><span class="sxs-lookup"><span data-stu-id="24101-106">A cache-busting string is a unique value representing the hash of the static image file appended to the asset's URL.</span></span> <span data-ttu-id="24101-107">Unikatowy ciąg będzie monitował klientów (i niektórych serwerów proxy) do ponownego załadowania obrazu z serwera hosta sieci Web, a nie z pamięci podręcznej klienta.</span><span class="sxs-lookup"><span data-stu-id="24101-107">The unique string prompts clients (and some proxies) to reload the image from the host web server and not from the client's cache.</span></span>

<span data-ttu-id="24101-108">Jeśli źródło obrazu ( `src` ) jest plikiem statycznym na serwerze sieci Web hosta:</span><span class="sxs-lookup"><span data-stu-id="24101-108">If the image source (`src`) is a static file on the host web server:</span></span>

* <span data-ttu-id="24101-109">Unikatowy ciąg Busting jest dołączany jako parametr zapytania do źródła obrazu.</span><span class="sxs-lookup"><span data-stu-id="24101-109">A unique cache-busting string is appended as a query parameter to the image source.</span></span>
* <span data-ttu-id="24101-110">Jeśli plik na serwerze sieci Web hosta ulegnie zmianie, generowany jest unikatowy adres URL żądania, który obejmuje zaktualizowany parametr żądania.</span><span class="sxs-lookup"><span data-stu-id="24101-110">If the file on the host web server changes, a unique request URL is generated that includes the updated request parameter.</span></span>

<span data-ttu-id="24101-111">Aby zapoznać się z omówieniem pomocników tagów, zobacz <xref:mvc/views/tag-helpers/intro> .</span><span class="sxs-lookup"><span data-stu-id="24101-111">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="image-tag-helper-attributes"></a><span data-ttu-id="24101-112">Atrybuty pomocnika tagów obrazu</span><span class="sxs-lookup"><span data-stu-id="24101-112">Image Tag Helper Attributes</span></span>

### <a name="src"></a><span data-ttu-id="24101-113">src</span><span class="sxs-lookup"><span data-stu-id="24101-113">src</span></span>

<span data-ttu-id="24101-114">Aby uaktywnić pomocnika tagów obrazu, `src` atrybut jest wymagany w `<img>` elemencie.</span><span class="sxs-lookup"><span data-stu-id="24101-114">To activate the Image Tag Helper, the `src` attribute is required on the `<img>` element.</span></span>

<span data-ttu-id="24101-115">Źródło obrazu ( `src` ) musi wskazywać fizyczny plik statyczny na serwerze.</span><span class="sxs-lookup"><span data-stu-id="24101-115">The image source (`src`) must point to a physical static file on the server.</span></span> <span data-ttu-id="24101-116">Jeśli `src` jest to zdalny identyfikator URI, parametr ciągu zapytania cache-Busting nie jest generowany.</span><span class="sxs-lookup"><span data-stu-id="24101-116">If the `src` is a remote URI, the cache-busting query string parameter isn't generated.</span></span>

### <a name="asp-append-version"></a><span data-ttu-id="24101-117">ASP — dołączanie wersji</span><span class="sxs-lookup"><span data-stu-id="24101-117">asp-append-version</span></span>

<span data-ttu-id="24101-118">Gdy `asp-append-version` jest określony z `true` wartością wraz z `src` atrybutem, zostanie wywołana pomocnika znacznika obrazu.</span><span class="sxs-lookup"><span data-stu-id="24101-118">When `asp-append-version` is specified with a `true` value along with a `src` attribute, the Image Tag Helper is invoked.</span></span>

<span data-ttu-id="24101-119">Poniższy przykład używa pomocnika tagu obrazu:</span><span class="sxs-lookup"><span data-stu-id="24101-119">The following example uses an Image Tag Helper:</span></span>

```cshtml
<img src="~/images/asplogo.png" asp-append-version="true">
```

<span data-ttu-id="24101-120">Jeśli plik statyczny istnieje w katalogu */wwwroot/images/*, wygenerowany kod HTML jest podobny do następującego (skrót będzie różny):</span><span class="sxs-lookup"><span data-stu-id="24101-120">If the static file exists in the directory */wwwroot/images/*, the generated HTML is similar to the following (the hash will be different):</span></span>

```html
<img src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM">
```

<span data-ttu-id="24101-121">Wartość przypisana do parametru `v` jest wartością skrótu pliku *asplogo.png* na dysku.</span><span class="sxs-lookup"><span data-stu-id="24101-121">The value assigned to the parameter `v` is the hash value of the *asplogo.png* file on disk.</span></span> <span data-ttu-id="24101-122">Jeśli serwer sieci Web nie może uzyskać dostępu do odczytu do pliku statycznego, żaden `v` parametr nie zostanie dodany do `src` atrybutu w renderowanej adjustacji.</span><span class="sxs-lookup"><span data-stu-id="24101-122">If the web server is unable to obtain read access to the static file, no `v` parameter is added to the `src` attribute in the rendered markup.</span></span>

## <a name="hash-caching-behavior"></a><span data-ttu-id="24101-123">Zachowanie buforowania wartości skrótu</span><span class="sxs-lookup"><span data-stu-id="24101-123">Hash caching behavior</span></span>

<span data-ttu-id="24101-124">Pomocnik tagu obrazu używa dostawcy pamięci podręcznej na lokalnym serwerze sieci Web do przechowywania obliczonego `Sha512` skrótu danego pliku.</span><span class="sxs-lookup"><span data-stu-id="24101-124">The Image Tag Helper uses the cache provider on the local web server to store the calculated `Sha512` hash of a given file.</span></span> <span data-ttu-id="24101-125">Jeśli plik jest żądany wielokrotnie, skrót nie jest obliczany ponownie.</span><span class="sxs-lookup"><span data-stu-id="24101-125">If the file is requested multiple times, the hash isn't recalculated.</span></span> <span data-ttu-id="24101-126">Pamięć podręczna jest unieważniona przez obserwatora plików, który jest dołączony do pliku po `Sha512` obliczeniu skrótu pliku.</span><span class="sxs-lookup"><span data-stu-id="24101-126">The cache is invalidated by a file watcher that's attached to the file when the file's `Sha512` hash is calculated.</span></span> <span data-ttu-id="24101-127">Gdy plik zostanie zmieniony na dysku, zostanie obliczony i zbuforowany nowy skrót.</span><span class="sxs-lookup"><span data-stu-id="24101-127">When the file changes on disk, a new hash is calculated and cached.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="24101-128">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="24101-128">Additional resources</span></span>

* <xref:performance/caching/memory>
