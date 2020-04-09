---
title: ASP.NET Podstawowe Blazor zarządzanie stanem
author: guardrex
description: Dowiedz się, jak Blazor zachowywać stan w aplikacjach serwera.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: e8a1959a8fc05ea59362bb5824181a9d2e418811
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218872"
---
# <a name="aspnet-core-opno-locblazor-state-management"></a>ASP.NET Podstawowe Blazor zarządzanie stanem

Przez [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

BlazorSerwer jest stanową strukturą aplikacji. W większości przypadków aplikacja utrzymuje stałe połączenie z serwerem. Stan użytkownika jest przechowywany w pamięci serwera w *obwodzie*. 

Przykłady stanu utrzymywanego dla obwodu użytkownika obejmują:

* Renderowany interfejs&mdash;użytkownika hierarchii wystąpień składników i ich najnowsze dane wyjściowe renderowania.
* Wartości wszystkich pól i właściwości w wystąpieniach komponentów.
* Dane przechowywane w instancja usługi [iniekcji zależności (DI),](xref:fundamentals/dependency-injection) które są ograniczone do obwodu.

> [!NOTE]
> Ten artykuł dotyczy trwałości stanu w Blazor aplikacjach serwera. BlazorAplikacje WebAssembly mogą korzystać z [trwałości stanu po stronie klienta w przeglądarce,](#client-side-in-the-browser) ale wymagają rozwiązań niestandardowych lub pakietów innych firm poza zakresem tego artykułu.

## <a name="opno-locblazor-circuits"></a>BlazorUkłady scalone

Jeśli użytkownik doświadcza tymczasowej utraty Blazor połączenia sieciowego, próbuje ponownie połączyć użytkownika z oryginalnym obwodem, aby mógł nadal korzystać z aplikacji. Jednak ponowne połączenie użytkownika z oryginalnym obwodem w pamięci serwera nie zawsze jest możliwe:

* Serwer nie może zachować odłączony obwód na zawsze. Serwer musi zwolnić odłączony obwód po upływie limitu czasu lub gdy serwer znajduje się pod ciśnieniem pamięci.
* W środowiskach wieloserwerowych, zrównoważonych obciążeniem, wszelkie żądania przetwarzania serwera mogą stać się niedostępne w danym momencie. Poszczególne serwery mogą zakończyć się niepowodzeniem lub zostać automatycznie usunięte, gdy nie są już wymagane do obsługi ogólnej liczby żądań. Oryginalny serwer może być niedostępny, gdy użytkownik próbuje ponownie nawiązać połączenie.
* Użytkownik może zamknąć i ponownie otworzyć przeglądarkę lub ponownie załadować stronę, co usuwa każdy stan przechowywany w pamięci przeglądarki. Na przykład wartości ustawione za pośrednictwem wywołań interop JavaScript są tracone.

Gdy nie można ponownie podłączyć użytkownika do oryginalnego obwodu, użytkownik otrzymuje nowy obwód z pustym stanem. Jest to równoważne zamknięciu i ponownym otwarciu aplikacji klasycznej.

## <a name="preserve-state-across-circuits"></a>Zachowywanie stanu w obwodach

W niektórych scenariuszach zachowanie stanu w obwodach jest pożądane. Aplikacja może zachować ważne dane dla użytkownika, jeśli:

* Serwer sieci web staje się niedostępny.
* Przeglądarka użytkownika jest zmuszona do uruchomienia nowego obwodu z nowym serwerem www.

Ogólnie rzecz biorąc utrzymanie stanu w obwodach ma zastosowanie do scenariuszy, w których użytkownicy aktywnie tworzą dane, a nie po prostu odczytują dane, które już istnieją.

Aby zachować stan poza jednym obwodem, *nie przechowuj tylko danych w pamięci serwera.* Aplikacja musi utrwalić dane do innej lokalizacji magazynu. Trwałość stanu nie&mdash;jest automatyczne należy podjąć kroki podczas tworzenia aplikacji do implementowania trwałości danych stanowych.

Trwałość danych jest zazwyczaj wymagane tylko dla stanu o wysokiej wartości, że użytkownicy mają wydatkowane wysiłku, aby utworzyć. W poniższych przykładach stan utrwalania oszczędza czas lub pomoce w działalności komercyjnej:

* Wieloetapowy &ndash; webform Jest to czasochłonne dla użytkownika, aby ponownie wprowadzić dane dla kilku zakończonych kroków wieloetapowego procesu, jeśli ich stan zostanie utracony. Użytkownik traci stan w tym scenariuszu, jeśli przejść od formularza wieloetapowego i powrócić do formularza później.
* Koszyk &ndash; Każdy ważny z handlowego punktu widzenia składnik aplikacji, który reprezentuje potencjalne przychody, może zostać zachowany. Użytkownik, który traci swój stan, a tym samym swój koszyk, może kupić mniej produktów lub usług, gdy wróci do witryny później.

Zwykle nie jest konieczne zachowanie łatwo odtworzonego stanu, takiego jak nazwa użytkownika wprowadzona w oknie dialogowym logowania, które nie zostało przesłane.

> [!IMPORTANT]
> Aplikacja może tylko zachowywać *stan aplikacji*. Nie można utrwalić interfejsów użytkownika, takich jak wystąpienia składników i ich drzewa renderowania. Składniki i drzewa renderowania nie są zazwyczaj serializable. Aby utrwalić coś podobnego do stanu interfejsu użytkownika, takich jak rozwinięte węzły TreeView, aplikacja musi mieć niestandardowy kod do modelowania zachowania jako serializable stanu aplikacji.

## <a name="where-to-persist-state"></a>Gdzie utrwalić stan

Istnieją trzy typowe lokalizacje dla Blazor stanu utrwalania w aplikacji serwera. Każde podejście najlepiej nadaje się do różnych scenariuszy i ma różne zastrzeżenia:

* [Po stronie serwera w bazie danych](#server-side-in-a-database)
* [Adres URL](#url)
* [Po stronie klienta w przeglądarce](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a>Po stronie serwera w bazie danych

W przypadku trwałej trwałości danych lub dla wszystkich danych, które muszą obejmować wielu użytkowników lub urządzenia, niezależna baza danych po stronie serwera jest prawie na pewno najlepszym wyborem. Dostępne są następujące opcje:

* Relacyjna baza danych SQL
* Magazyn wartości klucza
* Sklep blob
* Sklep z tabelami

Po zapisaniu danych w bazie danych, nowy obwód może być uruchomiony przez użytkownika w dowolnym momencie. Dane użytkownika są przechowywane i dostępne w każdym nowym obwodzie.

Aby uzyskać więcej informacji na temat opcji magazynu danych platformy Azure, zobacz [Dokumentację usługi Azure Storage](/azure/storage/) i [bazy danych platformy Azure](https://azure.microsoft.com/product-categories/databases/).

### <a name="url"></a>Adres URL

W przypadku danych przejściowych reprezentujących stan nawigacji należy modelować dane jako część adresu URL. Przykłady stanu modelowanego w adresie URL obejmują:

* Identyfikator oglądanej encji.
* Bieżący numer strony w siatce.

Zawartość paska adresu przeglądarki jest zachowywana:

* Jeśli użytkownik ręcznie ładuje stronę.
* Jeśli serwer sieci&mdash;web stanie się niedostępny, użytkownik jest zmuszony do ponownego załadowania strony w celu nawiązania połączenia z innym serwerem.

Aby uzyskać informacje na temat `@page` definiowania <xref:blazor/routing>wzorców adresów URL za pomocą dyrektywy, zobacz .

### <a name="client-side-in-the-browser"></a>Po stronie klienta w przeglądarce

W przypadku danych przejściowych, które użytkownik aktywnie tworzy, wspólnym zapasem kopii zapasowych jest przeglądarka `localStorage` i `sessionStorage` kolekcje. Aplikacja nie jest wymagana do zarządzania lub czyścić stan przechowywany, jeśli obwód jest porzucony, co jest zaletą w stosunku do magazynu po stronie serwera.

> [!NOTE]
> "Po stronie klienta" w tej sekcji odnosi się do scenariuszy po stronie klienta w przeglądarce, a [ Blazor nie WebAssembly hosting modelu](xref:blazor/hosting-models#blazor-webassembly). `localStorage`i `sessionStorage` może być Blazor używany w aplikacjach WebAssembly, ale tylko przez napisanie kodu niestandardowego lub przy użyciu pakietu innej firmy.

`localStorage`i `sessionStorage` różnią się w następujący sposób:

* `localStorage`jest objęty zakresem przeglądarki użytkownika. Jeśli użytkownik ponownie ładuje stronę lub zamyka i ponownie otwiera przeglądarkę, stan będzie się powtarzał. Jeśli użytkownik otworzy wiele kart przeglądarki, stan jest udostępniany na kartach. Dane są `localStorage` zachowywane do momentu jawnego wyczyszczenie.
* `sessionStorage`jest objęty zakresem do karty przeglądarki użytkownika. Jeśli użytkownik ponownie ładuje kartę, stan będzie się powtarzał. Jeśli użytkownik zamknie kartę lub przeglądarkę, stan zostanie utracony. Jeśli użytkownik otworzy wiele kart przeglądarki, każda karta ma własną niezależną wersję danych.

Ogólnie rzecz `sessionStorage` biorąc, jest bezpieczniejsze w użyciu. `sessionStorage`pozwala uniknąć ryzyka, że użytkownik otworzy wiele kart i napotka następujące:

* Błędy w magazynie stanu na kartach.
* Mylące zachowanie, gdy karta zastępuje stan innych kart.

`localStorage`jest lepszym wyborem, jeśli aplikacja musi zachowywać stan przez zamknięcie i ponowne otwarcie przeglądarki.

Zastrzeżenia dotyczące korzystania z pamięci masowej przeglądarki:

* Podobnie jak w przypadku korzystania z bazy danych po stronie serwera, ładowanie i zapisywanie danych są asynchroniczne.
* W przeciwieństwie do bazy danych po stronie serwera magazyn nie jest dostępny podczas prerendering, ponieważ żądana strona nie istnieje w przeglądarce podczas etapu prerendering.
* Przechowywanie kilku kilobajtów danych jest Blazor uzasadnione, aby utrzymać się dla aplikacji serwera. Poza kilka kilobajtów należy wziąć pod uwagę wpływ na wydajność, ponieważ dane są ładowane i zapisywane w sieci.
* Użytkownicy mogą przeglądać lub manipulować danymi. ASP.NET Podstawowa [ochrona danych](xref:security/data-protection/introduction) może zmniejszyć ryzyko.

## <a name="third-party-browser-storage-solutions"></a>Rozwiązania do przechowywania przeglądarek innych firm

Pakiety NuGet innych firm zapewniają interfejsy API do pracy z `localStorage` i `sessionStorage`.

Warto rozważyć wybór pakietu, który w przejrzysty sposób wykorzystuje ASP.NET [Ochronie Danych](xref:security/data-protection/introduction)Core. ASP.NET Core Data Protection szyfruje przechowywane dane i zmniejsza potencjalne ryzyko manipulowania przechowywanymi danymi. Jeśli dane serializowane json są przechowywane w postaci zwykłego tekstu, użytkownicy mogą zobaczyć dane za pomocą narzędzi programistycznych przeglądarki, a także zmodyfikować przechowywane dane. Zabezpieczanie danych nie zawsze stanowi problem, ponieważ dane mogą być trywialne w przyrodzie. Na przykład odczyt lub modyfikacja przechowywanego koloru elementu interfejsu użytkownika nie stanowi istotnego zagrożenia bezpieczeństwa dla użytkownika lub organizacji. Unikaj zezwalania użytkownikom na inspekcje lub manipulowanie *poufnymi danymi.*

## <a name="protected-browser-storage-experimental-package"></a>Pakiet eksperymentalny chronionego przechowywania przeglądarki

Przykład pakietu NuGet, który zapewnia `localStorage` ochronę `sessionStorage` [danych](xref:security/data-protection/introduction) dla i jest [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage`jest nieobsługiwanym pakietem eksperymentalnym nienadaje się do wykorzystania w tej chwili.

### <a name="installation"></a>Instalacja

Aby zainstalować `Microsoft.AspNetCore.ProtectedBrowserStorage` pakiet:

1. W Blazor projekcie aplikacji Serwer dodaj odwołanie do pakietu do [pliku Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).
1. W formacie HTML najwyższego poziomu (na przykład w pliku *Pages/_Host.cshtml* w domyślnym szablonie projektu) dodaj następujący `<script>` tag:

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. W `Startup.ConfigureServices` metodzie `AddProtectedBrowserStorage` wywołać, aby dodać `localStorage` i `sessionStorage` usługi do kolekcji usługi:

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Zapisywanie i ładowanie danych w ramach składnika

W każdym składniku, który wymaga ładowania [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) lub zapisywania danych do magazynu przeglądarki, użyj, aby wstrzyknąć wystąpienie jednego z następujących czynności:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

Wybór zależy od tego, którego zapasu chcesz użyć. W poniższym `sessionStorage` przykładzie jest używany:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

Instrukcję `@using` można umieścić w pliku *_Imports.brzytwa,* a nie w komponencie. Korzystanie z pliku *_Imports.razor* sprawia, że obszar nazw jest dostępny dla większych segmentów aplikacji lub całej aplikacji.

Aby `_currentCount` utrwalić `Counter` wartość składnika szablonu `IncrementCount` projektu, `ProtectedSessionStore.SetAsync`zmodyfikuj metodę użycia:

```csharp
private async Task IncrementCount()
{
    _currentCount++;
    await ProtectedSessionStore.SetAsync("count", _currentCount);
}
```

W większych, bardziej realistycznych aplikacjach przechowywanie poszczególnych pól jest mało prawdopodobnym scenariuszem. Aplikacje są bardziej prawdopodobne, aby przechowywać całe obiekty modelu, które zawierają stan złożony. `ProtectedSessionStore`automatycznie serializuje i deserializuje dane JSON.

W poprzednim przykładzie kodu `_currentCount` dane są `sessionStorage['count']` przechowywane tak jak w przeglądarce użytkownika. Dane nie są przechowywane w postaci zwykłego tekstu, ale są chronione za pomocą ASP.NET [Ochrony Danych Core.](xref:security/data-protection/introduction) Zaszyfrowane dane można zobaczyć, jeśli `sessionStorage['count']` są oceniane w konsoli dewelopera przeglądarki.

Aby odzyskać `_currentCount` dane, jeśli użytkownik `Counter` powróci do składnika później (w tym jeśli `ProtectedSessionStore.GetAsync`jest w zupełnie nowym obwodzie), należy użyć:

```csharp
protected override async Task OnInitializedAsync()
{
    _currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

Jeśli parametry składnika obejmują stan `ProtectedSessionStore.GetAsync` nawigacji, wywołaj `OnParametersSetAsync`i `OnInitializedAsync`przypisz wynik w , nie . `OnInitializedAsync`jest wywoływana tylko jeden raz, gdy składnik jest po raz pierwszy smówiło się. `OnInitializedAsync`nie zostanie wywołana później, jeśli użytkownik przejdzie do innego adresu URL, pozostając na tej samej stronie. Aby uzyskać więcej informacji, zobacz <xref:blazor/lifecycle>.

> [!WARNING]
> Przykłady w tej sekcji działają tylko wtedy, gdy serwer nie ma włączonego świszczącego dostępu. Po włączeniu prerenderingu generowany jest błąd podobny do:
>
> > W tej chwili nie można wykonywać połączeń międzyoperacyjnych JavaScript. Dzieje się tak, ponieważ składnik jest prerendered.
>
> Wyłącz prerendering lub dodaj dodatkowy kod do pracy z prerendering. Aby dowiedzieć się więcej na temat pisania kodu, który działa z prerendering, zobacz [Handle prerendering](#handle-prerendering) sekcji.

### <a name="handle-the-loading-state"></a>Obsługa stanu załadunku

Ponieważ magazyn przeglądarki jest asynchroniczne (dostęp za pośrednictwem połączenia sieciowego), zawsze jest okres czasu, zanim dane są ładowane i dostępne do użycia przez składnik. Aby uzyskać najlepsze wyniki, renderuj komunikat o stanie ładowania podczas ładowania, zamiast wyświetlania pustych lub domyślnych danych.

Jednym z podejść jest `null` śledzenie, czy dane są (nadal ładowane), czy nie. W komponencie domyślnym `Counter` liczba `int`jest utrzymywana w pliku . Aby `_currentCount` można było anulować,`?`dodając znak`int`zapytania ( ) do typu ( ):

```csharp
private int? _currentCount;
```

Zamiast bezwarunkowo wyświetlać przycisk Count i **Increment,** wybierz opcję wyświetlania tych elementów tylko wtedy, gdy dane są ładowane:

```razor
@if (_currentCount.HasValue)
{
    <p>Current count: <strong>@_currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Przemówienie uchwytu

Podczas prerendering:

* Interaktywne połączenie z przeglądarką użytkownika nie istnieje.
* Przeglądarka nie ma jeszcze strony, na której można uruchomić kod JavaScript.

`localStorage`lub `sessionStorage` nie są dostępne podczas prerendering. Jeśli składnik próbuje wchodzić w interakcję z magazynem, generowany jest błąd podobny do:

> W tej chwili nie można wykonywać połączeń międzyoperacyjnych JavaScript. Dzieje się tak, ponieważ składnik jest prerendered.

Jednym ze sposobów rozwiązania problemu jest wyłączenie prerenderingu. Jest to zwykle najlepszy wybór, jeśli aplikacja intensywnie korzysta z pamięci masowej opartej na przeglądarce. Prerendering zwiększa złożoność i nie przynosi korzyści aplikacji, ponieważ aplikacja nie `localStorage` może `sessionStorage` prerenderować żadnej przydatnej zawartości, dopóki nie będzie dostępna.

Aby wyłączyć program prerendering, otwórz plik *Pages/_Host.cshtml* i zmień `render-mode` pomocnika <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> [znacznika składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) na .

Prerendering może być przydatny w przypadku `localStorage` innych `sessionStorage`stron, które nie są używane lub . Aby zachować funkcję prerendering włączone, odroczyć operację ładowania, aż przeglądarka jest podłączony do obwodu. Oto przykład przechowywania wartości licznika:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? _currentCount;
    private bool _isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            _isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        _currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        _currentCount++;
        await ProtectedSessionStore.SetAsync("count", _currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Uwzględnij zachowanie stanu we wspólnym miejscu

Jeśli wiele składników polega na magazyn oparty na przeglądarce, ponowne implementowanie kodu dostawcy stanu wiele razy tworzy duplikację kodu. Jedną z opcji uniknięcia powielania kodu jest utworzenie *składnika nadrzędnego dostawcy stanu,* który hermetyzuje logikę dostawcy stanu. Składniki podrzędne mogą pracować z utrwalonych danych bez względu na mechanizm trwałości stanu.

W poniższym przykładzie składnika `CounterStateProvider` dane licznika są zachowywane:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (_hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool _hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        _hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

Składnik `CounterStateProvider` obsługuje fazy ładowania, nie renderowania jego zawartości podrzędnej, dopóki ładowanie jest zakończona.

Aby użyć `CounterStateProvider` składnika, zawiń wystąpienie składnika wokół dowolnego innego składnika, który wymaga dostępu do stanu licznika. Aby udostępnić stan wszystkim składnikom w `CounterStateProvider` aplikacji, `Router` zawiń `App` składnik wokół składnika (*App.brzytwa):*

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Opakowane składniki odbierać i można zmodyfikować stan licznika utrwalone. Następujący `Counter` składnik implementuje wzorzec:

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

Poprzedni składnik nie jest wymagany do `ProtectedBrowserStorage`interakcji z , ani nie zajmuje się fazą "ładowania".

Aby poradzić sobie z prerendering `CounterStateProvider` jak opisano wcześniej, można zmienić tak, aby wszystkie składniki, które zużywają dane licznika automatycznie pracować z prerendering. Zobacz [Handle prerendering](#handle-prerendering) sekcji, aby uzyskać szczegółowe informacje.

Ogólnie rzecz biorąc zaleca się wzorzec *składnika nadrzędnego dostawcy stanu:*

* Aby zużywać stan w wielu innych składnikach.
* Jeśli istnieje tylko jeden obiekt stanu najwyższego poziomu do utrwalenia.

Aby utrwalić wiele różnych obiektów stanu i zużywają różne podzbiory obiektów w różnych miejscach, lepiej jest unikać obsługi ładowania i zapisywania stanu na całym świecie.
