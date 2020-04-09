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
# <a name="localization-extensibility"></a>Rozszerzalność lokalizacji

Przez [Hisham Bin Ateya](https://github.com/hishamco)

W tym artykule:

* Wyświetla listę punktów rozszerzalności w interfejsach API lokalizacji.
* Zawiera instrukcje dotyczące rozszerzania lokalizacji aplikacji ASP.NET Core.

## <a name="extensible-points-in-localization-apis"></a>Rozszerzalne punkty w interfejsach API lokalizacji

ASP.NET interfejsy API lokalizacji podstawowej są tworzone tak, aby były rozszerzalne. Rozszerzalność umożliwia deweloperom dostosowanie lokalizacji zgodnie z ich potrzebami. Na [przykład, OrchardCore](https://github.com/orchardCMS/OrchardCore/) ma `POStringLocalizer`. `POStringLocalizer`opisuje szczegółowo przy użyciu lokalizacji `PO` obiektu [przenośnego](xref:fundamentals/portable-object-localization) do przechowywania zasobów lokalizacji za pomocą plików.

W tym artykule wymieniono dwa główne punkty rozszerzalności, które zapewniają interfejsy API lokalizacji: 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a>Dostawcy kultury lokalizacji

ASP.NET podstawowe interfejsy API lokalizacji mają czterech domyślnych dostawców, którzy mogą określić bieżącą kulturę żądania wykonywania:

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

Poprzedni dostawcy są szczegółowo opisane w dokumentacji [oprogramowania pośredniczącego lokalizacji.](xref:fundamentals/localization) Jeśli dostawcy domyślni nie spełniają Twoich potrzeb, stwórz niestandardowego dostawcę przy użyciu jednego z następujących metod:

### <a name="use-customrequestcultureprovider"></a>Użyj programu CustomRequestCultureProvider

<xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>zawiera niestandardowe, <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> które używa prostego delegata do określenia bieżącej kultury lokalizacji:

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

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a>Użyj nowego implemetation RequestCultureProvider

Można utworzyć <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> nową implementację, która określa informacje o kulturze żądania z niestandardowego źródła. Na przykład źródłem niestandardowym może być plik konfiguracyjny lub baza danych.

Poniższy przykład `AppSettingsRequestCultureProvider`pokazuje , <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> który rozszerza, aby określić informacje o kulturze żądania z *appsettings.json:*

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

## <a name="localization-resources"></a>Zasoby lokalizacyjne

ASP.NET Zapewnia lokalizacja rdzenia <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>. <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>jest implementacją, <xref:Microsoft.Extensions.Localization.IStringLocalizer> która `resx` jest używana do przechowywania zasobów lokalizacji.

Nie ograniczasz się `resx` do używania plików. Implementując `IStringLocalized`, można użyć dowolnego źródła danych.

Poniższe przykładowe <xref:Microsoft.Extensions.Localization.IStringLocalizer>projekty implementują: 

* [EFStringLocalizer](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [JsonStringLocalizer](https://github.com/hishamco/My.Extensions.Localization.Json)
* [SqlLocalizer](https://github.com/damienbod/AspNetCoreLocalization)
