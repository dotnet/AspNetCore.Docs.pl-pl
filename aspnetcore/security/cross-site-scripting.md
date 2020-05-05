---
title: Zapobiegaj skryptom między lokacjami (XSS) w ASP.NET Core
author: rick-anderson
description: Więcej informacji na temat skryptów między lokacjami (XSS) i techniki rozwiązywania tych luk w zabezpieczeniach aplikacji ASP.NET Core.
ms.author: riande
ms.date: 10/02/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cross-site-scripting
ms.openlocfilehash: 5a14042db6250d5f7a47acaf4083b44272c606ab
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777491"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a><span data-ttu-id="d9c0e-103">Zapobiegaj skryptom między lokacjami (XSS) w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d9c0e-103">Prevent Cross-Site Scripting (XSS) in ASP.NET Core</span></span>

<span data-ttu-id="d9c0e-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d9c0e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d9c0e-105">Skrypt między lokacjami (XSS) stanowi lukę w zabezpieczeniach, która umożliwia atakującemu umieszczenie skryptów po stronie klienta (zazwyczaj JavaScript) na stronach sieci Web.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-105">Cross-Site Scripting (XSS) is a security vulnerability which enables an attacker to place client side scripts (usually JavaScript) into web pages.</span></span> <span data-ttu-id="d9c0e-106">Gdy inni użytkownicy ładują strony, których dotyczy skrypt osoby atakującej, umożliwi atakującemu kradzież plików cookie i tokenów sesji, zmianę zawartości strony sieci Web za pomocą manipulowania DOM lub przekierowanie przeglądarki na inną stronę.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-106">When other users load affected pages the attacker's scripts will run, enabling the attacker to steal cookies and session tokens, change the contents of the web page through DOM manipulation or redirect the browser to another page.</span></span> <span data-ttu-id="d9c0e-107">Luki w zabezpieczeniach XSS są zwykle wykonywane, gdy aplikacja pobiera dane wejściowe użytkownika i wyprowadza ją na stronę bez sprawdzania poprawności, kodowania lub ucieczki.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-107">XSS vulnerabilities generally occur when an application takes user input and outputs it to a page without validating, encoding or escaping it.</span></span>

## <a name="protecting-your-application-against-xss"></a><span data-ttu-id="d9c0e-108">Ochrona aplikacji przed XSS</span><span class="sxs-lookup"><span data-stu-id="d9c0e-108">Protecting your application against XSS</span></span>

<span data-ttu-id="d9c0e-109">Na poziomie podstawowym ataki polegają na zalewaniu aplikacji do wstawienia `<script>` tagów do renderowanej strony lub przez wstawienie `On*` zdarzenia do elementu.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-109">At a basic level XSS works by tricking your application into inserting a `<script>` tag into your rendered page, or by inserting an `On*` event into an element.</span></span> <span data-ttu-id="d9c0e-110">Deweloperzy powinni używać następujących kroków zapobiegania, aby uniknąć wprowadzenia XSS do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-110">Developers should use the following prevention steps to avoid introducing XSS into their application.</span></span>

1. <span data-ttu-id="d9c0e-111">Nigdy nie umieszczaj niezaufanych danych w danych wejściowych HTML, chyba że wykonujesz poniższą procedurę.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-111">Never put untrusted data into your HTML input, unless you follow the rest of the steps below.</span></span> <span data-ttu-id="d9c0e-112">Niezaufane dane to wszelkie dane, które mogą być kontrolowane przez atakującego, dane wejściowe formularza HTML, ciągi zapytań, nagłówki HTTP, nawet źródła danych z bazy danych, ponieważ osoba atakująca może mieć możliwość naruszenia bazy danych, nawet jeśli nie mogą naruszyć aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-112">Untrusted data is any data that may be controlled by an attacker, HTML form inputs, query strings, HTTP headers, even data sourced from a database as an attacker may be able to breach your database even if they cannot breach your application.</span></span>

2. <span data-ttu-id="d9c0e-113">Przed umieszczeniem niezaufanych danych wewnątrz elementu HTML upewnij się, że jest on zakodowany w formacie HTML.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-113">Before putting untrusted data inside an HTML element ensure it's HTML encoded.</span></span> <span data-ttu-id="d9c0e-114">Kodowanie HTML przyjmuje takie znaki, &lt; jak i zmienia je na bezpieczną formę, &amp;taką jak lt;</span><span class="sxs-lookup"><span data-stu-id="d9c0e-114">HTML encoding takes characters such as &lt; and changes them into a safe form like &amp;lt;</span></span>

