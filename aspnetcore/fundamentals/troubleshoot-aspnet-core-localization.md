---
title: Rozwiązywanie problemów z lokalizacją ASP.NET Core
author: hishamco
description: Dowiedz się, jak zdiagnozować problemy z lokalizacją w aplikacjach ASP.NET Core.
ms.author: riande
ms.date: 01/24/2019
no-loc:
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
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 1cb7fb61063bf888819e940476f27c3adafa24de
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629928"
---
# <a name="troubleshoot-aspnet-core-localization"></a>Rozwiązywanie problemów z lokalizacją ASP.NET Core

Według [Hisham bin Ateya](https://github.com/hishamco)

Ten artykuł zawiera instrukcje dotyczące sposobu diagnozowania ASP.NET Core problemów z lokalizacją aplikacji.

## <a name="localization-configuration-issues"></a>Problemy z konfiguracją lokalizacji

**Kolejność oprogramowania pośredniczącego lokalizacji**  
Aplikacja może nie być zlokalizowana, ponieważ oprogramowanie pośredniczące nie jest uporządkowane zgodnie z oczekiwaniami.

Aby rozwiązać ten problem, upewnij się, że oprogramowanie pośredniczące do lokalizowania jest zarejestrowane przed oprogramowaniem pośredniczącym MVC. W przeciwnym razie nie zostanie zastosowane oprogramowanie pośredniczące lokalizacji.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

**Nie znaleziono ścieżki zasobów lokalizacji**

**Obsługiwane kultury w RequestCultureProvider nie pasują do zarejestrowanego wystąpienia**  

## <a name="resource-file-naming-issues"></a>Problemy z nazewnictwem plików zasobów

ASP.NET Core ma wstępnie zdefiniowane reguły i wskazówki dotyczące nazewnictwa plików zasobów lokalizacyjnych, które opisano szczegółowo w [tym miejscu](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).

## <a name="missing-resources"></a>Brakujące zasoby

Typowe przyczyny nieznalezienia zasobów obejmują:

- Nazwy zasobów są błędne w `resx` pliku lub w żądaniu lokalizatora.
- Brak zasobu `resx` w przypadku niektórych języków, ale istnieje w innych.
- Jeśli nadal występują problemy, sprawdź komunikaty dziennika lokalizacji (które znajdują się na `Debug` poziomie dziennika), aby uzyskać więcej informacji na temat brakujących zasobów.

_**Wskazówka:** W przypadku korzystania `CookieRequestCultureProvider` z programu Sprawdź, czy pojedyncze cudzysłowy nie są używane z kulturami wewnątrz cookie wartości lokalizacji. Na przykład `c='en-UK'|uic='en-US'` jest nieprawidłową cookie wartością, podczas gdy `c=en-UK|uic=en-US` jest prawidłowy._

## <a name="resources--class-libraries-issues"></a>Zasoby & problemy z bibliotekami klas

ASP.NET Core domyślnie oferuje sposób zezwalania bibliotekom klas na Znajdowanie plików zasobów za pośrednictwem [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).

Typowe problemy związane z bibliotekami klas obejmują:
- Brak `ResourceLocationAttribute` elementu w bibliotece klas uniemożliwi `ResourceManagerStringLocalizerFactory` odnajdywanie zasobów.
- Nazewnictwo plików zasobów. Aby uzyskać więcej informacji, zobacz sekcję [problemy związane z nazewnictwem plików zasobów](#resource-file-naming-issues) .
- Zmiana głównej przestrzeni nazw biblioteki klas. Aby uzyskać więcej informacji, zobacz sekcję [problemy dotyczące głównej przestrzeni nazw](#root-namespace-issues) .

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a>CustomRequestCultureProvider nie działa zgodnie z oczekiwaniami

`RequestLocalizationOptions`Klasa ma trzech dostawców domyślnych:

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) umożliwia dostosowanie sposobu, w jaki kultura lokalizacji jest udostępniana w aplikacji. `CustomRequestCultureProvider`Jest używany, gdy dostawcy domyślnie nie spełniają Twoich wymagań.

- Typowy powód niestandardowego dostawcy nie działa prawidłowo, ponieważ nie jest to pierwszy dostawca na `RequestCultureProviders` liście. Aby rozwiązać ten problem:

- Wstaw dostawcę niestandardowego na pozycji 0 na `RequestCultureProviders` liście w następujący sposób:

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

## <a name="root-namespace-issues"></a>Problemy z główną przestrzenią nazw

Gdy główna przestrzeń nazw zestawu różni się od nazwy zestawu, lokalizacja nie działa domyślnie. Aby uniknąć tego problemu, użyj [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), który jest szczegółowo opisany [tutaj](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)

> [!WARNING]
> Taka sytuacja może wystąpić, gdy nazwa projektu nie jest prawidłowym identyfikatorem platformy .NET. Na przykład `my-project-name.csproj` użyje głównej przestrzeni nazw `my_project_name` i nazwy zestawu `my-project-name` prowadzącej do tego błędu. 

## <a name="resources--build-action"></a>Akcja kompilacji & zasobów

W przypadku używania plików zasobów do lokalizacji należy pamiętać, że mają one odpowiednią akcję kompilacji. Powinny to być **zasoby osadzone**, w przeciwnym razie `ResourceStringLocalizer` nie można znaleźć tych zasobów.
