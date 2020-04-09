---
title: Rozszerzalność lokalizacji
author: hishamco
description: Dowiedz się, jak rozszerzyć interfejsy API lokalizacji w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2019
uid: fundamentals/localization-extensibility
ms.openlocfilehash: dfa2efe78b2e1e118e6b3f09bfc41f3330e1d721
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662100"
---
# <a name="localization-extensibility"></a><span data-ttu-id="cf822-103">Rozszerzalność lokalizacji</span><span class="sxs-lookup"><span data-stu-id="cf822-103">Localization Extensibility</span></span>

<span data-ttu-id="cf822-104">Przez [Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="cf822-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="cf822-105">W tym artykule:</span><span class="sxs-lookup"><span data-stu-id="cf822-105">This article:</span></span>

* <span data-ttu-id="cf822-106">Wyświetla listę punktów rozszerzalności w interfejsach API lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="cf822-106">Lists the extensibility points on the localization APIs.</span></span>
* <span data-ttu-id="cf822-107">Zawiera instrukcje dotyczące rozszerzania lokalizacji aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cf822-107">Provides instructions on how to extend ASP.NET Core app localization.</span></span>

## <a name="extensible-points-in-localization-apis"></a><span data-ttu-id="cf822-108">Rozszerzalne punkty w interfejsach API lokalizacji</span><span class="sxs-lookup"><span data-stu-id="cf822-108">Extensible Points in Localization APIs</span></span>

<span data-ttu-id="cf822-109">ASP.NET interfejsy API lokalizacji podstawowej są tworzone tak, aby były rozszerzalne.</span><span class="sxs-lookup"><span data-stu-id="cf822-109">ASP.NET Core localization APIs are built to be extensible.</span></span> <span data-ttu-id="cf822-110">Rozszerzalność umożliwia deweloperom dostosowanie lokalizacji zgodnie z ich potrzebami.</span><span class="sxs-lookup"><span data-stu-id="cf822-110">Extensibility allows developers to customize the localization according to their needs.</span></span> <span data-ttu-id="cf822-111">Na [przykład, OrchardCore](https://github.com/orchardCMS/OrchardCore/) ma `POStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="cf822-111">For instance, [OrchardCore](https://github.com/orchardCMS/OrchardCore/) has a `POStringLocalizer`.</span></span> <span data-ttu-id="cf822-112">`POStringLocalizer`opisuje szczegółowo przy użyciu lokalizacji `PO` obiektu [przenośnego](xref:fundamentals/portable-object-localization) do przechowywania zasobów lokalizacji za pomocą plików.</span><span class="sxs-lookup"><span data-stu-id="cf822-112">`POStringLocalizer` describes in detail using [Portable Object localization](xref:fundamentals/portable-object-localization) to use `PO` files to store localization resources.</span></span>

<span data-ttu-id="cf822-113">W tym artykule wymieniono dwa główne punkty rozszerzalności, które zapewniają interfejsy API lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="cf822-113">This article lists the two main extensibility points that localization APIs provide:</span></span> 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a><span data-ttu-id="cf822-114">Dostawcy kultury lokalizacji</span><span class="sxs-lookup"><span data-stu-id="cf822-114">Localization Culture Providers</span></span>

<span data-ttu-id="cf822-115">ASP.NET podstawowe interfejsy API lokalizacji mają czterech domyślnych dostawców, którzy mogą określić bieżącą kulturę żądania wykonywania:</span><span class="sxs-lookup"><span data-stu-id="cf822-115">ASP.NET Core localization APIs have four default providers that can determine the current culture of an executing request:</span></span>

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

<span data-ttu-id="cf822-116">Poprzedni dostawcy są szczegółowo opisane w dokumentacji [oprogramowania pośredniczącego lokalizacji.](xref:fundamentals/localization)</span><span class="sxs-lookup"><span data-stu-id="cf822-116">The preceding providers are described in detail in the [Localization Middleware](xref:fundamentals/localization) documentation.</span></span> <span data-ttu-id="cf822-117">Jeśli dostawcy domyślni nie spełniają Twoich potrzeb, stwórz niestandardowego dostawcę przy użyciu jednego z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="cf822-117">If the default providers don't meet your needs, build a custom provider using one of the following approaches:</span></span>

### <a name="use-customrequestcultureprovider"></a><span data-ttu-id="cf822-118">Użyj programu CustomRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="cf822-118">Use CustomRequestCultureProvider</span></span>

<span data-ttu-id="cf822-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>zawiera niestandardowe, <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> które używa prostego delegata do określenia bieżącej kultury lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="cf822-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> provides a custom <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> that uses a simple delegate to determine the current localization culture:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(culture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(culture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a><span data-ttu-id="cf822-120">Użyj nowego implemetation RequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="cf822-120">Use a new implemetation of RequestCultureProvider</span></span>

<span data-ttu-id="cf822-121">Można utworzyć <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> nową implementację, która określa informacje o kulturze żądania z niestandardowego źródła.</span><span class="sxs-lookup"><span data-stu-id="cf822-121">A new implementation of <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> can be created that determines the request culture information from a custom source.</span></span> <span data-ttu-id="cf822-122">Na przykład źródłem niestandardowym może być plik konfiguracyjny lub baza danych.</span><span class="sxs-lookup"><span data-stu-id="cf822-122">For example, the custom source can be a configuration file or database.</span></span>

<span data-ttu-id="cf822-123">Poniższy przykład `AppSettingsRequestCultureProvider`pokazuje , <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> który rozszerza, aby określić informacje o kulturze żądania z *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="cf822-123">The following example shows `AppSettingsRequestCultureProvider`, which extends the <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> to determine the request culture information from *appsettings.json*:</span></span>

```csharp
public class AppSettingsRequestCultureProvider : RequestCultureProvider
{
    public string CultureKey { get; set; } = "culture";

    public string UICultureKey { get; set; } = "ui-culture";

    public override Task<ProviderCultureResult> DetermineProviderCultureResult(HttpContext httpContext)
    {
        if (httpContext == null)
        {
            throw new ArgumentNullException();
        }

        var configuration = httpContext.RequestServices.GetService<IConfigurationRoot>();
        var culture = configuration[CultureKey];
        var uiCulture = configuration[UICultureKey];

        if (culture == null && uiCulture == null)
        {
            return Task.FromResult((ProviderCultureResult)null);
        }

        if (culture != null && uiCulture == null)
        {
            uiCulture = culture;
        }

        if (culture == null && uiCulture != null)
        {
            culture = uiCulture;
        }
        
        var providerResultCulture = new ProviderCultureResult(culture, uiCulture);

        return Task.FromResult(providerResultCulture);
    }
}
```

## <a name="localization-resources"></a><span data-ttu-id="cf822-124">Zasoby lokalizacyjne</span><span class="sxs-lookup"><span data-stu-id="cf822-124">Localization resources</span></span>

<span data-ttu-id="cf822-125">ASP.NET Zapewnia lokalizacja rdzenia <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>.</span><span class="sxs-lookup"><span data-stu-id="cf822-125">ASP.NET Core localization provides <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>.</span></span> <span data-ttu-id="cf822-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>jest implementacją, <xref:Microsoft.Extensions.Localization.IStringLocalizer> która `resx` jest używana do przechowywania zasobów lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="cf822-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> is an implementation of <xref:Microsoft.Extensions.Localization.IStringLocalizer> that is uses `resx` to store localization resources.</span></span>

<span data-ttu-id="cf822-127">Nie ograniczasz się `resx` do używania plików.</span><span class="sxs-lookup"><span data-stu-id="cf822-127">You aren't limited to using `resx` files.</span></span> <span data-ttu-id="cf822-128">Implementując `IStringLocalized`, można użyć dowolnego źródła danych.</span><span class="sxs-lookup"><span data-stu-id="cf822-128">By implementing `IStringLocalized`, any data source can be used.</span></span>

<span data-ttu-id="cf822-129">Poniższe przykładowe <xref:Microsoft.Extensions.Localization.IStringLocalizer>projekty implementują:</span><span class="sxs-lookup"><span data-stu-id="cf822-129">The following example projects implement <xref:Microsoft.Extensions.Localization.IStringLocalizer>:</span></span> 

* [<span data-ttu-id="cf822-130">EFStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="cf822-130">EFStringLocalizer</span></span>](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [<span data-ttu-id="cf822-131">JsonStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="cf822-131">JsonStringLocalizer</span></span>](https://github.com/hishamco/My.Extensions.Localization.Json)
* [<span data-ttu-id="cf822-132">SqlLocalizer</span><span class="sxs-lookup"><span data-stu-id="cf822-132">SqlLocalizer</span></span>](https://github.com/damienbod/AspNetCoreLocalization)
