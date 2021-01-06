---
title: Wskazówki dotyczące łagodzenia zagrożeń dla ASP.NET Core Blazor Server
author: guardrex
description: Dowiedz się, jak ograniczyć zagrożenia bezpieczeństwa do Blazor Server aplikacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/09/2020
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
uid: blazor/security/server/threat-mitigation
ms.openlocfilehash: d0ed36731d78d3e98aa294aca50492f0a3ac8174
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97506698"
---
# <a name="threat-mitigation-guidance-for-aspnet-core-no-locblazor-server"></a>Wskazówki dotyczące łagodzenia zagrożeń dla ASP.NET Core Blazor Server

Autor [Javier Calvarro Nelson](https://github.com/javiercn)

Blazor Server aplikacje przyjmują model przetwarzania danych *stanowych* , w którym serwer i klient utrzymują długoterminową relację. Stan trwały jest obsługiwany przez [obwód](xref:blazor/state-management), który może obejmować połączenia, które są również potencjalnie długotrwałe.

Gdy użytkownik odwiedza Blazor Server lokację, serwer tworzy obwód w pamięci serwera. Obwód wskazuje przeglądarce zawartość do renderowania i reagowanie na zdarzenia, na przykład gdy użytkownik wybierze przycisk w interfejsie użytkownika. Aby wykonać te czynności, obwód wywołuje funkcje języka JavaScript w przeglądarce użytkownika i metody .NET na serwerze. Ta dwukierunkowa interakcja oparta na języku JavaScript jest nazywana JavaScript międzyoperacyjną [(js Interop)](xref:blazor/call-javascript-from-dotnet).

Ze względu na to, że program JS Interop odbywa się przez Internet, a klient korzysta z przeglądarki zdalnej, Blazor Server aplikacje korzystają z większości problemów z zabezpieczeniami aplikacji sieci Web. W tym temacie opisano typowe zagrożenia dla Blazor Server aplikacji i przedstawiono wskazówki dotyczące łagodzenia zagrożeń ukierunkowane na aplikacje internetowe.

W środowiskach z ograniczeniami, takimi jak wewnątrz sieci firmowej lub intranetów, niektóre wskazówki dotyczące ograniczenia są następujące:

* Nie dotyczy w ograniczonym środowisku.
* Nie jest kosztem wdrożenia, ponieważ zagrożenie bezpieczeństwa jest niskie w ograniczonym środowisku.

## <a name="no-locblazor-and-shared-state"></a>Blazor i udostępniony stan

[!INCLUDE[](~/blazor/includes/security/blazor-shared-state.md)]

## <a name="resource-exhaustion"></a>Wyczerpanie zasobów

Wyczerpanie zasobów może wystąpić, gdy klient współdziała z serwerem i powoduje, że serwer zużywa nadmierne zasoby. Nadmierne wykorzystanie zasobów dotyczy głównie:

* [Procesor CPU](#cpu)
* [Pamięć](#memory)
* [Połączenia klienta](#client-connections)

Ataki typu "odmowa usługi" (DoS) zwykle poszukują wyczerpania zasobów aplikacji lub serwera. Jednak wyczerpanie zasobów nie musi być przyczyną ataku w systemie. Na przykład ograniczone zasoby mogą być wyczerpane ze względu na wysokie zapotrzebowanie użytkownika. System DoS został szczegółowo omówiony w sekcji [ataki typu "odmowa usługi" (DOS)](#denial-of-service-dos-attacks) .

Zasoby zewnętrzne dotyczące Blazor platformy, takie jak bazy danych i dojścia do plików (używane do odczytu i zapisu plików) mogą również powodować wyczerpanie zasobów. Aby uzyskać więcej informacji, zobacz <xref:performance/performance-best-practices>.

### <a name="cpu"></a>Procesor CPU

Wyczerpanie procesora może wystąpić, gdy jeden lub więcej klientów wymusza intensywną realizację procesora CPU przez serwer.

Rozważmy na przykład Blazor Server aplikację, która oblicza *numer Fibonnacci*. Numer Fibonnacci jest tworzony z sekwencji Fibonnacci, gdzie każda liczba w sekwencji jest sumą dwóch poprzednich numerów. Ilość pracy wymaganej do osiągnięcia odpowiedzi zależy od długości sekwencji i rozmiaru wartości początkowej. Jeśli aplikacja nie nakłada limitów na żądanie klienta, obliczenia intensywnie korzystające z procesora CPU mogą wzniżyć czas procesora i zmniejszyć wydajność innych zadań. Nadmierne zużycie zasobów to wpływ na dostępność.

Wykorzystanie procesora CPU jest problemem w przypadku wszystkich aplikacji publicznych. W zwykłych aplikacjach sieci Web żądania i połączenia przekroczą limit czasu jako zabezpieczenie, ale Blazor Server aplikacje nie zapewniają tych samych zabezpieczeń. Blazor Server aplikacje muszą zawierać odpowiednie sprawdzenia i limity przed przeprowadzeniem potencjalnej pracy intensywnie obciążającej procesor CPU.

### <a name="memory"></a>Pamięć

Wyczerpanie pamięci może wystąpić, gdy co najmniej jeden klient wymusić zużywanie dużej ilości pamięci na serwerze.

Rozważmy na przykład Blazor aplikację po stronie serwera ze składnikiem akceptującym i wyświetlającym listę elementów. Jeśli Blazor aplikacja nie nakłada limitów na liczbę dozwolonych elementów lub liczbę elementów, które zostały wyrenderowane z powrotem do klienta, przetwarzanie i renderowanie intensywnie korzystające z pamięci może wznieść ilość pamięci serwera do punktu, w którym pogorszy się wydajność serwera. Serwer może ulec awarii lub wolno do punktu, w którym wystąpiła awaria.

Rozważmy następujący scenariusz utrzymywania i wyświetlania listy elementów odnoszących się do potencjalnego scenariusza wyczerpania pamięci na serwerze:

* Elementy w `List<MyItem>` właściwości lub polu używają pamięci serwera. Jeśli aplikacja zezwala na niepowiązaną listę elementów, istnieje ryzyko, że serwer kończy Brak pamięci. Brak pamięci powoduje, że bieżąca sesja na zakończenie (awaria) i wszystkie sesje współbieżne w tym wystąpieniu serwera odbierają wyjątek braku pamięci. Aby zapobiec wystąpieniu tego scenariusza, aplikacja musi używać struktury danych, która nakłada limit elementów na współbieżnych użytkowników.
* Jeśli schemat stronicowania nie jest używany do renderowania, serwer używa dodatkowej pamięci dla obiektów, które nie są widoczne w interfejsie użytkownika. Bez limitu liczby elementów, wymagania dotyczące pamięci mogą wyczerpać dostępną pamięć serwera. Aby uniknąć tego scenariusza, należy użyć jednej z następujących metod:
  * Użyj list z podziałem na strony podczas renderowania.
  * Wyświetlić tylko pierwsze 100 do 1 000 elementów i wymagać od użytkownika wprowadzenia kryteriów wyszukiwania, aby znaleźć elementy poza wyświetlanymi elementami.
  * Aby zapoznać się z bardziej zaawansowanym scenariuszem renderowania, zaimplementuj listy lub siatki obsługujące *wirtualizację*. Przy użyciu wirtualizacji program wyświetla tylko podzbiór elementów, które są obecnie widoczne dla użytkownika. Gdy użytkownik współdziała z paskiem przewijania w interfejsie użytkownika, składnik renderuje tylko te elementy, które są wymagane do wyświetlenia. Elementy, które nie są obecnie wymagane do wyświetlania, mogą być przechowywane w magazynie pomocniczym, co jest idealnym rozwiązaniem. Niewyświetlane elementy można również przechowywać w pamięci, co jest mniej idealne.

Blazor Server aplikacje oferują podobny model programowania dla innych platform interfejsu użytkownika dla aplikacji stanowych, takich jak WPF, Windows Forms lub Blazor WebAssembly . Główną różnicą jest to, że w kilku strukturach interfejsu użytkownika używana przez aplikację pamięć należy do klienta i ma wpływ tylko na danego klienta. Na przykład Blazor WebAssembly aplikacja działa wyłącznie na kliencie i używa zasobów pamięci klienta. W tym Blazor Server scenariuszu pamięć używana przez aplikację należy do serwera i jest współdzielona przez klientów w wystąpieniu serwera.

Wymagania dotyczące pamięci po stronie serwera są rozpatrywane dla wszystkich Blazor Server aplikacji. Większość aplikacji sieci Web jest jednak bezstanowa, a pamięć użyta podczas przetwarzania żądania jest wydawana po zwróceniu odpowiedzi. Zgodnie z ogólnym zaleceniem nie należy zezwalać klientom na przydzielanie nieograniczonej ilości pamięci, tak jak w przypadku innych aplikacji po stronie serwera, które utrzymują połączenia klientów. Ilość pamięci zużywanej przez Blazor Server aplikację jest zachowywana przez dłuższy czas niż pojedyncze żądanie.

> [!NOTE]
> Podczas programowania można użyć profilera lub przechwycić ślad w celu oceny wymagań pamięci klientów. Program profilujący lub ślad nie będzie przechwytywać pamięci przyprzypisanej do określonego klienta. Aby przechwycić wykorzystanie pamięci przez określonego klienta podczas tworzenia, Przechwyć zrzut i sprawdź zapotrzebowanie na pamięć wszystkich obiektów, które zostały umieszczone w obwodzie użytkownika.

### <a name="client-connections"></a>Połączenia klienta

Wyczerpanie połączenia może wystąpić, gdy co najmniej jeden klient otwiera zbyt wiele równoczesnych połączeń z serwerem, uniemożliwiając innym klientom nawiązywanie nowych połączeń.

Blazor Klienci nawiązują pojedyncze połączenie dla każdej sesji i przechowują połączenie tak długo, jak okno przeglądarki jest otwarte. Wymagania na serwerze utrzymywania wszystkich połączeń nie są specyficzne dla Blazor aplikacji. Ze względu na trwały charakter połączeń i stanowy charakter Blazor Server aplikacji, wyczerpanie połączenia jest bardziej ryzykowne dla dostępności aplikacji.

Domyślnie nie ma żadnego limitu liczby połączeń na użytkownika dla Blazor Server aplikacji. Jeśli aplikacja wymaga limitu połączeń, należy wykonać co najmniej jedną z następujących metod:

* Wymaganie uwierzytelniania, które w sposób naturalny ogranicza możliwość łączenia się z aplikacją przez nieautoryzowanych użytkowników. Aby ten scenariusz był skuteczny, użytkownicy muszą mieć możliwość zapobiegania aprowizacji nowych użytkowników.
* Ogranicz liczbę połączeń na użytkownika. Ograniczenia połączeń można wykonać przy użyciu poniższych metod. Należy zachować ostrożność, aby zezwolić uprawnionym użytkownikom na dostęp do aplikacji (na przykład w przypadku ustanowienia limitu połączeń na podstawie adresu IP klienta).
  * Na poziomie aplikacji:
    * Rozszerzalność routingu punktu końcowego.
    * Wymagaj uwierzytelniania w celu nawiązania połączenia z aplikacją i śledzenia aktywnych sesji na użytkownika.
    * Odrzuć nowe sesje po osiągnięciu limitu.
    * Połączenia protokołu WebSocket serwera proxy z aplikacją za pomocą serwera proxy, takiego jak [ SignalR usługa platformy Azure](/azure/azure-signalr/signalr-overview) , która umożliwia multiplekser połączeń klientów z aplikacją. Zapewnia to aplikacji o większej pojemności połączenia niż może nawiązać pojedynczy klient, co uniemożliwia klientowi wyczerpanie połączeń z serwerem.
  * Na poziomie serwera: Użyj serwera proxy/bramy przed aplikacją. Na przykład, [frontony platformy Azure](/azure/frontdoor/front-door-overview) umożliwiają definiowanie i monitorowanie globalnego routingu ruchu internetowego do aplikacji oraz zarządzanie nim i działa, gdy Blazor Server aplikacje są skonfigurowane do korzystania z długotrwałego sondowania.
  
    > [!NOTE]
    > Chociaż długotrwałe sondowanie jest obsługiwane w przypadku Blazor Server aplikacji, usługa [WebSockets to zalecany protokół transportowy](xref:blazor/host-and-deploy/server#azure-signalr-service). [Drzwi frontonu platformy Azure](/azure/frontdoor/front-door-overview) nie obsługują teraz usługi WebSockets, ale pomoc techniczna dla gniazd WebSockets jest uwzględniana w przyszłych wydaniach.

## <a name="denial-of-service-dos-attacks"></a>Ataki typu "odmowa usługi" (DoS)

Ataki typu "odmowa usługi" (DoS) obejmują klienta, który powoduje, że serwer wyczerpuje jeden lub więcej zasobów, dzięki czemu aplikacja jest niedostępna. Blazor Server Aplikacje obejmują pewne limity domyślne i korzystają z innych ASP.NET Core i SignalR limitów ochrony przed atakami systemu DOS ustawionymi w systemie <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> .

| Blazor Server limit aplikacji | Opis | Domyślne |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | Maksymalna liczba odłączonych obwodów, które dany serwer przechowuje w pamięci w danym momencie. | 100 |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | Maksymalny czas przechowywania połączonego obwodu w pamięci przed jego usunięciem. | 3 minuty |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | Maksymalny czas oczekiwania serwera przed upływem limitu czasu asynchronicznego wywołania funkcji JavaScript. | 1 minuta |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | Maksymalna liczba niepotwierdzonych partii renderowania, które serwer przechowuje w pamięci na obwód w danym momencie do obsługi niezawodnego ponownego łączenia. Po osiągnięciu limitu serwer przestaje tworzyć nowe partie renderowania do momentu potwierdzenia co najmniej jednej partii przez klienta. | 10 |

Ustaw maksymalny rozmiar komunikatu dla pojedynczego przychodzącego komunikatu z centrum <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> .

| SignalR i limit ASP.NET Core | Opis | Domyślne |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> | Rozmiar wiadomości dla pojedynczej wiadomości. | 32 KB |

## <a name="interactions-with-the-browser-client"></a>Interakcje z przeglądarką (klient)

Klient współdziała z serwerem za pomocą wysyłania zdarzeń międzyoperacyjnych w języku JS i uzupełniania. Komunikacja międzyoperacyjna JS między językami JavaScript i .NET jest taka sama:

* Zdarzenia przeglądarki są wysyłane z klienta do serwera w sposób asynchroniczny.
* Serwer odpowiada asynchronicznie na odwzorowanie interfejsu użytkownika w razie potrzeby.

### <a name="javascript-functions-invoked-from-net"></a>Funkcje języka JavaScript wywoływane z platformy .NET

Dla wywołań z metod .NET do języka JavaScript:

* Wszystkie wywołania mają konfigurowalny limit czasu, po którym kończą się niepowodzeniem, zwracając <xref:System.OperationCanceledException> do obiektu wywołującego.
  * Istnieje domyślny limit czasu dla wywołań ( <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType> ) o jednej minucie. Aby skonfigurować ten limit, zobacz <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls> .
  * Można podać token anulowania, aby kontrolować anulowanie dla każdego wywołania. Należy polegać na domyślnym limicie czasu wywołań, gdy jest to możliwe, oraz o każdym wywołaniu klienta w przypadku podanego tokenu anulowania.
* Nie można zaufać wyniku wywołania języka JavaScript. BlazorKlient aplikacji uruchomiony w przeglądarce szuka funkcji języka JavaScript do wywołania. Funkcja jest wywoływana, a wynik lub błąd jest generowany. Złośliwy klient może próbować:
  * Przyczyna problemu w aplikacji przez zwrócenie błędu z funkcji JavaScript.
  * Wywołują niezamierzone zachowanie na serwerze, zwracając nieoczekiwany wynik z funkcji języka JavaScript.

Wykonaj następujące środki ostrożności, aby zabezpieczyć się przed poprzednimi scenariuszami:

* Zawijaj wywołania programu JS Interop w [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcjach, aby uwzględnić błędy, które mogą wystąpić podczas wywołań. Aby uzyskać więcej informacji, zobacz <xref:blazor/fundamentals/handle-errors#javascript-interop>.
* Sprawdź poprawność danych zwróconych przez wywołania międzyoperacyjności JS, w tym komunikaty o błędach, przed podjęciem jakiejkolwiek akcji.

### <a name="net-methods-invoked-from-the-browser"></a>Metody .NET wywoływane z przeglądarki

Nie ufaj wywołań z języka JavaScript do metod .NET. Gdy metoda .NET jest narażona na język JavaScript, należy rozważyć sposób wywołania metody .NET:

* Traktuj każdą metodę .NET uwidocznioną w języku JavaScript tak jak w przypadku publicznego punktu końcowego do aplikacji.
  * Sprawdź poprawność danych wejściowych.
    * Upewnij się, że wartości są w zakresie oczekiwanych zakresów.
    * Upewnij się, że użytkownik ma uprawnienia do wykonywania żądanej akcji.
  * Nie przydzielaj nadmiernej ilości zasobów w ramach wywołania metody .NET. Na przykład należy wykonać sprawdzenia i wprowadzić limity użycia procesora CPU i pamięci.
  * Należy wziąć pod uwagę, że metody static i instance mogą być udostępniane klientom JavaScript. Unikaj udostępniania stanu między sesjami, chyba że projekt nie wywołuje stanu udostępniania z odpowiednimi ograniczeniami.
    * W przypadku metod wystąpienia uwidocznionych za pomocą `DotNetReference` obiektów, które są pierwotnie tworzone za pomocą iniekcji zależności (di), obiekty powinny być zarejestrowane jako obiekty z zakresem. Dotyczy to wszystkich usług DI, które są Blazor Server używane przez aplikację.
    * W przypadku metod statycznych należy unikać ustanawiania stanu, którego nie można ograniczyć do klienta, o ile aplikacja nie jest jawnie udostępniana przez wszystkie użytkowników w wystąpieniu serwera.
  * Należy unikać przekazywania danych dostarczonych przez użytkownika w parametrach do wywołań JavaScript. Jeśli przekazywanie danych w parametrach jest absolutnie wymagane, należy się upewnić, że kod JavaScript obsługuje przekazywanie danych bez wprowadzania luk w zabezpieczeniach [skryptów między lokacjami (XSS)](#cross-site-scripting-xss) . Na przykład nie zapisuj danych dostarczonych przez użytkownika do Document Object Model (DOM) przez ustawienie `innerHTML` właściwości elementu. Rozważ użycie [zasad zabezpieczeń zawartości (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) , aby wyłączyć `eval` i inne niebezpieczne elementy podstawowe języka JavaScript.
* Unikaj implementowania niestandardowego wysyłania wywołań platformy .NET na podstawie implementacji wdrożenia platformy. Udostępnianie metod .NET w przeglądarce jest zaawansowanym scenariuszem, nie jest to zalecane w przypadku ogólnego Blazor opracowywania.

### <a name="events"></a>Zdarzenia

Zdarzenia zapewniają punkt wejścia do Blazor Server aplikacji. Te same reguły zabezpieczania punktów końcowych w aplikacjach sieci Web mają zastosowanie do obsługi zdarzeń w Blazor Server aplikacjach. Złośliwy klient może wysłać dowolne dane, które chcą wysłać jako ładunek dla zdarzenia.

Przykład:

* Zdarzenie zmiany dla elementu `<select>` może wysłać wartość, która nie należy do opcji prezentowanych przez aplikację dla klienta.
* `<input>`Może wysłać dowolne dane tekstowe do serwera, pomijając sprawdzanie poprawności po stronie klienta.

Aplikacja musi sprawdzić poprawność danych dla każdego zdarzenia, które obsługuje aplikacja. Blazor [Składniki formularzy](xref:blazor/forms-validation) struktury wykonują podstawowe walidacje. Jeśli aplikacja używa składników formularzy niestandardowych, kod niestandardowy musi być zapisany, aby sprawdzić poprawność danych zdarzenia.

Blazor Server zdarzenia są asynchroniczne, więc do serwera można wysłać wiele zdarzeń, zanim aplikacja ma czas na reagowanie, generując nowe renderowanie. Ma to pewne konsekwencje dla bezpieczeństwa. Ograniczanie akcji klienta w aplikacji musi być wykonywane wewnątrz obsługi zdarzeń i nie zależy od aktualnie renderowanego stanu widoku.

Rozważmy składnik licznika, który powinien zezwalać użytkownikowi na zwiększenie licznika maksymalnie trzy razy. Przycisk służący do zwiększania licznika jest warunkowo oparty na wartości `count` :

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

Klient może wysłać co najmniej jedno zdarzenie przyrostu, zanim środowisko generuje nowe renderowanie tego składnika. Wynikiem tego jest to, że wartość `count` może być zwiększana przez użytkownika *trzy razy* , ponieważ przycisk nie jest USUWANY przez interfejs użytkownika wystarczająco szybko. Poprawna Metoda osiągnięcia limitu trzech `count` przyrostów jest pokazana w poniższym przykładzie:

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

Po dodaniu `if (count < 3) { ... }` kontroli wewnątrz procedury obsługi decyzja o zwiększeniu zależy od `count` bieżącego stanu aplikacji. Decyzja nie jest oparta na stanie interfejsu użytkownika, ponieważ była w poprzednim przykładzie, co może być czasowo przestarzałe.

### <a name="guard-against-multiple-dispatches"></a>Ochrona przed wieloma operacjami wysyłania

Jeśli wywołanie zwrotne zdarzenia wywołuje długotrwałą operację asynchroniczną, taką jak pobieranie danych z zewnętrznej usługi lub bazy danych, należy rozważyć użycie funkcji Guard. Funkcja Guard może uniemożliwić użytkownikowi kolejkowanie wielu operacji, gdy operacja jest w toku i zawiera wizualne Opinie. Poniższy kod składnika jest ustawiany `isLoading` na `true` podczas `GetForecastAsync` pobierania danych z serwera. Mimo `isLoading` że `true` przycisk jest wyłączony w interfejsie użytkownika:

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

Wzorzec Guard przedstawiony w poprzednim przykładzie działa, jeśli operacja w tle jest wykonywana asynchronicznie ze `async` - `await` wzorcem.

### <a name="cancel-early-and-avoid-use-after-dispose"></a>Anuluj wczesne i Unikaj używania-After-Dispose

Oprócz używania ochrony opisanej w sekcji [Guard dla wielu odniesień](#guard-against-multiple-dispatches) należy rozważyć użycie elementu, <xref:System.Threading.CancellationToken> Aby anulować długotrwałe operacje, gdy składnik zostanie usunięty. Takie podejście ma dodatkową korzyść, unikając *użycia-After-Dispose* w składnikach:

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a>Unikaj zdarzeń, które generują duże ilości danych

Niektóre zdarzenia DOM, takie jak `oninput` lub `onscroll` , mogą generować dużą ilość danych. Unikaj używania tych zdarzeń w Blazor aplikacjach serwerowych.

## <a name="additional-security-guidance"></a>Dodatkowe wskazówki dotyczące zabezpieczeń

Wskazówki dotyczące zabezpieczania aplikacji ASP.NET Core mają zastosowanie do Blazor Server aplikacji i zostały omówione w następujących sekcjach:

* [Rejestrowanie i dane poufne](#logging-and-sensitive-data)
* [Ochrona informacji przesyłanych przy użyciu protokołu HTTPS](#protect-information-in-transit-with-https)
* [Skrypty między lokacjami (XSS)](#cross-site-scripting-xss)
* [Ochrona między źródłami](#cross-origin-protection)
* [Gniazdo kliknięcia](#click-jacking)
* [Otwórz przekierowania](#open-redirects)

### <a name="logging-and-sensitive-data"></a>Rejestrowanie i dane poufne

Interakcje między klientem a serwerem są rejestrowane w dziennikach serwera z <xref:Microsoft.Extensions.Logging.ILogger> wystąpieniami. Blazor pozwala uniknąć rejestrowania poufnych informacji, takich jak zdarzenia rzeczywiste lub dane wejściowe i wyjściowe w programie JS.

Gdy na serwerze wystąpi błąd, struktura powiadamia klienta i rozłączy sesję. Domyślnie klient otrzymuje ogólny komunikat o błędzie, który można zobaczyć w narzędziach deweloperskich w przeglądarce.

Błąd po stronie klienta nie zawiera stosu wywołań i nie zawiera szczegółów dotyczących przyczyny błędu, ale Dzienniki serwera zawierają takie informacje. W celach programistycznych informacje o poufnych informacjach o błędzie można udostępnić klientowi, włączając szczegółowe błędy.

Włącz szczegółowe błędy w języku JavaScript za pomocą:

* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.
* `DetailedErrors`Klucz konfiguracji ustawiony na `true` , który można ustawić w pliku ustawień aplikacji ( `appsettings.json` ). Klucz można również ustawić przy użyciu `ASPNETCORE_DETAILEDERRORS` zmiennej środowiskowej o wartości `true` .

> [!WARNING]
> Ujawnienie informacji o błędach klientom w Internecie stanowi zagrożenie bezpieczeństwa, które należy zawsze uniknąć.

### <a name="protect-information-in-transit-with-https"></a>Ochrona informacji przesyłanych przy użyciu protokołu HTTPS

Blazor Server używa SignalR do komunikacji między klientem a serwerem. Blazor Server zwykle używa transportu, który SignalR negocjuje, który jest zwykle gniazdem.

Blazor Server nie zapewnia integralności i poufności danych przesyłanych między serwerem a klientem. Zawsze używaj protokołu HTTPS.

### <a name="cross-site-scripting-xss"></a>Skrypty między lokacjami (XSS)

Skrypty między lokacjami (XSS) umożliwiają nieautoryzowanym podmiotom wykonywanie arbitralnej logiki w kontekście przeglądarki. Naruszona aplikacja może potencjalnie uruchomić dowolny kod na kliencie. Ta luka w zabezpieczeniach może być używana do potencjalnie wykonywania wielu złośliwych działań na serwerze:

* Wysyłaj fałszywe lub nieprawidłowe zdarzenia do serwera.
* Niepowodzenie wysyłania/nieprawidłowe uzupełnianie renderowania.
* Unikaj wysyłania uzupełniania renderowania.
* Wysyłaj wywołania międzyoperacyjne z JavaScript do platformy .NET.
* Modyfikowanie odpowiedzi wywołań międzyoperacyjnych z platformy .NET do języka JavaScript.
* Unikaj wysyłania do wyników międzyoperacyjnych platformy .NET i JS.

Blazor ServerStruktura wykonuje kroki w celu ochrony przed niektórymi z poprzednich zagrożeń:

* Powoduje zatrzymanie tworzenia nowych aktualizacji interfejsu użytkownika, jeśli klient nie potwierdza partii renderowania. Skonfigurowane przy użyciu <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType> .
* Przeprowadzenie dowolnego wywołania platformy .NET do języka JavaScript po jednej minucie bez otrzymania odpowiedzi od klienta. Skonfigurowane przy użyciu <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType> .
* Wykonuje podstawowe sprawdzanie poprawności wszystkich danych wejściowych pochodzących z przeglądarki podczas współdziałania JS:
  * Odwołania platformy .NET są prawidłowe i typu oczekiwanego przez metodę .NET.
  * Dane nie są źle sformułowane.
  * Poprawna liczba argumentów dla metody znajduje się w ładunku.
  * Argumenty lub wynik można przeprowadzić w deserializacji prawidłowo przed wywołaniem metody.
* Wykonuje podstawowe walidacje we wszystkich danych wejściowych pochodzących z przeglądarki z wysłanych zdarzeń:
  * Zdarzenie ma prawidłowy typ.
  * Dane dla zdarzenia można deserializować.
  * Istnieje procedura obsługi zdarzeń skojarzona ze zdarzeniem.

Oprócz zabezpieczeń wdrożonych przez platformę, aplikacja musi być kodowana przez dewelopera, aby chronić przed zagrożeniami i podejmować odpowiednie działania:

* Zawsze sprawdzaj poprawność danych podczas obsługi zdarzeń.
* Podejmij odpowiednią akcję po odebraniu nieprawidłowych danych:
  * Ignoruj dane i zwracają. Dzięki temu aplikacja może kontynuować przetwarzanie żądań.
  * Jeśli aplikacja ustali, że dane wejściowe są illegitimate i nie mogły zostać utworzone przez legalnego klienta, Zgłoś wyjątek. Zgłaszanie wyjątku spowoduje rozbicie obwodu i zakończenie sesji.
* Nie ufaj komunikatowi o błędzie dostarczonym przez uzupełnianie wsadowe renderowania zawarte w dziennikach. Ten błąd jest dostarczany przez klienta i nie może być zazwyczaj zaufany, ponieważ klient może zostać naruszony.
* Nie ufaj danych wejściowych w wywołaniach międzyoperacyjnych JS w obu kierunkach między językami JavaScript i .NET.
* Aplikacja jest odpowiedzialna za sprawdzenie, czy zawartość argumentów i wyników są prawidłowe, nawet jeśli argumenty lub wyniki są prawidłowo deserializowane.

W przypadku luki w zabezpieczeniach XSS aplikacja musi zawierać dane wejściowe użytkownika na renderowanej stronie. Blazor Server składniki wykonują krok czasu kompilacji, w którym Adiustacja w `.razor` pliku są przekształcane w procedurę logiki C#. W czasie wykonywania logika języka C# kompiluje *drzewo renderowania* opisujące elementy, tekst i składniki podrzędne. Jest on stosowany do modelu DOM przeglądarki za pośrednictwem sekwencji instrukcji języka JavaScript (lub jest serializowany do HTML w przypadku prerenderowania):

* Dane wejściowe użytkownika renderowane za pośrednictwem normalnej Razor składni (na przykład `@someStringValue` ) nie ujawniają luki w zabezpieczeniach programu XSS, ponieważ Razor składnia jest DODAWANA do modelu Dom za pośrednictwem poleceń, które mogą zapisywać tekst. Nawet jeśli wartość zawiera znacznik HTML, wartość jest wyświetlana jako tekst statyczny. Podczas renderowania wstępnego dane wyjściowe są kodowane w formacie HTML, co spowoduje również wyświetlenie zawartości jako tekst statyczny.
* Tagi skryptu nie są dozwolone i nie powinny być uwzględnione w drzewie renderowania składnika aplikacji. Jeśli tag skryptu jest zawarty w znaczniku składnika, generowany jest błąd czasu kompilacji.
* Autorzy składników mogą tworzyć składniki w języku C# bez użycia programu Razor . Autor składnika jest odpowiedzialny za korzystanie z odpowiednich interfejsów API podczas emitowania danych wyjściowych. Na przykład użyj, `builder.AddContent(0, someUserSuppliedString)` a *nie* `builder.AddMarkupContent(0, someUserSuppliedString)` , ponieważ drugie może utworzyć lukę w zabezpieczeniach.

W ramach ochrony przed atakami typu XSS należy rozważyć zaimplementowanie rozwiązań XSS, takich jak [zasady zabezpieczeń zawartości (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).

Aby uzyskać więcej informacji, zobacz <xref:security/cross-site-scripting>.

### <a name="cross-origin-protection"></a>Ochrona między źródłami

Ataki między źródłami obejmują klienta z innego źródła, wykonującego akcję względem serwera. Złośliwa akcja to zwykle żądanie GET lub formularz POST (CSRF żądania między lokacjami), ale możliwe jest również otwarcie złośliwego protokołu WebSocket. Blazor Server aplikacje oferują te [same gwarancje, że każda inna SignalR Aplikacja korzystająca z usługi Hub jest oferowana](xref:signalr/security):

* Blazor Server dostęp do aplikacji można uzyskać, chyba że zostaną podjęte dodatkowe środki w celu ich uniemożliwienia. Aby wyłączyć dostęp między źródłami, wyłącz funkcję CORS w punkcie końcowym przez dodanie oprogramowania pośredniczącego CORS do potoku i dodanie <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> do Blazor metadanych punktu końcowego lub ograniczenie zestawu dozwolonych źródeł przez [skonfigurowanie SignalR dla udostępniania zasobów między źródłami](xref:signalr/security#cross-origin-resource-sharing).
* Jeśli jest włączona funkcja CORS, w zależności od konfiguracji specyfikacji CORS może być wymagane wykonanie dodatkowych czynności w celu ochrony aplikacji. Jeśli funkcja CORS jest włączona globalnie, funkcję CORS można wyłączyć dla Blazor Server centrum, dodając <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> metadane do metadanych punktu końcowego po wywołaniu <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> konstruktora trasy punktu końcowego.

Aby uzyskać więcej informacji, zobacz <xref:security/anti-request-forgery>.

### <a name="click-jacking"></a>Gniazdo kliknięcia

Gniazda kliknięcia obejmują renderowanie lokacji jako `<iframe>` wewnątrz lokacji z innego źródła w celu nakłonienia użytkownika do wykonywania działań w lokacji w ramach ataku.

Aby chronić aplikację przed renderowaniem w ramach programu `<iframe>` , należy użyć [zasad zabezpieczeń zawartości (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) i `X-Frame-Options` nagłówka. Aby uzyskać więcej informacji, zobacz [powiadomienia MDN Web docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).

### <a name="open-redirects"></a>Otwórz przekierowania

Po Blazor Server rozpoczęciu sesji aplikacji serwer przeprowadza podstawowe sprawdzanie poprawności adresów URL wysyłanych w ramach uruchamiania sesji. Struktura sprawdza, czy podstawowy adres URL jest nadrzędnym bieżącym adresem URL przed ustanowieniem obwodu. Struktura nie wykonuje żadnych dodatkowych testów.

Gdy użytkownik wybierze link na kliencie, adres URL łącza jest wysyłany do serwera, który określa akcję do wykonania. Na przykład aplikacja może wykonać nawigację po stronie klienta lub wskazać przeglądarkę, aby przejść do nowej lokalizacji.

Składniki mogą również wyzwalać żądania nawigacji programowo za pomocą programu <xref:Microsoft.AspNetCore.Components.NavigationManager> . W takich scenariuszach aplikacja może wykonać nawigację po stronie klienta lub wskazać przeglądarkę, aby przejść do nowej lokalizacji.

Składniki muszą:

* Unikaj używania danych wejściowych użytkownika jako części argumentów wywołania nawigacji.
* Sprawdź poprawność argumentów, aby upewnić się, że element docelowy jest dozwolony przez aplikację.

W przeciwnym razie złośliwy użytkownik może wymusić przejście przeglądarki do witryny kontrolowanej przez osobę atakującą. W tym scenariuszu osoba atakująca dodaliśmy aplikację do korzystania z niektórych danych wejściowych użytkownika w ramach wywołania <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> metody.

To zalecenie ma zastosowanie również w przypadku renderowania linków w ramach aplikacji:

* Jeśli to możliwe, użyj linków względnych.
* Sprawdź, czy bezwzględne miejsca docelowe linków są prawidłowe przed dołączeniem ich do strony.

Aby uzyskać więcej informacji, zobacz <xref:security/preventing-open-redirects>.

## <a name="security-checklist"></a>Lista kontrolna zabezpieczeń

Poniżej wymieniono zagadnienia dotyczące zabezpieczeń, które nie są wyczerpujące:

* Sprawdź poprawność argumentów z zdarzeń.
* Sprawdź poprawność danych wejściowych i wyników z wywołań międzyoperacyjnych JS.
* Należy unikać używania (lub weryfikowania wcześniej) danych wejściowych użytkownika dla wywołań międzyoperacyjnych platformy .NET i JS.
* Uniemożliwiaj klientowi alokowanie nieograniczonej ilości pamięci.
  * Dane w składniku.
  * `DotNetObject` odwołania zwracane do klienta.
* Ochrona przed wieloma operacjami wysyłania.
* Anuluj długotrwałe operacje, gdy składnik zostanie usunięty.
* Unikaj zdarzeń, które generują duże ilości danych.
* Unikaj korzystania z danych wejściowych użytkownika jako części wywołań <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> i weryfikowania danych wejściowych użytkownika dla adresów URL w odniesieniu do zestawu dozwolonych źródeł najpierw w przypadku, gdy jest to nieuniknione.
* Nie należy podejmować decyzji dotyczących autoryzacji na podstawie stanu interfejsu użytkownika, ale tylko ze stanu składnika.
* Rozważ użycie [zasad zabezpieczeń zawartości (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) do ochrony przed atakami XSS.
* Należy rozważyć użycie usług CSP i [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) do ochrony przed kliknięciami.
* Upewnij się, że ustawienia CORS są odpowiednie podczas włączania funkcji CORS lub jawnie wyłącz funkcję CORS dla Blazor aplikacji.
* Przetestuj, aby upewnić się, że limity po stronie serwera dla Blazor aplikacji zapewniają akceptowalne środowisko użytkownika bez nieakceptowalnego poziomu ryzyka.