3. <span data-ttu-id="d9c0e-115">Przed umieszczeniem niezaufanych danych w atrybucie HTML upewnij się, że jest on zakodowany w formacie HTML.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-115">Before putting untrusted data into an HTML attribute ensure it's HTML encoded.</span></span> <span data-ttu-id="d9c0e-116">Kodowanie atrybutu HTML jest nadzbiorem kodowania HTML i koduje dodatkowe znaki takie jak "i".</span><span class="sxs-lookup"><span data-stu-id="d9c0e-116">HTML attribute encoding is a superset of HTML encoding and encodes additional characters such as " and '.</span></span>

4. <span data-ttu-id="d9c0e-117">Przed umieszczeniem niezaufanych danych w języku JavaScript Umieść dane w elemencie HTML, którego zawartość jest pobierana w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-117">Before putting untrusted data into JavaScript place the data in an HTML element whose contents you retrieve at runtime.</span></span> <span data-ttu-id="d9c0e-118">Jeśli to nie jest możliwe, upewnij się, że dane są kodowane w języku JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-118">If this isn't possible, then ensure the data is JavaScript encoded.</span></span> <span data-ttu-id="d9c0e-119">Kodowanie JavaScript pobiera niebezpieczne znaki dla języka JavaScript i zastępuje je szesnastkową, na przykład &lt; będzie kodowane jako `\u003C`.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-119">JavaScript encoding takes dangerous characters for JavaScript and replaces them with their hex, for example &lt; would be encoded as `\u003C`.</span></span>

5. <span data-ttu-id="d9c0e-120">Przed umieszczeniem niezaufanych danych w ciągu zapytania adresu URL upewnij się, że adres URL został zakodowany.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-120">Before putting untrusted data into a URL query string ensure it's URL encoded.</span></span>

## <a name="html-encoding-using-razor"></a><span data-ttu-id="d9c0e-121">Kodowanie HTML przy użyciuRazor</span><span class="sxs-lookup"><span data-stu-id="d9c0e-121">HTML Encoding using Razor</span></span>

<span data-ttu-id="d9c0e-122">Razor Aparat używany w MVC automatycznie koduje wszystkie dane wyjściowe pochodzące ze zmiennych, chyba że naprawdę nie zadziała to w ten sposób.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-122">The Razor engine used in MVC automatically encodes all output sourced from variables, unless you work really hard to prevent it doing so.</span></span> <span data-ttu-id="d9c0e-123">Używa reguł kodowania atrybutu języka HTML za każdym razem, *@* gdy używasz dyrektywy.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-123">It uses HTML attribute encoding rules whenever you use the *@* directive.</span></span> <span data-ttu-id="d9c0e-124">Ponieważ kodowanie atrybutu HTML jest nadzbiorem kodowania HTML, oznacza to, że nie trzeba niczego zachodzić, niezależnie od tego, czy należy użyć kodowania HTML czy kodowania atrybutów HTML.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-124">As HTML attribute encoding is a superset of HTML encoding this means you don't have to concern yourself with whether you should use HTML encoding or HTML attribute encoding.</span></span> <span data-ttu-id="d9c0e-125">Musisz się upewnić, że w kontekście HTML użyto tylko @, a nie przy próbie wstawienia niezaufanych danych wejściowych bezpośrednio do języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-125">You must ensure that you only use @ in an HTML context, not when attempting to insert untrusted input directly into JavaScript.</span></span> <span data-ttu-id="d9c0e-126">Pomocnicy tagów również kodują dane wejściowe, które są używane w parametrach tagów.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-126">Tag helpers will also encode input you use in tag parameters.</span></span>

<span data-ttu-id="d9c0e-127">Wykonaj następujące czynności Razor :</span><span class="sxs-lookup"><span data-stu-id="d9c0e-127">Take the following Razor view:</span></span>

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

