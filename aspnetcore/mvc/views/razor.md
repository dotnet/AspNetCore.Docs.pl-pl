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
# <a name="razor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="8c0f1-103">Odwołanie do składni maszynki do golenia dla ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8c0f1-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="8c0f1-104">Rick [Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)i Dan [Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="8c0f1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="8c0f1-105">Razor to składnia znaczników do osadzania kodu opartego na serwerze na stronach sieci Web.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="8c0f1-106">Składnia Razor składa się z znaczników Razor, C#i HTML.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="8c0f1-107">Pliki zawierające Razor zazwyczaj mają rozszerzenie pliku *.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="8c0f1-108">Brzytwa znajduje się również w [plikach komponentów Razor](xref:blazor/components) (*.brzytwa*).</span><span class="sxs-lookup"><span data-stu-id="8c0f1-108">Razor is also found in [Razor components](xref:blazor/components) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="8c0f1-109">Renderowanie HTML</span><span class="sxs-lookup"><span data-stu-id="8c0f1-109">Rendering HTML</span></span>

<span data-ttu-id="8c0f1-110">Domyślnym językiem składni Razor jest HTML.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-110">The default Razor language is HTML.</span></span> <span data-ttu-id="8c0f1-111">Renderowanie kodu HTML ze tagów składni Razor nie różni się od renderowania kodu HTML z pliku HTML.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="8c0f1-112">Znaczniki HTML w plikach *.cshtml* Razor są renderowane przez serwer bez zmian.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="8c0f1-113">Składnia Razor</span><span class="sxs-lookup"><span data-stu-id="8c0f1-113">Razor syntax</span></span>

<span data-ttu-id="8c0f1-114">Razor obsługuje język C# `@` i używa symbolu do przejścia z HTML do języka C#.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="8c0f1-115">Razor ocenia wyrażenia C# i renderuje je w danych wyjściowych HTML.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="8c0f1-116">Gdy `@` po symbolu następuje [słowo kluczowe razor reserved](#razor-reserved-keywords), przechodzi do znaczników specyficznych dla razora.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="8c0f1-117">W przeciwnym razie przechodzi do zwykłego języka C#.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="8c0f1-118">Aby uniknąć `@` symbolu znaczników Razor, `@` użyj drugiego symbolu:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="8c0f1-119">Kod jest renderowany w formacie `@` HTML z jednym symbolem:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="8c0f1-120">Atrybuty HTML i zawartość zawierająca adresy e-mail nie traktują symbolu `@` jako znaku przejścia.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="8c0f1-121">Adresy e-mail w poniższym przykładzie są nietknięte przez analizowanie razor:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="8c0f1-122">Niejawne wyrażenia Maszynki do golenia</span><span class="sxs-lookup"><span data-stu-id="8c0f1-122">Implicit Razor expressions</span></span>

<span data-ttu-id="8c0f1-123">Niejawne wyrażenia Razor zaczynają `@` się od kodu C#:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="8c0f1-124">Z wyjątkiem słowa kluczowego C#, `await` wyrażenia niejawne nie mogą zawierać spacji.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="8c0f1-125">Jeśli instrukcja C# ma wyraźne zakończenie, spacje mogą być intermingled:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="8c0f1-126">Wyrażenia niejawne **nie mogą** zawierać generycznych znaków`<>`C#, ponieważ znaki wewnątrz nawiasów ( ) są interpretowane jako znacznik HTML.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="8c0f1-127">Następujący kod jest **nieprawidłowy:**</span><span class="sxs-lookup"><span data-stu-id="8c0f1-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="8c0f1-128">Poprzedni kod generuje błąd kompilatora podobny do jednego z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="8c0f1-129">Element "int" nie został zamknięty.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="8c0f1-130">Wszystkie elementy muszą być samozamykające się lub mają pasujący tag końcowy.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="8c0f1-131">Nie można przekonwertować grupy metod "GenericMethod" na typ "object" nieobejmowany.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="8c0f1-132">Czy zamierzał pan powołać się na tę metodę?".</span><span class="sxs-lookup"><span data-stu-id="8c0f1-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="8c0f1-133">Wywołania metod ogólnych muszą być zawinięte w [jawne wyrażenie Razor](#explicit-razor-expressions) lub [blok kodu Razor](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="8c0f1-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="8c0f1-134">Jawne wyrażenia brzytwy</span><span class="sxs-lookup"><span data-stu-id="8c0f1-134">Explicit Razor expressions</span></span>

<span data-ttu-id="8c0f1-135">Jawne wyrażenia Razor `@` składają się z symbolu z zrównoważonym nawiasem.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="8c0f1-136">Aby renderować czas w zeszłym tygodniu, używana jest następująca znaczniki Razor:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="8c0f1-137">Każda zawartość `@()` w nawiasie jest oceniana i renderowana na dane wyjściowe.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="8c0f1-138">Wyrażenia niejawne, opisane w poprzedniej sekcji, zazwyczaj nie mogą zawierać spacji.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="8c0f1-139">W poniższym kodzie jeden tydzień nie jest odejmowany od bieżącego czasu:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="8c0f1-140">Kod renderuje następujący kod HTML:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="8c0f1-141">Wyrażenia jawne mogą służyć do łączenia tekstu z wynikiem wyrażenia:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="8c0f1-142">Bez wyrażenia jawnego `<p>Age@joe.Age</p>` jest traktowany jako `<p>Age@joe.Age</p>` adres e-mail i jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="8c0f1-143">Po zapisaniu jako wyrażenie `<p>Age33</p>` jawne, jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="8c0f1-144">Wyrażenia jawne mogą być używane do renderowania danych wyjściowych z metod ogólnych w plikach *.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="8c0f1-145">Poniższy znacznik pokazuje, jak poprawić błąd pokazany wcześniej spowodowane przez nawiasy języka ogólnego języka C#.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="8c0f1-146">Kod jest napisany jako wyrażenie jawne:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="8c0f1-147">Kodowanie wyrażeń</span><span class="sxs-lookup"><span data-stu-id="8c0f1-147">Expression encoding</span></span>

