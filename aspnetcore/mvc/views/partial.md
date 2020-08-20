---
title: Częściowe widoki w ASP.NET Core
author: ardalis
description: Odkryj, jak używać widoków częściowych, aby rozbić duże pliki znaczników i zmniejszyć duplikowanie wspólnego znacznika na stronach sieci Web w aplikacjach ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 06/12/2019
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
uid: mvc/views/partial
ms.openlocfilehash: 7a370042520adfe5b06784b76acf41a81eb2c72d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630630"
---
# <a name="partial-views-in-aspnet-core"></a>Częściowe widoki w ASP.NET Core

[Steve Kowalski](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT)i [Scott Sauber](https://twitter.com/scottsauber)

Widok częściowy to [Razor](xref:mvc/views/razor) plik znaczników (*. cshtml*), który renderuje dane wyjściowe HTML *w* innym renderowanym wyjściu pliku znaczników.

::: moniker range=">= aspnetcore-2.1"

Termin *częściowy widok* jest używany podczas tworzenia aplikacji MVC, gdzie pliki znaczników są nazywane *widokami*, lub Razor aplikacją Pages, gdzie pliki znaczników są nazywane *stronami*. Ten temat ogólnie odnosi się do widoków MVC i stron stron Razor jako *plików znaczników*.

::: moniker-end

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="when-to-use-partial-views"></a>Kiedy używać widoków częściowych

Widoki częściowe są efektywnym sposobem:

* Podziel duże pliki znaczników na mniejsze składniki.

  W dużych, złożonych plikach znaczników składających się z kilku elementów logicznych istnieje zaleta pracy z każdą częścią, która jest izolowana do widoku częściowego. Kod w pliku znaczników jest zarządzany, ponieważ znacznik zawiera tylko ogólną strukturę strony i odwołania do widoków częściowych.
* Zmniejszenie duplikatu typowej zawartości znaczników w plikach znaczników.

  Gdy te same elementy znaczników są używane w plikach znaczników, częściowy widok usuwa duplikowanie zawartości znacznika do jednego pliku widoku częściowego. Gdy znacznik zostanie zmieniony w widoku częściowym, aktualizuje renderowane dane wyjściowe plików znaczników, które używają widoku częściowego.

Widoków częściowych nie należy używać do obsługi wspólnych elementów układu. Wspólne elementy układu należy określić w plikach [_Layout. cshtml](xref:mvc/views/layout) .

Nie używaj widoku częściowego, w którym wymagana jest funkcja logiki renderowania złożonego lub wykonywania kodu. Zamiast widoku częściowego, należy użyć [składnika widoku](xref:mvc/views/view-components).

## <a name="declare-partial-views"></a>Zadeklaruj częściowe widoki

::: moniker range=">= aspnetcore-2.0"

Widok częściowy to plik *. cshtml* , który jest przechowywany w folderze *widoki* (MVC) lub na *stronie* ( Razor strony).

W ASP.NET Core MVC kontroler <xref:Microsoft.AspNetCore.Mvc.ViewResult> może zwrócić widok lub widok częściowy. Na Razor stronach <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> można zwrócić widok częściowy reprezentowany jako <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> obiekt. Odwołania do widoków częściowych i renderowania są opisane w sekcji [odwołanie do częściowego widoku](#reference-a-partial-view) .

W przeciwieństwie do widoku MVC lub renderowania stron, widok częściowy nie działa *_ViewStart. cshtml*. Aby uzyskać więcej informacji na temat *_ViewStart. cshtml*, zobacz <xref:mvc/views/layout> .

Nazwy plików widoku częściowego często zaczynają się od znaku podkreślenia ( `_` ). Ta konwencja nazewnictwa nie jest wymagana, ale pomaga wizualnie odróżnić widoki częściowe od widoków i stron.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Widok częściowy jest plikiem znaczników *. cshtml* , który jest przechowywany w folderze *widoki* .

Kontroler <xref:Microsoft.AspNetCore.Mvc.ViewResult> może zwrócić widok lub widok częściowy. Odwołania do widoków częściowych i renderowania są opisane w sekcji [odwołanie do częściowego widoku](#reference-a-partial-view) .

W przeciwieństwie do renderowania widoku MVC widok częściowy nie działa *_ViewStart. cshtml*. Aby uzyskać więcej informacji na temat *_ViewStart. cshtml*, zobacz <xref:mvc/views/layout> .

Nazwy plików widoku częściowego często zaczynają się od znaku podkreślenia ( `_` ). Ta konwencja nazewnictwa nie jest wymagana, ale pomaga wizualnie odróżnić widoki częściowe od widoków.

::: moniker-end

## <a name="reference-a-partial-view"></a>Odwołuje się do widoku częściowego

::: moniker range=">= aspnetcore-2.0"

### <a name="use-a-partial-view-in-a-no-locrazor-pages-pagemodel"></a>Używanie widoku częściowego na Razor stronach PageModel

W ASP.NET Core 2,0 lub 2,1, następująca metoda obsługi renderuje widok częściowy * \_ AuthorPartialRP. cshtml* do odpowiedzi:

```csharp
public IActionResult OnGetPartial() =>
    new PartialViewResult
    {
        ViewName = "_AuthorPartialRP",
        ViewData = ViewData,
    };
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

W ASP.NET Core 2,2 lub nowszej metoda obsługi może Alternatywnie wywołać <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Partial*> metodę w celu utworzenia `PartialViewResult` obiektu:

[!code-csharp[](partial/sample/PartialViewsSample/Pages/DiscoveryRP.cshtml.cs?name=snippet_OnGetPartial)]

::: moniker-end

### <a name="use-a-partial-view-in-a-markup-file"></a>Używanie widoku częściowego w pliku znaczników

::: moniker range=">= aspnetcore-2.1"

W pliku znaczników istnieje kilka sposobów odwoływania się do widoku częściowego. Zalecamy, aby aplikacje korzystały z jednej z następujących metod renderowania asynchronicznego:

* [Pomocnik tagu częściowego](#partial-tag-helper)
* [Asynchroniczny pomocnik HTML](#asynchronous-html-helper)

::: moniker-end

::: moniker range="< aspnetcore-2.1"

W pliku znaczników istnieją dwa sposoby odwoływania się do widoku częściowego:

* [Asynchroniczny pomocnik HTML](#asynchronous-html-helper)
* [Synchroniczny pomocnik HTML](#synchronous-html-helper)

Zalecamy, aby aplikacje korzystały z [asynchronicznego pomocnika HTML](#asynchronous-html-helper).

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="partial-tag-helper"></a>Pomocnik tagu częściowego

[Pomocnik tagu częściowego](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) wymaga ASP.NET Core 2,1 lub nowszego.

Pomocnik tagów częściowej renderuje zawartość asynchronicznie i używa składni podobnej do kodu HTML:

```cshtml
<partial name="_PartialName" />
```

Gdy rozszerzenie pliku jest obecne, pomocnik tagów odwołuje się do widoku częściowego, który musi znajdować się w tym samym folderze co plik znaczników wywołujący widok częściowy:

```cshtml
<partial name="_PartialName.cshtml" />
```

Poniższy przykład odwołuje się do widoku częściowego z poziomu głównego aplikacji. Ścieżki, które zaczynają się od ukośnika ( `~/` ) lub ukośnika ( `/` ), można znaleźć w katalogu głównym aplikacji:

**Razor Page**

```cshtml
<partial name="~/Pages/Folder/_PartialName.cshtml" />
<partial name="/Pages/Folder/_PartialName.cshtml" />
```

**MVC**

```cshtml
<partial name="~/Views/Folder/_PartialName.cshtml" />
<partial name="/Views/Folder/_PartialName.cshtml" />
```

Poniższy przykład odwołuje się do widoku częściowego ze ścieżką względną:

```cshtml
<partial name="../Account/_PartialName.cshtml" />
```

Aby uzyskać więcej informacji, zobacz <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.

::: moniker-end

### <a name="asynchronous-html-helper"></a>Asynchroniczny pomocnik HTML

W przypadku korzystania z pomocnika HTML najlepszym rozwiązaniem jest użycie <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*> . `PartialAsync` zwraca <xref:Microsoft.AspNetCore.Html.IHtmlContent> Typ opakowany w <xref:System.Threading.Tasks.Task%601> . Metoda jest przywoływana przez odtworzenie prefiksu oczekującego wywołania przy użyciu `@` znaku:

```cshtml
@await Html.PartialAsync("_PartialName")
```

Gdy rozszerzenie pliku jest obecne, pomocnik HTML odwołuje się do widoku częściowego, który musi znajdować się w tym samym folderze co plik znaczników wywołujący widok częściowy:

```cshtml
@await Html.PartialAsync("_PartialName.cshtml")
```

Poniższy przykład odwołuje się do widoku częściowego z poziomu głównego aplikacji. Ścieżki, które zaczynają się od ukośnika ( `~/` ) lub ukośnika ( `/` ), można znaleźć w katalogu głównym aplikacji:

::: moniker range=">= aspnetcore-2.1"

**Razor Page**

```cshtml
@await Html.PartialAsync("~/Pages/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Pages/Folder/_PartialName.cshtml")
```

**MVC**

::: moniker-end

```cshtml
@await Html.PartialAsync("~/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Views/Folder/_PartialName.cshtml")
```

Poniższy przykład odwołuje się do widoku częściowego ze ścieżką względną:

```cshtml
@await Html.PartialAsync("../Account/_LoginPartial.cshtml")
```

Alternatywnie możesz renderować widok częściowy za pomocą <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*> . Ta metoda nie zwraca elementu <xref:Microsoft.AspNetCore.Html.IHtmlContent> . Przesyła strumieniowo renderowane dane wyjściowe bezpośrednio do odpowiedzi. Ponieważ metoda nie zwraca wyniku, musi być wywołana w Razor bloku kodu:

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Home/Discovery.cshtml?name=snippet_RenderPartialAsync)]

Ponieważ `RenderPartialAsync` strumienie są renderowane, zapewnia lepszą wydajność w niektórych scenariuszach. W sytuacjach krytycznych dla wydajności należy wykonać testy porównawcze strony przy użyciu obu podejścia i użyć podejścia, które generuje szybszy czas odpowiedzi.

### <a name="synchronous-html-helper"></a>Synchroniczny pomocnik HTML

<xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> i <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> są synchronicznymi odpowiednikami `PartialAsync` i `RenderPartialAsync` , odpowiednio. Nie zaleca się synchronicznych odpowiedników, ponieważ występują scenariusze, w których są one zakleszczeniami. Metody synchroniczne są przeznaczone do usunięcia w przyszłej wersji.

> [!IMPORTANT]
> Jeśli musisz wykonać kod, użyj [składnika widoku](xref:mvc/views/view-components) zamiast widoku częściowego.

::: moniker range=">= aspnetcore-2.1"

Wywołanie `Partial` lub `RenderPartial` wynik w ostrzeżeniu programu Visual Studio Analyzer. Na przykład, obecność `Partial` daje następujący komunikat ostrzegawczy:

> Użycie IHtmlHelper. częściowe może spowodować zakleszczenia aplikacji. Rozważ użycie &lt; &gt; pomocnika tagów częściowych lub IHtmlHelper. PartialAsync.

Zamień wywołania na `@Html.Partial` with `@await Html.PartialAsync` lub [pomocnika tagów częściowych](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper). Aby uzyskać więcej informacji na temat migracji pomocnika częściowego znacznika, zobacz [Migrowanie z pomocnika HTML](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).

::: moniker-end

## <a name="partial-view-discovery"></a>Odnajdywanie widoku częściowego

Gdy do widoku częściowego odwołuje się nazwa bez rozszerzenia pliku, następujące lokalizacje są przeszukiwane w podanej kolejności:

::: moniker range=">= aspnetcore-2.1"

**Razor Page**

1. Aktualnie wykonywany folder strony
1. Wykres katalogu powyżej folderu strony
1. `/Shared`
1. `/Pages/Shared`
1. `/Views/Shared`

**MVC**

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`
1. `/Pages/Shared`

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`

::: moniker-end

Następujące konwencje dotyczą odnajdywania widoku częściowego:

* Różne widoki częściowe o tej samej nazwie pliku są dozwolone, gdy częściowe widoki znajdują się w różnych folderach.
* W przypadku odwoływania się do widoku częściowego według nazwy bez rozszerzenia pliku, gdy widok częściowy znajduje się zarówno w folderze wywołującym, jak i w folderze *udostępnionym* , widok częściowy w folderze obiektu wywołującego dostarcza widok częściowy. Jeśli widok częściowy nie znajduje się w folderze wywołującym, w folderze *udostępnionym* zostanie udostępniony widok częściowy. Częściowe widoki w folderze *udostępnionym* są nazywane *widokami części udostępnionych* lub *domyślnymi widokami częściowymi*.
* Częściowe widoki mogą być *łańcucha*częściowy &mdash; Widok może wywoływać inny widok częściowy, jeśli odwołanie cykliczne nie jest tworzone przez wywołania. Ścieżki względne są zawsze względne w stosunku do bieżącego pliku, nie do głównego lub nadrzędnego pliku.

> [!NOTE]
> Element [Razor](xref:mvc/views/razor) `section` zdefiniowany w widoku częściowym jest niewidoczny dla nadrzędnych plików znaczników. `section`Jest widoczny tylko dla widoku częściowego, w którym jest zdefiniowany.

## <a name="access-data-from-partial-views"></a>Dostęp do danych z widoków częściowych

Po utworzeniu wystąpienia widoku częściowego otrzymuje on *kopię* słownika elementu nadrzędnego `ViewData` . Aktualizacje wprowadzone do danych w widoku częściowym nie są utrwalane w widoku nadrzędnym. `ViewData` zmiany w częściowym widoku są tracone po powrocie widoku częściowego.

Poniższy przykład ilustruje, jak przekazać wystąpienie elementu [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) do widoku częściowego:

```cshtml
@await Html.PartialAsync("_PartialName", customViewData)
```

Można przekazać model do widoku częściowego. Model może być obiektem niestandardowym. Można przekazać model z `PartialAsync` (renderuje blok zawartości do obiektu wywołującego) lub `RenderPartialAsync` (strumieniowo zawartość do danych wyjściowych):

```cshtml
@await Html.PartialAsync("_PartialName", model)
```

::: moniker range=">= aspnetcore-2.1"

**Razor Page**

Następujące znaczniki w przykładowej aplikacji pochodzą ze strony *stron/ArticlesRP/ReadRP. cshtml* . Strona zawiera dwa widoki częściowe. Drugi widok częściowy przechodzi w modelu i `ViewData` do widoku częściowego. `ViewDataDictionary`Przeciążenie konstruktora służy do przekazywania nowego `ViewData` słownika podczas zachowywania istniejącego `ViewData` słownika.

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/ReadRP.cshtml?name=snippet_ReadPartialViewRP&highlight=5,15-20)]

*Pages/Shared/_AuthorPartialRP. cshtml* to pierwszy widok częściowy, do którego odwołuje się plik znacznika *ReadRP. cshtml* :

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/Shared/_AuthorPartialRP.cshtml)]

*Pages/ArticlesRP/_ArticleSectionRP. cshtml* to drugi widok częściowy, do którego odwołuje się plik znaczników *ReadRP. cshtml* :

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/_ArticleSectionRP.cshtml)]

**MVC**

::: moniker-end

Poniższy znacznik w aplikacji przykładowej pokazuje widok *widoki/artykuły/Read. cshtml* . Widok zawiera dwa widoki częściowe. Drugi widok częściowy przechodzi w modelu i `ViewData` do widoku częściowego. `ViewDataDictionary`Przeciążenie konstruktora służy do przekazywania nowego `ViewData` słownika podczas zachowywania istniejącego `ViewData` słownika.

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/Read.cshtml?name=snippet_ReadPartialView&highlight=5,15-20)]

*Widoki/Shared/_AuthorPartial. cshtml* to pierwszy widok częściowy, do którego odwołuje się plik znaczników *odczytu. cshtml* :

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Shared/_AuthorPartial.cshtml)]

*Widoki/artykuły/_ArticleSection. cshtml* to drugi widok częściowy, do którego odwołuje się plik znaczników *odczytu. cshtml* :

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/_ArticleSection.cshtml)]

W czasie wykonywania częściowe są renderowane do renderowanego wyjściowego pliku znaczników, który sam jest renderowany w udostępnionym *_Layout. cshtml*. Pierwszy widok częściowy renderuje nazwę autora artykułu i datę publikacji:

> Abraham Lincoln
>
> Ten widok częściowy z &lt; udostępnionej ścieżki pliku widoku częściowego &gt; .
> 11/19/1863 12:00:00 AM

Drugi widok częściowy renderuje sekcje artykułu:

> Sekcja jeden indeks: 0
>
> Cztery wyniki i siedem lat temu...
>
> Sekcja dwa indeks: 1
>
> Teraz pracujemy nad znakomitym wojny cywilnej, testowaniem...
>
> Sekcja trzy indeks: 2
>
> Jednak w większym sensie nie można przeznaczyć...

## <a name="additional-resources"></a>Dodatkowe zasoby

::: moniker range=">= aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end