<span data-ttu-id="d9c0e-128">Ten widok wyprowadza zawartość zmiennej *untrustedInput* .</span><span class="sxs-lookup"><span data-stu-id="d9c0e-128">This view outputs the contents of the *untrustedInput* variable.</span></span> <span data-ttu-id="d9c0e-129">Ta zmienna zawiera znaki, które są używane w atakach XSS, &lt;mianowicie "i &gt;.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-129">This variable includes some characters which are used in XSS attacks, namely &lt;, " and &gt;.</span></span> <span data-ttu-id="d9c0e-130">Badanie źródła pokazuje renderowane dane wyjściowe kodowane jako:</span><span class="sxs-lookup"><span data-stu-id="d9c0e-130">Examining the source shows the rendered output encoded as:</span></span>

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> <span data-ttu-id="d9c0e-131">ASP.NET Core MVC udostępnia klasę `HtmlString` , która nie jest automatycznie zakodowana w danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-131">ASP.NET Core MVC provides an `HtmlString` class which isn't automatically encoded upon output.</span></span> <span data-ttu-id="d9c0e-132">Tego elementu nie należy używać w połączeniu z niezaufanymi danymi wejściowymi, ponieważ spowoduje to ujawnienie luki w zabezpieczeniach XSS.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-132">This should never be used in combination with untrusted input as this will expose an XSS vulnerability.</span></span>

## <a name="javascript-encoding-using-razor"></a><span data-ttu-id="d9c0e-133">Kodowanie JavaScript przy użyciuRazor</span><span class="sxs-lookup"><span data-stu-id="d9c0e-133">JavaScript Encoding using Razor</span></span>

<span data-ttu-id="d9c0e-134">Czasami może się okazać, że chcesz wstawić wartość do języka JavaScript, aby przetworzyć ją w widoku.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-134">There may be times you want to insert a value into JavaScript to process in your view.</span></span> <span data-ttu-id="d9c0e-135">Istnieją dwa sposoby, aby to zrobić.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-135">There are two ways to do this.</span></span> <span data-ttu-id="d9c0e-136">Najbezpieczniejszym sposobem wstawiania wartości jest umieszczenie wartości w atrybucie danych tagu i pobranie go w języku JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-136">The safest way to insert values is to place the value in a data attribute of a tag and retrieve it in your JavaScript.</span></span> <span data-ttu-id="d9c0e-137">Przykład:</span><span class="sxs-lookup"><span data-stu-id="d9c0e-137">For example:</span></span>

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   <div
       id="injectedData"
       data-untrustedinput="@untrustedInput" />

   <script>
     var injectedData = document.getElementById("injectedData");

     // All clients
     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     // HTML 5 clients only
     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

<span data-ttu-id="d9c0e-138">Spowoduje to wygenerowanie następującego kodu HTML</span><span class="sxs-lookup"><span data-stu-id="d9c0e-138">This will produce the following HTML</span></span>

```html
<div
     id="injectedData"
     data-untrustedinput="&lt;&quot;123&quot;&gt;" />

   <script>
     var injectedData = document.getElementById("injectedData");

     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

<span data-ttu-id="d9c0e-139">Które po uruchomieniu programu będą renderować następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="d9c0e-139">Which, when it runs, will render the following:</span></span>

```
<"123">
   <"123">
```

<span data-ttu-id="d9c0e-140">Można również bezpośrednio wywołać koder JavaScript:</span><span class="sxs-lookup"><span data-stu-id="d9c0e-140">You can also call the JavaScript encoder directly:</span></span>

```cshtml
@using System.Text.Encodings.Web;
   @inject JavaScriptEncoder encoder;

   @{
       var untrustedInput = "<\"123\">";
   }

   <script>
       document.write("@encoder.Encode(untrustedInput)");
   </script>
```

<span data-ttu-id="d9c0e-141">Ta funkcja będzie renderowana w przeglądarce w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="d9c0e-141">This will render in the browser as follows:</span></span>

```html
<script>
    document.write("\u003C\u0022123\u0022\u003E");
