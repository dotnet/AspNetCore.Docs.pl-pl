---
title: Bezpieczne aplikacje ASP.NET Blazor Core Server
author: guardrex
description: Dowiedz się, jak ograniczyć Blazor zagrożenia bezpieczeństwa dla aplikacji serwera.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server
ms.openlocfilehash: bd03f811d0425fdfdb7bbbc24fea5481b49b8ed3
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80626021"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Bezpieczne ASP.NET aplikacje Core Blazor Server

Przez [Javier Calvarro Nelson](https://github.com/javiercn)

Aplikacje Blazor Server przyjmują *stanowy* model przetwarzania danych, w którym serwer i klient utrzymują długotrwałą relację. Stan trwały jest utrzymywany przez [obwód,](xref:blazor/state-management)który może obejmować połączenia, które są również potencjalnie długotrwałe.

Gdy użytkownik odwiedza witrynę serwera Blazor, serwer tworzy obwód w pamięci serwera. Obwód wskazuje przeglądarce, jaką zawartość ma być renderowana i reaguje na zdarzenia, na przykład gdy użytkownik wybierze przycisk w interfejsie użytkownika. Aby wykonać te akcje, obwód wywołuje funkcje JavaScript w przeglądarce użytkownika i metody .NET na serwerze. Ta dwukierunkowa interakcja oparta na JavaScript jest określana jako [javascript interop (JS interop)](xref:blazor/call-javascript-from-dotnet).

Ponieważ JS interop występuje przez Internet, a klient używa przeglądarki zdalnej, aplikacje Blazor Server udostępniają większość problemów związanych z bezpieczeństwem aplikacji internetowych. W tym temacie opisano typowe zagrożenia dla aplikacji Blazor Server i zawiera wskazówki dotyczące ograniczania zagrożeń koncentruje się na aplikacjach internetowych.

W ograniczonych środowiskach, takich jak wewnątrz sieci firmowych lub intranetów, niektóre wskazówki dotyczące łagodzenia albo:

* Nie ma zastosowania w środowisku ograniczonym.
* Nie jest wart kosztów do zaimplementowania, ponieważ ryzyko zabezpieczeń jest niska w środowisku ograniczonym.

## <a name="blazor-server-project-template"></a>Szablon projektu serwera Blazor

Szablon projektu Blazor Server można skonfigurować do uwierzytelniania podczas tworzenia projektu.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Postępuj zgodnie ze <xref:blazor/get-started> wskazówkami programu Visual Studio w tym artykule, aby utworzyć nowy projekt blazor server z mechanizmem uwierzytelniania.

Po wybraniu szablonu **aplikacji Blazor Server** w oknie **dialogowym Tworzenie nowej ASP.NET Core Web Application** wybierz pozycję **Zmień** w obszarze **Uwierzytelnianie**.

Zostanie otwarte okno dialogowe oferujące ten sam zestaw mechanizmów uwierzytelniania dostępnych dla innych projektów ASP.NET Core:

* **Bez uwierzytelniania**
* **Konta użytkowników indywidualnych** &ndash; mogą być przechowywane:
  * W aplikacji przy użyciu systemu [tożsamości](xref:security/authentication/identity) ASP.NET Core.
  * Z [usługą Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Konta służbowe lub szkolne**
* **Uwierzytelnianie systemu Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Postępuj zgodnie ze wskazówkami dotyczącymi kodu programu Visual Studio w tym artykule, <xref:blazor/get-started> aby utworzyć nowy projekt serwera Blazor z mechanizmem uwierzytelniania:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Dopuszczalne wartości uwierzytelniania`{AUTHENTICATION}`( ) są pokazane w poniższej tabeli.

| Mechanizm uwierzytelniania                                                                 | `{AUTHENTICATION}`Wartość |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| Bez uwierzytelniania                                                                        | `None`                   |
| Indywidualne<br>Użytkownicy zapisani w aplikacji z ASP.NET podstawową tożsamością.                        | `Individual`             |
| Indywidualne<br>Użytkownicy zapisani w [usłudze Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Konta służbowe lub szkolne<br>Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy.            | `SingleOrg`              |
| Konta służbowe lub szkolne<br>Uwierzytelnianie organizacyjne dla wielu dzierżawców.           | `MultiOrg`               |
| Uwierzytelnianie systemu Windows                                                                   | `Windows`                |

Polecenie tworzy folder o nazwie o `{APP NAME}` wartości podanej dla symbolu zastępczego i używa nazwy folderu jako nazwy aplikacji. Aby uzyskać więcej informacji, zobacz nowe polecenie [dotnet](/dotnet/core/tools/dotnet-new) w przewodniku .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Postępuj zgodnie ze wskazówkami <xref:blazor/get-started> dotyczącymi programu Visual Studio dla komputerów Mac w tym artykule.

1. W kroku **Konfigurowanie nowej aplikacji Serwera Blazor** wybierz pozycję **Uwierzytelnianie indywidualne (w aplikacji)** z listy rozwijanej **Uwierzytelnianie.**

1. Aplikacja jest tworzona dla poszczególnych użytkowników przechowywanych w aplikacji z ASP.NET Core Identity.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli/)

Postępuj zgodnie ze wskazówkami <xref:blazor/get-started> .NET Core CLI w artykule, aby utworzyć nowy projekt serwera Blazor z mechanizmem uwierzytelniania:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Dopuszczalne wartości uwierzytelniania`{AUTHENTICATION}`( ) są pokazane w poniższej tabeli.

| Mechanizm uwierzytelniania                                                                 | `{AUTHENTICATION}`Wartość |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| Bez uwierzytelniania                                                                        | `None`                   |
| Indywidualne<br>Użytkownicy zapisani w aplikacji z ASP.NET podstawową tożsamością.                        | `Individual`             |
| Indywidualne<br>Użytkownicy zapisani w [usłudze Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Konta służbowe lub szkolne<br>Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy.            | `SingleOrg`              |
| Konta służbowe lub szkolne<br>Uwierzytelnianie organizacyjne dla wielu dzierżawców.           | `MultiOrg`               |
| Uwierzytelnianie systemu Windows                                                                   | `Windows`                |

Polecenie tworzy folder o nazwie o `{APP NAME}` wartości podanej dla symbolu zastępczego i używa nazwy folderu jako nazwy aplikacji. Aby uzyskać więcej informacji, zobacz nowe polecenie [dotnet](/dotnet/core/tools/dotnet-new) w przewodniku .NET Core.

---

## <a name="pass-tokens-to-a-blazor-server-app"></a>Przekazywanie tokenów do aplikacji Blazor Server

Uwierzytelnij aplikację Blazor Server tak, jak w przypadku zwykłych stron Razor lub aplikacji MVC. Aprowizuj i zapisz tokeny w pliku cookie uwierzytelniania. Przykład:

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

Przykładowy kod, w `Startup.ConfigureServices` tym pełny przykład, zobacz [Przekazywanie tokenów do aplikacji Blazor po stronie serwera](https://github.com/javiercn/blazor-server-aad-sample).

Zdefiniuj klasę do przekazania w początkowym stanie aplikacji za pomocą tokenów dostępu i odświeżania:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Zdefiniuj usługę dostawcy tokenu **o określonym zakresie,** która może być używana w aplikacji Blazor do rozpoznawania tokenów z DI:

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;

public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

W `Startup.ConfigureServices`, dodaj usługi dla:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

W pliku *_Host.cshtml* utwórz i wystąpienie `InitialApplicationState` i przekaż go jako parametr do aplikacji:

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

W `App` składniku (*App.brzytwa*), rozwiąż usługę i zainiólj ją danymi z parametru:

```razor
@inject TokenProvider TokensProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokensProvider.AccessToken = InitialState.AccessToken;
        TokensProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

W usłudze, która sprawia, że żądanie bezpiecznego interfejsu API, wstrzyknąć dostawcę tokenu i pobrać token do wywołania interfejsu API:

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="resource-exhaustion"></a>Wyczerpanie zasobów

Wyczerpanie zasobów może wystąpić, gdy klient wchodzi w interakcję z serwerem i powoduje, że serwer zużywa nadmierne zasoby. Nadmierne zużycie zasobów ma przede wszystkim wpływ:

* [Procesora](#cpu)
* [Pamięci](#memory)
* [Połączenia klienta](#client-connections)

Ataki typu "odmowa usługi" (DoS) zwykle mają na celu wyczerpanie zasobów aplikacji lub serwera. Jednak wyczerpanie zasobów nie musi być wynikiem ataku na system. Na przykład ograniczone zasoby mogą zostać wyczerpane ze względu na duże zapotrzebowanie użytkowników. DoS jest dalej objęty w sekcji [Ataki typu "odmowa usługi" (DoS).](#denial-of-service-dos-attacks)

Zasoby zewnętrzne do platformy Blazor, takie jak bazy danych i dojścia plików (używane do odczytu i zapisu plików), mogą również wystąpić wyczerpanie zasobów. Aby uzyskać więcej informacji, zobacz <xref:performance/performance-best-practices>.

### <a name="cpu"></a>Procesor CPU

Wyczerpanie procesora CPU może wystąpić, gdy jeden lub więcej klientów wymusić serwer do wykonywania intensywnej pracy procesora CPU.

Rozważmy na przykład aplikację Blazor Server, która oblicza *liczbę Fibonnacci*. Numer Fibonnacci jest produkowany z sekwencji Fibonnacci, gdzie każda liczba w sekwencji jest sumą dwóch poprzednich liczb. Ilość pracy wymaganej do uzyskania odpowiedzi zależy od długości sekwencji i rozmiaru wartości początkowej. Jeśli aplikacja nie nakłada limitów na żądanie klienta, obliczenia intensywnie korzystające z procesora CPU mogą zdominować czas procesora CPU i zmniejszyć wydajność innych zadań. Nadmierne zużycie zasobów jest problemem bezpieczeństwa wpływającym na dostępność.

Wyczerpanie procesora jest problemem dla wszystkich aplikacji publicznych. W zwykłych aplikacjach internetowych, żądania i połączenia limit czasu jako zabezpieczenie, ale aplikacje Blazor Server nie zapewniają tych samych zabezpieczeń. Aplikacje Blazor Server muszą zawierać odpowiednie kontrole i limity przed wykonaniem potencjalnie intensywnej pracy procesora.

### <a name="memory"></a>Memory (Pamięć)

Wyczerpanie pamięci może wystąpić, gdy jeden lub więcej klientów wymusić serwer do korzystania z dużej ilości pamięci.

Rozważmy na przykład aplikację po stronie serwera Blazor ze składnikiem, który akceptuje i wyświetla listę elementów. Jeśli aplikacja Blazor nie nakłada limitów na liczbę dozwolonych elementów lub liczbę elementów renderowanych z powrotem do klienta, przetwarzanie i renderowanie intensywnie korzystające z pamięci może zdominować pamięć serwera do tego stopnia, że wydajność serwera cierpi. Serwer może ulec awarii lub spowolnić do tego stopnia, że wydaje się, że uległ awarii.

Należy wziąć pod uwagę następujący scenariusz do utrzymania i wyświetlania listy elementów, które odnoszą się do potencjalnego scenariusza wyczerpania pamięci na serwerze:

* Elementy we `List<MyItem>` właściwości lub polu używają pamięci serwera. Jeśli aplikacja zezwala na listę elementów, aby rozwijać bez ograniczeń, istnieje ryzyko serwera wyczerpania pamięci. Wyczerpanie pamięci powoduje, że bieżąca sesja kończy się (awaria), a wszystkie równoczesne sesje w tym wystąpieniu serwera otrzymują wyjątek braku pamięci. Aby zapobiec występowaniu tego scenariusza, aplikacja musi używać struktury danych, która nakłada limit elementów na równoczesnych użytkowników.
* Jeśli schemat stronicowania nie jest używany do renderowania, serwer używa dodatkowej pamięci dla obiektów, które nie są widoczne w interfejsie użytkownika. Bez ograniczenia liczby elementów zapotrzebowanie na pamięć może wyczerpać dostępną pamięć serwera. Aby zapobiec temu scenariuszowi, należy użyć jednego z następujących podejść:
  * Podczas renderowania należy używać list na strony.
  * Wyświetlaj tylko pierwsze od 100 do 1000 elementów i wymagaj od użytkownika wprowadzania kryteriów wyszukiwania w celu znalezienia elementów poza wyświetlanymi elementami.
  * W przypadku bardziej zaawansowanego scenariusza renderowania zaimplementuj listy lub siatki obsługujące *wirtualizację*. Za pomocą wirtualizacji listy renderuje tylko podzbiór elementów aktualnie widocznych dla użytkownika. Gdy użytkownik wchodzi w interakcję z paskiem przewijania w interfejsie użytkownika, składnik renderuje tylko te elementy wymagane do wyświetlenia. Elementy, które nie są obecnie wymagane do wyświetlania mogą być przechowywane w magazynie pomocniczym, co jest idealnym rozwiązaniem. Nieuwydajone przedmioty mogą być również przechowywane w pamięci, co jest mniej idealne.

Aplikacje Blazor Server oferują podobny model programowania do innych struktur interfejsu użytkownika dla aplikacji stanowych, takich jak WPF, Windows Forms lub Blazor WebAssembly. Główną różnicą jest to, że w kilku ramach interfejsu użytkownika pamięci używanej przez aplikację należy do klienta i dotyczy tylko tego klienta. Na przykład aplikacja Blazor WebAssembly działa całkowicie na kliencie i używa tylko zasobów pamięci klienta. W scenariuszu serwera Blazor pamięć zużywana przez aplikację należy do serwera i jest współużytkowana przez klientów w wystąpieniu serwera.

Wymagania dotyczące pamięci po stronie serwera są brane pod uwagę dla wszystkich aplikacji Blazor Server. Jednak większość aplikacji sieci web są bezstanowe, a pamięć używana podczas przetwarzania żądania jest zwalniana po zwróceniu odpowiedzi. Zgodnie z ogólnym zaleceniem nie zezwalaj klientom na przydzielanie niezwiązanej ilości pamięci, tak jak w każdej innej aplikacji po stronie serwera, która utrzymuje połączenia klienta. Pamięć zużywana przez aplikację Blazor Server jest utrzymywana przez dłuższy czas niż pojedyncze żądanie.

> [!NOTE]
> Podczas tworzenia profiler może służyć lub śledzenia przechwycone do oceny zapotrzebowania na pamięć klientów. Profiler lub śledzenia nie przechwytuje pamięci przydzielonej do określonego klienta. Aby przechwycić użycie pamięci określonego klienta podczas tworzenia, przechwyć zrzut i sprawdź zapotrzebowanie na pamięć wszystkich obiektów zakorzenionych w obwodzie użytkownika.

### <a name="client-connections"></a>Połączenia klienta

Wyczerpanie połączenia może wystąpić, gdy jeden lub więcej klientów otworzy zbyt wiele równoczesnych połączeń z serwerem, uniemożliwiając innym klientom ustanawianie nowych połączeń.

Klienci Blazor nawiązają jedno połączenie na sesję i utrzymują połączenie otwarte tak długo, jak długo jest otwarte okno przeglądarki. Wymagania dotyczące utrzymywania wszystkich połączeń przez serwer nie są specyficzne dla aplikacji Blazor. Biorąc pod uwagę trwały charakter połączeń i stanowy charakter aplikacji Blazor Server, wyczerpanie połączeń jest większym ryzykiem dla dostępności aplikacji.

Domyślnie nie ma limitu liczby połączeń na użytkownika dla aplikacji Blazor Server. Jeśli aplikacja wymaga limitu połączenia, należy podjąć co najmniej jedną z następujących metod:

* Wymagaj uwierzytelniania, co oczywiście ogranicza możliwość łączenia się z aplikacją przez nieautoryzowanych użytkowników. Aby ten scenariusz był skuteczny, użytkownicy muszą być uniemożliwione inicjowania obsługi administracyjnej nowych użytkowników do woli.
* Ogranicz liczbę połączeń na użytkownika. Ograniczenie połączeń można wykonać za pomocą następujących metod. Należy zachować ostrożność, aby umożliwić legalnym użytkownikom dostęp do aplikacji (na przykład, gdy limit połączenia jest ustanawiany na podstawie adresu IP klienta).
  * Na poziomie aplikacji:
    * Rozszerzalność routingu punktu końcowego.
    * Wymagaj uwierzytelniania, aby połączyć się z aplikacją i śledzić aktywne sesje na użytkownika.
    * Odrzuć nowe sesje po osiągnięciu limitu.
    * Proxy WebSocket połączenia z aplikacją za pomocą serwera proxy, takich jak [usługa Azure SignalR,](/azure/azure-signalr/signalr-overview) która multipleksuje połączenia z klientów do aplikacji. Zapewnia to aplikacji o większej pojemności połączenia niż jeden klient może ustanowić, uniemożliwiając klientowi wyczerpanie połączeń z serwerem.
  * Na poziomie serwera: użyj serwera proxy/bramy przed aplikacją. Na przykład [usługi Azure Front Door](/azure/frontdoor/front-door-overview) umożliwia definiowanie, zarządzanie i monitorowanie globalnego routingu ruchu internetowego do aplikacji.

## <a name="denial-of-service-dos-attacks"></a>Ataki typu "odmowa usługi" (DoS)

Ataki typu "odmowa usługi" (DoS) obejmują klienta, co powoduje, że serwer wyczerpuje jeden lub więcej zasobów, dzięki czemu aplikacja jest niedostępna. Aplikacje Blazor Server zawierają pewne domyślne limity i opierają się na innych limitach ASP.NET Core i SignalR, aby chronić przed atakami DoS:

| Limit aplikacji Blazor Server                            | Opis | Domyślne |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | Maksymalna liczba rozłączonych obwodów, które dany serwer przechowuje w pamięci naraz. | 100 |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | Maksymalny czas, przez który odłączony obwód jest przechowywany w pamięci przed rozerwaniem. | 3 minuty |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | Maksymalny czas oczekiwania serwera przed wytyczeniem czasu wywołania funkcji asynchronicznego języka JavaScript. | 1 min |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | Maksymalna liczba nieuznanych partii renderowania, które serwer przechowuje w pamięci na obwód w danym czasie, aby obsługiwać niezawodne ponowne połączenie. Po osiągnięciu limitu serwer przestaje produkować nowe partie renderowania, dopóki klient nie potwierdzi co najmniej jednej partii. | 10 |


| Limit signalr i ASP.NET Core             | Opis | Domyślne |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | Rozmiar wiadomości dla pojedynczej wiadomości. | 32 KB |

## <a name="interactions-with-the-browser-client"></a>Interakcje z przeglądarką (klientem)

Klient wchodzi w interakcję z serwerem za pośrednictwem wywoływania zdarzeń interop JS i ukończenia renderowania. Komunikacja interop JS idzie w obie strony między JavaScript i .NET:

* Zdarzenia przeglądarki są wywoływane z klienta do serwera w sposób asynchroniczne.
* Serwer odpowiada asynchronicznie rerendering interfejsu użytkownika w razie potrzeby.

### <a name="javascript-functions-invoked-from-net"></a>Funkcje JavaScript wywoływane z platformy .NET

W przypadku połączeń z metod platformy .NET do języka JavaScript:

* Wszystkie wywołania mają konfigurowalny limit czasu, po którym <xref:System.OperationCanceledException> nie powiedzie się, zwracając do wywołującego.
  * Istnieje domyślny limit czasu dla`CircuitOptions.JSInteropDefaultCallTimeout`połączeń ( ) jednej minuty. Aby skonfigurować ten <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>limit, zobacz .
  * Token anulowania można podać, aby kontrolować anulowanie na podstawie połączenia. Polegaj na domyślnym limitie czasu wywołania, jeśli jest to możliwe, i czasochłomnie dowolne wywołanie klienta, jeśli zostanie podany token anulowania.
* Nie można ufać wynikowi połączenia JavaScript. Klient Blazor aplikacji uruchomiony w przeglądarce wyszukuje funkcję JavaScript do wywołania. Funkcja jest wywoływana i wynik lub błąd jest produkowany. Złośliwy klient może próbować:
  * Przyczyną problemu w aplikacji jest zwracanie błędu z funkcji JavaScript.
  * Wywołaj niezamierzone zachowanie na serwerze, zwracając nieoczekiwany wynik z funkcji JavaScript.

Podjąć następujące środki ostrożności, aby chronić się przed poprzednimi scenariuszami:

* Zawijanie wywołań interop JS w instrukcjach [try-catch,](/dotnet/csharp/language-reference/keywords/try-catch) aby uwzględnić błędy, które mogą wystąpić podczas wywołań. Aby uzyskać więcej informacji, zobacz <xref:blazor/handle-errors#javascript-interop>.
* Sprawdź poprawność danych zwróconych z wywołań interop JS, w tym komunikatów o błędach, przed podjęciem jakiejkolwiek akcji.

### <a name="net-methods-invoked-from-the-browser"></a>Metody .NET wywoływane z przeglądarki

Nie ufaj wywołaniom z języka JavaScript do metod .NET. Gdy metoda .NET jest narażona na działanie języka JavaScript, należy wziąć pod uwagę sposób wywoływania metody .NET:

* Traktuj dowolną metodę platformy .NET narażoną na działanie języka JavaScript, tak jak publiczny punkt końcowy aplikacji.
  * Sprawdź poprawność danych wejściowych.
    * Upewnij się, że wartości znajdują się w oczekiwanych zakresach.
    * Upewnij się, że użytkownik ma uprawnienia do wykonywania żądanej akcji.
  * Nie przydzielaj nadmiernej ilości zasobów w ramach wywołania metody .NET. Na przykład należy przeprowadzić kontrole i umieścić limity użycia procesora CPU i pamięci.
  * Należy wziąć pod uwagę, że metody statyczne i wystąpienia mogą być udostępniane klientom JavaScript. Należy unikać udostępniania stanu między sesjami, chyba że projekt wymaga udostępniania stanu z odpowiednimi ograniczeniami.
    * Na przykład metody `DotNetReference` udostępniane za pośrednictwem obiektów, które są pierwotnie tworzone za pomocą iniekcji zależności (DI), obiekty powinny być rejestrowane jako obiekty o określonym zakresie. Dotyczy to dowolnej usługi DI Blazor używanej przez aplikację Serwer.
    * W przypadku metod statycznych należy unikać ustanawiania stanu, który nie może być ograniczony do klienta, chyba że aplikacja jawnie udostępnia stan według projektu wszystkich użytkowników w wystąpieniu serwera.
  * Unikaj przekazywania danych dostarczonych przez użytkownika w parametrach do wywołań JavaScript. Jeśli przekazywanie danych w parametrach jest absolutnie wymagane, upewnij się, że kod JavaScript obsługuje przekazywanie danych bez wprowadzania luk w zabezpieczeniach [skryptów krzyżowych (XSS).](#cross-site-scripting-xss) Na przykład nie zapisuj danych dostarczonych przez użytkownika do modelu `innerHTML` obiektu dokumentu (DOM), ustawiając właściwość elementu. Należy rozważyć użycie zasad zabezpieczeń zawartości `eval` [(CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) do wyłączenia i innych niebezpiecznych egoików JavaScript.
* Należy unikać implementacji niestandardowego wysyłania wywołań platformy .NET na podstawie implementacji wysyłki struktury. Uwidacznianie metod platformy .NET w przeglądarce jest Blazor scenariuszem zaawansowanym, który nie jest zalecany do ogólnego rozwoju.

### <a name="events"></a>Zdarzenia

Zdarzenia zapewniają punkt wejścia Blazor do aplikacji serwera. Te same reguły dotyczące ochrony punktów końcowych w Blazor aplikacjach sieci web mają zastosowanie do obsługi zdarzeń w aplikacjach serwera. Złośliwy klient może wysłać dowolne dane, które chce wysłać jako ładunek zdarzenia.

Przykład:

* Zdarzenie zmiany dla `<select>` może wysłać wartość, która nie znajduje się w ramach opcji, które aplikacja przedstawiona klientowi.
* Może `<input>` wysłać dowolne dane tekstowe do serwera, z pominięciem sprawdzania poprawności po stronie klienta.

Aplikacja musi sprawdzić poprawność danych dla każdego zdarzenia, które obsługuje aplikacja. Składniki Blazor [formularzy](xref:blazor/forms-validation) struktury wykonać podstawowe weryfikacje. Jeśli aplikacja używa składników formularzy niestandardowych, kod niestandardowy musi być zapisany w celu sprawdzenia poprawności danych zdarzeń, stosownie do potrzeb.

BlazorZdarzenia serwera są asynchroniczne, więc wiele zdarzeń można wysłać do serwera, zanim aplikacja ma czas na reakcję, tworząc nowy render. Ma to pewne implikacje dla bezpieczeństwa do rozważenia. Ograniczanie akcji klienta w aplikacji musi być wykonywane wewnątrz obsługi zdarzeń i nie zależy od bieżącego stanu renderowanego widoku.

Należy wziąć pod uwagę składnik licznika, który powinien umożliwić użytkownikowi przyrost licznika maksymalnie trzy razy. Przycisk, aby przyrost licznika jest warunkowo oparty `count`na wartości :

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

Klient może wysyłć co najmniej jedno zdarzenia przyrostowe, zanim struktura tworzy nowy render tego składnika. Wynik jest, `count` że może być zwiększane *przez trzy razy* przez użytkownika, ponieważ przycisk nie jest usuwany przez interfejs użytkownika wystarczająco szybko. Prawidłowy sposób osiągnięcia limitu `count` trzech przyrostów jest pokazany w poniższym przykładzie:

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

Dodając `if (count < 3) { ... }` check wewnątrz programu obsługi, decyzja o `count` przyrost jest oparta na bieżącym stanie aplikacji. Decyzja nie jest oparta na stanie interfejsu użytkownika, jak to było w poprzednim przykładzie, które mogą być tymczasowo przestarzałe.

### <a name="guard-against-multiple-dispatches"></a>Ochrona przed wieloma wysyłkami

Jeśli wywołanie zwrotne zdarzenia wywołuje długotrwałą operację asynchronicznie, taką jak pobieranie danych z zewnętrznej usługi lub bazy danych, należy rozważyć użycie warty. Osłona może uniemożliwić użytkownikowi kolejkowanie wielu operacji, gdy operacja jest w toku z wizualną informacją zwrotną. Poniższy kod `isLoading` składnika ustawia się `true` podczas `GetForecastAsync` uzyskiwania danych z serwera. While `isLoading` `true`is , przycisk jest wyłączony w interfejsie użytkownika:

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

Wzorzec osłony zademonstrowany w poprzednim przykładzie działa, jeśli operacja w `async` - `await` tle jest wykonywana asynchronicznie ze wzorcem.

### <a name="cancel-early-and-avoid-use-after-dispose"></a>Anuluj wcześniej i unikaj używania po utylizacji

Oprócz użycia osłony, jak opisano w sekcji Ochrona przed wieloma <xref:System.Threading.CancellationToken> [wysyłkami,](#guard-against-multiple-dispatches) należy rozważyć użycie a, aby anulować długotrwałe operacje, gdy komponent jest usuwany. Takie podejście ma dodatkową zaletę, aby uniknąć *użycia po zużyciu* w komponentach:

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

Niektóre zdarzenia DOM, `oninput` `onscroll`takie jak lub , mogą powodować dużą ilość danych. Unikaj używania Blazor tych zdarzeń w aplikacjach serwera.

## <a name="additional-security-guidance"></a>Dodatkowe wskazówki dotyczące zabezpieczeń

Wskazówki dotyczące zabezpieczania aplikacji ASP.NET Blazor Core mają zastosowanie do aplikacji serwera i są omówione w następujących sekcjach:

* [Rejestrowanie i dane poufne](#logging-and-sensitive-data)
* [Ochrona informacji podczas przesyłania za pomocą protokołu HTTPS](#protect-information-in-transit-with-https)
* [Skrypty międzyuczełowe (XSS)](#cross-site-scripting-xss))
* [Ochrona przed krzyżowym pochodzeniem](#cross-origin-protection)
* [Zatrzaskiwanie](#click-jacking)
* [Otwarte przekierowania](#open-redirects)

### <a name="logging-and-sensitive-data"></a>Rejestrowanie i dane poufne

Interakcje międzyoperacyjne JS między klientem a serwerem są <xref:Microsoft.Extensions.Logging.ILogger> rejestrowane w dziennikach serwera z wystąpieniami. Blazorpozwala uniknąć rejestrowania poufnych informacji, takich jak rzeczywiste zdarzenia lub wejścia i wyjścia interop JS.

Gdy wystąpi błąd na serwerze, struktura powiadamia klienta i rozdziera sesję. Domyślnie klient otrzymuje ogólny komunikat o błędzie, który można zobaczyć w narzędziach programistycznych przeglądarki.

Błąd po stronie klienta nie zawiera callstack i nie zawiera szczegółów na temat przyczyny błędu, ale dzienniki serwera zawierają takie informacje. Do celów programisty, poufne informacje o błędach mogą być udostępniane klientowi, włączając szczegółowe błędy.

Włącz szczegółowe błędy za pomocą:

* `CircuitOptions.DetailedErrors`.
* `DetailedErrors`konfiguracji. Na przykład ustaw `ASPNETCORE_DETAILEDERRORS` zmienną środowiskową `true`na wartość .

> [!WARNING]
> Udostępnianie informacji o błędzie klientom w Internecie jest zagrożeniem bezpieczeństwa, których należy zawsze unikać.

### <a name="protect-information-in-transit-with-https"></a>Ochrona informacji podczas przesyłania za pomocą protokołu HTTPS

BlazorSerwer używa SignalR do komunikacji między klientem a serwerem. BlazorSerwer zwykle używa transportu, SignalR który negocjuje, który jest zazwyczaj WebSockets.

BlazorSerwer nie zapewnia integralności i poufności danych wysyłanych między serwerem a klientem. Zawsze używaj protokołu HTTPS.

### <a name="cross-site-scripting-xss"></a>Skrypty międzyuczełowe (XSS)

Skrypty między witrynami (XSS) umożliwiają nieautoryzowanej stronie wykonywanie dowolnej logiki w kontekście przeglądarki. Rozwiązana aplikacja może potencjalnie uruchomić dowolny kod na kliencie. Luka może zostać wykorzystana do potencjalnego wykonania szeregu złośliwych działań przeciwko serwerowi:

* Wysyłanie fałszywych/nieprawidłowych zdarzeń do serwera.
* Wysyłka nie powiodło się/nieprawidłowe zakończenia renderowania.
* Należy unikać wysyłania uzupełnień renderowania.
* Wysyłanie wywołań międzyoperacyjnych z języka JavaScript do platformy .NET.
* Zmodyfikuj odpowiedź wywołań interop z platformy .NET do JavaScript.
* Należy unikać wysyłania .NET do JS interop wyników.

Struktura Blazor serwera podejmuje kroki w celu ochrony przed niektórymi z powyższych zagrożeń:

* Zatrzymuje tworzenie nowych aktualizacji interfejsu użytkownika, jeśli klient nie potwierdza partii renderowania. Skonfigurowano `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`z programem .
* Po minucie nie pobiera żadnych połączeń z platformy .NET do JavaScript bez odbierania odpowiedzi od klienta. Skonfigurowano `CircuitOptions.JSInteropDefaultCallTimeout`z programem .
* Wykonuje podstawową walidację wszystkich danych wejściowych pochodzących z przeglądarki podczas js interop:
  * Odwołania .NET są prawidłowe i typu oczekiwanego przez metodę .NET.
  * Dane nie są zniekształcone.
  * W ładunku znajduje się prawidłowa liczba argumentów dla metody.
  * Argumenty lub wynik można deserializacji poprawnie przed wywołaniem metody.
* Wykonuje podstawowe sprawdzanie poprawności we wszystkich danych wejściowych pochodzących z przeglądarki z wysłanych zdarzeń:
  * Zdarzenie ma prawidłowy typ.
  * Dane dla zdarzenia mogą być deserializowane.
  * Istnieje program obsługi zdarzeń skojarzony ze zdarzeniem.

Oprócz zabezpieczeń, które implementuje ramach, aplikacja musi być kodowane przez dewelopera w celu ochrony przed zagrożeniami i podjąć odpowiednie działania:

* Zawsze sprawdzaj poprawność danych podczas obsługi zdarzeń.
* Podejmij odpowiednie działania po otrzymaniu nieprawidłowych danych:
  * Zignoruj dane i wróć. Dzięki temu aplikacja może kontynuować przetwarzanie żądań.
  * Jeśli aplikacja stwierdzi, że dane wejściowe są bezprawne i nie mogą być produkowane przez legalnego klienta, zgłoś wyjątek. Zgłaszanie wyjątku rozrywa obwód i kończy sesję.
* Nie ufaj komunikatowi o błędzie dostarczonej przez ukończenia partii renderowania zawarte w dziennikach. Błąd jest dostarczany przez klienta i zazwyczaj nie można ufać, ponieważ klient może zostać naruszony.
* Nie ufaj wejściom na js interop wywołania w obu kierunkach między JavaScript i .NET metody.
* Aplikacja jest odpowiedzialna za sprawdzanie poprawności, że zawartość argumentów i wyników są prawidłowe, nawet jeśli argumenty lub wyniki są poprawnie deserializowane.

Aby luka w zabezpieczeniach XSS istniała, aplikacja musi zawierać dane wejściowe użytkownika na renderowanej stronie. BlazorSkładniki serwera wykonać krok w czasie kompilacji, gdzie znaczników w pliku *.brzytwa* jest przekształcany w proceduralnych logiki C#. W czasie wykonywania logiki języka C# tworzy *drzewo renderowania* opisujące elementy, tekst i składniki podrzędne. Jest to stosowane do modelu DOM przeglądarki za pomocą sekwencji instrukcji JavaScript (lub jest serializowane do HTML w przypadku prerenderingu):

* Dane wejściowe użytkownika renderowane za pomocą normalnej składni Razor (na przykład) nie ujawniają luki w zabezpieczeniach XSS, ponieważ składnia Razor jest dodawana do DOM za pomocą poleceń, `@someStringValue`które mogą pisać tylko tekst. Nawet jeśli wartość zawiera znaczniki HTML, wartość jest wyświetlana jako tekst statyczny. Podczas prerendering, dane wyjściowe jest zakodowany w formacie HTML, który również wyświetla zawartość jako tekst statyczny.
* Tagi skryptów nie są dozwolone i nie powinny być uwzględniane w drzewie renderowania składników aplikacji. Jeśli znacznik skryptu znajduje się w znacznikach składnika, generowany jest błąd w czasie kompilacji.
* Autorzy składników mogą tworzyć składniki w języku C# bez użycia Razor. Autor składnika jest odpowiedzialny za używanie poprawnych interfejsów API podczas emitowania danych wyjściowych. Na przykład `builder.AddContent(0, someUserSuppliedString)` użyj, a *nie* `builder.AddMarkupContent(0, someUserSuppliedString)`, ponieważ ta ostatnia może utworzyć lukę W ZABEZPIECZENIACH XSS.

W ramach ochrony przed atakami XSS należy rozważyć wdrożenie środków ograniczających zagrożenie XSS, takich jak [zasady zabezpieczeń zawartości (CSP).](https://developer.mozilla.org/docs/Web/HTTP/CSP)

Aby uzyskać więcej informacji, zobacz <xref:security/cross-site-scripting>.

### <a name="cross-origin-protection"></a>Ochrona przed krzyżowym pochodzeniem

Ataki między źródłami obejmują klienta z innego pochodzenia wykonującego akcję przeciwko serwerowi. Złośliwa akcja jest zazwyczaj żądanie GET lub post formularza (Cross-Site Request Forgery, CSRF), ale otwarcie złośliwego WebSocket jest również możliwe. BlazorAplikacje serwera oferują [takie SignalR same gwarancje, że każda inna aplikacja korzystająca z protokołu koncentratora oferuje:](xref:signalr/security)

* BlazorAplikacje serwera są dostępne cross-origin, chyba że zostaną podjęte dodatkowe środki, aby temu zapobiec. Aby wyłączyć dostęp między źródłami, wyłącz cors w punkcie końcowym, dodając oprogramowanie pośredniczące CORS do potoku i dodając `DisableCorsAttribute` do metadanych punktu Blazor końcowego lub ogranicz zestaw dozwolonych źródeł, [konfigurując SignalR udostępnianie zasobów między źródłami.](xref:signalr/security#cross-origin-resource-sharing)
* Jeśli usługa CORS jest włączona, w zależności od konfiguracji cors mogą być wymagane dodatkowe kroki w celu ochrony aplikacji. Jeśli usługa CORS jest globalnie włączona, Blazor można wyłączyć `DisableCorsAttribute` usługę CORS dla centrum Serwer, dodając metadane do metadanych punktu końcowego po wywołaniu `hub.MapBlazorHub()`.

Aby uzyskać więcej informacji, zobacz <xref:security/anti-request-forgery>.

### <a name="click-jacking"></a>Zatrzaskiwanie

Kliknięcie polega na renderowaniu witryny `<iframe>` jako witryny z innego pochodzenia, aby skłonić użytkownika do wykonywania działań w zaatakowanej witrynie.

Aby chronić aplikację przed renderowaniem `<iframe>`wewnątrz programu , użyj zasady zabezpieczeń zawartości [(CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) i nagłówka. `X-Frame-Options` Aby uzyskać więcej informacji, zobacz [dokumenty internetowe MDN: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).

### <a name="open-redirects"></a>Otwarte przekierowania

Po Blazor uruchomieniu sesji aplikacji serwera serwer wykonuje podstawową sprawdzanie poprawności adresów URL wysłanych w ramach uruchamiania sesji. Struktura sprawdza, czy podstawowy adres URL jest nadrzędnym bieżącym adresem URL przed ustanowieniem obwodu. Żadne dodatkowe kontrole nie są wykonywane przez ramy.

Gdy użytkownik wybierze łącze na kliencie, adres URL łącza jest wysyłany do serwera, który określa, jakie działania należy podjąć. Na przykład aplikacja może wykonać nawigację po stronie klienta lub wskazać w przeglądarce, aby przejść do nowej lokalizacji.

Składniki mogą również programowo wyzwalać żądania `NavigationManager`nawigacji za pomocą programu . W takich scenariuszach aplikacja może wykonać nawigację po stronie klienta lub wskazać w przeglądarce, aby przejść do nowej lokalizacji.

Składniki muszą:

* Należy unikać używania danych wejściowych użytkownika jako część argumentów wywołania nawigacji.
* Sprawdź poprawność argumentów, aby upewnić się, że obiekt docelowy jest dozwolony przez aplikację.

W przeciwnym razie złośliwy użytkownik może wymusić na przeglądarce, aby przejść do witryny kontrolowanej przez osobę atakującą. W tym scenariuszu osoba atakująca sztuczki aplikacji do korzystania z niektórych danych wejściowych użytkownika w ramach wywołania `NavigationManager.Navigate` metody.

Ta rada ma również zastosowanie podczas renderowania linków w ramach aplikacji:

* Jeśli to możliwe, użyj linków względnych.
* Sprawdź, czy bezwzględne miejsca docelowe łączy są prawidłowe przed dołączeniem ich do strony.

Aby uzyskać więcej informacji, zobacz <xref:security/preventing-open-redirects>.

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja

Aby uzyskać wskazówki dotyczące <xref:security/blazor/index>uwierzytelniania i autoryzacji, zobacz .

## <a name="security-checklist"></a>Lista kontrolna zabezpieczeń

Poniższa lista zagadnień dotyczących zabezpieczeń nie jest wyczerpująca:

* Sprawdzanie poprawności argumentów ze zdarzeń.
* Sprawdź poprawność danych wejściowych i wyników wywołań interop JS.
* Należy unikać używania (lub sprawdzania poprawności wcześniej) dane wejściowe użytkownika dla .NET do JS wywołania interop.
* Uniemożliwić klientowi przydzielanie niezwiązanej ilości pamięci.
  * Dane w składniku.
  * `DotNetObject`odwołania zwrócone do klienta.
* Ochrona przed wieloma wysyłkami.
* Anuluj długotrwałe operacje, gdy składnik jest usuwany.
* Unikaj zdarzeń, które generują duże ilości danych.
* Należy unikać używania danych `NavigationManager.Navigate` wejściowych użytkownika w ramach wywołań i sprawdzania poprawności danych wejściowych użytkownika dla adresów URL względem zestawu dozwolonych źródeł najpierw, jeśli jest to nieuniknione.
* Nie podejmuj decyzji dotyczących autoryzacji na podstawie stanu interfejsu użytkownika, ale tylko ze stanu składnika.
* Należy rozważyć użycie [zasad zabezpieczeń zawartości (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) do ochrony przed atakami XSS.
* Należy rozważyć użycie CSP i [X-Frame-Options,](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) aby chronić przed zatrzaskiem.
* Upewnij się, że ustawienia cors są odpowiednie, gdy Blazor włączasz cors lub jawnie wyłączyć CORS dla aplikacji.
* Przetestuj, aby upewnić Blazor się, że limity po stronie serwera dla aplikacji zapewniają akceptowalne środowisko użytkownika bez niedopuszczalnych poziomów ryzyka.
