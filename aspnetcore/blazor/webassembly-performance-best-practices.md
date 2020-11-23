---
title: ASP.NET Core Blazor WebAssembly najlepszych rozwiązań dotyczących wydajności
author: pranavkm
description: Wskazówki dotyczące zwiększania wydajności Blazor WebAssembly aplikacji ASP.NET Core i unikania typowych problemów z wydajnością.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 10/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: cc090b4e56745e6b010e4a7ee17332b0d3a95560
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417386"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a>ASP.NET Core Blazor WebAssembly najlepszych rozwiązań dotyczących wydajności

Autorzy [Pranav Krishnamoorthy](https://github.com/pranavkm) i [Steve Sanderson](https://github.com/SteveSandersonMS)

Blazor WebAssembly jest starannie zaprojektowany i zoptymalizowany pod kątem wysokiej wydajności w najbardziej realistycznych scenariuszach interfejsu użytkownika aplikacji. Jednak generowanie najlepszych wyników zależy od deweloperów korzystających z odpowiednich wzorców i funkcji. Należy wziąć pod uwagę następujące aspekty:

* **Przepływność środowiska uruchomieniowego**: kod platformy .NET działa w interpreterze środowiska uruchomieniowego webassembly, więc PRZEPŁYWNOŚĆ procesora CPU jest ograniczona. W wymagających scenariuszach aplikacja korzysta z [optymalizacji szybkości renderowania](#optimize-rendering-speed).
* **Czas uruchamiania**: aplikacja przenosi środowisko uruchomieniowe platformy .NET do przeglądarki, dlatego ważne jest, aby użyć funkcji [minimalizujących rozmiar pobieranych aplikacji](#minimize-app-download-size).

## <a name="optimize-rendering-speed"></a>Optymalizuj szybkość renderowania

Poniższe sekcje zawierają zalecenia dotyczące minimalizowania obciążeń renderowania i zwiększania czasu odpowiedzi interfejsu użytkownika. Poniższe porady mogą łatwo wprowadzić *dziesięć lub większą poprawę* w zakresie szybkości renderowania interfejsu użytkownika.

### <a name="avoid-unnecessary-rendering-of-component-subtrees"></a>Unikaj niepotrzebnego renderowania poddrzewa składników

W środowisku uruchomieniowym składniki istnieją jako hierarchia. Składnik główny ma składniki podrzędne. Z kolei dzieci mają własne składniki podrzędne i tak dalej. W przypadku wystąpienia zdarzenia, takiego jak użytkownik, który wybiera przycisk, w ten sposób Blazor decyduje o tym, które składniki mają być przerenderowane:

 1. Samo zdarzenie jest wysyłane do składnika, który wyrenderuje procedurę obsługi zdarzenia. Po wykonaniu procedury obsługi zdarzeń ten składnik jest renderowany.
 1. Zawsze, gdy dowolny składnik jest przerenderowany, dostarcza nową kopię wartości parametrów do poszczególnych składników podrzędnych.
 1. W przypadku otrzymania nowego zestawu wartości parametrów każdy składnik wybiera, czy ma być przerenderowany. Domyślnie składniki są rerenderowane, jeśli wartości parametrów mogły ulec zmianie (na przykład, jeśli są to obiekty modyfikowalne).

Ostatnie dwa kroki tej sekwencji kontynuują rekursywnie hierarchię składników. W wielu przypadkach całe poddrzewo jest renderowane. Oznacza to, że zdarzenia ukierunkowane na składniki wyższego poziomu mogą spowodować kosztowne procesy ponownego renderowania, ponieważ wszystko poniżej tego punktu musi być renderowane.

Jeśli chcesz przerwać ten proces i zapobiec obsłużeniu rekursji do określonego poddrzewa, możesz:

 * Upewnij się, że wszystkie parametry określonego składnika są typami pierwotnymi (na przykład,,,, `string` `int` `bool` `DateTime` i innych). Wbudowana logika do wykrywania zmian automatycznie pomija ponowne renderowanie, jeśli żadna z tych wartości parametrów nie została zmieniona. Jeśli renderuje składnik podrzędny z `<Customer CustomerId="@item.CustomerId" />` , gdzie `CustomerId` jest `int` wartością, wówczas nie jest on ponownie renderowany z wyjątkiem `item.CustomerId` zmian.
 * Jeśli musisz zaakceptować wartości parametrów niepierwotnych, takie jak niestandardowe typy modeli, wywołania zwrotne zdarzeń lub <xref:Microsoft.AspNetCore.Components.RenderFragment> wartości, możesz przesłonić, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> Aby kontrolować decyzję o tym, czy należy renderować, co zostało opisane w sekcji [ `ShouldRender` użycie](#use-of-shouldrender) .

Po pominięciu odrenderowania całych poddrzew może być możliwe usunięcie ogromnej większości kosztów renderowania w przypadku wystąpienia zdarzenia.

W celu pominięcia odwzorowania tej części interfejsu użytkownika warto uwzględnić składniki podrzędne. Jest to prawidłowy sposób zmniejszenia kosztów renderowania składnika nadrzędnego.

#### <a name="use-of-shouldrender"></a>Korzystanie z `ShouldRender`

W przypadku tworzenia składnika tylko interfejsu użytkownika, który nigdy nie zmienia się po początkowym renderowaniu (bez względu na wartości parametrów), skonfiguruj, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> Aby zwrócić `false` :

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

Jeśli składnik wymaga ponownej renderowania, gdy jego wartości parametrów są zmieniane w określony sposób, można użyć pól prywatnych do śledzenia niezbędnych informacji w celu wykrycia zmian. W poniższym przykładzie `shouldRender` jest oparta na sprawdzaniu dowolnego rodzaju zmiany lub mutacji, które powinny monitować o przerenderowanie. `prevOutboundFlightId` i `prevInboundFlightId` Śledź informacje dotyczące następnej potencjalnej aktualizacji:

```razor
@code {
    [Parameter]
    public FlightInfo OutboundFlight { get; set; }
    
    [Parameter]
    public FlightInfo InboundFlight { get; set; }

    private int prevOutboundFlightId;
    private int prevInboundFlightId;
    private bool shouldRender;

    protected override void OnParametersSet()
    {
        shouldRender = OutboundFlight.FlightId != prevOutboundFlightId
            || InboundFlight.FlightId != prevInboundFlightId;

        prevOutboundFlightId = OutboundFlight.FlightId;
        prevInboundFlightId = InboundFlight.FlightId;
    }

    protected override void ShouldRender() => shouldRender;

    // Note that 
}
```

W powyższym kodzie, program obsługi zdarzeń może być również ustawiony `shouldRender` na `true` tak, aby składnik został przerenderowany po zdarzeniu.

W przypadku większości składników ten poziom kontroli ręcznej nie jest konieczny. Należy tylko zależeć od pomijania poddrzew renderowania, jeśli te poddrzewa są szczególnie kosztowne do renderowania i powodują opóźnienia interfejsu użytkownika.

Aby uzyskać więcej informacji, zobacz <xref:blazor/components/lifecycle>.

::: moniker range=">= aspnetcore-5.0"

### <a name="virtualization"></a>Wirtualizacja

Podczas renderowania dużych ilości interfejsu użytkownika w obrębie pętli, na przykład lista lub siatka z tysiącami wpisów, zawiera ilość operacji renderowania może prowadzić do opóźnienia w renderowaniu interfejsu użytkownika i w ten sposób słabe środowisko użytkownika. W przypadku, gdy użytkownik widzi tylko niewielką liczbę elementów jednocześnie bez przewijania, wydaje się, że wastefule to wiele czasu renderowania elementów, które nie są obecnie widoczne.

Aby rozwiązać ten krok, program Blazor udostępnia wbudowany [ `<Virtualize>` składnik](xref:blazor/components/virtualization) , który tworzy wygląd i zachowanie przewijania z arbitralnie dużej listy, ale w rzeczywistości renderuje tylko elementy listy, które znajdują się w bieżącym okienku ekranu przewijania. Na przykład oznacza to, że aplikacja może mieć listę z 100 000 wpisów, ale płacisz kosztem renderowania 20 elementów, które są widoczne w dowolnym momencie. Użycie `<Virtualize>` składnika może skalować wydajność interfejsu użytkownika według kolejności wielkości.

`<Virtualize>` można użyć, gdy:

 * Renderowanie zestawu elementów danych w pętli.
 * Większość elementów nie jest widoczna z powodu przewijania.
 * Renderowane elementy mają dokładnie taki sam rozmiar. Gdy użytkownik przewija do dowolnego punktu, składnik może obliczyć widoczne elementy do wyświetlenia.

Poniżej przedstawiono przykład niezwirtualizowanej listy:

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    }
</div>
```

Jeśli `allFlights` Kolekcja zawiera 10 000 elementów, tworzy wystąpienie i renderuje wystąpienia 10 000 `<FlightSummary>` składników. W porównaniu poniżej przedstawiono przykład listy zwirtualizowanej:

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    </Virtualize>
</div>
```

Mimo że wynikowy interfejs użytkownika wygląda tak samo dla użytkownika, w tle, tylko tworzy wystąpienie składnika i renderuje jako wiele `<FlightSummary>` wystąpień wymagane do wypełnienia regionu przewijania. Zestaw `<FlightSummary>` wyświetlanych wystąpień jest ponownie obliczany i renderowany podczas przewijania użytkownika.

`<Virtualize>` ma także inne korzyści. Na przykład gdy składnik żąda danych z zewnętrznego interfejsu API, `<Virtualize>` zezwala składnikowi na pobieranie tylko wycinków rekordów, które odpowiadają bieżącemu widocznemu regionowi, zamiast pobierać wszystkie dane z kolekcji.

Aby uzyskać więcej informacji, zobacz <xref:blazor/components/virtualization>.

::: moniker-end

### <a name="create-lightweight-optimized-components"></a>Twórz lekkie, zoptymalizowane składniki

Większość Blazor składników nie wymaga agresywnej optymalizacji. Wynika to z faktu, że większość składników nie jest często powtarzana w interfejsie użytkownika i nie jest uruchamiana z dużą częstotliwością. Na przykład `@page` składniki i składniki reprezentujące jednopoziomowe elementy interfejsu użytkownika, takie jak okna dialogowe lub formularze, najprawdopodobniej pojawiają się tylko jeden w czasie i ponownie renderują w odpowiedzi na gest użytkownika. Te składniki nie tworzą obciążenia o wysokim poziomie renderowania, dzięki czemu można swobodnie korzystać z dowolnej kombinacji potrzebnych funkcji, bez obaw o wydajność renderowania.

Istnieją jednak również typowe scenariusze, w których można tworzyć składniki, które muszą być powtórzone na dużą skalę. Na przykład:

 * Duże zagnieżdżone formularze mogą mieć setki poszczególnych wejść, etykiet i innych elementów.
 * Siatki mogą zawierać tysiące komórek.
 * Wykresy punktowe mogą mieć miliony punktów danych.

Jeśli modeluje każdą jednostkę jako oddzielne wystąpienia składnika, będzie wiele z nich, aby wydajność renderowania stała się krytyczna. Ta sekcja zawiera wskazówki dotyczące podejmowania takich składników w sposób uproszczony, co sprawia, że interfejs użytkownika pozostanie szybko i będzie odpowiadać.

#### <a name="avoid-thousands-of-component-instances"></a>Unikaj tysięcy wystąpień składników

Każdy składnik jest oddzielną wyspa, która może być niezależna od elementów nadrzędnych i podrzędnych. Wybierając sposób dzielenia interfejsu użytkownika na hierarchię składników, można przejąć kontrolę nad szczegółowością renderowania interfejsu użytkownika. Może to być dobre lub złe w przypadku wydajności.

 * Dzieląc interfejs użytkownika na więcej składników, można przetworzyć mniejsze części interfejsu użytkownika, gdy wystąpią zdarzenia. Na przykład gdy użytkownik kliknie przycisk w wierszu tabeli, może być możliwe tylko przerenderowanie pojedynczego wiersza zamiast całej strony lub tabeli.
 * Jednak każdy dodatkowy składnik wiąże się z dodatkowym obciążeniem pamięci i procesora, aby zająć się niezależnym stanem i wyrenderowaniem.

Podczas dostrajania wydajności programu Blazor WebAssembly .NET 5 mierzy się obciążenie renderowania wokół 0,06 MS na wystąpienie składnika. Jest to oparte na prostym składniku, który akceptuje trzy parametry działające na typowym laptopie. Wewnętrznie narzuty są duże ze względu na pobieranie stanu poszczególnych składników ze słowników oraz przekazywanie i otrzymywanie parametrów. Dzięki mnożenia można zobaczyć, że dodanie 2 000 dodatkowych wystąpień składników spowodowałoby dodanie 0,12 sekund do czasu renderowania, a interfejs użytkownika byłby wolny dla użytkowników.

Istnieje możliwość, że składniki są bardziej uproszczone, dzięki czemu można uzyskać więcej z nich, ale często bardziej wydajną techniką nie jest to wiele składników. W poniższych sekcjach opisano dwa sposoby.

##### <a name="inline-child-components-into-their-parents"></a>Wbudowane składniki podrzędne do ich elementów nadrzędnych

Rozważmy następujący składnik, który renderuje sekwencję składników podrzędnych:

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <ChatMessageDisplay Message="@message" />
    }
</div>
```

Dla poprzedniego przykładowego kodu `<ChatMessageDisplay>` składnik jest zdefiniowany w pliku `ChatMessageDisplay.razor` zawierającym:

```razor
<div class="chat-message">
    <span class="author">@Message.Author</span>
    <span class="text">@Message.Text</span>
</div>

@code {
    [Parameter]
    public ChatMessage Message { get; set; }
}
```

Powyższy przykład działa prawidłowo i sprawdza, czy tylko tysiące wiadomości nie są wyświetlane jednocześnie. Aby jednocześnie pokazać tysiące komunikatów, *należy rozważyć* rozważenia oddzielnego `ChatMessageDisplay` składnika. Zamiast tego Renderuj wbudowane bezpośrednio do obiektu nadrzędnego:

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    }
</div>
```

Pozwala to uniknąć narzutu na składnik renderowania, tak aby wiele składników podrzędnych była kosztem niemożliwości ponownego renderowania każdego z nich.

##### <a name="define-reusable-renderfragments-in-code"></a>Zdefiniuj wielokrotne użycie `RenderFragments` kodu

Składniki podrzędne mogą być rozprowadzone wyłącznie jako sposób użycia logiki renderowania. W takim przypadku nadal można ponownie używać logiki renderowania bez deklarowania rzeczywistych składników. W bloku dowolnego składnika `@code` można zdefiniować <xref:Microsoft.AspNetCore.Components.RenderFragment> , który EMITUJE interfejs użytkownika i może być wywoływany z dowolnego miejsca:

```razor
<h1>Hello, world!</h1>

@RenderWelcomeInfo

@code {
    RenderFragment RenderWelcomeInfo = __builder =>
    {
        <div>
            <p>Welcome to your new app!</p>

            <SurveyPrompt Title="How is Blazor working for you?" />
        </div>
    };
}
```

Jak demonstated w poprzednim przykładzie, składniki mogą emitować znaczniki z kodu w obrębie ich `@code` bloku i poza nim. To podejście definiuje <xref:Microsoft.AspNetCore.Components.RenderFragment> delegata, który można renderować wewnątrz zwykłych wyników renderowania składnika, opcjonalnie w wielu miejscach. Jest to konieczne, aby delegat zaakceptował parametr o nazwie `__builder` typu, <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> Aby Razor kompilator mógł generować instrukcje renderowania dla niego.

Jeśli chcesz umożliwić wielokrotne użycie wielu składników, rozważ zadeklarowanie go jako `public static` członka:

```razor
public static RenderFragment SayHello = __builder =>
{
    <h1>Hello!</h1>
};
```

Ta funkcja może teraz zostać wywołana z niepowiązanego składnika. Ta technika jest przydatna do tworzenia bibliotek wstawek znaczników wielokrotnego użytku, które są renderowane bez jakichkolwiek obciążeń dla składników.

<xref:Microsoft.AspNetCore.Components.RenderFragment> Delegaty mogą również akceptować parametry. Aby utworzyć odpowiednik `ChatMessageDisplay` składnika z wcześniejszego przykładu:

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        @DisplayChatMessage(message)
    }
</div>

@code {
    RenderFragment<ChatMessage> DisplayChatMessage = message => __builder =>
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    };
}
```

Takie podejście umożliwia ponowne korzystanie z logiki renderowania bez narzutu na składnik. Nie ma jednak możliwości odświeżenia poddrzewa interfejsu użytkownika niezależnie od tego, czy nie ma możliwości pominięcia renderowania tego poddrzewa interfejsu użytkownika podczas jego nadrzędnego renderowania, ponieważ nie ma granicy składnika.

#### <a name="dont-receive-too-many-parameters"></a>Nie odbieraj zbyt wielu parametrów

Jeśli składnik powtarza się bardzo często, na przykład setki lub tysiące razy, należy pamiętać, że narzuty dotyczące przekazywania i otrzymywania każdego parametru kompiluje.

Jest to rzadkie, że zbyt wiele parametrów poważnie ogranicza wydajność, ale może być czynnikiem. Dla `<TableCell>` składnika, który renderuje 1 000 razy w obrębie siatki, każdy dodatkowy parametr, który przeszedł do niego, może dodać około 15 MS do całkowitego kosztu renderowania. Jeśli każda komórka zaakceptowała 10 parametrów, przekazywanie parametrów zajmie około 150 MS na składnik renderowania i w ten sposób prawdopodobnie 150 000 MS (150 s) i z własnej przyczyny jest interfejs użytkownika.

Aby zmniejszyć obciążenie, można powiązać wiele parametrów za pośrednictwem klas niestandardowych. Na przykład `<TableCell>` składnik może zaakceptować:

```razor
@typeparam TItem

