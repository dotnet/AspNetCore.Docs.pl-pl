---
title: Obsługa błędów w aplikacjach Blazor ASP.NET Core
author: guardrex
description: Odkryj, jak Blazor ASP.NET Core Blazor jak program zarządza nieobsługiwanymi wyjątkami i jak opracowywać aplikacje wykrywające i obsługujące błędy.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: 63bb791958785fa9a4a676f1aab79126c6873068
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111048"
---
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a>Obsługa błędów w aplikacjach Blazor ASP.NET Core

[Steve Sanderson](https://github.com/SteveSandersonMS)

W tym artykule opisano Blazor , jak zarządzać nieobsługiwanymi wyjątkami oraz jak opracowywać aplikacje wykrywające i obsługujące błędy.

## <a name="detailed-errors-during-development"></a>Szczegóły błędów podczas opracowywania

Gdy Blazor aplikacja nie działa prawidłowo podczas opracowywania, otrzymuje szczegółowe informacje o błędzie z aplikacji, które pomagają w rozwiązywaniu problemów i rozwiązaniu problemu. Gdy wystąpi błąd, Blazor aplikacje wyświetlają złoty pasek u dołu ekranu:

* W trakcie programowania złoty pasek kieruje użytkownika do konsoli przeglądarki, gdzie można zobaczyć wyjątek.
* W środowisku produkcyjnym złoty pasek powiadamia użytkownika o wystąpieniu błędu i zaleca odświeżenie przeglądarki.

Interfejs użytkownika dla tego środowiska obsługi błędów jest częścią szablonów Blazor projektu.

Blazor W aplikacji webassembly Dostosuj środowisko w pliku *wwwroot/index.html* :

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

W aplikacji Blazor serwera Dostosuj środowisko w pliku *pages/_Host. cshtml* :

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

`blazor-error-ui` Element jest ukryty przez style zawarte w Blazor szablonach (*wwwroot/CSS/site. css*), a następnie pokazywany w przypadku wystąpienia błędu:

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

## <a name="how-a-opno-locblazor-server-app-reacts-to-unhandled-exceptions"></a>Jak aplikacja Blazor serwera reaguje na Nieobsłużone wyjątki

BlazorSerwer jest strukturą stanową. Gdy użytkownicy współpracują z aplikacją, utrzymują połączenie z serwerem znanym jako *obwód*. Obwód zawiera aktywne wystąpienia składnika, a także wiele innych aspektów stanu, takich jak:

* Najnowsze renderowane dane wyjściowe składników.
* Bieżący zestaw delegatów obsługi zdarzeń, które mogą być wyzwalane przez zdarzenia po stronie klienta.

Jeśli użytkownik otworzy aplikację na wielu kartach przeglądarki, mają one wiele niezależnych obwodów.

Blazortraktuje większość nieobsłużonych wyjątków jako krytyczne dla obwodu, w którym występują. Jeśli obwód zostanie przerwany z powodu nieobsługiwanego wyjątku, użytkownik może nadal korzystać z aplikacji tylko przez ponowne załadowanie strony w celu utworzenia nowego obwodu. Nie ma to wpływu na obwody, które zostały przerwane, które są obwody dla innych użytkowników lub kart przeglądarki. Ten scenariusz jest podobny do aplikacji klasycznej,&mdash;w której awaria aplikacji musi zostać uruchomiona ponownie, ale nie ma to wpływu na inne aplikacje.

Obwód jest zakończony, gdy wystąpił nieobsługiwany wyjątek z następujących powodów:

* Nieobsługiwany wyjątek często pozostawia obwód w niezdefiniowanym stanie.
* Nie można zagwarantować normalnej operacji aplikacji po wystąpieniu nieobsłużonego wyjątku.
* W przypadku kontynuowania obwodu w aplikacji mogą pojawić się luki w zabezpieczeniach.

## <a name="manage-unhandled-exceptions-in-developer-code"></a>Zarządzanie nieobsługiwanymi wyjątkami w kodzie dewelopera

Aby aplikacja kontynuowała działanie po wystąpieniu błędu, aplikacja musi mieć logikę obsługi błędów. W dalszej części tego artykułu opisano potencjalne źródła nieobsłużonych wyjątków.

W środowisku produkcyjnym nie Renderuj komunikatów wyjątków struktury ani śladów stosu w interfejsie użytkownika. Renderowanie komunikatów o wyjątkach lub śladów stosu może:

* Ujawnianie poufnych informacji użytkownikom końcowym.
* Pomóż złośliwemu użytkownikowi wykrywać słabe strony w aplikacji, która może naruszyć bezpieczeństwo aplikacji, serwera lub sieci.

## <a name="log-errors-with-a-persistent-provider"></a>Rejestrowanie błędów przez dostawcę trwałego

Jeśli wystąpi nieobsługiwany wyjątek, wyjątek jest rejestrowany w <xref:Microsoft.Extensions.Logging.ILogger> wystąpieniach skonfigurowanych w kontenerze usługi. Domyślnie Blazor aplikacje rejestrują dane wyjściowe konsoli z dostawcą rejestrowania konsoli. Należy rozważyć logowanie do bardziej trwałej lokalizacji z dostawcą, który zarządza rozmiarem dziennika i rotacją dzienników. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/logging/index>.

Podczas opracowywania Blazor programu zwykle program wysyła do konsoli przeglądarki pełne szczegóły dotyczące wyjątków, aby pomóc w debugowaniu. W środowisku produkcyjnym szczegółowe błędy w konsoli przeglądarki są domyślnie wyłączone, co oznacza, że błędy nie są wysyłane do klientów, ale wszystkie szczegóły wyjątku nadal są rejestrowane po stronie serwera. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/error-handling>.

Należy zdecydować, które zdarzenia mają być rejestrowane, oraz poziom ważności zarejestrowanych zdarzeń. Nieszkodliwi użytkownicy mogą być w stanie wyzwolić błędy w sposób celowy. Na przykład nie należy rejestrować zdarzenia z błędu, gdy `ProductId` w adresie URL składnika, który zawiera szczegółowe informacje o produkcie. Nie wszystkie błędy powinny być traktowane jako zdarzenia o wysokiej ważności do rejestrowania.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/logging/index#create-logs-in-blazor>.

## <a name="places-where-errors-may-occur"></a>Miejsca, w których mogą wystąpić błędy

Kod struktury i aplikacji może wyzwolić Nieobsłużone wyjątki w jednej z następujących lokalizacji:

* [Tworzenie wystąpienia składnika](#component-instantiation)
* [Metody cyklu życia](#lifecycle-methods)
* [Logika renderowania](#rendering-logic)
* [Procedury obsługi zdarzeń](#event-handlers)
* [Usuwanie składników](#component-disposal)
* [Międzyoperacyjność w języku JavaScript](#javascript-interop)
* [BlazorRenderowanie serwera](#blazor-server-prerendering)

Poprzednie Nieobsłużone wyjątki zostały opisane w poniższych sekcjach tego artykułu.

### <a name="component-instantiation"></a>Tworzenie wystąpienia składnika

Podczas Blazor tworzenia wystąpienia składnika:

* Konstruktor składnika jest wywoływany.
* Konstruktory wszelkich niepojedynczych usług DI dostarczonych do konstruktora składnika za pośrednictwem [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) dyrektywy lub [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) atrybutu są wywoływane.

Obwód Blazor serwera kończy się niepowodzeniem, gdy dowolny wykonany Konstruktor lub setter `[Inject]` dla każdej właściwości zgłasza nieobsługiwany wyjątek. Wyjątek jest krytyczny, ponieważ struktura nie może utworzyć wystąpienia składnika. Jeśli logika konstruktora może generować wyjątki, aplikacja powinna zalewkować wyjątki przy użyciu instrukcji [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) z obsługą błędów i rejestrowaniem.

### <a name="lifecycle-methods"></a>Metody cyklu życia

W okresie istnienia składnika program Blazor wywołuje następujące [metody cyklu życia](xref:blazor/lifecycle):

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

Jeśli jakakolwiek metoda cyklu życia zgłasza wyjątek, synchronicznie lub asynchronicznie, wyjątek jest krytyczny dla Blazor obwodu serwera. Aby składniki zajmowały błędy w metodach cyklu życia, Dodaj logikę obsługi błędów.

W poniższym przykładzie, gdzie `OnParametersSetAsync` wywołuje metodę w celu uzyskania produktu:

* Wyjątek zgłoszony w `ProductRepository.GetProductByIdAsync` metodzie jest obsługiwany przez `try-catch` instrukcję.
* Gdy `catch` blok jest wykonywany:
  * `loadFailed`jest ustawiona na `true`, która jest używana do wyświetlania komunikatu o błędzie dla użytkownika.
  * Błąd jest rejestrowany.

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a>Logika renderowania

Znaczniki deklaratywne w pliku `.razor` składnika są kompilowane do metody języka C# o nazwie `BuildRenderTree`. Gdy składnik renderuje, `BuildRenderTree` wykonuje i tworzy strukturę danych opisującą elementy, tekst i składniki podrzędne renderowanego składnika.

Logika renderowania może zgłosić wyjątek. Przykład tego scenariusza występuje, gdy `@someObject.PropertyName` jest oceniane, ale `@someObject` jest `null`. Nieobsługiwany wyjątek zgłoszony przez logikę renderowania jest krytyczny dla obwodu Blazor serwera.

Aby zapobiec wystąpieniu wyjątku odwołania o wartości null w logice renderowania, `null` przed uzyskaniem dostępu do elementów członkowskich Sprawdź, czy jest on obiektem. W poniższym przykładzie właściwości nie `person.Address` są dostępne, jeśli `person.Address` `null`:

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

Poprzedni kod założono, `person` że `null`nie jest. Często Struktura kodu gwarantuje, że obiekt istnieje w momencie renderowania składnika. W takich przypadkach nie jest konieczne sprawdzanie `null` logiki renderowania. W poprzednim przykładzie można zagwarantować `person` , że istnieje, ponieważ `person` jest tworzony podczas tworzenia wystąpienia składnika.

### <a name="event-handlers"></a>Procedury obsługi zdarzeń

Kod po stronie klienta wyzwala wywołania kodu C#, gdy programy obsługi zdarzeń są tworzone przy użyciu:

* `@onclick`
* `@onchange`
* Inne `@on...` atrybuty
* `@bind`

Kod procedury obsługi zdarzeń może zgłosić nieobsługiwany wyjątek w tych scenariuszach.

Jeśli procedura obsługi zdarzeń zgłasza nieobsługiwany wyjątek (na przykład kwerenda bazy danych kończy się niepowodzeniem), wyjątek jest krytyczny dla obwodu Blazor serwera. Jeśli aplikacja wywołuje kod, który może zakończyć się niepowodzeniem z powodów zewnętrznych, należy zastosować wyjątek pułapki przy użyciu instrukcji [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) z obsługą błędów i rejestrowaniem.

Jeśli kod użytkownika nie jest pułapk i nie obsługuje wyjątku, struktura rejestruje wyjątek i kończy obwód.

### <a name="component-disposal"></a>Usuwanie składników

Składnik może zostać usunięty z interfejsu użytkownika, na przykład, ponieważ użytkownik przeszedł do innej strony. Gdy składnik implementujący <xref:System.IDisposable?displayProperty=fullName> jest usuwany z interfejsu użytkownika, struktura wywołuje <xref:System.IDisposable.Dispose*> metodę składnika.

Jeśli `Dispose` Metoda składnika zgłasza nieobsługiwany wyjątek, wyjątek jest krytyczny dla obwodu Blazor serwera. Jeśli logika usuwania może generować wyjątki, aplikacja powinna zalewkować wyjątki przy użyciu instrukcji [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) z obsługą błędów i rejestrowaniem.

Aby uzyskać więcej informacji na temat usuwania składników <xref:blazor/lifecycle#component-disposal-with-idisposable>, zobacz.

### <a name="javascript-interop"></a>Międzyoperacyjność w języku JavaScript

`IJSRuntime.InvokeAsync<T>`umożliwia programowi .NET Code wykonywanie wywołań asynchronicznych do środowiska uruchomieniowego JavaScript w przeglądarce użytkownika.

Poniższe warunki dotyczą obsługi błędów w programie `InvokeAsync<T>`:

* Jeśli wywołanie synchronicznie `InvokeAsync<T>` zakończy się niepowodzeniem, wystąpi wyjątek programu .NET. Wywołanie `InvokeAsync<T>` może zakończyć się niepowodzeniem, na przykład dlatego, że nie można serializować dostarczonych argumentów. Kod dewelopera musi przechwycić wyjątek. Jeśli kod aplikacji w obsłudze zdarzeń lub metoda cyklu życia składnika nie obsłuży wyjątku, wynikający z nich wyjątek jest krytyczny dla obwodu Blazor serwera.
* Jeśli wywołanie `InvokeAsync<T>` powiedzie się asynchronicznie, .NET <xref:System.Threading.Tasks.Task> kończy się niepowodzeniem. Wywołanie `InvokeAsync<T>` może zakończyć się niepowodzeniem, na przykład ponieważ kod po stronie JavaScript zgłasza wyjątek lub zwraca `Promise` , który został ukończony jako. `rejected` Kod dewelopera musi przechwycić wyjątek. W przypadku użycia operatora [await](/dotnet/csharp/language-reference/keywords/await) Rozważ zapakowanie wywołania metody w instrukcji [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) z obsługą błędów i rejestrowaniem. W przeciwnym razie niepowodzenie kodu spowoduje nieobsłużony wyjątek, który jest krytyczny dla obwodu Blazor serwera.
* Domyślnie wywołania programu muszą zakończyć `InvokeAsync<T>` się w określonym przedziale czasu lub w przeciwnym razie upłynął limit czasu połączenia. Domyślny limit czasu wynosi jedną minutę. Limit czasu chroni kod przed utratą połączenia sieciowego lub kodem JavaScript, który nigdy nie odsyła komunikat uzupełniający. Jeśli wystąpiło przełączenie, `Task` wynikiem kończy się <xref:System.OperationCanceledException>niepowodzeniem a. Zalewka i przetwórz wyjątek z rejestrowaniem.

Podobnie kod JavaScript może inicjować wywołania metod .NET wskazywanych przez [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) atrybut. Jeśli te metody .NET zgłaszają nieobsługiwany wyjątek:

* Wyjątek nie jest traktowany jako krytyczny Blazor dla obwodu serwera.
* Po stronie `Promise` JavaScript jest odrzucany.

Istnieje możliwość użycia kodu obsługi błędów po stronie .NET lub stronie JavaScript wywołania metody.

Aby uzyskać więcej informacji zobacz następujące artykuły:

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="opno-locblazor-server-prerendering"></a>BlazorRenderowanie na serwerze

Blazorskładniki mogą być wstępnie renderowane przy użyciu [pomocnika tagów składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) , dzięki czemu RENDEROWANE znaczniki HTML są zwracane jako część początkowego żądania HTTP użytkownika. Działa to w następujący sposób:

* Tworzenie nowego obwodu dla wszystkich wstępnie renderowanych składników, które są częścią tej samej strony.
* Generowanie początkowego kodu HTML.
* Przetraktowanie obwodu `disconnected` do momentu, aż przeglądarka SignalR użytkownika nawiąże połączenie z powrotem z tym samym serwerem. Po nawiązaniu połączenia zostanie wznowione działanie międzydziałające w obwodzie i zostanie zaktualizowane oznaczenie HTML składników.

Jeśli jakikolwiek składnik zgłasza nieobsłużony wyjątek podczas renderowania pre, na przykład podczas wykonywania metody cyklu życia lub logiki renderowania:

* Wyjątek jest krytyczny dla obwodu.
* Wyjątek jest generowany w stosie wywołań z pomocnika `Component` tagów. W związku z tym całe żądanie HTTP kończy się niepowodzeniem, chyba że wyjątek jest jawnie przechwycony przez kod dewelopera.

W normalnych warunkach w przypadku niepowodzenia wstępnego renderowania kontynuowanie kompilowania i renderowania składnika nie ma sensu, ponieważ nie można renderować składnika roboczego.

Aby tolerować błędy, które mogą wystąpić podczas renderowania prerenderingu, logika obsługi błędów musi być umieszczona wewnątrz składnika, który może zgłaszać wyjątki. Używaj instrukcji [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) z obsługą błędów i rejestrowaniem. Zamiast zawijać pomocnika `Component` tagów w `try-catch` instrukcji, umieść logikę obsługi błędów w składniku renderowanym przez `Component` pomocnika tagów.

## <a name="advanced-scenarios"></a>Scenariusze zaawansowane

### <a name="recursive-rendering"></a>Renderowanie cykliczne

Składniki można cyklicznie zagnieżdżać. Jest to przydatne do reprezentowania struktur danych rekursywnych. Na przykład `TreeNode` składnik może renderować więcej `TreeNode` składników dla każdego węzła podrzędnego.

W przypadku renderowania cyklicznego należy unikać tworzenia wzorców, które powodują nieskończoną rekursję:

* Nie Renderuj rekursywnie struktury danych zawierającej cykl. Na przykład nie Renderuj węzła drzewa, którego elementy podrzędne zawierają sam siebie.
* Nie twórz łańcucha układów zawierających cykl. Na przykład nie twórz układu, którego układ jest sam.
* Nie Zezwalaj użytkownikowi końcowemu na naruszenie nieodmian rekursji (reguł) przy użyciu złośliwego wpisu danych lub wywołań międzyoperacyjnych języka JavaScript.

Nieskończone pętle podczas renderowania:

* Powoduje, że proces renderowania kontynuuje działanie zawsze.
* Jest równoznaczny z tworzeniem niezakończonej pętli.

W tych scenariuszach obwód serwera, Blazor którego to dotyczy, kończy się niepowodzeniem, a wątek zwykle próbuje wykonać:

* Zużywaj ilość czasu procesora CPU dozwoloną przez system operacyjny w nieskończoność.
* Korzystaj z nieograniczonej ilości pamięci serwera. Zużywanie nieograniczonej pamięci jest równoważne scenariuszowi, w którym niezakończona pętla dodaje wpisy do kolekcji na każdej iteracji.

Aby uniknąć nieskończonych wzorców rekursji, należy się upewnić, że kod renderowania cyklicznego zawiera odpowiednie warunki zatrzymania.

### <a name="custom-render-tree-logic"></a>Logika drzewa renderowania niestandardowego

Większość Blazor składników jest zaimplementowana jako pliki *Razor* i są kompilowane do tworzenia logiki, która działa `RenderTreeBuilder` w celu renderowania danych wyjściowych. Deweloper może ręcznie zaimplementować `RenderTreeBuilder` logikę przy użyciu procedury kodu w języku C#. Aby uzyskać więcej informacji, zobacz <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.

> [!WARNING]
> Korzystanie z logiki konstruktora drzewa renderowania ręcznego jest uznawane za zaawansowane i niebezpieczne scenariusze, które nie są zalecane do ogólnego tworzenia składników.

Jeśli `RenderTreeBuilder` kod zostanie zapisany, Deweloper musi zagwarantować poprawność kodu. Na przykład Deweloper musi upewnić się, że:

* Wywołania `OpenElement` i `CloseElement` są prawidłowo zrównoważone.
* Atrybuty są dodawane tylko w prawidłowych miejscach.

Nieprawidłowa ręczna logika konstruktora drzewa renderowania może spowodować dowolne niezdefiniowane zachowanie, w tym awarie, zawieszenie serwera i luki w zabezpieczeniach.

Rozważ ręczne renderowanie logiki konstruktora drzew drzewa na tym samym poziomie złożoności i z tym samym poziomem *zagrożenia* co ręczne pisanie kodu zestawu lub instrukcji MSIL.
