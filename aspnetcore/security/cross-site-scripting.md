---
title: Zapobiegaj skryptom między lokacjami (XSS) w ASP.NET Core
author: rick-anderson
description: Więcej informacji na temat skryptów między lokacjami (XSS) i techniki rozwiązywania tych luk w zabezpieczeniach aplikacji ASP.NET Core.
ms.author: riande
ms.date: 10/02/2018
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
uid: security/cross-site-scripting
ms.openlocfilehash: a7a0c0ff44de5b04d7fa9a8f2f16f7c9f786f64b
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057073"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a>Zapobiegaj skryptom między lokacjami (XSS) w ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Skrypt między lokacjami (XSS) stanowi lukę w zabezpieczeniach, która umożliwia atakującemu umieszczenie skryptów po stronie klienta (zazwyczaj JavaScript) na stronach sieci Web. Gdy inni użytkownicy ładują strony, których dotyczy skrypt osoby atakującej, co umożliwi atakującemu kradzież cookie tokenów s i sesji, należy zmienić zawartość strony sieci Web za pomocą manipulowania dom lub przekierować przeglądarkę na inną stronę. Luki w zabezpieczeniach XSS są zwykle wykonywane, gdy aplikacja pobiera dane wejściowe użytkownika i wyprowadza ją na stronę bez sprawdzania poprawności, kodowania lub ucieczki.

## <a name="protecting-your-application-against-xss"></a>Ochrona aplikacji przed XSS

Na poziomie podstawowym ataki polegają na zalewaniu aplikacji do wstawienia `<script>` tagów do renderowanej strony lub przez wstawienie `On*` zdarzenia do elementu. Deweloperzy powinni używać następujących kroków zapobiegania, aby uniknąć wprowadzenia XSS do aplikacji.

1. Nigdy nie umieszczaj niezaufanych danych w danych wejściowych HTML, chyba że wykonujesz poniższą procedurę. Niezaufane dane to wszelkie dane, które mogą być kontrolowane przez atakującego, dane wejściowe formularza HTML, ciągi zapytań, nagłówki HTTP, nawet źródła danych z bazy danych, ponieważ osoba atakująca może mieć możliwość naruszenia bazy danych, nawet jeśli nie mogą naruszyć aplikacji.

2. Przed umieszczeniem niezaufanych danych wewnątrz elementu HTML upewnij się, że jest on zakodowany w formacie HTML. Kodowanie HTML przyjmuje takie znaki, jak &lt; i zmienia je na bezpieczną formę, taką jak &amp; lt;

3. Przed umieszczeniem niezaufanych danych w atrybucie HTML upewnij się, że jest on zakodowany w formacie HTML. Kodowanie atrybutu HTML jest nadzbiorem kodowania HTML i koduje dodatkowe znaki takie jak "i".

4. Przed umieszczeniem niezaufanych danych w języku JavaScript Umieść dane w elemencie HTML, którego zawartość jest pobierana w czasie wykonywania. Jeśli to nie jest możliwe, upewnij się, że dane są kodowane w języku JavaScript. Kodowanie JavaScript pobiera niebezpieczne znaki dla języka JavaScript i zastępuje je szesnastkową, na przykład będzie &lt; kodowane jako `\u003C` .

5. Przed umieszczeniem niezaufanych danych w ciągu zapytania adresu URL upewnij się, że adres URL został zakodowany.

## <a name="html-encoding-using-no-locrazor"></a>Kodowanie HTML przy użyciu Razor

RazorAparat używany w MVC automatycznie koduje wszystkie dane wyjściowe pochodzące ze zmiennych, chyba że naprawdę nie zadziała to w ten sposób. Używa reguł kodowania atrybutu języka HTML za każdym razem, gdy używasz *@* dyrektywy. Ponieważ kodowanie atrybutu HTML jest nadzbiorem kodowania HTML, oznacza to, że nie trzeba niczego zachodzić, niezależnie od tego, czy należy użyć kodowania HTML czy kodowania atrybutów HTML. Musisz się upewnić, że w kontekście HTML użyto tylko @, a nie przy próbie wstawienia niezaufanych danych wejściowych bezpośrednio do języka JavaScript. Pomocnicy tagów również kodują dane wejściowe, które są używane w parametrach tagów.

Wykonaj następujące czynności Razor :

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

Ten widok wyprowadza zawartość zmiennej *untrustedInput* . Ta zmienna zawiera znaki, które są używane w atakach XSS, mianowicie &lt; "i &gt; . Badanie źródła pokazuje renderowane dane wyjściowe kodowane jako:

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> ASP.NET Core MVC udostępnia `HtmlString` klasę, która nie jest automatycznie zakodowana w danych wyjściowych. Tego elementu nie należy używać w połączeniu z niezaufanymi danymi wejściowymi, ponieważ spowoduje to ujawnienie luki w zabezpieczeniach XSS.