...

@code {
    [Parameter]
    public TItem Data { get; set; }
    
    [Parameter]
    public GridOptions Options { get; set; }
}
```

W poprzednim przykładzie `Data` różni się dla każdej komórki, ale `Options` jest wspólne dla wszystkich. Oczywiście może być udoskonaleniem, aby nie mieć `<TableCell>` składnika i zamiast niego wbudowany w jego logikę w składnik nadrzędny.

#### <a name="ensure-cascading-parameters-are-fixed"></a>Upewnij się, że parametry kaskadowe zostały naprawione

`<CascadingValue>`Składnik ma opcjonalny parametr o nazwie `IsFixed` .

 * Jeśli `IsFixed` wartość jest `false` (domyślnie), a następnie każdy odbiorca wartości kaskadowej skonfiguruje subskrypcję do odbierania powiadomień o zmianach. W takim przypadku każdy `[CascadingParameter]` jest **znacznie droższy** niż regularna `[Parameter]` ze względu na śledzenie subskrypcji.
 * Jeśli `IsFixed` wartość jest `true` (na przykład `<CascadingValue Value="@someValue" IsFixed="true">` ), adresaci Pobiera wartość początkową, ale *nie* konfiguruje żadnej subskrypcji do odbierania aktualizacji. W takim przypadku każdy `[CascadingParameter]` jest lekki i **nie jest droższy** od zwykłego `[Parameter]` .

Tak, gdzie to możliwe, należy używać `IsFixed="true"` na wartościach kaskadowych. Można to zrobić zawsze, gdy wartość jest podawana nie zmienia się w czasie. We wspólnym wzorcu, w którym składnik przechodzi `this` jako wartość kaskadowo, należy użyć `IsFixed="true"` :

```razor
<CascadingValue Value="this" IsFixed="true">
    <SomeOtherComponents>
