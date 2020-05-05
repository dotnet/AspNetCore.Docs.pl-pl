---
title: Obszary w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak obszary są funkcją ASP.NET MVC służącą do organizowania powiązanych funkcji w grupie jako oddzielnej przestrzeni nazw (dla routingu) i struktury folderów (dla widoków).
ms.author: riande
ms.date: 03/21/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/areas
ms.openlocfilehash: 00071b91f2ef374668b74a41190d49b0be83e817
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774304"
---
# <a name="areas-in-aspnet-core"></a>Obszary w ASP.NET Core

Autorzy [Dhananjay Kumara](https://twitter.com/debug_mode) i [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Obszary są funkcją ASP.NET używaną do organizowania powiązanych funkcji w grupie jako oddzielnej:

* Przestrzeń nazw dla routingu.
* Struktura folderów dla widoków i Razor stron.

Za pomocą obszarów tworzy hierarchię na potrzeby routingu przez dodanie innego parametru `area`trasy, do `controller` i `action` lub Razor strony. `page`

Obszary umożliwiają dzielenie aplikacji sieci Web na ASP.NET Core do mniejszych grup funkcjonalnych, z których każdy ma własny zestaw Razor stron, kontrolerów, widoków i modeli. Obszar jest efektywnie strukturą wewnątrz aplikacji. W projekcie sieci Web ASP.NET Core składniki logiczne, takie jak Pages, model, Controller i View, są przechowywane w różnych folderach. Środowisko uruchomieniowe ASP.NET Core używa konwencji nazewnictwa, aby utworzyć relację między tymi składnikami. W przypadku dużej aplikacji warto podzielić aplikację na oddzielne obszary wysokiego poziomu funkcji. Na przykład aplikacja handlu elektronicznego z wieloma jednostkami biznesowymi, takimi jak wyewidencjonowywanie, rozliczenia i wyszukiwanie. Każda z tych jednostek ma własny obszar, który zawiera widoki, kontrolery, Razor strony i modele.

Rozważ użycie obszarów w projekcie, gdy:

* Aplikacja składa się z wielu składników funkcjonalnych wysokiego poziomu, które można logicznie oddzielić.
* Chcesz podzielić aplikację na partycje, tak aby każdy obszar funkcjonalny mógł działać niezależnie.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([jak pobrać](xref:index#how-to-download-a-sample)). Przykład pobierania zawiera podstawową aplikację do testowania obszarów.

Jeśli używasz Razor stron, zobacz [obszary ze Razor stronami](#areas-with-razor-pages) w tym dokumencie.

## <a name="areas-for-controllers-with-views"></a>Obszary dla kontrolerów z widokami

Typowy ASP.NET Core aplikacja internetowa korzystająca z obszarów, kontrolerów i widoków zawiera następujące elementy:

* [Struktura folderów obszaru](#area-folder-structure).
* Kontrolery z [`[Area]`](#attribute) atrybutem, aby skojarzyć kontroler z obszarem:

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* [Trasa obszaru dodana do uruchamiania](#add-area-route):

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a>Struktura folderów obszaru

Weź pod uwagę aplikację, która ma dwie grupy logiczne, *produkty* i *usługi*. Przy użyciu obszarów struktura folderów będzie wyglądać podobnie do następujących:

* Project name (Nazwa projektu)
  * Obszary
    * Produkty
      * Kontrolery
        * HomeController.cs
        * ManageController.cs
      * Widoki
        * Domowy
          * Index.cshtml
        * Zarządzanie
          * Index.cshtml
          * About. cshtml
    * Usługi
      * Kontrolery
        * HomeController.cs
      * Widoki
        * Domowy
          * Index.cshtml

Chociaż poprzedni układ jest typowy w przypadku korzystania z obszarów, do korzystania z tej struktury folderów są wymagane tylko pliki widoku. Wyświetl wyszukiwania odnajdywania dla zgodnego pliku widoku obszaru w następującej kolejności:

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>Skojarz kontroler z obszarem

Kontrolery obszaru są oznaczone atrybutem [ &lbrack;obszaru&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>Dodaj trasę obszaru

Trasy obszaru zwykle używają [konwencjonalnego routingu](xref:mvc/controllers/routing#cr) , a nie [routingu atrybutu](xref:mvc/controllers/routing#ar). Routowanie konwencjonalne jest zależne od kolejności. Ogólnie rzecz biorąc, trasy z obszarami należy umieścić wcześniej w tabeli tras, ponieważ są one bardziej specyficzne niż trasy bez obszaru.

`{area:...}`może służyć jako token w szablonach tras, jeśli przestrzeń adresów URL jest jednolita dla wszystkich obszarów:

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

W poprzednim kodzie, `exists` stosuje ograniczenie, które musi być zgodne z obszarem. Używanie `{area:...}` z `MapControllerRoute`:

* To najmniej skomplikowany mechanizm dodawania routingu do obszarów.
* Dopasowuje wszystkie kontrolery z `[Area("Area name")]` atrybutem.

Poniższy kod używa <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> do tworzenia dwóch nazwanych tras obszaru:

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

Aby uzyskać więcej informacji, zobacz [Routing obszaru](xref:mvc/controllers/routing#areas).

### <a name="link-generation-with-mvc-areas"></a>Generowanie linków z obszarami MVC

Poniższy kod z pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) pokazuje generowanie linków z określonym obszarem:

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

Pobieranie próbek obejmuje [Widok częściowy](xref:mvc/views/partial) zawierający:

* Poprzednie linki.
* Linki podobne do poprzedniego Except `area` nie zostały określone.

Widok częściowy jest przywoływany w [pliku układu](xref:mvc/views/layout), więc każda Strona w aplikacji wyświetla wygenerowane linki. Linki wygenerowane bez określenia obszaru są prawidłowe tylko wtedy, gdy jest przywoływany ze strony w tym samym obszarze i kontrolerze.

Gdy nie określono obszaru lub kontrolera, routing zależy od wartości [otoczenia](xref:mvc/controllers/routing#ambient) . Bieżące wartości trasy bieżącego żądania są uznawane za wartości otoczenia dla generacji łącza. W wielu przypadkach dla przykładowej aplikacji użycie wartości otoczenia powoduje wygenerowanie nieprawidłowych linków ze znacznikiem, które nie określa obszaru.

Aby uzyskać więcej informacji, zobacz [routing do kontrolera akcji](xref:mvc/controllers/routing).

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a>Układ współużytkowany dla obszarów korzystających z pliku _ViewStart. cshtml

Aby udostępnić wspólny układ całej aplikacji, Zachowaj *_ViewStart. cshtml* w [folderze głównym aplikacji](#arf). Aby uzyskać więcej informacji, zobacz <xref:mvc/views/layout>.

<a name="arf"></a>

### <a name="application-root-folder"></a>Folder główny aplikacji

Folder główny aplikacji jest folderem zawierającym *Startup.cs* w aplikacji internetowej utworzonej za pomocą szablonów ASP.NET Core.

### <a name="_viewimportscshtml"></a>_ViewImports. cshtml

 */Views/_ViewImports. cshtml*, dla MVC i */Pages/_ViewImports. cshtml* dla Razor stron nie są importowane do widoków w obszarach. Użyj jednej z następujących metod, aby udostępnić widok Imports do wszystkich widoków:

* Dodaj *_ViewImports. cshtml* do [folderu głównego aplikacji](#arf). *_ViewImports. cshtml* w folderze głównym aplikacji zostanie zastosowane do wszystkich widoków w aplikacji.
* Skopiuj plik *_ViewImports. cshtml* do odpowiedniego folderu widoku w obszarze obszary.

Plik *_ViewImports. cshtml* [zazwyczaj zawiera instrukcje](xref:mvc/views/tag-helpers/intro) Imports, `@using`, i `@inject` . Aby uzyskać więcej informacji, zobacz [Importowanie wspólnych dyrektyw](xref:mvc/views/layout#importing-shared-directives).

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>Zmień domyślny folder obszaru, w którym są przechowywane widoki

Poniższy kod zmienia domyślny folder obszaru z `"Areas"` na: `"MyAreas"`

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a>Obszary ze Razor stronami

Obszary ze Razor stronami wymagają `Areas/<area name>/Pages` folderu w folderze głównym aplikacji. Następująca struktura folderów jest używana z [przykładową aplikacją](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):

* Project name (Nazwa projektu)
  * Obszary
    * Produkty
      * Pages
        * _ViewImports
        * Informacje
        * Indeks
    * Usługi
      * Pages
        * Zarządzanie
          * Informacje
          * Indeks

### <a name="link-generation-with-razor-pages-and-areas"></a>Generowanie linków przy Razor użyciu stron i obszarów

Poniższy kod z pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) pokazuje Generowanie łącza z określonym obszarem (na przykład `asp-area="Products"`):

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

Pobieranie próbek obejmuje [Widok częściowy](xref:mvc/views/partial) zawierający poprzednie linki i te same linki bez określania obszaru. Widok częściowy jest przywoływany w [pliku układu](xref:mvc/views/layout), więc każda Strona w aplikacji wyświetla wygenerowane linki. Linki wygenerowane bez określenia obszaru są prawidłowe tylko wtedy, gdy są przywoływane ze strony w tym samym obszarze.

Gdy obszar nie zostanie określony, routing zależy od wartości *otoczenia* . Bieżące wartości trasy bieżącego żądania są uznawane za wartości otoczenia dla generacji łącza. W wielu przypadkach dla przykładowej aplikacji korzystanie z wartości otoczenia powoduje wygenerowanie nieprawidłowych linków. Rozważmy na przykład linki wygenerowane na podstawie następującego kodu:

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

Dla poprzedniego kodu:

* Link wygenerowany z `<a asp-page="/Manage/About">` jest prawidłowy tylko wtedy, gdy ostatnie żądanie dotyczyło strony w `Services` obszarze. Na przykład `/Services/Manage/` `/Services/Manage/Index`,, lub `/Services/Manage/About`.
* Link wygenerowany z `<a asp-page="/About">` jest prawidłowy tylko wtedy, gdy ostatnie żądanie dotyczyło strony w `/Home`.
* Kod pochodzi z pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a>Importowanie przestrzeni nazw i pomocników tagów przy użyciu pliku _ViewImports

Plik *_ViewImports. cshtml* można dodać do każdego folderu *stron* obszaru, aby zaimportować przestrzeń nazw i Tagi pomocników do każdej Razor strony w folderze.

Weź pod uwagę obszar *usług* przykładowego kodu, który nie zawiera pliku *_ViewImports. cshtml* . Następujące znaczniki przedstawiają stronę */Services/Manage/about* Razor :

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

W powyższym znaczniku:

* W pełni kwalifikowana nazwa domeny musi zostać użyta do określenia modelu (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).
* [Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) są włączani przez`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

W przykładowym pobieranym obszarze produkty znajdują się następujące *_ViewImports. cshtml* :

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

Następujące znaczniki przedstawiają stronę */Products/about* Razor :

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

W poprzednim pliku przestrzeń nazw i `@addTagHelper` dyrektywa są importowane do pliku przez *obszary/produkty/strony/_ViewImports. cshtml* .

Aby uzyskać więcej informacji, zobacz [Zarządzanie zakresem pomocnika tagów](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) i [Importowanie wspólnych dyrektyw](xref:mvc/views/layout#importing-shared-directives).

### <a name="shared-layout-for-razor-pages-areas"></a>Układ współużytkowany dla Razor obszarów stron

Aby udostępnić wspólny układ całej aplikacji, Przenieś *_ViewStart. cshtml* do folderu głównego aplikacji.

### <a name="publishing-areas"></a>Publikowanie obszarów

Wszystkie pliki *. cshtml i pliki znajdujące się w katalogu *wwwroot* są publikowane w `<Project Sdk="Microsoft.NET.Sdk.Web">` danych wyjściowych, gdy są zawarte w pliku *. csproj.
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Obszary są funkcją ASP.NET używaną do organizowania powiązanych funkcji w grupie jako oddzielnej przestrzeni nazw (dla routingu) i struktury folderów (dla widoków). Za pomocą obszarów tworzy hierarchię na potrzeby routingu przez dodanie innego parametru `area`trasy, do `controller` i `action` lub Razor strony. `page`

Obszary umożliwiają dzielenie aplikacji sieci Web na ASP.NET Core do mniejszych grup funkcjonalnych, z których każdy ma własny zestaw Razor stron, kontrolerów, widoków i modeli. Obszar jest efektywnie strukturą wewnątrz aplikacji. W projekcie sieci Web ASP.NET Core składniki logiczne, takie jak Pages, model, Controller i View, są przechowywane w różnych folderach. Środowisko uruchomieniowe ASP.NET Core używa konwencji nazewnictwa, aby utworzyć relację między tymi składnikami. W przypadku dużej aplikacji warto podzielić aplikację na oddzielne obszary wysokiego poziomu funkcji. Na przykład aplikacja handlu elektronicznego z wieloma jednostkami biznesowymi, takimi jak wyewidencjonowywanie, rozliczenia i wyszukiwanie. Każda z tych jednostek ma własny obszar, który zawiera widoki, kontrolery, Razor strony i modele.

Rozważ użycie obszarów w projekcie, gdy:

* Aplikacja składa się z wielu składników funkcjonalnych wysokiego poziomu, które można logicznie oddzielić.
* Chcesz podzielić aplikację na partycje, tak aby każdy obszar funkcjonalny mógł działać niezależnie.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([jak pobrać](xref:index#how-to-download-a-sample)). Przykład pobierania zawiera podstawową aplikację do testowania obszarów.

Jeśli używasz Razor stron, zobacz [obszary ze Razor stronami](#areas-with-razor-pages) w tym dokumencie.

## <a name="areas-for-controllers-with-views"></a>Obszary dla kontrolerów z widokami

Typowy ASP.NET Core aplikacja internetowa korzystająca z obszarów, kontrolerów i widoków zawiera następujące elementy:

* [Struktura folderów obszaru](#area-folder-structure).
* Kontrolery z [`[Area]`](#attribute) atrybutem, aby skojarzyć kontroler z obszarem:

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* [Trasa obszaru dodana do uruchamiania](#add-area-route):

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a>Struktura folderów obszaru

Weź pod uwagę aplikację, która ma dwie grupy logiczne, *produkty* i *usługi*. Przy użyciu obszarów struktura folderów będzie wyglądać podobnie do następujących:

* Project name (Nazwa projektu)
  * Obszary
    * Produkty
      * Kontrolery
        * HomeController.cs
        * ManageController.cs
      * Widoki
        * Domowy
          * Index.cshtml
        * Zarządzanie
          * Index.cshtml
          * About. cshtml
    * Usługi
      * Kontrolery
        * HomeController.cs
      * Widoki
        * Domowy
          * Index.cshtml

Chociaż poprzedni układ jest typowy w przypadku korzystania z obszarów, do korzystania z tej struktury folderów są wymagane tylko pliki widoku. Wyświetl wyszukiwania odnajdywania dla zgodnego pliku widoku obszaru w następującej kolejności:

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>Skojarz kontroler z obszarem

Kontrolery obszaru są oznaczone atrybutem [ &lbrack;obszaru&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>Dodaj trasę obszaru

Trasy obszaru zwykle używają konwencjonalnego routingu, a nie routingu atrybutu. Routowanie konwencjonalne jest zależne od kolejności. Ogólnie rzecz biorąc, trasy z obszarami należy umieścić wcześniej w tabeli tras, ponieważ są one bardziej specyficzne niż trasy bez obszaru.

`{area:...}`może służyć jako token w szablonach tras, jeśli przestrzeń adresów URL jest jednolita dla wszystkich obszarów:

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

W poprzednim kodzie, `exists` stosuje ograniczenie, które musi być zgodne z obszarem. Korzystanie `{area:...}` z programu to najmniej skomplikowany mechanizm dodawania routingu do obszarów.

Poniższy kod używa <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> do tworzenia dwóch nazwanych tras obszaru:

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

W przypadku `MapAreaRoute` korzystania z programu z ASP.NET Core 2,2, zobacz [ten problem](https://github.com/dotnet/AspNetCore/issues/7772)w usłudze GitHub.

Aby uzyskać więcej informacji, zobacz [Routing obszaru](xref:mvc/controllers/routing#areas).

### <a name="link-generation-with-mvc-areas"></a>Generowanie linków z obszarami MVC

Poniższy kod z pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) pokazuje generowanie linków z określonym obszarem:

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

Linki generowane za pomocą powyższego kodu są prawidłowe w dowolnym miejscu aplikacji.

Pobieranie próbek obejmuje [Widok częściowy](xref:mvc/views/partial) zawierający poprzednie linki i te same linki bez określania obszaru. Widok częściowy jest przywoływany w [pliku układu](xref:mvc/views/layout), więc każda Strona w aplikacji wyświetla wygenerowane linki. Linki wygenerowane bez określenia obszaru są prawidłowe tylko wtedy, gdy jest przywoływany ze strony w tym samym obszarze i kontrolerze.

Gdy nie określono obszaru lub kontrolera, routing zależy od wartości *otoczenia* . Bieżące wartości trasy bieżącego żądania są uznawane za wartości otoczenia dla generacji łącza. W wielu przypadkach dla przykładowej aplikacji korzystanie z wartości otoczenia powoduje wygenerowanie nieprawidłowych linków.

Aby uzyskać więcej informacji, zobacz [routing do kontrolera akcji](xref:mvc/controllers/routing).

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a>Układ współużytkowany dla obszarów korzystających z pliku _ViewStart. cshtml

Aby udostępnić wspólny układ całej aplikacji, Przenieś *_ViewStart. cshtml* do folderu głównego aplikacji.

### <a name="_viewimportscshtml"></a>_ViewImports. cshtml

W swojej lokalizacji standardowej */Views/_ViewImports. cshtml* nie ma zastosowania do obszarów. Aby użyć wspólnych [pomocników tagów](xref:mvc/views/tag-helpers/intro), `@using`lub `@inject` w Twoim regionie, upewnij się, że odpowiedni plik *_ViewImports. cshtml* [ma zastosowanie do widoków obszaru](xref:mvc/views/layout#importing-shared-directives). Jeśli chcesz mieć takie samo zachowanie we wszystkich widokach, Przenieś */Views/_ViewImports. cshtml* do katalogu głównego aplikacji.

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>Zmień domyślny folder obszaru, w którym są przechowywane widoki

Poniższy kod zmienia domyślny folder obszaru z `"Areas"` na: `"MyAreas"`

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a>Obszary ze Razor stronami

Obszary ze Razor stronami wymagają `Areas/<area name>/Pages` folderu w folderze głównym aplikacji. Następująca struktura folderów jest używana z [przykładową aplikacją](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):

* Project name (Nazwa projektu)
  * Obszary
    * Produkty
      * Pages
        * _ViewImports
        * Informacje
        * Indeks
    * Usługi
      * Pages
        * Zarządzanie
          * Informacje
          * Indeks

### <a name="link-generation-with-razor-pages-and-areas"></a>Generowanie linków przy Razor użyciu stron i obszarów

Poniższy kod z pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) pokazuje Generowanie łącza z określonym obszarem (na przykład `asp-area="Products"`):

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

Linki generowane za pomocą powyższego kodu są prawidłowe w dowolnym miejscu aplikacji.

Pobieranie próbek obejmuje [Widok częściowy](xref:mvc/views/partial) zawierający poprzednie linki i te same linki bez określania obszaru. Widok częściowy jest przywoływany w [pliku układu](xref:mvc/views/layout), więc każda Strona w aplikacji wyświetla wygenerowane linki. Linki wygenerowane bez określenia obszaru są prawidłowe tylko wtedy, gdy są przywoływane ze strony w tym samym obszarze.

Gdy obszar nie zostanie określony, routing zależy od wartości *otoczenia* . Bieżące wartości trasy bieżącego żądania są uznawane za wartości otoczenia dla generacji łącza. W wielu przypadkach dla przykładowej aplikacji korzystanie z wartości otoczenia powoduje wygenerowanie nieprawidłowych linków. Rozważmy na przykład linki wygenerowane na podstawie następującego kodu:

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

Dla poprzedniego kodu:

* Link wygenerowany z `<a asp-page="/Manage/About">` jest prawidłowy tylko wtedy, gdy ostatnie żądanie dotyczyło strony w `Services` obszarze. Na przykład `/Services/Manage/` `/Services/Manage/Index`,, lub `/Services/Manage/About`.
* Link wygenerowany z `<a asp-page="/About">` jest prawidłowy tylko wtedy, gdy ostatnie żądanie dotyczyło strony w `/Home`.
* Kod pochodzi z pobranego [przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a>Importowanie przestrzeni nazw i pomocników tagów przy użyciu pliku _ViewImports

Plik *_ViewImports. cshtml* można dodać do każdego folderu *stron* obszaru, aby zaimportować przestrzeń nazw i Tagi pomocników do każdej Razor strony w folderze.

Weź pod uwagę obszar *usług* przykładowego kodu, który nie zawiera pliku *_ViewImports. cshtml* . Następujące znaczniki przedstawiają stronę */Services/Manage/about* Razor :

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

W powyższym znaczniku:

* W pełni kwalifikowana nazwa domeny musi zostać użyta do określenia modelu (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).
* [Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) są włączani przez`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

W przykładowym pobieranym obszarze produkty znajdują się następujące *_ViewImports. cshtml* :

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

Następujące znaczniki przedstawiają stronę */Products/about* Razor :

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

W poprzednim pliku przestrzeń nazw i `@addTagHelper` dyrektywa są importowane do pliku przez *obszary/produkty/strony/_ViewImports. cshtml* .

Aby uzyskać więcej informacji, zobacz [Zarządzanie zakresem pomocnika tagów](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) i [Importowanie wspólnych dyrektyw](xref:mvc/views/layout#importing-shared-directives).

### <a name="shared-layout-for-razor-pages-areas"></a>Układ współużytkowany dla Razor obszarów stron

Aby udostępnić wspólny układ całej aplikacji, Przenieś *_ViewStart. cshtml* do folderu głównego aplikacji.

### <a name="publishing-areas"></a>Publikowanie obszarów

Wszystkie pliki *. cshtml i pliki znajdujące się w katalogu *wwwroot* są publikowane w `<Project Sdk="Microsoft.NET.Sdk.Web">` danych wyjściowych, gdy są zawarte w pliku *. csproj.
::: moniker-end