<span data-ttu-id="8c0f1-148">Wyrażenia C#, które oceniają ciąg są zakodowane w formacie HTML.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="8c0f1-149">Wyrażenia C#, które `IHtmlContent` oceniają, są `IHtmlContent.WriteTo`renderowane bezpośrednio za pośrednictwem .</span><span class="sxs-lookup"><span data-stu-id="8c0f1-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="8c0f1-150">Wyrażenia C#, które nie `IHtmlContent` mają oceny są konwertowane na ciąg przez `ToString` i zakodowane przed ich renderowaniem.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="8c0f1-151">Kod renderuje następujący kod HTML:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-151">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="8c0f1-152">Kod HTML jest wyświetlany w przeglądarce jako:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-152">The HTML is shown in the browser as:</span></span>

```html
<span>Hello World</span>
```

<span data-ttu-id="8c0f1-153">`HtmlHelper.Raw`dane wyjściowe nie są zakodowane, ale renderowane jako znaczniki HTML.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="8c0f1-154">Używanie `HtmlHelper.Raw` danych wejściowych użytkownika bez użycia bezpieczeństwa jest zagrożeniem bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="8c0f1-155">Dane wejściowe użytkownika mogą zawierać złośliwy kod JavaScript lub inne exploity.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-155">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="8c0f1-156">Odkażanie danych wejściowych użytkownika jest trudne.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-156">Sanitizing user input is difficult.</span></span> <span data-ttu-id="8c0f1-157">Unikaj `HtmlHelper.Raw` używania danych wejściowych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-157">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="8c0f1-158">Kod renderuje następujący kod HTML:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-158">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="8c0f1-159">Bloki kodów brzytwy</span><span class="sxs-lookup"><span data-stu-id="8c0f1-159">Razor code blocks</span></span>

<span data-ttu-id="8c0f1-160">Bloki kodu brzytwy zaczynają się `@` od i są zamknięte przez `{}`.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-160">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="8c0f1-161">W przeciwieństwie do wyrażeń kod C# wewnątrz bloków kodu nie jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-161">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="8c0f1-162">Bloki kodu i wyrażenia w widoku współużytkują ten sam zakres i są zdefiniowane w kolejności:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-162">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="8c0f1-163">Kod renderuje następujący kod HTML:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-163">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8c0f1-164">W blokach kodu zadeklaruj [funkcje lokalne](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) za pomocą znaczników, które mają służyć jako metody tworzenia szablonów:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

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

<span data-ttu-id="8c0f1-165">Kod renderuje następujący kod HTML:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-165">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="8c0f1-166">Przejścia niejawne</span><span class="sxs-lookup"><span data-stu-id="8c0f1-166">Implicit transitions</span></span>

<span data-ttu-id="8c0f1-167">Domyślnym językiem w bloku kodu jest C#, ale strona Razor może przejść z powrotem do html:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="8c0f1-168">Jawne przejście rozdzielane</span><span class="sxs-lookup"><span data-stu-id="8c0f1-168">Explicit delimited transition</span></span>

<span data-ttu-id="8c0f1-169">Aby zdefiniować podsekcję bloku kodu, który powinien renderować kod HTML, otoczyć znaki renderowania tagiem Razor: `<text>`</span><span class="sxs-lookup"><span data-stu-id="8c0f1-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="8c0f1-170">Ta ta ta możliwość renderowania kodu HTML, który nie jest otoczony tagiem HTML.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="8c0f1-171">Bez tagu HTML lub Razor występuje błąd środowiska uruchomieniowego Razor.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="8c0f1-172">Tag `<text>` jest przydatny do kontrolowania odstępów podczas renderowania zawartości:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-172">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="8c0f1-173">Renderowana jest `<text>` tylko zawartość między tagiem.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-173">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="8c0f1-174">W danych wyjściowych `<text>` HTML nie ma żadnych odstępów przed lub po wyświetlenie znacznika.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="8c0f1-175">Jawne przejście wiersza</span><span class="sxs-lookup"><span data-stu-id="8c0f1-175">Explicit line transition</span></span>

<span data-ttu-id="8c0f1-176">Aby wypozorować resztę całego wiersza jako `@:` HTML wewnątrz bloku kodu, użyj składni:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-176">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="8c0f1-177">Bez `@:` w kodzie razor błąd środowiska uruchomieniowego jest generowany.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-177">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="8c0f1-178">Dodatkowe `@` znaki w pliku Razor może powodować błędy kompilatora w instrukcjach później w bloku.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-178">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="8c0f1-179">Te błędy kompilatora może być trudne do zrozumienia, ponieważ rzeczywisty błąd występuje przed zgłoszonym błędem.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-179">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="8c0f1-180">Ten błąd jest często po połączeniu wielu wyrażeń niejawnych/jawnych w jednym bloku kodu.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-180">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="8c0f1-181">Struktury sterowania</span><span class="sxs-lookup"><span data-stu-id="8c0f1-181">Control structures</span></span>

<span data-ttu-id="8c0f1-182">Struktury sterowania są rozszerzeniem bloków kodu.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-182">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="8c0f1-183">Wszystkie aspekty bloków kodu (przejście do znaczników, wbudowany C#) dotyczą również następujących struktur:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-183">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="8c0f1-184">Warunki, \@jeśli, w przeciwnym razie, w inny sposób, i \@przełączyć</span><span class="sxs-lookup"><span data-stu-id="8c0f1-184">Conditionals \@if, else if, else, and \@switch</span></span>

<span data-ttu-id="8c0f1-185">`@if`formantów po uruchomieniu kodu:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-185">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="8c0f1-186">`else`i `else if` nie wymagają `@` symbolu:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-186">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="8c0f1-187">Następujące znaczniki pokazuje, jak używać instrukcji switch:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-187">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="8c0f1-188">Zapętlanie, \@ \@foreach, \@ \@while, i zrobić podczas</span><span class="sxs-lookup"><span data-stu-id="8c0f1-188">Looping \@for, \@foreach, \@while, and \@do while</span></span>