</CascadingValue>
```

Jest to bardzo istotna różnica, jeśli istnieje duża liczba innych składników, które otrzymują kaskadową wartość. Aby uzyskać więcej informacji, zobacz <xref:blazor/components/cascading-values-and-parameters>.

#### <a name="avoid-attribute-splatting-with-captureunmatchedvalues"></a>Unikaj korzystając atrybutów z `CaptureUnmatchedValues`

Składniki mogą wybrać otrzymywanie niedopasowanych wartości parametrów przy użyciu <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> flagi:

```razor
<div @attributes="OtherAttributes">...</div>

@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public IDictionary<string, object> OtherAttributes { get; set; }
}
```

Takie podejście umożliwia przechodzenie przez dowolne dodatkowe atrybuty do elementu. Jednak jest on również dość kosztowny, ponieważ moduł renderujący musi:

* Dopasowuje wszystkie podane parametry do zestawu znanych parametrów w celu skompilowania słownika.
* Śledź, jak wiele kopii tego samego atrybutu zastępuje siebie nawzajem.

Korzystaj z bezpłatnych <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> składników niezwiązanych z wydajnością, takich jak te, które nie są często powtarzane. Jednak w przypadku składników, które są renderowane na dużą skalę, takich jak każdy element na dużej liście lub w komórkach w siatce, spróbuj uniknąć korzystając atrybutu.

Aby uzyskać więcej informacji, zobacz <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.

#### <a name="implement-setparametersasync-manually"></a>Zaimplementuj `SetParametersAsync` ręcznie

Jednym z głównych aspektów narzutu na składnik renderowania jest zapisanie przychodzących wartości parametrów do `[Parameter]` właściwości. Aby to zrobić, moduł renderowania musi używać odbicia. Mimo że jest to nieco zoptymalizowane, brak obsługi JIT w środowisku uruchomieniowym webassembly nakłada limity.

W niektórych ekstremalnych przypadkach warto uniknąć odbicia i zaimplementować własną logikę ustawienia parametru ręcznie. Może to być stosowane w przypadku:

 * Masz składnik, który renderuje bardzo często (na przykład, w interfejsie użytkownika znajdują się setki lub tysiące kopii tego elementu).
 * Akceptuje wiele parametrów.
 * Okaże się, że narzuty odbioru parametrów mają zauważalny wpływ na czas odpowiedzi interfejsu użytkownika.

W takich przypadkach można przesłonić metodę wirtualną składnika <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> i wdrożyć własną logikę specyficzną dla danego składnika. W poniższym przykładzie zamierzone jest uniknięcie przeszukiwania słownika:

```razor
@code {
    [Parameter]
    public int MessageId { get; set; }

    [Parameter]
    public string Text { get; set; }

    [Parameter]
    public EventCallback<string> TextChanged { get; set; }

    [Parameter]
    public Theme CurrentTheme { get; set; }

    public override Task SetParametersAsync(ParameterView parameters)
    {
        foreach (var parameter in parameters)
        {
            switch (parameter.Name)
            {
                case nameof(MessageId):
                    MessageId = (int)parameter.Value;
                    break;
                case nameof(Text):
                    Text = (string)parameter.Value;
                    break;
                case nameof(TextChanged):
                    TextChanged = (EventCallback<string>)parameter.Value;
                    break;
                case nameof(CurrentTheme):
                    CurrentTheme = (Theme)parameter.Value;
                    break;
                default:
                    throw new ArgumentException($"Unknown parameter: {parameter.Name}");
            }
        }

        return base.SetParametersAsync(ParameterView.Empty);
    }
}
```

W poprzednim kodzie zwracające klasę bazową <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> działają normalne metody cyklu życia bez ponownego przypisywania parametrów.

Jak widać w powyższym kodzie, zastępowanie <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> i dostarczanie logiki niestandardowej jest skomplikowane i pracochłonne, dlatego nie zalecamy ogólnie tego podejścia. W skrajnych przypadkach może zwiększyć wydajność renderowania o 20-25%, ale rozwiązanie to należy wziąć pod uwagę tylko w scenariuszach wymienionych powyżej.

### <a name="dont-trigger-events-too-rapidly"></a>Nie Wyzwalaj zbyt szybko zdarzeń

Niektóre zdarzenia przeglądarki są niezwykle często wyzwalane, na przykład `onmousemove` i `onscroll` , które mogą być uruchamiane dziesiątki lub setki razy na sekundę. W większości przypadków nie trzeba często wykonywać aktualizacji interfejsu użytkownika. Jeśli użytkownik spróbuje to zrobić, może to spowodować szkody czas odpowiedzi interfejsu użytkownika lub nadmierne wykorzystanie procesora CPU.

Zamiast używać natywnych `@onmousemove` lub `@onscroll` zdarzeń, warto użyć kodu js Interop do zarejestrowania wywołania zwrotnego, które jest generowane rzadziej. Na przykład poniższy składnik ( `MyComponent.razor` ) wyświetla pozycję myszy, ale tylko aktualizuje się co najwyżej raz na 500 MS:

```razor
@inject IJSRuntime JS
@implements IDisposable

