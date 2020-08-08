---
title: odwołanie do składni Razor dla ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej o Razor składni znaczników na potrzeby osadzania kodu opartego na serwerze w stronach sieci Web.
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
uid: mvc/views/razor
ms.openlocfilehash: 4b418c53535965eae6b41f3297b0c6336fb993d5
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020590"
---
# <a name="no-locrazor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="a1740-103">Razorodwołanie do składni dla ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a1740-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="a1740-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)i [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="a1740-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="a1740-105">Razorjest składnią znaczników na potrzeby osadzania kodu opartego na serwerze w stronach sieci Web.</span><span class="sxs-lookup"><span data-stu-id="a1740-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="a1740-106">RazorSkładnia składa się z Razor znaczników, C# i HTML.</span><span class="sxs-lookup"><span data-stu-id="a1740-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="a1740-107">Pliki zawierające Razor generalnie mają rozszerzenie *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a1740-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="a1740-108">Razorznajduje się również w plikach [ Razor składników](xref:blazor/components/index) (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="a1740-108">Razor is also found in [Razor components](xref:blazor/components/index) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="a1740-109">Renderowanie HTML</span><span class="sxs-lookup"><span data-stu-id="a1740-109">Rendering HTML</span></span>

<span data-ttu-id="a1740-110">RazorJęzykiem domyślnym jest HTML.</span><span class="sxs-lookup"><span data-stu-id="a1740-110">The default Razor language is HTML.</span></span> <span data-ttu-id="a1740-111">Renderowanie kodu HTML z Razor znaczników nie różni się od renderowania kodu HTML z pliku HTML.</span><span class="sxs-lookup"><span data-stu-id="a1740-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="a1740-112">Znaczniki HTML w plikach *. cshtml* Razor są renderowane przez serwer bez zmian.</span><span class="sxs-lookup"><span data-stu-id="a1740-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="no-locrazor-syntax"></a><span data-ttu-id="a1740-113">Razorobowiązuje</span><span class="sxs-lookup"><span data-stu-id="a1740-113">Razor syntax</span></span>

<span data-ttu-id="a1740-114">Razorobsługuje język C# i używa `@` symbolu do przejścia z HTML do języka c#.</span><span class="sxs-lookup"><span data-stu-id="a1740-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="a1740-115">Razoroblicza wyrażenia języka C# i renderuje je w danych wyjściowych w formacie HTML.</span><span class="sxs-lookup"><span data-stu-id="a1740-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="a1740-116">Gdy `@` po symbolu następuje [ Razor zastrzeżone słowo kluczowe](#razor-reserved-keywords), przechodzi do Razor specyficznego znacznika.</span><span class="sxs-lookup"><span data-stu-id="a1740-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="a1740-117">W przeciwnym razie przechodzi do zwykłego języka C#.</span><span class="sxs-lookup"><span data-stu-id="a1740-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="a1740-118">Aby wypróbować `@` symbol w Razor znaczniku, użyj drugiego `@` symbolu:</span><span class="sxs-lookup"><span data-stu-id="a1740-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="a1740-119">Kod jest renderowany w języku HTML z pojedynczym `@` symbolem:</span><span class="sxs-lookup"><span data-stu-id="a1740-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="a1740-120">Atrybuty i zawartość HTML zawierające adresy e-mail nie traktują `@` symbolu jako znaku przejścia.</span><span class="sxs-lookup"><span data-stu-id="a1740-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="a1740-121">W poniższym przykładzie adresy e-mail są nienaruszone, Razor Analiza:</span><span class="sxs-lookup"><span data-stu-id="a1740-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-no-locrazor-expressions"></a><span data-ttu-id="a1740-122">Wyrażenia niejawne Razor</span><span class="sxs-lookup"><span data-stu-id="a1740-122">Implicit Razor expressions</span></span>

<span data-ttu-id="a1740-123">Wyrażenia niejawne Razor zaczynają się od `@` kodu C#:</span><span class="sxs-lookup"><span data-stu-id="a1740-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="a1740-124">Z wyjątkiem `await` słowa kluczowego języka C# wyrażenia niejawne nie mogą zawierać spacji.</span><span class="sxs-lookup"><span data-stu-id="a1740-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="a1740-125">Jeśli instrukcja języka C# ma jasne zakończenie, spacje mogą być wymieszaniu:</span><span class="sxs-lookup"><span data-stu-id="a1740-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="a1740-126">Wyrażenia niejawne **nie mogą** zawierać typów ogólnych języka C#, ponieważ znaki wewnątrz nawiasów ( `<>` ) są interpretowane jako tag HTML.</span><span class="sxs-lookup"><span data-stu-id="a1740-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="a1740-127">Następujący **kod jest nieprawidłowy** :</span><span class="sxs-lookup"><span data-stu-id="a1740-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="a1740-128">Poprzedni kod generuje błąd kompilatora podobny do jednego z następujących:</span><span class="sxs-lookup"><span data-stu-id="a1740-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="a1740-129">Element "int" nie został zamknięty.</span><span class="sxs-lookup"><span data-stu-id="a1740-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="a1740-130">Wszystkie elementy muszą być albo zamykane samodzielnie lub mieć pasujący tag końcowy.</span><span class="sxs-lookup"><span data-stu-id="a1740-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="a1740-131">Nie można przekonwertować grupy metod "GenericMethod" na typ inny niż delegatd "Object".</span><span class="sxs-lookup"><span data-stu-id="a1740-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="a1740-132">Czy chodziło o wywołanie metody?</span><span class="sxs-lookup"><span data-stu-id="a1740-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="a1740-133">Wywołania metody ogólnej muszą być opakowane w [jawne Razor wyrażenie](#explicit-razor-expressions) lub [ Razor blok kodu](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="a1740-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-no-locrazor-expressions"></a><span data-ttu-id="a1740-134">RazorWyrażenia jawne</span><span class="sxs-lookup"><span data-stu-id="a1740-134">Explicit Razor expressions</span></span>

