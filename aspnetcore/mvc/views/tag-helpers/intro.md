---
title: Pomocnicy tagów w ASP.NET Core
author: rick-anderson
description: Dowiedz się, czym są pomocnicy tagów i jak ich używać w ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 03/18/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/intro
ms.openlocfilehash: d2791422e50082eab51c0735e2209e5b829d22f2
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212506"
---
# <a name="tag-helpers-in-aspnet-core"></a>Pomocnicy tagów w ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="what-are-tag-helpers"></a>Co to są pomocnicy tagów

Pomocnicy tagów włączają kod po stronie serwera, aby uczestniczyć w tworzeniu i renderowaniu elementów HTML w Razor plikach. Na przykład wbudowana `ImageTagHelper` można dołączyć numer wersji do nazwy obrazu. Za każdym razem, gdy obraz ulegnie zmianie, serwer generuje nową unikatową wersję obrazu, dzięki czemu klienci mają zapewniony dostęp do bieżącego obrazu (zamiast nieodświeżonego obrazu w pamięci podręcznej). Istnieje wiele wbudowanych pomocników tagów dla typowych zadań, takich jak tworzenie formularzy, linków, ładowanie zasobów i inne — a nawet więcej dostępnych w publicznych repozytoriach GitHub i jako pakiety NuGet. Pomocnicy tagów są twórcy w języku C# i są elementami docelowymi elementów HTML w oparciu o nazwę elementu, nazwę atrybutu lub tag nadrzędny. Na przykład, wbudowany `LabelTagHelper` może wskazywać element HTML, `<label>` gdy `LabelTagHelper` atrybuty są stosowane. Jeśli znasz [pomocników HTML](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), pomocników tagów zmniejszają jawne przejścia między językami HTML i C# w Razor widokach. W wielu przypadkach pomocników HTML udostępniają alternatywne podejście do osoby pomagającej z określonym znacznikiem, ale ważne jest, aby rozpoznać, że pomocnicy tagów nie zastępują pomocników HTML i nie ma pomocy dla tagów dla każdego pomocnika HTML. [Pomocnicy tagów w porównaniu do pomocników HTML](#tag-helpers-compared-to-html-helpers) objaśniają różnice bardziej szczegółowo.

## <a name="what-tag-helpers-provide"></a>Jakie pomocnicy tagów dostarczają

**Środowisko programistyczne przyjazne dla języka HTML** W większości, Razor znaczniki przy użyciu pomocników tagów wyglądają jak standardowa html. Projektanci frontonu z obsługą języka HTML/CSS/JavaScript można edytować Razor bez uczenia składni języka C# Razor .