<h1>@message</h1>

<div @ref="myMouseMoveElement" style="border:1px dashed red;height:200px;">
    Move mouse here
</div>

@code {
    ElementReference myMouseMoveElement;
    DotNetObjectReference<MyComponent> selfReference;
    private string message = "Move the mouse in the box";

    [JSInvokable]
    public void HandleMouseMove(int x, int y)
    {
        message = $"Mouse move at {x}, {y}";
        StateHasChanged();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            selfReference = DotNetObjectReference.Create(this);
            var minInterval = 500; // Only notify every 500 ms
            await JS.InvokeVoidAsync("onThrottledMouseMove", 
                myMouseMoveElement, selfReference, minInterval);
        }
    }

    public void Dispose() => selfReference?.Dispose();
}
```

Odpowiedni kod JavaScript, który może być umieszczony na `index.html` stronie lub załadowany jako moduł ES6, rejestruje rzeczywisty odbiornik zdarzeń dom. W tym przykładzie odbiornik zdarzeń używa [ `throttle` funkcji Lodash](https://lodash.com/docs/4.17.15#throttle) , aby ograniczyć szybkość wywołań:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.20/lodash.min.js"></script>
<script>
  function onThrottledMouseMove(elem, component, interval) {
    elem.addEventListener('mousemove', _.throttle(e => {
      component.invokeMethodAsync('HandleMouseMove', e.offsetX, e.offsetY);
    }, interval));
  }
</script>
```