## <a name="javascript-encoding-using-no-locrazor"></a>Kodowanie JavaScript przy użyciu Razor

Czasami może się okazać, że chcesz wstawić wartość do języka JavaScript, aby przetworzyć ją w widoku. Istnieją dwa sposoby, aby to zrobić. Najbezpieczniejszym sposobem wstawiania wartości jest umieszczenie wartości w atrybucie danych tagu i pobranie go w języku JavaScript. Na przykład:

```cshtml
@{
    var untrustedInput = "<script>alert(1)</script>";
}

<div id="injectedData"
     data-untrustedinput="@untrustedInput" />

<div id="scriptedWrite" />
<div id="scriptedWrite-html5" />

<script>
    var injectedData = document.getElementById("injectedData");

    // All clients
    var clientSideUntrustedInputOldStyle =
        injectedData.getAttribute("data-untrustedinput");

    // HTML 5 clients only
    var clientSideUntrustedInputHtml5 =
        injectedData.dataset.untrustedinput;

    // Put the injected, untrusted data into the scriptedWrite div tag.
    // Do NOT use document.write() on dynamically generated data as it
    // can lead to XSS.

    document.getElementById("scriptedWrite").innerText += clientSideUntrustedInputOldStyle;

    // Or you can use createElement() to dynamically create document elements
    // This time we're using textContent to ensure the data is properly encoded.
    var x = document.createElement("div");
    x.textContent = clientSideUntrustedInputHtml5;
    document.body.appendChild(x);

    // You can also use createTextNode on an element to ensure data is properly encoded.
    var y = document.createElement("div");
    y.appendChild(document.createTextNode(clientSideUntrustedInputHtml5));
    document.body.appendChild(y);

</script>
   ```

Powyższy znacznik generuje następujący kod HTML:

```html
<div id="injectedData"
     data-untrustedinput="&lt;script&gt;alert(1)&lt;/script&gt;" />

<div id="scriptedWrite" />
<div id="scriptedWrite-html5" />

<script>
    var injectedData = document.getElementById("injectedData");

    // All clients
    var clientSideUntrustedInputOldStyle =
        injectedData.getAttribute("data-untrustedinput");

    // HTML 5 clients only
    var clientSideUntrustedInputHtml5 =
        injectedData.dataset.untrustedinput;

    // Put the injected, untrusted data into the scriptedWrite div tag.
    // Do NOT use document.write() on dynamically generated data as it can
    // lead to XSS.

    document.getElementById("scriptedWrite").innerText += clientSideUntrustedInputOldStyle;

    // Or you can use createElement() to dynamically create document elements
    // This time we're using textContent to ensure the data is properly encoded.
    var x = document.createElement("div");
    x.textContent = clientSideUntrustedInputHtml5;
    document.body.appendChild(x);

    // You can also use createTextNode on an element to ensure data is properly encoded.
    var y = document.createElement("div");
    y.appendChild(document.createTextNode(clientSideUntrustedInputHtml5));
    document.body.appendChild(y);

</script>
   ```

Poprzedni kod generuje następujące dane wyjściowe:

```
<script>alert(1)</script>
<script>alert(1)</script>
<script>alert(1)</script>
```

>[!WARNING]
> Do ***nie** można łączyć niezaufanych danych wejściowych w języku JavaScript w celu tworzenia elementów dom lub korzystania `document.write()` z dynamicznie generowanej zawartości.
>
> Użyj jednego z poniższych metod, aby zapobiec ujawnieniu kodu do typu XSS opartego na modelu DOM: _ `createElement()` i przypisywać wartości właściwości odpowiednimi metodami lub właściwościami, takimi jak `node.textContent=` lub `node.InnerText=` .
> * `document.CreateTextNode()` i Dołącz je do odpowiedniej lokalizacji DOM.
> * `element.SetAttribute()`
> * `element[attribute]=`

## <a name="accessing-encoders-in-code"></a>Uzyskiwanie dostępu do koderów w kodzie

Kodery HTML, JavaScript i URL są dostępne dla kodu na dwa sposoby, można je wstrzyknąć za pośrednictwem [iniekcji zależności](xref:fundamentals/dependency-injection) lub użyć domyślnych koderów zawartych w `System.Text.Encodings.Web` przestrzeni nazw. Jeśli używasz koderów domyślnych, wszystkie zastosowane do zakresów znaków, które mają być traktowane jako bezpieczne, nie zaczną obowiązywać — domyślne kodery będą używać najbezpieczniejszych reguł kodowania.

Aby użyć konfigurowalnych koderów za pośrednictwem narzędzi, konstruktory powinny przyjmować parametr *HtmlEncode*, *JavaScriptEncoder* i *UrlEncoder* , zgodnie z potrzebami. Na przykład:

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

