---
title: Rozwiązywanie problemów z lokalizacją ASP.NET rdzenia
author: hishamco
description: Dowiedz się, jak zdiagnozować problemy z lokalizacją w aplikacjach ASP.NET Core.
ms.author: riande
ms.date: 01/24/2019
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 229e274a22e170d984a16d3b1ee64ebc38c4ef77
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660378"
---
# <a name="troubleshoot-aspnet-core-localization"></a>Rozwiązywanie problemów z lokalizacją ASP.NET rdzenia

Przez [Hisham Bin Ateya](https://github.com/hishamco)

Ten artykuł zawiera instrukcje dotyczące diagnozowania problemów z lokalizacją aplikacji ASP.NET Core.

## <a name="localization-configuration-issues"></a>Problemy z konfiguracją lokalizacji

**Kolejność oprogramowania pośredniczącego lokalizacji**  
Aplikacja może nie lokalizować, ponieważ oprogramowanie pośredniczące lokalizacji nie jest uporządkowane zgodnie z oczekiwaniami.

Aby rozwiązać ten problem, upewnij się, że oprogramowanie pośredniczące lokalizacji jest zarejestrowane przed oprogramowaniem pośredniczącym MVC. W przeciwnym razie oprogramowanie pośredniczące lokalizacji nie jest stosowane.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

**Nie znaleziono ścieżki zasobów lokalizacyjnych**

**Obsługiwane kultury w RequestCultureProvider nie pasują do zarejestrowanych raz**  

## <a name="resource-file-naming-issues"></a>Problemy z nazewnictwem plików zasobów

ASP.NET Core ma wstępnie zdefiniowane reguły i wytyczne dotyczące nazewnictwa plików zasobów lokalizacyjnych, które są szczegółowo opisane w [tym miejscu](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).

## <a name="missing-resources"></a>Brakujące zasoby

Typowe przyczyny nieuleczania zasobów obejmują:

- Nazwy zasobów są błędnie wpisane w `resx` pliku lub żądania lokalizatora.
- Zasób brakuje `resx` w niektórych językach, ale istnieje w innych.
- Jeśli nadal występują problemy, sprawdź komunikaty dziennika lokalizacji `Debug` (które znajdują się na poziomie dziennika), aby uzyskać więcej informacji na temat brakujących zasobów.

_**Podpowiedź:** Podczas `CookieRequestCultureProvider`korzystania z , sprawdź pojedyncze cudzysłowy nie są używane z kultur wewnątrz wartości pliku cookie lokalizacji. Na przykład `c='en-UK'|uic='en-US'` jest nieprawidłową wartością pliku cookie, gdy `c=en-UK|uic=en-US` jest prawidłowa._

## <a name="resources--class-libraries-issues"></a>Problemy z bibliotekami & zasobów

ASP.NET Core domyślnie umożliwia bibliotekom klas znajdowanie plików zasobów za pośrednictwem [atrybutu ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).

Typowe problemy z bibliotekami klas obejmują:
- Brak `ResourceLocationAttribute` w bibliotece klas `ResourceManagerStringLocalizerFactory` uniemożliwi odnajdowanie zasobów.
- Nazewnictwo plików zasobów. Aby uzyskać więcej informacji, zobacz Sekcja [Problemy z nazewnictwem plików zasobów.](#resource-file-naming-issues)
- Zmiana głównego obszaru nazw biblioteki klas. Aby uzyskać więcej informacji, zobacz [sekcję Problemy z głównym obszarem nazw.](#root-namespace-issues)

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a>CustomRequestCultureProvider nie działa zgodnie z oczekiwaniami

Klasa `RequestLocalizationOptions` ma trzech domyślnych dostawców:

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) umożliwia dostosowanie, jak kultura lokalizacji jest dostępna w aplikacji. Jest `CustomRequestCultureProvider` używany, gdy dostawcy domyślni nie spełniają twoich wymagań.

- Częstym powodem, dla którego dostawca niestandardowy nie działa poprawnie, `RequestCultureProviders` jest to, że nie jest pierwszym dostawcą na liście. Aby rozwiązać ten problem:

- Wstaw dostawcę niestandardowego w `RequestCultureProviders` pozycji 0 na liście jako następujące:

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

- Użyj `AddInitialRequestCultureProvider` metody rozszerzenia, aby ustawić dostawcę niestandardowego jako dostawcę początkowego.

## <a name="root-namespace-issues"></a>Problemy z głównym obszarem nazw

Gdy główny obszar nazw zestawu jest inny niż nazwa zestawu, lokalizacja nie działa domyślnie. Aby uniknąć tego problemu, użyj [rootnamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), który jest szczegółowo opisany [tutaj](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)

> [!WARNING]
> Może to nastąpić, gdy nazwa projektu nie jest prawidłowym identyfikatorem platformy .NET. Na `my-project-name.csproj` przykład użyje głównego `my_project_name` obszaru nazw `my-project-name` i nazwy zestawu prowadzącego do tego błędu. 

## <a name="resources--build-action"></a>Zasoby & akcji kompilacji

Jeśli używasz plików zasobów do lokalizacji, ważne jest, że mają one odpowiednią akcję kompilacji. Powinny one być **osadzone zasobów,** w `ResourceStringLocalizer` przeciwnym razie nie jest w stanie znaleźć te zasoby.
