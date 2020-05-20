---
title: odwołanie do składni Razor dla ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej o Razor składni znaczników na potrzeby osadzania kodu opartego na serwerze w stronach sieci Web.
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/razor
ms.openlocfilehash: 2831fd2edd029043e9457cd213e32f1a82c2872e
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424424"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="4474d-103">Składnia Razor odwołanie do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4474d-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="4474d-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)i [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="4474d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="4474d-105">Razor jest składnią znaczników dla osadzania kodu opartego na serwerze w stronach sieci Web.</span><span class="sxs-lookup"><span data-stu-id="4474d-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="4474d-106">Składnia Razor składa się z znaczników Razor, C# i HTML.</span><span class="sxs-lookup"><span data-stu-id="4474d-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="4474d-107">Pliki zawierające plik Razor zazwyczaj mają rozszerzenie *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4474d-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="4474d-108">Razor również w plikach [składników Razor](xref:blazor/components) (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="4474d-108">Razor is also found in [Razor components](xref:blazor/components) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="4474d-109">Renderowanie HTML</span><span class="sxs-lookup"><span data-stu-id="4474d-109">Rendering HTML</span></span>

<span data-ttu-id="4474d-110">Domyślnym językiem składni Razor jest HTML.</span><span class="sxs-lookup"><span data-stu-id="4474d-110">The default Razor language is HTML.</span></span> <span data-ttu-id="4474d-111">Renderowanie kodu HTML ze tagów składni Razor nie różni się od renderowania kodu HTML z pliku HTML.</span><span class="sxs-lookup"><span data-stu-id="4474d-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="4474d-112">Znaczniki HTML w plikach Razor *. cshtml* są renderowane przez serwer bez zmian.</span><span class="sxs-lookup"><span data-stu-id="4474d-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="4474d-113">Składnia Razor</span><span class="sxs-lookup"><span data-stu-id="4474d-113">Razor syntax</span></span>

<span data-ttu-id="4474d-114">Razor obsługuje C# i używa `@` symbolu do przechodzenia z języka HTML do języka c#.</span><span class="sxs-lookup"><span data-stu-id="4474d-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="4474d-115">Razor oblicza wyrażenia języka C# i renderuje je w danych wyjściowych w formacie HTML.</span><span class="sxs-lookup"><span data-stu-id="4474d-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="4474d-116">Gdy `@` po symbolu następuje [słowo kluczowe zarezerwowane Razor](#razor-reserved-keywords), przechodzi do znacznika specyficznego dla Razor.</span><span class="sxs-lookup"><span data-stu-id="4474d-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="4474d-117">W przeciwnym razie przechodzi do zwykłego języka C#.</span><span class="sxs-lookup"><span data-stu-id="4474d-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="4474d-118">Aby wypróbować `@` symbol w znaczniku Razor, użyj drugiego `@` symbolu:</span><span class="sxs-lookup"><span data-stu-id="4474d-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="4474d-119">Kod jest renderowany w języku HTML z pojedynczym `@` symbolem:</span><span class="sxs-lookup"><span data-stu-id="4474d-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="4474d-120">Atrybuty i zawartość HTML zawierające adresy e-mail nie traktują `@` symbolu jako znaku przejścia.</span><span class="sxs-lookup"><span data-stu-id="4474d-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="4474d-121">W poniższym przykładzie adresy e-mail są nienaruszone przez analizę Razor:</span><span class="sxs-lookup"><span data-stu-id="4474d-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="4474d-122">Niejawne wyrażenia Razor</span><span class="sxs-lookup"><span data-stu-id="4474d-122">Implicit Razor expressions</span></span>

<span data-ttu-id="4474d-123">Niejawne wyrażenia Razor zaczynają się od `@` kodu C#:</span><span class="sxs-lookup"><span data-stu-id="4474d-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="4474d-124">Z wyjątkiem `await` słowa kluczowego języka C# wyrażenia niejawne nie mogą zawierać spacji.</span><span class="sxs-lookup"><span data-stu-id="4474d-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="4474d-125">Jeśli instrukcja języka C# ma jasne zakończenie, spacje mogą być wymieszaniu:</span><span class="sxs-lookup"><span data-stu-id="4474d-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="4474d-126">Wyrażenia niejawne **nie mogą** zawierać typów ogólnych języka C#, ponieważ znaki wewnątrz nawiasów ( `<>` ) są interpretowane jako tag HTML.</span><span class="sxs-lookup"><span data-stu-id="4474d-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="4474d-127">Następujący **kod jest nieprawidłowy** :</span><span class="sxs-lookup"><span data-stu-id="4474d-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="4474d-128">Poprzedni kod generuje błąd kompilatora podobny do jednego z następujących:</span><span class="sxs-lookup"><span data-stu-id="4474d-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="4474d-129">Element "int" nie został zamknięty.</span><span class="sxs-lookup"><span data-stu-id="4474d-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="4474d-130">Wszystkie elementy muszą być albo zamykane samodzielnie lub mieć pasujący tag końcowy.</span><span class="sxs-lookup"><span data-stu-id="4474d-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="4474d-131">Nie można przekonwertować grupy metod "GenericMethod" na typ inny niż delegatd "Object".</span><span class="sxs-lookup"><span data-stu-id="4474d-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="4474d-132">Czy chodziło o wywołanie metody?</span><span class="sxs-lookup"><span data-stu-id="4474d-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="4474d-133">Wywołania metody ogólnej muszą być opakowane w [jawne wyrażenie Razor](#explicit-razor-expressions) lub [blok kodu Razor](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="4474d-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="4474d-134">Jawne wyrażenia Razor</span><span class="sxs-lookup"><span data-stu-id="4474d-134">Explicit Razor expressions</span></span>

<span data-ttu-id="4474d-135">Jawne wyrażenia Razor składają się z `@` symbolu z zrównoważonym nawiasem.</span><span class="sxs-lookup"><span data-stu-id="4474d-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="4474d-136">Aby renderować czas ostatniego tygodnia, używana jest następująca funkcja znaczników Razor:</span><span class="sxs-lookup"><span data-stu-id="4474d-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="4474d-137">Każda zawartość w `@()` nawiasie zostanie oceniona i przerenderowana do danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="4474d-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="4474d-138">Wyrażenia niejawne, opisane w poprzedniej sekcji, zazwyczaj nie mogą zawierać spacji.</span><span class="sxs-lookup"><span data-stu-id="4474d-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="4474d-139">W poniższym kodzie tydzień nie jest odejmowany od bieżącego czasu:</span><span class="sxs-lookup"><span data-stu-id="4474d-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="4474d-140">Kod renderuje następujący HTML:</span><span class="sxs-lookup"><span data-stu-id="4474d-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="4474d-141">Wyrażenia jawne mogą służyć do łączenia tekstu z wynikiem wyrażenia:</span><span class="sxs-lookup"><span data-stu-id="4474d-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="4474d-142">Bez wyrażenia jawnego `<p>Age@joe.Age</p>` jest traktowany jako adres e-mail i `<p>Age@joe.Age</p>` jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="4474d-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="4474d-143">Po zapisaniu jako jawne wyrażenie `<p>Age33</p>` jest renderowane.</span><span class="sxs-lookup"><span data-stu-id="4474d-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="4474d-144">Wyrażenia jawne mogą służyć do renderowania danych wyjściowych z metod ogólnych w plikach *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4474d-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="4474d-145">W poniższym znaczniku pokazano, jak poprawić przedstawiony wcześniej błąd w nawiasach ogólnych języka C#.</span><span class="sxs-lookup"><span data-stu-id="4474d-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="4474d-146">Kod jest zapisywana jako jawne wyrażenie:</span><span class="sxs-lookup"><span data-stu-id="4474d-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="4474d-147">Kodowanie wyrażeń</span><span class="sxs-lookup"><span data-stu-id="4474d-147">Expression encoding</span></span>

<span data-ttu-id="4474d-148">Wyrażenia języka C#, które są obliczane jako ciąg, są kodowane w formacie HTML.</span><span class="sxs-lookup"><span data-stu-id="4474d-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="4474d-149">Wyrażenia języka C#, które mają być `IHtmlContent` renderowane bezpośrednio przez `IHtmlContent.WriteTo` .</span><span class="sxs-lookup"><span data-stu-id="4474d-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="4474d-150">Wyrażenia języka C#, które nie są szacowane do, `IHtmlContent` są konwertowane na ciąg przez `ToString` i kodowane przed ich renderowaniem.</span><span class="sxs-lookup"><span data-stu-id="4474d-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="4474d-151">Kod renderuje następujący HTML:</span><span class="sxs-lookup"><span data-stu-id="4474d-151">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="4474d-152">KOD HTML jest wyświetlany w przeglądarce jako:</span><span class="sxs-lookup"><span data-stu-id="4474d-152">The HTML is shown in the browser as:</span></span>

```html
<span>Hello World</span>
```

<span data-ttu-id="4474d-153">`HtmlHelper.Raw`dane wyjściowe nie są kodowane, ale renderowane jako znaczniki HTML.</span><span class="sxs-lookup"><span data-stu-id="4474d-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="4474d-154">Korzystanie `HtmlHelper.Raw` z nieoczyszczonych danych wejściowych użytkownika stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="4474d-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="4474d-155">Dane wejściowe użytkownika mogą zawierać złośliwe skrypty JavaScript lub inne luki w zabezpieczeniach.</span><span class="sxs-lookup"><span data-stu-id="4474d-155">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="4474d-156">Oczyszczanie danych wejściowych użytkownika jest trudne.</span><span class="sxs-lookup"><span data-stu-id="4474d-156">Sanitizing user input is difficult.</span></span> <span data-ttu-id="4474d-157">Unikaj używania `HtmlHelper.Raw` z danymi wejściowymi użytkownika.</span><span class="sxs-lookup"><span data-stu-id="4474d-157">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="4474d-158">Kod renderuje następujący HTML:</span><span class="sxs-lookup"><span data-stu-id="4474d-158">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="4474d-159">Bloki kodu Razor</span><span class="sxs-lookup"><span data-stu-id="4474d-159">Razor code blocks</span></span>

<span data-ttu-id="4474d-160">Bloki kodu Razor zaczynają się od `@` i są ujęte w `{}` .</span><span class="sxs-lookup"><span data-stu-id="4474d-160">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="4474d-161">W przeciwieństwie do wyrażeń, kod C# wewnątrz bloków kodu nie jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="4474d-161">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="4474d-162">Bloki kodu i wyrażenia w widoku mają ten sam zakres i są zdefiniowane w kolejności:</span><span class="sxs-lookup"><span data-stu-id="4474d-162">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="4474d-163">Kod renderuje następujący HTML:</span><span class="sxs-lookup"><span data-stu-id="4474d-163">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4474d-164">W blokach kodu Zadeklaruj [funkcje lokalne](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) z adiustacją, aby zapewnić metody tworzenia szablonów:</span><span class="sxs-lookup"><span data-stu-id="4474d-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

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

<span data-ttu-id="4474d-165">Kod renderuje następujący HTML:</span><span class="sxs-lookup"><span data-stu-id="4474d-165">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="4474d-166">Przejścia niejawne</span><span class="sxs-lookup"><span data-stu-id="4474d-166">Implicit transitions</span></span>

<span data-ttu-id="4474d-167">Językiem domyślnym w bloku kodu jest C#, ale strona Razor może przejść z powrotem do HTML:</span><span class="sxs-lookup"><span data-stu-id="4474d-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="4474d-168">Jawne ograniczone przechodzenie</span><span class="sxs-lookup"><span data-stu-id="4474d-168">Explicit delimited transition</span></span>

<span data-ttu-id="4474d-169">Aby zdefiniować podsekcję bloku kodu, który powinien renderować HTML, należy ująć znaki do renderowania przy użyciu `<text>` tagu Razor:</span><span class="sxs-lookup"><span data-stu-id="4474d-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="4474d-170">To podejście służy do renderowania kodu HTML, który nie jest ujęty w tag HTML.</span><span class="sxs-lookup"><span data-stu-id="4474d-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="4474d-171">Bez tagu HTML lub Razor Wystąpił błąd środowiska uruchomieniowego Razor.</span><span class="sxs-lookup"><span data-stu-id="4474d-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="4474d-172">`<text>`Tag jest przydatny do kontrolowania odstępów podczas renderowania zawartości:</span><span class="sxs-lookup"><span data-stu-id="4474d-172">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="4474d-173">Tylko zawartość między `<text>` tagiem jest renderowana.</span><span class="sxs-lookup"><span data-stu-id="4474d-173">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="4474d-174">W danych wyjściowych HTML nie ma odstępów przed tagiem ani po nim `<text>` .</span><span class="sxs-lookup"><span data-stu-id="4474d-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="4474d-175">Jawne przejście liniowe</span><span class="sxs-lookup"><span data-stu-id="4474d-175">Explicit line transition</span></span>

<span data-ttu-id="4474d-176">Aby renderować resztę całego wiersza jako HTML wewnątrz bloku kodu, użyj `@:` składni:</span><span class="sxs-lookup"><span data-stu-id="4474d-176">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="4474d-177">Bez `@:` kodu, generowany jest błąd środowiska uruchomieniowego Razor.</span><span class="sxs-lookup"><span data-stu-id="4474d-177">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="4474d-178">Dodatkowe `@` znaki w pliku Razor mogą spowodować błędy kompilatora w instrukcjach znajdujących się w dalszej części bloku.</span><span class="sxs-lookup"><span data-stu-id="4474d-178">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="4474d-179">Te błędy kompilatora mogą być trudne do zrozumienia, ponieważ rzeczywisty błąd występuje przed zgłoszonym błędem.</span><span class="sxs-lookup"><span data-stu-id="4474d-179">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="4474d-180">Ten błąd jest typowy po połączeniu wielu wyrażeń jawnych/jawnych w jeden blok kodu.</span><span class="sxs-lookup"><span data-stu-id="4474d-180">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="4474d-181">Struktury formantów</span><span class="sxs-lookup"><span data-stu-id="4474d-181">Control structures</span></span>

<span data-ttu-id="4474d-182">Struktury formantów są rozszerzeniem bloków kodu.</span><span class="sxs-lookup"><span data-stu-id="4474d-182">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="4474d-183">Wszystkie aspekty bloków kodu (przechodzenie do znaczników, wbudowane C#) również mają zastosowanie do następujących struktur:</span><span class="sxs-lookup"><span data-stu-id="4474d-183">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="4474d-184">Warunkowe \@ IF, Else IF, else i \@ Switch</span><span class="sxs-lookup"><span data-stu-id="4474d-184">Conditionals \@if, else if, else, and \@switch</span></span>

<span data-ttu-id="4474d-185">`@if`kontroluje, kiedy kod jest uruchamiany:</span><span class="sxs-lookup"><span data-stu-id="4474d-185">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="4474d-186">`else`i `else if` nie wymaga `@` symbolu:</span><span class="sxs-lookup"><span data-stu-id="4474d-186">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="4474d-187">W poniższym znaczniku pokazano, jak używać instrukcji switch:</span><span class="sxs-lookup"><span data-stu-id="4474d-187">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="4474d-188">Zapętlenie \@ dla, \@ foreach, \@ while, i \@ do while</span><span class="sxs-lookup"><span data-stu-id="4474d-188">Looping \@for, \@foreach, \@while, and \@do while</span></span>

<span data-ttu-id="4474d-189">KOD HTML z szablonem może być renderowany przy użyciu instrukcji sterowania pętlą.</span><span class="sxs-lookup"><span data-stu-id="4474d-189">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="4474d-190">Aby renderować listę osób:</span><span class="sxs-lookup"><span data-stu-id="4474d-190">To render a list of people:</span></span>

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

<span data-ttu-id="4474d-191">Obsługiwane są następujące instrukcje pętli:</span><span class="sxs-lookup"><span data-stu-id="4474d-191">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="4474d-192">Złożone \@ użycie</span><span class="sxs-lookup"><span data-stu-id="4474d-192">Compound \@using</span></span>

<span data-ttu-id="4474d-193">W języku C# `using` instrukcja jest używana w celu upewnienia się, że obiekt został usunięty.</span><span class="sxs-lookup"><span data-stu-id="4474d-193">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="4474d-194">W przypadku aparatu Razor ten sam mechanizm jest używany do tworzenia pomocników HTML zawierających dodatkową zawartość.</span><span class="sxs-lookup"><span data-stu-id="4474d-194">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="4474d-195">W poniższym kodzie, pomocników HTML renderuje `<form>` tag przy użyciu `@using` instrukcji:</span><span class="sxs-lookup"><span data-stu-id="4474d-195">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a><span data-ttu-id="4474d-196">\@Wypróbuj, catch, finally</span><span class="sxs-lookup"><span data-stu-id="4474d-196">\@try, catch, finally</span></span>

<span data-ttu-id="4474d-197">Obsługa wyjątków jest podobna do języka C#:</span><span class="sxs-lookup"><span data-stu-id="4474d-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a><span data-ttu-id="4474d-198">\@skręt</span><span class="sxs-lookup"><span data-stu-id="4474d-198">\@lock</span></span>

<span data-ttu-id="4474d-199">Razor ma możliwość ochrony sekcji krytycznych przy użyciu instrukcji lock:</span><span class="sxs-lookup"><span data-stu-id="4474d-199">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="4474d-200">Komentarze</span><span class="sxs-lookup"><span data-stu-id="4474d-200">Comments</span></span>

<span data-ttu-id="4474d-201">Razor obsługuje komentarze w językach C# i HTML:</span><span class="sxs-lookup"><span data-stu-id="4474d-201">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="4474d-202">Kod renderuje następujący HTML:</span><span class="sxs-lookup"><span data-stu-id="4474d-202">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="4474d-203">Komentarze Razor są usuwane przez serwer przed renderowaniem strony sieci Web.</span><span class="sxs-lookup"><span data-stu-id="4474d-203">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="4474d-204">Razor używa `@*  *@` do ograniczania komentarzy.</span><span class="sxs-lookup"><span data-stu-id="4474d-204">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="4474d-205">Poniższy kod jest oznaczony jako komentarz, więc serwer nie renderuje żadnego znacznika:</span><span class="sxs-lookup"><span data-stu-id="4474d-205">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="4474d-206">Dyrektyw</span><span class="sxs-lookup"><span data-stu-id="4474d-206">Directives</span></span>

<span data-ttu-id="4474d-207">Dyrektywy Razor są reprezentowane przez niejawne wyrażenia z zastrzeżonymi słowami kluczowymi po `@` symbolu.</span><span class="sxs-lookup"><span data-stu-id="4474d-207">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="4474d-208">Dyrektywa zwykle zmienia sposób analizowania widoku lub umożliwia wykonywanie różnych funkcji.</span><span class="sxs-lookup"><span data-stu-id="4474d-208">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="4474d-209">Zrozumienie, jak program Razor generuje kod dla widoku, ułatwia zrozumienie, jak działają dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="4474d-209">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="4474d-210">Kod generuje klasę podobną do następującej:</span><span class="sxs-lookup"><span data-stu-id="4474d-210">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="4474d-211">W dalszej części tego artykułu, sekcja [Badanie klasy Razor C# wygenerowanej dla widoku](#inspect-the-razor-c-class-generated-for-a-view) wyjaśnia sposób wyświetlania tej wygenerowanej klasy.</span><span class="sxs-lookup"><span data-stu-id="4474d-211">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### <a name="attribute"></a><span data-ttu-id="4474d-212">\@przypisane</span><span class="sxs-lookup"><span data-stu-id="4474d-212">\@attribute</span></span>

<span data-ttu-id="4474d-213">`@attribute`Dyrektywa dodaje dany atrybut do klasy wygenerowanej strony lub widoku.</span><span class="sxs-lookup"><span data-stu-id="4474d-213">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="4474d-214">Poniższy przykład dodaje `[Authorize]` atrybut:</span><span class="sxs-lookup"><span data-stu-id="4474d-214">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a><span data-ttu-id="4474d-215">\@kodu</span><span class="sxs-lookup"><span data-stu-id="4474d-215">\@code</span></span>

<span data-ttu-id="4474d-216">*Ten scenariusz dotyczy tylko składników Razor (Razor).*</span><span class="sxs-lookup"><span data-stu-id="4474d-216">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="4474d-217">`@code`Blok umożliwia [składnikowi Razor](xref:blazor/components) Dodawanie elementów członkowskich C# (pól, właściwości i metod) do składnika:</span><span class="sxs-lookup"><span data-stu-id="4474d-217">The `@code` block enables a [Razor component](xref:blazor/components) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="4474d-218">W przypadku składników Razor `@code` jest alias [`@functions`](#functions) i zalecany `@functions` .</span><span class="sxs-lookup"><span data-stu-id="4474d-218">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="4474d-219">Dozwolony jest więcej niż jeden `@code` blok.</span><span class="sxs-lookup"><span data-stu-id="4474d-219">More than one `@code` block is permissible.</span></span>

::: moniker-end

### <a name="functions"></a><span data-ttu-id="4474d-220">\@obowiązki</span><span class="sxs-lookup"><span data-stu-id="4474d-220">\@functions</span></span>

<span data-ttu-id="4474d-221">`@functions`Dyrektywa umożliwia dodawanie elementów członkowskich C# (pól, właściwości i metod) do wygenerowanej klasy:</span><span class="sxs-lookup"><span data-stu-id="4474d-221">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4474d-222">W [składnikach Razor](xref:blazor/components), `@code` Użyj `@functions` do dodawania elementów członkowskich języka C#.</span><span class="sxs-lookup"><span data-stu-id="4474d-222">In [Razor components](xref:blazor/components), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="4474d-223">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="4474d-223">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="4474d-224">Kod generuje następujący znacznik HTML:</span><span class="sxs-lookup"><span data-stu-id="4474d-224">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="4474d-225">Następujący kod jest wygenerowaną klasą Razor C#:</span><span class="sxs-lookup"><span data-stu-id="4474d-225">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4474d-226">`@functions`metody służą jako metody tworzenia szablonów, gdy mają znaczniki:</span><span class="sxs-lookup"><span data-stu-id="4474d-226">`@functions` methods serve as templating methods when they have markup:</span></span>

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

<span data-ttu-id="4474d-227">Kod renderuje następujący HTML:</span><span class="sxs-lookup"><span data-stu-id="4474d-227">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a><span data-ttu-id="4474d-228">\@wprowadza</span><span class="sxs-lookup"><span data-stu-id="4474d-228">\@implements</span></span>

<span data-ttu-id="4474d-229">`@implements`Dyrektywa implementuje interfejs dla wygenerowanej klasy.</span><span class="sxs-lookup"><span data-stu-id="4474d-229">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="4474d-230">Poniższy przykład implementuje, <xref:System.IDisposable?displayProperty=fullName> Aby <xref:System.IDisposable.Dispose*> można było wywołać metodę:</span><span class="sxs-lookup"><span data-stu-id="4474d-230">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

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

### <a name="inherits"></a><span data-ttu-id="4474d-231">\@inherit</span><span class="sxs-lookup"><span data-stu-id="4474d-231">\@inherits</span></span>

<span data-ttu-id="4474d-232">`@inherits`Dyrektywa zapewnia pełną kontrolę nad klasą, którą dziedziczy widok:</span><span class="sxs-lookup"><span data-stu-id="4474d-232">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="4474d-233">Następujący kod jest niestandardowym typem strony Razor:</span><span class="sxs-lookup"><span data-stu-id="4474d-233">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="4474d-234">`CustomText`Zostanie wyświetlony w widoku:</span><span class="sxs-lookup"><span data-stu-id="4474d-234">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="4474d-235">Kod renderuje następujący HTML:</span><span class="sxs-lookup"><span data-stu-id="4474d-235">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="4474d-236">`@model`i `@inherits` mogą być używane w tym samym widoku.</span><span class="sxs-lookup"><span data-stu-id="4474d-236">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="4474d-237">`@inherits`może znajdować się w pliku *_ViewImports. cshtml* , który został zaimportowany przez widok:</span><span class="sxs-lookup"><span data-stu-id="4474d-237">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="4474d-238">Poniższy kod stanowi przykład widoku o jednoznacznie określonym typie:</span><span class="sxs-lookup"><span data-stu-id="4474d-238">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="4474d-239">W przypadku rick@contoso.com przekazywania "" w modelu widok generuje następujący znacznik HTML:</span><span class="sxs-lookup"><span data-stu-id="4474d-239">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a><span data-ttu-id="4474d-240">\@dodanie</span><span class="sxs-lookup"><span data-stu-id="4474d-240">\@inject</span></span>

<span data-ttu-id="4474d-241">`@inject`Dyrektywa umożliwia stronie Razor dodanie usługi z [kontenera usługi](xref:fundamentals/dependency-injection) do widoku.</span><span class="sxs-lookup"><span data-stu-id="4474d-241">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="4474d-242">Aby uzyskać więcej informacji, zobacz [iniekcja zależności w widokach](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4474d-242">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a><span data-ttu-id="4474d-243">\@wyglądu</span><span class="sxs-lookup"><span data-stu-id="4474d-243">\@layout</span></span>

<span data-ttu-id="4474d-244">*Ten scenariusz dotyczy tylko składników Razor (Razor).*</span><span class="sxs-lookup"><span data-stu-id="4474d-244">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="4474d-245">`@layout`Dyrektywa określa układ składnika Razor.</span><span class="sxs-lookup"><span data-stu-id="4474d-245">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="4474d-246">Składniki układu są używane do uniknięcia duplikowania kodu i niespójności.</span><span class="sxs-lookup"><span data-stu-id="4474d-246">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="4474d-247">Aby uzyskać więcej informacji, zobacz <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="4474d-247">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### <a name="model"></a><span data-ttu-id="4474d-248">\@wzorów</span><span class="sxs-lookup"><span data-stu-id="4474d-248">\@model</span></span>

<span data-ttu-id="4474d-249">*Ten scenariusz dotyczy tylko widoków MVC i Razor Pages (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="4474d-249">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="4474d-250">`@model`Dyrektywa określa typ modelu przekazaną do widoku lub strony:</span><span class="sxs-lookup"><span data-stu-id="4474d-250">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="4474d-251">W aplikacji ASP.NET Core MVC lub Razor Pages utworzonej przy użyciu poszczególnych kont użytkowników, *widoki/konta/login. cshtml* zawierają następującą deklarację modelu:</span><span class="sxs-lookup"><span data-stu-id="4474d-251">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="4474d-252">Wygenerowana Klasa dziedziczy po `RazorPage<dynamic>` :</span><span class="sxs-lookup"><span data-stu-id="4474d-252">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="4474d-253">Razor uwidacznia `Model` Właściwość do uzyskiwania dostępu do modelu przekazaną do widoku:</span><span class="sxs-lookup"><span data-stu-id="4474d-253">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="4474d-254">`@model`Dyrektywa określa typ `Model` właściwości.</span><span class="sxs-lookup"><span data-stu-id="4474d-254">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="4474d-255">Dyrektywa określa, `T` w `RazorPage<T>` którym wygenerowanej klasie, z której pochodzi widok.</span><span class="sxs-lookup"><span data-stu-id="4474d-255">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="4474d-256">Jeśli `@model` dyrektywa nie jest określona, `Model` Właściwość jest typu `dynamic` .</span><span class="sxs-lookup"><span data-stu-id="4474d-256">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="4474d-257">Aby uzyskać więcej informacji, zobacz [silnie wpisane modele i @model słowo kluczowe](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="4474d-257">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### <a name="namespace"></a><span data-ttu-id="4474d-258">\@obszaru</span><span class="sxs-lookup"><span data-stu-id="4474d-258">\@namespace</span></span>

<span data-ttu-id="4474d-259">`@namespace`Dyrektywa:</span><span class="sxs-lookup"><span data-stu-id="4474d-259">The `@namespace` directive:</span></span>

* <span data-ttu-id="4474d-260">Ustawia przestrzeń nazw klasy wygenerowanej strony Razor, widoku MVC lub składnika Razor.</span><span class="sxs-lookup"><span data-stu-id="4474d-260">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="4474d-261">Ustawia główne pochodne przestrzenie nazw stron, widoków lub klas składników z najbliższego pliku importów w drzewie katalogów, *_ViewImports. cshtml* (widoki lub strony) lub *_Imports. Razor* (składniki Razor).</span><span class="sxs-lookup"><span data-stu-id="4474d-261">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="4474d-262">W przykładzie Razor Pages przedstawionym w poniższej tabeli:</span><span class="sxs-lookup"><span data-stu-id="4474d-262">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="4474d-263">Każda Strona importuje *strony/_ViewImports. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4474d-263">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="4474d-264">*Strona/_ViewImports. cshtml* zawiera `@namespace Hello.World` .</span><span class="sxs-lookup"><span data-stu-id="4474d-264">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="4474d-265">Każda Strona ma `Hello.World` jako element główny przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="4474d-265">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="4474d-266">Strona</span><span class="sxs-lookup"><span data-stu-id="4474d-266">Page</span></span>                                        | <span data-ttu-id="4474d-267">Przestrzeń nazw</span><span class="sxs-lookup"><span data-stu-id="4474d-267">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="4474d-268">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="4474d-268">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="4474d-269">*Pages/MorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="4474d-269">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="4474d-270">*Pages/MorePages/EvenMorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="4474d-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="4474d-271">Poprzednie relacje mają zastosowanie do plików importu używanych z widokami MVC i składnikami Razor.</span><span class="sxs-lookup"><span data-stu-id="4474d-271">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="4474d-272">Gdy wiele plików importu ma `@namespace` dyrektywę, plik znajdujący się najbliżej strony, widoku lub składnika w drzewie katalogów jest używany do ustawiania głównej przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="4474d-272">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="4474d-273">Jeśli folder *EvenMorePages* w poprzednim przykładzie ma plik Imports z plikiem `@namespace Another.Planet` (lub *strony/MorePages/EvenMorePages/Page. cshtml* zawiera `@namespace Another.Planet` ), wynik zostanie przedstawiony w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="4474d-273">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="4474d-274">Strona</span><span class="sxs-lookup"><span data-stu-id="4474d-274">Page</span></span>                                        | <span data-ttu-id="4474d-275">Przestrzeń nazw</span><span class="sxs-lookup"><span data-stu-id="4474d-275">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="4474d-276">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="4474d-276">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="4474d-277">*Pages/MorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="4474d-277">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="4474d-278">*Pages/MorePages/EvenMorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="4474d-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### <a name="page"></a><span data-ttu-id="4474d-279">\@stronic</span><span class="sxs-lookup"><span data-stu-id="4474d-279">\@page</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4474d-280">`@page`Dyrektywa ma różne skutki w zależności od typu pliku, w którym występuje.</span><span class="sxs-lookup"><span data-stu-id="4474d-280">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="4474d-281">Dyrektywa:</span><span class="sxs-lookup"><span data-stu-id="4474d-281">The directive:</span></span>

* <span data-ttu-id="4474d-282">W pliku *. cshtml* wskazuje, że plik jest stroną Razor.</span><span class="sxs-lookup"><span data-stu-id="4474d-282">In in a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="4474d-283">Aby uzyskać więcej informacji, zobacz [trasy niestandardowe](xref:razor-pages/index#custom-routes) i <xref:razor-pages/index> .</span><span class="sxs-lookup"><span data-stu-id="4474d-283">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="4474d-284">Określa, że składnik Razor powinien obsługiwać żądania bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="4474d-284">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="4474d-285">Aby uzyskać więcej informacji, zobacz <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="4474d-285">For more information, see <xref:blazor/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4474d-286">`@page`Dyrektywa w pierwszym wierszu pliku *. cshtml* wskazuje, że plik jest stroną Razor.</span><span class="sxs-lookup"><span data-stu-id="4474d-286">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="4474d-287">Aby uzyskać więcej informacji, zobacz <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="4474d-287">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### <a name="section"></a><span data-ttu-id="4474d-288">\@Paragraf</span><span class="sxs-lookup"><span data-stu-id="4474d-288">\@section</span></span>

<span data-ttu-id="4474d-289">*Ten scenariusz dotyczy tylko widoków MVC i Razor Pages (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="4474d-289">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="4474d-290">`@section`Dyrektywa jest używana w połączeniu z [układem MVC i Razor Pages](xref:mvc/views/layout) , aby umożliwić widokom lub stronom renderowanie zawartości w różnych częściach strony html.</span><span class="sxs-lookup"><span data-stu-id="4474d-290">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="4474d-291">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="4474d-291">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="using"></a><span data-ttu-id="4474d-292">\@using</span><span class="sxs-lookup"><span data-stu-id="4474d-292">\@using</span></span>

<span data-ttu-id="4474d-293">`@using`Dyrektywa dodaje `using` dyrektywę C# do wygenerowanego widoku:</span><span class="sxs-lookup"><span data-stu-id="4474d-293">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4474d-294">W [składnikach Razor](xref:blazor/components) `@using` również kontroluje składniki, które znajdują się w zakresie.</span><span class="sxs-lookup"><span data-stu-id="4474d-294">In [Razor components](xref:blazor/components), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="4474d-295">Atrybuty dyrektywy</span><span class="sxs-lookup"><span data-stu-id="4474d-295">Directive attributes</span></span>

<span data-ttu-id="4474d-296">Atrybuty dyrektywy Razor są reprezentowane przez niejawne wyrażenia z zastrzeżonymi słowami kluczowymi po `@` symbolu.</span><span class="sxs-lookup"><span data-stu-id="4474d-296">Razor directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="4474d-297">Atrybut dyrektywy zazwyczaj zmienia sposób analizowania elementu lub umożliwia korzystanie z różnych funkcji.</span><span class="sxs-lookup"><span data-stu-id="4474d-297">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### <a name="attributes"></a><span data-ttu-id="4474d-298">\@Attributes</span><span class="sxs-lookup"><span data-stu-id="4474d-298">\@attributes</span></span>

<span data-ttu-id="4474d-299">*Ten scenariusz dotyczy tylko składników Razor (Razor).*</span><span class="sxs-lookup"><span data-stu-id="4474d-299">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="4474d-300">`@attributes`zezwala składnikowi na renderowanie niezadeklarowanych atrybutów.</span><span class="sxs-lookup"><span data-stu-id="4474d-300">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="4474d-301">Aby uzyskać więcej informacji, zobacz <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="4474d-301">For more information, see <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span></span>

### <a name="bind"></a><span data-ttu-id="4474d-302">\@węglowodor</span><span class="sxs-lookup"><span data-stu-id="4474d-302">\@bind</span></span>

<span data-ttu-id="4474d-303">*Ten scenariusz dotyczy tylko składników Razor (Razor).*</span><span class="sxs-lookup"><span data-stu-id="4474d-303">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="4474d-304">Powiązanie danych w składnikach jest realizowane przy użyciu `@bind` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="4474d-304">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="4474d-305">Aby uzyskać więcej informacji, zobacz <xref:blazor/data-binding>.</span><span class="sxs-lookup"><span data-stu-id="4474d-305">For more information, see <xref:blazor/data-binding>.</span></span>

### <a name="onevent"></a><span data-ttu-id="4474d-306">\@na {EVENT}</span><span class="sxs-lookup"><span data-stu-id="4474d-306">\@on{EVENT}</span></span>

<span data-ttu-id="4474d-307">*Ten scenariusz dotyczy tylko składników Razor (Razor).*</span><span class="sxs-lookup"><span data-stu-id="4474d-307">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="4474d-308">Razor udostępnia funkcje obsługi zdarzeń dla składników.</span><span class="sxs-lookup"><span data-stu-id="4474d-308">Razor provides event handling features for components.</span></span> <span data-ttu-id="4474d-309">Aby uzyskać więcej informacji, zobacz <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="4474d-309">For more information, see <xref:blazor/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a><span data-ttu-id="4474d-310">\@na {EVENT}:p reventDefault</span><span class="sxs-lookup"><span data-stu-id="4474d-310">\@on{EVENT}:preventDefault</span></span>

<span data-ttu-id="4474d-311">*Ten scenariusz dotyczy tylko składników Razor (Razor).*</span><span class="sxs-lookup"><span data-stu-id="4474d-311">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="4474d-312">Zapobiega domyślnej akcji dla zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="4474d-312">Prevents the default action for the event.</span></span>

### <a name="oneventstoppropagation"></a><span data-ttu-id="4474d-313">\@na {EVENT}: stopPropagation</span><span class="sxs-lookup"><span data-stu-id="4474d-313">\@on{EVENT}:stopPropagation</span></span>

<span data-ttu-id="4474d-314">*Ten scenariusz dotyczy tylko składników Razor (Razor).*</span><span class="sxs-lookup"><span data-stu-id="4474d-314">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="4474d-315">Powoduje zatrzymanie propagacji zdarzenia dla zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="4474d-315">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a><span data-ttu-id="4474d-316">\@key</span><span class="sxs-lookup"><span data-stu-id="4474d-316">\@key</span></span>

<span data-ttu-id="4474d-317">*Ten scenariusz dotyczy tylko składników Razor (Razor).*</span><span class="sxs-lookup"><span data-stu-id="4474d-317">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="4474d-318">`@key`Atrybut dyrektywy powoduje, że algorytmy porównujące składniki, aby zagwarantować zachowywanie elementów lub składników na podstawie wartości klucza.</span><span class="sxs-lookup"><span data-stu-id="4474d-318">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="4474d-319">Aby uzyskać więcej informacji, zobacz <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="4474d-319">For more information, see <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### <a name="ref"></a><span data-ttu-id="4474d-320">\@ref</span><span class="sxs-lookup"><span data-stu-id="4474d-320">\@ref</span></span>

<span data-ttu-id="4474d-321">*Ten scenariusz dotyczy tylko składników Razor (Razor).*</span><span class="sxs-lookup"><span data-stu-id="4474d-321">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="4474d-322">Odwołania do składników ( `@ref` ) umożliwiają odwoływanie się do wystąpienia składnika, dzięki czemu można wydać polecenia do tego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="4474d-322">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="4474d-323">Aby uzyskać więcej informacji, zobacz <xref:blazor/components#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="4474d-323">For more information, see <xref:blazor/components#capture-references-to-components>.</span></span>

### <a name="typeparam"></a><span data-ttu-id="4474d-324">\@typeparam</span><span class="sxs-lookup"><span data-stu-id="4474d-324">\@typeparam</span></span>

<span data-ttu-id="4474d-325">*Ten scenariusz dotyczy tylko składników Razor (Razor).*</span><span class="sxs-lookup"><span data-stu-id="4474d-325">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="4474d-326">`@typeparam`Dyrektywa deklaruje parametr typu ogólnego dla wygenerowanej klasy składnika.</span><span class="sxs-lookup"><span data-stu-id="4474d-326">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="4474d-327">Aby uzyskać więcej informacji, zobacz <xref:blazor/templated-components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="4474d-327">For more information, see <xref:blazor/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-razor-delegates"></a><span data-ttu-id="4474d-328">Delegaty Razor dla szablonu</span><span class="sxs-lookup"><span data-stu-id="4474d-328">Templated Razor delegates</span></span>

<span data-ttu-id="4474d-329">Szablony Razor umożliwiają zdefiniowanie fragmentu interfejsu użytkownika w następującym formacie:</span><span class="sxs-lookup"><span data-stu-id="4474d-329">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="4474d-330">Poniższy przykład ilustruje, jak określić delegata Razor dla szablonu jako <xref:System.Func%602> .</span><span class="sxs-lookup"><span data-stu-id="4474d-330">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="4474d-331">[Typ dynamiczny](/dotnet/csharp/programming-guide/types/using-type-dynamic) jest określony dla parametru metody, która jest hermetyzowana przez delegata.</span><span class="sxs-lookup"><span data-stu-id="4474d-331">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="4474d-332">[Typ obiektu](/dotnet/csharp/language-reference/keywords/object) jest określony jako wartość zwracana delegata.</span><span class="sxs-lookup"><span data-stu-id="4474d-332">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="4474d-333">Szablon jest używany z elementem <xref:System.Collections.Generic.List%601> `Pet` zawierającym `Name` Właściwość.</span><span class="sxs-lookup"><span data-stu-id="4474d-333">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="4474d-334">Szablon jest renderowany przy użyciu `pets` instrukcji dostarczonej przez `foreach` instrukcję:</span><span class="sxs-lookup"><span data-stu-id="4474d-334">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="4474d-335">Renderowane dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="4474d-335">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="4474d-336">Możesz również podać wbudowany szablon Razor jako argument metody.</span><span class="sxs-lookup"><span data-stu-id="4474d-336">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="4474d-337">W poniższym przykładzie `Repeat` Metoda otrzymuje szablon Razor.</span><span class="sxs-lookup"><span data-stu-id="4474d-337">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="4474d-338">Metoda używa szablonu do tworzenia zawartości HTML z powtarzanymi elementami podanymi z listy:</span><span class="sxs-lookup"><span data-stu-id="4474d-338">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="4474d-339">Korzystając z listy zwierząt domowych z poprzedniego przykładu, `Repeat` Metoda jest wywoływana z:</span><span class="sxs-lookup"><span data-stu-id="4474d-339">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="4474d-340"><xref:System.Collections.Generic.List%601>z programu `Pet` .</span><span class="sxs-lookup"><span data-stu-id="4474d-340"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="4474d-341">Liczba powtórzeń poszczególnych zwierząt domowych.</span><span class="sxs-lookup"><span data-stu-id="4474d-341">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="4474d-342">Wbudowany szablon do użycia dla elementów listy nieuporządkowanej listy.</span><span class="sxs-lookup"><span data-stu-id="4474d-342">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="4474d-343">Renderowane dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="4474d-343">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="4474d-344">Pomocnicy tagów</span><span class="sxs-lookup"><span data-stu-id="4474d-344">Tag Helpers</span></span>

<span data-ttu-id="4474d-345">*Ten scenariusz dotyczy tylko widoków MVC i Razor Pages (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="4474d-345">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="4474d-346">Istnieją trzy dyrektywy, które odnoszą się do [pomocników tagów](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="4474d-346">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="4474d-347">Dyrektywę</span><span class="sxs-lookup"><span data-stu-id="4474d-347">Directive</span></span> | <span data-ttu-id="4474d-348">Funkcja</span><span class="sxs-lookup"><span data-stu-id="4474d-348">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="4474d-349">Sprawia, że pomocnicy tagów są dostępni do widoku.</span><span class="sxs-lookup"><span data-stu-id="4474d-349">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="4474d-350">Usuwa pomocników tagów, które zostały wcześniej dodane z widoku.</span><span class="sxs-lookup"><span data-stu-id="4474d-350">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="4474d-351">Określa prefiks tagu w celu włączenia obsługi pomocnika tagów i zapewnienia jawnego użycia pomocnika tagów.</span><span class="sxs-lookup"><span data-stu-id="4474d-351">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a>Razor<span data-ttu-id="4474d-352">zastrzeżone słowa kluczowe</span><span class="sxs-lookup"><span data-stu-id="4474d-352"> reserved keywords</span></span>

### <a name="razor-keywords"></a>Razor<span data-ttu-id="4474d-353">służąc</span><span class="sxs-lookup"><span data-stu-id="4474d-353"> keywords</span></span>

* <span data-ttu-id="4474d-354">Strona (wymaga ASP.NET Core 2,1 lub nowszej)</span><span class="sxs-lookup"><span data-stu-id="4474d-354">page (Requires ASP.NET Core 2.1 or later)</span></span>
* <span data-ttu-id="4474d-355">namespace</span><span class="sxs-lookup"><span data-stu-id="4474d-355">namespace</span></span>
* <span data-ttu-id="4474d-356"> — funkcje</span><span class="sxs-lookup"><span data-stu-id="4474d-356">functions</span></span>
* <span data-ttu-id="4474d-357">inherit</span><span class="sxs-lookup"><span data-stu-id="4474d-357">inherits</span></span>
* <span data-ttu-id="4474d-358">model</span><span class="sxs-lookup"><span data-stu-id="4474d-358">model</span></span>
* <span data-ttu-id="4474d-359">section</span><span class="sxs-lookup"><span data-stu-id="4474d-359">section</span></span>
* <span data-ttu-id="4474d-360">Pomocnik (aktualnie nieobsługiwany przez ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="4474d-360">helper (Not currently supported by ASP.NET Core)</span></span>

Razor<span data-ttu-id="4474d-361">Słowa kluczowe są wyprowadzane z `@(Razor Keyword)` (na przykład `@(functions)` ).</span><span class="sxs-lookup"><span data-stu-id="4474d-361"> keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="4474d-362">RazorSłowa kluczowe języka C#</span><span class="sxs-lookup"><span data-stu-id="4474d-362">C# Razor keywords</span></span>

* <span data-ttu-id="4474d-363">case</span><span class="sxs-lookup"><span data-stu-id="4474d-363">case</span></span>
* <span data-ttu-id="4474d-364">do</span><span class="sxs-lookup"><span data-stu-id="4474d-364">do</span></span>
* <span data-ttu-id="4474d-365">default</span><span class="sxs-lookup"><span data-stu-id="4474d-365">default</span></span>
* <span data-ttu-id="4474d-366">dla</span><span class="sxs-lookup"><span data-stu-id="4474d-366">for</span></span>
* <span data-ttu-id="4474d-367">foreach</span><span class="sxs-lookup"><span data-stu-id="4474d-367">foreach</span></span>
* <span data-ttu-id="4474d-368">if</span><span class="sxs-lookup"><span data-stu-id="4474d-368">if</span></span>
* <span data-ttu-id="4474d-369">else</span><span class="sxs-lookup"><span data-stu-id="4474d-369">else</span></span>
* <span data-ttu-id="4474d-370">lock</span><span class="sxs-lookup"><span data-stu-id="4474d-370">lock</span></span>
* <span data-ttu-id="4474d-371">switch</span><span class="sxs-lookup"><span data-stu-id="4474d-371">switch</span></span>
* <span data-ttu-id="4474d-372">try</span><span class="sxs-lookup"><span data-stu-id="4474d-372">try</span></span>
* <span data-ttu-id="4474d-373">efektywn</span><span class="sxs-lookup"><span data-stu-id="4474d-373">catch</span></span>
* <span data-ttu-id="4474d-374">finally</span><span class="sxs-lookup"><span data-stu-id="4474d-374">finally</span></span>
* <span data-ttu-id="4474d-375">using</span><span class="sxs-lookup"><span data-stu-id="4474d-375">using</span></span>
* <span data-ttu-id="4474d-376">while</span><span class="sxs-lookup"><span data-stu-id="4474d-376">while</span></span>

<span data-ttu-id="4474d-377">RazorSłowa kluczowe języka C# muszą mieć podwójne ucieczki z `@(@C# Razor Keyword)` (na przykład `@(@case)` ).</span><span class="sxs-lookup"><span data-stu-id="4474d-377">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="4474d-378">Pierwszy `@` wyprowadza Razor Analizator.</span><span class="sxs-lookup"><span data-stu-id="4474d-378">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="4474d-379">Drugi `@` wyprowadza analizator składni języka C#.</span><span class="sxs-lookup"><span data-stu-id="4474d-379">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="4474d-380">Zastrzeżone słowa kluczowe, które nie są używane przezRazor</span><span class="sxs-lookup"><span data-stu-id="4474d-380">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="4474d-381">class</span><span class="sxs-lookup"><span data-stu-id="4474d-381">class</span></span>

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="4474d-382">Sprawdzanie Razor klasy języka C# wygenerowanej dla widoku</span><span class="sxs-lookup"><span data-stu-id="4474d-382">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="4474d-383">W zestaw .NET Core SDK 2,1 lub nowszej, [ Razor zestaw SDK](xref:razor-pages/sdk) obsługuje kompilację Razor plików.</span><span class="sxs-lookup"><span data-stu-id="4474d-383">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="4474d-384">Podczas kompilowania projektu Razor zestaw SDK generuje \*obj/<build_configuration>/<target_framework_moniker>/ Razor \* katalogu w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="4474d-384">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="4474d-385">Struktura katalogów w *Razor* katalogu odzwierciedla strukturę katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="4474d-385">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="4474d-386">Rozważmy następującą strukturę katalogów w projekcie ASP.NET Core stron 2,1 na Razor platformie .NET Core 2,1:</span><span class="sxs-lookup"><span data-stu-id="4474d-386">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

* <span data-ttu-id="4474d-387">**Obszarach**</span><span class="sxs-lookup"><span data-stu-id="4474d-387">**Areas/**</span></span>
  * <span data-ttu-id="4474d-388">**Administratora**</span><span class="sxs-lookup"><span data-stu-id="4474d-388">**Admin/**</span></span>
    * <span data-ttu-id="4474d-389">**Page**</span><span class="sxs-lookup"><span data-stu-id="4474d-389">**Pages/**</span></span>
      * <span data-ttu-id="4474d-390">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="4474d-390">*Index.cshtml*</span></span>
      * <span data-ttu-id="4474d-391">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="4474d-391">*Index.cshtml.cs*</span></span>
* <span data-ttu-id="4474d-392">**Page**</span><span class="sxs-lookup"><span data-stu-id="4474d-392">**Pages/**</span></span>
  * <span data-ttu-id="4474d-393">**Udostępniać**</span><span class="sxs-lookup"><span data-stu-id="4474d-393">**Shared/**</span></span>
    * <span data-ttu-id="4474d-394">*_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="4474d-394">*_Layout.cshtml*</span></span>
  * <span data-ttu-id="4474d-395">*_ViewImports. cshtml*</span><span class="sxs-lookup"><span data-stu-id="4474d-395">*_ViewImports.cshtml*</span></span>
  * <span data-ttu-id="4474d-396">*_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="4474d-396">*_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="4474d-397">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="4474d-397">*Index.cshtml*</span></span>
  * <span data-ttu-id="4474d-398">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="4474d-398">*Index.cshtml.cs*</span></span>

<span data-ttu-id="4474d-399">Kompilowanie projektu w konfiguracji *debugowania* powoduje zwrócenie następującego katalogu *obj* :</span><span class="sxs-lookup"><span data-stu-id="4474d-399">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

* <span data-ttu-id="4474d-400">**obiektów**</span><span class="sxs-lookup"><span data-stu-id="4474d-400">**obj/**</span></span>
  * <span data-ttu-id="4474d-401">**Rozpocząć**</span><span class="sxs-lookup"><span data-stu-id="4474d-401">**Debug/**</span></span>
    * <span data-ttu-id="4474d-402">**netcoreapp 2.1/**</span><span class="sxs-lookup"><span data-stu-id="4474d-402">**netcoreapp2.1/**</span></span>
      * **Razor/**
        * <span data-ttu-id="4474d-403">**Obszarach**</span><span class="sxs-lookup"><span data-stu-id="4474d-403">**Areas/**</span></span>
          * <span data-ttu-id="4474d-404">**Administratora**</span><span class="sxs-lookup"><span data-stu-id="4474d-404">**Admin/**</span></span>
            * <span data-ttu-id="4474d-405">**Page**</span><span class="sxs-lookup"><span data-stu-id="4474d-405">**Pages/**</span></span>
              * <span data-ttu-id="4474d-406">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="4474d-406">*Index.g.cshtml.cs*</span></span>
        * <span data-ttu-id="4474d-407">**Page**</span><span class="sxs-lookup"><span data-stu-id="4474d-407">**Pages/**</span></span>
          * <span data-ttu-id="4474d-408">**Udostępniać**</span><span class="sxs-lookup"><span data-stu-id="4474d-408">**Shared/**</span></span>
            * <span data-ttu-id="4474d-409">*_Layout. g. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="4474d-409">*_Layout.g.cshtml.cs*</span></span>
          * <span data-ttu-id="4474d-410">*_ViewImports. g. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="4474d-410">*_ViewImports.g.cshtml.cs*</span></span>
          * <span data-ttu-id="4474d-411">*_ViewStart. g. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="4474d-411">*_ViewStart.g.cshtml.cs*</span></span>
          * <span data-ttu-id="4474d-412">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="4474d-412">*Index.g.cshtml.cs*</span></span>

<span data-ttu-id="4474d-413">Aby wyświetlić wygenerowaną klasę dla *stron/index. cshtml*, Otwórz *obj/Debug/netcoreapp 2.1/ Razor /Pages/index.g.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="4474d-413">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="4474d-414">Dodaj następującą klasę do projektu ASP.NET Core MVC:</span><span class="sxs-lookup"><span data-stu-id="4474d-414">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="4474d-415">W programie `Startup.ConfigureServices` Zastąp wartość `RazorTemplateEngine` dodany przez MVC `CustomTemplateEngine` klasą:</span><span class="sxs-lookup"><span data-stu-id="4474d-415">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="4474d-416">Ustaw punkt przerwania na `return csharpDocument;` instrukcji `CustomTemplateEngine` .</span><span class="sxs-lookup"><span data-stu-id="4474d-416">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="4474d-417">Gdy wykonanie programu zatrzyma się w punkcie przerwania, Wyświetl wartość `generatedCode` .</span><span class="sxs-lookup"><span data-stu-id="4474d-417">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Widok wizualizatora tekstu generatedCode](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="4474d-419">Wyświetl wyszukiwania i rozróżnianie wielkości liter</span><span class="sxs-lookup"><span data-stu-id="4474d-419">View lookups and case sensitivity</span></span>

<span data-ttu-id="4474d-420">RazorAparat widoku wykonuje wyszukiwania z uwzględnieniem wielkości liter w widokach.</span><span class="sxs-lookup"><span data-stu-id="4474d-420">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="4474d-421">Jednak rzeczywiste wyszukiwanie jest określane przez podstawowy system plików:</span><span class="sxs-lookup"><span data-stu-id="4474d-421">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="4474d-422">Źródło na podstawie pliku:</span><span class="sxs-lookup"><span data-stu-id="4474d-422">File based source:</span></span>
  * <span data-ttu-id="4474d-423">W systemach operacyjnych z systemami plików bez uwzględniania wielkości liter (na przykład Windows) wyszukiwania dostawcy plików fizycznych nie uwzględniają wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="4474d-423">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="4474d-424">Na przykład `return View("Test")` wyniki są zgodne z */views/Home/test.cshtml*, */views/Home/test.cshtml*i innymi wariantami wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="4474d-424">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="4474d-425">W przypadku systemów plików z uwzględnieniem wielkości liter (na przykład Linux, OSX i with `EmbeddedFileProvider` ) Wyszukiwanie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="4474d-425">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="4474d-426">Na przykład, w odniesieniu do `return View("Test")` */views/Home/test.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4474d-426">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="4474d-427">Wstępnie skompilowane widoki: w przypadku ASP.NET Core 2,0 i nowszych wyszukiwanie wstępnie skompilowanych widoków nie uwzględnia wielkości liter we wszystkich systemach operacyjnych.</span><span class="sxs-lookup"><span data-stu-id="4474d-427">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="4474d-428">Zachowanie jest takie samo jak zachowanie dostawcy plików fizycznych w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="4474d-428">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="4474d-429">Jeśli dwa skompilowane widoki różnią się tylko wielkością liter, wynik wyszukiwania jest niejednoznaczny.</span><span class="sxs-lookup"><span data-stu-id="4474d-429">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="4474d-430">Deweloperzy są zachęcani do dopasowania wielkości liter w nazwach plików i katalogów do wielkości liter:</span><span class="sxs-lookup"><span data-stu-id="4474d-430">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="4474d-431">Nazwy obszaru, kontrolera i akcji.</span><span class="sxs-lookup"><span data-stu-id="4474d-431">Area, controller, and action names.</span></span>
* Razor<span data-ttu-id="4474d-432">Page.</span><span class="sxs-lookup"><span data-stu-id="4474d-432"> Pages.</span></span>

<span data-ttu-id="4474d-433">Przypadek dopasowania gwarantuje, że wdrożenia znajdą swoje widoki niezależnie od systemu plików.</span><span class="sxs-lookup"><span data-stu-id="4474d-433">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4474d-434">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="4474d-434">Additional resources</span></span>

<span data-ttu-id="4474d-435">[Wprowadzenie do programowania w sieci Web ASP.NET za pomocą Razor Składnia](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) zawiera wiele przykładów programowania z Razor składnią.</span><span class="sxs-lookup"><span data-stu-id="4474d-435">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