<span data-ttu-id="8c0f1-189">Szablon HTML może być renderowany za pomocą instrukcji kontroli pętli.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-189">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="8c0f1-190">Aby wyświetlić listę osób:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-190">To render a list of people:</span></span>

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

<span data-ttu-id="8c0f1-191">Obsługiwane są następujące instrukcje pętli:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-191">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="8c0f1-192">Związek \@za pomocą</span><span class="sxs-lookup"><span data-stu-id="8c0f1-192">Compound \@using</span></span>

<span data-ttu-id="8c0f1-193">W języku C#instrukcja `using` jest używana w celu zapewnienia, że obiekt jest usuwany.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-193">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="8c0f1-194">W Razor ten sam mechanizm jest używany do tworzenia pomocników HTML, które zawierają dodatkową zawartość.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-194">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="8c0f1-195">W poniższym kodzie Pomocnicy `<form>` HTML `@using` renderuje tag z instrukcją:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-195">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a><span data-ttu-id="8c0f1-196">\@spróbuj, złapać, wreszcie</span><span class="sxs-lookup"><span data-stu-id="8c0f1-196">\@try, catch, finally</span></span>

<span data-ttu-id="8c0f1-197">Obsługa wyjątków jest podobna do języka C#:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a><span data-ttu-id="8c0f1-198">\@lock</span><span class="sxs-lookup"><span data-stu-id="8c0f1-198">\@lock</span></span>

<span data-ttu-id="8c0f1-199">Razor ma możliwość ochrony krytycznych sekcji za pomocą instrukcji blokady:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-199">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="8c0f1-200">Komentarze</span><span class="sxs-lookup"><span data-stu-id="8c0f1-200">Comments</span></span>

<span data-ttu-id="8c0f1-201">Razor obsługuje komentarze języka C# i HTML:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-201">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="8c0f1-202">Kod renderuje następujący kod HTML:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-202">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="8c0f1-203">Komentarze razor są usuwane przez serwer przed renderowaniem strony internetowej.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-203">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="8c0f1-204">Razor używa `@*  *@` do rozgraniczenia komentarzy.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-204">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="8c0f1-205">Następujący kod jest komentowany, więc serwer nie renderuje żadnych znaczników:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-205">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="8c0f1-206">Dyrektyw</span><span class="sxs-lookup"><span data-stu-id="8c0f1-206">Directives</span></span>