<span data-ttu-id="a1740-135">RazorWyrażenia jawne składają się z `@` symbolu z wyrównanym nawiasem.</span><span class="sxs-lookup"><span data-stu-id="a1740-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="a1740-136">W celu renderowania czasu ostatniego tygodnia Razor używane są następujące znaczniki:</span><span class="sxs-lookup"><span data-stu-id="a1740-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="a1740-137">Każda zawartość w `@()` nawiasie zostanie oceniona i przerenderowana do danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="a1740-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="a1740-138">Wyrażenia niejawne, opisane w poprzedniej sekcji, zazwyczaj nie mogą zawierać spacji.</span><span class="sxs-lookup"><span data-stu-id="a1740-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="a1740-139">W poniższym kodzie tydzień nie jest odejmowany od bieżącego czasu:</span><span class="sxs-lookup"><span data-stu-id="a1740-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="a1740-140">Kod renderuje następujący HTML:</span><span class="sxs-lookup"><span data-stu-id="a1740-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="a1740-141">Wyrażenia jawne mogą służyć do łączenia tekstu z wynikiem wyrażenia:</span><span class="sxs-lookup"><span data-stu-id="a1740-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="a1740-142">Bez wyrażenia jawnego `<p>Age@joe.Age</p>` jest traktowany jako adres e-mail i `<p>Age@joe.Age</p>` jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="a1740-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="a1740-143">Po zapisaniu jako jawne wyrażenie `<p>Age33</p>` jest renderowane.</span><span class="sxs-lookup"><span data-stu-id="a1740-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="a1740-144">Wyrażenia jawne mogą służyć do renderowania danych wyjściowych z metod ogólnych w plikach *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a1740-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="a1740-145">W poniższym znaczniku pokazano, jak poprawić przedstawiony wcześniej błąd w nawiasach ogólnych języka C#.</span><span class="sxs-lookup"><span data-stu-id="a1740-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="a1740-146">Kod jest zapisywana jako jawne wyrażenie:</span><span class="sxs-lookup"><span data-stu-id="a1740-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="a1740-147">Kodowanie wyrażeń</span><span class="sxs-lookup"><span data-stu-id="a1740-147">Expression encoding</span></span>

<span data-ttu-id="a1740-148">Wyrażenia języka C#, które są obliczane jako ciąg, są kodowane w formacie HTML.</span><span class="sxs-lookup"><span data-stu-id="a1740-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="a1740-149">Wyrażenia języka C#, które mają być `IHtmlContent` renderowane bezpośrednio przez `IHtmlContent.WriteTo` .</span><span class="sxs-lookup"><span data-stu-id="a1740-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="a1740-150">Wyrażenia języka C#, które nie są szacowane do, `IHtmlContent` są konwertowane na ciąg przez `ToString` i kodowane przed ich renderowaniem.</span><span class="sxs-lookup"><span data-stu-id="a1740-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="a1740-151">Kod renderuje następujący HTML:</span><span class="sxs-lookup"><span data-stu-id="a1740-151">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="a1740-152">KOD HTML jest wyświetlany w przeglądarce jako:</span><span class="sxs-lookup"><span data-stu-id="a1740-152">The HTML is shown in the browser as:</span></span>

```html
<span>Hello World</span>
```

<span data-ttu-id="a1740-153">`HtmlHelper.Raw`dane wyjściowe nie są kodowane, ale renderowane jako znaczniki HTML.</span><span class="sxs-lookup"><span data-stu-id="a1740-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="a1740-154">Korzystanie `HtmlHelper.Raw` z nieoczyszczonych danych wejściowych użytkownika stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="a1740-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="a1740-155">Dane wejściowe użytkownika mogą zawierać złośliwe skrypty JavaScript lub inne luki w zabezpieczeniach.</span><span class="sxs-lookup"><span data-stu-id="a1740-155">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="a1740-156">Oczyszczanie danych wejściowych użytkownika jest trudne.</span><span class="sxs-lookup"><span data-stu-id="a1740-156">Sanitizing user input is difficult.</span></span> <span data-ttu-id="a1740-157">Unikaj używania `HtmlHelper.Raw` z danymi wejściowymi użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a1740-157">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="a1740-158">Kod renderuje następujący HTML:</span><span class="sxs-lookup"><span data-stu-id="a1740-158">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="no-locrazor-code-blocks"></a><span data-ttu-id="a1740-159">Razorbloki kodu</span><span class="sxs-lookup"><span data-stu-id="a1740-159">Razor code blocks</span></span>

<span data-ttu-id="a1740-160">Razorbloki kodu zaczynają się od `@` i są ujęte w nawiasy `{}` .</span><span class="sxs-lookup"><span data-stu-id="a1740-160">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="a1740-161">W przeciwieństwie do wyrażeń, kod C# wewnątrz bloków kodu nie jest renderowany.</span><span class="sxs-lookup"><span data-stu-id="a1740-161">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="a1740-162">Bloki kodu i wyrażenia w widoku mają ten sam zakres i są zdefiniowane w kolejności:</span><span class="sxs-lookup"><span data-stu-id="a1740-162">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="a1740-163">Kod renderuje następujący HTML:</span><span class="sxs-lookup"><span data-stu-id="a1740-163">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a1740-164">W blokach kodu Zadeklaruj [funkcje lokalne](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) z adiustacją, aby zapewnić metody tworzenia szablonów:</span><span class="sxs-lookup"><span data-stu-id="a1740-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

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

<span data-ttu-id="a1740-165">Kod renderuje następujący HTML:</span><span class="sxs-lookup"><span data-stu-id="a1740-165">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="a1740-166">Przejścia niejawne</span><span class="sxs-lookup"><span data-stu-id="a1740-166">Implicit transitions</span></span>

