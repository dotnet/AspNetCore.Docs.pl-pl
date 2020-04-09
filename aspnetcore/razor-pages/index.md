---
title: Wprowadzenie do stron Razor w ASP.NET Core
author: Rick-Anderson
description: Dowiedz się, jak razor Pages w ASP.NET Core sprawia, że kodowanie scenariuszy skoncentrowanych na stronach jest łatwiejsze i bardziej wydajne niż przy użyciu MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
uid: razor-pages/index
ms.openlocfilehash: 42ffb0d4d2e49663dd53ffeee5d9fa2a931ee5b7
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667581"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a>Wprowadzenie do stron Razor w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autorstwa [Ricka Andersona](https://twitter.com/RickAndMSFT) i [Ryana Nowaka](https://github.com/rynowak)

Razor Pages może sprawić, że scenariusze związane ze stroną kodowania będą łatwiejsze i bardziej wydajne niż przy użyciu kontrolerów i widoków.

Jeśli szukasz samouczka, który używa podejścia Model-View-Controller, zobacz [Wprowadzenie do ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).

Ten dokument zawiera wprowadzenie do stron Razor. To nie jest samouczek krok po kroku. Jeśli niektóre sekcje są zbyt zaawansowane, zobacz [Wprowadzenie do strony Razor Pages](xref:tutorials/razor-pages/razor-pages-start). Aby zapoznać się z omówieniem ASP.NET Core, zobacz [Wprowadzenie do ASP.NET Core](xref:index).

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a>Tworzenie projektu strony razor

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Zobacz [Wprowadzenie do strony Razor Pages,](xref:tutorials/razor-pages/razor-pages-start) aby uzyskać szczegółowe instrukcje dotyczące tworzenia projektu Razor Pages.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uruchom `dotnet new webapp` z wiersza polecenia.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Uruchom `dotnet new webapp` z wiersza polecenia.

Otwórz wygenerowany plik *csproj* z programu Visual Studio dla komputerów Mac.

---

## <a name="razor-pages"></a>Razor Pages

Strona maszynki do golenia jest włączona w *Startup.cs:*

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

Rozważmy podstawową stronę:<a name="OnGet"></a>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

