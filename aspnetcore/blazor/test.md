---
title: Testowanie składników w ASP.NET Core Blazor
author: guardrex
description: Dowiedz się, jak testować componments w Blazor aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/10/2020
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
uid: blazor/test
ms.openlocfilehash: cd4aee66fd6df6cc0ce520d8ca66e0a2cf130eff
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054869"
---
# <a name="test-components-in-aspnet-core-no-locblazor"></a>Testowanie składników w ASP.NET Core Blazor

[Egil Hansen](https://egilhansen.com/)

Testowanie jest ważnym aspektem tworzenia stabilnych i konserwowanych oprogramowania.

Aby przetestować Blazor składnik, *składnik objęty testem* (Wytnij) to:

* Renderowane z odpowiednimi danymi wejściowymi dla testu.
* W zależności od typu wykonanego testu, który może podlegać interakcji lub modyfikacji. Na przykład programy obsługi zdarzeń mogą być wyzwalane, takie jak `onclick` zdarzenie dla przycisku.
* Inspekcja pod kątem oczekiwanych wartości.

## <a name="test-approaches"></a>Podejścia testowe

Dwa typowe podejścia do testowania Blazor składników to kompleksowe (E2E) testowanie i testowanie jednostkowe:

* **Testowanie jednostkowe** : [testy jednostkowe](/dotnet/core/testing/) są napisywane przy użyciu biblioteki testów jednostkowych, która zapewnia:
  * Renderowanie składnika.
  * Inspekcja danych wyjściowych i stanu składnika.
  * Wyzwalanie obsługi zdarzeń i metod cyklu życia.
  * Potwierdza, że zachowanie składnika jest poprawne.

  [bUnit](https://github.com/egil/bUnit) to przykład biblioteki, która umożliwia Razor testowanie jednostkowe składnika.

* **Testowanie E2E** : Test Runner uruchamia Blazor aplikację zawierającą wycięte i automatyzuje wystąpienie przeglądarki. Narzędzie do testowania sprawdza i współdziała z wycinaniem przez przeglądarkę. [Selen](https://github.com/SeleniumHQ/selenium) to przykład platformy testowania E2E, która może być używana z Blazor aplikacjami.

W teście jednostkowym Blazor dotyczy tylko składnika ( Razor /c #). Zależności zewnętrzne, takie jak usługi i międzyoperacyjność w programie JS, muszą być makietne. W testowaniu E2E Blazor składnik i całość infrastruktury pomocniczej są częścią testu, w tym CSS, js i interfejsów API Dom i Browser.

*Zakres testów* opisuje, jak obszerne są testy. Zakres testów ma zwykle wpływ na szybkość testów. Testy jednostkowe działają w podzestawie podsystemów aplikacji i zwykle są wykonywane w milisekundach. Testy E2E, które testują szeroką grupę podsystemów aplikacji, mogą potrwać kilka sekund. 

Testy jednostkowe umożliwiają również dostęp do wystąpienia wycinania, co pozwala na inspekcję i weryfikację stanu wewnętrznego składnika. Zwykle nie jest to możliwe w testowaniu E2E.

W odniesieniu do środowiska składnika testy E2E muszą mieć pewność, że oczekiwany stan środowiska został osiągnięty przed rozpoczęciem weryfikacji. W przeciwnym razie wynik jest nieprzewidywalny. W przypadku testów jednostkowych, renderowanie wycinania i cyklu życia testu jest bardziej zintegrowane, co poprawia stabilność testu.

Testowanie E2E polega na uruchamianiu wielu procesów, operacji we/wy na dysku oraz innych działaniach podsystemu, które często prowadzą do słabej niezawodności testu. Testy jednostkowe są zazwyczaj izolowane od tych rodzajów problemów.

Poniższa tabela zawiera podsumowanie różnic między dwoma podejściami do testowania.

| Możliwość                       | Testowanie jednostek                     | Testowanie E2E                             |
| -------------------------------- | -------------------------------- | --------------------------------------- |
| Zakres testu                       | Blazor tylko składnik ( Razor /c #) | Blazor składnik ( Razor /c #) z CSS/JS |
| Czas wykonywania testu              | )                     | Sekundy                                 |
| Dostęp do wystąpienia składnika | Tak                              | Nie                                      |
| Poufne dla środowiska     | Nie                               | Tak                                     |
| Niezawodność                      | Bardziej niezawodne                    | Mniej niezawodne                           |

## <a name="choose-the-most-appropriate-test-approach"></a>Wybierz najbardziej odpowiednie podejście testowe

Rozważmy scenariusz podczas wybierania typu testów do wykonania. Niektóre zagadnienia zostały opisane w poniższej tabeli.

| Scenariusz | Sugerowane podejście | Uwagi |
| -------- | ------------------ | ------- |
| Składnik bez logiki międzyoperacyjna JS | Testowanie jednostek | Jeśli w składniku nie ma zależności od współdziałania ze JS Blazor , składnik można przetestować bez dostępu do js lub interfejsu API modelu DOM. W tym scenariuszu nie ma żadnych wad, aby wybrać testy jednostkowe. |
| Składnik z prostą logiką międzyoperacyjną JS | Testowanie jednostek | Jest to typowy element dla składników służących do wykonywania zapytań względem modelu DOM lub wyzwalania animacji za pomocą międzyoperacyjnego JS. Testowanie jednostkowe jest zwykle preferowane w tym scenariuszu, ponieważ jest to proste, aby zasymulować interakcję JS za pomocą <xref:Microsoft.JSInterop.IJSRuntime> interfejsu. |
| Składnik, który zależy od złożonego kodu JS | Testowanie jednostkowe i oddzielne testowanie JS | Jeśli składnik używa interfejsu JS Interop do wywołania dużej lub złożonej biblioteki JS, ale interakcja między Blazor biblioteką składnika i JS jest prosta, najlepszym rozwiązaniem jest prawdopodobnie traktowanie składnika i biblioteki js lub kodu jako dwóch oddzielnych części i przetestowanie każdego z nich. Przetestuj Blazor składnik przy użyciu biblioteki testów jednostkowych i przetestuj go przy użyciu biblioteki testowej js. |
| Składnik z logiką, który zależy od manipulowania JS w modelu DOM przeglądarki | Testowanie E2E | Gdy funkcje składnika są zależne od kodu JS i jego manipulowania modelem DOM, sprawdź, czy zarówno JS, jak i Blazor kod w teście E2E. Jest to podejście, które deweloperzy architektury pobrały Blazor za pomocą Blazor logiki renderowania przeglądarki, która ma ściśle połączony kod C# i JS. Kod C# i JS musi współpracować ze sobą w celu poprawnego renderowania Blazor składników w przeglądarce.
| Składnik zależny od biblioteki składników innych firm z niezależnymi zależnościami | Testowanie E2E | Gdy funkcjonalność składnika jest zależna od biblioteki składników innej firmy, która ma trudne do makiety zależności, takich jak program JS Interop, testowanie E2E może być jedyną opcją do testowania składnika. |

## <a name="test-components-with-bunit"></a>Składniki testowe z bUnit

Nie istnieje oficjalne środowisko do testowania firmy Microsoft dla programu Blazor , ale projekt oparty na społeczności [bUnit](https://github.com/egil/bUnit) zapewnia wygodny sposób na składniki testów jednostkowych Blazor .

> [!NOTE]
> bUnit jest biblioteką testową innej firmy i nie jest obsługiwana ani utrzymywana przez firmę Microsoft.

bUnit współpracuje z strukturami testowania ogólnego przeznaczenia, takimi jak [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [nunit](https://nunit.org/)i [xUnit](https://xunit.github.io/). Te platformy testowe sprawiają, że testy bUnit wyglądają i przypominają zwykłe testy jednostkowe. testy bUnit zintegrowane z platformą testowania ogólnego przeznaczenia są zwykle wykonywane przy użyciu:

* [Eksplorator testów programu Visual Studio](/visualstudio/test/run-unit-tests-with-test-explorer).
* [`dotnet test`](/dotnet/core/tools/dotnet-test) Polecenie interfejsu wiersza polecenia w powłoce poleceń.
* Zautomatyzowany potok testowania DevOps.

> [!NOTE]
> Koncepcje testowe i implementacje testów w różnych strukturach testów są podobne, ale nie są identyczne. Wskazówki można znaleźć w dokumentacji platformy testowej.

Poniżej przedstawiono strukturę testu bUnit na `Counter` składniku w aplikacji na podstawie Blazor szablonu projektu. `Counter`Składnik wyświetla i zwiększa licznik na podstawie użytkownika wybierającego przycisk na stronie:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

Poniższy test bUnit sprawdza, czy licznik wycinania jest zwiększany prawidłowo, gdy przycisk jest zaznaczony:

```csharp
[Fact]
public void CounterShouldIncrementWhenSelected()
{
    // Arrange
    using var ctx = new TestContext();
    var cut = ctx.RenderComponent<Counter>();
    var paraElm = cut.Find("p");

    // Act
    cut.Find("button").Click();
    var paraElmText = paraElm.TextContent;

    // Assert
    paraElmText.MarkupMatches("Current count: 1");
}
```

W każdym kroku testu odbywają się następujące działania:

* *Porządkowanie* : `Counter` składnik jest renderowany przy użyciu bUnit `TestContext` . Element akapitu wycinania ( `<p>` ) został znaleziony i przypisany do `paraElm` .

* *Działanie* : element Button ( `<button>` ) jest zlokalizowany, a następnie wybierany przez wywołanie `Click` , które powinno zwiększyć licznik i zaktualizować zawartość znacznika akapitu ( `<p>` ). Zawartość tekstowa elementu akapitu jest uzyskiwana przez wywołanie `TextContent` .

* *Assert* : `MarkupMatches` jest wywoływana dla zawartości tekstowej w celu sprawdzenia, czy pasuje do oczekiwanego ciągu, czyli `Current count: 1` .

> [!NOTE]
> `MarkupMatches`Metoda Assert różni się od zwykłego potwierdzenia porównania ciągów (na przykład `Assert.Equal("Current count: 1", paraElmText);` ) `MarkupMatches` wykonuje porównanie semantyczne danych wejściowych i oczekiwanych znaczników HTML. Porównanie semantyczne ma świadomość semantyki języka HTML, co oznacza, że ignorowanie nieznaczących białych znaków. Powoduje to bardziej stabilne testy. Aby uzyskać więcej informacji, zobacz [Dostosowywanie semantycznego porównania kodu HTML](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Wprowadzenie z bUnit](https://bunit.egilhansen.com/docs/getting-started/): bUnit instrukcje obejmują wskazówki dotyczące tworzenia projektu testowego, odwoływania się do pakietów platformy testowania i kompilowania i uruchamiania testów.
