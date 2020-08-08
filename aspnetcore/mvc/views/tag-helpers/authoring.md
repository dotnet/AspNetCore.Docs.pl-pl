---
title: Tworzenie pomocników tagów w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak tworzyć pomocników tagów w ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: mvc/views/tag-helpers/authoring
ms.openlocfilehash: d40b37a4ca8acf57b662124597a8ebc3b90bffd2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018171"
---
# <a name="author-tag-helpers-in-aspnet-core"></a>Tworzenie pomocników tagów w ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="get-started-with-tag-helpers"></a>Wprowadzenie do pomocników tagów

Ten samouczek zawiera wprowadzenie do programowania pomocników tagów. [Wprowadzenie do pomocników tagów](intro.md) opisuje zalety zapewniane przez pomocników tagów.

Pomocnik tagu jest klasą, która implementuje `ITagHelper` interfejs. Jednak podczas tworzenia pomocnika tagów zwykle pochodzi od `TagHelper` , dlatego zapewnia dostęp do `Process` metody.

1. Utwórz nowy projekt ASP.NET Core o nazwie **AuthoringTagHelpers**. Nie będziesz potrzebować uwierzytelniania dla tego projektu.

1. Utwórz folder do przechowywania pomocników tagów o nazwie *TagHelpers*. Folder *TagHelpers* *nie* jest wymagany, ale jest odpowiednią Konwencją. Teraz zacznij pisać kilka prostych pomocników tagów.

## <a name="a-minimal-tag-helper"></a>Minimalny pomocnik tagów

W tej sekcji napiszesz pomocnika tagu, który aktualizuje tag wiadomości e-mail. Przykład:

```html
<email>Support</email>
```

Serwer użyje pomocnika tagu poczty e-mail do przekonwertowania tego znacznika na następujące elementy:

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

Oznacza to, że tag zakotwiczony, który tworzy to łącze e-mail. Możesz to zrobić, jeśli piszesz aparat bloga i potrzebujesz wysłać wiadomość e-mail na potrzeby marketingu, pomocy technicznej i innych kontaktów, a wszystko to w tej samej domenie.

1. Dodaj następującą `EmailTagHelper` klasę do folderu *TagHelpers* .

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]

   * Pomocnicy tagów używają konwencji nazewnictwa, która jest przeznaczona dla elementów nazwy klasy głównej (minus *TagHelper* część nazwy klasy). W tym przykładzie nazwą główną **EmailTagHelper** jest *adres e-mail*, więc `<email>` tag będzie przeznaczony. Ta konwencja nazewnictwa powinna współpracować z większością pomocników tagów, w dalszej części zaprezentowano sposób jego zastąpienia.

   * `EmailTagHelper`Klasa pochodzi od `TagHelper` . `TagHelper`Klasa zawiera metody i właściwości do pisania pomocników tagów.

   * Zastąpiona `Process` Metoda kontroluje, co pomocnik tagów wykonuje po wykonaniu. `TagHelper`Klasa udostępnia również asynchroniczną wersję ( `ProcessAsync` ) z tymi samymi parametrami.

   * Parametr kontekstowy do `Process` (i `ProcessAsync` ) zawiera informacje skojarzone z wykonywaniem bieżącego tagu HTML.

   * Parametr danych wyjściowych do `Process` (i `ProcessAsync` ) zawiera stanowy element HTML reprezentatywny dla oryginalnego źródła używanego do generowania tagu HTML i zawartości.

   * Nasza nazwa klasy ma sufiks **TagHelper**, co *nie* jest wymagane, ale jest uznawana za Konwencję najlepszych rozwiązań. Można zadeklarować klasę jako:

   ```csharp
   public class Email : TagHelper
   ```

1. Aby udostępnić `EmailTagHelper` klasę wszystkim naszym Razor widokom, Dodaj `addTagHelper` dyrektywę do pliku *views/_ViewImports. cshtml* :

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]

   W powyższym kodzie użyto składni wieloznacznej do określenia wszystkich pomocników tagów w naszym zestawie. Pierwszy ciąg po `@addTagHelper` określa pomocnika tagów do załadowania (Użyj znaku "*" dla wszystkich pomocników tagów), a drugi ciąg "AuthoringTagHelpers" określa zestaw, w którym znajduje się pomocnik tagów. Należy również pamiętać, że drugi wiersz zawiera ASP.NET Core pomocników tagów MVC przy użyciu składni wieloznacznej (Ci pomocnicy są omawiane w temacie [wprowadzenie do pomocników tagów](intro.md)). Jest to `@addTagHelper` dyrektywa, która sprawia, że pomocnik tagów jest dostępny dla Razor widoku. Alternatywnie można podać w pełni kwalifikowaną nazwę (FQN) pomocnika tagów, jak pokazano poniżej:

```csharp
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<!--
the following snippet uses TagHelpers3 and should use TagHelpers (not the 3)
    [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
-->

Aby dodać pomocnika tagów do widoku przy użyciu FQN, należy najpierw dodać FQN ( `AuthoringTagHelpers.TagHelpers.EmailTagHelper` ), a następnie **nazwę zestawu** (*AuthoringTagHelpers*, niekoniecznie `namespace` ). Większość deweloperów woli używać składni wieloznacznej. [Wprowadzenie do](intro.md) pomocników tagów prowadzi do szczegółów dotyczących dodawania, usuwania, hierarchii i symboli wieloznacznych.

1. Zaktualizuj znaczniki w pliku *views/Home/Contact. cshtml* przy użyciu następujących zmian:

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. Uruchom aplikację i użyj ulubionej przeglądarki, aby wyświetlić źródło HTML, aby sprawdzić, czy Tagi poczty e-mail zostały zamienione na znaczniki zakotwiczenia (na przykład `<a>Support</a>` ). *Obsługa* i *Marketing* są renderowane jako linki, ale nie mają `href` atrybutu, aby uczynić je funkcjonalnymi. Naprawimy to w następnej sekcji.

## <a name="setattribute-and-setcontent"></a>SetAttribute i SetContent

W tej sekcji zaktualizujemy, `EmailTagHelper` Aby utworzyć prawidłowy tag kotwicy dla poczty e-mail. Zaktualizujemy go, aby pobierał informacje z Razor widoku (w formie `mail-to` atrybutu) i używać go podczas generowania zakotwiczenia.

Zaktualizuj `EmailTagHelper` klasę, wykonując następujące czynności:

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

* Nazwy klas i właściwości w przypadku Pascalów dla pomocników tagów są tłumaczone na ich [Kebab przypadku](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101). W związku z tym, aby użyć `MailTo` atrybutu, użyjesz `<email mail-to="value"/>` równoważnej.

* Ostatni wiersz ustawia kompletną zawartość dla naszego pomocnika tagów w minimalnym stopniu funkcjonalnym.

* Wyróżniony wiersz pokazuje składnię dodawania atrybutów:

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

To podejście działa dla atrybutu "href", o ile nie istnieje obecnie w kolekcji atrybutów. Można również użyć metody, `output.Attributes.Add` Aby dodać atrybut pomocnika tagów na końcu kolekcji atrybutów tagu.

1. Zaktualizuj znaczniki w pliku *views/Home/Contact. cshtml* przy użyciu następujących zmian:

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

1. Uruchom aplikację i sprawdź, czy wygeneruje ona odpowiednie linki.

<a name="self-closing"></a>

   > [!NOTE]
   > Jeśli użytkownik zapisał tag poczty e-mail w sposób samozamykający ( `<email mail-to="Rick" />` ), ostateczne dane wyjściowe również będą zamykane automatycznie. Aby umożliwić zapisanie znacznika tylko za pomocą tagu początkowego ( `<email mail-to="Rick">` ), należy oznaczyć klasę następującymi:
   >
   > [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]

   W przypadku pomocnika tagów poczty e-mail z samym zamknięciem dane wyjściowe byłyby `<a href="mailto:Rick@contoso.com" />` . Tagi zakotwiczenia samozamykające nie są prawidłowym kodem HTML, więc nie chcesz go utworzyć, ale możesz chcieć utworzyć pomocnika tagu, który jest samozamykany. Pomocnicy tagów ustawiają typ `TagMode` właściwości po odczytaniu znacznika.

### <a name="processasync"></a>ProcessAsync

W tej sekcji zapiszemy pomocnika asynchronicznej poczty e-mail.

1. Zastąp `EmailTagHelper` klasę następującym kodem:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

   **Uwagi:**

   * Ta wersja używa metody asynchronicznej `ProcessAsync` . Asynchroniczny `GetChildContentAsync` zwrot `Task` zawiera `TagHelperContent` .

   * Użyj `output` parametru, aby pobrać zawartość elementu HTML.

1. Wprowadź następujące zmiany w pliku *views/Home/Contact. cshtml* , aby pomocnik tagów mógł uzyskać docelowy adres e-mail.

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. Uruchom aplikację i sprawdź, czy generuje ona prawidłowe linki do poczty e-mail.

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a>Elementy SetHtmlContent i PostContent. SetHtmlContent

1. Dodaj następującą `BoldTagHelper` klasę do folderu *TagHelpers* .

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

   * Ten `[HtmlTargetElement]` atrybut przekazuje parametr atrybutu, który określa, że każdy element HTML, który zawiera ATRYBUT HTML o nazwie "Bold", jest zgodny, a `Process` Metoda override w klasie zostanie uruchomiona. W naszym przykładzie `Process` Metoda usuwa atrybut "Bold" i otacza znaczniki zawierające `<strong></strong>` .

   * Ponieważ nie chcesz zamieniać istniejącej zawartości tagu, należy napisać `<strong>` tag otwierając z `PreContent.SetHtmlContent` metodą i tagiem zamykającym `</strong>` przy użyciu `PostContent.SetHtmlContent` metody.

1. Zmodyfikuj widok *about. cshtml* , aby zawierał `bold` wartość atrybutu. Ukończony kod jest przedstawiony poniżej.

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

1. Uruchom aplikację. Możesz użyć ulubionej przeglądarki, aby sprawdzić źródło i sprawdzić adiustację.

   `[HtmlTargetElement]`Atrybut powyżej dotyczy tylko znacznika HTML, który zawiera nazwę atrybutu "Bold". `<bold>`Element nie został zmodyfikowany przez pomocnika tagów.

1. Dodaj komentarz do `[HtmlTargetElement]` wiersza atrybutu, który zostanie domyślnie przypisany do `<bold>` tagów, czyli znaczników HTML w formularzu `<bold>` . Należy pamiętać, że domyślna konwencja nazewnictwa będzie zgodna z nazwą klasy **pogrubioną**TagHelper do `<bold>` tagów.

1. Uruchom aplikację i sprawdź, czy `<bold>` tag jest przetwarzany przez pomocnika tagów.

Dekorowania nazwy klasę z wieloma `[HtmlTargetElement]` atrybutami powoduje logiczne lub docelowe elementy. Na przykład przy użyciu poniższego kodu, tag pogrubiony lub pogrubiony atrybut będzie pasować.

[!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

Gdy wiele atrybutów jest dodawanych do tej samej instrukcji, środowisko uruchomieniowe traktuje je jako logiczne-i. Na przykład w poniższym kodzie element HTML musi mieć nazwę "Bold" z atrybutem o nazwie "Bold" ( `<bold bold />` ), aby dopasować.

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

Można również użyć, `[HtmlTargetElement]` Aby zmienić nazwę elementu wskazywanego. Na przykład, jeśli chcesz `BoldTagHelper` , aby Tagi były docelowe `<MyBold>` , użyj następującego atrybutu:

```csharp
[HtmlTargetElement("MyBold")]
```

## <a name="pass-a-model-to-a-tag-helper"></a>Przekaż model do pomocnika tagów

1. Dodawanie folderu *models* .

1. Dodaj następującą `WebsiteContext` klasę do folderu *models* :

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

1. Dodaj następującą `WebsiteInformationTagHelper` klasę do folderu *TagHelpers* .

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]

   * Jak wspomniano wcześniej, pomocników tagów tłumaczy nazwy klas i właściwości w języku C# z uwzględnieniem wielkości liter dla pomocników tagów w [przypadku Kebab](https://wiki.c2.com/?KebabCase). W związku z tym, aby użyć `WebsiteInformationTagHelper` w programie Razor , należy napisać `<website-information />` .

   * Nie można jednoznacznie zidentyfikować elementu docelowego z `[HtmlTargetElement]` atrybutem, więc wartość domyślna to `website-information` . Jeśli zastosowano następujący atrybut (należy zauważyć, że nie jest on Kebab, ale dopasowuje nazwę klasy):

   ```csharp
   [HtmlTargetElement("WebsiteInformation")]
   ```

   Tag przypadku Kebab nie jest `<website-information />` zgodny. Jeśli chcesz użyć `[HtmlTargetElement]` atrybutu, użyj przypadku Kebab, jak pokazano poniżej:

   ```csharp
   [HtmlTargetElement("Website-Information")]
   ```

   * Elementy, które są przez siebie zamykane, nie mają zawartości. Na potrzeby tego przykładu znaczniki Razor będą używały tagów samozamykających, ale pomocnik tagów będzie tworzyć element [sekcji](https://www.w3.org/TR/html5/sections.html#the-section-element) (który nie jest zamykany i zapisujesz zawartość wewnątrz `section` elementu). W związku z tym należy ustawić, aby `TagMode` `StartTagAndEndTag` zapisywać dane wyjściowe. Alternatywnie możesz dodać komentarz do ustawienia wiersza `TagMode` i napisać znacznik z tagiem zamykającym. (Przykładowe znaczniki są podane w dalszej części tego samouczka).

   * `$`(Znak dolara) w następującym wierszu używa [interpolowanego ciągu](/dotnet/csharp/language-reference/keywords/interpolated-strings):

   ```cshtml
   $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
   ```

1. Dodaj następujący znacznik do widoku *Informacje o. cshtml* . Wyróżnione znaczniki wyświetlają informacje o witrynie sieci Web.

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,4-8, 18-999)]

   > [!NOTE]
   > W Razor poniższym znaczniku:
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=18-18)]
   >
   > Razorwie, że `info` atrybut jest klasą, a nie ciągiem, i chcesz napisać kod w języku C#. Dowolny atrybut pomocnika tagów niebędących ciągami powinien być zapisany bez `@` znaku.

1. Uruchom aplikację i przejdź do widoku informacje, aby wyświetlić informacje o witrynie sieci Web.

   > [!NOTE]
   > Można użyć następującego znacznika z tagiem zamykającym i usunąć wiersz za pomocą `TagMode.StartTagAndEndTag` pomocnika tagów:
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=20-21)]

## <a name="condition-tag-helper"></a>Pomocnik tagów warunku

Pomocnik tagów warunku renderuje dane wyjściowe, gdy przeszedł wartość true.

1. Dodaj następującą `ConditionTagHelper` klasę do folderu *TagHelpers* .

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

1. Zastąp zawartość pliku *viewss/Home/index. cshtml* następującym znacznikiem:

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Index.cshtml)]

1. Zastąp `Index` metodę w `Home` kontrolerze następującym kodem:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

1. Uruchom aplikację i przejdź do strony głównej. Adiustacja w warunku `div` nie będzie renderowana. Dołącz ciąg zapytania `?approved=true` do adresu URL (na przykład `http://localhost:1235/Home/Index?approved=true` ). `approved`jest ustawiona na wartość true i zostanie wyświetlona Adiustacja warunkowa.