Poprzedni kod wygląda podobnie do [pliku widoku Razor](xref:tutorials/first-mvc-app/adding-view) używanego w aplikacji ASP.NET Core z kontrolerami i widokami. To, co sprawia, że jest inny, [`@page`](xref:mvc/views/razor#page) to dyrektywa. `@page`sprawia, że plik do akcji MVC - co oznacza, że obsługuje żądania bezpośrednio, bez przechodzenia przez kontroler. `@page`musi być pierwszą dyrektywą Razor na stronie. `@page`wpływa na zachowanie innych konstrukcji [Razor.](xref:mvc/views/razor) Nazwy plików Razor Pages mają sufiks *.cshtml.*

Podobna strona, `PageModel` przy użyciu klasy, jest wyświetlana w następujących dwóch plikach. Plik *Pages/Index2.cshtml:*

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

Model strony *Pages/Index2.cshtml.cs:*

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Zgodnie z `PageModel` konwencją plik klasy ma taką samą nazwę jak plik Razor Page z dołączona do *pliku cs.* Na przykład poprzednia strona Razor to *Pages/Index2.cshtml*. Plik zawierający `PageModel` klasę nosi nazwę *Pages/Index2.cshtml.cs*.

Skojarzenia ścieżek URL ze stronami są określane przez lokalizację strony w systemie plików. W poniższej tabeli przedstawiono ścieżkę strony Razor i pasujący adres URL:

| Nazwa pliku i ścieżka               | pasujący adres URL |
| ----------------- | ------------ |
| */Strony/Indeks.cshtml* | `/` lub `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Strony/Sklep/Indeks.cshtml* | `/Store` lub `/Store/Index` |

Uwagi:

* Środowisko wykonawcze domyślnie wyszukuje pliki Razor Pages w folderze *Strony.*
* `Index`jest stroną domyślną, gdy adres URL nie zawiera strony.

## <a name="write-a-basic-form"></a>Napisz formularz podstawowy

Razor Pages został zaprojektowany tak, aby typowe wzorce używane z przeglądarkami internetowymi były łatwe do wdrożenia podczas tworzenia aplikacji. [Powiązanie modelu,](xref:mvc/models/model-binding) [Pomocników tagów](xref:mvc/views/tag-helpers/intro)i pomocników HTML *po prostu pracować* z właściwości zdefiniowane w razor page klasy. Rozważmy stronę, która implementuje podstawowy formularz `Contact` "skontaktuj się z nami" dla modelu:

Dla przykładów w tym `DbContext` dokumencie jest inicjowany w pliku [Startup.cs.](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24)

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

Model danych:

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Kontekst db:

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

Plik widoku *Pages/Create.cshtml:*

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

Model strony *Pages/Create.cshtml.cs:*

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

Zgodnie z `PageModel` konwencją `<PageName>Model` klasa jest wywoływana i znajduje się w tej samej przestrzeni nazw co strona.

Klasa `PageModel` umożliwia oddzielenie logiki strony od jej prezentacji. Definiuje programy obsługi stron dla żądań wysyłanych do strony i dane używane do renderowania strony. Separacja ta pozwala na:

* Zarządzanie zależnościami stron poprzez [iniekcję zależności](xref:fundamentals/dependency-injection).
* [Testowanie jednostek](xref:test/razor-pages-tests)

Strona ma `OnPostAsync` *metodę obsługi*, `POST` która jest uruchamiana na żądaniach (gdy użytkownik publikuje formularz). Można dodać metody obsługi dla dowolnego zlecenia HTTP. Najczęstsze procedury obsługi to:

* `OnGet` — na potrzeby inicjowania stanu wymaganego dla strony. W poprzednim kodzie `OnGet` metoda wyświetla *CreateModel.cshtml* Razor Page.
* `OnPost` — na potrzeby obsługi przesłanych formularzy.

Sufiks nazewnictwa `Async` jest opcjonalny, ale jest często używany zgodnie z konwencją na potrzeby funkcji asynchronicznych. Powyższy kod jest typowy dla stron Razor.

Jeśli znasz ASP.NET aplikacje za pomocą kontrolerów i widoków:

* Kod `OnPostAsync` w poprzednim przykładzie wygląda podobnie do typowego kodu kontrolera.
* Większość ćwierczy MVC, takich jak [powiązanie modelu,](xref:mvc/models/model-binding) [sprawdzanie poprawności](xref:mvc/models/validation)i wyniki akcji, działa tak samo z kontrolerami i stronami razor. 

Poprzednia `OnPostAsync` metoda:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

Podstawowy `OnPostAsync`przepływ:

Sprawdź, czy nie ma błędów sprawdzania poprawności.

* Jeśli nie ma żadnych błędów, zapisz dane i przekieruj.
* Jeśli występują błędy, pokaż stronę ponownie z komunikatami sprawdzania poprawności. W wielu przypadkach błędy weryfikacji zostaną wykryte w kliencie i nigdy nie zostaną przesłane do serwera.

Plik widoku *Pages/Create.cshtml:*

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

Renderowany kod HTML ze *stron/Create.cshtml*:

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

W poprzednim kodzie księgowanie formularza:

* Z prawidłowymi danymi:

  * Metoda `OnPostAsync` obsługi wywołuje <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> metodę pomocnika. Metoda `RedirectToPage` zwraca wystąpienie klasy <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>. `RedirectToPage`:

    * Jest wynikiem akcji.
    * Jest podobny `RedirectToAction` `RedirectToRoute` do lub (używany w kontrolerach i widokach).
    * Jest dostosowany do stron. W poprzednim przykładzie przekierowuje do głównej`/Index`strony indeksu ( ). `RedirectToPage`jest szczegółowo opisana w sekcji [generowanie adresów URL dla stron.](#url_gen)

* Z błędami sprawdzania poprawności, które są przekazywane do serwera:

  * Metoda `OnPostAsync` obsługi wywołuje <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> metodę pomocnika. Metoda `Page` zwraca wystąpienie klasy <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>. Powrót `Page` jest podobny do tego, `View`jak akcje w kontrolerach zwracają . `PageResult`jest domyślnym typem zwracanym dla metody obsługi. Metoda obsługi, `void` która zwraca renderowania strony.
  * W poprzednim przykładzie księgowanie formularza bez wyników wartości w [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) zwracanie false. W tym przykładzie nie błędy sprawdzania poprawności są wyświetlane na kliencie. Przekazywanie błędów sprawdzania poprawności jest omówione w dalszej części tego dokumentu.

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* Z błędami sprawdzania poprawności wykrytymi przez sprawdzanie poprawności po stronie klienta:

  * Dane **nie** są księgowane na serwerze.
  * Sprawdzanie poprawności po stronie klienta jest wyjaśnione w dalszej części tego dokumentu.

Właściwość `Customer` używa [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) atrybutu, aby zdecydować się na powiązanie modelu:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

`[BindProperty]`**nie** powinny być używane w modelach zawierających właściwości, które nie powinny być zmieniane przez klienta. Aby uzyskać więcej informacji, zobacz [Nadpostanie](xref:data/ef-rp/crud#overposting).

Razor Pages, domyślnie, powiązać właściwości`GET` tylko z non-zleceń. Powiązanie właściwości usuwa konieczność pisania kodu w celu konwersji danych HTTP do typu modelu. Powiązanie zmniejsza kod przy użyciu tej`<input asp-for="Customer.Name">`samej właściwości do renderowania pól formularza ( ) i zaakceptować dane wejściowe.

[!INCLUDE[](~/includes/bind-get.md)]

Przeglądanie pliku widoku *Pages/Create.cshtml:*

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* W poprzednim kodzie [pomocnik](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` znacznika wejściowego `<input>` wiąże element `Customer.Name` HTML z wyrażeniem modelu.
* [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)udostępnia Pomocników Tagów.

### <a name="the-home-page"></a>Strona główna

*Index.cshtml* jest stroną główną:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

Powiązana `PageModel` klasa (*Index.cshtml.cs*):

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

Plik *Index.cshtml* zawiera następujące znaczniki:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

`<a /a>` [Pomocnik znacznika kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) użył tego atrybutu `asp-route-{value}` do wygenerowania łącza do strony Edycji. Łącze zawiera dane trasy o identyfikatorze kontaktu. Na przykład `https://localhost:5001/Edit/1`. [Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) umożliwiają uczestniczenie kodu po stronie serwera w tworzeniu i renderowaniu elementów HTML w plikach Razor.

Plik *Index.cshtml* zawiera znaczniki umożliwiające utworzenie przycisku usuwania dla każdego kontaktu z klientem:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

Renderowany kod HTML:

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

Gdy przycisk usuwania jest renderowany w formacie HTML, jego [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) zawiera parametry dla:

* Identyfikator kontaktu z klientem `asp-route-id` określony przez atrybut.
* The `handler`, określony `asp-page-handler` przez atrybut.

Po wybraniu przycisku `POST` żądanie formularza jest wysyłane do serwera. Zgodnie z konwencją nazwa metody obsługi jest wybierana `handler` na podstawie `OnPost[handler]Async`wartości parametru zgodnie ze schematem .

Ponieważ `handler` jest `delete` w tym `OnPostDeleteAsync` przykładzie, metoda obsługi `POST` jest używana do przetwarzania żądania. Jeśli `asp-page-handler` jest ustawiona na inną `remove`wartość, na przykład `OnPostRemoveAsync` , wybrana jest metoda obsługi o nazwie.

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

Metoda: `OnPostDeleteAsync`

* Pobiera `id` ciąg kwerendy.
* Wysyła zapytania do bazy danych `FindAsync`dla kontaktu z klientem za pomocą programu .
* Jeśli kontakt z klientem zostanie znaleziony, zostanie usunięty, a baza danych zostanie zaktualizowana.
* Wywołania <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> przekierowania do głównej`/Index`strony indeksu ( ).

### <a name="the-editcshtml-file"></a>Plik Edit.cshtml

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

Pierwszy wiersz zawiera `@page "{id:int}"` dyrektywę. Ograniczenie`"{id:int}"` routingu nakazuje stronie akceptowanie żądań na stronie zawierającej `int` dane trasy. Jeśli żądanie na stronie nie zawiera danych trasy, które `int`można przekonwertować na , środowisko wykonawcze zwraca błąd HTTP 404 (nie znaleziono). Aby identyfikator był opcjonalny, `?` dołącz do ograniczenia trasy:

 ```cshtml
@page "{id:int?}"
```

Plik *Edit.cshtml.cs:*

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a>Walidacja

Reguły sprawdzania poprawności:

* Są deklaratywnie określone w klasie modelu.
* Są wymuszane wszędzie w aplikacji.

Obszar <xref:System.ComponentModel.DataAnnotations> nazw zawiera zestaw wbudowanych atrybutów sprawdzania poprawności, które są stosowane deklaratywnie do klasy lub właściwości. DataAnnotations zawiera również atrybuty formatowania, takie jak, [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) które pomagają w formatowaniu i nie zapewniają sprawdzania poprawności.

Należy `Customer` wziąć pod uwagę model:

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Korzystanie z następującego pliku widoku *Create.cshtml:*

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

Powyższy kod ma następujące działanie:

* Zawiera skrypty weryfikacji jQuery i jQuery.
* Używa `<div />` pomocników `<span />` i [tagów,](xref:mvc/views/tag-helpers/intro) aby włączyć:

  * Sprawdzanie poprawności po stronie klienta.
  * Renderowanie błędów sprawdzania poprawności.

* Generuje następujący kod HTML:

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

Zamieszczając formularz Utwórz bez wartości nazwy wyświetla komunikat o błędzie "Wymagane jest pole Nazwa". na formularzu. Jeśli javascript jest włączony na kliencie, przeglądarka wyświetla błąd bez księgowania na serwerze.

Atrybut `[StringLength(10)]` generuje `data-val-length-max="10"` na renderowanym html. `data-val-length-max`uniemożliwia przeglądarkom wprowadzenie większej niż maksymalna określona długość. Jeśli narzędzie takie jak [Fiddler](https://www.telerik.com/fiddler) jest używane do edytowania i odtwarzania posta:

* Z nazwą dłuższą niż 10.
* Komunikat o błędzie "Nazwa pola musi być ciągiem o maksymalnej długości 10." zostanie zwrócona.

Należy wziąć `Movie` pod uwagę następujący model:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Atrybuty sprawdzania poprawności określają zachowanie do wymuszania właściwości modelu, do których są stosowane:

* `Required` Atrybuty `MinimumLength` i atrybuty wskazują, że właściwość musi mieć wartość, ale nic nie uniemożliwia użytkownikowi wprowadzania biały znak, aby spełnić tę walidację.
* Atrybut `RegularExpression` jest używany do ograniczania, jakie znaki mogą być wprowadzane. W poprzednim kodzie "Genre":

  * Może używać tylko liter.
  * Pierwsza litera musi być wielką literą. Białe znaki, liczby i znaki specjalne nie są dozwolone.

* "Ocena": `RegularExpression`

  * Wymaga, aby pierwszy znak był wielką literą.
  * Umożliwia znaki specjalne i liczby w kolejnych spacjach. "PG-13" jest ważny dla oceny, ale nie dla "Gatunku".

* Atrybut `Range` ogranicza wartość do określonego zakresu.
* Atrybut `StringLength` ustawia maksymalną długość właściwości ciągu i opcjonalnie jego minimalną długość.
* Typy wartości `decimal`(takie `float` `DateTime`jak , `int`, , ) są `[Required]` z natury wymagane i nie potrzebują atrybutu.

Strona Utwórz `Movie` dla modelu wyświetla błędy z nieprawidłowymi wartościami:

![Formularz widoku filmu z wieloma błędami sprawdzania poprawności po stronie klienta jQuery](~/tutorials/razor-pages/validation/_static/val.png)

Aby uzyskać więcej informacji, zobacz:

* [Dodawanie sprawdzania poprawności do aplikacji Movie](xref:tutorials/razor-pages/validation)
* [Sprawdzanie poprawności modelu w ASP.NET Core](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Obsługa żądań HEAD za pomocą rezerwowego programu OnGet

`HEAD`żądania umożliwiają pobieranie nagłówków dla określonego zasobu. W `GET` przeciwieństwie `HEAD` do żądań żądania nie zwracają treści odpowiedzi.

Zwykle program `OnHead` obsługi jest tworzony i `HEAD` wywoływany dla żądań:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

Razor Pages powraca do `OnGet` wywoływania `OnHead` programu obsługi, jeśli nie jest zdefiniowany program obsługi.

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a>Strony XSRF/CSRF i Razor

Strony brzytwy są chronione przez [walidację antyforgery.](xref:security/anti-request-forgery) [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) wstrzykuje tokeny antyforgery do elementów formularza HTML.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a>Używanie układów, częściowych, szablonów i pomocników znaczników ze stronami Razor

Strony działają ze wszystkimi możliwościami silnika widoku Razor. Układy, częściowe, szablony, Pomocników tagów, *_ViewStart.cshtml*i *_ViewImports.cshtml* działają w taki sam sposób, jak w przypadku konwencjonalnych widoków Razor.

Rozsyłajmy tę stronę, korzystając z niektórych z tych możliwości.

Dodawanie [strony układu](xref:mvc/views/layout) do *strony/udostępnione/_Layout.cshtml:*

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

[Układ:](xref:mvc/views/layout)

* Steruje układem każdej strony (chyba że strona zrezygnuje z układu).
* Importuje struktury HTML, takie jak JavaScript i arkusze stylów.
* Zawartość strony Razor są renderowane, gdzie `@RenderBody()` jest wywoływana.

Aby uzyskać więcej informacji, zobacz [stronę układu](xref:mvc/views/layout).

[Właściwość Layout](xref:mvc/views/layout#specifying-a-layout) jest ustawiona w *pliku Pages/_ViewStart.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

Układ znajduje się w folderze *Strony/Udostępnione.* Strony wyszukają inne widoki (układy, szablony, częściowe) hierarchicznie, zaczynając w tym samym folderze co bieżąca strona. Układ w folderze *Strony/Udostępnione* może być używany z dowolnej strony Razor w folderze *Strony.*

Plik układu powinien znajdować się w folderze *Strony/Udostępnione.*

Zaleca **się,** aby nie umieszczać pliku układu w folderze *Widoki/Udostępnione.* *Widoki/Udostępnione* jest wzorcem widoków MVC. Strony razor są przeznaczone do polegania na hierarchii folderów, a nie na konwencjach ścieżek.

Wyświetlanie wyszukiwania ze strony Razor zawiera folder *Strony.* Układy, szablony i częściowe używane z kontrolerami MVC i konwencjonalnymi widokami Razor *po prostu działają.*

Dodawanie pliku *Pages/_ViewImports.cshtml:*

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace`jest wyjaśnione w dalszej części samouczka. Dyrektywa `@addTagHelper` wprowadza [wbudowane Pomocników tagów](xref:mvc/views/tag-helpers/builtin-th/Index) do wszystkich stron w folderze *Strony.*

<a name="namespace"></a>

Dyrektywa `@namespace` ustawiona na stronie:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

Dyrektywa `@namespace` ustawia obszar nazw dla strony. Dyrektywa `@model` nie musi zawierać obszaru nazw.

Gdy `@namespace` dyrektywa jest zawarta w *_ViewImports.cshtml,* określony obszar nazw dostarcza prefiks wygenerowanego obszaru nazw `@namespace` na stronie, która importuje dyrektywę. Pozostała część wygenerowanego obszaru nazw (część sufiksu) to ścieżka względna oddzielona kropkami między folderem zawierającym *_ViewImports.cshtml* a folderem zawierającym stronę.

Na przykład `PageModel` klasa *Pages/Customers/Edit.cshtml.cs* jawnie ustawia obszar nazw:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

Plik *Pages/_ViewImports.cshtml* ustawia następujący obszar nazw:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

Wygenerowany obszar nazw dla *strony Pages/Customers/Edit.cshtml* Razor `PageModel` Page jest taki sam jak klasa.

`@namespace`*współpracuje również z konwencjonalnymi widokami Razor.*

Należy wziąć pod uwagę plik widoku *Pages/Create.cshtml:*

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

Zaktualizowany plik widoku *Pages/Create.cshtml* z *_ViewImports.cshtml* i poprzednim plikiem układu:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

W poprzednim kodzie *_ViewImports.cshtml* zaimportował obszar nazw i Pomocników tagów. Plik układu zaimportował pliki JavaScript.

[Projekt startowy Razor Pages](#rpvs17) zawiera *pages/_ValidationScriptsPartial.cshtml*, który łączy sprawdzanie poprawności po stronie klienta.

Aby uzyskać więcej informacji <xref:mvc/views/partial>na temat widoków częściowych, zobacz .

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Generowanie adresów URL dla stron

Strona, `Create` pokazana wcześniej, `RedirectToPage`używa:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

Aplikacja ma następującą strukturę plików/folderów:

* */Strony*

  * *Indeks.cshtml*
  * *Prywatność.cshtml*
  * */Klienci*

    * *Utwórz.cshtml*
    * *Edytuj.cshtml*
    * *Indeks.cshtml*

*Strony Pages/Customers/Create.cshtml* i *Pages/Customers/Edit.cshtml* przekierowują strony *do stron/klientów/index.cshtml* po sukcesie. Ciąg `./Index` jest względną nazwą strony używaną do uzyskiwania dostępu do poprzedniej strony. Służy do generowania adresów URL na stronie *Pages/Customers/Index.cshtml.* Przykład:

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

Bezwzględna nazwa `/Index` strony służy do generowania adresów URL na stronie *Pages/Index.cshtml.* Przykład:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

Nazwa strony jest ścieżką do strony z głównego */pages* folderu, w tym interlinia `/` (na przykład ). `/Index` Poprzednie przykłady generowania adresów URL oferują ulepszone opcje i funkcjonalne możliwości w stosunku do twardego kodowania adresu URL. Generowanie adresów URL używa [routingu](xref:mvc/controllers/routing) i może generować i kodować parametry zgodnie z tym, jak trasa jest zdefiniowana w ścieżce docelowej.

Generowanie adresów URL dla stron obsługuje nazwy względne. W poniższej tabeli przedstawiono, `RedirectToPage` która strona indeksu jest zaznaczona przy użyciu różnych parametrów w *pages/customers/create.cshtml*.

| RedirectToPage(x)| Strona |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Strony/indeks* |
| RedirectToPage("./Index"); | *Strony/klienci/indeks* |
| RedirectToPage(".. /Index") | *Strony/indeks* |
| RedirectToPage("Indeks")  | *Strony/klienci/indeks* |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

`RedirectToPage("Index")`, `RedirectToPage("./Index")`i `RedirectToPage("../Index")` są *nazwami względnymi*. Parametr `RedirectToPage` jest *łączony* ze ścieżką bieżącej strony w celu obliczenia nazwy strony docelowej.

Względne łączenie nazw jest przydatne, gdy place budowy o złożonej strukturze. Gdy nazwy względne są używane do łączenia między stronami w folderze:

* Zmiana nazwy folderu nie powoduje przerwania względnych łączy.
* Łącza nie są przerywane, ponieważ nie zawierają nazwy folderu.

Aby przekierować do strony w innym [obszarze,](xref:mvc/controllers/areas)określ obszar:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/areas> i <xref:razor-pages/razor-pages-conventions>.

## <a name="viewdata-attribute"></a>Atrybut ViewData

Dane mogą być przekazywane <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>do strony z . Właściwości z `[ViewData]` atrybutem mają swoje wartości przechowywane <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>i ładowane z .

W poniższym przykładzie `AboutModel` stosuje `[ViewData]` atrybut do `Title` właściwości:

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

Na stronie Informacje przejdź `Title` do właściwości jako właściwości modelu:

```cshtml
<h1>@Model.Title</h1>
```

W układzie tytuł jest odczytywany ze słownika ViewData:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a>TempData ( TempData )

ASP.NET Core eksponuje <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>plik . Ta właściwość przechowuje dane, dopóki nie jest odczytywany. I <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> metody mogą służyć do badania danych bez usuwania. `TempData`jest przydatne do przekierowania, gdy dane są potrzebne dla więcej niż jednego żądania.

Poniższy kod ustawia `Message` wartość `TempData`użycia:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

Następujące znaczniki w pliku *Pages/Customers/Index.cshtml* wyświetlają wartość `Message` użycia `TempData`.

```cshtml
<h3>Msg: @Model.Message</h3>
```

Model strony *Pages/Customers/Index.cshtml.cs* stosuje `[TempData]` atrybut do `Message` właściwości.

```csharp
[TempData]
public string Message { get; set; }
```

Aby uzyskać więcej informacji, zobacz [TempData](xref:fundamentals/app-state#tempdata).

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Wiele programów obsługi na stronie

Następująca strona generuje znaczniki dla dwóch `asp-page-handler` programów obsługi przy użyciu Pomocnika znaczników:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

Formularz w poprzednim przykładzie ma dwa przyciski `FormActionTagHelper` przesyłania, z których każdy używa do przesyłania do innego adresu URL. Atrybut `asp-page-handler` jest towarzyszem `asp-page`. `asp-page-handler`generuje adresy URL, które przesyłają do każdej z metod obsługi zdefiniowanych przez stronę. `asp-page`nie jest określony, ponieważ próbka łączy się z bieżącą stroną.

Model strony:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Poprzedni kod używa *nazwanych metod obsługi*. Nazwane metody obsługi są tworzone przez `On<HTTP Verb>` biorąc `Async` tekst w nazwie po i przed (jeśli jest obecny). W poprzednim przykładzie metody strony są OnPost**JoinList**Async i OnPost**JoinListUC**Async. Po usunięciu *OnPost* i *Async* `JoinList` nazwy `JoinListUC`obsługi są i .

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Przy użyciu poprzedniego kodu ścieżka adresu URL, do `OnPostJoinListAsync` którą przesyła się, to `https://localhost:5001/Customers/CreateFATH?handler=JoinList`. Ścieżka adresu URL, `OnPostJoinListUCAsync` do `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`którą się przesyła, to .

## <a name="custom-routes"></a>Trasy niestandardowe

Za `@page` pomocą dyrektywy można:

* Określ niestandardową trasę do strony. Na przykład trasę do strony Informacje można `/Some/Other/Path` `@page "/Some/Other/Path"`ustawić za pomocą pliku .
* Dołączanie segmentów do domyślnej trasy strony. Na przykład segment "element" można dodać do domyślnej `@page "item"`trasy strony za pomocą pliku .
* Dołącz parametry do domyślnej trasy strony. Na przykład parametr ID, `id`może być wymagany dla `@page "{id}"`strony z .

Ścieżka względna katalogu głównego wyznaczona`~`przez tyldę ( ) na początku ścieżki jest obsługiwana. Na przykład `@page "~/Some/Other/Path"` jest taka `@page "/Some/Other/Path"`sama jak .

Jeśli nie podoba Ci się `?handler=JoinList` ciąg zapytania w adresie URL, zmień trasę, aby umieścić nazwę programu obsługi w części ścieżki adresu URL. Trasę można dostosować, dodając szablon trasy ujęty w `@page` cudzysłowie po dyrektywie.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Przy użyciu poprzedniego kodu ścieżka adresu URL, do `OnPostJoinListAsync` którą przesyła się, to `https://localhost:5001/Customers/CreateFATH/JoinList`. Ścieżka adresu URL, `OnPostJoinListUCAsync` do `https://localhost:5001/Customers/CreateFATH/JoinListUC`którą się przesyła, to .

`?` Poniżej `handler` oznacza, że parametr trasy jest opcjonalny.

## <a name="advanced-configuration-and-settings"></a>Zaawansowana konfiguracja i ustawienia

Konfiguracja i ustawienia w poniższych sekcjach nie są wymagane przez większość aplikacji.

Aby skonfigurować opcje zaawansowane, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>użyj metody rozszerzenia:

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

Użyj, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> aby ustawić katalog główny dla stron lub dodać konwencje modelu aplikacji dla stron. Aby uzyskać więcej informacji na temat konwencji, zobacz [Konwencje autoryzacji stron razor](xref:security/authorization/razor-pages-authorization).

Aby wstępnie skompilować widoki, zobacz [Kompilacja widoku Razor](xref:mvc/views/view-compilation).

### <a name="specify-that-razor-pages-are-at-the-content-root"></a>Określ, że strony Razor znajdują się w katalogu głównym zawartości

Domyślnie strony Razor są zakorzenione w katalogu */Pages.* Dodaj, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> aby określić, że strony Razor znajdują się w [katalogu głównym zawartości](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) aplikacji:

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a>Określanie, że strony Razor znajdują się w niestandardowym katalogu głównym

Dodaj, <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> aby określić, że strony Razor znajdują się w niestandardowym katalogu głównym w aplikacji (podaj ścieżkę względną):

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* Zobacz [Wprowadzenie do strony Razor Pages](xref:tutorials/razor-pages/razor-pages-start), która opiera się na tym wprowadzeniu
* [Pobieranie lub wyświetlanie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/integrate-components>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autorstwa [Ricka Andersona](https://twitter.com/RickAndMSFT) i [Ryana Nowaka](https://github.com/rynowak)

Razor Pages to nowy aspekt ASP.NET Core MVC, który sprawia, że kodowanie scenariuszy skoncentrowanych na stronach jest łatwiejsze i bardziej wydajne.

Jeśli szukasz samouczka, który używa podejścia Model-View-Controller, zobacz [Wprowadzenie do ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).

Ten dokument zawiera wprowadzenie do stron Razor. To nie jest samouczek krok po kroku. Jeśli niektóre sekcje są zbyt zaawansowane, zobacz [Wprowadzenie do strony Razor Pages](xref:tutorials/razor-pages/razor-pages-start). Aby zapoznać się z omówieniem ASP.NET Core, zobacz [Wprowadzenie do ASP.NET Core](xref:index).

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a>Tworzenie projektu strony razor

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Zobacz [Wprowadzenie do strony Razor Pages,](xref:tutorials/razor-pages/razor-pages-start) aby uzyskać szczegółowe instrukcje dotyczące tworzenia projektu Razor Pages.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Uruchom `dotnet new webapp` z wiersza polecenia.

Otwórz wygenerowany plik *csproj* z programu Visual Studio dla komputerów Mac.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uruchom `dotnet new webapp` z wiersza polecenia.

---

## <a name="razor-pages"></a>Razor Pages

Strona maszynki do golenia jest włączona w *Startup.cs:*

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

Rozważmy podstawową stronę:<a name="OnGet"></a>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

Poprzedni kod wygląda podobnie do [pliku widoku Razor](xref:tutorials/first-mvc-app/adding-view) używanego w aplikacji ASP.NET Core z kontrolerami i widokami. To, co sprawia, że jest inny, `@page` to dyrektywa. `@page`sprawia, że plik do akcji MVC - co oznacza, że obsługuje żądania bezpośrednio, bez przechodzenia przez kontroler. `@page`musi być pierwszą dyrektywą Razor na stronie. `@page`wpływa na zachowanie innych konstrukcji Razor.

Podobna strona, `PageModel` przy użyciu klasy, jest wyświetlana w następujących dwóch plikach. Plik *Pages/Index2.cshtml:*

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

Model strony *Pages/Index2.cshtml.cs:*

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Zgodnie z `PageModel` konwencją plik klasy ma taką samą nazwę jak plik Razor Page z dołączona do *pliku cs.* Na przykład poprzednia strona Razor to *Pages/Index2.cshtml*. Plik zawierający `PageModel` klasę nosi nazwę *Pages/Index2.cshtml.cs*.

Skojarzenia ścieżek URL ze stronami są określane przez lokalizację strony w systemie plików. W poniższej tabeli przedstawiono ścieżkę strony Razor i pasujący adres URL:

| Nazwa pliku i ścieżka               | pasujący adres URL |
| ----------------- | ------------ |
| */Strony/Indeks.cshtml* | `/` lub `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Strony/Sklep/Indeks.cshtml* | `/Store` lub `/Store/Index` |

Uwagi:

* Środowisko wykonawcze domyślnie wyszukuje pliki Razor Pages w folderze *Strony.*
* `Index`jest stroną domyślną, gdy adres URL nie zawiera strony.

## <a name="write-a-basic-form"></a>Napisz formularz podstawowy

Razor Pages został zaprojektowany tak, aby typowe wzorce używane z przeglądarkami internetowymi były łatwe do wdrożenia podczas tworzenia aplikacji. [Powiązanie modelu,](xref:mvc/models/model-binding) [Pomocników tagów](xref:mvc/views/tag-helpers/intro)i pomocników HTML *po prostu pracować* z właściwości zdefiniowane w razor page klasy. Rozważmy stronę, która implementuje podstawowy formularz `Contact` "skontaktuj się z nami" dla modelu:

Dla przykładów w tym `DbContext` dokumencie jest inicjowany w pliku [Startup.cs.](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16)

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

Model danych:

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

Kontekst db:

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

Plik widoku *Pages/Create.cshtml:*

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

Model strony *Pages/Create.cshtml.cs:*

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

Zgodnie z `PageModel` konwencją `<PageName>Model` klasa jest wywoływana i znajduje się w tej samej przestrzeni nazw co strona.

Klasa `PageModel` umożliwia oddzielenie logiki strony od jej prezentacji. Definiuje programy obsługi stron dla żądań wysyłanych do strony i dane używane do renderowania strony. Separacja ta pozwala na:

* Zarządzanie zależnościami stron poprzez [iniekcję zależności](xref:fundamentals/dependency-injection).
* [Jednostka testująca](xref:test/razor-pages-tests) strony.

Strona ma `OnPostAsync` *metodę obsługi*, `POST` która jest uruchamiana na żądaniach (gdy użytkownik publikuje formularz). Metody procedury obsługi można dodać dla dowolnego czasownika HTTP. Najczęstsze procedury obsługi to:

* `OnGet` — na potrzeby inicjowania stanu wymaganego dla strony. [OnGet](#OnGet) próbki.
* `OnPost` — na potrzeby obsługi przesłanych formularzy.

Sufiks nazewnictwa `Async` jest opcjonalny, ale jest często używany zgodnie z konwencją na potrzeby funkcji asynchronicznych. Powyższy kod jest typowy dla stron Razor.

Jeśli znasz ASP.NET aplikacje za pomocą kontrolerów i widoków:

* Kod `OnPostAsync` w poprzednim przykładzie wygląda podobnie do typowego kodu kontrolera.
* Większość ćwierczy MVC, takich jak [powiązanie modelu,](xref:mvc/models/model-binding) [sprawdzanie poprawności,](xref:mvc/models/validation) [Sprawdzanie poprawności](xref:mvc/models/validation)i wyniki akcji są współużytkowane.

Poprzednia `OnPostAsync` metoda:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

Podstawowy `OnPostAsync`przepływ:

Sprawdź, czy nie ma błędów sprawdzania poprawności.

* Jeśli nie ma żadnych błędów, zapisz dane i przekieruj.
* Jeśli występują błędy, pokaż stronę ponownie z komunikatami sprawdzania poprawności. Sprawdzanie poprawności po stronie klienta jest identyczne z tradycyjnymi aplikacjami core MVC ASP.NET. W wielu przypadkach błędy weryfikacji zostaną wykryte w kliencie i nigdy nie zostaną przesłane do serwera.

Po pomyślnym wprowadzeniu danych `OnPostAsync` metoda obsługi `RedirectToPage` wywołuje metodę pomocnika, aby `RedirectToPageResult`zwrócić wystąpienie . `RedirectToPage`jest nowym wynikiem akcji, `RedirectToAction` `RedirectToRoute`podobnym do lub , ale dostosowanym do stron. W poprzednim przykładzie przekierowuje do głównej`/Index`strony indeksu ( ). `RedirectToPage`jest szczegółowo opisana w sekcji [generowanie adresów URL dla stron.](#url_gen)

Gdy przesłany formularz ma błędy sprawdzania poprawności (które`OnPostAsync` są przekazywane `Page` do serwera), metoda obsługi wywołuje metodę pomocnika. Metoda `Page` zwraca wystąpienie klasy `PageResult`. Powrót `Page` jest podobny do tego, `View`jak akcje w kontrolerach zwracają . `PageResult`jest domyślnym typem zwracanym dla metody obsługi. Metoda obsługi, `void` która zwraca renderowania strony.

Właściwość `Customer` używa `[BindProperty]` atrybutu, aby zdecydować się na powiązanie modelu.

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

Razor Pages, domyślnie, powiązać właściwości`GET` tylko z non-zleceń. Utworzenie powiązania z właściwościami może zmniejszyć ilość kodu, który trzeba napisać. Powiązanie zmniejsza kod przy użyciu tej`<input asp-for="Customer.Name">`samej właściwości do renderowania pól formularza ( ) i zaakceptować dane wejściowe.

[!INCLUDE[](~/includes/bind-get.md)]

Strona główna (*Index.cshtml*):

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

Powiązana `PageModel` klasa (*Index.cshtml.cs*):

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

Plik *Index.cshtml* zawiera następujące znaczniki umożliwiające utworzenie łącza edycji dla każdego kontaktu:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Pomocnik znacznika kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) użył tego atrybutu `asp-route-{value}` do wygenerowania łącza do strony Edycji. Łącze zawiera dane trasy o identyfikatorze kontaktu. Na przykład `https://localhost:5001/Edit/1`. [Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) umożliwiają uczestniczenie kodu po stronie serwera w tworzeniu i renderowaniu elementów HTML w plikach Razor. Pomocnik tagów jest włączona przez`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

Plik *Pages/Edit.cshtml:*

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

Pierwszy wiersz zawiera `@page "{id:int}"` dyrektywę. Ograniczenie`"{id:int}"` routingu nakazuje stronie akceptowanie żądań na stronie zawierającej `int` dane trasy. Jeśli żądanie na stronie nie zawiera danych trasy, które `int`można przekonwertować na , środowisko wykonawcze zwraca błąd HTTP 404 (nie znaleziono). Aby identyfikator był opcjonalny, `?` dołącz do ograniczenia trasy:

 ```cshtml
@page "{id:int?}"
```

Plik *Pages/Edit.cshtml.cs:*

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

Plik *Index.cshtml* zawiera również znaczniki umożliwiające utworzenie przycisku usuwania dla każdego kontaktu z klientem:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

Gdy przycisk usuwania jest renderowany `formaction` w formacie HTML, zawiera parametry dla:

* Identyfikator kontaktu z klientem `asp-route-id` określony przez atrybut.
* Określony `handler` przez `asp-page-handler` atrybut.

Oto przykład renderowanego przycisku usuwania z identyfikatorem `1`kontaktu z klientem:

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

Po wybraniu przycisku `POST` żądanie formularza jest wysyłane do serwera. Zgodnie z konwencją nazwa metody obsługi jest wybierana `handler` na podstawie `OnPost[handler]Async`wartości parametru zgodnie ze schematem .

Ponieważ `handler` jest `delete` w tym `OnPostDeleteAsync` przykładzie, metoda obsługi `POST` jest używana do przetwarzania żądania. Jeśli `asp-page-handler` jest ustawiona na inną `remove`wartość, na przykład `OnPostRemoveAsync` , wybrana jest metoda obsługi o nazwie. Poniższy kod `OnPostDeleteAsync` pokazuje program obsługi:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

Metoda: `OnPostDeleteAsync`

* Akceptuje ciąg `id` zapytania. Jeśli dyrektywa strony *Index.cshtml* zawierała `"{id:int?}"` `id` ograniczenie routingu, pochodziłoby z danych trasy. Dane trasy `id` dla jest określony w `https://localhost:5001/Customers/2`identyfikatorze URI, takich jak .
* Wysyła zapytania do bazy danych `FindAsync`dla kontaktu z klientem za pomocą programu .
* Jeśli kontakt z klientem zostanie znaleziony, zostaną one usunięte z listy kontaktów z klientami. Baza danych jest aktualizowana.
* Wywołania `RedirectToPage` przekierowania do głównej`/Index`strony indeksu ( ).

## <a name="mark-page-properties-as-required"></a>Oznaczanie właściwości strony zgodnie z wymaganiami

Właściwości na `PageModel` a mogą być oznaczone atrybutem [Wymagane:](/dotnet/api/system.componentmodel.dataannotations.requiredattribute)

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

Aby uzyskać więcej informacji, zobacz [Sprawdzanie poprawności modelu](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Obsługa żądań HEAD za pomocą rezerwowego programu OnGet

`HEAD`żądania umożliwiają pobieranie nagłówków dla określonego zasobu. W `GET` przeciwieństwie `HEAD` do żądań żądania nie zwracają treści odpowiedzi.

Zwykle program `OnHead` obsługi jest tworzony i `HEAD` wywoływany dla żądań: 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

W ASP.NET Core 2.1 lub nowszym Razor Pages `OnGet` powraca `OnHead` do wywoływania programu obsługi, jeśli nie jest zdefiniowany program obsługi. To zachowanie jest włączone przez wywołanie [SetCompatibilityVersion](xref:mvc/compatibility-version) w: `Startup.ConfigureServices`

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

Domyślne szablony `SetCompatibilityVersion` generują wywołanie w ASP.NET Core 2.1 i 2.2. `SetCompatibilityVersion`skutecznie ustawia opcję `AllowMappingHeadRequestsToGetHandler` Razor `true`Pages na .

Zamiast włączać wszystkie zachowania za `SetCompatibilityVersion`pomocą , możesz wyraźnie zdecydować się na *określone* zachowania. Poniższy kod zezwala na `HEAD` mapowanie żądań `OnGet` do programu obsługi:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a>Strony XSRF/CSRF i Razor

Nie musisz pisać żadnego kodu do [sprawdzania poprawności antyforgery.](xref:security/anti-request-forgery) Generowanie i walidacja tokenów antyforgery są automatycznie uwzględniane na stronach Razor.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a>Używanie układów, częściowych, szablonów i pomocników znaczników ze stronami Razor

Strony działają ze wszystkimi możliwościami silnika widoku Razor. Układy, częściowe, szablony, Pomocników tagów, *_ViewStart.cshtml*, *_ViewImports.cshtml* działają w taki sam sposób, jak w przypadku konwencjonalnych widoków Razor.

Rozsyłajmy tę stronę, korzystając z niektórych z tych możliwości.

Dodawanie [strony układu](xref:mvc/views/layout) do *strony/udostępnione/_Layout.cshtml:*

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

[Układ:](xref:mvc/views/layout)

* Steruje układem każdej strony (chyba że strona zrezygnuje z układu).
* Importuje struktury HTML, takie jak JavaScript i arkusze stylów.

Więcej informacji można znaleźć na [stronie układu.](xref:mvc/views/layout)

[Właściwość Layout](xref:mvc/views/layout#specifying-a-layout) jest ustawiona w *pliku Pages/_ViewStart.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

Układ znajduje się w folderze *Strony/Udostępnione.* Strony wyszukają inne widoki (układy, szablony, częściowe) hierarchicznie, zaczynając w tym samym folderze co bieżąca strona. Układ w folderze *Strony/Udostępnione* może być używany z dowolnej strony Razor w folderze *Strony.*

Plik układu powinien znajdować się w folderze *Strony/Udostępnione.*

Zaleca **się,** aby nie umieszczać pliku układu w folderze *Widoki/Udostępnione.* *Widoki/Udostępnione* jest wzorcem widoków MVC. Strony razor są przeznaczone do polegania na hierarchii folderów, a nie na konwencjach ścieżek.

Wyświetlanie wyszukiwania ze strony Razor zawiera folder *Strony.* Układy, szablony i części używane z kontrolerami MVC i konwencjonalnymi widokami Razor *po prostu działają.*

Dodawanie pliku *Pages/_ViewImports.cshtml:*

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace`jest wyjaśnione w dalszej części samouczka. Dyrektywa `@addTagHelper` wprowadza [wbudowane Pomocników tagów](xref:mvc/views/tag-helpers/builtin-th/Index) do wszystkich stron w folderze *Strony.*

<a name="namespace"></a>

Gdy `@namespace` dyrektywa jest używana jawnie na stronie:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

Dyrektywa ustawia obszar nazw dla strony. Dyrektywa `@model` nie musi zawierać obszaru nazw.

Gdy `@namespace` dyrektywa jest zawarta w *_ViewImports.cshtml,* określony obszar nazw dostarcza prefiks wygenerowanego obszaru nazw `@namespace` na stronie, która importuje dyrektywę. Pozostała część wygenerowanego obszaru nazw (część sufiksu) to ścieżka względna oddzielona kropkami między folderem zawierającym *_ViewImports.cshtml* a folderem zawierającym stronę.

Na przykład `PageModel` klasa *Pages/Customers/Edit.cshtml.cs* jawnie ustawia obszar nazw:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

Plik *Pages/_ViewImports.cshtml* ustawia następujący obszar nazw:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

Wygenerowany obszar nazw dla *strony Pages/Customers/Edit.cshtml* Razor `PageModel` Page jest taki sam jak klasa.

`@namespace`*współpracuje również z konwencjonalnymi widokami Razor.*

Oryginalny plik widoku *Pages/Create.cshtml:*

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

Zaktualizowany plik widoku *Pages/Create.cshtml:*

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

[Projekt startowy Razor Pages](#rpvs17) zawiera *pages/_ValidationScriptsPartial.cshtml*, który łączy sprawdzanie poprawności po stronie klienta.

Aby uzyskać więcej informacji <xref:mvc/views/partial>na temat widoków częściowych, zobacz .

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Generowanie adresów URL dla stron

Strona, `Create` pokazana wcześniej, `RedirectToPage`używa:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

Aplikacja ma następującą strukturę plików/folderów:

* */Strony*

  * *Indeks.cshtml*
  * */Klienci*

    * *Utwórz.cshtml*
    * *Edytuj.cshtml*
    * *Indeks.cshtml*

*Strony Pages/Customers/Create.cshtml* i *Pages/Customers/Edit.cshtml* przekierowują strony *pages/index.cshtml* po sukcesie. Ciąg `/Index` jest częścią identyfikatora URI, aby uzyskać dostęp do poprzedniej strony. Ciąg `/Index` może służyć do generowania identyfikatorów URI na stronie *Pages/Index.cshtml.* Przykład:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

Nazwa strony jest ścieżką do strony z głównego */pages* folderu, w tym interlinia `/` (na przykład ). `/Index` Poprzednie przykłady generowania adresów URL oferują ulepszone opcje i funkcjonalne możliwości za ok. Generowanie adresów URL używa [routingu](xref:mvc/controllers/routing) i może generować i kodować parametry zgodnie z tym, jak trasa jest zdefiniowana w ścieżce docelowej.

Generowanie adresów URL dla stron obsługuje nazwy względne. W poniższej tabeli przedstawiono, `RedirectToPage` która strona indeksu jest wybrana z różnymi parametrami niż *Strony/Klienci/Create.cshtml*:

| RedirectToPage(x)| Strona |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Strony/indeks* |
| RedirectToPage("./Index"); | *Strony/klienci/indeks* |
| RedirectToPage(".. /Index") | *Strony/indeks* |
| RedirectToPage("Indeks")  | *Strony/klienci/indeks* |

`RedirectToPage("Index")`, `RedirectToPage("./Index")`i `RedirectToPage("../Index")` są *nazwami względnymi*. Parametr `RedirectToPage` jest *łączony* ze ścieżką bieżącej strony w celu obliczenia nazwy strony docelowej.  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

Względne łączenie nazw jest przydatne, gdy place budowy o złożonej strukturze. Jeśli nazwy względne są używane do łączenia między stronami w folderze, można zmienić nazwę tego folderu. Wszystkie łącza nadal działają (ponieważ nie zawierały nazwy folderu).

Aby przekierować do strony w innym [obszarze,](xref:mvc/controllers/areas)określ obszar:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/areas>.

## <a name="viewdata-attribute"></a>Atrybut ViewData

Dane mogą być przekazywane do strony z [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute). Właściwości na kontrolerach lub modelach `[ViewData]` razor Page z atrybutem mają swoje wartości przechowywane i ładowane z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).

W poniższym przykładzie `AboutModel` `Title` zawiera właściwość oznaczoną `[ViewData]`symbolem . Właściwość `Title` jest ustawiona na tytuł strony Informacje:

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

Na stronie Informacje przejdź `Title` do właściwości jako właściwości modelu:

```cshtml
<h1>@Model.Title</h1>
```

W układzie tytuł jest odczytywany ze słownika ViewData:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a>TempData ( TempData )

ASP.NET Core udostępnia [właściwość TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) na [kontrolerze](/dotnet/api/microsoft.aspnetcore.mvc.controller). Ta właściwość przechowuje dane, dopóki nie jest odczytywany. I `Keep` `Peek` metody mogą służyć do badania danych bez usuwania. `TempData`jest przydatne do przekierowania, gdy dane są potrzebne dla więcej niż jednego żądania.

Poniższy kod ustawia `Message` wartość `TempData`użycia:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

Następujące znaczniki w pliku *Pages/Customers/Index.cshtml* wyświetlają wartość `Message` użycia `TempData`.

```cshtml
<h3>Msg: @Model.Message</h3>
```

Model strony *Pages/Customers/Index.cshtml.cs* stosuje `[TempData]` atrybut do `Message` właściwości.

```csharp
[TempData]
public string Message { get; set; }
```

Aby uzyskać więcej informacji, zobacz [TempData](xref:fundamentals/app-state#tempdata) .

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Wiele programów obsługi na stronie

Następująca strona generuje znaczniki dla dwóch `asp-page-handler` programów obsługi przy użyciu Pomocnika znaczników:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

Formularz w poprzednim przykładzie ma dwa przyciski `FormActionTagHelper` przesyłania, z których każdy używa do przesyłania do innego adresu URL. Atrybut `asp-page-handler` jest towarzyszem `asp-page`. `asp-page-handler`generuje adresy URL, które przesyłają do każdej z metod obsługi zdefiniowanych przez stronę. `asp-page`nie jest określony, ponieważ próbka łączy się z bieżącą stroną.

Model strony:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Poprzedni kod używa *nazwanych metod obsługi*. Nazwane metody obsługi są tworzone przez `On<HTTP Verb>` biorąc `Async` tekst w nazwie po i przed (jeśli jest obecny). W poprzednim przykładzie metody strony są OnPost**JoinList**Async i OnPost**JoinListUC**Async. Po usunięciu *OnPost* i *Async* `JoinList` nazwy `JoinListUC`obsługi są i .

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Przy użyciu poprzedniego kodu ścieżka adresu URL, do `OnPostJoinListAsync` którą przesyła się, to `https://localhost:5001/Customers/CreateFATH?handler=JoinList`. Ścieżka adresu URL, `OnPostJoinListUCAsync` do `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`którą się przesyła, to .

## <a name="custom-routes"></a>Trasy niestandardowe

Za `@page` pomocą dyrektywy można:

* Określ niestandardową trasę do strony. Na przykład trasę do strony Informacje można `/Some/Other/Path` `@page "/Some/Other/Path"`ustawić za pomocą pliku .
* Dołączanie segmentów do domyślnej trasy strony. Na przykład segment "element" można dodać do domyślnej `@page "item"`trasy strony za pomocą pliku .
* Dołącz parametry do domyślnej trasy strony. Na przykład parametr ID, `id`może być wymagany dla `@page "{id}"`strony z .

Ścieżka względna katalogu głównego wyznaczona`~`przez tyldę ( ) na początku ścieżki jest obsługiwana. Na przykład `@page "~/Some/Other/Path"` jest taka `@page "/Some/Other/Path"`sama jak .

Jeśli nie podoba Ci się `?handler=JoinList` ciąg zapytania w adresie URL, zmień trasę, aby umieścić nazwę programu obsługi w części ścieżki adresu URL. Trasę można dostosować, dodając szablon trasy ujęty w `@page` cudzysłowie po dyrektywie.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Przy użyciu poprzedniego kodu ścieżka adresu URL, do `OnPostJoinListAsync` którą przesyła się, to `https://localhost:5001/Customers/CreateFATH/JoinList`. Ścieżka adresu URL, `OnPostJoinListUCAsync` do `https://localhost:5001/Customers/CreateFATH/JoinListUC`którą się przesyła, to .

`?` Poniżej `handler` oznacza, że parametr trasy jest opcjonalny.

## <a name="configuration-and-settings"></a>Konfiguracja i ustawienia

Aby skonfigurować opcje zaawansowane, `AddRazorPagesOptions` użyj metody rozszerzenia w konstruktorze MVC:

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

Obecnie można użyć `RazorPagesOptions` do ustawienia katalogu głównego dla stron lub dodać konwencje modelu aplikacji dla stron. W ten sposób w przyszłości umożliwimy większą rozszerzalność.

Aby wstępnie skompilować widoki, zobacz [Kompilacja widoku Razor](xref:mvc/views/view-compilation) .

[Pobierz lub wyświetl przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).

Zobacz [Wprowadzenie do strony Razor ,](xref:tutorials/razor-pages/razor-pages-start)która opiera się na tym wprowadzeniu.

### <a name="specify-that-razor-pages-are-at-the-content-root"></a>Określ, że strony Razor znajdują się w katalogu głównym zawartości

Domyślnie strony Razor są zakorzenione w katalogu */Pages.* Dodaj [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) do [AddMvc,](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) aby określić, że strony Razor znajdują się w [katalogu głównym zawartości](xref:fundamentals/index#content-root) [(ContentRootPath)](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)aplikacji:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a>Określanie, że strony Razor znajdują się w niestandardowym katalogu głównym

Dodaj [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) do [AddMvc,](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) aby określić, że strony Razor znajdują się w niestandardowym katalogu głównym w aplikacji (podaj ścieżkę względną):

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
