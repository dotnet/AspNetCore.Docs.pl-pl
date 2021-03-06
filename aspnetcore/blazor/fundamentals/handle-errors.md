---
title: Obsługa błędów w Blazor aplikacjach ASP.NET Core
author: guardrex
description: Odkryj, jak ASP.NET Core Blazor jak program Blazor zarządza nieobsługiwanymi wyjątkami i jak opracowywać aplikacje wykrywające i obsługujące błędy.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2021
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
uid: blazor/fundamentals/handle-errors
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: d4c8054afc3818d58bc2a047a0aa74613ae6047b
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395100"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a>Obsługa błędów w Blazor aplikacjach ASP.NET Core

W tym artykule opisano, jak Blazor zarządzać nieobsługiwanymi wyjątkami oraz jak opracowywać aplikacje wykrywające i obsługujące błędy.

::: zone pivot="webassembly"

## <a name="detailed-errors-during-development"></a>Szczegóły błędów podczas opracowywania

Gdy Blazor aplikacja nie działa prawidłowo podczas opracowywania, otrzymuje szczegółowe informacje o błędzie z aplikacji, które pomagają w rozwiązywaniu problemów i rozwiązaniu problemu. Gdy wystąpi błąd, Blazor w dolnej części ekranu aplikacje są wyświetlane żółte paski:

* Podczas tworzenia pasek kieruje użytkownika do konsoli przeglądarki, gdzie można zobaczyć wyjątek.
* W środowisku produkcyjnym pasek powiadamia użytkownika o wystąpieniu błędu i zaleca odświeżenie przeglądarki.

Interfejs użytkownika dla tego środowiska obsługi błędów jest częścią [ Blazor szablonów projektu](xref:blazor/project-structure).

W Blazor WebAssembly aplikacji Dostosuj środowisko w `wwwroot/index.html` pliku:

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

`blazor-error-ui`Element jest zwykle ukryty z powodu obecności `display: none` stylu `blazor-error-ui` klasy CSS w arkuszu stylów aplikacji ( `wwwroot/css/app.css` ). Gdy wystąpi błąd, struktura ma zastosowanie `display: block` do elementu.

## <a name="manage-unhandled-exceptions-in-developer-code"></a>Zarządzanie nieobsługiwanymi wyjątkami w kodzie dewelopera

Aby aplikacja kontynuowała działanie po wystąpieniu błędu, aplikacja musi mieć logikę obsługi błędów. W dalszej części tego artykułu opisano potencjalne źródła nieobsłużonych wyjątków.

W środowisku produkcyjnym nie Renderuj komunikatów wyjątków struktury ani śladów stosu w interfejsie użytkownika. Renderowanie komunikatów o wyjątkach lub śladów stosu może:

* Ujawnianie poufnych informacji użytkownikom końcowym.
* Pomóż złośliwemu użytkownikowi wykrywać słabe strony w aplikacji, która może naruszyć bezpieczeństwo aplikacji, serwera lub sieci.

## <a name="global-exception-handling"></a>Globalna obsługa wyjątków

[!INCLUDE[](~/blazor/includes/handle-errors/global-exception-handling.md)]

## <a name="log-errors-with-a-persistent-provider"></a>Rejestrowanie błędów przez dostawcę trwałego

Jeśli wystąpi nieobsługiwany wyjątek, wyjątek jest rejestrowany w <xref:Microsoft.Extensions.Logging.ILogger> wystąpieniach skonfigurowanych w kontenerze usługi. Domyślnie Blazor aplikacje rejestrują dane wyjściowe konsoli z dostawcą rejestrowania konsoli. Należy rozważyć logowanie do bardziej trwałej lokalizacji na serwerze, wysyłając informacje o błędzie do interfejsu API sieci Web zaplecza, który używa dostawcy rejestrowania z zarządzaniem rozmiarem dziennika i rotacją dzienników. Alternatywnie aplikacja interfejsu API sieci Web zaplecza może używać usługi zarządzania wydajnością aplikacji (APM), takiej jak [Azure Application Insights (Azure monitor) &dagger; ](/azure/azure-monitor/app/app-insights-overview), aby rejestrować informacje o błędach odbierane od klientów.

Należy zdecydować, które zdarzenia mają być rejestrowane, oraz poziom ważności zarejestrowanych zdarzeń. Nieszkodliwi użytkownicy mogą być w stanie wyzwolić błędy w sposób celowy. Na przykład nie należy rejestrować zdarzenia z błędu, gdy `ProductId` w adresie URL składnika, który zawiera szczegółowe informacje o produkcie. Nie wszystkie błędy powinny być traktowane jako zdarzenia do rejestrowania.

