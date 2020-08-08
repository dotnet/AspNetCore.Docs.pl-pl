---
title: Wprowadzenie do Razor stron w ASP.NET Core
author: Rick-Anderson
description: Dowiedz się Razor , jak strony w ASP.NET Core sprawia, że kodowanie scenariuszy ukierunkowanych na strony jest łatwiejsze i wydajniejsze niż używanie MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
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
uid: razor-pages/index
ms.openlocfilehash: 203d77a2caaba9156419a04754fe162dd57052c1
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88012712"
---
# <a name="introduction-to-no-locrazor-pages-in-aspnet-core"></a>Wprowadzenie do Razor stron w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT) i [Ryan Nowak](https://github.com/rynowak)

RazorStrony mogą sprawiać, że kodowanie scenariuszy ukierunkowanych na strony jest łatwiejsze i wydajniejsze niż używanie kontrolerów i widoków.

Jeśli szukasz samouczka korzystającego z podejścia Model-View-Controller, zobacz Wprowadzenie do [ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).

Ten dokument zawiera wprowadzenie do Razor stron. Nie jest to samouczek krok po kroku. Jeśli okaże się, że niektóre sekcje są zbyt zaawansowane, zobacz [wprowadzenie do Razor stron](xref:tutorials/razor-pages/razor-pages-start). Aby zapoznać się z omówieniem ASP.NET Core, zobacz [wprowadzenie do ASP.NET Core](xref:index).

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a>Tworzenie Razor projektu stron

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Aby uzyskać szczegółowe instrukcje dotyczące tworzenia projektu stron, zobacz Wprowadzenie do [ Razor stron](xref:tutorials/razor-pages/razor-pages-start) Razor .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uruchom `dotnet new webapp` polecenie w wierszu polecenia.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Aby uzyskać szczegółowe instrukcje dotyczące tworzenia projektu stron, zobacz Wprowadzenie do [ Razor stron](xref:tutorials/razor-pages/razor-pages-start) Razor .

---

## <a name="no-locrazor-pages"></a>RazorPage

RazorStrony są włączone w *Startup.cs*:

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

Weź pod uwagę podstawową stronę:<a name="OnGet"></a>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

