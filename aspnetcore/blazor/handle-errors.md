---
title: Obsługa błędów w aplikacjach ASP.NET Core Blazor
author: guardrex
description: Dowiedz się, jak Blazor ASP.NET Blazor Core, jak zarządza nieobsługiwałem wyjątków i jak tworzyć aplikacje, które wykrywają i obsługują błędy.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/29/2020
no-loc:
- Blazor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: 4fdaf7fb90d126b8f7f029aac3af49eec3b69e74
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80382278"
---
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a>Obsługa błędów w aplikacjach ASP.NET Core Blazor

Przez [Steve Sanderson](https://github.com/SteveSandersonMS)

W tym Blazor artykule opisano, jak zarządza nieobsługiwanie wyjątków i jak tworzyć aplikacje, które wykrywają i obsługują błędy.

## <a name="detailed-errors-during-development"></a>Szczegółowe błędy podczas opracowywania

Gdy Blazor aplikacja nie działa poprawnie podczas tworzenia, otrzymywanie szczegółowych informacji o błędzie z aplikacji pomaga w rozwiązywaniu problemów i rozwiązywaniu problemu. W przypadku wystąpienia Blazor błędu aplikacje wyświetlają złoty pasek u dołu ekranu:

* Podczas tworzenia złotego paska kieruje cię do konsoli przeglądarki, gdzie można zobaczyć wyjątek.
* W produkcji złoty pasek powiadamia użytkownika o wystąpieniu błędu i zaleca odświeżenie przeglądarki.

Interfejs użytkownika dla tego środowiska obsługi Blazor błędów jest częścią szablonów projektu.

W Blazor aplikacji WebAssembly dostosuj środowisko w pliku *wwwroot/index.html:*

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

W Blazor aplikacji Serwer dostosuj środowisko w pliku *Pages/_Host.cshtml:*

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

Element `blazor-error-ui` jest ukryty przez style zawarte Blazor w szablonach (*wwwroot/css/site.css),* a następnie wyświetlany po wystąpieniu błędu:

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="how-a-opno-locblazor-server-app-reacts-to-unhandled-exceptions"></a>Jak Blazor aplikacja Server reaguje na nieobsługiwalne wyjątki

BlazorSerwer jest stanową strukturą. Podczas gdy użytkownicy wchodzą w interakcję z aplikacją, utrzymują połączenie z serwerem znanym jako *obwód*. Obwód zawiera aktywne wystąpienia składników oraz wiele innych aspektów stanu, takich jak:

* Najnowsze renderowane dane wyjściowe składników.
* Bieżący zestaw delegatów obsługi zdarzeń, które mogą być wyzwalane przez zdarzenia po stronie klienta.

Jeśli użytkownik otworzy aplikację w wielu kartach przeglądarki, mają wiele niezależnych obwodów.

Blazortraktuje większość nieobsługiwałem wyjątków jako śmiertelne dla obwodu, w którym występują. Jeśli obwód zostanie zakończony z powodu nieobsługiwał wyjątek, użytkownik może kontynuować interakcję z aplikacją tylko przez ponowne załadowanie strony w celu utworzenia nowego obwodu. Nie ma to wpływu na obwody poza końców, które są obwodami dla innych użytkowników lub innymi kartami przeglądarki. Ten scenariusz jest podobny do&mdash;aplikacji klasycznej, która ulega awarii, należy ponownie uruchomić aplikację, ale nie ma to wpływu na inne aplikacje.

Obwód zostaje zakończony, gdy wystąpi nieobsługiowany wyjątek z następujących powodów:

* Nieobsługiwał wyjątek często pozostawia obwód w stanie nieokreślonym.
* Normalna operacja aplikacji nie może być zagwarantowana po nieobsługiwał wyjątek.
* Jeśli obwód będzie kontynuowany, w aplikacji mogą pojawić się luki w zabezpieczeniach.

## <a name="manage-unhandled-exceptions-in-developer-code"></a>Zarządzanie nieobsługiwałem wyjątków w kodzie dewelopera

Aby aplikacja mogła być kontynuowana po błędzie, aplikacja musi mieć logikę obsługi błędów. W dalszej części tego artykułu opisano potencjalne źródła nieobsługiwał wyjątków.

W produkcji nie renderuj komunikatów wyjątków framework lub ślady stosu w interfejsie użytkownika. Renderowanie komunikatów o wyjątkach lub śladów stosu może:

* Ujawnianie poufnych informacji użytkownikom końcowym.
* Pomóż złośliwemu użytkownikowi odkryć słabe punkty w aplikacji, które mogą naruszyć bezpieczeństwo aplikacji, serwera lub sieci.

## <a name="log-errors-with-a-persistent-provider"></a>Rejestrowanie błędów u trwałego dostawcy

Jeśli wystąpi nieobsługiowany wyjątek, <xref:Microsoft.Extensions.Logging.ILogger> wyjątek jest rejestrowany w wystąpieniach skonfigurowanych w kontenerze usługi. Domyślnie Blazor aplikacje logują się do danych wyjściowych konsoli za pomocą dostawcy rejestrowania konsoli. Należy wziąć pod uwagę rejestrowanie do bardziej stałej lokalizacji z dostawcą, który zarządza rozmiar dziennika i rotacji dziennika. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/logging/index>.

Podczas tworzenia Blazor zwykle wysyła pełne szczegóły wyjątków do konsoli przeglądarki, aby pomóc w debugowaniu. W wersji produkcyjnej szczegółowe błędy w konsoli przeglądarki są domyślnie wyłączone, co oznacza, że błędy nie są wysyłane do klientów, ale pełne szczegóły wyjątku są nadal rejestrowane po stronie serwera. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/error-handling>.

Należy zdecydować, które zdarzenia mają zostać zarejestrowane i poziom ważności zarejestrowanych zdarzeń. Wrogo nastawieni użytkownicy mogą celowo wyzwolić błędy. Na przykład nie rejestruj zdarzenia z powodu `ProductId` błędu, w którym w adresie URL składnika, który wyświetla szczegóły produktu, znajduje się nieznany. Nie wszystkie błędy powinny być traktowane jako zdarzenia o wysokiej ważności do rejestrowania.

## <a name="places-where-errors-may-occur"></a>Miejsca, w których mogą wystąpić błędy

Framework i kod aplikacji może wyzwolić nieobsługiwał wyjątki w dowolnej z następujących lokalizacji:

* [Tworzenie wystąpienia składników](#component-instantiation)
* [Metody cyklu życia](#lifecycle-methods)
* [Logika renderowania](#rendering-logic)
* [Procedury obsługi zdarzeń](#event-handlers)
* [Utylizacja komponentów](#component-disposal)
* [Międzyoperacyjność w języku JavaScript](#javascript-interop)
* [BlazorRerendering serwera](#blazor-server-prerendering)

Poprzednie nieobsługiwały wyjątki są opisane w poniższych sekcjach tego artykułu.

### <a name="component-instantiation"></a>Tworzenie wystąpienia składników

Po Blazor wykonaniu wystąpienia składnika:

* Konstruktor składnika jest wywoływany.
* Konstruktory wszystkich usług DI innych niż singleton dostarczane do [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) konstruktora składnika za pośrednictwem dyrektywy lub atrybutu [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) są wywoływane.

Obwód Blazor serwera kończy się niepowodzeniem, gdy `[Inject]` dowolny wykonany konstruktor lub ustawiacz dla dowolnej właściwości zgłasza nieobsługiowany wyjątek. Wyjątek jest krytyczny, ponieważ struktura nie może utworzyć wystąpienia składnika. Jeśli logika konstruktora może zgłaszać wyjątki, aplikacja powinna zalewkować wyjątki przy użyciu [instrukcji try-catch](/dotnet/csharp/language-reference/keywords/try-catch) z obsługą błędów i rejestrowaniem.

### <a name="lifecycle-methods"></a>Metody cyklu życia

W okresie istnienia składnika Blazor wywołuje następujące metody [cyklu życia:](xref:blazor/lifecycle)

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

Jeśli dowolna metoda cyklu życia zgłasza wyjątek, synchronicznie lub asynchronicznie, wyjątek jest krytyczny dla obwodu Blazor serwera. Aby składniki radziły sobie z błędami w metodach cyklu życia, dodaj logikę obsługi błędów.

W poniższym `OnParametersSetAsync` przykładzie, w którym wywołuje metodę uzyskania produktu:

* Wyjątek zgłoszony `ProductRepository.GetProductByIdAsync` w metodzie jest `try-catch` obsługiwany przez instrukcję.
* Po `catch` wykonaniu bloku:
  * `loadFailed`jest ustawiona na `true`, który jest używany do wyświetlania komunikatu o błędzie dla użytkownika.
  * Błąd jest rejestrowany.

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a>Logika renderowania

Znaczników deklaratywnych w pliku składnika `.razor` jest `BuildRenderTree`kompilowany do metody C# o nazwie . Gdy składnik renderuje, `BuildRenderTree` wykonuje i buduje strukturę danych opisującą elementy, tekst i składniki podrzędne renderowanego składnika.

Logika renderowania może zgłosić wyjątek. Przykład tego scenariusza `@someObject.PropertyName` występuje, `@someObject` gdy `null`jest oceniany, ale jest . Nieobsłużony wyjątek zgłaszany przez Blazor logikę renderowania jest krytyczny dla obwodu serwera.

Aby zapobiec wyjątek odwołania null w logice renderowania, sprawdź obiekt przed dostępem `null` do jego elementów członkowskich. W poniższym `person.Address` przykładzie właściwości nie są `person.Address` `null`dostępne, jeśli jest:

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

Poprzedni kod zakłada, `person` że nie `null`jest . Często struktura kodu gwarantuje, że obiekt istnieje w czasie renderowania składnika. W takich przypadkach nie jest konieczne, `null` aby sprawdzić w logice renderowania. W poprzednim przykładzie może być gwarantowane istnieć, `person` ponieważ `person` jest tworzony podczas tworzenia składnika jest tworzona.

### <a name="event-handlers"></a>Procedury obsługi zdarzeń

Kod po stronie klienta wyzwala wywołania kodu Języka C#, gdy programy obsługi zdarzeń są tworzone przy użyciu:

* `@onclick`
* `@onchange`
* Inne `@on...` atrybuty
* `@bind`

Kod obsługi zdarzeń może zgłosić nieobsługiowany wyjątek w tych scenariuszach.

Jeśli program obsługi zdarzeń zgłasza nieobsługiowany wyjątek (na przykład kwerenda Blazor bazy danych kończy się niepowodzeniem), wyjątek jest krytyczny dla obwodu serwera. Jeśli aplikacja wywołuje kod, który może zakończyć się niepowodzeniem z przyczyn zewnętrznych, wyjątki pułapki przy użyciu [instrukcji try-catch](/dotnet/csharp/language-reference/keywords/try-catch) z obsługi błędów i rejestrowania.

Jeśli kod użytkownika nie pułapki i obsługi wyjątku, struktura rejestruje wyjątek i kończy obwód.

### <a name="component-disposal"></a>Utylizacja komponentów

Składnik może zostać usunięty z interfejsu użytkownika, na przykład, ponieważ użytkownik przeszedł do innej strony. Gdy składnik, który <xref:System.IDisposable?displayProperty=fullName> implementuje jest usuwany z interfejsu użytkownika, struktura wywołuje metodę składnika. <xref:System.IDisposable.Dispose*>

Jeśli składnika `Dispose` metoda zgłasza nieobsługiwać wyjątek, wyjątek jest krytyczny dla obwodu Blazor serwera. Jeśli logika usuwania może zgłaszać wyjątki, aplikacja powinna zalewkować wyjątki przy użyciu [instrukcji try-catch](/dotnet/csharp/language-reference/keywords/try-catch) z obsługi błędów i rejestrowania.

Aby uzyskać więcej informacji <xref:blazor/lifecycle#component-disposal-with-idisposable>na temat usuwania komponentów, zobacz .

### <a name="javascript-interop"></a>Międzyoperacyjność w języku JavaScript

`IJSRuntime.InvokeAsync<T>`umożliwia kodowi .NET wykonywanie wywołań asynchronicznych do środowiska wykonawczego JavaScript w przeglądarce użytkownika.

Następujące warunki mają zastosowanie `InvokeAsync<T>`do obsługi błędów z:

* Jeśli `InvokeAsync<T>` wywołanie nie powiedzie się synchronicznie, wystąpi wyjątek .NET. Wywołanie `InvokeAsync<T>` może zakończyć się niepowodzeniem, na przykład, ponieważ dostarczone argumenty nie mogą być serializowane. Kod dewelopera musi przechwytyć wyjątek. Jeśli kod aplikacji w programie obsługi zdarzeń lub metody cyklu życia składnika nie Blazor obsługuje wyjątek, wynikowy wyjątek jest krytyczny dla obwodu serwera.
* Jeśli wywołanie `InvokeAsync<T>` nie powiedzie się asynchronicznie, .NET <xref:System.Threading.Tasks.Task> kończy się niepowodzeniem. Wywołanie `InvokeAsync<T>` może zakończyć się niepowodzeniem, na przykład, ponieważ kod `Promise` po stronie `rejected`JavaScript zgłasza wyjątek lub zwraca, który został ukończony jako . Kod dewelopera musi przechwytyć wyjątek. Jeśli używasz [await](/dotnet/csharp/language-reference/keywords/await) operatora, należy rozważyć zawijanie wywołania metody w [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługi błędów i rejestrowania. W przeciwnym razie kod w przypadku awarii powoduje nieobsługiwać wyjątek, który jest krytyczny dla obwodu Blazor serwera.
* Domyślnie wywołania `InvokeAsync<T>` muszą zakończyć się w określonym czasie lub w przeciwnym razie przesunie się czas połączenia. Domyślny limit czasu wynosi jedną minutę. Limit czasu chroni kod przed utratą łączności sieciowej lub kodu JavaScript, który nigdy nie wysyła z powrotem komunikatu uzupełniania. Jeśli przesiąknie `Task` czasu połączenia, wynik nie powiedzie się z <xref:System.OperationCanceledException>. Zalewka i przetwarzać wyjątek z rejestrowania.

Podobnie kod JavaScript może inicjować wywołania metod [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) platformy .NET wskazanych przez atrybut. Jeśli te metody platformy .NET zgłaszają nieobsługiowany wyjątek:

* Wyjątek nie jest traktowany jako Blazor krytyczny dla obwodu serwera.
* Strona JavaScript `Promise` zostanie odrzucona.

Można użyć kodu obsługi błędów po stronie platformy .NET lub javascript wywołania metody.

Aby uzyskać więcej informacji zobacz następujące artykuły:

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="opno-locblazor-server-prerendering"></a>BlazorPrerendering serwera

Blazorskładniki mogą być prerendered przy użyciu [Pomocnika tagu składnika,](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) tak aby ich renderowane znaczniki HTML jest zwracany jako część początkowego żądania HTTP użytkownika. Działa to poprzez:

* Tworzenie nowego obwodu dla wszystkich wstępnie powstałych komponentów, które są częścią tej samej strony.
* Generowanie początkowego kodu HTML.
* Traktowanie obwodu `disconnected` jako dopóki przeglądarka użytkownika SignalR nie nawiązuje połączenia z powrotem do tego samego serwera. Po nawiązaniu połączenia interakcyjność w obwodzie jest wznawiana, a znaczniki HTML składników są aktualizowane.

Jeśli dowolny składnik zgłasza nieobsługiwanie wyjątek podczas prerendering, na przykład podczas metody cyklu życia lub w logice renderowania:

* Wyjątek jest fatalny dla obwodu.
* Wyjątek jest zgłaszany stos `Component` wywołania z Pomocnika tagu. W związku z tym całe żądanie HTTP kończy się niepowodzeniem, chyba że wyjątek jest jawnie złowionych przez kod dewelopera.

W normalnych warunkach, gdy program prerendering kończy się niepowodzeniem, kontynuowanie tworzenia i renderowania składnika nie ma sensu, ponieważ nie można renderować składnika roboczego.

Aby tolerować błędy, które mogą wystąpić podczas prerendering, logika obsługi błędów musi być umieszczony wewnątrz składnika, który może zgłaszać wyjątki. Użyj [instrukcji try-catch](/dotnet/csharp/language-reference/keywords/try-catch) z obsługą błędów i rejestrowaniem. Zamiast zawijać `Component` Pomocnika znacznika w instrukcji, umieść logikę `try-catch` obsługi `Component` błędów w składniku renderowanym przez Pomocnika tagu.

## <a name="advanced-scenarios"></a>Scenariusze zaawansowane

### <a name="recursive-rendering"></a>Renderowanie cykliczne

Komponenty mogą być zagnieżdżone rekursywnie. Jest to przydatne do reprezentowania struktur danych cyklicznych. Na przykład `TreeNode` składnik może `TreeNode` renderować więcej składników dla każdego z elementów podrzędnych węzła.

Podczas renderowania rekursywnie należy unikać wzorców kodowania, które powodują nieskończoną rekursję:

* Nie cyklicznie renderować struktury danych, która zawiera cykl. Na przykład nie renderuj węzła drzewa, którego dzieci zawiera się.
* Nie należy tworzyć łańcucha układów, które zawierają cykl. Na przykład nie należy tworzyć układu, którego układ jest sam.
* Nie zezwalaj użytkownikowi końcowemu na naruszanie invariants recursion (reguł) za pośrednictwem złośliwego wprowadzania danych lub wywołań interop JavaScript.

Nieskończone pętle podczas renderowania:

* Powoduje, że proces renderowania trwa wiecznie.
* Jest odpowiednikiem tworzenia pętli nieokreślonej.

W tych scenariuszach obwód Blazor serwera, którego dotyczy problem, kończy się niepowodzeniem, a wątek zwykle próbuje:

* Zużywają tyle czasu procesora, ile pozwala system operacyjny, przez czas nieokreślony.
* Zużywają nieograniczoną ilość pamięci serwera. Korzystanie z pamięci nieograniczonej jest odpowiednikiem scenariusza, w którym nieokreślona pętla dodaje wpisy do kolekcji w każdej iteracji.

Aby uniknąć nieskończonych wzorców rekursji, upewnij się, że rekursywny kod renderowania zawiera odpowiednie warunki zatrzymania.

### <a name="custom-render-tree-logic"></a>Niestandardowa logika drzewa renderowania

Większość Blazor składników są implementowane jako pliki *.brzytwa* i są `RenderTreeBuilder` kompilowane do tworzenia logiki, która działa na a do renderowania ich danych wyjściowych. Deweloper może ręcznie `RenderTreeBuilder` zaimplementować logikę przy użyciu proceduralnego kodu języka C#. Aby uzyskać więcej informacji, zobacz <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.

> [!WARNING]
> Korzystanie z logiki konstruktora drzewa renderowania ręcznego jest uważany za zaawansowany i niebezpieczny scenariusz, nie zalecane dla ogólnego rozwoju składników.

Jeśli `RenderTreeBuilder` kod jest napisany, deweloper musi zagwarantować poprawność kodu. Na przykład deweloper musi upewnić się, że:

* Połączenia `OpenElement` do `CloseElement` i są prawidłowo wyważone.
* Atrybuty są dodawane tylko w odpowiednich miejscach.

Niepoprawna logika konstruktora drzew renderowania ręcznego może powodować dowolne niezdefiniowane zachowanie, w tym awarie, zawieszanie się serwera i luki w zabezpieczeniach.

Należy wziąć pod uwagę ręczne renderowania logiki konstruktora drzewa na tym samym poziomie złożoności i z tym samym poziomie *niebezpieczeństwa,* jak pisanie kodu zestawu lub instrukcje MSIL ręcznie.