**Rozbudowane środowisko IntelliSense do tworzenia kodu HTML i Razor adiustacji** jest to bardzo zróżnicowane dla pomocników HTML, poprzednie podejście do tworzenia znaczników w programie po stronie serwera w Razor widokach. [Pomocnicy tagów w porównaniu do pomocników HTML](#tag-helpers-compared-to-html-helpers) objaśniają różnice bardziej szczegółowo. [Obsługa technologii IntelliSense dla pomocników tagów](#intellisense-support-for-tag-helpers) objaśnia środowisko IntelliSense. Nawet deweloperzy z Razor składnią języka c# są wydajniejszi przy użyciu pomocników tagów niż pisanie znaczników języka c# Razor .

**Sposób zwiększania produktywności i tworzenia bardziej niezawodnego, niezawodnego i możliwego do utrzymania kodu przy użyciu informacji dostępnych tylko na serwerze** Na przykład historycznie mantrą przy aktualizacji obrazów miało na celu zmianę nazwy obrazu po zmianie obrazu. Obrazy powinny być agresywnie buforowane ze względu na wydajność, a jeśli nie zmienisz nazwy obrazu, jest to ryzykowne dla klientów, którzy będą otrzymywać stare kopie. Historycznie, po edytowaniu obrazu należy zmienić nazwę i wszystkie odwołania do obrazu w aplikacji sieci Web wymagały zaktualizowania. Nie tylko jest to bardzo intensywna praca, ale jest również podatna na błędy (można pominąć odwołanie, przypadkowo wprowadzić niewłaściwy ciąg itd.) Wbudowana `ImageTagHelper` można to zrobić automatycznie. Do `ImageTagHelper` nazwy obrazu można dołączyć numer wersji, więc po każdym zmianie obrazu serwer automatycznie generuje nową unikatową wersję obrazu. Klienci mają gwarancję pobrania bieżącego obrazu. Ta niezawodna oszczędność i robocizna jest ogólnie bezpłatna przy użyciu `ImageTagHelper` .

Większość wbudowanych pomocników tagów docelowo standardowe elementy HTML i udostępniają atrybuty po stronie serwera dla elementu. Na przykład `<input>` element użyty w wielu widokach w folderze *widoki/konto* zawiera `asp-for` atrybut. Ten atrybut wyodrębnia nazwę określonej właściwości modelu do renderowanego kodu HTML. Rozważ Razor Widok z następującym modelem:

```csharp
public class Movie
{
    public int ID { get; set; }
    public string Title { get; set; }
    public DateTime ReleaseDate { get; set; }
    public string Genre { get; set; }
    public decimal Price { get; set; }
}
```

Następujące Razor znaczniki:

```cshtml
<label asp-for="Movie.Title"></label>
```

Generuje następujący kod HTML:

```html
<label for="Movie_Title">Title</label>
```

`asp-for`Atrybut jest udostępniany przez `For` Właściwość w [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0). Aby uzyskać więcej informacji, zobacz [pomocnicy tagów autora](xref:mvc/views/tag-helpers/authoring) .

## <a name="managing-tag-helper-scope"></a>Zarządzanie zakresem pomocnika tagów

Zakres pomocników tagów jest kontrolowany przez kombinację `@addTagHelper` `@removeTagHelper` znaku "!".

<a name="add-helper-label"></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a>`@addTagHelper`udostępnia pomocników tagów

Jeśli utworzysz nową aplikację sieci Web ASP.NET Core o nazwie *AuthoringTagHelpers*, do projektu zostanie dodany następujący plik *widoków/_ViewImports. cshtml* :

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

`@addTagHelper`Dyrektywa powoduje, że pomocników tagów są dostępni do widoku. W takim przypadku plik widoku to *Pages/_ViewImports. cshtml*, które domyślnie są dziedziczone przez wszystkie pliki w folderze *stron* i podfolderach. Udostępnianie pomocników tagów. W powyższym kodzie użyto składni wieloznacznej (" \* "), aby określić, że wszystkie pomocniki tagów w określonym zestawie (*Microsoft. AspNetCore. MVC. TagHelpers*) będą dostępne dla każdego pliku widoku w katalogu *widoków* lub podkatalogu. Pierwszy parametr po `@addTagHelper` określeniu pomocników tagów do załadowania (używamy " \* " dla wszystkich pomocników tagów), a drugi parametr "Microsoft. AspNetCore. MVC. TagHelpers" określa zestaw zawierający pomocników tagów. *Microsoft. AspNetCore. MVC. TagHelpers* to zestaw wbudowanych pomocników tagów ASP.NET Core.

Aby uwidocznić wszystkich pomocników tagów w tym projekcie (tworząc zestaw o nazwie *AuthoringTagHelpers*), należy użyć następujących elementów:

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

Jeśli projekt zawiera `EmailTagHelper` domyślną przestrzeń nazw ( `AuthoringTagHelpers.TagHelpers.EmailTagHelper` ), można podać w pełni kwalifikowaną nazwę (FQN) pomocnika tagów:

```cshtml
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

Aby dodać pomocnika tagów do widoku przy użyciu FQN, należy najpierw dodać FQN ( `AuthoringTagHelpers.TagHelpers.EmailTagHelper` ), a następnie nazwę zestawu (*AuthoringTagHelpers*). Większość deweloperów preferuje użycie \* składni symbol wieloznaczny "". Składnia symbol wieloznaczny umożliwia wstawianie symbolu wieloznacznego " \* " jako sufiksu w FQN. Na przykład każda z poniższych dyrektyw przyniesie `EmailTagHelper` :

```cshtml
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

Jak wspomniano wcześniej, dodanie `@addTagHelper` dyrektywy do pliku *views/_ViewImports. cshtml* sprawia, że pomocnik tagów jest dostępny dla wszystkich plików widoku w katalogu *widoków* i podkatalogach. Możesz użyć `@addTagHelper` dyrektywy w określonych plikach widoku, jeśli chcesz, aby uwidocznić pomocnika tagów tylko w tych widokach.

<a name="remove-razor-directives-label"></a>

### <a name="removetaghelper-removes-tag-helpers"></a>`@removeTagHelper`usuwa pomocników tagów

`@removeTagHelper`Ma takie same dwa parametry jak `@addTagHelper` i usuwa pomocnika tagu, który został wcześniej dodany. Na przykład `@removeTagHelper` zastosowana do określonego widoku powoduje usunięcie określonego pomocnika tagów z widoku. Użycie `@removeTagHelper` w pliku *viewss/Folder/_ViewImports. cshtml* usuwa określony pomocnik tagów ze wszystkich widoków w *folderze*.

### <a name="controlling-tag-helper-scope-with-the-_viewimportscshtml-file"></a>Kontrolowanie zakresu pomocnika tagów przy użyciu pliku *_ViewImports. cshtml*

Można dodać plik *_ViewImports. cshtml* do dowolnego folderu widoku, a aparat widoku stosuje dyrektywy zarówno z tego pliku, jak i plików *views/_ViewImports. cshtml* . Po dodaniu pustego pliku *views/Home/_ViewImports. cshtml* dla widoków *głównych* nie można zmienić, ponieważ plik *_ViewImports. cshtml* jest dodatkiem. Wszystkie `@addTagHelper` dyrektywy dodawane do pliku *views/Home/_ViewImports. cshtml* (które nie znajdują się w pliku *widoków domyślnych/_ViewImports. cshtml* ) uwidaczniają te pomocników tagów tylko w folderze *głównym* .

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a>Rezygnacja z poszczególnych elementów

Pomocnika tagów można wyłączyć na poziomie elementu za pomocą znaku rezygnacji z pomocnika tagów ("!"). Na przykład `Email` Walidacja jest wyłączona w elemencie `<span>` z znakiem rezygnacji pomocnika tagu:

```cshtml
<!span asp-validation-for="Email" class="text-danger"></!span>
```

Należy zastosować znak rezygnacji pomocnika tagów do otwierającego i zamykającego znacznika. (Edytor programu Visual Studio automatycznie dodaje znak rezygnacji do tagu zamykającego po dodaniu go do tagu otwierającego). Po dodaniu znaku rezygnacji atrybuty pomocnika elementu i tagu nie będą już wyświetlane w postaci charakterystycznej czcionki.

<a name="prefix-razor-directives-label"></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a>Używanie `@tagHelperPrefix` , aby utworzyć jawne użycie pomocnika tagów

`@tagHelperPrefix`Dyrektywa pozwala określić ciąg prefiksu tagu w celu włączenia obsługi pomocnika tagów i zapewnienia jawnego użycia pomocnika tagów. Można na przykład dodać następujące znaczniki do pliku *views/_ViewImports. cshtml* :

```cshtml
@tagHelperPrefix th:
```

W poniższym obrazie kodu prefiks pomocnika tagów jest ustawiony na `th:` , więc tylko te elementy, które używają `th:` pomocników tagów prefiksu (w przypadku elementów z obsługą pomocniczych tagów mają charakterystyczną czcionkę). `<label>`Elementy i `<input>` mają prefiks pomocnika tagów i są obsługiwane przez pomocnika tagów, podczas gdy `<span>` element nie jest.

![image (obraz)](intro/_static/thp.png)

Te same reguły hierarchii, które mają zastosowanie do programu, mają `@addTagHelper` również zastosowanie do `@tagHelperPrefix` .

## <a name="self-closing-tag-helpers"></a>Samozamykające pomocników tagów

Wielu pomocników tagów nie można używać jako tagów samozamykających. Niektóre pomocnicy tagów zostały zaprojektowane jako Tagi samozamykające się. Użycie pomocnika tagów, który nie został zaprojektowany do samodzielnego zamykania, pomija renderowane dane wyjściowe. Samodzielne zamknięcie pomocnika tagów skutkuje tagiem samozamykającym w renderowanych danych wyjściowych. Aby uzyskać więcej informacji, zobacz [tę uwagę](xref:mvc/views/tag-helpers/authoring#self-closing) w [ułatwieniach tworzenia tagów](xref:mvc/views/tag-helpers/authoring).

## <a name="c-in-tag-helpers-attributedeclaration"></a>Język C# w atrybutach/deklaracji pomocników tagów 

Pomocnicy tagów nie zezwalają na C# w obszarze atrybutu lub deklaracji tagu elementu. Na przykład następujący kod jest nieprawidłowy:

```cshtml
<input asp-for="LastName"  
       @(Model?.LicenseId == null ? "disabled" : string.Empty) />
```

Poprzedni kod można napisać jako:

```cshtml
<input asp-for="LastName" 
       disabled="@(Model?.LicenseId == null)" />
```

## <a name="intellisense-support-for-tag-helpers"></a>Obsługa technologii IntelliSense dla pomocników tagów

Podczas tworzenia nowej aplikacji sieci Web ASP.NET Core w programie Visual Studio zostanie dodany pakiet NuGet "Microsoft. AspNetCore. Razor .. Narzędzia ". Jest to pakiet, który dodaje narzędzia pomocnika tagów.

Rozważ zapisanie `<label>` elementu HTML. Gdy tylko wprowadzisz `<l` w edytorze programu Visual Studio, IntelliSense wyświetla pasujące elementy:

![image (obraz)](intro/_static/label.png)

Możesz nie tylko uzyskać Pomoc HTML, ale ikonę (" @" symbol with "<>" w tym obszarze).

![image (obraz)](intro/_static/tagSym.png)

identyfikuje element jako obiekt przeznaczony dla pomocników tagów. Czyste elementy HTML (takie jak `fieldset` ) wyświetlają ikonę "<>".

Czysty tag HTML `<label>` wyświetla tag HTML (z domyślnym motywem kolorów programu Visual Studio) w czcionkze brązowym, atrybuty w kolorze czerwonym i wartości atrybutów w kolorze niebieskim.

![image (obraz)](intro/_static/LableHtmlTag.png)

Po wprowadzeniu funkcja `<label` IntelliSense wyświetla dostępne atrybuty HTML/CSS i atrybuty dostosowane przez pomocnika tagów:

![image (obraz)](intro/_static/labelattr.png)

Uzupełnianie instrukcji IntelliSense umożliwia wprowadzenie klawisza Tab w celu ukończenia instrukcji z wybraną wartością:

![image (obraz)](intro/_static/stmtcomplete.png)

Gdy tylko atrybut pomocnika tagów zostanie wprowadzony, czcionki tagów i atrybutów zmienią się. Przy użyciu domyślnego motywu kolorów "niebieska" lub "jasne" programu Visual Studio czcionka jest pogrubiona. Jeśli używasz motywu "ciemny", czcionka jest pogrubiona. Obrazy z tego dokumentu zostały wykonane przy użyciu motywu domyślnego.

![image (obraz)](intro/_static/labelaspfor2.png)

Możesz wprowadzić skrót do programu Visual Studio *CompleteWord* (kombinacja klawiszy CTRL + SPACJA jest [wartością domyślną](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) wewnątrz cudzysłowu podwójnego ("") i jesteś teraz w języku c#, podobnie jak w przypadku klasy języka c#. Technologia IntelliSense wyświetla wszystkie metody i właściwości w modelu strony. Metody i właściwości są dostępne, ponieważ typ właściwości to `ModelExpression` . Na poniższej ilustracji edytuję `Register` Widok, więc `RegisterViewModel` jest dostępny.

![image (obraz)](intro/_static/intellemail.png)

Technologia IntelliSense wyświetla listę właściwości i metod dostępnych dla modelu na stronie. Rozbudowane środowisko IntelliSense ułatwia wybranie klasy CSS:

![image (obraz)](intro/_static/iclass.png)

![image (obraz)](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a>Pomocnicy tagów w porównaniu do pomocników HTML

Pomocnicy tagów dołącza do elementów HTML w Razor widokach, natomiast [pomocników HTML](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) są wywoływane jako metody odnoszące się do języka HTML w Razor widokach. Weź pod uwagę następujące Razor znaczniki, które tworzą etykietę HTML z klasą CSS "Caption":

```cshtml
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

Symbol at ( `@` ) mówi Razor , że jest to początek kodu. Następne dwa parametry ("FirstName" i "First Name:") są ciągami, więc [technologia IntelliSense](/visualstudio/ide/using-intellisense) nie może pomóc. Ostatni argument:

```cshtml
new {@class="caption"}
```

Jest obiektem anonimowym używanym do reprezentowania atrybutów. Ponieważ `class` jest zarezerwowanym słowem kluczowym w języku c#, należy użyć `@` symbolu, aby wymusić, że C# interpretuje `@class=` jako symbol (nazwa właściwości). Do projektanta frontonu (ktoś zaznajomiony z językiem HTML/CSS/JavaScript i innymi technologiami klienta, ale nie zna języka C# i Razor ), większość linii jest obca. Cały wiersz musi być utworzony bez pomocy na platformie IntelliSense.

Przy użyciu `LabelTagHelper` , tę samą adiustację można napisać jako:

```cshtml
<label class="caption" asp-for="FirstName"></label>
```

Za pomocą wersji pomocnika tagów, gdy tylko wprowadzisz `<l` w edytorze programu Visual Studio, IntelliSense wyświetla pasujące elementy:

![image (obraz)](intro/_static/label.png)

Technologia IntelliSense pomaga napisać cały wiersz.

Poniższy obraz kodu przedstawia część formularza widoku *widoki/Account/register. cshtml* Razor wygenerowanego na podstawie szablonu MVC ASP.NET 4.5. x dołączonego do programu Visual Studio.

![image (obraz)](intro/_static/regCS.png)

Edytor programu Visual Studio Wyświetla kod C# z szarym tłem. Na przykład `AntiForgeryToken` pomocnik HTML:

```cshtml
@Html.AntiForgeryToken()
```

jest wyświetlany szare tło. Większość znaczników w widoku rejestru to C#. Porównaj ją z równoważną metodą przy użyciu pomocników tagów:

![image (obraz)](intro/_static/regTH.png)

Oznakowanie jest znacznie bardziej czytelne i łatwiejsze do odczytania, edycji i konserwacji niż podejście pomocników HTML. Kod w języku C# jest zredukowany do minimum, o którym serwer musi wiedzieć. Edytor programu Visual Studio Wyświetla znaczniki wskazywane przez pomocnika tagów w czcionce charakterystycznej.

Weź pod uwagę grupę *poczty e-mail* :

[!code-cshtml[](intro/sample/Register.cshtml?range=12-18)]

Każdy atrybut "ASP-" ma wartość "email", ale "Poczta E-mail" nie jest ciągiem. W tym kontekście "Poczta E-mail" jest właściwością wyrażenia modelu C# dla `RegisterViewModel` .

Edytor programu Visual Studio pomaga napisać **wszystkie** znaczniki w podejściu pomocnika tagów w formularzu rejestracji, natomiast program Visual Studio nie zapewnia pomocy w przypadku większości kodu w podejściu do pomocników html. [Obsługa technologii IntelliSense dla pomocników tagów](#intellisense-support-for-tag-helpers) prowadzi do szczegółowych informacji na temat pracy z pomocnikami tagów w edytorze programu Visual Studio.

## <a name="tag-helpers-compared-to-web-server-controls"></a>Pomocnicy tagów w porównaniu do kontrolek serwera sieci Web

* Pomocnicy tagów nie są własnością elementu, z którym są skojarzone; po prostu biorą udział w renderowaniu elementu i zawartości. [Kontrolki serwera sieci Web](https://msdn.microsoft.com/library/7698y1f0.aspx) ASP.NET są deklarowane i wywoływane na stronie.

* [Kontrolki serwera sieci Web](https://msdn.microsoft.com/library/zsyt68f1.aspx) mają nieuproszczony cykl życia, który może sprawiać, że programowanie i debugowanie jest trudne.

* Kontrolki serwera sieci Web umożliwiają dodawanie funkcji do elementów Document Object Model klienta (DOM) przy użyciu formantu klienta. Pomocnicy tagów nie mają modelu DOM.

* Formanty serwera sieci Web obejmują automatyczne wykrywanie przeglądarki. Pomocnicy tagów nie znają przeglądarki.

* Wiele pomocników tagów może działać na tym samym elemencie (zobacz [unikanie konfliktów pomocnika tagów](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) ), podczas gdy zazwyczaj nie można tworzyć kontrolek serwera sieci Web.

* Pomocnicy tagów mogą modyfikować tag i zawartość elementów HTML, do których należą zakresy, ale nie mogą bezpośrednio modyfikować niczego na stronie. Formanty serwera sieci Web mają mniej konkretny zakres i mogą wykonywać akcje, które wpływają na inne części strony. Włączanie niezamierzonych efektów ubocznych.

* Formanty serwera sieci Web używają konwerterów typów do konwertowania ciągów na obiekty. Korzystając z pomocników tagów, pracujesz natywnie w języku C#, więc nie musisz wykonywać konwersji typów.

* Formanty serwera sieci Web używają elementu [System. ComponentModel](/dotnet/api/system.componentmodel) , aby zaimplementować zachowanie składników i formantów w czasie wykonywania oraz w czasie projektowania. `System.ComponentModel`zawiera klasy bazowe i interfejsy służące do implementowania atrybutów i konwerterów typów, powiązania ze źródłami danych i składnikami licencjonowania. Kontrast służący do tagowania pomocników, które zazwyczaj pochodzą z `TagHelper` , i `TagHelper` klasy bazowej uwidacznia tylko dwie metody `Process` i `ProcessAsync` .

## <a name="customizing-the-tag-helper-element-font"></a>Dostosowywanie czcionki elementu pomocnika tagów

Możesz dostosować czcionkę i kolorowanie przy użyciu **Tools**  >  **opcji**narzędzia  >  **Environment**  >  **czcionki i kolory**środowiska:

![image (obraz)](intro/_static/fontoptions2.png)

[!INCLUDE[](~/includes/built-in-TH.md)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Autorzy tagów](xref:mvc/views/tag-helpers/authoring)
* [Praca z formularzami](xref:mvc/views/working-with-forms)
* [TagHelperSamples w usłudze GitHub](https://github.com/dpaquette/TagHelperSamples) zawiera przykłady pomocnika tagów do pracy z [Bootstrap](https://getbootstrap.com/).