<span data-ttu-id="8c0f1-207">Dyrektywy razor są reprezentowane przez niejawne wyrażenia `@` z zastrzeżonych słów kluczowych po symbolu.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-207">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="8c0f1-208">Dyrektywa zazwyczaj zmienia sposób, w jaki widok jest analizowany lub włącza różne funkcje.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-208">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="8c0f1-209">Zrozumienie, jak Razor generuje kod dla widoku ułatwia zrozumienie, jak działają dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-209">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="8c0f1-210">Kod generuje klasę podobną do następującej:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-210">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="8c0f1-211">W dalszej części tego artykułu w sekcji [Sprawdź razor C# klasy generowane dla widoku](#inspect-the-razor-c-class-generated-for-a-view) wyjaśnia, jak wyświetlić tę wygenerowaną klasę.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-211">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### <a name="attribute"></a><span data-ttu-id="8c0f1-212">\@Atrybut</span><span class="sxs-lookup"><span data-stu-id="8c0f1-212">\@attribute</span></span>

<span data-ttu-id="8c0f1-213">Dyrektywa `@attribute` dodaje dany atrybut do klasy wygenerowanej strony lub widoku.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-213">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="8c0f1-214">W poniższym `[Authorize]` przykładzie dodaje atrybut:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-214">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a><span data-ttu-id="8c0f1-215">\@Kod</span><span class="sxs-lookup"><span data-stu-id="8c0f1-215">\@code</span></span>

<span data-ttu-id="8c0f1-216">*Ten scenariusz dotyczy tylko komponentów Razor (.brzytwa).*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-216">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="8c0f1-217">Blok `@code` umożliwia [składnik Razor,](xref:blazor/components) aby dodać elementy członkowskie Języka C# (pola, właściwości i metody) do składnika:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-217">The `@code` block enables a [Razor component](xref:blazor/components) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="8c0f1-218">W przypadku komponentów Razor `@code` [`@functions`](#functions) jest aliasem i zalecanym przez . `@functions`</span><span class="sxs-lookup"><span data-stu-id="8c0f1-218">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="8c0f1-219">Dopuszczalne `@code` jest więcej niż jeden blok.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-219">More than one `@code` block is permissible.</span></span>

::: moniker-end

### <a name="functions"></a><span data-ttu-id="8c0f1-220">\@Funkcje</span><span class="sxs-lookup"><span data-stu-id="8c0f1-220">\@functions</span></span>

<span data-ttu-id="8c0f1-221">Dyrektywa `@functions` umożliwia dodawanie elementów członkowskich języka C# (pól, właściwości i metod) do wygenerowanej klasy:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-221">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8c0f1-222">W [komponentach Razor](xref:blazor/components)użyj, `@code` `@functions` aby dodać elementy członkowskie języka C#.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-222">In [Razor components](xref:blazor/components), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="8c0f1-223">Przykład:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-223">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="8c0f1-224">Kod generuje następujące znaczniki HTML:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-224">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="8c0f1-225">Następujący kod jest wygenerowaną klasą Razor C#:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-225">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8c0f1-226">`@functions`metody służą jako metody tworzenia szablonów, gdy mają znaczniki:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-226">`@functions` methods serve as templating methods when they have markup:</span></span>

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

<span data-ttu-id="8c0f1-227">Kod renderuje następujący kod HTML:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-227">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a><span data-ttu-id="8c0f1-228">\@Implementuje</span><span class="sxs-lookup"><span data-stu-id="8c0f1-228">\@implements</span></span>

<span data-ttu-id="8c0f1-229">Dyrektywa `@implements` implementuje interfejs dla wygenerowanej klasy.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-229">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="8c0f1-230">Poniższy przykład <xref:System.IDisposable?displayProperty=fullName> implementuje, <xref:System.IDisposable.Dispose*> aby można było wywołać metodę:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-230">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

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

### <a name="inherits"></a><span data-ttu-id="8c0f1-231">\@Dziedziczy</span><span class="sxs-lookup"><span data-stu-id="8c0f1-231">\@inherits</span></span>

<span data-ttu-id="8c0f1-232">Dyrektywa `@inherits` zapewnia pełną kontrolę nad klasą dziedziczy widok:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-232">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="8c0f1-233">Poniższy kod to niestandardowy typ strony Razor:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-233">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="8c0f1-234">Jest `CustomText` wyświetlany w widoku:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-234">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="8c0f1-235">Kod renderuje następujący kod HTML:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-235">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="8c0f1-236">`@model`i `@inherits` mogą być używane w tym samym widoku.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-236">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="8c0f1-237">`@inherits`może znajdować się w *pliku _ViewImports.cshtml* importowanym przez widok:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-237">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="8c0f1-238">Poniższy kod jest przykładem widoku silnie typizowanego:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-238">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="8c0f1-239">Jeślirick@contoso.com" " jest przekazywana w modelu, widok generuje następujące znaczniki HTML:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-239">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a><span data-ttu-id="8c0f1-240">\@Wstrzyknąć</span><span class="sxs-lookup"><span data-stu-id="8c0f1-240">\@inject</span></span>

<span data-ttu-id="8c0f1-241">Dyrektywa `@inject` umożliwia Razor Page wstrzyknąć usługi z [kontenera usługi](xref:fundamentals/dependency-injection) do widoku.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-241">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="8c0f1-242">Aby uzyskać więcej informacji, zobacz [Iniekcja zależności do widoków](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8c0f1-242">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a><span data-ttu-id="8c0f1-243">\@Układ</span><span class="sxs-lookup"><span data-stu-id="8c0f1-243">\@layout</span></span>

<span data-ttu-id="8c0f1-244">*Ten scenariusz dotyczy tylko komponentów Razor (.brzytwa).*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-244">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="8c0f1-245">Dyrektywa `@layout` określa układ dla komponentu Razor.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-245">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="8c0f1-246">Składniki układu są używane w celu uniknięcia duplikacji kodu i niespójności.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-246">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="8c0f1-247">Aby uzyskać więcej informacji, zobacz <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-247">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### <a name="model"></a><span data-ttu-id="8c0f1-248">\@Modelu</span><span class="sxs-lookup"><span data-stu-id="8c0f1-248">\@model</span></span>

<span data-ttu-id="8c0f1-249">*Ten scenariusz dotyczy tylko widoków MVC i stron Razor (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-249">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="8c0f1-250">Dyrektywa `@model` określa typ modelu przekazywane do widoku lub strony:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-250">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="8c0f1-251">W ASP.NET Core MVC lub Razor Pages aplikacji utworzonej z poszczególnych kont użytkowników, *Widoki/Konto/Login.cshtml* zawiera następującą deklarację modelu:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-251">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="8c0f1-252">Wygenerowana klasa dziedziczy po: `RazorPage<dynamic>`</span><span class="sxs-lookup"><span data-stu-id="8c0f1-252">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="8c0f1-253">Razor udostępnia `Model` właściwość dostępu do modelu przekazywane do widoku:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-253">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="8c0f1-254">Dyrektywa `@model` określa typ `Model` właściwości.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-254">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="8c0f1-255">Dyrektywa określa `T` w `RazorPage<T>` tym wygenerowanej klasy, że widok pochodzi z.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-255">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="8c0f1-256">Jeśli `@model` dyrektywa nie jest określona, `Model` właściwość jest typu. `dynamic`</span><span class="sxs-lookup"><span data-stu-id="8c0f1-256">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="8c0f1-257">Aby uzyskać więcej informacji, zobacz [Silnie wpisane modele i @model słowo kluczowe](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="8c0f1-257">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### <a name="namespace"></a><span data-ttu-id="8c0f1-258">\@namespace</span><span class="sxs-lookup"><span data-stu-id="8c0f1-258">\@namespace</span></span>

<span data-ttu-id="8c0f1-259">Dyrektywa: `@namespace`</span><span class="sxs-lookup"><span data-stu-id="8c0f1-259">The `@namespace` directive:</span></span>

* <span data-ttu-id="8c0f1-260">Ustawia obszar nazw klasy wygenerowanej strony Razor, widoku MVC lub komponentu Razor.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-260">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="8c0f1-261">Ustawia główne obszary nazw stron, widoków lub klas składników z najbliższego pliku importu w drzewie katalogów, *_ViewImports.cshtml* (widoki lub strony) lub *_Imports.razor* (składniki Razor).</span><span class="sxs-lookup"><span data-stu-id="8c0f1-261">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="8c0f1-262">W przykładzie Strony razor pokazane w poniższej tabeli:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-262">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="8c0f1-263">Każda strona importuje *pages/_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-263">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="8c0f1-264">*Strony/_ViewImports.cshtml* zawiera `@namespace Hello.World`.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-264">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="8c0f1-265">Każda strona `Hello.World` ma jako katalog główny jej obszaru nazw.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-265">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="8c0f1-266">Strona</span><span class="sxs-lookup"><span data-stu-id="8c0f1-266">Page</span></span>                                        | <span data-ttu-id="8c0f1-267">Przestrzeń nazw</span><span class="sxs-lookup"><span data-stu-id="8c0f1-267">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="8c0f1-268">*Strony/indeks.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-268">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="8c0f1-269">*Strony/Więcej Stron/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-269">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="8c0f1-270">*Strony/Więcej Stron/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="8c0f1-271">Poprzednie relacje dotyczą importu plików używanych z widokami MVC i komponentami Razor.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-271">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="8c0f1-272">Jeśli wiele plików `@namespace` importu ma dyrektywę, plik znajdujący się najbliżej strony, widoku lub składnika w drzewie katalogów jest używany do ustawiania głównego obszaru nazw.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-272">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="8c0f1-273">Jeśli folder *EvenMorePages* w poprzednim przykładzie zawiera `@namespace Another.Planet` plik importu (lub plik *Pages/MorePages/EvenMorePages/Page.cshtml),* `@namespace Another.Planet`wynik jest wyświetlany w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-273">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="8c0f1-274">Strona</span><span class="sxs-lookup"><span data-stu-id="8c0f1-274">Page</span></span>                                        | <span data-ttu-id="8c0f1-275">Przestrzeń nazw</span><span class="sxs-lookup"><span data-stu-id="8c0f1-275">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="8c0f1-276">*Strony/indeks.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-276">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="8c0f1-277">*Strony/Więcej Stron/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-277">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="8c0f1-278">*Strony/Więcej Stron/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### <a name="page"></a><span data-ttu-id="8c0f1-279">\@Strona</span><span class="sxs-lookup"><span data-stu-id="8c0f1-279">\@page</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8c0f1-280">Dyrektywa `@page` ma różne skutki w zależności od typu pliku, w którym się pojawia.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-280">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="8c0f1-281">Dyrektywa:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-281">The directive:</span></span>

* <span data-ttu-id="8c0f1-282">W pliku *cshtml* wskazuje, że plik jest Stroną Razor.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-282">In in a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="8c0f1-283">Aby uzyskać więcej informacji, <xref:razor-pages/index>zobacz [Trasy niestandardowe](xref:razor-pages/index#custom-routes) i .</span><span class="sxs-lookup"><span data-stu-id="8c0f1-283">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="8c0f1-284">Określa, że składnik Razor powinien obsługiwać żądania bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-284">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="8c0f1-285">Aby uzyskać więcej informacji, zobacz <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-285">For more information, see <xref:blazor/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8c0f1-286">Dyrektywa `@page` w pierwszym wierszu pliku *cshtml* wskazuje, że plik jest stroną Razor.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-286">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="8c0f1-287">Aby uzyskać więcej informacji, zobacz <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-287">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### <a name="section"></a><span data-ttu-id="8c0f1-288">\@Sekcji</span><span class="sxs-lookup"><span data-stu-id="8c0f1-288">\@section</span></span>

<span data-ttu-id="8c0f1-289">*Ten scenariusz dotyczy tylko widoków MVC i stron Razor (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-289">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="8c0f1-290">Dyrektywa `@section` jest używana w połączeniu z [układami MVC i Razor Pages,](xref:mvc/views/layout) aby umożliwić widoki lub strony do renderowania zawartości w różnych częściach strony HTML.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-290">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="8c0f1-291">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-291">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="using"></a><span data-ttu-id="8c0f1-292">\@using</span><span class="sxs-lookup"><span data-stu-id="8c0f1-292">\@using</span></span>

<span data-ttu-id="8c0f1-293">Dyrektywa `@using` dodaje C# `using` dyrektywy do wygenerowanego widoku:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-293">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8c0f1-294">W [komponentach Razor](xref:blazor/components)kontroluje również, `@using` które komponenty są w zakresie.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-294">In [Razor components](xref:blazor/components), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="8c0f1-295">Atrybuty dyrektywy</span><span class="sxs-lookup"><span data-stu-id="8c0f1-295">Directive attributes</span></span>

### <a name="attributes"></a><span data-ttu-id="8c0f1-296">\@Atrybuty</span><span class="sxs-lookup"><span data-stu-id="8c0f1-296">\@attributes</span></span>

<span data-ttu-id="8c0f1-297">*Ten scenariusz dotyczy tylko komponentów Razor (.brzytwa).*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-297">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="8c0f1-298">`@attributes`umożliwia składnikowi renderowanie nie zadeklarowanych atrybutów.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-298">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="8c0f1-299">Aby uzyskać więcej informacji, zobacz <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-299">For more information, see <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span></span>

### <a name="bind"></a><span data-ttu-id="8c0f1-300">\@Powiązać</span><span class="sxs-lookup"><span data-stu-id="8c0f1-300">\@bind</span></span>

<span data-ttu-id="8c0f1-301">*Ten scenariusz dotyczy tylko komponentów Razor (.brzytwa).*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-301">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="8c0f1-302">Powiązanie danych w składnikach `@bind` odbywa się za pomocą atrybutu.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-302">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="8c0f1-303">Aby uzyskać więcej informacji, zobacz <xref:blazor/data-binding>.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-303">For more information, see <xref:blazor/data-binding>.</span></span>

### <a name="onevent"></a><span data-ttu-id="8c0f1-304">\@na{EVENT}</span><span class="sxs-lookup"><span data-stu-id="8c0f1-304">\@on{EVENT}</span></span>

<span data-ttu-id="8c0f1-305">*Ten scenariusz dotyczy tylko komponentów Razor (.brzytwa).*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-305">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="8c0f1-306">Maszynka do golenia zapewnia funkcje obsługi zdarzeń dla komponentów.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-306">Razor provides event handling features for components.</span></span> <span data-ttu-id="8c0f1-307">Aby uzyskać więcej informacji, zobacz <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-307">For more information, see <xref:blazor/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a><span data-ttu-id="8c0f1-308">\@na{EVENT}:preventDefault</span><span class="sxs-lookup"><span data-stu-id="8c0f1-308">\@on{EVENT}:preventDefault</span></span>

<span data-ttu-id="8c0f1-309">*Ten scenariusz dotyczy tylko komponentów Razor (.brzytwa).*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-309">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="8c0f1-310">Zapobiega domyślnej akcji zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-310">Prevents the default action for the event.</span></span>

### <a name="oneventstoppropagation"></a><span data-ttu-id="8c0f1-311">\@na{EVENT}:stopPropagacja</span><span class="sxs-lookup"><span data-stu-id="8c0f1-311">\@on{EVENT}:stopPropagation</span></span>

<span data-ttu-id="8c0f1-312">*Ten scenariusz dotyczy tylko komponentów Razor (.brzytwa).*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-312">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="8c0f1-313">Zatrzymuje propagację zdarzeń dla zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-313">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a><span data-ttu-id="8c0f1-314">\@key</span><span class="sxs-lookup"><span data-stu-id="8c0f1-314">\@key</span></span>

<span data-ttu-id="8c0f1-315">*Ten scenariusz dotyczy tylko komponentów Razor (.brzytwa).*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-315">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="8c0f1-316">Atrybut `@key` dyrektywy powoduje, że algorytm diffing składników, aby zagwarantować zachowanie elementów lub składników na podstawie wartości klucza.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-316">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="8c0f1-317">Aby uzyskać więcej informacji, zobacz <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-317">For more information, see <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### <a name="ref"></a><span data-ttu-id="8c0f1-318">\@ref</span><span class="sxs-lookup"><span data-stu-id="8c0f1-318">\@ref</span></span>

<span data-ttu-id="8c0f1-319">*Ten scenariusz dotyczy tylko komponentów Razor (.brzytwa).*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-319">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="8c0f1-320">Odwołania do`@ref`komponentu ( ) umożliwiają odwoływanie się do wystąpienia składnika, dzięki czemu można wydawać polecenia do tego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-320">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="8c0f1-321">Aby uzyskać więcej informacji, zobacz <xref:blazor/components#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-321">For more information, see <xref:blazor/components#capture-references-to-components>.</span></span>

### <a name="typeparam"></a><span data-ttu-id="8c0f1-322">\@typeparam</span><span class="sxs-lookup"><span data-stu-id="8c0f1-322">\@typeparam</span></span>

<span data-ttu-id="8c0f1-323">*Ten scenariusz dotyczy tylko komponentów Razor (.brzytwa).*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-323">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="8c0f1-324">Dyrektywa `@typeparam` deklaruje parametr typu ogólnego dla wygenerowanej klasy składnika.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-324">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="8c0f1-325">Aby uzyskać więcej informacji, zobacz <xref:blazor/templated-components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-325">For more information, see <xref:blazor/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-razor-delegates"></a><span data-ttu-id="8c0f1-326">Delegowani razor szablonów</span><span class="sxs-lookup"><span data-stu-id="8c0f1-326">Templated Razor delegates</span></span>

<span data-ttu-id="8c0f1-327">Szablony maszynki do golenia umożliwiają zdefiniowanie fragmentu kodu interfejsu użytkownika w następującym formacie:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-327">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="8c0f1-328">Poniższy przykład ilustruje sposób określania szablonu <xref:System.Func%602>Razor delegata jako .</span><span class="sxs-lookup"><span data-stu-id="8c0f1-328">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="8c0f1-329">[Typ dynamiczny](/dotnet/csharp/programming-guide/types/using-type-dynamic) jest określony dla parametru metody, która jest hermetyzowana przez delegata.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-329">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="8c0f1-330">[Typ obiektu](/dotnet/csharp/language-reference/keywords/object) jest określony jako zwracana wartość pełnomocnika.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-330">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="8c0f1-331">Szablon jest używany <xref:System.Collections.Generic.List%601> z, `Pet` że `Name` ma właściwość.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-331">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="8c0f1-332">Szablon jest renderowany `pets` z `foreach` dostarczonym przez instrukcję:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-332">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="8c0f1-333">Renderowane wyjście:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-333">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="8c0f1-334">Można również podać wbudowany szablon Razor jako argument do metody.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-334">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="8c0f1-335">W poniższym przykładzie `Repeat` metoda odbiera szablon Razor.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-335">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="8c0f1-336">Metoda używa szablonu do tworzenia zawartości HTML z powtórzeniami elementów dostarczonych z listy:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-336">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="8c0f1-337">Korzystając z listy zwierząt domowych `Repeat` z poprzedniego przykładu, metoda jest wywoływana z:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-337">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="8c0f1-338"><xref:System.Collections.Generic.List%601>z `Pet`.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-338"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="8c0f1-339">Liczba powtórzeń każdego zwierzaka.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-339">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="8c0f1-340">Szablon wbudowany do użycia dla elementów listy listy nieurządzonych.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-340">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="8c0f1-341">Renderowane wyjście:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-341">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="8c0f1-342">Pomocnicy tagów</span><span class="sxs-lookup"><span data-stu-id="8c0f1-342">Tag Helpers</span></span>

<span data-ttu-id="8c0f1-343">*Ten scenariusz dotyczy tylko widoków MVC i stron Razor (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-343">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="8c0f1-344">Istnieją trzy dyrektywy, które odnoszą się do [Pomocników Tagów.](xref:mvc/views/tag-helpers/intro)</span><span class="sxs-lookup"><span data-stu-id="8c0f1-344">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="8c0f1-345">Dyrektywy</span><span class="sxs-lookup"><span data-stu-id="8c0f1-345">Directive</span></span> | <span data-ttu-id="8c0f1-346">Funkcja</span><span class="sxs-lookup"><span data-stu-id="8c0f1-346">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="8c0f1-347">Udostępnia pomocnikom znaczników widok.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-347">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="8c0f1-348">Usuwa pomocników tagów wcześniej dodanych z widoku.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-348">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="8c0f1-349">Określa prefiks znacznika, aby włączyć obsługę Pomocnika znaczników i uczynić użycie pomocnika znaczników jawnym.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-349">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a><span data-ttu-id="8c0f1-350">Słowa kluczowe zastrzeżone razor</span><span class="sxs-lookup"><span data-stu-id="8c0f1-350">Razor reserved keywords</span></span>

### <a name="razor-keywords"></a><span data-ttu-id="8c0f1-351">Razor słowa kluczowe</span><span class="sxs-lookup"><span data-stu-id="8c0f1-351">Razor keywords</span></span>

* <span data-ttu-id="8c0f1-352">(wymaga ASP.NET Core 2.1 lub nowszym)</span><span class="sxs-lookup"><span data-stu-id="8c0f1-352">page (Requires ASP.NET Core 2.1 or later)</span></span>
* <span data-ttu-id="8c0f1-353">namespace</span><span class="sxs-lookup"><span data-stu-id="8c0f1-353">namespace</span></span>
* <span data-ttu-id="8c0f1-354"> — funkcje</span><span class="sxs-lookup"><span data-stu-id="8c0f1-354">functions</span></span>
* <span data-ttu-id="8c0f1-355">Dziedziczy</span><span class="sxs-lookup"><span data-stu-id="8c0f1-355">inherits</span></span>
* <span data-ttu-id="8c0f1-356">model</span><span class="sxs-lookup"><span data-stu-id="8c0f1-356">model</span></span>
* <span data-ttu-id="8c0f1-357">section</span><span class="sxs-lookup"><span data-stu-id="8c0f1-357">section</span></span>
* <span data-ttu-id="8c0f1-358">pomocnik (obecnie nie jest obsługiwany przez ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="8c0f1-358">helper (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="8c0f1-359">Razor słowa kluczowe `@(Razor Keyword)` są wysunął z (na przykład, `@(functions)`).</span><span class="sxs-lookup"><span data-stu-id="8c0f1-359">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="8c0f1-360">C# Razor słowa kluczowe</span><span class="sxs-lookup"><span data-stu-id="8c0f1-360">C# Razor keywords</span></span>

* <span data-ttu-id="8c0f1-361">case</span><span class="sxs-lookup"><span data-stu-id="8c0f1-361">case</span></span>
* <span data-ttu-id="8c0f1-362">do</span><span class="sxs-lookup"><span data-stu-id="8c0f1-362">do</span></span>
* <span data-ttu-id="8c0f1-363">default</span><span class="sxs-lookup"><span data-stu-id="8c0f1-363">default</span></span>
* <span data-ttu-id="8c0f1-364">for</span><span class="sxs-lookup"><span data-stu-id="8c0f1-364">for</span></span>
* <span data-ttu-id="8c0f1-365">foreach</span><span class="sxs-lookup"><span data-stu-id="8c0f1-365">foreach</span></span>
* <span data-ttu-id="8c0f1-366">if</span><span class="sxs-lookup"><span data-stu-id="8c0f1-366">if</span></span>
* <span data-ttu-id="8c0f1-367">else</span><span class="sxs-lookup"><span data-stu-id="8c0f1-367">else</span></span>
* <span data-ttu-id="8c0f1-368">lock</span><span class="sxs-lookup"><span data-stu-id="8c0f1-368">lock</span></span>
* <span data-ttu-id="8c0f1-369">switch</span><span class="sxs-lookup"><span data-stu-id="8c0f1-369">switch</span></span>
* <span data-ttu-id="8c0f1-370">try</span><span class="sxs-lookup"><span data-stu-id="8c0f1-370">try</span></span>
* <span data-ttu-id="8c0f1-371">Złapać</span><span class="sxs-lookup"><span data-stu-id="8c0f1-371">catch</span></span>
* <span data-ttu-id="8c0f1-372">finally</span><span class="sxs-lookup"><span data-stu-id="8c0f1-372">finally</span></span>
* <span data-ttu-id="8c0f1-373">using</span><span class="sxs-lookup"><span data-stu-id="8c0f1-373">using</span></span>
* <span data-ttu-id="8c0f1-374">while</span><span class="sxs-lookup"><span data-stu-id="8c0f1-374">while</span></span>

<span data-ttu-id="8c0f1-375">C# Razor słowa kluczowe muszą `@(@C# Razor Keyword)` być dwukrotnie `@(@case)`uciekł z (na przykład, ).</span><span class="sxs-lookup"><span data-stu-id="8c0f1-375">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="8c0f1-376">Pierwszy `@` ucieka parser Razor.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-376">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="8c0f1-377">Drugi `@` wyujmuje parser C#.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-377">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="8c0f1-378">Zarezerwowane słowa kluczowe nie używane przez Razor</span><span class="sxs-lookup"><span data-stu-id="8c0f1-378">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="8c0f1-379">class</span><span class="sxs-lookup"><span data-stu-id="8c0f1-379">class</span></span>

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="8c0f1-380">Sprawdź klasę Razor C# wygenerowaną pod kątem widoku</span><span class="sxs-lookup"><span data-stu-id="8c0f1-380">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="8c0f1-381">W pliku .NET Core SDK 2.1 lub nowszym [zestaw Razor SDK](xref:razor-pages/sdk) obsługuje kompilację plików Razor.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-381">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="8c0f1-382">Podczas tworzenia projektu, Razor SDK generuje *obj/<build_configuration>/<target_framework_moniker>/Razor* katalogu w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-382">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="8c0f1-383">Struktura katalogów w katalogu *Razor* odzwierciedla strukturę katalogów projektu.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-383">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="8c0f1-384">Rozważmy następującą strukturę katalogów w projekcie ASP.NET Core 2.1 Razor Pages kierowanym na program .NET Core 2.1:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-384">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

* <span data-ttu-id="8c0f1-385">**Obszary/**</span><span class="sxs-lookup"><span data-stu-id="8c0f1-385">**Areas/**</span></span>
  * <span data-ttu-id="8c0f1-386">**Administrator/**</span><span class="sxs-lookup"><span data-stu-id="8c0f1-386">**Admin/**</span></span>
    * <span data-ttu-id="8c0f1-387">**Strony/**</span><span class="sxs-lookup"><span data-stu-id="8c0f1-387">**Pages/**</span></span>
      * <span data-ttu-id="8c0f1-388">*Indeks.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-388">*Index.cshtml*</span></span>
      * <span data-ttu-id="8c0f1-389">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-389">*Index.cshtml.cs*</span></span>
* <span data-ttu-id="8c0f1-390">**Strony/**</span><span class="sxs-lookup"><span data-stu-id="8c0f1-390">**Pages/**</span></span>
  * <span data-ttu-id="8c0f1-391">**Współdzielone/**</span><span class="sxs-lookup"><span data-stu-id="8c0f1-391">**Shared/**</span></span>
    * <span data-ttu-id="8c0f1-392">*_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-392">*_Layout.cshtml*</span></span>
  * <span data-ttu-id="8c0f1-393">*_ViewImports.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-393">*_ViewImports.cshtml*</span></span>
  * <span data-ttu-id="8c0f1-394">*_ViewStart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-394">*_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="8c0f1-395">*Indeks.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-395">*Index.cshtml*</span></span>
  * <span data-ttu-id="8c0f1-396">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-396">*Index.cshtml.cs*</span></span>

<span data-ttu-id="8c0f1-397">Tworzenie projektu w konfiguracji *debugowania* daje następujący *katalog obj:*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-397">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

* <span data-ttu-id="8c0f1-398">**obj/**</span><span class="sxs-lookup"><span data-stu-id="8c0f1-398">**obj/**</span></span>
  * <span data-ttu-id="8c0f1-399">**Debugowanie/**</span><span class="sxs-lookup"><span data-stu-id="8c0f1-399">**Debug/**</span></span>
    * <span data-ttu-id="8c0f1-400">**netcoreapp2.1/**</span><span class="sxs-lookup"><span data-stu-id="8c0f1-400">**netcoreapp2.1/**</span></span>
      * <span data-ttu-id="8c0f1-401">**Brzytwa/**</span><span class="sxs-lookup"><span data-stu-id="8c0f1-401">**Razor/**</span></span>
        * <span data-ttu-id="8c0f1-402">**Obszary/**</span><span class="sxs-lookup"><span data-stu-id="8c0f1-402">**Areas/**</span></span>
          * <span data-ttu-id="8c0f1-403">**Administrator/**</span><span class="sxs-lookup"><span data-stu-id="8c0f1-403">**Admin/**</span></span>
            * <span data-ttu-id="8c0f1-404">**Strony/**</span><span class="sxs-lookup"><span data-stu-id="8c0f1-404">**Pages/**</span></span>
              * <span data-ttu-id="8c0f1-405">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-405">*Index.g.cshtml.cs*</span></span>
        * <span data-ttu-id="8c0f1-406">**Strony/**</span><span class="sxs-lookup"><span data-stu-id="8c0f1-406">**Pages/**</span></span>
          * <span data-ttu-id="8c0f1-407">**Współdzielone/**</span><span class="sxs-lookup"><span data-stu-id="8c0f1-407">**Shared/**</span></span>
            * <span data-ttu-id="8c0f1-408">*_Layout.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-408">*_Layout.g.cshtml.cs*</span></span>
          * <span data-ttu-id="8c0f1-409">*_ViewImports.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-409">*_ViewImports.g.cshtml.cs*</span></span>
          * <span data-ttu-id="8c0f1-410">*_ViewStart.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-410">*_ViewStart.g.cshtml.cs*</span></span>
          * <span data-ttu-id="8c0f1-411">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="8c0f1-411">*Index.g.cshtml.cs*</span></span>

<span data-ttu-id="8c0f1-412">Aby wyświetlić wygenerowaną klasę *pages/index.cshtml*, otwórz *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-412">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="8c0f1-413">Dodaj następującą klasę do projektu ASP.NET Core MVC:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-413">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="8c0f1-414">W `Startup.ConfigureServices`, zastąpić `RazorTemplateEngine` dodane przez MVC `CustomTemplateEngine` z klasy:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-414">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="8c0f1-415">Ustaw punkt przerwania `return csharpDocument;` na `CustomTemplateEngine`instrukcji .</span><span class="sxs-lookup"><span data-stu-id="8c0f1-415">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="8c0f1-416">Gdy wykonanie programu zatrzymuje się w punkcie `generatedCode`przerwania, wyświetl wartość .</span><span class="sxs-lookup"><span data-stu-id="8c0f1-416">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Widok wizualizatora tekstu wygenerowanego kodu](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="8c0f1-418">Wyświetlanie odnośnych i rozróżniania wielkości liter</span><span class="sxs-lookup"><span data-stu-id="8c0f1-418">View lookups and case sensitivity</span></span>

<span data-ttu-id="8c0f1-419">Aparat widoku Razor wykonuje wyszukiwania z uwzględnieniem wielkości liter dla widoków.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-419">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="8c0f1-420">Jednak rzeczywiste wyszukiwanie jest określane przez podstawowy system plików:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-420">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="8c0f1-421">Źródło oparte na plikach:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-421">File based source:</span></span>
  * <span data-ttu-id="8c0f1-422">W systemach operacyjnych z niewrażliwymi na wielkości systemami plików (na przykład Windows) fizyczne wyszukiwania dostawcy plików są niewrażliwe na rozmiary.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-422">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="8c0f1-423">Na przykład `return View("Test")` wyniki w dopasowania dla */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, i inny wariant wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-423">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="8c0f1-424">W systemach plików z uwzględnieniem wielkości liter (na `EmbeddedFileProvider`przykład Linux, OSX i with), wyszukiwania są rozróżniane.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-424">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="8c0f1-425">Na przykład `return View("Test")` w szczególności pasuje *do /Views/Home/Test.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-425">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="8c0f1-426">Wstępnie skompilowane widoki: w przypadku ASP.NET Core 2.0 i nowszych widoków jest niewrażliwe na wszystkie systemy operacyjne.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-426">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="8c0f1-427">Zachowanie jest identyczne z zachowaniem fizycznego dostawcy plików w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-427">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="8c0f1-428">Jeśli dwa wstępnie skompilowane widoki różnią się tylko w przypadku, wynik wyszukiwania jest niedeterministyczny.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-428">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="8c0f1-429">Deweloperzy są zachęcani do dopasowania wielkości liter nazw plików i katalogów do wielkości:</span><span class="sxs-lookup"><span data-stu-id="8c0f1-429">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="8c0f1-430">Nazwy obszaru, kontrolera i akcji.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-430">Area, controller, and action names.</span></span>
* <span data-ttu-id="8c0f1-431">Strony brzytwy.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-431">Razor Pages.</span></span>

<span data-ttu-id="8c0f1-432">Dopasowanie przypadku zapewnia wdrożenia znaleźć ich widoki niezależnie od podstawowego systemu plików.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-432">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8c0f1-433">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="8c0f1-433">Additional resources</span></span>

<span data-ttu-id="8c0f1-434">[Wprowadzenie do ASP.NET programowania sieci Web Za pomocą składni Razor](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) zapewnia wiele przykładów programowania ze składnią Razor.</span><span class="sxs-lookup"><span data-stu-id="8c0f1-434">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