Ta technika może być jeszcze bardziej ważna dla Blazor Server , ponieważ każde wywołanie zdarzenia obejmuje dostarczenie komunikatu przez sieć. Jest to przydatne w przypadku, gdy Blazor WebAssembly może znacznie zmniejszyć ilość pracy renderowania.

## <a name="optimize-javascript-interop-speed"></a>Optymalizuj szybkość międzyoperacyjności kodu JavaScript

Wywołania między programami .NET i JavaScript obejmują kilka dodatkowych kosztów, ponieważ:

 * Domyślnie wywołania są asynchroniczne.
 * Domyślnie parametry i zwracane wartości są serializowane w formacie JSON. Ma to na celu zapewnienie łatwego w zrozumieniu mechanizmu konwersji między typami .NET i JavaScript.

Ponadto Blazor Server te wywołania są przesyłane przez sieć.

### <a name="avoid-excessively-fine-grained-calls"></a>Unikaj nadmiernie szczegółowych wywołań

Ponieważ każde wywołanie wiąże się z pewnym obciążeniem, może być cenne, aby zmniejszyć liczbę wywołań. Rozważmy następujący kod, który przechowuje kolekcję elementów w `localStorage` sklepie przeglądarki:

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    foreach (var item in items)
    {
        await JS.InvokeVoidAsync("localStorage.setItem", item.Id, 
            JsonSerializer.Serialize(item));
    }
}
```

W powyższym przykładzie jest to oddzielne wywołanie międzyoperacyjna JS dla każdego elementu. Zamiast tego, następujące podejście zmniejsza międzyoperacyjność elementu JS do pojedynczego wywołania:

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    await JS.InvokeVoidAsync("storeAllInLocalStorage", items);
}
```