Aby uzyskać więcej informacji, zobacz następujące artykuły:

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&Dagger;
* <xref:web-api/index>

&dagger;Natywne funkcje [Application Insights](/azure/azure-monitor/app/app-insights-overview) obsługujące Blazor WebAssembly aplikacje i natywną Blazor obsługę platformy [Google Analytics](https://analytics.google.com/analytics/web/) mogą być dostępne w przyszłych wersjach tych technologii. Aby uzyskać więcej informacji, zobacz temat [Obsługa usługi App Insights na Blazor stronie klienta WASM (Microsoft/ApplicationInsights-dotnet #2143)](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2143) oraz [Web Analytics i Diagnostics (w tym linki do implementacji społeczności) (w #5461 dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore/issues/5461). W międzyczasie aplikacja po stronie klienta Blazor WebAssembly może używać [Application Insights zestawu SDK języka JavaScript](/azure/azure-monitor/app/javascript) z usługą [js Interop](xref:blazor/call-javascript-from-dotnet) do rejestrowania błędów bezpośrednio do Application Insights z aplikacji po stronie klienta.

&Dagger;Dotyczy aplikacji ASP.NET Core po stronie serwera, które są aplikacjami zaplecza interfejsu API sieci Web dla Blazor aplikacji. Aplikacje po stronie klienta zalewki i wysyłają informacje o błędach do internetowego interfejsu API, który rejestruje informacje o błędzie dla dostawcy trwałego rejestrowania.

## <a name="places-where-errors-may-occur"></a>Miejsca, w których mogą wystąpić błędy

Kod struktury i aplikacji może wyzwolić Nieobsłużone wyjątki w następujących lokalizacjach, które opisano w następujących sekcjach tego artykułu:

* [Tworzenie wystąpienia składnika](#component-instantiation-webassembly)
* [Metody cyklu życia](#lifecycle-methods-webassembly)
* [Logika renderowania](#rendering-logic-webassembly)
* [Procedury obsługi zdarzeń](#event-handlers-webassembly)
* [Usuwanie składników](#component-disposal-webassembly)
* [Międzyoperacyjność w języku JavaScript](#javascript-interop-webassembly)

<h3 id="component-instantiation-webassembly">Tworzenie wystąpienia składnika</h3>

Podczas Blazor tworzenia wystąpienia składnika:

* Konstruktor składnika jest wywoływany.
* Konstruktory wszelkich niepojedynczych usług DI dostarczonych do konstruktora składnika za pośrednictwem [`@inject`](xref:mvc/views/razor#inject) dyrektywy lub [ `[Inject]` atrybutu](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) są wywoływane.

Błąd w wykonanym konstruktorze lub Metoda ustawiająca dla dowolnej `[Inject]` właściwości powoduje nieobsłużony wyjątek i uniemożliwia utworzenie wystąpienia składnika przez strukturę. Jeśli logika konstruktora może generować wyjątki, aplikacja powinna zalewkować wyjątki przy użyciu [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.

<h3 id="lifecycle-methods-webassembly">Metody cyklu życia</h3>

W okresie istnienia składnika Blazor wywołuje [metody cyklu życia](xref:blazor/components/lifecycle#lifecycle-methods). Aby składniki zajmowały błędy w metodach cyklu życia, Dodaj logikę obsługi błędów.

W poniższym przykładzie, gdzie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> wywołuje metodę w celu uzyskania produktu:

* Wyjątek zgłoszony w `ProductRepository.GetProductByIdAsync` metodzie jest obsługiwany przez [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcję.
* Gdy `catch` blok jest wykonywany:
  * `loadFailed` jest ustawiona na `true` , która jest używana do wyświetlania komunikatu o błędzie dla użytkownika.
  * Błąd jest rejestrowany.

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

<h3 id="rendering-logic-webassembly">Logika renderowania</h3>

Znaczniki deklaratywne w Razor pliku składnika ( `.razor` ) są kompilowane do metody języka C# o nazwie <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> . Gdy składnik renderuje, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> wykonuje i tworzy strukturę danych opisującą elementy, tekst i składniki podrzędne renderowanego składnika.

Logika renderowania może zgłosić wyjątek. Przykład tego scenariusza występuje, gdy `@someObject.PropertyName` jest oceniane, ale `@someObject` jest `null` .

Aby uniknąć <xref:System.NullReferenceException> renderowania logiki, `null` przed uzyskaniem dostępu do elementów członkowskich Sprawdź, czy jest on obiektem. W poniższym przykładzie `person.Address` właściwości nie są dostępne, jeśli `person.Address` `null` :

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

Poprzedni kod założono, że `person` nie jest `null` . Często Struktura kodu gwarantuje, że obiekt istnieje w momencie renderowania składnika. W takich przypadkach nie jest konieczne sprawdzanie `null` logiki renderowania. W poprzednim przykładzie `person` można zagwarantować, że istnieje, ponieważ `person` jest tworzony podczas tworzenia wystąpienia składnika, jak pokazano na poniższym przykładzie:

```razor
@code {
    private Person person = new Person();

    ...
}
```

<h3 id="event-handlers-webassembly">Procedury obsługi zdarzeń</h3>

Kod po stronie klienta wyzwala wywołania kodu C#, gdy programy obsługi zdarzeń są tworzone przy użyciu:

* `@onclick`
* `@onchange`
* Inne `@on...` atrybuty
* `@bind`

Kod procedury obsługi zdarzeń może zgłosić nieobsługiwany wyjątek w tych scenariuszach.

Jeśli aplikacja wywołuje kod, który może zakończyć się niepowodzeniem z powodów zewnętrznych, należy zastosować wyjątek pułapki przy użyciu [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.

Jeśli kod użytkownika nie pułapki i obsłuży wyjątek, struktura rejestruje wyjątek.

<h3 id="component-disposal-webassembly">Usuwanie składników</h3>

Składnik może zostać usunięty z interfejsu użytkownika, na przykład, ponieważ użytkownik przeszedł do innej strony. Gdy składnik implementujący <xref:System.IDisposable?displayProperty=fullName> jest usuwany z interfejsu użytkownika, struktura wywołuje <xref:System.IDisposable.Dispose%2A> metodę składnika.

Jeśli logika usuwania może generować wyjątki, aplikacja powinna zalewkować wyjątki przy użyciu [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.

Aby uzyskać więcej informacji na temat usuwania składników, zobacz <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .

<h3 id="javascript-interop-webassembly">Międzyoperacyjność w języku JavaScript</h3>

<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> umożliwia programowi .NET Code wykonywanie wywołań asynchronicznych do środowiska uruchomieniowego JavaScript w przeglądarce użytkownika.

Poniższe warunki dotyczą obsługi błędów w programie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> :

* Jeśli wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> synchronicznie zakończy się niepowodzeniem, wystąpi wyjątek programu .NET. Wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> może zakończyć się niepowodzeniem, na przykład dlatego, że nie można serializować dostarczonych argumentów. Kod dewelopera musi przechwycić wyjątek.
* Jeśli wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> powiedzie się asynchronicznie, .NET <xref:System.Threading.Tasks.Task> kończy się niepowodzeniem. Wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> może zakończyć się niepowodzeniem, na przykład ponieważ kod po stronie JavaScript zgłasza wyjątek lub zwraca `Promise` , który został ukończony jako `rejected` . Kod dewelopera musi przechwycić wyjątek. W przypadku użycia [`await`](/dotnet/csharp/language-reference/keywords/await) operatora Rozważ zapakowanie wywołania metody w [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.
* Domyślnie wywołania programu <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> muszą zakończyć się w określonym przedziale czasu lub w przeciwnym razie upłynął limit czasu połączenia. Domyślny limit czasu wynosi jedną minutę. Limit czasu chroni kod przed utratą połączenia sieciowego lub kodem JavaScript, który nigdy nie odsyła komunikat uzupełniający. Jeśli wystąpiło przełączenie, wynikiem <xref:System.Threading.Tasks> kończy się niepowodzeniem a <xref:System.OperationCanceledException> . Zalewka i przetwórz wyjątek z rejestrowaniem.

Podobnie kod JavaScript może inicjować wywołania metod .NET wskazywanych przez [ `[JSInvokable]` atrybut](xref:blazor/call-dotnet-from-javascript). Jeśli te metody .NET zgłaszają nieobsłużony wyjątek, po stronie JavaScript `Promise` zostanie odrzucona.

Istnieje możliwość użycia kodu obsługi błędów po stronie .NET lub stronie JavaScript wywołania metody.

Aby uzyskać więcej informacji, zobacz następujące artykuły:

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

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

W tych scenariuszach wątek zwykle próbuje wykonać:

* Zużywaj ilość czasu procesora CPU dozwoloną przez system operacyjny w nieskończoność.
* Korzystaj z nieograniczonej ilości pamięci klienta. Zużywanie nieograniczonej pamięci jest równoważne scenariuszowi, w którym niezakończona pętla dodaje wpisy do kolekcji na każdej iteracji.

Aby uniknąć nieskończonych wzorców rekursji, należy się upewnić, że kod renderowania cyklicznego zawiera odpowiednie warunki zatrzymania.

### <a name="custom-render-tree-logic"></a>Logika drzewa renderowania niestandardowego

Większość Blazor składników jest implementowana jako Razor pliki składników ( `.razor` ) i są kompilowane przez platformę w celu utworzenia logiki, która działa w <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> celu renderowania danych wyjściowych. Jednak deweloper może ręcznie zaimplementować <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logikę przy użyciu procedury kodu w języku C#. Aby uzyskać więcej informacji, zobacz <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.

> [!WARNING]
> Korzystanie z logiki konstruktora drzewa renderowania ręcznego jest uznawane za zaawansowane i niebezpieczne scenariusze, które nie są zalecane do ogólnego tworzenia składników.

Jeśli <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> kod zostanie zapisany, Deweloper musi zagwarantować poprawność kodu. Na przykład Deweloper musi upewnić się, że:

* Wywołania <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> i <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> są prawidłowo zrównoważone.
* Atrybuty są dodawane tylko w prawidłowych miejscach.

Nieprawidłowa ręczna logika konstruktora drzewa renderowania może spowodować dowolne niezdefiniowane zachowanie, w tym awarie, zawieszenie aplikacji i luki w zabezpieczeniach.

Rozważ ręczne renderowanie logiki konstruktora drzew drzewa na tym samym poziomie złożoności i z tym samym poziomem *zagrożenia* co ręczne pisanie kodu zestawu lub [języka pośredniego firmy Microsoft (MSIL)](/dotnet/standard/managed-code) .

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;
* <xref:web-api/index>

&dagger;Dotyczy aplikacji interfejsu API sieci Web ASP.NET Core zaplecza, które są Blazor WebAssembly używane przez aplikacje po stronie klienta do rejestrowania.

::: zone-end

::: zone pivot="server"

## <a name="detailed-errors-during-development"></a>Szczegóły błędów podczas opracowywania

Gdy Blazor aplikacja nie działa prawidłowo podczas opracowywania, otrzymuje szczegółowe informacje o błędzie z aplikacji, które pomagają w rozwiązywaniu problemów i rozwiązaniu problemu. Gdy wystąpi błąd, Blazor w dolnej części ekranu aplikacje są wyświetlane żółte paski:

* Podczas tworzenia pasek kieruje użytkownika do konsoli przeglądarki, gdzie można zobaczyć wyjątek.
* W środowisku produkcyjnym pasek powiadamia użytkownika o wystąpieniu błędu i zaleca odświeżenie przeglądarki.

Interfejs użytkownika dla tego środowiska obsługi błędów jest częścią [ Blazor szablonów projektu](xref:blazor/project-structure).

W Blazor Server aplikacji Dostosuj środowisko w `Pages/_Host.cshtml` pliku:

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

`blazor-error-ui`Element jest zwykle ukryty z powodu obecności `display: none` stylu `blazor-error-ui` klasy CSS w arkuszu stylów witryny ( `wwwroot/css/site.css` ). Gdy wystąpi błąd, struktura ma zastosowanie `display: block` do elementu.

## <a name="blazor-server-detailed-circuit-errors"></a>Blazor Server Szczegóły błędów obwodu

Błędy po stronie klienta nie obejmują stosu wywołań i nie zawierają szczegółów przyczyny błędu, ale Dzienniki serwera zawierają takie informacje. W celach programistycznych informacje o błędzie obwodu poufnego mogą być udostępniane klientowi, włączając szczegółowe błędy.

Ustaw <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> wartość `true` . Aby uzyskać więcej informacji i zapoznać się z przykładem, zobacz <xref:blazor/fundamentals/signalr#circuit-handler-options> .

Alternatywą dla ustawienia <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> jest ustawienie `DetailedErrors` klucza konfiguracji `true` w pliku ustawień środowiska deweloperskiego aplikacji ( `appsettings.Development.json` ).  Ponadto należy ustawić [ SignalR funkcję rejestrowania po stronie serwera](xref:signalr/diagnostics#server-side-logging) ( `Microsoft.AspNetCore.SignalR` ) w celu [debugowania](xref:Microsoft.Extensions.Logging.LogLevel) lub [śledzenia](xref:Microsoft.Extensions.Logging.LogLevel) szczegółowego SignalR rejestrowania.

`appsettings.Development.json`:

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

<xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors>Klucz konfiguracji można również ustawić `true` przy użyciu `ASPNETCORE_DETAILEDERRORS` zmiennej środowiskowej o wartości `true` na serwerach środowiska deweloperskiego/przejściowego lub w systemie lokalnym.

> [!WARNING]
> Zawsze należy unikać ujawniania informacji o błędach klientom w Internecie, co stanowi zagrożenie bezpieczeństwa.

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a>Jak Blazor Server Aplikacja reaguje na Nieobsłużone wyjątki

Blazor Server jest strukturą stanową. Gdy użytkownicy współpracują z aplikacją, utrzymują połączenie z serwerem znanym jako *obwód*. Obwód zawiera aktywne wystąpienia składnika, a także wiele innych aspektów stanu, takich jak:

* Najnowsze renderowane dane wyjściowe składników.
* Bieżący zestaw delegatów obsługi zdarzeń, które mogą być wyzwalane przez zdarzenia po stronie klienta.

Jeśli użytkownik otworzy aplikację na wielu kartach przeglądarki, użytkownik utworzy wiele niezależnych obwody.

Blazor traktuje większość nieobsłużonych wyjątków jako krytyczne dla obwodu, w którym występują. Jeśli obwód zostanie przerwany z powodu nieobsługiwanego wyjątku, użytkownik może nadal korzystać z aplikacji tylko przez ponowne załadowanie strony w celu utworzenia nowego obwodu. Nie ma to wpływu na obwody, które zostały przerwane, które są obwody dla innych użytkowników lub kart przeglądarki. Ten scenariusz jest podobny do aplikacji klasycznej, która ulega awarii. Aplikacja, która uległa awarii, musi zostać ponownie uruchomiona, ale nie ma to wpływu na inne aplikacje.

Struktura kończy obwód, gdy wystąpi nieobsługiwany wyjątek z następujących powodów:

* Nieobsługiwany wyjątek często pozostawia obwód w niezdefiniowanym stanie.
* Nie można zagwarantować normalnej operacji aplikacji po wystąpieniu nieobsłużonego wyjątku.
* Luki w zabezpieczeniach mogą pojawić się w aplikacji, jeśli obwód będzie kontynuował działanie w niezdefiniowanym stanie.

## <a name="manage-unhandled-exceptions-in-developer-code"></a>Zarządzanie nieobsługiwanymi wyjątkami w kodzie dewelopera

Aby aplikacja kontynuowała działanie po wystąpieniu błędu, aplikacja musi mieć logikę obsługi błędów. W dalszej części tego artykułu opisano potencjalne źródła nieobsłużonych wyjątków.

W środowisku produkcyjnym nie Renderuj komunikatów wyjątków struktury ani śladów stosu w interfejsie użytkownika. Renderowanie komunikatów o wyjątkach lub śladów stosu może:

* Ujawnianie poufnych informacji użytkownikom końcowym.
* Pomóż złośliwemu użytkownikowi wykrywać słabe strony w aplikacji, która może naruszyć bezpieczeństwo aplikacji, serwera lub sieci.

## <a name="global-exception-handling"></a>Globalna obsługa wyjątków

[!INCLUDE[](~/blazor/includes/handle-errors/global-exception-handling.md)]

Ponieważ metody przedstawione w tej sekcji obsługują błędy w [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji, SignalR połączenie między klientem i serwerem nie jest zerwane w przypadku wystąpienia błędu, a obwód pozostaje aktywny. Dowolny nieobsługiwany wyjątek jest krytyczny dla obwodu. Aby uzyskać więcej informacji, zobacz poprzednią sekcję dotyczącą sposobu, w [jaki Blazor Server Aplikacja reaguje na Nieobsłużone wyjątki](#how-a-blazor-server-app-reacts-to-unhandled-exceptions).

## <a name="log-errors-with-a-persistent-provider"></a>Rejestrowanie błędów przez dostawcę trwałego

Jeśli wystąpi nieobsługiwany wyjątek, wyjątek jest rejestrowany w <xref:Microsoft.Extensions.Logging.ILogger> wystąpieniach skonfigurowanych w kontenerze usługi. Domyślnie Blazor aplikacje rejestrują dane wyjściowe konsoli z dostawcą rejestrowania konsoli. Należy rozważyć rejestrowanie do bardziej trwałej lokalizacji na serwerze z dostawcą, który zarządza rozmiarem dziennika i rotacją dzienników. Alternatywnie aplikacja może używać usługi zarządzania wydajnością aplikacji (APM), takiej jak [Azure Application Insights (Azure monitor)](/azure/azure-monitor/app/app-insights-overview).

Podczas opracowywania Blazor Server aplikacja zwykle wysyła pełne szczegóły wyjątków do konsoli przeglądarki w celu ułatwienia debugowania. W środowisku produkcyjnym szczegółowe błędy nie są wysyłane do klientów, ale na serwerze są rejestrowane pełne szczegóły wyjątku.

Należy zdecydować, które zdarzenia mają być rejestrowane, oraz poziom ważności zarejestrowanych zdarzeń. Nieszkodliwi użytkownicy mogą być w stanie wyzwolić błędy w sposób celowy. Na przykład nie należy rejestrować zdarzenia z błędu, gdy `ProductId` w adresie URL składnika, który zawiera szczegółowe informacje o produkcie. Nie wszystkie błędy powinny być traktowane jako zdarzenia do rejestrowania.

Aby uzyskać więcej informacji, zobacz następujące artykuły:

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;

&dagger;Dotyczy aplikacji ASP.NET Core po stronie serwera, które są aplikacjami zaplecza interfejsu API sieci Web dla Blazor aplikacji.

## <a name="places-where-errors-may-occur"></a>Miejsca, w których mogą wystąpić błędy

Kod struktury i aplikacji może wyzwolić Nieobsłużone wyjątki w następujących lokalizacjach, które opisano w następujących sekcjach tego artykułu:

* [Tworzenie wystąpienia składnika](#component-instantiation-server)
* [Metody cyklu życia](#lifecycle-methods-server)
* [Logika renderowania](#rendering-logic-server)
* [Procedury obsługi zdarzeń](#event-handlers-server)
* [Usuwanie składników](#component-disposal-server)
* [Międzyoperacyjność w języku JavaScript](#javascript-interop-server)
* [Blazor Server Renderowanie](#blazor-server-prerendering-server)

<h3 id="component-instantiation-server">Tworzenie wystąpienia składnika</h3>

Podczas Blazor tworzenia wystąpienia składnika:

* Konstruktor składnika jest wywoływany.
* Konstruktory wszelkich niepojedynczych usług DI dostarczonych do konstruktora składnika za pośrednictwem [`@inject`](xref:mvc/views/razor#inject) dyrektywy lub [ `[Inject]` atrybutu](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) są wywoływane.

Blazor ServerObwód kończy się niepowodzeniem, gdy dowolny wykonany Konstruktor lub setter dla każdej `[Inject]` właściwości zgłasza nieobsługiwany wyjątek. Wyjątek jest krytyczny, ponieważ struktura nie może utworzyć wystąpienia składnika. Jeśli logika konstruktora może generować wyjątki, aplikacja powinna zalewkować wyjątki przy użyciu [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.

<h3 id="lifecycle-methods-server">Metody cyklu życia</h3>

W okresie istnienia składnika Blazor wywołuje [metody cyklu życia](xref:blazor/components/lifecycle#lifecycle-methods). Jeśli jakakolwiek metoda cyklu życia zgłasza wyjątek, synchronicznie lub asynchronicznie, wyjątek jest krytyczny dla Blazor Server obwodu. Aby składniki zajmowały błędy w metodach cyklu życia, Dodaj logikę obsługi błędów.

W poniższym przykładzie, gdzie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> wywołuje metodę w celu uzyskania produktu:

* Wyjątek zgłoszony w `ProductRepository.GetProductByIdAsync` metodzie jest obsługiwany przez [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcję.
* Gdy `catch` blok jest wykonywany:
  * `loadFailed` jest ustawiona na `true` , która jest używana do wyświetlania komunikatu o błędzie dla użytkownika.
  * Błąd jest rejestrowany.

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

<h3 id="rendering-logic-server">Logika renderowania</h3>

Znaczniki deklaratywne w Razor pliku składnika ( `.razor` ) są kompilowane do metody języka C# o nazwie <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> . Gdy składnik renderuje, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> wykonuje i tworzy strukturę danych opisującą elementy, tekst i składniki podrzędne renderowanego składnika.

Logika renderowania może zgłosić wyjątek. Przykład tego scenariusza występuje, gdy `@someObject.PropertyName` jest oceniane, ale `@someObject` jest `null` . Nieobsługiwany wyjątek zgłoszony przez logikę renderowania jest krytyczny dla Blazor Server obwodu.

Aby uniknąć <xref:System.NullReferenceException> renderowania logiki, `null` przed uzyskaniem dostępu do elementów członkowskich Sprawdź, czy jest on obiektem. W poniższym przykładzie `person.Address` właściwości nie są dostępne, jeśli `person.Address` `null` :

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

Poprzedni kod założono, że `person` nie jest `null` . Często Struktura kodu gwarantuje, że obiekt istnieje w momencie renderowania składnika. W takich przypadkach nie jest konieczne sprawdzanie `null` logiki renderowania. W poprzednim przykładzie `person` można zagwarantować, że istnieje, ponieważ `person` jest tworzony podczas tworzenia wystąpienia składnika, jak pokazano na poniższym przykładzie:

```razor
@code {
    private Person person = new Person();

    ...
}
```

<h3 id="event-handlers-server">Procedury obsługi zdarzeń</h3>

Kod po stronie klienta wyzwala wywołania kodu C#, gdy programy obsługi zdarzeń są tworzone przy użyciu:

* `@onclick`
* `@onchange`
* Inne `@on...` atrybuty
* `@bind`

Kod procedury obsługi zdarzeń może zgłosić nieobsługiwany wyjątek w tych scenariuszach.

Jeśli procedura obsługi zdarzeń zgłasza nieobsługiwany wyjątek (na przykład kwerenda bazy danych kończy się niepowodzeniem), wyjątek jest krytyczny dla Blazor Server obwodu. Jeśli aplikacja wywołuje kod, który może zakończyć się niepowodzeniem z powodów zewnętrznych, należy zastosować wyjątek pułapki przy użyciu [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.

Jeśli kod użytkownika nie jest pułapk i nie obsługuje wyjątku, struktura rejestruje wyjątek i kończy obwód.

<h3 id="component-disposal-server">Usuwanie składników</h3>

Składnik może zostać usunięty z interfejsu użytkownika, na przykład, ponieważ użytkownik przeszedł do innej strony. Gdy składnik implementujący <xref:System.IDisposable?displayProperty=fullName> jest usuwany z interfejsu użytkownika, struktura wywołuje <xref:System.IDisposable.Dispose%2A> metodę składnika.

Jeśli `Dispose` Metoda składnika zgłasza nieobsługiwany wyjątek, wyjątek jest krytyczny dla Blazor Server obwodu. Jeśli logika usuwania może generować wyjątki, aplikacja powinna zalewkować wyjątki przy użyciu [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.

Aby uzyskać więcej informacji na temat usuwania składników, zobacz <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .

<h3 id="javascript-interop-server">Międzyoperacyjność w języku JavaScript</h3>

<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> umożliwia programowi .NET Code wykonywanie wywołań asynchronicznych do środowiska uruchomieniowego JavaScript w przeglądarce użytkownika.

Poniższe warunki dotyczą obsługi błędów w programie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> :

* Jeśli wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> synchronicznie zakończy się niepowodzeniem, wystąpi wyjątek programu .NET. Wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> może zakończyć się niepowodzeniem, na przykład dlatego, że nie można serializować dostarczonych argumentów. Kod dewelopera musi przechwycić wyjątek. Jeśli kod aplikacji w obsłudze zdarzeń lub metoda cyklu życia składnika nie obsługuje wyjątku, wynikający z nich wyjątek jest krytyczny dla Blazor Server obwodu.
* Jeśli wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> powiedzie się asynchronicznie, .NET <xref:System.Threading.Tasks.Task> kończy się niepowodzeniem. Wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> może zakończyć się niepowodzeniem, na przykład ponieważ kod po stronie JavaScript zgłasza wyjątek lub zwraca `Promise` , który został ukończony jako `rejected` . Kod dewelopera musi przechwycić wyjątek. W przypadku użycia [`await`](/dotnet/csharp/language-reference/keywords/await) operatora Rozważ zapakowanie wywołania metody w [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem. W przeciwnym razie niepowodzenie kodu spowoduje nieobsłużony wyjątek, który jest krytyczny dla Blazor Server obwodu.
* Domyślnie wywołania programu <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> muszą zakończyć się w określonym przedziale czasu lub w przeciwnym razie upłynął limit czasu połączenia. Domyślny limit czasu wynosi jedną minutę. Limit czasu chroni kod przed utratą połączenia sieciowego lub kodem JavaScript, który nigdy nie odsyła komunikat uzupełniający. Jeśli wystąpiło przełączenie, wynikiem <xref:System.Threading.Tasks> kończy się niepowodzeniem a <xref:System.OperationCanceledException> . Zalewka i przetwórz wyjątek z rejestrowaniem.

Podobnie kod JavaScript może inicjować wywołania metod .NET wskazywanych przez [ `[JSInvokable]` atrybut](xref:blazor/call-dotnet-from-javascript). Jeśli te metody .NET zgłaszają nieobsługiwany wyjątek:

* Wyjątek nie jest traktowany jako krytyczny dla Blazor Server obwodu.
* Po stronie JavaScript `Promise` jest odrzucany.

Istnieje możliwość użycia kodu obsługi błędów po stronie .NET lub stronie JavaScript wywołania metody.

Aby uzyskać więcej informacji, zobacz następujące artykuły:

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

<h3 id="blazor-server-prerendering-server">Blazor Server Renderowanie prerenderingu</h3>

Blazor składniki mogą być wstępnie renderowane przy użyciu [pomocnika tagów składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) , dzięki czemu RENDEROWANE znaczniki HTML są zwracane jako część początkowego żądania HTTP użytkownika. Działa to w następujący sposób:

* Tworzenie nowego obwodu dla wszystkich wstępnie renderowanych składników, które są częścią tej samej strony.
* Generowanie początkowego kodu HTML.
* Przetraktowanie obwodu `disconnected` do momentu, aż przeglądarka użytkownika nawiąże SignalR połączenie z powrotem z tym samym serwerem. Po nawiązaniu połączenia zostanie wznowione działanie międzydziałające w obwodzie i zostanie zaktualizowane oznaczenie HTML składników.

Jeśli jakikolwiek składnik zgłasza nieobsłużony wyjątek podczas renderowania pre, na przykład podczas wykonywania metody cyklu życia lub logiki renderowania:

* Wyjątek jest krytyczny dla obwodu.
* Wyjątek jest generowany w stosie wywołań z <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> pomocnika tagów. W związku z tym całe żądanie HTTP kończy się niepowodzeniem, chyba że wyjątek jest jawnie przechwycony przez kod dewelopera.

W normalnych warunkach w przypadku niepowodzenia wstępnego renderowania kontynuowanie kompilowania i renderowania składnika nie ma sensu, ponieważ nie można renderować składnika roboczego.

Aby tolerować błędy, które mogą wystąpić podczas renderowania prerenderingu, logika obsługi błędów musi być umieszczona wewnątrz składnika, który może zgłaszać wyjątki. Używaj [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem. Zamiast zawijać <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> pomocnika tagów w [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji, umieść logikę obsługi błędów w składniku renderowanym przez <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> pomocnika tagów.

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

W tych scenariuszach obwód, którego to dotyczy, Blazor Server kończy się niepowodzeniem, a wątek zwykle próbuje wykonać:

* Zużywaj ilość czasu procesora CPU dozwoloną przez system operacyjny w nieskończoność.
* Korzystaj z nieograniczonej ilości pamięci serwera. Zużywanie nieograniczonej pamięci jest równoważne scenariuszowi, w którym niezakończona pętla dodaje wpisy do kolekcji na każdej iteracji.

Aby uniknąć nieskończonych wzorców rekursji, należy się upewnić, że kod renderowania cyklicznego zawiera odpowiednie warunki zatrzymania.

### <a name="custom-render-tree-logic"></a>Logika drzewa renderowania niestandardowego

Większość Blazor składników jest implementowana jako Razor pliki składników ( `.razor` ) i są kompilowane przez platformę w celu utworzenia logiki, która działa w <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> celu renderowania danych wyjściowych. Jednak deweloper może ręcznie zaimplementować <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logikę przy użyciu procedury kodu w języku C#. Aby uzyskać więcej informacji, zobacz <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.

> [!WARNING]
> Korzystanie z logiki konstruktora drzewa renderowania ręcznego jest uznawane za zaawansowane i niebezpieczne scenariusze, które nie są zalecane do ogólnego tworzenia składników.

Jeśli <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> kod zostanie zapisany, Deweloper musi zagwarantować poprawność kodu. Na przykład Deweloper musi upewnić się, że:

* Wywołania <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> i <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> są prawidłowo zrównoważone.
* Atrybuty są dodawane tylko w prawidłowych miejscach.

Nieprawidłowa ręczna logika konstruktora drzewa renderowania może spowodować dowolne niezdefiniowane zachowanie, w tym awarie, zawieszenie serwera i luki w zabezpieczeniach.

Rozważ ręczne renderowanie logiki konstruktora drzew drzewa na tym samym poziomie złożoności i z tym samym poziomem *zagrożenia* co ręczne pisanie kodu zestawu lub [języka pośredniego firmy Microsoft (MSIL)](/dotnet/standard/managed-code) .

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;

&dagger;Dotyczy aplikacji ASP.NET Core po stronie serwera, które są aplikacjami zaplecza interfejsu API sieci Web dla Blazor aplikacji.

::: zone-end