</script>
```

>[!WARNING]
> <span data-ttu-id="d9c0e-142">Nie należy łączyć niezaufanych danych wejściowych w języku JavaScript w celu utworzenia elementów DOM.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-142">Don't concatenate untrusted input in JavaScript to create DOM elements.</span></span> <span data-ttu-id="d9c0e-143">Należy `createElement()` używać i przypisywać odpowiednio wartości właściwości, takich `node.TextContent=`jak, lub `element.SetAttribute()` / `element[attribute]=` użyć w inny sposób, aby samodzielnie uwidocznić ataki oparte na modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-143">You should use `createElement()` and assign property values appropriately such as `node.TextContent=`, or use `element.SetAttribute()`/`element[attribute]=` otherwise you expose yourself to DOM-based XSS.</span></span>

## <a name="accessing-encoders-in-code"></a><span data-ttu-id="d9c0e-144">Uzyskiwanie dostępu do koderów w kodzie</span><span class="sxs-lookup"><span data-stu-id="d9c0e-144">Accessing encoders in code</span></span>

<span data-ttu-id="d9c0e-145">Kodery HTML, JavaScript i URL są dostępne dla kodu na dwa sposoby, można je wstrzyknąć za pośrednictwem [iniekcji zależności](xref:fundamentals/dependency-injection) lub użyć domyślnych koderów zawartych w `System.Text.Encodings.Web` przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-145">The HTML, JavaScript and URL encoders are available to your code in two ways, you can inject them via [dependency injection](xref:fundamentals/dependency-injection) or you can use the default encoders contained in the `System.Text.Encodings.Web` namespace.</span></span> <span data-ttu-id="d9c0e-146">Jeśli używasz koderów domyślnych, wszystkie zastosowane do zakresów znaków, które mają być traktowane jako bezpieczne, nie zaczną obowiązywać — domyślne kodery będą używać najbezpieczniejszych reguł kodowania.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-146">If you use the default encoders then any  you applied to character ranges to be treated as safe won't take effect - the default encoders use the safest encoding rules possible.</span></span>

<span data-ttu-id="d9c0e-147">Aby użyć konfigurowalnych koderów za pośrednictwem narzędzi, konstruktory powinny przyjmować parametr *HtmlEncode*, *JavaScriptEncoder* i *UrlEncoder* , zgodnie z potrzebami.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-147">To use the configurable encoders via DI your constructors should take an *HtmlEncoder*, *JavaScriptEncoder* and *UrlEncoder* parameter as appropriate.</span></span> <span data-ttu-id="d9c0e-148">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="d9c0e-148">For example;</span></span>

```csharp
public class HomeController : Controller
   {
       HtmlEncoder _htmlEncoder;
       JavaScriptEncoder _javaScriptEncoder;
       UrlEncoder _urlEncoder;

       public HomeController(HtmlEncoder htmlEncoder,
                             JavaScriptEncoder javascriptEncoder,
                             UrlEncoder urlEncoder)
       {
           _htmlEncoder = htmlEncoder;
           _javaScriptEncoder = javascriptEncoder;
           _urlEncoder = urlEncoder;
       }
   }
   ```

## <a name="encoding-url-parameters"></a><span data-ttu-id="d9c0e-149">Parametry kodowania adresu URL</span><span class="sxs-lookup"><span data-stu-id="d9c0e-149">Encoding URL Parameters</span></span>

<span data-ttu-id="d9c0e-150">Jeśli chcesz skompilować ciąg zapytania URL z niezaufanymi danymi wejściowymi jako wartość, użyj `UrlEncoder` do kodowania wartości.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-150">If you want to build a URL query string with untrusted input as a value use the `UrlEncoder` to encode the value.</span></span> <span data-ttu-id="d9c0e-151">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="d9c0e-151">For example,</span></span>

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

<span data-ttu-id="d9c0e-152">Po kodowaniu zmienna encodedValue będzie zawierać `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-152">After encoding the encodedValue variable will contain `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span></span> <span data-ttu-id="d9c0e-153">Spacje, cudzysłowy, interpunkcja i inne niebezpieczne znaki będą zakodowane procentowo na ich wartość szesnastkową, na przykład znak spacji stanie się %20.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-153">Spaces, quotes, punctuation and other unsafe characters will be percent encoded to their hexadecimal value, for example a space character will become %20.</span></span>