<span data-ttu-id="a1740-167">Językiem domyślnym w bloku kodu jest C#, ale Razor strona może przejść z powrotem do HTML:</span><span class="sxs-lookup"><span data-stu-id="a1740-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="a1740-168">Jawne ograniczone przechodzenie</span><span class="sxs-lookup"><span data-stu-id="a1740-168">Explicit delimited transition</span></span>

<span data-ttu-id="a1740-169">Aby zdefiniować podsekcję bloku kodu, który powinien renderować HTML, należy ująć znaki do renderowania za pomocą Razor `<text>` tagu:</span><span class="sxs-lookup"><span data-stu-id="a1740-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="a1740-170">To podejście służy do renderowania kodu HTML, który nie jest ujęty w tag HTML.</span><span class="sxs-lookup"><span data-stu-id="a1740-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="a1740-171">Bez tagu HTML lub Razor tag wystąpi Razor błąd w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="a1740-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="a1740-172">`<text>`Tag jest przydatny do kontrolowania odstępów podczas renderowania zawartości:</span><span class="sxs-lookup"><span data-stu-id="a1740-172">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="a1740-173">Tylko zawartość między `<text>` tagiem jest renderowana.</span><span class="sxs-lookup"><span data-stu-id="a1740-173">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="a1740-174">W danych wyjściowych HTML nie ma odstępów przed tagiem ani po nim `<text>` .</span><span class="sxs-lookup"><span data-stu-id="a1740-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="a1740-175">Jawne przejście liniowe</span><span class="sxs-lookup"><span data-stu-id="a1740-175">Explicit line transition</span></span>

<span data-ttu-id="a1740-176">Aby renderować resztę całego wiersza jako HTML wewnątrz bloku kodu, użyj `@:` składni:</span><span class="sxs-lookup"><span data-stu-id="a1740-176">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="a1740-177">Bez `@:` kodu, Razor generowany jest błąd czasu wykonywania.</span><span class="sxs-lookup"><span data-stu-id="a1740-177">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="a1740-178">Dodatkowe `@` znaki w Razor pliku mogą spowodować błędy kompilatora w instrukcjach znajdujących się w dalszej części bloku.</span><span class="sxs-lookup"><span data-stu-id="a1740-178">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="a1740-179">Te błędy kompilatora mogą być trudne do zrozumienia, ponieważ rzeczywisty błąd występuje przed zgłoszonym błędem.</span><span class="sxs-lookup"><span data-stu-id="a1740-179">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="a1740-180">Ten błąd jest typowy po połączeniu wielu wyrażeń jawnych/jawnych w jeden blok kodu.</span><span class="sxs-lookup"><span data-stu-id="a1740-180">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="a1740-181">Struktury formantów</span><span class="sxs-lookup"><span data-stu-id="a1740-181">Control structures</span></span>

