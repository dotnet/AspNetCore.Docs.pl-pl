---
title: Udostępnianie kontrolerów, widoków, Razor stron i nie tylko za pomocą części aplikacji w ASP.NET Core
author: rick-anderson
description: Udostępnianie kontrolerów, widoku, Razor stron i innych elementów przy użyciu części aplikacji w ASP.NET Core
ms.author: riande
ms.date: 11/11/2019
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
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 33deb5ff794982e0c074186bb2abb88344e8a116
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061187"
---
# <a name="share-controllers-views-no-locrazor-pages-and-more-with-application-parts"></a>Udostępnianie kontrolerów, widoków, Razor stron i nie tylko za pomocą części aplikacji

::: moniker range=">= aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([jak pobrać](xref:index#how-to-download-a-sample))

*Część aplikacji* to Abstrakcja zasobów aplikacji. Części aplikacji umożliwiają ASP.NET Core odnajdywania kontrolerów, wyświetlania składników, pomocników tagów, Razor stron, źródeł kompilacji Razor i nie tylko. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> jest częścią aplikacji. `AssemblyPart` hermetyzuje odwołanie do zestawu i udostępnia typy i odwołania do kompilacji.

[Dostawcy funkcji](#fp) pracują z częściami aplikacji, aby wypełnić funkcje aplikacji ASP.NET Core. Głównym przypadkiem użycia części aplikacji jest skonfigurowanie aplikacji do odnajdywania (lub unikania ładowania) ASP.NET Core funkcji z zestawu. Na przykład może być konieczne udostępnienie typowych funkcji między wieloma aplikacjami. Korzystając z części aplikacji, można udostępnić zestaw (DLL) zawierający kontrolery, widoki, Razor strony, źródła kompilacji Razor, pomocników tagów i wiele więcej w przypadku wielu aplikacji. Udostępnianie zestawu jest preferowane do duplikowania kodu w wielu projektach.

ASP.NET Core aplikacje ładują funkcje z programu <xref:System.Web.WebPages.ApplicationPart> . <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>Klasa reprezentuje część aplikacji, która jest obsługiwana przez zestaw.

## <a name="load-aspnet-core-features"></a>Załaduj funkcje ASP.NET Core

Użyj <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> klas i, <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> Aby odnajdywać i ładować funkcje ASP.NET Core (kontrolery, składniki widoku itp.). <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager>Śledzi dostępne części aplikacji i dostawców funkcji. `ApplicationPartManager` jest skonfigurowany w `Startup.ConfigureServices` :

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

Poniższy kod stanowi alternatywny sposób konfigurowania `ApplicationPartManager` użycia `AssemblyPart` :

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

Powyższe dwa przykłady kodu ładują `SharedController` z zestawu. `SharedController`Nie znajduje się w projekcie aplikacji. Zobacz Pobieranie przykładowego [rozwiązania WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) .

### <a name="include-views"></a>Dołącz widoki

Użyj [ Razor biblioteki klas](xref:razor-pages/ui-class) , aby dołączyć widoki w zestawie.

### <a name="prevent-loading-resources"></a>Zapobiegaj ładowaniu zasobów

Części aplikacji mogą służyć do *uniknięcia* ładowania zasobów w określonym zestawie lub lokalizacji. Dodaj lub Usuń elementy członkowskie  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> kolekcji, aby ukryć lub udostępnić dostępne zasoby. Kolejność wpisów w `ApplicationParts` kolekcji nie jest ważna. Skonfiguruj `ApplicationPartManager` program przed użyciem go do konfigurowania usług w kontenerze. Na przykład skonfiguruj `ApplicationPartManager` przed wywołaniem `AddControllersAsServices` . Wywołaj `Remove` `ApplicationParts` kolekcję, aby usunąć zasób.

`ApplicationPartManager`Zawiera części dla:

* Zestaw aplikacji i zestawy zależne.
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

<a name="fp"></a>

## <a name="feature-providers"></a>Dostawcy funkcji

Dostawcy funkcji aplikacji badają części aplikacji i udostępniają funkcje dla tych części. Istnieją Wbudowani dostawcy funkcji dla następujących funkcji ASP.NET Core:

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* `internal class`[ Razor CompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)

Dostawcy funkcji dziedziczą z <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> , gdzie `T` jest typem funkcji. Dostawców funkcji można zaimplementować dla dowolnego z wcześniej wymienionych typów funkcji. Kolejność dostawców funkcji w programie `ApplicationPartManager.FeatureProviders` może mieć wpływ na zachowanie. Później dodani dostawcy mogą reagować na akcje podejmowane przez wcześniej dodanych dostawców.

### <a name="display-available-features"></a>Wyświetlanie dostępnych funkcji

Funkcje dostępne dla aplikacji można wyliczyć, żądając od `ApplicationPartManager` [iniekcji zależności](../../fundamentals/dependency-injection.md):

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

[Przykład pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) używa powyższego kodu, aby wyświetlić funkcje aplikacji:

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a>Odnajdywanie w częściach aplikacji

Błędy HTTP 404 nie są rzadko stosowane podczas tworzenia przy użyciu części aplikacji. Te błędy są zwykle spowodowane brakiem zasadniczego wymagania w zakresie odnajdywania części aplikacji. Jeśli aplikacja zwróci błąd HTTP 404, sprawdź, czy zostały spełnione następujące wymagania:

* `applicationName`Ustawienie musi być ustawione na zestaw główny używany do odnajdywania. Zestaw główny używany do odnajdywania jest zwykle zestawem punktów wejścia.
* Zestaw główny musi mieć odwołanie do części używanych do odnajdywania. Odwołanie może być bezpośrednie lub przechodnie.
* Zestaw główny musi odwoływać się do zestawu SDK sieci Web. Struktura ma logikę, która składa się z atrybutów w zestawie głównym używanym do odnajdywania.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([jak pobrać](xref:index#how-to-download-a-sample))

*Część aplikacji* to Abstrakcja zasobów aplikacji. Części aplikacji umożliwiają ASP.NET Core odnajdywania kontrolerów, wyświetlania składników, pomocników tagów, Razor stron, źródeł kompilacji Razor i nie tylko. [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) jest częścią aplikacji. `AssemblyPart` hermetyzuje odwołanie do zestawu i udostępnia typy i odwołania do kompilacji.

*Dostawcy funkcji* pracują z częściami aplikacji, aby wypełnić funkcje aplikacji ASP.NET Core. Głównym przypadkiem użycia części aplikacji jest skonfigurowanie aplikacji do odnajdywania (lub unikania ładowania) ASP.NET Core funkcji z zestawu. Na przykład może być konieczne udostępnienie typowych funkcji między wieloma aplikacjami. Korzystając z części aplikacji, można udostępnić zestaw (DLL) zawierający kontrolery, widoki, Razor strony, źródła kompilacji Razor, pomocników tagów i wiele więcej w przypadku wielu aplikacji. Udostępnianie zestawu jest preferowane do duplikowania kodu w wielu projektach.

ASP.NET Core aplikacje ładują funkcje z programu <xref:System.Web.WebPages.ApplicationPart> . <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>Klasa reprezentuje część aplikacji, która jest obsługiwana przez zestaw.

## <a name="load-aspnet-core-features"></a>Załaduj funkcje ASP.NET Core

Użyj `ApplicationPart` klas i, `AssemblyPart` Aby odnajdywać i ładować funkcje ASP.NET Core (kontrolery, składniki widoku itp.). <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager>Śledzi dostępne części aplikacji i dostawców funkcji. `ApplicationPartManager` jest skonfigurowany w `Startup.ConfigureServices` :

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

Poniższy kod stanowi alternatywny sposób konfigurowania `ApplicationPartManager` użycia `AssemblyPart` :

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

Powyższe dwa przykłady kodu ładują `SharedController` z zestawu. `SharedController`Nie znajduje się w projekcie aplikacji. Zobacz Pobieranie przykładowego [rozwiązania WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) .

### <a name="include-views"></a>Dołącz widoki

Użyj [ Razor biblioteki klas](xref:razor-pages/ui-class) , aby dołączyć widoki w zestawie.

### <a name="prevent-loading-resources"></a>Zapobiegaj ładowaniu zasobów

Części aplikacji mogą służyć do *uniknięcia* ładowania zasobów w określonym zestawie lub lokalizacji. Dodaj lub Usuń elementy członkowskie  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> kolekcji, aby ukryć lub udostępnić dostępne zasoby. Kolejność wpisów w `ApplicationParts` kolekcji nie jest ważna. Skonfiguruj `ApplicationPartManager` program przed użyciem go do konfigurowania usług w kontenerze. Na przykład skonfiguruj `ApplicationPartManager` przed wywołaniem `AddControllersAsServices` . Wywołaj `Remove` `ApplicationParts` kolekcję, aby usunąć zasób.

Następujący kod używa <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> do usunięcia `MyDependentLibrary` z aplikacji: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]

`ApplicationPartManager`Zawiera części dla:

* Zestaw aplikacji i zestawy zależne.
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

## <a name="application-feature-providers"></a>Dostawcy funkcji aplikacji

Dostawcy funkcji aplikacji badają części aplikacji i udostępniają funkcje dla tych części. Istnieją Wbudowani dostawcy funkcji dla następujących funkcji ASP.NET Core:

* [Kontrolery](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [Pomocnicy tagów](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [Wyświetl składniki](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

Dostawcy funkcji dziedziczą z <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> , gdzie `T` jest typem funkcji. Dostawców funkcji można zaimplementować dla dowolnego z wcześniej wymienionych typów funkcji. Kolejność dostawców funkcji w programie `ApplicationPartManager.FeatureProviders` może mieć wpływ na zachowanie. Później dodani dostawcy mogą reagować na akcje podejmowane przez wcześniej dodanych dostawców.

### <a name="display-available-features"></a>Wyświetlanie dostępnych funkcji

Funkcje dostępne dla aplikacji można wyliczyć, żądając od `ApplicationPartManager` [iniekcji zależności](../../fundamentals/dependency-injection.md):

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

[Przykład pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) używa powyższego kodu, aby wyświetlić funkcje aplikacji:

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a>Odnajdywanie w częściach aplikacji

Błędy HTTP 404 nie są rzadko stosowane podczas tworzenia przy użyciu części aplikacji. Te błędy są zwykle spowodowane brakiem zasadniczego wymagania w zakresie odnajdywania części aplikacji. Jeśli aplikacja zwróci błąd HTTP 404, sprawdź, czy zostały spełnione następujące wymagania:

* `applicationName`Ustawienie musi być ustawione na zestaw główny używany do odnajdywania. Zestaw główny używany do odnajdywania jest zwykle zestawem punktów wejścia.
* Zestaw główny musi mieć odwołanie do części używanych do odnajdywania. Odwołanie może być bezpośrednie lub przechodnie.
* Zestaw główny musi odwoływać się do zestawu SDK sieci Web.
  * Struktura ASP.NET Core ma niestandardową logikę kompilacji, która oznacza atrybuty w zestawie głównym, które są używane do odnajdywania.

::: moniker-end
