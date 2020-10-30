---
title: Rozszerzalność lokalizacji
author: hishamco
description: Dowiedz się, jak rozłożyć interfejsy API lokalizacji w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2019
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
uid: fundamentals/localization-extensibility
ms.openlocfilehash: a6ef5a547e6ccba6771cdf892a9636f83d6796b1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053738"
---
# <a name="localization-extensibility"></a>Rozszerzalność lokalizacji

Według [Hisham bin Ateya](https://github.com/hishamco)

W tym artykule:

* Wyświetla listę punktów rozszerzalności w interfejsach API lokalizacji.
* Zawiera instrukcje dotyczące sposobu rozbudowania lokalizacji aplikacji ASP.NET Core.

## <a name="extensible-points-in-localization-apis"></a>Rozszerzalne punkty w interfejsach API lokalizacji

Interfejsy API lokalizacji ASP.NET Core są kompilowane do rozszerzalności. Rozszerzalność umożliwia deweloperom dostosowanie lokalizacji zgodnie z ich potrzebami. Na przykład [OrchardCore](https://github.com/orchardCMS/OrchardCore/) ma `POStringLocalizer` . `POStringLocalizer` szczegółowo opisano przy użyciu [lokalizacji obiektów przenośnych](xref:fundamentals/portable-object-localization) do `PO` przechowywania zasobów lokalizacji.

W tym artykule wymieniono dwa główne punkty rozszerzalności zapewniane przez interfejsy API lokalizacji: 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a>Dostawcy kultury lokalizacji

Interfejsy API lokalizacji ASP.NET Core mają czterech dostawców domyślnych, którzy mogą określić bieżącą kulturę żądania wykonania:

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

Poprzedni dostawcy są szczegółowo opisane w dokumentacji [oprogramowania pośredniczącego](xref:fundamentals/localization) . Jeśli dostawcy domyślnie nie spełnią Twoich potrzeb, należy utworzyć niestandardowego dostawcę przy użyciu jednej z następujących metod:

### <a name="use-customrequestcultureprovider"></a>Użyj CustomRequestCultureProvider

<xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> zapewnia niestandardowy <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> , który używa prostego delegata do określenia bieżącej kultury lokalizacji:

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

    var requestCulture = new ProviderCultureResult(currentCulture);
    
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

    var requestCulture = new ProviderCultureResult(currentCulture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a>Użyj nowego Jawna implementacja RequestCultureProvider

Można utworzyć nową implementację <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> , która określa informacje o kulturze żądania ze źródła niestandardowego. Na przykład źródło niestandardowe może być plikiem konfiguracji lub bazą danych.

Poniższy przykład pokazuje `AppSettingsRequestCultureProvider` , że rozszerza, <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> Aby określić informacje o kulturze żądań z *appsettings.json* :

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

## <a name="localization-resources"></a>Zasoby lokalizacji

Zapewnia ASP.NET Core lokalizacji <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> . <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> jest implementacją programu <xref:Microsoft.Extensions.Localization.IStringLocalizer> , która jest stosowana `resx` do przechowywania zasobów lokalizacji.

Nie można używać `resx` plików. Implementując `IStringLocalized` , można użyć dowolnego źródła danych.

Następujące przykładowe projekty implementują <xref:Microsoft.Extensions.Localization.IStringLocalizer> : 

* [EFStringLocalizer](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [JsonStringLocalizer](https://github.com/hishamco/My.Extensions.Localization.Json)
* [Sqllokalizowaer](https://github.com/damienbod/AspNetCoreLocalization)