Odpowiednia funkcja języka JavaScript zdefiniowana w następujący sposób:

```javascript
function storeAllInLocalStorage(items) {
  items.forEach(item => {
    localStorage.setItem(item.id, JSON.stringify(item));
  });
}
```

W przypadku Blazor WebAssembly , zwykle jest to kwestia tylko wtedy, gdy wykonujesz dużą liczbę wywołań międzyoperacyjnych w języku js.

### <a name="consider-making-synchronous-calls"></a>Rozważ możliwość wykonywania wywołań synchronicznych

Wywołania międzyoperacyjne JavaScript są domyślnie asynchroniczne, bez względu na to, czy kod jest wywoływany synchronicznie, czy asynchronicznie. Ma to na celu zapewnienie, że składniki są zgodne z systemami Blazor WebAssembly i Blazor Server . W systemie Blazor Server wszystkie wywołania międzyoperacyjne języka JavaScript muszą być asynchroniczne, ponieważ są wysyłane za pośrednictwem połączenia sieciowego.

Jeśli masz pewność, że aplikacja została kiedykolwiek uruchomiona tylko w programie Blazor WebAssembly , możesz wybrać opcję wykonywania synchronicznych wywołań w języku JavaScript. Ta funkcja ma nieco mniej obciążenia niż wywołania asynchroniczne i może powodować mniejszą liczbę cykli renderowania, ponieważ nie ma stanu pośredniego podczas oczekiwania na wyniki.

