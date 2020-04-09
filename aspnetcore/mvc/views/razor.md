---
title: Odwołanie do składni maszynki do golenia dla ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej o składni znaczników Razor do osadzania kodu opartego na serwerze na stronach sieci Web.
ms.author: riande
ms.date: 02/12/2020
uid: mvc/views/razor
ms.openlocfilehash: dd5c73be56ed0dafb759df2f5ff2eac1a3b5b09e
ms.sourcegitcommit: d03905aadf5ceac39fff17706481af7f6c130411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80381772"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a>Odwołanie do składni maszynki do golenia dla ASP.NET Core

Rick [Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)i Dan [Vicarel](https://github.com/Rabadash8820)

Razor to składnia znaczników do osadzania kodu opartego na serwerze na stronach sieci Web. Składnia Razor składa się z znaczników Razor, C#i HTML. Pliki zawierające Razor zazwyczaj mają rozszerzenie pliku *.cshtml.* Brzytwa znajduje się również w [plikach komponentów Razor](xref:blazor/components) (*.brzytwa*).

## <a name="rendering-html"></a>Renderowanie HTML

Domyślnym językiem składni Razor jest HTML. Renderowanie kodu HTML ze tagów składni Razor nie różni się od renderowania kodu HTML z pliku HTML. Znaczniki HTML w plikach *.cshtml* Razor są renderowane przez serwer bez zmian.

## <a name="razor-syntax"></a>Składnia Razor

Razor obsługuje język C# `@` i używa symbolu do przejścia z HTML do języka C#. Razor ocenia wyrażenia C# i renderuje je w danych wyjściowych HTML.

Gdy `@` po symbolu następuje [słowo kluczowe razor reserved](#razor-reserved-keywords), przechodzi do znaczników specyficznych dla razora. W przeciwnym razie przechodzi do zwykłego języka C#.

Aby uniknąć `@` symbolu znaczników Razor, `@` użyj drugiego symbolu:

```cshtml
<p>@@Username</p>
```

Kod jest renderowany w formacie `@` HTML z jednym symbolem:

```html
<p>@Username</p>
```

Atrybuty HTML i zawartość zawierająca adresy e-mail nie traktują symbolu `@` jako znaku przejścia. Adresy e-mail w poniższym przykładzie są nietknięte przez analizowanie razor:

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a>Niejawne wyrażenia Maszynki do golenia

Niejawne wyrażenia Razor zaczynają `@` się od kodu C#:

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

Z wyjątkiem słowa kluczowego C#, `await` wyrażenia niejawne nie mogą zawierać spacji. Jeśli instrukcja C# ma wyraźne zakończenie, spacje mogą być intermingled:

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

Wyrażenia niejawne **nie mogą** zawierać generycznych znaków`<>`C#, ponieważ znaki wewnątrz nawiasów ( ) są interpretowane jako znacznik HTML. Następujący kod jest **nieprawidłowy:**

```cshtml
<p>@GenericMethod<int>()</p>
```

Poprzedni kod generuje błąd kompilatora podobny do jednego z następujących czynności:

* Element "int" nie został zamknięty. Wszystkie elementy muszą być samozamykające się lub mają pasujący tag końcowy.
* Nie można przekonwertować grupy metod "GenericMethod" na typ "object" nieobejmowany. Czy zamierzał pan powołać się na tę metodę?".

Wywołania metod ogólnych muszą być zawinięte w [jawne wyrażenie Razor](#explicit-razor-expressions) lub [blok kodu Razor](#razor-code-blocks).

## <a name="explicit-razor-expressions"></a>Jawne wyrażenia brzytwy

Jawne wyrażenia Razor `@` składają się z symbolu z zrównoważonym nawiasem. Aby renderować czas w zeszłym tygodniu, używana jest następująca znaczniki Razor:

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

Każda zawartość `@()` w nawiasie jest oceniana i renderowana na dane wyjściowe.

Wyrażenia niejawne, opisane w poprzedniej sekcji, zazwyczaj nie mogą zawierać spacji. W poniższym kodzie jeden tydzień nie jest odejmowany od bieżącego czasu:

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

Kod renderuje następujący kod HTML:

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

Wyrażenia jawne mogą służyć do łączenia tekstu z wynikiem wyrażenia:

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

Bez wyrażenia jawnego `<p>Age@joe.Age</p>` jest traktowany jako `<p>Age@joe.Age</p>` adres e-mail i jest renderowany. Po zapisaniu jako wyrażenie `<p>Age33</p>` jawne, jest renderowany.

Wyrażenia jawne mogą być używane do renderowania danych wyjściowych z metod ogólnych w plikach *.cshtml.* Poniższy znacznik pokazuje, jak poprawić błąd pokazany wcześniej spowodowane przez nawiasy języka ogólnego języka C#. Kod jest napisany jako wyrażenie jawne:

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a>Kodowanie wyrażeń

Wyrażenia C#, które oceniają ciąg są zakodowane w formacie HTML. Wyrażenia C#, które `IHtmlContent` oceniają, są `IHtmlContent.WriteTo`renderowane bezpośrednio za pośrednictwem . Wyrażenia C#, które nie `IHtmlContent` mają oceny są konwertowane na ciąg przez `ToString` i zakodowane przed ich renderowaniem.

```cshtml
@("<span>Hello World</span>")
```

Kod renderuje następujący kod HTML:

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

Kod HTML jest wyświetlany w przeglądarce jako:

```html
<span>Hello World</span>
```

`HtmlHelper.Raw`dane wyjściowe nie są zakodowane, ale renderowane jako znaczniki HTML.

> [!WARNING]
> Używanie `HtmlHelper.Raw` danych wejściowych użytkownika bez użycia bezpieczeństwa jest zagrożeniem bezpieczeństwa. Dane wejściowe użytkownika mogą zawierać złośliwy kod JavaScript lub inne exploity. Odkażanie danych wejściowych użytkownika jest trudne. Unikaj `HtmlHelper.Raw` używania danych wejściowych użytkownika.

```cshtml
@Html.Raw("<span>Hello World</span>")
```

Kod renderuje następujący kod HTML:

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a>Bloki kodów brzytwy

Bloki kodu brzytwy zaczynają się `@` od i są zamknięte przez `{}`. W przeciwieństwie do wyrażeń kod C# wewnątrz bloków kodu nie jest renderowany. Bloki kodu i wyrażenia w widoku współużytkują ten sam zakres i są zdefiniowane w kolejności:

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

Kod renderuje następujący kod HTML:

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

W blokach kodu zadeklaruj [funkcje lokalne](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) za pomocą znaczników, które mają służyć jako metody tworzenia szablonów:

```cshtml
@{
    void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }

    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}
```

Kod renderuje następujący kod HTML:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a>Przejścia niejawne

Domyślnym językiem w bloku kodu jest C#, ale strona Razor może przejść z powrotem do html:

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>Jawne przejście rozdzielane

Aby zdefiniować podsekcję bloku kodu, który powinien renderować kod HTML, otoczyć znaki renderowania tagiem Razor: `<text>`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

Ta ta ta możliwość renderowania kodu HTML, który nie jest otoczony tagiem HTML. Bez tagu HTML lub Razor występuje błąd środowiska uruchomieniowego Razor.

Tag `<text>` jest przydatny do kontrolowania odstępów podczas renderowania zawartości:

* Renderowana jest `<text>` tylko zawartość między tagiem.
* W danych wyjściowych `<text>` HTML nie ma żadnych odstępów przed lub po wyświetlenie znacznika.

### <a name="explicit-line-transition"></a>Jawne przejście wiersza

Aby wypozorować resztę całego wiersza jako `@:` HTML wewnątrz bloku kodu, użyj składni:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

Bez `@:` w kodzie razor błąd środowiska uruchomieniowego jest generowany.

Dodatkowe `@` znaki w pliku Razor może powodować błędy kompilatora w instrukcjach później w bloku. Te błędy kompilatora może być trudne do zrozumienia, ponieważ rzeczywisty błąd występuje przed zgłoszonym błędem. Ten błąd jest często po połączeniu wielu wyrażeń niejawnych/jawnych w jednym bloku kodu.

## <a name="control-structures"></a>Struktury sterowania

Struktury sterowania są rozszerzeniem bloków kodu. Wszystkie aspekty bloków kodu (przejście do znaczników, wbudowany C#) dotyczą również następujących struktur:

### <a name="conditionals-if-else-if-else-and-switch"></a>Warunki, \@jeśli, w przeciwnym razie, w inny sposób, i \@przełączyć

`@if`formantów po uruchomieniu kodu:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

`else`i `else if` nie wymagają `@` symbolu:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

Następujące znaczniki pokazuje, jak używać instrukcji switch:

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a>Zapętlanie, \@ \@foreach, \@ \@while, i zrobić podczas

Szablon HTML może być renderowany za pomocą instrukcji kontroli pętli. Aby wyświetlić listę osób:

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

Obsługiwane są następujące instrukcje pętli:

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a>Związek \@za pomocą

W języku C#instrukcja `using` jest używana w celu zapewnienia, że obiekt jest usuwany. W Razor ten sam mechanizm jest używany do tworzenia pomocników HTML, które zawierają dodatkową zawartość. W poniższym kodzie Pomocnicy `<form>` HTML `@using` renderuje tag z instrukcją:

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a>\@spróbuj, złapać, wreszcie

Obsługa wyjątków jest podobna do języka C#:

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>\@lock

Razor ma możliwość ochrony krytycznych sekcji za pomocą instrukcji blokady:

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>Komentarze

Razor obsługuje komentarze języka C# i HTML:

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

Kod renderuje następujący kod HTML:

```html
<!-- HTML comment -->
```

Komentarze razor są usuwane przez serwer przed renderowaniem strony internetowej. Razor używa `@*  *@` do rozgraniczenia komentarzy. Następujący kod jest komentowany, więc serwer nie renderuje żadnych znaczników:

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a>Dyrektyw

Dyrektywy razor są reprezentowane przez niejawne wyrażenia `@` z zastrzeżonych słów kluczowych po symbolu. Dyrektywa zazwyczaj zmienia sposób, w jaki widok jest analizowany lub włącza różne funkcje.

Zrozumienie, jak Razor generuje kod dla widoku ułatwia zrozumienie, jak działają dyrektywy.

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

Kod generuje klasę podobną do następującej:

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

W dalszej części tego artykułu w sekcji [Sprawdź razor C# klasy generowane dla widoku](#inspect-the-razor-c-class-generated-for-a-view) wyjaśnia, jak wyświetlić tę wygenerowaną klasę.

### <a name="attribute"></a>\@Atrybut

Dyrektywa `@attribute` dodaje dany atrybut do klasy wygenerowanej strony lub widoku. W poniższym `[Authorize]` przykładzie dodaje atrybut:

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a>\@Kod

*Ten scenariusz dotyczy tylko komponentów Razor (.brzytwa).*

Blok `@code` umożliwia [składnik Razor,](xref:blazor/components) aby dodać elementy członkowskie Języka C# (pola, właściwości i metody) do składnika:

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

W przypadku komponentów Razor `@code` [`@functions`](#functions) jest aliasem i zalecanym przez . `@functions` Dopuszczalne `@code` jest więcej niż jeden blok.

::: moniker-end

### <a name="functions"></a>\@Funkcje

Dyrektywa `@functions` umożliwia dodawanie elementów członkowskich języka C# (pól, właściwości i metod) do wygenerowanej klasy:

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

W [komponentach Razor](xref:blazor/components)użyj, `@code` `@functions` aby dodać elementy członkowskie języka C#.

::: moniker-end

Przykład:

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

Kod generuje następujące znaczniki HTML:

```html
<div>From method: Hello</div>
```

Następujący kod jest wygenerowaną klasą Razor C#:

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

`@functions`metody służą jako metody tworzenia szablonów, gdy mają znaczniki:

```cshtml
@{
    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}

@functions {
    private void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }
}
```

Kod renderuje następujący kod HTML:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a>\@Implementuje

Dyrektywa `@implements` implementuje interfejs dla wygenerowanej klasy.

Poniższy przykład <xref:System.IDisposable?displayProperty=fullName> implementuje, <xref:System.IDisposable.Dispose*> aby można było wywołać metodę:

```cshtml
@implements IDisposable

<h1>Example</h1>

@functions {
    private bool _isDisposed;

    ...

    public void Dispose() => _isDisposed = true;
}
```

::: moniker-end

### <a name="inherits"></a>\@Dziedziczy

Dyrektywa `@inherits` zapewnia pełną kontrolę nad klasą dziedziczy widok:

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

Poniższy kod to niestandardowy typ strony Razor:

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

Jest `CustomText` wyświetlany w widoku:

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

Kod renderuje następujący kod HTML:

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 `@model`i `@inherits` mogą być używane w tym samym widoku. `@inherits`może znajdować się w *pliku _ViewImports.cshtml* importowanym przez widok:

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

Poniższy kod jest przykładem widoku silnie typizowanego:

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

Jeślirick@contoso.com" " jest przekazywana w modelu, widok generuje następujące znaczniki HTML:

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a>\@Wstrzyknąć

Dyrektywa `@inject` umożliwia Razor Page wstrzyknąć usługi z [kontenera usługi](xref:fundamentals/dependency-injection) do widoku. Aby uzyskać więcej informacji, zobacz [Iniekcja zależności do widoków](xref:mvc/views/dependency-injection).

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a>\@Układ

*Ten scenariusz dotyczy tylko komponentów Razor (.brzytwa).*

Dyrektywa `@layout` określa układ dla komponentu Razor. Składniki układu są używane w celu uniknięcia duplikacji kodu i niespójności. Aby uzyskać więcej informacji, zobacz <xref:blazor/layouts>.

::: moniker-end

### <a name="model"></a>\@Modelu

*Ten scenariusz dotyczy tylko widoków MVC i stron Razor (.cshtml).*

Dyrektywa `@model` określa typ modelu przekazywane do widoku lub strony:

```cshtml
@model TypeNameOfModel
```

W ASP.NET Core MVC lub Razor Pages aplikacji utworzonej z poszczególnych kont użytkowników, *Widoki/Konto/Login.cshtml* zawiera następującą deklarację modelu:

```cshtml
@model LoginViewModel
```

Wygenerowana klasa dziedziczy po: `RazorPage<dynamic>`

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor udostępnia `Model` właściwość dostępu do modelu przekazywane do widoku:

```cshtml
<div>The Login Email: @Model.Email</div>
```

Dyrektywa `@model` określa typ `Model` właściwości. Dyrektywa określa `T` w `RazorPage<T>` tym wygenerowanej klasy, że widok pochodzi z. Jeśli `@model` dyrektywa nie jest określona, `Model` właściwość jest typu. `dynamic` Aby uzyskać więcej informacji, zobacz [Silnie wpisane modele i @model słowo kluczowe](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).

### <a name="namespace"></a>\@namespace

Dyrektywa: `@namespace`

* Ustawia obszar nazw klasy wygenerowanej strony Razor, widoku MVC lub komponentu Razor.
* Ustawia główne obszary nazw stron, widoków lub klas składników z najbliższego pliku importu w drzewie katalogów, *_ViewImports.cshtml* (widoki lub strony) lub *_Imports.razor* (składniki Razor).

```cshtml
@namespace Your.Namespace.Here
```

W przykładzie Strony razor pokazane w poniższej tabeli:

* Każda strona importuje *pages/_ViewImports.cshtml*.
* *Strony/_ViewImports.cshtml* zawiera `@namespace Hello.World`.
* Każda strona `Hello.World` ma jako katalog główny jej obszaru nazw.

| Strona                                        | Przestrzeń nazw                             |
| ------------------------------------------- | ------------------------------------- |
| *Strony/indeks.cshtml*                        | `Hello.World`                         |
| *Strony/Więcej Stron/Page.cshtml*               | `Hello.World.MorePages`               |
| *Strony/Więcej Stron/EvenMorePages/Page.cshtml* | `Hello.World.MorePages.EvenMorePages` |

Poprzednie relacje dotyczą importu plików używanych z widokami MVC i komponentami Razor.

Jeśli wiele plików `@namespace` importu ma dyrektywę, plik znajdujący się najbliżej strony, widoku lub składnika w drzewie katalogów jest używany do ustawiania głównego obszaru nazw.

Jeśli folder *EvenMorePages* w poprzednim przykładzie zawiera `@namespace Another.Planet` plik importu (lub plik *Pages/MorePages/EvenMorePages/Page.cshtml),* `@namespace Another.Planet`wynik jest wyświetlany w poniższej tabeli.

| Strona                                        | Przestrzeń nazw               |
| ------------------------------------------- | ----------------------- |
| *Strony/indeks.cshtml*                        | `Hello.World`           |
| *Strony/Więcej Stron/Page.cshtml*               | `Hello.World.MorePages` |
| *Strony/Więcej Stron/EvenMorePages/Page.cshtml* | `Another.Planet`        |

### <a name="page"></a>\@Strona

::: moniker range=">= aspnetcore-3.0"

Dyrektywa `@page` ma różne skutki w zależności od typu pliku, w którym się pojawia. Dyrektywa:

* W pliku *cshtml* wskazuje, że plik jest Stroną Razor. Aby uzyskać więcej informacji, <xref:razor-pages/index>zobacz [Trasy niestandardowe](xref:razor-pages/index#custom-routes) i .
* Określa, że składnik Razor powinien obsługiwać żądania bezpośrednio. Aby uzyskać więcej informacji, zobacz <xref:blazor/routing>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Dyrektywa `@page` w pierwszym wierszu pliku *cshtml* wskazuje, że plik jest stroną Razor. Aby uzyskać więcej informacji, zobacz <xref:razor-pages/index>.

::: moniker-end

### <a name="section"></a>\@Sekcji

*Ten scenariusz dotyczy tylko widoków MVC i stron Razor (.cshtml).*

Dyrektywa `@section` jest używana w połączeniu z [układami MVC i Razor Pages,](xref:mvc/views/layout) aby umożliwić widoki lub strony do renderowania zawartości w różnych częściach strony HTML. Aby uzyskać więcej informacji, zobacz <xref:mvc/views/layout>.

### <a name="using"></a>\@using

Dyrektywa `@using` dodaje C# `using` dyrektywy do wygenerowanego widoku:

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

W [komponentach Razor](xref:blazor/components)kontroluje również, `@using` które komponenty są w zakresie.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a>Atrybuty dyrektywy

### <a name="attributes"></a>\@Atrybuty

*Ten scenariusz dotyczy tylko komponentów Razor (.brzytwa).*

`@attributes`umożliwia składnikowi renderowanie nie zadeklarowanych atrybutów. Aby uzyskać więcej informacji, zobacz <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.

### <a name="bind"></a>\@Powiązać

*Ten scenariusz dotyczy tylko komponentów Razor (.brzytwa).*

Powiązanie danych w składnikach `@bind` odbywa się za pomocą atrybutu. Aby uzyskać więcej informacji, zobacz <xref:blazor/data-binding>.

### <a name="onevent"></a>\@na{EVENT}

*Ten scenariusz dotyczy tylko komponentów Razor (.brzytwa).*

Maszynka do golenia zapewnia funkcje obsługi zdarzeń dla komponentów. Aby uzyskać więcej informacji, zobacz <xref:blazor/event-handling>.

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a>\@na{EVENT}:preventDefault

*Ten scenariusz dotyczy tylko komponentów Razor (.brzytwa).*

Zapobiega domyślnej akcji zdarzenia.

### <a name="oneventstoppropagation"></a>\@na{EVENT}:stopPropagacja

*Ten scenariusz dotyczy tylko komponentów Razor (.brzytwa).*

Zatrzymuje propagację zdarzeń dla zdarzenia.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a>\@key

*Ten scenariusz dotyczy tylko komponentów Razor (.brzytwa).*

Atrybut `@key` dyrektywy powoduje, że algorytm diffing składników, aby zagwarantować zachowanie elementów lub składników na podstawie wartości klucza. Aby uzyskać więcej informacji, zobacz <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.

### <a name="ref"></a>\@ref

*Ten scenariusz dotyczy tylko komponentów Razor (.brzytwa).*

Odwołania do`@ref`komponentu ( ) umożliwiają odwoływanie się do wystąpienia składnika, dzięki czemu można wydawać polecenia do tego wystąpienia. Aby uzyskać więcej informacji, zobacz <xref:blazor/components#capture-references-to-components>.

### <a name="typeparam"></a>\@typeparam

*Ten scenariusz dotyczy tylko komponentów Razor (.brzytwa).*

Dyrektywa `@typeparam` deklaruje parametr typu ogólnego dla wygenerowanej klasy składnika. Aby uzyskać więcej informacji, zobacz <xref:blazor/templated-components#generic-typed-components>.

::: moniker-end

## <a name="templated-razor-delegates"></a>Delegowani razor szablonów

Szablony maszynki do golenia umożliwiają zdefiniowanie fragmentu kodu interfejsu użytkownika w następującym formacie:

```cshtml
@<tag>...</tag>
```

Poniższy przykład ilustruje sposób określania szablonu <xref:System.Func%602>Razor delegata jako . [Typ dynamiczny](/dotnet/csharp/programming-guide/types/using-type-dynamic) jest określony dla parametru metody, która jest hermetyzowana przez delegata. [Typ obiektu](/dotnet/csharp/language-reference/keywords/object) jest określony jako zwracana wartość pełnomocnika. Szablon jest używany <xref:System.Collections.Generic.List%601> z, `Pet` że `Name` ma właściwość.

```csharp
public class Pet
{
    public string Name { get; set; }
}
```

```cshtml
@{
    Func<dynamic, object> petTemplate = @<p>You have a pet named <strong>@item.Name</strong>.</p>;

    var pets = new List<Pet>
    {
        new Pet { Name = "Rin Tin Tin" },
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "K-9" }
    };
}
```

Szablon jest renderowany `pets` z `foreach` dostarczonym przez instrukcję:

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

Renderowane wyjście:

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

Można również podać wbudowany szablon Razor jako argument do metody. W poniższym przykładzie `Repeat` metoda odbiera szablon Razor. Metoda używa szablonu do tworzenia zawartości HTML z powtórzeniami elementów dostarczonych z listy:

```cshtml
@using Microsoft.AspNetCore.Html

@functions {
    public static IHtmlContent Repeat(IEnumerable<dynamic> items, int times,
        Func<dynamic, IHtmlContent> template)
    {
        var html = new HtmlContentBuilder();

        foreach (var item in items)
        {
            for (var i = 0; i < times; i++)
            {
                html.AppendHtml(template(item));
            }
        }

        return html;
    }
}
```

Korzystając z listy zwierząt domowych `Repeat` z poprzedniego przykładu, metoda jest wywoływana z:

* <xref:System.Collections.Generic.List%601>z `Pet`.
* Liczba powtórzeń każdego zwierzaka.
* Szablon wbudowany do użycia dla elementów listy listy nieurządzonych.

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

Renderowane wyjście:

```html
<ul>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>K-9</li>
    <li>K-9</li>
    <li>K-9</li>
</ul>
```

## <a name="tag-helpers"></a>Pomocnicy tagów

*Ten scenariusz dotyczy tylko widoków MVC i stron Razor (.cshtml).*

Istnieją trzy dyrektywy, które odnoszą się do [Pomocników Tagów.](xref:mvc/views/tag-helpers/intro)

| Dyrektywy | Funkcja |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | Udostępnia pomocnikom znaczników widok. |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | Usuwa pomocników tagów wcześniej dodanych z widoku. |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | Określa prefiks znacznika, aby włączyć obsługę Pomocnika znaczników i uczynić użycie pomocnika znaczników jawnym. |

## <a name="razor-reserved-keywords"></a>Słowa kluczowe zastrzeżone razor

### <a name="razor-keywords"></a>Razor słowa kluczowe

* (wymaga ASP.NET Core 2.1 lub nowszym)
* namespace
*  — funkcje
* Dziedziczy
* model
* section
* pomocnik (obecnie nie jest obsługiwany przez ASP.NET Core)

Razor słowa kluczowe `@(Razor Keyword)` są wysunął z (na przykład, `@(functions)`).

### <a name="c-razor-keywords"></a>C# Razor słowa kluczowe

* case
* do
* default
* for
* foreach
* if
* else
* lock
* switch
* try
* Złapać
* finally
* using
* while

C# Razor słowa kluczowe muszą `@(@C# Razor Keyword)` być dwukrotnie `@(@case)`uciekł z (na przykład, ). Pierwszy `@` ucieka parser Razor. Drugi `@` wyujmuje parser C#.

### <a name="reserved-keywords-not-used-by-razor"></a>Zarezerwowane słowa kluczowe nie używane przez Razor

* class

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a>Sprawdź klasę Razor C# wygenerowaną pod kątem widoku

::: moniker range=">= aspnetcore-2.1"

W pliku .NET Core SDK 2.1 lub nowszym [zestaw Razor SDK](xref:razor-pages/sdk) obsługuje kompilację plików Razor. Podczas tworzenia projektu, Razor SDK generuje *obj/<build_configuration>/<target_framework_moniker>/Razor* katalogu w katalogu projektu. Struktura katalogów w katalogu *Razor* odzwierciedla strukturę katalogów projektu.

Rozważmy następującą strukturę katalogów w projekcie ASP.NET Core 2.1 Razor Pages kierowanym na program .NET Core 2.1:

* **Obszary/**
  * **Administrator/**
    * **Strony/**
      * *Indeks.cshtml*
      * *Index.cshtml.cs*
* **Strony/**
  * **Współdzielone/**
    * *_Layout.cshtml*
  * *_ViewImports.cshtml*
  * *_ViewStart.cshtml*
  * *Indeks.cshtml*
  * *Index.cshtml.cs*

Tworzenie projektu w konfiguracji *debugowania* daje następujący *katalog obj:*

* **obj/**
  * **Debugowanie/**
    * **netcoreapp2.1/**
      * **Brzytwa/**
        * **Obszary/**
          * **Administrator/**
            * **Strony/**
              * *Index.g.cshtml.cs*
        * **Strony/**
          * **Współdzielone/**
            * *_Layout.g.cshtml.cs*
          * *_ViewImports.g.cshtml.cs*
          * *_ViewStart.g.cshtml.cs*
          * *Index.g.cshtml.cs*

Aby wyświetlić wygenerowaną klasę *pages/index.cshtml*, otwórz *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Dodaj następującą klasę do projektu ASP.NET Core MVC:

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

W `Startup.ConfigureServices`, zastąpić `RazorTemplateEngine` dodane przez MVC `CustomTemplateEngine` z klasy:

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

Ustaw punkt przerwania `return csharpDocument;` na `CustomTemplateEngine`instrukcji . Gdy wykonanie programu zatrzymuje się w punkcie `generatedCode`przerwania, wyświetl wartość .

![Widok wizualizatora tekstu wygenerowanego kodu](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a>Wyświetlanie odnośnych i rozróżniania wielkości liter

Aparat widoku Razor wykonuje wyszukiwania z uwzględnieniem wielkości liter dla widoków. Jednak rzeczywiste wyszukiwanie jest określane przez podstawowy system plików:

* Źródło oparte na plikach:
  * W systemach operacyjnych z niewrażliwymi na wielkości systemami plików (na przykład Windows) fizyczne wyszukiwania dostawcy plików są niewrażliwe na rozmiary. Na przykład `return View("Test")` wyniki w dopasowania dla */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, i inny wariant wielkości liter.
  * W systemach plików z uwzględnieniem wielkości liter (na `EmbeddedFileProvider`przykład Linux, OSX i with), wyszukiwania są rozróżniane. Na przykład `return View("Test")` w szczególności pasuje *do /Views/Home/Test.cshtml*.
* Wstępnie skompilowane widoki: w przypadku ASP.NET Core 2.0 i nowszych widoków jest niewrażliwe na wszystkie systemy operacyjne. Zachowanie jest identyczne z zachowaniem fizycznego dostawcy plików w systemie Windows. Jeśli dwa wstępnie skompilowane widoki różnią się tylko w przypadku, wynik wyszukiwania jest niedeterministyczny.

Deweloperzy są zachęcani do dopasowania wielkości liter nazw plików i katalogów do wielkości:

* Nazwy obszaru, kontrolera i akcji.
* Strony brzytwy.

Dopasowanie przypadku zapewnia wdrożenia znaleźć ich widoki niezależnie od podstawowego systemu plików.

## <a name="additional-resources"></a>Zasoby dodatkowe

[Wprowadzenie do ASP.NET programowania sieci Web Za pomocą składni Razor](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) zapewnia wiele przykładów programowania ze składnią Razor.