> [!NOTE]
> Użyj operatora [nameof](/dotnet/csharp/language-reference/keywords/nameof) , aby określić atrybut docelowy zamiast określać ciąg, jak za pomocą pomocnika tagów pogrubionych:
>
> [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
> Operator [nameof](/dotnet/csharp/language-reference/keywords/nameof) będzie chronić kod w przypadku jego ponownego zamiaru (możemy zmienić nazwę na `RedCondition` ).

### <a name="avoid-tag-helper-conflicts"></a>Unikaj konfliktów pomocników tagów

W tej sekcji napiszesz parę pomocników tagów AutoLink. Pierwszy zastąpi znaczniki zawierające adres URL zaczynający się od protokołu HTTP do tagu kotwicy HTML zawierającego ten sam adres URL (i w związku z tym spowoduje nakazanie linku do adresu URL). Druga będzie taka sama dla adresu URL rozpoczynającego się od strony WWW.

Ponieważ te dwa pomocnicy są ściśle powiązane i w przyszłości mogą się z nimi odnowić, zostaną zachowane w tym samym pliku.

1. Dodaj następującą `AutoLinkerHttpTagHelper` klasę do folderu *TagHelpers* .

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

   >[!NOTE]
   >`AutoLinkerHttpTagHelper`Klasa wskazuje `p` elementy i używa [wyrażenia regularnego](/dotnet/standard/base-types/regular-expression-language-quick-reference) do utworzenia zakotwiczenia.

1. Dodaj następujący znacznik na końcu pliku *views/Home/Contact. cshtml* :

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

1. Uruchom aplikację i sprawdź, czy pomocnik tagów prawidłowo renderuje kotwicę.

1. Zaktualizuj `AutoLinker` klasę, aby uwzględnić, `AutoLinkerWwwTagHelper` która spowoduje przekonwertowanie tekstu www na tag kotwicy, który zawiera także oryginalny tekst www. Zaktualizowany kod jest wyróżniony poniżej:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

1. Uruchom aplikację. Zauważ, że tekst www jest renderowany jako link, ale tekst HTTP nie jest. Jeśli umieścisz punkt przerwania w obu klasach, zobaczysz, że Klasa pomocnika tagów HTTP jest uruchamiana jako pierwsza. Problem polega na tym, że dane wyjściowe pomocnika tagów są buforowane, a kiedy pomocnik tagów WWW jest uruchomiony, zastępuje zbuforowane dane wyjściowe z pomocnika tagów HTTP. W dalszej części tego samouczka zobaczymy, jak sterować kolejnością, w której są uruchamiane Tagi pomocników. Naprawimy kod w następujący sposób:

   [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

   > [!NOTE]
   > W pierwszej wersji pomocników tagów autolinku otrzymasz zawartość elementu docelowego z następującym kodem:
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
   >
   > Oznacza to, że wywoływanie `GetChildContentAsync` przy użyciu `TagHelperOutput` metody zakończono `ProcessAsync` . Jak wspomniano wcześniej, ponieważ dane wyjściowe są buforowane, ostatni pomocnik tagu do uruchomienia usługi WINS. Rozwiązano problem z następującym kodem:
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
   >
   > Powyższy kod sprawdza, czy zawartość została zmodyfikowana, a jeśli ma, pobiera zawartość z bufora wyjściowego.

1. Uruchom aplikację i sprawdź, czy dwa linki działają zgodnie z oczekiwaniami. Mimo że pomocnika tagów autokonsolidatora jest poprawna i kompletna, ma delikatny problem. Jeśli pomocnik tagów WWW zostanie uruchomiony w pierwszej kolejności, linki www nie będą poprawne. Zaktualizuj kod, dodając `Order` Przeciążenie, aby kontrolować kolejność, w której jest uruchamiany tag. `Order`Właściwość określa kolejność wykonywania względem innych pomocników tagów ukierunkowanych na ten sam element. Domyślna wartość kolejności wynosi zero, a wystąpienia o niższych wartościach są wykonywane jako pierwsze.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]

   Poprzedni kod gwarantuje, że pomocnik tagów HTTP jest uruchamiany przed pomocnikiem tagów WWW. Zmień `Order` na `MaxValue` i sprawdź, czy znaczniki wygenerowane dla tagu www są nieprawidłowe.

## <a name="inspect-and-retrieve-child-content"></a>Inspekcja i pobieranie zawartości podrzędnej

Pomocnicy tagów udostępniają kilka właściwości do pobierania zawartości.

* Wynik `GetChildContentAsync` może być dołączany do `output.Content` .
* Wyniki z programu można sprawdzić `GetChildContentAsync` `GetContent` .
* Jeśli zmodyfikujesz `output.Content` , treść TagHelper nie zostanie wykonana ani renderowana, chyba że wywołasz metodę `GetChildContentAsync` , jak w przypadku przykładu autokonsolidatora:

[!code-csharp[](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

* Wiele wywołań `GetChildContentAsync` zwraca tę samą wartość i nie ponownie wykonuje `TagHelper` treści, chyba że przekażesz fałszywy parametr wskazujący, że nie należy używać buforowanego wyniku.

## <a name="load-minified-partial-view-taghelper"></a>Załaduj widok częściowy zminimalizowanego TagHelper

W środowiskach produkcyjnych można ulepszyć wydajność, ładując częściowe widoki zminimalizowanego. Aby skorzystać z zminimalizowanego widoku częściowego w środowisku produkcyjnym:

* Utwórz/Skonfiguruj proces przed kompilacją, który minifies częściowe widoki.
* Użyj poniższego kodu, aby załadować zminimalizowanego częściowe widoki w środowiskach innych niż programistyczne.

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/MinifiedVersionTagHelper.cs?name=snippet)]