Aby wykonać synchroniczne wywołanie z platformy .NET do języka JavaScript, Rzutowanie <xref:Microsoft.JSInterop.IJSRuntime> na <xref:Microsoft.JSInterop.IJSInProcessRuntime> :

```razor
@inject IJSRuntime JS

...

@code {
    protected override void HandleSomeEvent()
    {
        var jsInProcess = (IJSInProcessRuntime)JS;
        var value = jsInProcess.Invoke<string>("javascriptFunctionIdentifier");
    }
}
```

::: moniker range=">= aspnetcore-5.0"

Podczas pracy z programem `IJSObjectReference` można wykonać wywołanie synchroniczne przez rzutowanie na `IJSInProcessObjectReference` .

::: moniker-end

Aby wykonać synchroniczne wywołanie z JavaScript do .NET, użyj `DotNet.invokeMethod` zamiast `DotNet.invokeMethodAsync` .

Wywołania synchroniczne działają, jeśli:

* Aplikacja działa w systemie Blazor WebAssembly , nie Blazor Server .
* Wywołana funkcja zwraca wartość synchronicznie (nie jest to `async` Metoda i nie zwraca platformy .NET <xref:System.Threading.Tasks.Task> ani JavaScript `Promise` ).

Aby uzyskać więcej informacji, zobacz <xref:blazor/call-javascript-from-dotnet>.

::: moniker range=">= aspnetcore-5.0"
 
### <a name="consider-making-unmarshalled-calls"></a>Rozważ utworzenie nieskierowanych wywołań

W przypadku uruchamiania w systemie Blazor WebAssembly można wykonać nieskierowanie wywołania z platformy .NET do języka JavaScript. Są to wywołania synchroniczne, które nie wykonują serializacji JSON argumentów lub zwracanych wartości. Wszystkie aspekty związane z zarządzaniem pamięcią i tłumaczeniami między środowiskami .NET i JavaScript są pozostawiane do dewelopera.

> [!WARNING]
> Podczas używania `IJSUnmarshalledRuntime` ma najmniejsze obciążenie związane z interfejsem js międzyoperacyjności, interfejsy API języka JavaScript wymagane do współdziałania z tymi interfejsy API są obecnie nieudokumentowane i mogą ulegać zmianom w przyszłych wersjach.

```javascript
function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
}
```

```razor
@inject IJSRuntime JS

@code {
    protected override void OnInitialized()
    {
        var unmarshalledJs = (IJSUnmarshalledRuntime)JS;
        var value = unmarshalledJs.InvokeUnmarshalled<string>("jsInteropCall");
    }
}
```

::: moniker-end