## <a name="encoding-url-parameters"></a>Parametry kodowania adresu URL

Jeśli chcesz skompilować ciąg zapytania URL z niezaufanymi danymi wejściowymi jako wartość, użyj `UrlEncoder` do kodowania wartości. Na przykład

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

Po kodowaniu zmienna encodedValue będzie zawierać `%22Quoted%20Value%20with%20spaces%20and%20%26%22` . Spacje, cudzysłowy, interpunkcja i inne niebezpieczne znaki będą zakodowane procentowo na ich wartość szesnastkową, na przykład znak spacji stanie się %20.

>[!WARNING]
> Nie używaj niezaufanych danych wejściowych jako części ścieżki URL. Zawsze przekazuj niezaufane dane wejściowe jako wartość ciągu zapytania.

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a>Dostosowywanie koderów

Domyślnie kodery wykorzystują bezpieczną listę ograniczoną do podstawowego zakresu Unicode w języku łacińskim i kodują wszystkie znaki poza tym zakresem jako odpowiedniki kodu znaku. To zachowanie ma także wpływ na Razor renderowanie TagHelper i HtmlHelper, ponieważ użyje koderów do wyprowadzania ciągów.

W związku z tym należy chronić przed nieznanymi lub przyszłymi błędami przeglądarki (poprzednie błędy przeglądarki wyzwolenie analizy na podstawie przetwarzania znaków innych niż angielski). Jeśli witryna sieci Web wykonuje duże użycie znaków innych niż łacińskie, takich jak chińskie, cyrylica lub inne, to prawdopodobnie zachowanie nie jest wymagane.

Można dostosować listy bezpiecznych koderów, aby uwzględnić zakresy Unicode odpowiednie dla aplikacji podczas uruchamiania, w programie `ConfigureServices()` .

Na przykład przy użyciu konfiguracji domyślnej można użyć Razor HtmlHelper podobnej do tego;

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

Po wyświetleniu źródła strony sieci Web zobaczysz, że został on wyrenderowany w następujący sposób, z zakodowanym tekstem w języku chińskim.

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

Aby rozszerzyć znaki traktowane jako bezpieczne przez koder, należy wstawić następujący wiersz do `ConfigureServices()` metody w `startup.cs` ;

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

Ten przykład rozszerza bezpieczną listę w celu uwzględnienia zakresu Unicode CjkUnifiedIdeographs. Renderowane dane wyjściowe staną się teraz

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

Zakresy bezpiecznych list są określone jako wykresy kodu Unicode, a nie Języki. [Standard Unicode](https://unicode.org/) ma listę [wykresów kodu](https://www.unicode.org/charts/index.html) , których można użyć do znalezienia wykresu zawierającego znaki. Każdy koder, kod HTML, JavaScript i adres URL, muszą być skonfigurowane osobno.

> [!NOTE]
> Dostosowanie listy bezpiecznych ma wpływ tylko na kodery, które są źródłem przez DI. Jeśli użytkownik uzyskuje bezpośredni dostęp do kodera przy użyciu `System.Text.Encodings.Web.*Encoder.Default` domyślnego, zostanie użyty tylko podstawowy safelist.

## <a name="where-should-encoding-take-place"></a>Gdzie ma nastąpić kodowanie?

Ogólna przyjęta Metoda polega na tym, że kodowanie ma miejsce w punkcie wyjścia, a zakodowane wartości nigdy nie powinny być przechowywane w bazie danych. Kodowanie w punkcie wyjścia umożliwia zmianę użycia danych, na przykład z HTML do wartości ciągu zapytania. Umożliwia również łatwe wyszukiwanie danych bez konieczności kodowania wartości przed wyszukiwaniem i pozwala korzystać ze wszystkich zmian lub poprawek błędów w koderach.

## <a name="validation-as-an-xss-prevention-technique"></a>Weryfikacja jako technika zapobiegania XSS

Walidacja może być przydatnym narzędziem ograniczającym ataki XSS. Na przykład ciąg liczbowy zawierający tylko znaki 0-9 nie wywoła ataku typu XSS. Walidacja jest bardziej skomplikowana przy akceptowaniu kodu HTML w danych wejściowych użytkownika. Analiza danych wejściowych HTML jest trudna, jeśli nie jest to możliwe. Zaawansowana, powiązana z analizatorem, który oddziela osadzony kod HTML, jest bezpieczniejsza opcja przyjmowania danych wejściowych. Nigdy nie należy polegać wyłącznie na walidacji. Zawsze Koduj niezaufane dane wejściowe przed wyjściem, niezależnie od tego, jakie sprawdzanie poprawności lub oczyszczanie zostało wykonane.
