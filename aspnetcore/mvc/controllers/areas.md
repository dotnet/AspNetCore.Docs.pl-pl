---
title: Obszary w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak obszary są ASP.NET funkcją MVC używaną do organizowania powiązanych funkcji w grupę jako oddzielny obszar nazw (do routingu) i strukturę folderów (dla widoków).
ms.author: riande
ms.date: 03/21/2019
uid: mvc/controllers/areas
ms.openlocfilehash: 8859bc52416ff657036198c73f63b8b0a0201e11
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80625932"
---
# <a name="areas-in-aspnet-core"></a>Obszary w ASP.NET Core

Przez [Dhananjay Kumar](https://twitter.com/debug_mode) i [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Obszary są ASP.NET funkcją służącą do organizowania powiązanych funkcji w grupie jako osobnej:

* Obszar nazw dla routingu.
* Struktura folderów dla widoków i stron Razor.

Za pomocą obszarów tworzy hierarchię do celów routingu, `action` dodając inny parametr `page`trasy, `area`, do `controller` lub razor Strony .

Obszary umożliwiają podział ASP.NET podstawowej aplikacji sieci Web na mniejsze grupy funkcjonalne, z których każda ma własny zestaw stron Razor, kontrolerów, widoków i modeli. Obszar jest skutecznie strukturą wewnątrz aplikacji. W projekcie sieci Web ASP.NET Core składniki logiczne, takie jak Strony, Model, Kontroler i Widok, są przechowywane w różnych folderach. Środowisko uruchomieniowe ASP.NET Core używa konwencji nazewnictwa do tworzenia relacji między tymi składnikami. W przypadku dużej aplikacji może być korzystne partycjonowanie aplikacji na oddzielne obszary wysokiego poziomu funkcjonalności. Na przykład aplikacja e-commerce z wieloma jednostkami biznesowymi, takimi jak kasa, rozliczenia i wyszukiwanie. Każda z tych jednostek ma swój własny obszar zawierający widoki, kontrolery, strony razor i modele.

Rozważ użycie obszarów w projekcie, gdy:

* Aplikacja składa się z wielu składników funkcjonalnych wysokiego poziomu, które można logicznie rozdzielić.
* Chcesz podzielić aplikację, aby każdy obszar funkcjonalny mógł pracować niezależnie.

[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) [(jak pobrać).](xref:index#how-to-download-a-sample) Przykład pobierania zawiera podstawową aplikację do testowania obszarów.

Jeśli używasz stron Razor, zobacz [Obszary ze stronami Razor](#areas-with-razor-pages) w tym dokumencie.

## <a name="areas-for-controllers-with-views"></a>Obszary dla kontrolerów z widokami

Typowa aplikacja sieci Web ASP.NET Core przy użyciu obszarów, kontrolerów i widoków zawiera następujące elementy:

* [Struktura folderów Obszar](#area-folder-structure).
* Kontrolery z [`[Area]`](#attribute) atrybutem, aby skojarzyć kontroler z obszarem:

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* [Trasa obszaru dodana do uruchomienia:](#add-area-route)

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a>Struktura folderów obszarowych

Należy wziąć pod uwagę aplikację, która ma dwie grupy logiczne, *Produkty* i *usługi*. Przy użyciu obszarów struktura folderów będzie podobna do następującej:

* Project name (Nazwa projektu)
  * Obszary
    * Produkty
      * Kontrolery
        * HomeController.cs
        * ManageController.cs
      * Widoki
        * Strona główna
          * Index.cshtml
        * Zarządzanie
          * Index.cshtml
          * Informacje.cshtml
    * Usługi
      * Kontrolery
        * HomeController.cs
      * Widoki
        * Strona główna
          * Index.cshtml

Podczas gdy poprzedni układ jest typowy podczas korzystania z obszarów, tylko pliki widoku są wymagane do korzystania z tej struktury folderów. Wyświetl wyszukiwanie odnajdowania pasującego pliku widoku obszaru w następującej kolejności:

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>Skojarzenie kontrolera z obszarem

Kontrolery obszarów są oznaczone atrybutem [ &lbrack;Obszar:&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>Dodaj trasę obszaru

Trasy obszarowe zazwyczaj używają [routingu konwencjonalnego,](xref:mvc/controllers/routing#cr) a nie [routingu atrybutów](xref:mvc/controllers/routing#ar). Routing konwencjonalny jest zależny od zamówienia. Ogólnie rzecz biorąc, trasy z obszarami powinny być umieszczane wcześniej w tabeli tras, ponieważ są bardziej szczegółowe niż trasy bez obszaru.

`{area:...}`może służyć jako token w szablonach trasy, jeśli przestrzeń adresów URL jest jednolita we wszystkich obszarach:

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

W poprzednim kodzie `exists` stosuje ograniczenie, które trasa musi pasować do obszaru. Korzystanie `{area:...}` `MapControllerRoute`z:

* Jest najmniej skomplikowanym mechanizmem dodawania routingu do obszarów.
* Dopasowuje wszystkie `[Area("Area name")]` kontrolery z atrybutem.

Poniższy kod <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> służy do tworzenia dwóch nazwanych tras obszaru:

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

Aby uzyskać więcej informacji, zobacz [Routing obszaru](xref:mvc/controllers/routing#areas).

### <a name="link-generation-with-mvc-areas"></a>Generowanie łączy z obszarami MVC

Poniższy kod z [przykładowego pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) pokazuje generowanie łączy z określonym obszarem:

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

Przykładowe pobieranie zawiera [widok częściowy,](xref:mvc/views/partial) który zawiera:

* Powyższe łącza.
* Łącza podobne do poprzedniego `area` z wyjątkiem nie jest określony.

Widok częściowy odwołuje się do [pliku układu,](xref:mvc/views/layout)więc każda strona w aplikacji wyświetla wygenerowane łącza. Łącza generowane bez określania obszaru są prawidłowe tylko wtedy, gdy odwołuje się do strony w tym samym obszarze i kontrolera.

Jeśli obszar lub kontroler nie jest określony, routing zależy od wartości [otoczenia.](xref:mvc/controllers/routing#ambient) Bieżące wartości trasy bieżącego żądania są traktowane jako wartości otoczenia dla generowania łączy. W wielu przypadkach dla przykładowej aplikacji przy użyciu wartości otoczenia generuje niepoprawne łącza z znaczników, które nie określają obszaru.

Aby uzyskać więcej informacji, zobacz [Routing do akcji kontrolera](xref:mvc/controllers/routing).

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a>Układ udostępniony dla obszarów przy użyciu pliku _ViewStart.cshtml

Aby udostępnić wspólny układ dla całej aplikacji, zachowaj *_ViewStart.cshtml* w [folderze głównym aplikacji](#arf). Aby uzyskać więcej informacji, zobacz <xref:mvc/views/layout>.

<a name="arf"></a>

### <a name="application-root-folder"></a>Folder główny aplikacji

Folder główny aplikacji to folder zawierający *Startup.cs* w aplikacji internetowej utworzonej za pomocą szablonów ASP.NET Core.

### <a name="_viewimportscshtml"></a>_ViewImports.cshtml

 */Views/_ViewImports.cshtml*, dla MVC i */Pages/_ViewImports.cshtml* dla stron Razor, nie jest importowany do widoków w obszarach. Użyj jednego z następujących podejść, aby zapewnić import widoku do wszystkich widoków:

* Dodaj *_ViewImports.cshtml* do [folderu głównego aplikacji](#arf). *_ViewImports.cshtml* w folderze głównym aplikacji będzie stosowany do wszystkich widoków w aplikacji.
* Skopiuj plik *_ViewImports.cshtml* do odpowiedniego folderu widoku w obszarze obszary.

Plik *_ViewImports.cshtml* zazwyczaj zawiera [importowanie](xref:mvc/views/tag-helpers/intro) pomocników znaczników `@inject` i instrukcje. `@using` Aby uzyskać więcej informacji, zobacz [Importowanie dyrektyw udostępnionych](xref:mvc/views/layout#importing-shared-directives).

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>Zmienianie domyślnego folderu obszaru, w którym są przechowywane widoki

Poniższy kod zmienia domyślny `"Areas"` `"MyAreas"`folder obszarowy z:

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a>Obszary ze stronami razor

Obszary ze stronami `Areas/<area name>/Pages` Razor wymagają folderu w katalogu głównym aplikacji. Z [przykładową aplikacją](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)używana jest następująca struktura folderów:

* Project name (Nazwa projektu)
  * Obszary
    * Produkty
      * Strony
        * _ViewImports
        * Informacje
        * Indeks
    * Usługi
      * Strony
        * Zarządzanie
          * Informacje
          * Indeks

### <a name="link-generation-with-razor-pages-and-areas"></a>Generowanie łączyć się ze stronami i obszarami razor

Poniższy kod z [przykładowego pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) pokazuje generowanie łączy `asp-area="Products"`z określonym obszarem (na przykład):

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

Przykładowe pobieranie zawiera [widok częściowy,](xref:mvc/views/partial) który zawiera poprzednie łącza i te same łącza bez określania obszaru. Widok częściowy odwołuje się do [pliku układu,](xref:mvc/views/layout)więc każda strona w aplikacji wyświetla wygenerowane łącza. Łącza wygenerowane bez określania obszaru są prawidłowe tylko wtedy, gdy odwołuje się do strony w tym samym obszarze.

Jeśli obszar nie jest określony, routing zależy od wartości *otoczenia.* Bieżące wartości trasy bieżącego żądania są traktowane jako wartości otoczenia dla generowania łączy. W wielu przypadkach dla przykładowej aplikacji przy użyciu wartości otoczenia generuje niepoprawne łącza. Rozważmy na przykład łącza wygenerowane na podstawie następującego kodu:

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

Dla poprzedniego kodu:

* Łącze `<a asp-page="/Manage/About">` wygenerowane z jest poprawne tylko wtedy, gdy ostatnie żądanie dotyczyło strony w `Services` obszarze. Na przykład `/Services/Manage/` `/Services/Manage/Index`, `/Services/Manage/About`, lub .
* Łącze `<a asp-page="/About">` wygenerowane z jest poprawne tylko wtedy, gdy ostatnie żądanie dotyczyło strony w pliku `/Home`.
* Kod pochodzi z [przykładowego pobrania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a>Importowanie obszaru nazw i pomocników znaczników za pomocą pliku _ViewImports

Plik *_ViewImports.cshtml* można dodać do każdego folderu *Strony* obszaru, aby zaimportować obszar nazw i Pomocników znaczników do każdej strony Razor w folderze.

Należy wziąć pod uwagę *usługi* obszaru przykładowego kodu, który nie zawiera *pliku _ViewImports.cshtml.* Następujące znaczniki pokazuje */Services/Manage/About* Razor Page:

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

W poprzednim znaczniku:

* Do określenia modelu musi służyć w pełni`@model RPareas.Areas.Services.Pages.Manage.AboutModel`kwalifikowana nazwa domeny ( ).
* [Pomocnik tagów](xref:mvc/views/tag-helpers/intro) jest włączona przez`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

W przykładowym pliku pobierania obszar Produkty zawiera następujący *plik _ViewImports.cshtml:*

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

Następujące znaczniki pokazuje */Produkty/ O* Razor Strony:

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

W poprzednim pliku obszar nazw `@addTagHelper` i dyrektywa są importowane do pliku przez plik *Obszary/Produkty/Strony/_ViewImports.cshtml.*

Aby uzyskać więcej informacji, zobacz [Zarządzanie zakresem pomocnika znaczników](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) i [importowanie dyrektyw udostępnionych](xref:mvc/views/layout#importing-shared-directives).

### <a name="shared-layout-for-razor-pages-areas"></a>Układ udostępniony dla obszarów stron razor

Aby udostępnić wspólny układ dla całej aplikacji, przenieś *_ViewStart.cshtml* do folderu głównego aplikacji.

### <a name="publishing-areas"></a>Obszary wydawnicze

Wszystkie pliki i pliki *.cshtml w katalogu *wwwroot* `<Project Sdk="Microsoft.NET.Sdk.Web">` są publikowane do danych wyjściowych, gdy jest zawarte w pliku *.csproj.
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Obszary są funkcją ASP.NET używaną do organizowania powiązanych funkcji w grupę jako oddzielny obszar nazw (do routingu) i strukturę folderów (dla widoków). Za pomocą obszarów tworzy hierarchię do celów routingu, `action` dodając inny parametr `page`trasy, `area`, do `controller` lub razor Strony .

Obszary umożliwiają podział ASP.NET podstawowej aplikacji sieci Web na mniejsze grupy funkcjonalne, z których każda ma własny zestaw stron Razor, kontrolerów, widoków i modeli. Obszar jest skutecznie strukturą wewnątrz aplikacji. W projekcie sieci Web ASP.NET Core składniki logiczne, takie jak Strony, Model, Kontroler i Widok, są przechowywane w różnych folderach. Środowisko uruchomieniowe ASP.NET Core używa konwencji nazewnictwa do tworzenia relacji między tymi składnikami. W przypadku dużej aplikacji może być korzystne partycjonowanie aplikacji na oddzielne obszary wysokiego poziomu funkcjonalności. Na przykład aplikacja e-commerce z wieloma jednostkami biznesowymi, takimi jak kasa, rozliczenia i wyszukiwanie. Każda z tych jednostek ma swój własny obszar zawierający widoki, kontrolery, strony razor i modele.

Rozważ użycie obszarów w projekcie, gdy:

* Aplikacja składa się z wielu składników funkcjonalnych wysokiego poziomu, które można logicznie rozdzielić.
* Chcesz podzielić aplikację, aby każdy obszar funkcjonalny mógł pracować niezależnie.

[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) [(jak pobrać).](xref:index#how-to-download-a-sample) Przykład pobierania zawiera podstawową aplikację do testowania obszarów.

Jeśli używasz stron Razor, zobacz [Obszary ze stronami Razor](#areas-with-razor-pages) w tym dokumencie.

## <a name="areas-for-controllers-with-views"></a>Obszary dla kontrolerów z widokami

Typowa aplikacja sieci Web ASP.NET Core przy użyciu obszarów, kontrolerów i widoków zawiera następujące elementy:

* [Struktura folderów Obszar](#area-folder-structure).
* Kontrolery z [`[Area]`](#attribute) atrybutem, aby skojarzyć kontroler z obszarem:

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* [Trasa obszaru dodana do uruchomienia:](#add-area-route)

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a>Struktura folderów obszarowych

Należy wziąć pod uwagę aplikację, która ma dwie grupy logiczne, *Produkty* i *usługi*. Przy użyciu obszarów struktura folderów będzie podobna do następującej:

* Project name (Nazwa projektu)
  * Obszary
    * Produkty
      * Kontrolery
        * HomeController.cs
        * ManageController.cs
      * Widoki
        * Strona główna
          * Index.cshtml
        * Zarządzanie
          * Index.cshtml
          * Informacje.cshtml
    * Usługi
      * Kontrolery
        * HomeController.cs
      * Widoki
        * Strona główna
          * Index.cshtml

Podczas gdy poprzedni układ jest typowy podczas korzystania z obszarów, tylko pliki widoku są wymagane do korzystania z tej struktury folderów. Wyświetl wyszukiwanie odnajdowania pasującego pliku widoku obszaru w następującej kolejności:

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>Skojarzenie kontrolera z obszarem

Kontrolery obszarów są oznaczone atrybutem [ &lbrack;Obszar:&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>Dodaj trasę obszaru

Trasy obszarowe zazwyczaj używają routingu konwencjonalnego, a nie routingu atrybutów. Routing konwencjonalny jest zależny od zamówienia. Ogólnie rzecz biorąc, trasy z obszarami powinny być umieszczane wcześniej w tabeli tras, ponieważ są bardziej szczegółowe niż trasy bez obszaru.

`{area:...}`może służyć jako token w szablonach trasy, jeśli przestrzeń adresów URL jest jednolita we wszystkich obszarach:

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

W poprzednim kodzie `exists` stosuje ograniczenie, które trasa musi pasować do obszaru. Używanie `{area:...}` jest najmniej skomplikowanym mechanizmem do dodawania routingu do obszarów.

Poniższy kod <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> służy do tworzenia dwóch nazwanych tras obszaru:

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

Podczas `MapAreaRoute` korzystania z ASP.NET Core 2.2, zobacz [ten problem GitHub](https://github.com/dotnet/AspNetCore/issues/7772).

Aby uzyskać więcej informacji, zobacz [Routing obszaru](xref:mvc/controllers/routing#areas).

### <a name="link-generation-with-mvc-areas"></a>Generowanie łączy z obszarami MVC

Poniższy kod z [przykładowego pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) pokazuje generowanie łączy z określonym obszarem:

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

Łącza wygenerowane za pomocą poprzedniego kodu są prawidłowe w dowolnym miejscu w aplikacji.

Przykładowe pobieranie zawiera [widok częściowy,](xref:mvc/views/partial) który zawiera poprzednie łącza i te same łącza bez określania obszaru. Widok częściowy odwołuje się do [pliku układu,](xref:mvc/views/layout)więc każda strona w aplikacji wyświetla wygenerowane łącza. Łącza generowane bez określania obszaru są prawidłowe tylko wtedy, gdy odwołuje się do strony w tym samym obszarze i kontrolera.

Jeśli obszar lub kontroler nie jest określony, routing zależy od wartości *otoczenia.* Bieżące wartości trasy bieżącego żądania są traktowane jako wartości otoczenia dla generowania łączy. W wielu przypadkach dla przykładowej aplikacji przy użyciu wartości otoczenia generuje niepoprawne łącza.

Aby uzyskać więcej informacji, zobacz [Routing do akcji kontrolera](xref:mvc/controllers/routing).

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a>Układ udostępniony dla obszarów przy użyciu pliku _ViewStart.cshtml

Aby udostępnić wspólny układ dla całej aplikacji, przenieś *_ViewStart.cshtml* do folderu głównego aplikacji.

### <a name="_viewimportscshtml"></a>_ViewImports.cshtml

W swojej standardowej lokalizacji */Views/_ViewImports.cshtml* nie ma zastosowania do obszarów. Aby korzystać z `@using`popularnych `@inject` [pomocników znaczników](xref:mvc/views/tag-helpers/intro), lub w twojej okolicy, upewnij się, że odpowiedni plik *_ViewImports.cshtml* [ma zastosowanie do widoków obszaru](xref:mvc/views/layout#importing-shared-directives). Jeśli chcesz takie samo zachowanie we wszystkich widokach, przenieś */Views/_ViewImports.cshtml* do katalogu głównego aplikacji.

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>Zmienianie domyślnego folderu obszaru, w którym są przechowywane widoki

Poniższy kod zmienia domyślny `"Areas"` `"MyAreas"`folder obszarowy z:

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a>Obszary ze stronami razor

Obszary ze stronami `Areas/<area name>/Pages` Razor wymagają folderu w katalogu głównym aplikacji. Z [przykładową aplikacją](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)używana jest następująca struktura folderów:

* Project name (Nazwa projektu)
  * Obszary
    * Produkty
      * Strony
        * _ViewImports
        * Informacje
        * Indeks
    * Usługi
      * Strony
        * Zarządzanie
          * Informacje
          * Indeks

### <a name="link-generation-with-razor-pages-and-areas"></a>Generowanie łączyć się ze stronami i obszarami razor

Poniższy kod z [przykładowego pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) pokazuje generowanie łączy `asp-area="Products"`z określonym obszarem (na przykład):

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

Łącza wygenerowane za pomocą poprzedniego kodu są prawidłowe w dowolnym miejscu w aplikacji.

Przykładowe pobieranie zawiera [widok częściowy,](xref:mvc/views/partial) który zawiera poprzednie łącza i te same łącza bez określania obszaru. Widok częściowy odwołuje się do [pliku układu,](xref:mvc/views/layout)więc każda strona w aplikacji wyświetla wygenerowane łącza. Łącza wygenerowane bez określania obszaru są prawidłowe tylko wtedy, gdy odwołuje się do strony w tym samym obszarze.

Jeśli obszar nie jest określony, routing zależy od wartości *otoczenia.* Bieżące wartości trasy bieżącego żądania są traktowane jako wartości otoczenia dla generowania łączy. W wielu przypadkach dla przykładowej aplikacji przy użyciu wartości otoczenia generuje niepoprawne łącza. Rozważmy na przykład łącza wygenerowane na podstawie następującego kodu:

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

Dla poprzedniego kodu:

* Łącze `<a asp-page="/Manage/About">` wygenerowane z jest poprawne tylko wtedy, gdy ostatnie żądanie dotyczyło strony w `Services` obszarze. Na przykład `/Services/Manage/` `/Services/Manage/Index`, `/Services/Manage/About`, lub .
* Łącze `<a asp-page="/About">` wygenerowane z jest poprawne tylko wtedy, gdy ostatnie żądanie dotyczyło strony w pliku `/Home`.
* Kod pochodzi z [przykładowego pobrania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a>Importowanie obszaru nazw i pomocników znaczników za pomocą pliku _ViewImports

Plik *_ViewImports.cshtml* można dodać do każdego folderu *Strony* obszaru, aby zaimportować obszar nazw i Pomocników znaczników do każdej strony Razor w folderze.

Należy wziąć pod uwagę *usługi* obszaru przykładowego kodu, który nie zawiera *pliku _ViewImports.cshtml.* Następujące znaczniki pokazuje */Services/Manage/About* Razor Page:

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

W poprzednim znaczniku:

* Do określenia modelu musi służyć w pełni`@model RPareas.Areas.Services.Pages.Manage.AboutModel`kwalifikowana nazwa domeny ( ).
* [Pomocnik tagów](xref:mvc/views/tag-helpers/intro) jest włączona przez`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

W przykładowym pliku pobierania obszar Produkty zawiera następujący *plik _ViewImports.cshtml:*

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

Następujące znaczniki pokazuje */Produkty/ O* Razor Strony:

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

W poprzednim pliku obszar nazw `@addTagHelper` i dyrektywa są importowane do pliku przez plik *Obszary/Produkty/Strony/_ViewImports.cshtml.*

Aby uzyskać więcej informacji, zobacz [Zarządzanie zakresem pomocnika znaczników](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) i [importowanie dyrektyw udostępnionych](xref:mvc/views/layout#importing-shared-directives).

### <a name="shared-layout-for-razor-pages-areas"></a>Układ udostępniony dla obszarów stron razor

Aby udostępnić wspólny układ dla całej aplikacji, przenieś *_ViewStart.cshtml* do folderu głównego aplikacji.

### <a name="publishing-areas"></a>Obszary wydawnicze

Wszystkie pliki i pliki *.cshtml w katalogu *wwwroot* `<Project Sdk="Microsoft.NET.Sdk.Web">` są publikowane do danych wyjściowych, gdy jest zawarte w pliku *.csproj.
::: moniker-end