<span data-ttu-id="a1740-182">Struktury formantów są rozszerzeniem bloków kodu.</span><span class="sxs-lookup"><span data-stu-id="a1740-182">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="a1740-183">Wszystkie aspekty bloków kodu (przechodzenie do znaczników, wbudowane C#) również mają zastosowanie do następujących struktur:</span><span class="sxs-lookup"><span data-stu-id="a1740-183">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="a1740-184">Warunki`@if, else if, else, and @switch`</span><span class="sxs-lookup"><span data-stu-id="a1740-184">Conditionals `@if, else if, else, and @switch`</span></span>

<span data-ttu-id="a1740-185">`@if`kontroluje, kiedy kod jest uruchamiany:</span><span class="sxs-lookup"><span data-stu-id="a1740-185">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="a1740-186">`else`i `else if` nie wymaga `@` symbolu:</span><span class="sxs-lookup"><span data-stu-id="a1740-186">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="a1740-187">W poniższym znaczniku pokazano, jak używać instrukcji switch:</span><span class="sxs-lookup"><span data-stu-id="a1740-187">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="a1740-188">Pętli`@for, @foreach, @while, and @do while`</span><span class="sxs-lookup"><span data-stu-id="a1740-188">Looping `@for, @foreach, @while, and @do while`</span></span>

<span data-ttu-id="a1740-189">KOD HTML z szablonem może być renderowany przy użyciu instrukcji sterowania pętlą.</span><span class="sxs-lookup"><span data-stu-id="a1740-189">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="a1740-190">Aby renderować listę osób:</span><span class="sxs-lookup"><span data-stu-id="a1740-190">To render a list of people:</span></span>

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

<span data-ttu-id="a1740-191">Obsługiwane są następujące instrukcje pętli:</span><span class="sxs-lookup"><span data-stu-id="a1740-191">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="a1740-192">Złożonego`@using`</span><span class="sxs-lookup"><span data-stu-id="a1740-192">Compound `@using`</span></span>

<span data-ttu-id="a1740-193">W języku C# `using` instrukcja jest używana w celu upewnienia się, że obiekt został usunięty.</span><span class="sxs-lookup"><span data-stu-id="a1740-193">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="a1740-194">W programie Razor ten sam mechanizm jest używany do tworzenia pomocników HTML, które zawierają dodatkową zawartość.</span><span class="sxs-lookup"><span data-stu-id="a1740-194">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="a1740-195">W poniższym kodzie, pomocników HTML renderuje `<form>` tag przy użyciu `@using` instrukcji:</span><span class="sxs-lookup"><span data-stu-id="a1740-195">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

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

<span data-ttu-id="a1740-196">Obsługa wyjątków jest podobna do języka C#:</span><span class="sxs-lookup"><span data-stu-id="a1740-196">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

<span data-ttu-id="a1740-197">Razorma możliwość ochrony sekcji krytycznych przy użyciu instrukcji lock:</span><span class="sxs-lookup"><span data-stu-id="a1740-197">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="a1740-198">Komentarze</span><span class="sxs-lookup"><span data-stu-id="a1740-198">Comments</span></span>

<span data-ttu-id="a1740-199">Razorobsługuje komentarze w językach C# i HTML:</span><span class="sxs-lookup"><span data-stu-id="a1740-199">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="a1740-200">Kod renderuje następujący HTML:</span><span class="sxs-lookup"><span data-stu-id="a1740-200">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="a1740-201">RazorKomentarze są usuwane przez serwer przed renderowaniem strony sieci Web.</span><span class="sxs-lookup"><span data-stu-id="a1740-201">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="a1740-202">Razorużywa `@*  *@` do ograniczania komentarzy.</span><span class="sxs-lookup"><span data-stu-id="a1740-202">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="a1740-203">Poniższy kod jest oznaczony jako komentarz, więc serwer nie renderuje żadnego znacznika:</span><span class="sxs-lookup"><span data-stu-id="a1740-203">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="a1740-204">Dyrektyw</span><span class="sxs-lookup"><span data-stu-id="a1740-204">Directives</span></span>

<span data-ttu-id="a1740-205">Razordyrektywy są reprezentowane przez niejawne wyrażenia z zastrzeżonymi słowami kluczowymi po `@` symbolu.</span><span class="sxs-lookup"><span data-stu-id="a1740-205">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="a1740-206">Dyrektywa zwykle zmienia sposób analizowania widoku lub umożliwia wykonywanie różnych funkcji.</span><span class="sxs-lookup"><span data-stu-id="a1740-206">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="a1740-207">Zrozumienie, jak program Razor generuje kod dla widoku ułatwia zrozumienie, jak działają dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="a1740-207">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="a1740-208">Kod generuje klasę podobną do następującej:</span><span class="sxs-lookup"><span data-stu-id="a1740-208">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="a1740-209">W dalszej części tego artykułu w sekcji [Badanie Razor klasy języka C# wygenerowanej dla widoku](#inspect-the-razor-c-class-generated-for-a-view) wyjaśniono sposób wyświetlania tej wygenerowanej klasy.</span><span class="sxs-lookup"><span data-stu-id="a1740-209">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### `@attribute`

<span data-ttu-id="a1740-210">`@attribute`Dyrektywa dodaje dany atrybut do klasy wygenerowanej strony lub widoku.</span><span class="sxs-lookup"><span data-stu-id="a1740-210">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="a1740-211">Poniższy przykład dodaje `[Authorize]` atrybut:</span><span class="sxs-lookup"><span data-stu-id="a1740-211">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

<span data-ttu-id="a1740-212">*Ten scenariusz dotyczy tylko Razor składników (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="a1740-212">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="a1740-213">`@code`Blok umożliwia [ Razor składnikowi](xref:blazor/components/index) Dodawanie elementów członkowskich języka C# (pól, właściwości i metod) do składnika:</span><span class="sxs-lookup"><span data-stu-id="a1740-213">The `@code` block enables a [Razor component](xref:blazor/components/index) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="a1740-214">W przypadku Razor składników `@code` jest to alias [`@functions`](#functions) i zalecane `@functions` .</span><span class="sxs-lookup"><span data-stu-id="a1740-214">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="a1740-215">Dozwolony jest więcej niż jeden `@code` blok.</span><span class="sxs-lookup"><span data-stu-id="a1740-215">More than one `@code` block is permissible.</span></span>

::: moniker-end

### `@functions`

<span data-ttu-id="a1740-216">`@functions`Dyrektywa umożliwia dodawanie elementów członkowskich C# (pól, właściwości i metod) do wygenerowanej klasy:</span><span class="sxs-lookup"><span data-stu-id="a1740-216">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a1740-217">W obszarze [ Razor składniki](xref:blazor/components/index)Użyj `@code` , `@functions` Aby dodać elementy członkowskie języka C#.</span><span class="sxs-lookup"><span data-stu-id="a1740-217">In [Razor components](xref:blazor/components/index), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="a1740-218">Przykład:</span><span class="sxs-lookup"><span data-stu-id="a1740-218">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="a1740-219">Kod generuje następujący znacznik HTML:</span><span class="sxs-lookup"><span data-stu-id="a1740-219">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="a1740-220">Następujący kod jest wygenerowaną Razor klasą języka C#:</span><span class="sxs-lookup"><span data-stu-id="a1740-220">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a1740-221">`@functions`metody służą jako metody tworzenia szablonów, gdy mają znaczniki:</span><span class="sxs-lookup"><span data-stu-id="a1740-221">`@functions` methods serve as templating methods when they have markup:</span></span>

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

<span data-ttu-id="a1740-222">Kod renderuje następujący HTML:</span><span class="sxs-lookup"><span data-stu-id="a1740-222">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

<span data-ttu-id="a1740-223">`@implements`Dyrektywa implementuje interfejs dla wygenerowanej klasy.</span><span class="sxs-lookup"><span data-stu-id="a1740-223">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="a1740-224">Poniższy przykład implementuje, <xref:System.IDisposable?displayProperty=fullName> Aby <xref:System.IDisposable.Dispose*> można było wywołać metodę:</span><span class="sxs-lookup"><span data-stu-id="a1740-224">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

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

<span data-ttu-id="a1740-225">`@inherits`Dyrektywa zapewnia pełną kontrolę nad klasą, którą dziedziczy widok:</span><span class="sxs-lookup"><span data-stu-id="a1740-225">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="a1740-226">Następujący kod jest niestandardowym Razor typem strony:</span><span class="sxs-lookup"><span data-stu-id="a1740-226">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="a1740-227">`CustomText`Zostanie wyświetlony w widoku:</span><span class="sxs-lookup"><span data-stu-id="a1740-227">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="a1740-228">Kod renderuje następujący HTML:</span><span class="sxs-lookup"><span data-stu-id="a1740-228">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="a1740-229">`@model`i `@inherits` mogą być używane w tym samym widoku.</span><span class="sxs-lookup"><span data-stu-id="a1740-229">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="a1740-230">`@inherits`może znajdować się w pliku *_ViewImports. cshtml* , który został zaimportowany przez widok:</span><span class="sxs-lookup"><span data-stu-id="a1740-230">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="a1740-231">Poniższy kod stanowi przykład widoku o jednoznacznie określonym typie:</span><span class="sxs-lookup"><span data-stu-id="a1740-231">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="a1740-232">W przypadku rick@contoso.com przekazywania "" w modelu widok generuje następujący znacznik HTML:</span><span class="sxs-lookup"><span data-stu-id="a1740-232">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

<span data-ttu-id="a1740-233">`@inject`Dyrektywa umożliwia Razor stronie dodanie usługi z [kontenera usługi](xref:fundamentals/dependency-injection) do widoku.</span><span class="sxs-lookup"><span data-stu-id="a1740-233">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="a1740-234">Aby uzyskać więcej informacji, zobacz [iniekcja zależności w widokach](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a1740-234">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### `@layout`

<span data-ttu-id="a1740-235">*Ten scenariusz dotyczy tylko Razor składników (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="a1740-235">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="a1740-236">`@layout`Dyrektywa określa układ Razor składnika.</span><span class="sxs-lookup"><span data-stu-id="a1740-236">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="a1740-237">Składniki układu są używane do uniknięcia duplikowania kodu i niespójności.</span><span class="sxs-lookup"><span data-stu-id="a1740-237">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="a1740-238">Aby uzyskać więcej informacji, zobacz <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="a1740-238">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### `@model`

<span data-ttu-id="a1740-239">*Ten scenariusz ma zastosowanie tylko do widoków i Razor stron MVC (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="a1740-239">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="a1740-240">`@model`Dyrektywa określa typ modelu przekazaną do widoku lub strony:</span><span class="sxs-lookup"><span data-stu-id="a1740-240">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="a1740-241">W aplikacji ASP.NET Core MVC lub Razor stronach utworzonych przy użyciu poszczególnych kont użytkowników, *widoki/konta/login. cshtml* zawierają następującą deklarację modelu:</span><span class="sxs-lookup"><span data-stu-id="a1740-241">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="a1740-242">Wygenerowana Klasa dziedziczy po `RazorPage<dynamic>` :</span><span class="sxs-lookup"><span data-stu-id="a1740-242">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="a1740-243">Razoruwidacznia `Model` Właściwość do uzyskiwania dostępu do modelu przekazaną do widoku:</span><span class="sxs-lookup"><span data-stu-id="a1740-243">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="a1740-244">`@model`Dyrektywa określa typ `Model` właściwości.</span><span class="sxs-lookup"><span data-stu-id="a1740-244">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="a1740-245">Dyrektywa określa, `T` w `RazorPage<T>` którym wygenerowanej klasie, z której pochodzi widok.</span><span class="sxs-lookup"><span data-stu-id="a1740-245">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="a1740-246">Jeśli `@model` dyrektywa nie jest określona, `Model` Właściwość jest typu `dynamic` .</span><span class="sxs-lookup"><span data-stu-id="a1740-246">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="a1740-247">Aby uzyskać więcej informacji, zobacz [silnie wpisane modele i @model słowo kluczowe](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="a1740-247">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### `@namespace`

<span data-ttu-id="a1740-248">`@namespace`Dyrektywa:</span><span class="sxs-lookup"><span data-stu-id="a1740-248">The `@namespace` directive:</span></span>

* <span data-ttu-id="a1740-249">Ustawia przestrzeń nazw klasy wygenerowanej Razor strony, widoku MVC lub Razor składnika.</span><span class="sxs-lookup"><span data-stu-id="a1740-249">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="a1740-250">Ustawia główne pochodne przestrzenie nazw stron, widoków lub klas składników z najbliższego pliku importów w drzewie katalogów, *_ViewImports. cshtml* (widoki lub strony) lub *_Imports. Razor* ( Razor składniki).</span><span class="sxs-lookup"><span data-stu-id="a1740-250">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="a1740-251">Dla Razor przykładu stron przedstawionych w poniższej tabeli:</span><span class="sxs-lookup"><span data-stu-id="a1740-251">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="a1740-252">Każda Strona importuje *strony/_ViewImports. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a1740-252">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="a1740-253">*Strona/_ViewImports. cshtml* zawiera `@namespace Hello.World` .</span><span class="sxs-lookup"><span data-stu-id="a1740-253">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="a1740-254">Każda Strona ma `Hello.World` jako element główny przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="a1740-254">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="a1740-255">Strona</span><span class="sxs-lookup"><span data-stu-id="a1740-255">Page</span></span>                                        | <span data-ttu-id="a1740-256">Przestrzeń nazw</span><span class="sxs-lookup"><span data-stu-id="a1740-256">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="a1740-257">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="a1740-257">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="a1740-258">*Pages/MorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="a1740-258">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="a1740-259">*Pages/MorePages/EvenMorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="a1740-259">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="a1740-260">Powyższe relacje mają zastosowanie do plików importu używanych ze składnikami i widokami MVC Razor .</span><span class="sxs-lookup"><span data-stu-id="a1740-260">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="a1740-261">Gdy wiele plików importu ma `@namespace` dyrektywę, plik znajdujący się najbliżej strony, widoku lub składnika w drzewie katalogów jest używany do ustawiania głównej przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="a1740-261">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="a1740-262">Jeśli folder *EvenMorePages* w poprzednim przykładzie ma plik Imports z plikiem `@namespace Another.Planet` (lub *strony/MorePages/EvenMorePages/Page. cshtml* zawiera `@namespace Another.Planet` ), wynik zostanie przedstawiony w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="a1740-262">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="a1740-263">Strona</span><span class="sxs-lookup"><span data-stu-id="a1740-263">Page</span></span>                                        | <span data-ttu-id="a1740-264">Przestrzeń nazw</span><span class="sxs-lookup"><span data-stu-id="a1740-264">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="a1740-265">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="a1740-265">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="a1740-266">*Pages/MorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="a1740-266">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="a1740-267">*Pages/MorePages/EvenMorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="a1740-267">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a1740-268">`@page`Dyrektywa ma różne skutki w zależności od typu pliku, w którym występuje.</span><span class="sxs-lookup"><span data-stu-id="a1740-268">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="a1740-269">Dyrektywa:</span><span class="sxs-lookup"><span data-stu-id="a1740-269">The directive:</span></span>

* <span data-ttu-id="a1740-270">W pliku *. cshtml* wskazuje, że plik jest Razor stroną.</span><span class="sxs-lookup"><span data-stu-id="a1740-270">In in a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="a1740-271">Aby uzyskać więcej informacji, zobacz [trasy niestandardowe](xref:razor-pages/index#custom-routes) i <xref:razor-pages/index> .</span><span class="sxs-lookup"><span data-stu-id="a1740-271">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="a1740-272">Określa, że Razor składnik powinien obsługiwać żądania bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="a1740-272">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="a1740-273">Aby uzyskać więcej informacji, zobacz <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="a1740-273">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a1740-274">`@page`Dyrektywa w pierwszym wierszu pliku *. cshtml* wskazuje, że plik jest Razor stroną.</span><span class="sxs-lookup"><span data-stu-id="a1740-274">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="a1740-275">Aby uzyskać więcej informacji, zobacz <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="a1740-275">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### `@section`

<span data-ttu-id="a1740-276">*Ten scenariusz ma zastosowanie tylko do widoków i Razor stron MVC (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="a1740-276">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="a1740-277">`@section`Dyrektywa jest używana w połączeniu z [ Razor układami MVC i Pages](xref:mvc/views/layout) , aby umożliwić widokom i stronom renderowanie zawartości w różnych częściach strony html.</span><span class="sxs-lookup"><span data-stu-id="a1740-277">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="a1740-278">Aby uzyskać więcej informacji, zobacz <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="a1740-278">For more information, see <xref:mvc/views/layout>.</span></span>

### `@using`

<span data-ttu-id="a1740-279">`@using`Dyrektywa dodaje `using` dyrektywę C# do wygenerowanego widoku:</span><span class="sxs-lookup"><span data-stu-id="a1740-279">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a1740-280">W [ Razor składniku składniki](xref:blazor/components/index), `@using` również kontroluje, które składniki znajdują się w zakresie.</span><span class="sxs-lookup"><span data-stu-id="a1740-280">In [Razor components](xref:blazor/components/index), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="a1740-281">Atrybuty dyrektywy</span><span class="sxs-lookup"><span data-stu-id="a1740-281">Directive attributes</span></span>

<span data-ttu-id="a1740-282">Razoratrybuty dyrektywy są reprezentowane przez niejawne wyrażenia z zastrzeżonymi słowami kluczowymi po `@` symbolu.</span><span class="sxs-lookup"><span data-stu-id="a1740-282">Razor directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="a1740-283">Atrybut dyrektywy zazwyczaj zmienia sposób analizowania elementu lub umożliwia korzystanie z różnych funkcji.</span><span class="sxs-lookup"><span data-stu-id="a1740-283">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### `@attributes`

<span data-ttu-id="a1740-284">*Ten scenariusz dotyczy tylko Razor składników (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="a1740-284">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="a1740-285">`@attributes`zezwala składnikowi na renderowanie niezadeklarowanych atrybutów.</span><span class="sxs-lookup"><span data-stu-id="a1740-285">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="a1740-286">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="a1740-286">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

### `@bind`

<span data-ttu-id="a1740-287">*Ten scenariusz dotyczy tylko Razor składników (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="a1740-287">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="a1740-288">Powiązanie danych w składnikach jest realizowane przy użyciu `@bind` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="a1740-288">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="a1740-289">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/data-binding>.</span><span class="sxs-lookup"><span data-stu-id="a1740-289">For more information, see <xref:blazor/components/data-binding>.</span></span>

### `@on{EVENT}`

<span data-ttu-id="a1740-290">*Ten scenariusz dotyczy tylko Razor składników (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="a1740-290">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="a1740-291">Razorudostępnia funkcje obsługi zdarzeń dla składników programu.</span><span class="sxs-lookup"><span data-stu-id="a1740-291">Razor provides event handling features for components.</span></span> <span data-ttu-id="a1740-292">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="a1740-292">For more information, see <xref:blazor/components/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

<span data-ttu-id="a1740-293">*Ten scenariusz dotyczy tylko Razor składników (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="a1740-293">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="a1740-294">Zapobiega domyślnej akcji dla zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="a1740-294">Prevents the default action for the event.</span></span>

### `@on{EVENT}:stopPropagation`

<span data-ttu-id="a1740-295">*Ten scenariusz dotyczy tylko Razor składników (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="a1740-295">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="a1740-296">Powoduje zatrzymanie propagacji zdarzenia dla zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="a1740-296">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

<span data-ttu-id="a1740-297">*Ten scenariusz dotyczy tylko Razor składników (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="a1740-297">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="a1740-298">`@key`Atrybut dyrektywy powoduje, że algorytmy porównujące składniki, aby zagwarantować zachowywanie elementów lub składników na podstawie wartości klucza.</span><span class="sxs-lookup"><span data-stu-id="a1740-298">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="a1740-299">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="a1740-299">For more information, see <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### `@ref`

<span data-ttu-id="a1740-300">*Ten scenariusz dotyczy tylko Razor składników (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="a1740-300">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="a1740-301">Odwołania do składników ( `@ref` ) umożliwiają odwoływanie się do wystąpienia składnika, dzięki czemu można wydać polecenia do tego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="a1740-301">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="a1740-302">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/index#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="a1740-302">For more information, see <xref:blazor/components/index#capture-references-to-components>.</span></span>

### `@typeparam`

<span data-ttu-id="a1740-303">*Ten scenariusz dotyczy tylko Razor składników (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="a1740-303">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="a1740-304">`@typeparam`Dyrektywa deklaruje parametr typu ogólnego dla wygenerowanej klasy składnika.</span><span class="sxs-lookup"><span data-stu-id="a1740-304">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="a1740-305">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/templated-components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="a1740-305">For more information, see <xref:blazor/components/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-no-locrazor-delegates"></a><span data-ttu-id="a1740-306">Delegaty z szablonami Razor</span><span class="sxs-lookup"><span data-stu-id="a1740-306">Templated Razor delegates</span></span>

<span data-ttu-id="a1740-307">RazorSzablony umożliwiają zdefiniowanie fragmentu interfejsu użytkownika w następującym formacie:</span><span class="sxs-lookup"><span data-stu-id="a1740-307">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="a1740-308">Poniższy przykład ilustruje sposób określania delegata z szablonem Razor jako <xref:System.Func%602> .</span><span class="sxs-lookup"><span data-stu-id="a1740-308">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="a1740-309">[Typ dynamiczny](/dotnet/csharp/programming-guide/types/using-type-dynamic) jest określony dla parametru metody, która jest hermetyzowana przez delegata.</span><span class="sxs-lookup"><span data-stu-id="a1740-309">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="a1740-310">[Typ obiektu](/dotnet/csharp/language-reference/keywords/object) jest określony jako wartość zwracana delegata.</span><span class="sxs-lookup"><span data-stu-id="a1740-310">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="a1740-311">Szablon jest używany z elementem <xref:System.Collections.Generic.List%601> `Pet` zawierającym `Name` Właściwość.</span><span class="sxs-lookup"><span data-stu-id="a1740-311">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="a1740-312">Szablon jest renderowany przy użyciu `pets` instrukcji dostarczonej przez `foreach` instrukcję:</span><span class="sxs-lookup"><span data-stu-id="a1740-312">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="a1740-313">Renderowane dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="a1740-313">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="a1740-314">Możesz również podać Razor szablon wbudowany jako argument metody.</span><span class="sxs-lookup"><span data-stu-id="a1740-314">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="a1740-315">W poniższym przykładzie `Repeat` Metoda otrzymuje Razor szablon.</span><span class="sxs-lookup"><span data-stu-id="a1740-315">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="a1740-316">Metoda używa szablonu do tworzenia zawartości HTML z powtarzanymi elementami podanymi z listy:</span><span class="sxs-lookup"><span data-stu-id="a1740-316">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="a1740-317">Korzystając z listy zwierząt domowych z poprzedniego przykładu, `Repeat` Metoda jest wywoływana z:</span><span class="sxs-lookup"><span data-stu-id="a1740-317">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="a1740-318"><xref:System.Collections.Generic.List%601>z programu `Pet` .</span><span class="sxs-lookup"><span data-stu-id="a1740-318"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="a1740-319">Liczba powtórzeń poszczególnych zwierząt domowych.</span><span class="sxs-lookup"><span data-stu-id="a1740-319">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="a1740-320">Wbudowany szablon do użycia dla elementów listy nieuporządkowanej listy.</span><span class="sxs-lookup"><span data-stu-id="a1740-320">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="a1740-321">Renderowane dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="a1740-321">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="a1740-322">Pomocnicy tagów</span><span class="sxs-lookup"><span data-stu-id="a1740-322">Tag Helpers</span></span>

<span data-ttu-id="a1740-323">*Ten scenariusz ma zastosowanie tylko do widoków i Razor stron MVC (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="a1740-323">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="a1740-324">Istnieją trzy dyrektywy, które odnoszą się do [pomocników tagów](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="a1740-324">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="a1740-325">Dyrektywę</span><span class="sxs-lookup"><span data-stu-id="a1740-325">Directive</span></span> | <span data-ttu-id="a1740-326">Funkcja</span><span class="sxs-lookup"><span data-stu-id="a1740-326">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="a1740-327">Sprawia, że pomocnicy tagów są dostępni do widoku.</span><span class="sxs-lookup"><span data-stu-id="a1740-327">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="a1740-328">Usuwa pomocników tagów, które zostały wcześniej dodane z widoku.</span><span class="sxs-lookup"><span data-stu-id="a1740-328">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="a1740-329">Określa prefiks tagu w celu włączenia obsługi pomocnika tagów i zapewnienia jawnego użycia pomocnika tagów.</span><span class="sxs-lookup"><span data-stu-id="a1740-329">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="no-locrazor-reserved-keywords"></a><span data-ttu-id="a1740-330">Razorzastrzeżone słowa kluczowe</span><span class="sxs-lookup"><span data-stu-id="a1740-330">Razor reserved keywords</span></span>

### <a name="no-locrazor-keywords"></a><span data-ttu-id="a1740-331">Razorsłużąc</span><span class="sxs-lookup"><span data-stu-id="a1740-331">Razor keywords</span></span>

* <span data-ttu-id="a1740-332">`page`(Wymaga ASP.NET Core 2,1 lub nowszego)</span><span class="sxs-lookup"><span data-stu-id="a1740-332">`page` (Requires ASP.NET Core 2.1 or later)</span></span>
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* <span data-ttu-id="a1740-333">`helper`(Obecnie nie jest obsługiwane przez ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="a1740-333">`helper` (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="a1740-334">RazorSłowa kluczowe są wyprowadzane z `@(Razor Keyword)` (na przykład `@(functions)` ).</span><span class="sxs-lookup"><span data-stu-id="a1740-334">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-no-locrazor-keywords"></a><span data-ttu-id="a1740-335">RazorSłowa kluczowe języka C#</span><span class="sxs-lookup"><span data-stu-id="a1740-335">C# Razor keywords</span></span>

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

<span data-ttu-id="a1740-336">RazorSłowa kluczowe języka C# muszą mieć podwójne ucieczki z `@(@C# Razor Keyword)` (na przykład `@(@case)` ).</span><span class="sxs-lookup"><span data-stu-id="a1740-336">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="a1740-337">Pierwszy `@` wyprowadza Razor Analizator.</span><span class="sxs-lookup"><span data-stu-id="a1740-337">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="a1740-338">Drugi `@` wyprowadza analizator składni języka C#.</span><span class="sxs-lookup"><span data-stu-id="a1740-338">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-no-locrazor"></a><span data-ttu-id="a1740-339">Zastrzeżone słowa kluczowe, które nie są używane przezRazor</span><span class="sxs-lookup"><span data-stu-id="a1740-339">Reserved keywords not used by Razor</span></span>

* `class`

## <a name="inspect-the-no-locrazor-c-class-generated-for-a-view"></a><span data-ttu-id="a1740-340">Sprawdzanie Razor klasy języka C# wygenerowanej dla widoku</span><span class="sxs-lookup"><span data-stu-id="a1740-340">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="a1740-341">W zestaw .NET Core SDK 2,1 lub nowszej, [ Razor zestaw SDK](xref:razor-pages/sdk) obsługuje kompilację Razor plików.</span><span class="sxs-lookup"><span data-stu-id="a1740-341">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="a1740-342">Podczas kompilowania projektu Razor zestaw SDK generuje \*obj/<build_configuration>/<target_framework_moniker>/ Razor \* katalogu w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="a1740-342">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="a1740-343">Struktura katalogów w *Razor* katalogu odzwierciedla strukturę katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="a1740-343">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="a1740-344">Rozważmy następującą strukturę katalogów w projekcie ASP.NET Core stron 2,1 na Razor platformie .NET Core 2,1:</span><span class="sxs-lookup"><span data-stu-id="a1740-344">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

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

<span data-ttu-id="a1740-345">Kompilowanie projektu w konfiguracji *debugowania* powoduje zwrócenie następującego katalogu *obj* :</span><span class="sxs-lookup"><span data-stu-id="a1740-345">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

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

<span data-ttu-id="a1740-346">Aby wyświetlić wygenerowaną klasę dla *stron/index. cshtml*, Otwórz *obj/Debug/netcoreapp 2.1/ Razor /Pages/index.g.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="a1740-346">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="a1740-347">Dodaj następującą klasę do projektu ASP.NET Core MVC:</span><span class="sxs-lookup"><span data-stu-id="a1740-347">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="a1740-348">W programie `Startup.ConfigureServices` Zastąp wartość `RazorTemplateEngine` dodany przez MVC `CustomTemplateEngine` klasą:</span><span class="sxs-lookup"><span data-stu-id="a1740-348">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="a1740-349">Ustaw punkt przerwania na `return csharpDocument;` instrukcji `CustomTemplateEngine` .</span><span class="sxs-lookup"><span data-stu-id="a1740-349">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="a1740-350">Gdy wykonanie programu zatrzyma się w punkcie przerwania, Wyświetl wartość `generatedCode` .</span><span class="sxs-lookup"><span data-stu-id="a1740-350">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Widok wizualizatora tekstu generatedCode](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="a1740-352">Wyświetl wyszukiwania i rozróżnianie wielkości liter</span><span class="sxs-lookup"><span data-stu-id="a1740-352">View lookups and case sensitivity</span></span>

<span data-ttu-id="a1740-353">RazorAparat widoku wykonuje wyszukiwania z uwzględnieniem wielkości liter w widokach.</span><span class="sxs-lookup"><span data-stu-id="a1740-353">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="a1740-354">Jednak rzeczywiste wyszukiwanie jest określane przez podstawowy system plików:</span><span class="sxs-lookup"><span data-stu-id="a1740-354">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="a1740-355">Źródło na podstawie pliku:</span><span class="sxs-lookup"><span data-stu-id="a1740-355">File based source:</span></span>
  * <span data-ttu-id="a1740-356">W systemach operacyjnych z systemami plików bez uwzględniania wielkości liter (na przykład Windows) wyszukiwania dostawcy plików fizycznych nie uwzględniają wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="a1740-356">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="a1740-357">Na przykład `return View("Test")` wyniki są zgodne z */views/Home/test.cshtml*, */views/Home/test.cshtml*i innymi wariantami wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="a1740-357">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="a1740-358">W przypadku systemów plików z uwzględnieniem wielkości liter (na przykład Linux, OSX i with `EmbeddedFileProvider` ) Wyszukiwanie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="a1740-358">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="a1740-359">Na przykład, w odniesieniu do `return View("Test")` */views/Home/test.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a1740-359">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="a1740-360">Wstępnie skompilowane widoki: w przypadku ASP.NET Core 2,0 i nowszych wyszukiwanie wstępnie skompilowanych widoków nie uwzględnia wielkości liter we wszystkich systemach operacyjnych.</span><span class="sxs-lookup"><span data-stu-id="a1740-360">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="a1740-361">Zachowanie jest takie samo jak zachowanie dostawcy plików fizycznych w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="a1740-361">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="a1740-362">Jeśli dwa skompilowane widoki różnią się tylko wielkością liter, wynik wyszukiwania jest niejednoznaczny.</span><span class="sxs-lookup"><span data-stu-id="a1740-362">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="a1740-363">Deweloperzy są zachęcani do dopasowania wielkości liter w nazwach plików i katalogów do wielkości liter:</span><span class="sxs-lookup"><span data-stu-id="a1740-363">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="a1740-364">Nazwy obszaru, kontrolera i akcji.</span><span class="sxs-lookup"><span data-stu-id="a1740-364">Area, controller, and action names.</span></span>
* <span data-ttu-id="a1740-365">RazorPage.</span><span class="sxs-lookup"><span data-stu-id="a1740-365">Razor Pages.</span></span>

<span data-ttu-id="a1740-366">Przypadek dopasowania gwarantuje, że wdrożenia znajdą swoje widoki niezależnie od systemu plików.</span><span class="sxs-lookup"><span data-stu-id="a1740-366">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a1740-367">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="a1740-367">Additional resources</span></span>

<span data-ttu-id="a1740-368">[Wprowadzenie do programowania w sieci Web ASP.NET za pomocą Razor Składnia](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) zawiera wiele przykładów programowania z Razor składnią.</span><span class="sxs-lookup"><span data-stu-id="a1740-368">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
