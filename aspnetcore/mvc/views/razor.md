---
title: odwołanie do składni Razor dla ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej o Razor składni znaczników na potrzeby osadzania kodu opartego na serwerze w stronach sieci Web.
ms.author: riande
ms.date: 02/12/2020
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
uid: mvc/views/razor
ms.openlocfilehash: 60471232b3373039404b27c4afd1a1725d4d21eb
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586868"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a>Razor odwołanie do składni dla ASP.NET Core

Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)i [Dan Vicarel](https://github.com/Rabadash8820)

Razor jest składnią znaczników na potrzeby osadzania kodu opartego na serwerze w stronach sieci Web. RazorSkładnia składa się z Razor znaczników, C# i HTML. Pliki zawierające Razor generalnie mają rozszerzenie *. cshtml* . Razorznajduje się również w plikach [ Razor składników](xref:blazor/components/index) (*. Razor*).

## <a name="rendering-html"></a>Renderowanie HTML

RazorJęzykiem domyślnym jest HTML. Renderowanie kodu HTML z Razor znaczników nie różni się od renderowania kodu HTML z pliku HTML. Znaczniki HTML w plikach *. cshtml* Razor są renderowane przez serwer bez zmian.

## <a name="razor-syntax"></a>Razor obowiązuje

Razor obsługuje język C# i używa `@` symbolu do przejścia z HTML do języka c#. Razor oblicza wyrażenia języka C# i renderuje je w danych wyjściowych w formacie HTML.

Gdy `@` po symbolu następuje [ Razor zastrzeżone słowo kluczowe](#razor-reserved-keywords), przechodzi do Razor specyficznego znacznika. W przeciwnym razie przechodzi do zwykłego języka C#.

Aby wypróbować `@` symbol w Razor znaczniku, użyj drugiego `@` symbolu:

```cshtml
<p>@@Username</p>
```

Kod jest renderowany w języku HTML z pojedynczym `@` symbolem:

```html
<p>@Username</p>
```

Atrybuty i zawartość HTML zawierające adresy e-mail nie traktują `@` symbolu jako znaku przejścia. W poniższym przykładzie adresy e-mail są nienaruszone, Razor Analiza:

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a>Wyrażenia niejawne Razor

Wyrażenia niejawne Razor zaczynają się od `@` kodu C#:

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

Z wyjątkiem `await` słowa kluczowego języka C# wyrażenia niejawne nie mogą zawierać spacji. Jeśli instrukcja języka C# ma jasne zakończenie, spacje mogą być wymieszaniu:

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

Wyrażenia niejawne **nie mogą** zawierać typów ogólnych języka C#, ponieważ znaki wewnątrz nawiasów ( `<>` ) są interpretowane jako tag HTML. Następujący **kod jest nieprawidłowy** :

```cshtml
<p>@GenericMethod<int>()</p>
```

Poprzedni kod generuje błąd kompilatora podobny do jednego z następujących:

* Element "int" nie został zamknięty. Wszystkie elementy muszą być albo zamykane samodzielnie lub mieć pasujący tag końcowy.
* Nie można przekonwertować grupy metod "GenericMethod" na typ inny niż delegatd "Object". Czy chodziło o wywołanie metody?

Wywołania metody ogólnej muszą być opakowane w [jawne Razor wyrażenie](#explicit-razor-expressions) lub [ Razor blok kodu](#razor-code-blocks).

## <a name="explicit-razor-expressions"></a>RazorWyrażenia jawne

RazorWyrażenia jawne składają się z `@` symbolu z wyrównanym nawiasem. W celu renderowania czasu ostatniego tygodnia Razor używane są następujące znaczniki:

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

Każda zawartość w `@()` nawiasie zostanie oceniona i przerenderowana do danych wyjściowych.

Wyrażenia niejawne, opisane w poprzedniej sekcji, zazwyczaj nie mogą zawierać spacji. W poniższym kodzie tydzień nie jest odejmowany od bieżącego czasu:

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

Kod renderuje następujący HTML:

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

Bez wyrażenia jawnego `<p>Age@joe.Age</p>` jest traktowany jako adres e-mail i `<p>Age@joe.Age</p>` jest renderowany. Po zapisaniu jako jawne wyrażenie `<p>Age33</p>` jest renderowane.

Wyrażenia jawne mogą służyć do renderowania danych wyjściowych z metod ogólnych w plikach *. cshtml* . W poniższym znaczniku pokazano, jak poprawić przedstawiony wcześniej błąd w nawiasach ogólnych języka C#. Kod jest zapisywana jako jawne wyrażenie:

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a>Kodowanie wyrażeń

Wyrażenia języka C#, które są obliczane jako ciąg, są kodowane w formacie HTML. Wyrażenia języka C#, które mają być `IHtmlContent` renderowane bezpośrednio przez `IHtmlContent.WriteTo` . Wyrażenia języka C#, które nie są szacowane do, `IHtmlContent` są konwertowane na ciąg przez `ToString` i kodowane przed ich renderowaniem.

```cshtml
@("<span>Hello World</span>")
```

Poprzedni kod renderuje następujący HTML:

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

KOD HTML jest wyświetlany w przeglądarce jako zwykły tekst:

&lt;zakres &gt; Hello World &lt; /span&gt;

`HtmlHelper.Raw` dane wyjściowe nie są kodowane, ale renderowane jako znaczniki HTML.

> [!WARNING]
> Korzystanie `HtmlHelper.Raw` z nieoczyszczonych danych wejściowych użytkownika stanowi zagrożenie bezpieczeństwa. Dane wejściowe użytkownika mogą zawierać złośliwe skrypty JavaScript lub inne luki w zabezpieczeniach. Oczyszczanie danych wejściowych użytkownika jest trudne. Unikaj używania `HtmlHelper.Raw` z danymi wejściowymi użytkownika.

```cshtml
@Html.Raw("<span>Hello World</span>")
```

Kod renderuje następujący HTML:

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a>Razor bloki kodu

Razor bloki kodu zaczynają się od `@` i są ujęte w nawiasy `{}` . W przeciwieństwie do wyrażeń, kod C# wewnątrz bloków kodu nie jest renderowany. Bloki kodu i wyrażenia w widoku mają ten sam zakres i są zdefiniowane w kolejności:

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

Kod renderuje następujący HTML:

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

W blokach kodu Zadeklaruj [funkcje lokalne](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) z adiustacją, aby zapewnić metody tworzenia szablonów:

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

Kod renderuje następujący HTML:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a>Przejścia niejawne

Językiem domyślnym w bloku kodu jest C#, ale Razor strona może przejść z powrotem do HTML:

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>Jawne ograniczone przechodzenie

Aby zdefiniować podsekcję bloku kodu, który powinien renderować HTML, należy ująć znaki do renderowania za pomocą Razor `<text>` tagu:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

To podejście służy do renderowania kodu HTML, który nie jest ujęty w tag HTML. Bez tagu HTML lub Razor tag wystąpi Razor błąd w czasie wykonywania.

`<text>`Tag jest przydatny do kontrolowania odstępów podczas renderowania zawartości:

* Tylko zawartość między `<text>` tagiem jest renderowana.
* W danych wyjściowych HTML nie ma odstępów przed tagiem ani po nim `<text>` .

### <a name="explicit-line-transition"></a>Jawne przejście liniowe

Aby renderować resztę całego wiersza jako HTML wewnątrz bloku kodu, użyj `@:` składni:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

Bez `@:` kodu, Razor generowany jest błąd czasu wykonywania.

Dodatkowe `@` znaki w Razor pliku mogą spowodować błędy kompilatora w instrukcjach znajdujących się w dalszej części bloku. Te błędy kompilatora mogą być trudne do zrozumienia, ponieważ rzeczywisty błąd występuje przed zgłoszonym błędem. Ten błąd jest typowy po połączeniu wielu wyrażeń jawnych/jawnych w jeden blok kodu.

## <a name="control-structures"></a>Struktury formantów

Struktury formantów są rozszerzeniem bloków kodu. Wszystkie aspekty bloków kodu (przechodzenie do znaczników, wbudowane C#) również mają zastosowanie do następujących struktur:

### <a name="conditionals-if-else-if-else-and-switch"></a>Warunki `@if, else if, else, and @switch`

`@if` kontroluje, kiedy kod jest uruchamiany:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

`else` i `else if` nie wymaga `@` symbolu:

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

W poniższym znaczniku pokazano, jak używać instrukcji switch:

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

### <a name="looping-for-foreach-while-and-do-while"></a>Pętli `@for, @foreach, @while, and @do while`

KOD HTML z szablonem może być renderowany przy użyciu instrukcji sterowania pętlą. Aby renderować listę osób:

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

### <a name="compound-using"></a>Złożonego `@using`

W języku C# `using` instrukcja jest używana w celu upewnienia się, że obiekt został usunięty. W programie Razor ten sam mechanizm jest używany do tworzenia pomocników HTML, które zawierają dodatkową zawartość. W poniższym kodzie, pomocników HTML renderuje `<form>` tag przy użyciu `@using` instrukcji:

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### `@try, catch, finally`

Obsługa wyjątków jest podobna do języka C#:

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

Razor ma możliwość ochrony sekcji krytycznych przy użyciu instrukcji lock:

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>Komentarze

Razor obsługuje komentarze w językach C# i HTML:

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

Kod renderuje następujący HTML:

```html
<!-- HTML comment -->
```

Razor Komentarze są usuwane przez serwer przed renderowaniem strony sieci Web. Razor używa `@*  *@` do ograniczania komentarzy. Poniższy kod jest oznaczony jako komentarz, więc serwer nie renderuje żadnego znacznika:

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

Razor dyrektywy są reprezentowane przez niejawne wyrażenia z zastrzeżonymi słowami kluczowymi po `@` symbolu. Dyrektywa zwykle zmienia sposób analizowania widoku lub umożliwia wykonywanie różnych funkcji.

Zrozumienie, jak program Razor generuje kod dla widoku ułatwia zrozumienie, jak działają dyrektywy.

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

W dalszej części tego artykułu w sekcji [Badanie Razor klasy języka C# wygenerowanej dla widoku](#inspect-the-razor-c-class-generated-for-a-view) wyjaśniono sposób wyświetlania tej wygenerowanej klasy.

### `@attribute`

`@attribute`Dyrektywa dodaje dany atrybut do klasy wygenerowanej strony lub widoku. Poniższy przykład dodaje `[Authorize]` atrybut:

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

*Ten scenariusz dotyczy tylko Razor składników (. Razor).*

`@code`Blok umożliwia [ Razor składnikowi](xref:blazor/components/index) Dodawanie elementów członkowskich języka C# (pól, właściwości i metod) do składnika:

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

W przypadku Razor składników `@code` jest to alias [`@functions`](#functions) i zalecane `@functions` . Dozwolony jest więcej niż jeden `@code` blok.

::: moniker-end

### `@functions`

`@functions`Dyrektywa umożliwia dodawanie elementów członkowskich C# (pól, właściwości i metod) do wygenerowanej klasy:

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

W obszarze [ Razor składniki](xref:blazor/components/index)Użyj `@code` , `@functions` Aby dodać elementy członkowskie języka C#.

::: moniker-end

Na przykład:

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

Kod generuje następujący znacznik HTML:

```html
<div>From method: Hello</div>
```

Następujący kod jest wygenerowaną Razor klasą języka C#:

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

`@functions` metody służą jako metody tworzenia szablonów, gdy mają znaczniki:

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

Kod renderuje następujący HTML:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

`@implements`Dyrektywa implementuje interfejs dla wygenerowanej klasy.

Poniższy przykład implementuje, <xref:System.IDisposable?displayProperty=fullName> Aby <xref:System.IDisposable.Dispose*> można było wywołać metodę:

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

### `@inherits`

`@inherits`Dyrektywa zapewnia pełną kontrolę nad klasą, którą dziedziczy widok:

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

Następujący kod jest niestandardowym Razor typem strony:

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

`CustomText`Zostanie wyświetlony w widoku:

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

Kod renderuje następujący HTML:

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 `@model` i `@inherits` mogą być używane w tym samym widoku. `@inherits` może znajdować się w pliku *_ViewImports. cshtml* , który został zaimportowany przez widok:

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

Poniższy kod stanowi przykład widoku o jednoznacznie określonym typie:

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

W przypadku rick@contoso.com przekazywania "" w modelu widok generuje następujący znacznik HTML:

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

`@inject`Dyrektywa umożliwia Razor stronie dodanie usługi z [kontenera usługi](xref:fundamentals/dependency-injection) do widoku. Aby uzyskać więcej informacji, zobacz [iniekcja zależności w widokach](xref:mvc/views/dependency-injection).

::: moniker range=">= aspnetcore-3.0"

### `@layout`

*Ten scenariusz dotyczy tylko Razor składników (. Razor).*

`@layout`Dyrektywa określa układ dla składników rutowanych Razor , które mają [`@page`](#page) dyrektywę. Składniki układu są używane do uniknięcia duplikowania kodu i niespójności. Aby uzyskać więcej informacji, zobacz <xref:blazor/layouts>.

::: moniker-end

### `@model`

*Ten scenariusz ma zastosowanie tylko do widoków i Razor stron MVC (. cshtml).*

`@model`Dyrektywa określa typ modelu przekazaną do widoku lub strony:

```cshtml
@model TypeNameOfModel
```

W aplikacji ASP.NET Core MVC lub Razor stronach utworzonych przy użyciu poszczególnych kont użytkowników, *widoki/konta/login. cshtml* zawierają następującą deklarację modelu:

```cshtml
@model LoginViewModel
```

Wygenerowana Klasa dziedziczy po `RazorPage<dynamic>` :

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor uwidacznia `Model` Właściwość do uzyskiwania dostępu do modelu przekazaną do widoku:

```cshtml
<div>The Login Email: @Model.Email</div>
```

`@model`Dyrektywa określa typ `Model` właściwości. Dyrektywa określa, `T` w `RazorPage<T>` którym wygenerowanej klasie, z której pochodzi widok. Jeśli `@model` dyrektywa nie jest określona, `Model` Właściwość jest typu `dynamic` . Aby uzyskać więcej informacji, zobacz [silnie wpisane modele i @model słowo kluczowe](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).

### `@namespace`

`@namespace`Dyrektywa:

* Ustawia przestrzeń nazw klasy wygenerowanej Razor strony, widoku MVC lub Razor składnika.
* Ustawia główne pochodne przestrzenie nazw stron, widoków lub klas składników z najbliższego pliku importów w drzewie katalogów, *_ViewImports. cshtml* (widoki lub strony) lub *_Imports. Razor* ( Razor składniki).

```cshtml
@namespace Your.Namespace.Here
```

Dla Razor przykładu stron przedstawionych w poniższej tabeli:

* Każda Strona importuje *strony/_ViewImports. cshtml*.
* *Strona/_ViewImports. cshtml* zawiera `@namespace Hello.World` .
* Każda Strona ma `Hello.World` jako element główny przestrzeni nazw.

| Strona                                        | Przestrzeń nazw                             |
| ------------------------------------------- | ------------------------------------- |
| *Pages/index. cshtml*                        | `Hello.World`                         |
| *Pages/MorePages/Page. cshtml*               | `Hello.World.MorePages`               |
| *Pages/MorePages/EvenMorePages/Page. cshtml* | `Hello.World.MorePages.EvenMorePages` |

Powyższe relacje mają zastosowanie do plików importu używanych ze składnikami i widokami MVC Razor .

Gdy wiele plików importu ma `@namespace` dyrektywę, plik znajdujący się najbliżej strony, widoku lub składnika w drzewie katalogów jest używany do ustawiania głównej przestrzeni nazw.

Jeśli folder *EvenMorePages* w poprzednim przykładzie ma plik Imports z plikiem `@namespace Another.Planet` (lub *strony/MorePages/EvenMorePages/Page. cshtml* zawiera `@namespace Another.Planet` ), wynik zostanie przedstawiony w poniższej tabeli.

| Strona                                        | Przestrzeń nazw               |
| ------------------------------------------- | ----------------------- |
| *Pages/index. cshtml*                        | `Hello.World`           |
| *Pages/MorePages/Page. cshtml*               | `Hello.World.MorePages` |
| *Pages/MorePages/EvenMorePages/Page. cshtml* | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

`@page`Dyrektywa ma różne skutki w zależności od typu pliku, w którym występuje. Dyrektywa:

* W pliku *. cshtml* wskazuje, że plik jest Razor stroną. Aby uzyskać więcej informacji, zobacz [trasy niestandardowe](xref:razor-pages/index#custom-routes) i <xref:razor-pages/index> .
* Określa, że Razor składnik powinien obsługiwać żądania bezpośrednio. Aby uzyskać więcej informacji, zobacz <xref:blazor/fundamentals/routing>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

`@page`Dyrektywa w pierwszym wierszu pliku *. cshtml* wskazuje, że plik jest Razor stroną. Aby uzyskać więcej informacji, zobacz <xref:razor-pages/index>.

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

### `@preservewhitespace`

*Ten scenariusz dotyczy tylko Razor składników ( `.razor` ).*

Gdy jest ustawiona na `false` (domyślnie), biały znak w renderowanej adjustacji ze Razor składników ( `.razor` ) jest usuwany, jeśli:

* Początkowe lub końcowe w obrębie elementu.
* Interlinia lub kończąca się w `RenderFragment` parametrze. Na przykład zawartość podrzędna została przeniesiona do innego składnika.
* Poprzedza lub następuje blok kodu w języku C#, taki jak `@if` lub `@foreach` .

::: moniker-end

### `@section`

*Ten scenariusz ma zastosowanie tylko do widoków i Razor stron MVC (. cshtml).*

`@section`Dyrektywa jest używana w połączeniu z [ Razor układami MVC i Pages](xref:mvc/views/layout) , aby umożliwić widokom i stronom renderowanie zawartości w różnych częściach strony html. Aby uzyskać więcej informacji, zobacz <xref:mvc/views/layout>.

### `@using`

`@using`Dyrektywa dodaje `using` dyrektywę C# do wygenerowanego widoku:

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

W [ Razor składniku składniki](xref:blazor/components/index), `@using` również kontroluje, które składniki znajdują się w zakresie.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a>Atrybuty dyrektywy

Razor atrybuty dyrektywy są reprezentowane przez niejawne wyrażenia z zastrzeżonymi słowami kluczowymi po `@` symbolu. Atrybut dyrektywy zazwyczaj zmienia sposób analizowania elementu lub umożliwia korzystanie z różnych funkcji.

### `@attributes`

*Ten scenariusz dotyczy tylko Razor składników (. Razor).*

`@attributes` zezwala składnikowi na renderowanie niezadeklarowanych atrybutów. Aby uzyskać więcej informacji, zobacz <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.

### `@bind`

*Ten scenariusz dotyczy tylko Razor składników (. Razor).*

Powiązanie danych w składnikach jest realizowane przy użyciu `@bind` atrybutu. Aby uzyskać więcej informacji, zobacz <xref:blazor/components/data-binding>.

### `@on{EVENT}`

*Ten scenariusz dotyczy tylko Razor składników (. Razor).*

Razor udostępnia funkcje obsługi zdarzeń dla składników programu. Aby uzyskać więcej informacji, zobacz <xref:blazor/components/event-handling>.

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

*Ten scenariusz dotyczy tylko Razor składników (. Razor).*

Zapobiega domyślnej akcji dla zdarzenia.

### `@on{EVENT}:stopPropagation`

*Ten scenariusz dotyczy tylko Razor składników (. Razor).*

Powoduje zatrzymanie propagacji zdarzenia dla zdarzenia.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

*Ten scenariusz dotyczy tylko Razor składników (. Razor).*

`@key`Atrybut dyrektywy powoduje, że algorytmy porównujące składniki, aby zagwarantować zachowywanie elementów lub składników na podstawie wartości klucza. Aby uzyskać więcej informacji, zobacz <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.

### `@ref`

*Ten scenariusz dotyczy tylko Razor składników (. Razor).*

Odwołania do składników ( `@ref` ) umożliwiają odwoływanie się do wystąpienia składnika, dzięki czemu można wydać polecenia do tego wystąpienia. Aby uzyskać więcej informacji, zobacz <xref:blazor/components/index#capture-references-to-components>.

### `@typeparam`

*Ten scenariusz dotyczy tylko Razor składników (. Razor).*

`@typeparam`Dyrektywa deklaruje parametr typu ogólnego dla wygenerowanej klasy składnika. Aby uzyskać więcej informacji, zobacz <xref:blazor/components/templated-components>.

::: moniker-end

## <a name="templated-razor-delegates"></a>Delegaty z szablonami Razor

Razor Szablony umożliwiają zdefiniowanie fragmentu interfejsu użytkownika w następującym formacie:

```cshtml
@<tag>...</tag>
```

Poniższy przykład ilustruje sposób określania delegata z szablonem Razor jako <xref:System.Func%602> . [Typ dynamiczny](/dotnet/csharp/programming-guide/types/using-type-dynamic) jest określony dla parametru metody, która jest hermetyzowana przez delegata. [Typ obiektu](/dotnet/csharp/language-reference/keywords/object) jest określony jako wartość zwracana delegata. Szablon jest używany z elementem <xref:System.Collections.Generic.List%601> `Pet` zawierającym `Name` Właściwość.

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

Szablon jest renderowany przy użyciu `pets` instrukcji dostarczonej przez `foreach` instrukcję:

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

Renderowane dane wyjściowe:

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

Możesz również podać Razor szablon wbudowany jako argument metody. W poniższym przykładzie `Repeat` Metoda otrzymuje Razor szablon. Metoda używa szablonu do tworzenia zawartości HTML z powtarzanymi elementami podanymi z listy:

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

Korzystając z listy zwierząt domowych z poprzedniego przykładu, `Repeat` Metoda jest wywoływana z:

* <xref:System.Collections.Generic.List%601> z programu `Pet` .
* Liczba powtórzeń poszczególnych zwierząt domowych.
* Wbudowany szablon do użycia dla elementów listy nieuporządkowanej listy.

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

Renderowane dane wyjściowe:

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

*Ten scenariusz ma zastosowanie tylko do widoków i Razor stron MVC (. cshtml).*

Istnieją trzy dyrektywy, które odnoszą się do [pomocników tagów](xref:mvc/views/tag-helpers/intro).

| Dyrektywę | Funkcja |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | Sprawia, że pomocnicy tagów są dostępni do widoku. |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | Usuwa pomocników tagów, które zostały wcześniej dodane z widoku. |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | Określa prefiks tagu w celu włączenia obsługi pomocnika tagów i zapewnienia jawnego użycia pomocnika tagów. |

## <a name="razor-reserved-keywords"></a>Razor zastrzeżone słowa kluczowe

### <a name="razor-keywords"></a>Razor służąc

* `page` (Wymaga ASP.NET Core 2,1 lub nowszego)
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* `helper` (Obecnie nie jest obsługiwane przez ASP.NET Core)

Razor Słowa kluczowe są wyprowadzane z `@(Razor Keyword)` (na przykład `@(functions)` ).

### <a name="c-razor-keywords"></a>RazorSłowa kluczowe języka C#

* `case`
* `do`
* `default`
* `for`
* `foreach`
* `if`
* `else`
* `lock`
* `switch`
* `try`
* `catch`
* `finally`
* `using`
* `while`

RazorSłowa kluczowe języka C# muszą mieć podwójne ucieczki z `@(@C# Razor Keyword)` (na przykład `@(@case)` ). Pierwszy `@` wyprowadza Razor Analizator. Drugi `@` wyprowadza analizator składni języka C#.

### <a name="reserved-keywords-not-used-by-razor"></a>Zastrzeżone słowa kluczowe, które nie są używane przez Razor

* `class`

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a>Sprawdzanie Razor klasy języka C# wygenerowanej dla widoku

::: moniker range=">= aspnetcore-2.1"

W zestaw .NET Core SDK 2,1 lub nowszej, [ Razor zestaw SDK](xref:razor-pages/sdk) obsługuje kompilację Razor plików. Podczas kompilowania projektu Razor zestaw SDK generuje *obj/<build_configuration>/<target_framework_moniker>/ Razor* katalogu w katalogu głównym projektu. Struktura katalogów w *Razor* katalogu odzwierciedla strukturę katalogu projektu.

Rozważmy następującą strukturę katalogów w projekcie ASP.NET Core stron 2,1 na Razor platformie .NET Core 2,1:

```
 Areas/
   Admin/
     Pages/
       Index.cshtml
       Index.cshtml.cs
 Pages/
   Shared/
     _Layout.cshtml
   _ViewImports.cshtml
   _ViewStart.cshtml
   Index.cshtml
   Index.cshtml.cs
  ```

Kompilowanie projektu w konfiguracji *debugowania* powoduje zwrócenie następującego katalogu *obj* :

```
 obj/
   Debug/
     netcoreapp2.1/
       Razor/
         Areas/
           Admin/
             Pages/
               Index.g.cshtml.cs
         Pages/
           Shared/
             _Layout.g.cshtml.cs
           _ViewImports.g.cshtml.cs
           _ViewStart.g.cshtml.cs
           Index.g.cshtml.cs
```

Aby wyświetlić wygenerowaną klasę dla *stron/index. cshtml*, Otwórz *obj/Debug/netcoreapp 2.1/ Razor /Pages/index.g.cshtml.cs*.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Dodaj następującą klasę do projektu ASP.NET Core MVC:

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

W programie `Startup.ConfigureServices` Zastąp wartość `RazorTemplateEngine` dodany przez MVC `CustomTemplateEngine` klasą:

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

Ustaw punkt przerwania na `return csharpDocument;` instrukcji `CustomTemplateEngine` . Gdy wykonanie programu zatrzyma się w punkcie przerwania, Wyświetl wartość `generatedCode` .

![Widok wizualizatora tekstu generatedCode](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a>Wyświetl wyszukiwania i rozróżnianie wielkości liter

RazorAparat widoku wykonuje wyszukiwania z uwzględnieniem wielkości liter w widokach. Jednak rzeczywiste wyszukiwanie jest określane przez podstawowy system plików:

* Źródło na podstawie pliku:
  * W systemach operacyjnych z systemami plików bez uwzględniania wielkości liter (na przykład Windows) wyszukiwania dostawcy plików fizycznych nie uwzględniają wielkości liter. Na przykład `return View("Test")` wyniki są zgodne z */views/Home/test.cshtml*, */views/Home/test.cshtml* i innymi wariantami wielkości liter.
  * W przypadku systemów plików z uwzględnieniem wielkości liter (na przykład Linux, OSX i with `EmbeddedFileProvider` ) Wyszukiwanie jest rozróżniana wielkość liter. Na przykład, w odniesieniu do `return View("Test")` */views/Home/test.cshtml*.
* Wstępnie skompilowane widoki: w przypadku ASP.NET Core 2,0 i nowszych wyszukiwanie wstępnie skompilowanych widoków nie uwzględnia wielkości liter we wszystkich systemach operacyjnych. Zachowanie jest takie samo jak zachowanie dostawcy plików fizycznych w systemie Windows. Jeśli dwa skompilowane widoki różnią się tylko wielkością liter, wynik wyszukiwania jest niejednoznaczny.

Deweloperzy są zachęcani do dopasowania wielkości liter w nazwach plików i katalogów do wielkości liter:

* Nazwy obszaru, kontrolera i akcji.
* Razor Page.

Przypadek dopasowania gwarantuje, że wdrożenia znajdą swoje widoki niezależnie od systemu plików.

## <a name="additional-resources"></a>Dodatkowe zasoby

[Wprowadzenie do programowania w sieci Web ASP.NET za pomocą Razor Składnia](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) zawiera wiele przykładów programowania z Razor składnią.