>[!WARNING]
> <span data-ttu-id="d9c0e-154">Nie używaj niezaufanych danych wejściowych jako części ścieżki URL.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-154">Don't use untrusted input as part of a URL path.</span></span> <span data-ttu-id="d9c0e-155">Zawsze przekazuj niezaufane dane wejściowe jako wartość ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-155">Always pass untrusted input as a query string value.</span></span>

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a><span data-ttu-id="d9c0e-156">Dostosowywanie koderów</span><span class="sxs-lookup"><span data-stu-id="d9c0e-156">Customizing the Encoders</span></span>

<span data-ttu-id="d9c0e-157">Domyślnie kodery wykorzystują bezpieczną listę ograniczoną do podstawowego zakresu Unicode w języku łacińskim i kodują wszystkie znaki poza tym zakresem jako odpowiedniki kodu znaku.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-157">By default encoders use a safe list limited to the Basic Latin Unicode range and encode all characters outside of that range as their character code equivalents.</span></span> <span data-ttu-id="d9c0e-158">To zachowanie ma także Razor wpływ na renderowanie TagHelper i HtmlHelper, ponieważ użyje koderów do wyprowadzania ciągów.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-158">This behavior also affects Razor TagHelper and HtmlHelper rendering as it will use the encoders to output your strings.</span></span>

<span data-ttu-id="d9c0e-159">W związku z tym należy chronić przed nieznanymi lub przyszłymi błędami przeglądarki (poprzednie błędy przeglądarki wyzwolenie analizy na podstawie przetwarzania znaków innych niż angielski).</span><span class="sxs-lookup"><span data-stu-id="d9c0e-159">The reasoning behind this is to protect against unknown or future browser bugs (previous browser bugs have tripped up parsing based on the processing of non-English characters).</span></span> <span data-ttu-id="d9c0e-160">Jeśli witryna sieci Web wykonuje duże użycie znaków innych niż łacińskie, takich jak chińskie, cyrylica lub inne, to prawdopodobnie zachowanie nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-160">If your web site makes heavy use of non-Latin characters, such as Chinese, Cyrillic or others this is probably not the behavior you want.</span></span>

<span data-ttu-id="d9c0e-161">Można dostosować listy bezpiecznych koderów, aby uwzględnić zakresy Unicode odpowiednie dla aplikacji podczas uruchamiania, w programie `ConfigureServices()`.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-161">You can customize the encoder safe lists to include Unicode ranges appropriate to your application during startup, in `ConfigureServices()`.</span></span>

<span data-ttu-id="d9c0e-162">Na przykład przy użyciu konfiguracji domyślnej można użyć Razor HtmlHelper podobnej do tego;</span><span class="sxs-lookup"><span data-stu-id="d9c0e-162">For example, using the default configuration you might use a Razor HtmlHelper like so;</span></span>

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

<span data-ttu-id="d9c0e-163">Po wyświetleniu źródła strony sieci Web zobaczysz, że został on wyrenderowany w następujący sposób, z zakodowanym tekstem w języku chińskim.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-163">When you view the source of the web page you will see it has been rendered as follows, with the Chinese text encoded;</span></span>

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

<span data-ttu-id="d9c0e-164">Aby rozszerzyć znaki traktowane jako bezpieczne przez koder, należy wstawić następujący wiersz do `ConfigureServices()` metody w; `startup.cs`</span><span class="sxs-lookup"><span data-stu-id="d9c0e-164">To widen the characters treated as safe by the encoder you would insert the following line into the `ConfigureServices()` method in `startup.cs`;</span></span>

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

<span data-ttu-id="d9c0e-165">Ten przykład rozszerza bezpieczną listę w celu uwzględnienia zakresu Unicode CjkUnifiedIdeographs.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-165">This example widens the safe list to include the Unicode Range CjkUnifiedIdeographs.</span></span> <span data-ttu-id="d9c0e-166">Renderowane dane wyjściowe staną się teraz</span><span class="sxs-lookup"><span data-stu-id="d9c0e-166">The rendered output would now become</span></span>

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