## <a name="minimize-app-download-size"></a>Minimalizuj rozmiar pobierania aplikacji

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a>Przycinanie języka pośredniego (IL)

[Przycinanie nieużywanych zestawów z Blazor WebAssembly aplikacji](xref:blazor/host-and-deploy/configure-trimmer) zmniejsza rozmiar aplikacji przez usunięcie nieużywanego kodu w danych binarnych aplikacji. Domyślnie element dostosowujący jest wykonywany podczas publikowania aplikacji. Aby skorzystać z przycinania, Opublikuj aplikację do wdrożenia przy użyciu [`dotnet publish`](/dotnet/core/tools/dotnet-publish) polecenia z opcją [-c |--Configuration](/dotnet/core/tools/dotnet-publish#options) ustawioną na `Release` :

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a>Tworzenie łączy języka pośredniego (IL)

[Łączenie Blazor WebAssembly aplikacji](xref:blazor/host-and-deploy/configure-linker) zmniejsza rozmiar aplikacji przez przycinanie nieużywanego kodu w plikach binarnych aplikacji. Domyślnie konsolidator języka pośredniego (IL) jest włączony tylko w przypadku kompilowania w `Release` konfiguracji. Aby z tego skorzystać, Opublikuj aplikację do wdrożenia przy użyciu [`dotnet publish`](/dotnet/core/tools/dotnet-publish) polecenia z opcją [-c |--Configuration](/dotnet/core/tools/dotnet-publish#options) ustawioną na `Release` :

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="use-systemtextjson"></a>Użyj System.Text.Jsna

Blazorimplementacja elementu webinterop w języku JS polega na tym <xref:System.Text.Json> , że jest to biblioteka serializacji JSON o wysokiej wydajności z alokacją małej ilości pamięci. Użycie nie powoduje, że <xref:System.Text.Json> rozmiar ładunku aplikacji jest większy niż dodanie co najmniej jednej alternatywnej biblioteki JSON.

Aby uzyskać wskazówki dotyczące migracji, zobacz [Jak przeprowadzić migrację z `Newtonsoft.Json` do programu `System.Text.Json` ](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).

### <a name="lazy-load-assemblies"></a>Zestawy ładowania z opóźnieniem

Ładuj zestawy w czasie wykonywania, gdy zestawy są wymagane przez trasę. Aby uzyskać więcej informacji, zobacz <xref:blazor/webassembly-lazy-load-assemblies>.

### <a name="compression"></a>Kompresja

Po Blazor WebAssembly opublikowaniu aplikacji dane wyjściowe są kompresowane statycznie podczas publikowania, aby zmniejszyć rozmiar aplikacji i usunąć obciążenie dla kompresji w czasie wykonywania. Blazor opiera się na serwerze w celu przeprowadzenia negotation zawartości i obkompresji plików skompresowanych statycznie.

Po wdrożeniu aplikacji Sprawdź, czy aplikacja obsługuje skompresowane pliki. Zbadaj kartę Sieć w Narzędzia deweloperskie przeglądarki i sprawdź, czy pliki są obsługiwane przez program `Content-Encoding: br` lub `Content-Encoding: gz` . Jeśli host nie obsługuje skompresowanych plików, postępuj zgodnie z instrukcjami w temacie <xref:blazor/host-and-deploy/webassembly#compression> .

### <a name="disable-unused-features"></a>Wyłącz nieużywane funkcje

Blazor WebAssemblyśrodowisko uruchomieniowe obejmuje następujące funkcje platformy .NET, które można wyłączyć, jeśli aplikacja nie wymaga ich dla mniejszego rozmiaru ładunku:

* Plik danych jest uwzględniany w celu poprawnego wprowadzania informacji o strefie czasowej. Jeśli aplikacja nie wymaga tej funkcji, rozważ wyłączenie jej przez ustawienie właściwości programu `BlazorEnableTimeZoneSupport` MSBuild w pliku projektu aplikacji na `false` :

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* Domyślnie Blazor WebAssembly zasoby globalizacji są wymagane do wyświetlania wartości, takich jak daty i waluta, w kulturze użytkownika. Jeśli aplikacja nie wymaga lokalizacji, można [skonfigurować aplikację do obsługi niezmiennej kultury](xref:blazor/globalization-localization), która jest oparta na `en-US` kulturze:

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Informacje o sortowaniu są uwzględniane w celu <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> poprawnego działania interfejsów API. Jeśli masz pewność, że aplikacja nie wymaga danych sortowania, rozważ wyłączenie jej przez ustawienie `BlazorWebAssemblyPreserveCollationData` Właściwości programu MSBuild w pliku projektu aplikacji na `false` :

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