Poprzedni kod wygląda podobnie jak [ Razor plik widoku](xref:tutorials/first-mvc-app/adding-view) używany w aplikacji ASP.NET Core z kontrolerami i widokami. Co sprawia, że jest to [`@page`](xref:mvc/views/razor#page) dyrektywa. `@page`sprawia, że plik jest akcją MVC, co oznacza, że obsługuje żądania bezpośrednio, bez przechodzenia przez kontroler. `@page`musi być pierwszą Razor dyrektywą na stronie. `@page`wpływa na zachowanie innych [Razor](xref:mvc/views/razor) konstrukcji. RazorNazwy plików stron mają sufiks *. cshtml* .

Podobna Strona, za pomocą `PageModel` klasy, jest pokazana w następujących dwóch plikach. Plik *Pages/index2. cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

Model strony *stron/index2. cshtml. cs* :

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Zgodnie z Konwencją `PageModel` plik klasy ma taką samą nazwę jak Razor plik stronicowania dołączony do *. cs* . Na przykład Poprzednia Razor strona to *Pages/index2. cshtml*. Plik zawierający `PageModel` klasę ma nazwę *Pages/index2. cshtml. cs*.

Skojarzenia ścieżek adresów URL ze stronami są określane przez lokalizację strony w systemie plików. W poniższej tabeli przedstawiono Razor ścieżkę strony i pasujący adres URL:

| Nazwa i ścieżka pliku               | pasujący adres URL |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` lub `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` lub `/Store/Index` |

Uwagi:

* Środowisko uruchomieniowe domyślnie wyszukuje Razor pliki stron w folderze *Pages* .
* `Index`jest stroną domyślną, gdy adres URL nie zawiera strony.

## <a name="write-a-basic-form"></a>Napisz podstawowy formularz

RazorStrony są przeznaczone do tworzenia wspólnych wzorców używanych z przeglądarkami sieci Web, które są łatwe do wdrożenia podczas kompilowania aplikacji. [Powiązania modelu](xref:mvc/models/model-binding), [pomocników tagów](xref:mvc/views/tag-helpers/intro)i pomocników HTML same *działają* z właściwościami zdefiniowanymi w Razor klasie Page. Weź pod uwagę stronę implementującą podstawowy formularz "contact us" (kontakt z nami) dla `Contact` modelu:

Przykłady w tym dokumencie `DbContext` są inicjowane w pliku [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) .

Baza danych w pamięci wymaga `Microsoft.EntityFrameworkCore.InMemory` pakietu NuGet.

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

Model danych:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Kontekst bazy danych:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

Plik widoku *Pages/Create. cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

Model strony *Pages/Create. cshtml. cs* :

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

Zgodnie z Konwencją `PageModel` Klasa jest wywoływana `<PageName>Model` i znajduje się w tej samej przestrzeni nazw co strona.

`PageModel`Klasa umożliwia rozdzielenie logiki strony od jej prezentacji. Definiuje procedury obsługi stron dla żądań wysyłanych do strony oraz dane używane do renderowania strony. Ta separacja umożliwia:

* Zarządzanie zależnościami stron przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection).
* [Testowanie jednostek](xref:test/razor-pages-tests)

Strona ma `OnPostAsync` *metodę obsługi*, która jest uruchamiana na `POST` żądaniach (gdy użytkownik księguje formularz). Można dodać metody obsługi dla dowolnego zlecenia HTTP. Najczęstsze procedury obsługi to:

* `OnGet` — na potrzeby inicjowania stanu wymaganego dla strony. W poprzednim kodzie `OnGet` Metoda wyświetla stronę *onmode. cshtml* Razor .
* `OnPost` — na potrzeby obsługi przesłanych formularzy.

Sufiks nazewnictwa `Async` jest opcjonalny, ale jest często używany zgodnie z konwencją na potrzeby funkcji asynchronicznych. Poprzedni kod jest typowy dla Razor stron.

Jeśli znasz już aplikacje ASP.NET przy użyciu kontrolerów i widoków:

* `OnPostAsync`Kod w poprzednim przykładzie wygląda podobnie do typowego kodu kontrolera.
* Większość elementów podstawowych MVC, takich jak [powiązania modelu](xref:mvc/models/model-binding), [Walidacja](xref:mvc/models/validation)i akcje, działa tak samo, jak w przypadku kontrolerów i Razor stron. 

Poprzednia `OnPostAsync` Metoda:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

Podstawowy przepływ `OnPostAsync` :

Sprawdź, czy występują błędy walidacji.

* Jeśli nie ma żadnych błędów, Zapisz dane i Przekieruj.
* W przypadku wystąpienia błędów ponownie Wyświetl stronę z komunikatami walidacji. W wielu przypadkach błędy weryfikacji zostaną wykryte w kliencie i nigdy nie zostaną przesłane do serwera.

Plik widoku *Pages/Create. cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

Renderowany kod HTML ze *stron/Create. cshtml*:

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

W poprzednim kodzie, ogłaszanie formularza:

* Z prawidłowymi danymi:

  * `OnPostAsync`Metoda obsługi wywołuje <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> metodę pomocnika. Metoda `RedirectToPage` zwraca wystąpienie klasy <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>. `RedirectToPage`:

    * Jest wynikiem akcji.
    * Jest podobny do `RedirectToAction` lub `RedirectToRoute` (używany w kontrolerach i widokach).
    * Jest dostosowywany dla stron. W powyższym przykładzie przekierowuje do strony indeksu głównego ( `/Index` ). `RedirectToPage`szczegółowo znajduje się w sekcji [generowanie adresów URL dla stron](#url_gen) .

* Z błędami walidacji, które są przesyłane do serwera:

  * `OnPostAsync`Metoda obsługi wywołuje <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> metodę pomocnika. Metoda `Page` zwraca wystąpienie klasy <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>. Zwracanie `Page` jest podobne do sposobu, w jaki akcje w kontrolerach zwracają `View` . `PageResult`jest domyślnym typem zwracanym dla metody obsługi. Metoda obsługi, która zwraca `void` renderowanie strony.
  * W poprzednim przykładzie, księgowanie formularza bez wartości powoduje, że [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) zwraca wartość false. W tym przykładzie na kliencie nie są wyświetlane błędy sprawdzania poprawności. Przekazywanie błędów sprawdzania poprawności jest omówione w dalszej części tego dokumentu.

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* Z błędami walidacji wykrytymi przez weryfikację po stronie klienta:

  * Dane **nie** są ogłaszane na serwerze.
  * Sprawdzanie poprawności po stronie klienta zostało wyjaśnione w dalszej części tego dokumentu.

`Customer`Właściwość używa [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) atrybutu, aby zrezygnować z powiązania modelu:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

`[BindProperty]`**nie** powinien być używany dla modeli zawierających właściwości, które nie powinny być zmieniane przez klienta. Aby uzyskać więcej informacji, zobacz temat [przefinalizowanie](xref:data/ef-rp/crud#overposting).

RazorDomyślnie strony powiążą właściwości tylko z `GET` niezleceniami. Powiązanie z właściwościami eliminuje konieczność pisania kodu w celu przekonwertowania danych HTTP na typ modelu. Powiązanie zmniejsza kod, używając tej samej właściwości do renderowania pól formularza ( `<input asp-for="Customer.Name">` ) i akceptuję dane wejściowe.

[!INCLUDE[](~/includes/bind-get.md)]

Przeglądanie pliku widoku *stron/Create. cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* W poprzednim kodzie [pomocnik tagów wejściowych](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` wiąże `<input>` element HTML z `Customer.Name` wyrażeniem modelu.
* [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)udostępnia pomocników tagów.

### <a name="the-home-page"></a>Strona główna

*Index. cshtml* to Strona główna:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

Skojarzona `PageModel` Klasa (*index.cshtml.cs*):

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

Plik *index. cshtml* zawiera następujące znaczniki:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

`<a /a>` [Pomocnik tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) użył `asp-route-{value}` atrybutu w celu wygenerowania linku do strony edycji. Link zawiera dane trasy z IDENTYFIKATORem kontaktu. Na przykład `https://localhost:5001/Edit/1`. [Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) włączają kod po stronie serwera, aby uczestniczyć w tworzeniu i RENDEROWANIU elementów HTML w Razor plikach.

Plik *index. cshtml* zawiera znaczniki umożliwiające utworzenie przycisku usuwania dla każdego kontaktu z klientem:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

Renderowany kod HTML:

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

Gdy przycisk Usuń jest renderowany w języku HTML, jego [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) zawiera parametry dla:

* Identyfikator osoby kontaktowej klienta określony przez `asp-route-id` atrybut.
* `handler`, Określony przez `asp-page-handler` atrybut.

Po wybraniu przycisku `POST` do serwera zostanie wysłane żądanie formularza. Według Konwencji, nazwa metody obsługi jest wybierana na podstawie wartości `handler` parametru zgodnie z schematem `OnPost[handler]Async` .

Ponieważ `handler` jest `delete` w tym przykładzie, `OnPostDeleteAsync` Metoda obsługi jest używana do przetwarzania `POST` żądania. Jeśli `asp-page-handler` jest ustawiona na inną wartość, na przykład, jest `remove` wybierana metoda obsługi o nazwie `OnPostRemoveAsync` .

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

`OnPostDeleteAsync`Metoda:

* Pobiera `id` z ciągu zapytania.
* Wysyła zapytanie do bazy danych w celu skontaktowania się z klientem za pomocą programu `FindAsync` .
* Jeśli kontakt z klientem zostanie znaleziony, zostanie on usunięty, a baza danych zostanie zaktualizowana.
* Wywołania <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> do przekierowania na stronę indeksu głównego ( `/Index` ).

### <a name="the-editcshtml-file"></a>Plik Edit. cshtml

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

Pierwszy wiersz zawiera `@page "{id:int}"` dyrektywę. Ograniczenie routingu `"{id:int}"` instruuje stronę, aby akceptowała żądania do strony zawierającej `int` dane trasy. Jeśli żądanie do strony nie zawiera danych trasy, które można przekonwertować na obiekt `int` , środowisko uruchomieniowe zwróci błąd HTTP 404 (nie znaleziono). Aby identyfikator był opcjonalny, Dołącz `?` do ograniczenia trasy:

 ```cshtml
@page "{id:int?}"
```

Plik *Edit.cshtml.cs* :

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a>Weryfikacja

Reguły walidacji:

* Są deklaratywnie określone w klasie modelu.
* Są wymuszane wszędzie w aplikacji.

<xref:System.ComponentModel.DataAnnotations>Przestrzeń nazw zawiera zestaw wbudowanych atrybutów walidacji, które są stosowane deklaratywnie do klasy lub właściwości. Adnotacje DataAnnotation zawierają również atrybuty formatowania, takie jak [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) Pomoc dotycząca formatowania i nie zapewniają weryfikacji.

Rozważmy `Customer` model:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Za pomocą następującego pliku widoku *Create. cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

Powyższy kod ma następujące działanie:

* Obejmuje skrypty sprawdzania poprawności jQuery i jQuery.
* Używa `<div />` `<span />` [pomocników tagów](xref:mvc/views/tag-helpers/intro) i do włączania:

  * Sprawdzanie poprawności po stronie klienta.
  * Renderowanie błędów walidacji.

* Generuje następujący kod HTML:

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

Opublikowanie formularza tworzenia bez wartości nazwa powoduje wyświetlenie komunikatu o błędzie "Nazwa pola jest wymagana." w formularzu. Jeśli na kliencie jest włączona obsługa języka JavaScript, przeglądarka wyświetli komunikat o błędzie bez publikowania na serwerze.

Ten `[StringLength(10)]` atrybut jest generowany `data-val-length-max="10"` na RENDEROWANYM kodzie HTML. `data-val-length-max`zapobiega wprowadzaniu przez przeglądarki więcej niż określoną maksymalną długość. Jeśli jest używane narzędzie, takie jak [programu Fiddler](https://www.telerik.com/fiddler) , do edytowania i powtarzania wpisu:

* O nazwie dłuższej niż 10.
* Komunikat o błędzie "Nazwa pola musi być ciągiem o maksymalnej długości 10". .

Rozważmy następujący `Movie` model:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Atrybuty walidacji określają zachowanie do wymuszania na właściwościach modelu, do których są stosowane:

* `Required`Atrybuty i `MinimumLength` wskazują, że właściwość musi mieć wartość, ale nic nie zapobiega wprowadzaniu przez użytkownika białych znaków w celu zaspokojenia tej walidacji.
* Ten `RegularExpression` atrybut służy do ograniczania, jakie znaki mogą być wprowadzane. W poprzednim kodzie "gatunek":

  * Należy używać tylko liter.
  * Pierwsza litera musi być wielką literą. Odstępy, cyfry i znaki specjalne są niedozwolone.

* `RegularExpression`"Ocena":

  * Wymaga, aby pierwszy znak był wielką literą.
  * Zezwala na znaki specjalne i cyfry w kolejnych odstępach. "PG-13" jest prawidłowy dla oceny, ale kończy się niepowodzeniem dla "gatunku".

* Atrybut `Range` ogranicza wartość do określonego zakresu.
* `StringLength`Atrybut ustawia maksymalną długość właściwości ciągu i opcjonalnie jej długość minimalną.
* Typy wartości (takie jak `decimal` ,,, `int` `float` `DateTime` ) są z założenia wymagane i nie wymagają `[Required]` atrybutu.

Na stronie Tworzenie dla `Movie` modelu są wyświetlane błędy z nieprawidłowymi wartościami:

![Formularz widoku filmu z wieloma błędami walidacji po stronie klienta jQuery](~/tutorials/razor-pages/validation/_static/val.png)

Aby uzyskać więcej informacji, zobacz:

* [Dodawanie walidacji do aplikacji filmowej](xref:tutorials/razor-pages/validation)
* [Walidacja modelu w ASP.NET Core](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Obsługa żądań głównych przy użyciu rezerwy procedury obsługi OnGet

`HEAD`żądania umożliwiają pobranie nagłówków dla określonego zasobu. W przeciwieństwie do `GET` żądań, `HEAD` żądania nie zwracają treści odpowiedzi.

Zwykle `OnHead` program obsługi jest tworzony i wywoływany dla `HEAD` żądań:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

RazorStrony powracają do wywoływania `OnGet` procedury obsługi, jeśli nie `OnHead` zdefiniowano procedury obsługi.

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a>XSRF/CSRF i Razor strony

RazorStrony są chronione przez [weryfikację przed fałszerstwem](xref:security/anti-request-forgery). [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) wprowadza do elementów formularza HTML tokeny zabezpieczające przed fałszerstwem.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a>Używanie układów, częściowych, szablonów i pomocników tagów ze Razor stronami

Strony współpracują ze wszystkimi możliwościami Razor aparatu widoku. Układy, częściowe, szablony, pomocniki tagów, *_ViewStart. cshtml*i *_ViewImports. cshtml* działają w taki sam sposób, jak w przypadku widoków konwencjonalnych Razor .

Zanotujmy Tę stronę, korzystając z zalet niektórych z tych funkcji.

Dodaj [stronę układu](xref:mvc/views/layout) do *stron/Shared/_Layout. cshtml*:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

[Układ](xref:mvc/views/layout):

* Steruje układem każdej strony (chyba że strona nie jest częścią układu).
* Importuje struktury HTML, takie jak JavaScript i stylesheets.
* Zawartość Razor strony jest renderowana, gdzie `@RenderBody()` jest wywoływana.

Aby uzyskać więcej informacji, zobacz [stronę układu](xref:mvc/views/layout).

Właściwość [układu](xref:mvc/views/layout#specifying-a-layout) jest ustawiana na *stronie/_ViewStart. cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

Układ znajduje się w *stronie/w folderze udostępnionym* . Strony szukają innych widoków (układów, szablonów, częściowych) hierarchicznie, rozpoczynając w tym samym folderze, w którym znajduje się bieżąca strona. Układ na *stronach/w folderze udostępnionym* może być używany z dowolnej Razor strony w folderze *strony* .

Plik układu powinien przejść do *stron/folderu udostępnionego* .

Zalecamy **umieszczenie pliku** układu w *widokach/folderze udostępnionym* . *Widoki/udostępnione* są wzorcem widoków MVC. RazorStrony są przeznaczone do korzystania z hierarchii folderów, a nie Konwencji ścieżek.

Widok wyszukiwania na Razor stronie zawiera folder *strony* . Układy, szablony i częściowe używane razem z kontrolerami MVC i konwencjonalnymi Razor widokami *działają tylko*.

Dodaj plik *Pages/_ViewImports. cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace`wyjaśniono w dalszej części tego samouczka. Dyrektywa znajduje się `@addTagHelper` w [wbudowanych pomocników tagów](xref:mvc/views/tag-helpers/builtin-th/Index) do wszystkich stron w folderze *strony* .

<a name="namespace"></a>

`@namespace`Dyrektywa ustawiona na stronie:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

`@namespace`Dyrektywa ustawia przestrzeń nazw dla strony. `@model`Dyrektywa nie musi zawierać przestrzeni nazw.

Gdy `@namespace` dyrektywa jest zawarta w *_ViewImports. cshtml*, określona przestrzeń nazw udostępnia prefiks dla wygenerowanej przestrzeni nazw na stronie, która importuje `@namespace` dyrektywę. Pozostała część wygenerowanej przestrzeni nazw (część sufiksu) jest ścieżką względną oddzieloną kropką między folderem zawierającym *_ViewImports. cshtml* i folderem zawierającym stronę.

Na przykład `PageModel` Klasa *Pages/Customers/Edit. cshtml. cs* jawnie ustawia przestrzeń nazw:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

Plik *Pages/_ViewImports. cshtml* ustawia następującą przestrzeń nazw:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

Wygenerowana przestrzeń nazw strony */Customers/Edit. cshtml* Razor jest taka sama jak `PageModel` Klasa.

`@namespace`*działa również z konwencjonalnymi Razor widokami.*

Rozważ użycie pliku widoku *Pages/Create. cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

Zaktualizowane *strony/Create. cshtml* plik widoku z *_ViewImports. cshtml* i poprzedni plik układu:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

W poprzednim kodzie, *_ViewImports. cshtml* zaimportował przestrzeń nazw i pomocników tagów. Plik układu zaimportował pliki JavaScript.

Na stronie [ Razor startowej projektu](#rpvs17) znajdują się *strony/_ValidationScriptsPartial. cshtml*, które przechwytuje walidację po stronie klienta.

Aby uzyskać więcej informacji o widokach częściowych, zobacz <xref:mvc/views/partial> .

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Generowanie adresu URL dla stron

`Create`Strona, pokazana wcześniej, używa `RedirectToPage` :

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

Aplikacja ma następującą strukturę plików/folderów:

* */Pages*

  * *Index. cshtml*
  * *Privacy. cshtml*
  * */Customers*

    * *Create. cshtml*
    * *Edytuj. cshtml*
    * *Index. cshtml*

Strony */Customers/Create. cshtml* i *Pages/Customers/Edit. cshtml* przekierowywać do *stron/Customers/index. cshtml* po powodzeniu. Ciąg `./Index` jest względną nazwą strony używaną w celu uzyskania dostępu do poprzedniej strony. Służy do generowania adresów URL na stronie *strony/klienci/index. cshtml* . Przykład:

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

Bezwzględna nazwa strony `/Index` służy do generowania adresów URL na stronie *stron/index. cshtml* . Przykład:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

Nazwa strony jest ścieżką do strony z folderu głównego */Pages* , włącznie z wiodącym `/` (na przykład `/Index` ). Powyższe przykłady generowania adresów URL oferują udoskonalone Opcje i możliwości funkcjonalne w porównaniu z zakodowanym adresem URL. Generowanie adresów URL używa [routingu](xref:mvc/controllers/routing) i może generować i kodować parametry zgodnie ze sposobem zdefiniowania trasy w ścieżce docelowej.

Generowanie adresów URL dla stron obsługuje nazwy względne. W poniższej tabeli przedstawiono, która strona indeksu została wybrana przy użyciu różnych `RedirectToPage` parametrów na stronie */Customers/Create. cshtml*.

| RedirectToPage (x)| Strona |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Strony/indeks* |
| RedirectToPage("./Index"); | *Strony/klienci/indeks* |
| RedirectToPage(".. /Index") | *Strony/indeks* |
| RedirectToPage ("index")  | *Strony/klienci/indeks* |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

`RedirectToPage("Index")`, `RedirectToPage("./Index")` , i `RedirectToPage("../Index")` są *nazwami względnymi*. `RedirectToPage`Parametr jest *połączony* ze ścieżką bieżącej strony, aby obliczyć nazwę strony docelowej.

Łączenie nazw względnych jest przydatne podczas kompilowania lokacji ze złożoną strukturą. Gdy nazwy względne są używane do łączenia między stronami w folderze:

* Zmiana nazwy folderu nie powoduje zerwania linków względnych.
* Linki nie są przerwane, ponieważ nie zawierają nazwy folderu.

Aby przekierować do strony w innym [obszarze](xref:mvc/controllers/areas), określ obszar:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/areas> i <xref:razor-pages/razor-pages-conventions>.

## <a name="viewdata-attribute"></a>ViewData — atrybut

Dane można przekazywać do strony za pomocą <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> . Właściwości z `[ViewData]` atrybutem mają przechowywane i ładowane wartości z <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> .

W poniższym przykładzie `AboutModel` stosuje `[ViewData]` atrybut do `Title` Właściwości:

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

Na stronie informacje uzyskaj dostęp do `Title` właściwości jako właściwości modelu:

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

## <a name="tempdata"></a>TempData

ASP.NET Core uwidacznia <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> . Ta właściwość przechowuje dane, dopóki nie zostanie odczytana. <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*>Metody i <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> mogą służyć do badania danych bez usuwania. `TempData`jest przydatne w przypadku przekierowania, gdy dane są potrzebne dla więcej niż jednego żądania.

Poniższy kod ustawia wartość `Message` użycia `TempData` :

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

W poniższym znaczniku w pliku *Pages/Customers/index. cshtml* jest wyświetlana wartość `Message` using `TempData` .

```cshtml
<h3>Msg: @Model.Message</h3>
```

Model strony *Pages/Customers/index. cshtml. cs* stosuje `[TempData]` atrybut do `Message` właściwości.

```csharp
[TempData]
public string Message { get; set; }
```

Aby uzyskać więcej informacji, zobacz [TempData](xref:fundamentals/app-state#tempdata).

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Wiele programów obsługi na stronie

Poniższa Strona generuje znaczniki dla dwóch programów obsługi przy użyciu `asp-page-handler` pomocnika tagów:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

Formularz w poprzednim przykładzie ma dwa przyciski przesyłania, z których każdy używa `FormActionTagHelper` do przesłania do innego adresu URL. Ten `asp-page-handler` atrybut jest towarzyszący do `asp-page` . `asp-page-handler`generuje adresy URL, które przesyłają do każdej metody obsługi zdefiniowanej przez stronę. `asp-page`nie została określona, ponieważ próbka jest łączona z bieżącą stroną.

Model strony:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Poprzedni kod używa *nazwanych metod obsługi*. Nazwane metody obsługi są tworzone przez pobranie tekstu w nazwie po `On<HTTP Verb>` i przed `Async` (jeśli istnieje). W poprzednim przykładzie metody strony są onpost**JoinList**Async i Onpost**JoinListUC**Async. Po usunięciu funkcji *onpost* i *Async* nazwy programów obsługi są `JoinList` i `JoinListUC` .

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Korzystając z powyższego kodu, ścieżka URL, która jest przesyłana do usługi, `OnPostJoinListAsync` to `https://localhost:5001/Customers/CreateFATH?handler=JoinList` . Ścieżka URL, która jest przesyłana do programu, `OnPostJoinListUCAsync` to `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .

## <a name="custom-routes"></a>Trasy niestandardowe

Użyj `@page` dyrektywy, aby:

* Określ trasę niestandardową dla strony. Na przykład trasy do strony informacje można ustawić na wartość `/Some/Other/Path` z `@page "/Some/Other/Path"` .
* Dołącz segmenty do domyślnej trasy strony. Na przykład segment "Item" można dodać do domyślnej trasy strony przy użyciu `@page "item"` .
* Dołącz parametry do domyślnej trasy strony. Na przykład parametr ID, `id` , może być wymagany dla strony z `@page "{id}"` .

Ścieżka względna elementu głównego wypisana przez tyldę ( `~` ) na początku ścieżki jest obsługiwana. Na przykład `@page "~/Some/Other/Path"` jest taka sama jak `@page "/Some/Other/Path"` .

Jeśli nie podoba Ci się ciąg zapytania `?handler=JoinList` w adresie URL, Zmień trasę, aby umieścić nazwę programu obsługi w części adresu URL. Trasę można dostosować, dodając szablon trasy ujęty w podwójne cudzysłowy po `@page` dyrektywie.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Korzystając z powyższego kodu, ścieżka URL, która jest przesyłana do usługi, `OnPostJoinListAsync` to `https://localhost:5001/Customers/CreateFATH/JoinList` . Ścieżka URL, która jest przesyłana do programu, `OnPostJoinListUCAsync` to `https://localhost:5001/Customers/CreateFATH/JoinListUC` .

`?`Poniżej przedstawiono `handler` , że parametr trasy jest opcjonalny.

## <a name="advanced-configuration-and-settings"></a>Zaawansowana konfiguracja i ustawienia

Konfiguracja i ustawienia w poniższych sekcjach nie są wymagane przez większość aplikacji.

Aby skonfigurować opcje zaawansowane, użyj <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> przeciążenia, które konfiguruje <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> :

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

Użyj, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> Aby ustawić katalog główny dla stron lub dodać konwencje modelu aplikacji dla stron. Aby uzyskać więcej informacji na temat Konwencji, zobacz [ Razor strony konwencje autoryzacji](xref:security/authorization/razor-pages-authorization).

Aby wstępnie skompilować widoki, zobacz [ Razor przeglądanie kompilacji](xref:mvc/views/view-compilation).

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a>Określ, że Razor strony znajdują się w katalogu głównym zawartości

Domyślnie Razor strony są umieszczane w katalogu */Pages* . Dodaj <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> , aby określić, że Razor strony znajdują się w [katalogu głównym zawartości](xref:fundamentals/index#content-root) <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> aplikacji:

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a>Określ, że Razor strony znajdują się w niestandardowym katalogu głównym

Dodaj <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> , aby określić, że Razor strony znajdują się w niestandardowym katalogu głównym w aplikacji (podaj ścieżkę względną):

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* Zobacz Rozpoczynanie [pracy ze Razor stronami](xref:tutorials/razor-pages/razor-pages-start), które kompilują się w tym wprowadzeniu.
* [Autoryzuj atrybut i Razor strony](xref:security/authorization/simple#aarp)
* [Pobieranie lub wyświetlanie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT) i [Ryan Nowak](https://github.com/rynowak)

RazorStrony to nowy aspekt ASP.NET Core MVC, który sprawia, że kodowanie scenariuszy ukierunkowanych na strony jest łatwiejsze i bardziej produktywne.

Jeśli szukasz samouczka korzystającego z podejścia Model-View-Controller, zobacz Wprowadzenie do [ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).

Ten dokument zawiera wprowadzenie do Razor stron. Nie jest to samouczek krok po kroku. Jeśli okaże się, że niektóre sekcje są zbyt zaawansowane, zobacz [wprowadzenie do Razor stron](xref:tutorials/razor-pages/razor-pages-start). Aby zapoznać się z omówieniem ASP.NET Core, zobacz [wprowadzenie do ASP.NET Core](xref:index).

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a>Tworzenie Razor projektu stron

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Aby uzyskać szczegółowe instrukcje dotyczące tworzenia projektu stron, zobacz Wprowadzenie do [ Razor stron](xref:tutorials/razor-pages/razor-pages-start) Razor .

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Uruchom `dotnet new webapp` polecenie w wierszu polecenia.

Otwórz wygenerowany plik *csproj* z Visual Studio dla komputerów Mac.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uruchom `dotnet new webapp` polecenie w wierszu polecenia.

---

## <a name="no-locrazor-pages"></a>RazorPage

RazorStrony są włączone w *Startup.cs*:

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

Weź pod uwagę podstawową stronę:<a name="OnGet"></a>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

Poprzedni kod wygląda podobnie jak [ Razor plik widoku](xref:tutorials/first-mvc-app/adding-view) używany w aplikacji ASP.NET Core z kontrolerami i widokami. Co sprawia, że jest to `@page` dyrektywa. `@page`sprawia, że plik jest akcją MVC, co oznacza, że obsługuje żądania bezpośrednio, bez przechodzenia przez kontroler. `@page`musi być pierwszą Razor dyrektywą na stronie. `@page`wpływa na zachowanie innych Razor konstrukcji.

Podobna Strona, za pomocą `PageModel` klasy, jest pokazana w następujących dwóch plikach. Plik *Pages/index2. cshtml* :

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

Model strony *stron/index2. cshtml. cs* :

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Zgodnie z Konwencją `PageModel` plik klasy ma taką samą nazwę jak Razor plik stronicowania dołączony do *. cs* . Na przykład Poprzednia Razor strona to *Pages/index2. cshtml*. Plik zawierający `PageModel` klasę ma nazwę *Pages/index2. cshtml. cs*.

Skojarzenia ścieżek adresów URL ze stronami są określane przez lokalizację strony w systemie plików. W poniższej tabeli przedstawiono Razor ścieżkę strony i pasujący adres URL:

| Nazwa i ścieżka pliku               | pasujący adres URL |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` lub `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` lub `/Store/Index` |

Uwagi:

* Środowisko uruchomieniowe domyślnie wyszukuje Razor pliki stron w folderze *Pages* .
* `Index`jest stroną domyślną, gdy adres URL nie zawiera strony.

## <a name="write-a-basic-form"></a>Napisz podstawowy formularz

RazorStrony są przeznaczone do tworzenia wspólnych wzorców używanych z przeglądarkami sieci Web, które są łatwe do wdrożenia podczas kompilowania aplikacji. [Powiązania modelu](xref:mvc/models/model-binding), [pomocników tagów](xref:mvc/views/tag-helpers/intro)i pomocników HTML same *działają* z właściwościami zdefiniowanymi w Razor klasie Page. Weź pod uwagę stronę implementującą podstawowy formularz "contact us" (kontakt z nami) dla `Contact` modelu:

Przykłady w tym dokumencie `DbContext` są inicjowane w pliku [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) .

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

Model danych:

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

Kontekst bazy danych:

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

Plik widoku *Pages/Create. cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

Model strony *Pages/Create. cshtml. cs* :

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

Zgodnie z Konwencją `PageModel` Klasa jest wywoływana `<PageName>Model` i znajduje się w tej samej przestrzeni nazw co strona.

`PageModel`Klasa umożliwia rozdzielenie logiki strony od jej prezentacji. Definiuje procedury obsługi stron dla żądań wysyłanych do strony oraz dane używane do renderowania strony. Ta separacja umożliwia:

* Zarządzanie zależnościami stron przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection).
* [Testowanie jednostkowe](xref:test/razor-pages-tests) stron.

Strona ma `OnPostAsync` *metodę obsługi*, która jest uruchamiana na `POST` żądaniach (gdy użytkownik księguje formularz). Metody procedury obsługi można dodać dla dowolnego czasownika HTTP. Najczęstsze procedury obsługi to:

* `OnGet` — na potrzeby inicjowania stanu wymaganego dla strony. Przykład [OnGet](#OnGet) .
* `OnPost` — na potrzeby obsługi przesłanych formularzy.

Sufiks nazewnictwa `Async` jest opcjonalny, ale jest często używany zgodnie z konwencją na potrzeby funkcji asynchronicznych. Poprzedni kod jest typowy dla Razor stron.

Jeśli znasz już aplikacje ASP.NET przy użyciu kontrolerów i widoków:

* `OnPostAsync`Kod w poprzednim przykładzie wygląda podobnie do typowego kodu kontrolera.
* Większość elementów podstawowych MVC, takich jak [powiązanie modelu](xref:mvc/models/model-binding), [Walidacja](xref:mvc/models/validation), [Walidacja](xref:mvc/models/validation)i wyniki akcji, jest udostępniana.

Poprzednia `OnPostAsync` Metoda:

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

Podstawowy przepływ `OnPostAsync` :

Sprawdź, czy występują błędy walidacji.

* Jeśli nie ma żadnych błędów, Zapisz dane i Przekieruj.
* W przypadku wystąpienia błędów ponownie Wyświetl stronę z komunikatami walidacji. Walidacja po stronie klienta jest taka sama jak w przypadku tradycyjnych ASP.NET Core aplikacji MVC. W wielu przypadkach błędy weryfikacji zostaną wykryte w kliencie i nigdy nie zostaną przesłane do serwera.

Po pomyślnym wprowadzeniu danych `OnPostAsync` Metoda obsługi wywołuje `RedirectToPage` metodę pomocnika zwracającą wystąpienie elementu `RedirectToPageResult` . `RedirectToPage`to nowy wynik akcji, podobny do `RedirectToAction` lub `RedirectToRoute` , ale dostosowany do stron. W powyższym przykładzie przekierowuje do strony indeksu głównego ( `/Index` ). `RedirectToPage`szczegółowo znajduje się w sekcji [generowanie adresów URL dla stron](#url_gen) .

Gdy przesłany formularz ma błędy walidacji (które są przekazywane do serwera), `OnPostAsync` Metoda obsługi wywołuje `Page` metodę pomocnika. Metoda `Page` zwraca wystąpienie klasy `PageResult`. Zwracanie `Page` jest podobne do sposobu, w jaki akcje w kontrolerach zwracają `View` . `PageResult`jest domyślnym typem zwracanym dla metody obsługi. Metoda obsługi, która zwraca `void` renderowanie strony.

`Customer`Właściwość używa `[BindProperty]` atrybutu, aby zrezygnować z powiązania modelu.

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

RazorDomyślnie strony powiążą właściwości tylko z `GET` niezleceniami. Utworzenie powiązania z właściwościami może zmniejszyć ilość kodu, który trzeba napisać. Powiązanie zmniejsza kod, używając tej samej właściwości do renderowania pól formularza ( `<input asp-for="Customer.Name">` ) i akceptuję dane wejściowe.

[!INCLUDE[](~/includes/bind-get.md)]

Strona główna (*index. cshtml*):

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

Skojarzona `PageModel` Klasa (*index.cshtml.cs*):

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

Plik *index. cshtml* zawiera następujące znaczniki, aby utworzyć łącze do edycji dla każdego kontaktu:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Pomocnik tagu kotwicy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) użył `asp-route-{value}` atrybutu w celu wygenerowania linku do strony edycji. Link zawiera dane trasy z IDENTYFIKATORem kontaktu. Na przykład `https://localhost:5001/Edit/1`. [Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) włączają kod po stronie serwera, aby uczestniczyć w tworzeniu i RENDEROWANIU elementów HTML w Razor plikach. Pomocnicy tagów są włączani przez`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

Plik *Pages/Edit. cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

Pierwszy wiersz zawiera `@page "{id:int}"` dyrektywę. Ograniczenie routingu `"{id:int}"` instruuje stronę, aby akceptowała żądania do strony zawierającej `int` dane trasy. Jeśli żądanie do strony nie zawiera danych trasy, które można przekonwertować na obiekt `int` , środowisko uruchomieniowe zwróci błąd HTTP 404 (nie znaleziono). Aby identyfikator był opcjonalny, Dołącz `?` do ograniczenia trasy:

 ```cshtml
@page "{id:int?}"
```

Plik *stron/Edytuj. cshtml. cs* :

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

Plik *index. cshtml* zawiera również znaczniki umożliwiające utworzenie przycisku usuwania dla każdego kontaktu z klientem:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

Gdy przycisk Usuń jest renderowany w języku HTML, jego `formaction` Parametry obejmują:

* Identyfikator osoby kontaktowej klienta określony przez `asp-route-id` atrybut.
* `handler`Określony przez `asp-page-handler` atrybut.

Oto przykład renderowanego przycisku usuwania z IDENTYFIKATORem kontaktu klienta `1` :

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

Po wybraniu przycisku `POST` do serwera zostanie wysłane żądanie formularza. Według Konwencji, nazwa metody obsługi jest wybierana na podstawie wartości `handler` parametru zgodnie z schematem `OnPost[handler]Async` .

Ponieważ `handler` jest `delete` w tym przykładzie, `OnPostDeleteAsync` Metoda obsługi jest używana do przetwarzania `POST` żądania. Jeśli `asp-page-handler` jest ustawiona na inną wartość, na przykład, jest `remove` wybierana metoda obsługi o nazwie `OnPostRemoveAsync` . Poniższy kod ilustruje `OnPostDeleteAsync` procedurę obsługi:

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

`OnPostDeleteAsync`Metoda:

* Akceptuje `id` z ciągu zapytania. Jeśli na stronie *index. cshtml* znajduje się ograniczenie routingu `"{id:int?}"` , `id` będą pochodzić z danych tras. Dane trasy dla programu `id` są określone w identyfikatorze URI, takich jak `https://localhost:5001/Customers/2` .
* Wysyła zapytanie do bazy danych w celu skontaktowania się z klientem za pomocą programu `FindAsync` .
* Jeśli kontakt z klientem zostanie znaleziony, zostanie on usunięty z listy kontaktów klientów. Baza danych jest aktualizowana.
* Wywołania `RedirectToPage` do przekierowania na stronę indeksu głównego ( `/Index` ).

## <a name="mark-page-properties-as-required"></a>Oznacz właściwości strony jako wymagane

Właściwości na stronie `PageModel` można oznaczyć przy użyciu [wymaganego](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) atrybutu:

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

Aby uzyskać więcej informacji, zobacz [Walidacja modelu](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Obsługa żądań głównych przy użyciu rezerwy procedury obsługi OnGet

`HEAD`żądania umożliwiają pobranie nagłówków dla określonego zasobu. W przeciwieństwie do `GET` żądań, `HEAD` żądania nie zwracają treści odpowiedzi.

Zwykle `OnHead` program obsługi jest tworzony i wywoływany dla `HEAD` żądań: 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

W ASP.NET Core 2,1 lub nowszej strony wracają Razor do wywoływania `OnGet` procedury obsługi, jeśli nie `OnHead` zdefiniowano procedury obsługi. To zachowanie jest włączane przez wywołanie [SetCompatibilityVersion](xref:mvc/compatibility-version) w `Startup.ConfigureServices` :

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

Szablony domyślne generują `SetCompatibilityVersion` wywołanie w ASP.NET Core 2,1 i 2,2. `SetCompatibilityVersion`efektywnie ustawia Razor opcję strony `AllowMappingHeadRequestsToGetHandler` na `true` .

Zamiast korzystać z wszystkich zachowań w programie `SetCompatibilityVersion` , można jawnie zrezygnować z *określonych* zachowań. Poniższy kod pozwala na umożliwienie `HEAD` mapowania żądań do `OnGet` programu obsługi:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a>XSRF/CSRF i Razor strony

Nie trzeba pisać kodu do [weryfikacji przed fałszerstwem](xref:security/anti-request-forgery). Generowanie i sprawdzanie poprawności tokenów antysfałszowanych są automatycznie dołączane do Razor stron.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a>Używanie układów, częściowych, szablonów i pomocników tagów ze Razor stronami

Strony współpracują ze wszystkimi możliwościami Razor aparatu widoku. Układy, częściowe, szablony, pomocniki tagów, *_ViewStart. cshtml*, *_ViewImports. cshtml* działają w taki sam sposób, jak w przypadku Razor widoków konwencjonalnych.

Zanotujmy Tę stronę, korzystając z zalet niektórych z tych funkcji.

Dodaj [stronę układu](xref:mvc/views/layout) do *stron/Shared/_Layout. cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

[Układ](xref:mvc/views/layout):

* Steruje układem każdej strony (chyba że strona nie jest częścią układu).
* Importuje struktury HTML, takie jak JavaScript i stylesheets.

Aby uzyskać więcej informacji, zobacz [stronę układu](xref:mvc/views/layout) .

Właściwość [układu](xref:mvc/views/layout#specifying-a-layout) jest ustawiana na *stronie/_ViewStart. cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

Układ znajduje się w *stronie/w folderze udostępnionym* . Strony szukają innych widoków (układów, szablonów, częściowych) hierarchicznie, rozpoczynając w tym samym folderze, w którym znajduje się bieżąca strona. Układ na *stronach/w folderze udostępnionym* może być używany z dowolnej Razor strony w folderze *strony* .

Plik układu powinien przejść do *stron/folderu udostępnionego* .

Zalecamy **umieszczenie pliku** układu w *widokach/folderze udostępnionym* . *Widoki/udostępnione* są wzorcem widoków MVC. RazorStrony są przeznaczone do korzystania z hierarchii folderów, a nie Konwencji ścieżek.

Widok wyszukiwania na Razor stronie zawiera folder *strony* . Układy, szablony i częściowe, które są używane z kontrolerami MVC i konwencjonalnymi Razor widokami *działają tylko*.

Dodaj plik *Pages/_ViewImports. cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace`wyjaśniono w dalszej części tego samouczka. Dyrektywa znajduje się `@addTagHelper` w [wbudowanych pomocników tagów](xref:mvc/views/tag-helpers/builtin-th/Index) do wszystkich stron w folderze *strony* .

<a name="namespace"></a>

Gdy `@namespace` dyrektywa jest używana jawnie na stronie:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

Dyrektywa ustawia przestrzeń nazw dla strony. `@model`Dyrektywa nie musi zawierać przestrzeni nazw.

Gdy `@namespace` dyrektywa jest zawarta w *_ViewImports. cshtml*, określona przestrzeń nazw udostępnia prefiks dla wygenerowanej przestrzeni nazw na stronie, która importuje `@namespace` dyrektywę. Pozostała część wygenerowanej przestrzeni nazw (część sufiksu) jest ścieżką względną oddzieloną kropką między folderem zawierającym *_ViewImports. cshtml* i folderem zawierającym stronę.

Na przykład `PageModel` Klasa *Pages/Customers/Edit. cshtml. cs* jawnie ustawia przestrzeń nazw:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

Plik *Pages/_ViewImports. cshtml* ustawia następującą przestrzeń nazw:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

Wygenerowana przestrzeń nazw strony */Customers/Edit. cshtml* Razor jest taka sama jak `PageModel` Klasa.

`@namespace`*działa również z konwencjonalnymi Razor widokami.*

Oryginalne *strony/Utwórz plik widoku. cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

Zaktualizowane *strony/Utwórz plik widoku. cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

Na stronie [ Razor startowej projektu](#rpvs17) znajdują się *strony/_ValidationScriptsPartial. cshtml*, które przechwytuje walidację po stronie klienta.

Aby uzyskać więcej informacji o widokach częściowych, zobacz <xref:mvc/views/partial> .

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Generowanie adresu URL dla stron

`Create`Strona, pokazana wcześniej, używa `RedirectToPage` :

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

Aplikacja ma następującą strukturę plików/folderów:

* */Pages*

  * *Index. cshtml*
  * */Customers*

    * *Create. cshtml*
    * *Edytuj. cshtml*
    * *Index. cshtml*

Strony */Customers/Create. cshtml* i *Pages/Customers/Edit. cshtml* przekierują do *stron/index. cshtml* po powodzeniu. Ciąg `/Index` jest częścią identyfikatora URI, aby uzyskać dostęp do poprzedniej strony. Ten ciąg `/Index` może służyć do generowania identyfikatorów URI na stronie *stron/index. cshtml* . Przykład:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

Nazwa strony jest ścieżką do strony z folderu głównego */Pages* , włącznie z wiodącym `/` (na przykład `/Index` ). Powyższe przykłady generowania adresów URL oferują ulepszone opcje i możliwości funkcjonalne w porównaniu z zakodowana adresem URL. Generowanie adresów URL używa [routingu](xref:mvc/controllers/routing) i może generować i kodować parametry zgodnie ze sposobem zdefiniowania trasy w ścieżce docelowej.

Generowanie adresów URL dla stron obsługuje nazwy względne. W poniższej tabeli przedstawiono, która strona indeksu została wybrana z różnymi `RedirectToPage` parametrami *stron/Customers/Create. cshtml*:

| RedirectToPage (x)| Strona |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Strony/indeks* |
| RedirectToPage("./Index"); | *Strony/klienci/indeks* |
| RedirectToPage(".. /Index") | *Strony/indeks* |
| RedirectToPage ("index")  | *Strony/klienci/indeks* |

`RedirectToPage("Index")`, `RedirectToPage("./Index")` , i `RedirectToPage("../Index")` są *nazwami względnymi*. `RedirectToPage`Parametr jest *połączony* ze ścieżką bieżącej strony, aby obliczyć nazwę strony docelowej.  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

Łączenie nazw względnych jest przydatne podczas kompilowania lokacji ze złożoną strukturą. Jeśli używasz nazw względnych do łączenia między stronami w folderze, możesz zmienić nazwę tego folderu. Wszystkie linki nadal działają (ponieważ nie zawierają nazwy folderu).

Aby przekierować do strony w innym [obszarze](xref:mvc/controllers/areas), określ obszar:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/areas>.

## <a name="viewdata-attribute"></a>ViewData — atrybut

Dane można przekazywać do strony z [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute). Właściwości kontrolerów lub Razor modeli stron z `[ViewData]` atrybutem są przechowywane i ładowane z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).

W poniższym przykładzie `AboutModel` zawiera właściwość, która jest `Title` oznaczona za pomocą `[ViewData]` . `Title`Właściwość jest ustawiona na tytuł strony informacje:

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

Na stronie informacje uzyskaj dostęp do `Title` właściwości jako właściwości modelu:

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

## <a name="tempdata"></a>TempData

ASP.NET Core uwidacznia Właściwość [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) na [kontrolerze](/dotnet/api/microsoft.aspnetcore.mvc.controller). Ta właściwość przechowuje dane, dopóki nie zostanie odczytana. `Keep`Metody i `Peek` mogą służyć do badania danych bez usuwania. `TempData`jest przydatne w przypadku przekierowania, gdy dane są potrzebne dla więcej niż jednego żądania.

Poniższy kod ustawia wartość `Message` użycia `TempData` :

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

W poniższym znaczniku w pliku *Pages/Customers/index. cshtml* jest wyświetlana wartość `Message` using `TempData` .

```cshtml
<h3>Msg: @Model.Message</h3>
```

Model strony *Pages/Customers/index. cshtml. cs* stosuje `[TempData]` atrybut do `Message` właściwości.

```csharp
[TempData]
public string Message { get; set; }
```

Aby uzyskać więcej informacji, zobacz [TempData](xref:fundamentals/app-state#tempdata) .

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Wiele programów obsługi na stronie

Poniższa Strona generuje znaczniki dla dwóch programów obsługi przy użyciu `asp-page-handler` pomocnika tagów:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

Formularz w poprzednim przykładzie ma dwa przyciski przesyłania, z których każdy używa `FormActionTagHelper` do przesłania do innego adresu URL. Ten `asp-page-handler` atrybut jest towarzyszący do `asp-page` . `asp-page-handler`generuje adresy URL, które przesyłają do każdej metody obsługi zdefiniowanej przez stronę. `asp-page`nie została określona, ponieważ próbka jest łączona z bieżącą stroną.

Model strony:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Poprzedni kod używa *nazwanych metod obsługi*. Nazwane metody obsługi są tworzone przez pobranie tekstu w nazwie po `On<HTTP Verb>` i przed `Async` (jeśli istnieje). W poprzednim przykładzie metody strony są onpost**JoinList**Async i Onpost**JoinListUC**Async. Po usunięciu funkcji *onpost* i *Async* nazwy programów obsługi są `JoinList` i `JoinListUC` .

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Korzystając z powyższego kodu, ścieżka URL, która jest przesyłana do usługi, `OnPostJoinListAsync` to `https://localhost:5001/Customers/CreateFATH?handler=JoinList` . Ścieżka URL, która jest przesyłana do programu, `OnPostJoinListUCAsync` to `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .

## <a name="custom-routes"></a>Trasy niestandardowe

Użyj `@page` dyrektywy, aby:

* Określ trasę niestandardową dla strony. Na przykład trasy do strony informacje można ustawić na wartość `/Some/Other/Path` z `@page "/Some/Other/Path"` .
* Dołącz segmenty do domyślnej trasy strony. Na przykład segment "Item" można dodać do domyślnej trasy strony przy użyciu `@page "item"` .
* Dołącz parametry do domyślnej trasy strony. Na przykład parametr ID, `id` , może być wymagany dla strony z `@page "{id}"` .

Ścieżka względna elementu głównego wypisana przez tyldę ( `~` ) na początku ścieżki jest obsługiwana. Na przykład `@page "~/Some/Other/Path"` jest taka sama jak `@page "/Some/Other/Path"` .

Jeśli nie podoba Ci się ciąg zapytania `?handler=JoinList` w adresie URL, Zmień trasę, aby umieścić nazwę programu obsługi w części adresu URL. Trasę można dostosować, dodając szablon trasy ujęty w podwójne cudzysłowy po `@page` dyrektywie.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Korzystając z powyższego kodu, ścieżka URL, która jest przesyłana do usługi, `OnPostJoinListAsync` to `https://localhost:5001/Customers/CreateFATH/JoinList` . Ścieżka URL, która jest przesyłana do programu, `OnPostJoinListUCAsync` to `https://localhost:5001/Customers/CreateFATH/JoinListUC` .

`?`Poniżej przedstawiono `handler` , że parametr trasy jest opcjonalny.

## <a name="configuration-and-settings"></a>Konfiguracja i ustawienia

Aby skonfigurować opcje zaawansowane, użyj metody rozszerzenia `AddRazorPagesOptions` w konstruktorze MVC:

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

Obecnie można użyć, `RazorPagesOptions` Aby ustawić katalog główny dla stron lub dodać konwencje modelu aplikacji dla stron. W przyszłości włączysz więcej rozszerzeń w ten sposób.

Aby wstępnie skompilować widoki, zobacz [ Razor przeglądanie kompilacji](xref:mvc/views/view-compilation) .

[Pobierz lub Wyświetl przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).

Zobacz Rozpoczynanie [pracy ze Razor stronami](xref:tutorials/razor-pages/razor-pages-start), które kompilują się w tym wprowadzeniu.

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a>Określ, że Razor strony znajdują się w katalogu głównym zawartości

Domyślnie Razor strony są umieszczane w katalogu */Pages* . Dodaj [program with Razor PagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) , aby określić, że Razor strony znajdują się w [katalogu głównym zawartości](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) aplikacji:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a>Określ, że Razor strony znajdują się w niestandardowym katalogu głównym

Dodaj element [with Razor PagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) , aby określić, że Razor strony znajdują się w niestandardowym katalogu głównym w aplikacji (podaj ścieżkę względną):

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Autoryzuj atrybut i Razor strony](xref:security/authorization/simple#aarp)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