<span data-ttu-id="d9c0e-167">Zakresy bezpiecznych list są określone jako wykresy kodu Unicode, a nie Języki.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-167">Safe list ranges are specified as Unicode code charts, not languages.</span></span> <span data-ttu-id="d9c0e-168">[Standard Unicode](https://unicode.org/) ma listę [wykresów kodu](https://www.unicode.org/charts/index.html) , których można użyć do znalezienia wykresu zawierającego znaki.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-168">The [Unicode standard](https://unicode.org/) has a list of [code charts](https://www.unicode.org/charts/index.html) you can use to find the chart containing your characters.</span></span> <span data-ttu-id="d9c0e-169">Każdy koder, kod HTML, JavaScript i adres URL, muszą być skonfigurowane osobno.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-169">Each encoder, Html, JavaScript and Url, must be configured separately.</span></span>

> [!NOTE]
> <span data-ttu-id="d9c0e-170">Dostosowanie listy bezpiecznych ma wpływ tylko na kodery, które są źródłem przez DI.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-170">Customization of the safe list only affects encoders sourced via DI.</span></span> <span data-ttu-id="d9c0e-171">Jeśli użytkownik uzyskuje bezpośredni dostęp do `System.Text.Encodings.Web.*Encoder.Default` kodera przy użyciu domyślnego, zostanie użyty tylko podstawowy safelist.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-171">If you directly access an encoder via `System.Text.Encodings.Web.*Encoder.Default` then the default, Basic Latin only safelist will be used.</span></span>

## <a name="where-should-encoding-take-place"></a><span data-ttu-id="d9c0e-172">Gdzie ma nastąpić kodowanie?</span><span class="sxs-lookup"><span data-stu-id="d9c0e-172">Where should encoding take place?</span></span>

<span data-ttu-id="d9c0e-173">Ogólna przyjęta Metoda polega na tym, że kodowanie ma miejsce w punkcie wyjścia, a zakodowane wartości nigdy nie powinny być przechowywane w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-173">The general accepted practice is that encoding takes place at the point of output and encoded values should never be stored in a database.</span></span> <span data-ttu-id="d9c0e-174">Kodowanie w punkcie wyjścia umożliwia zmianę użycia danych, na przykład z HTML do wartości ciągu zapytania.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-174">Encoding at the point of output allows you to change the use of data, for example, from HTML to a query string value.</span></span> <span data-ttu-id="d9c0e-175">Umożliwia również łatwe wyszukiwanie danych bez konieczności kodowania wartości przed wyszukiwaniem i pozwala korzystać ze wszystkich zmian lub poprawek błędów w koderach.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-175">It also enables you to easily search your data without having to encode values before searching and allows you to take advantage of any changes or bug fixes made to encoders.</span></span>

## <a name="validation-as-an-xss-prevention-technique"></a><span data-ttu-id="d9c0e-176">Weryfikacja jako technika zapobiegania XSS</span><span class="sxs-lookup"><span data-stu-id="d9c0e-176">Validation as an XSS prevention technique</span></span>

<span data-ttu-id="d9c0e-177">Walidacja może być przydatnym narzędziem ograniczającym ataki XSS.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-177">Validation can be a useful tool in limiting XSS attacks.</span></span> <span data-ttu-id="d9c0e-178">Na przykład ciąg liczbowy zawierający tylko znaki 0-9 nie wywoła ataku typu XSS.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-178">For example, a numeric string containing only the characters 0-9 won't trigger an XSS attack.</span></span> <span data-ttu-id="d9c0e-179">Walidacja jest bardziej skomplikowana przy akceptowaniu kodu HTML w danych wejściowych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-179">Validation becomes more complicated when accepting HTML in user input.</span></span> <span data-ttu-id="d9c0e-180">Analiza danych wejściowych HTML jest trudna, jeśli nie jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-180">Parsing HTML input is difficult, if not impossible.</span></span> <span data-ttu-id="d9c0e-181">Zaawansowana, powiązana z analizatorem, który oddziela osadzony kod HTML, jest bezpieczniejsza opcja przyjmowania danych wejściowych.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-181">Markdown, coupled with a parser that strips embedded HTML, is a safer option for accepting rich input.</span></span> <span data-ttu-id="d9c0e-182">Nigdy nie należy polegać wyłącznie na walidacji.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-182">Never rely on validation alone.</span></span> <span data-ttu-id="d9c0e-183">Zawsze Koduj niezaufane dane wejściowe przed wyjściem, niezależnie od tego, jakie sprawdzanie poprawności lub oczyszczanie zostało wykonane.</span><span class="sxs-lookup"><span data-stu-id="d9c0e-183">Always encode untrusted input before output, no matter what validation or sanitization has been performed.</span></span>
