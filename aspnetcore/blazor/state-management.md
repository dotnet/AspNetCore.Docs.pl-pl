---
title: BlazorZarządzanie stanem ASP.NET Core
author: guardrex
description: Dowiedz się, jak utrwalać stan w Blazor Server aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 24f845bc1d98331f2ee54710d17beb6ffa95ad88
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855420"
---
# <a name="aspnet-core-no-locblazor-state-management"></a>BlazorZarządzanie stanem ASP.NET Core

[Steve Sanderson](https://github.com/SteveSandersonMS) i [Luke Latham](https://github.com/guardrex)

::: zone pivot="webassembly"

Stan użytkownika utworzony w Blazor WebAssembly aplikacji jest przechowywany w pamięci przeglądarki.

Przykłady stanu użytkownika znajdującego się w pamięci przeglądarki obejmują:

* Hierarchia wystąpień składników i ich najnowsze dane wyjściowe renderowania w renderowanym interfejsie użytkownika.
* Wartości pól i właściwości w wystąpieniach składników.
* Dane przechowywane w wystąpieniach usługi [wtrysku zależności (di)](xref:fundamentals/dependency-injection) .
* Wartości ustawione za poorednictwem wywołań [międzyoperacyjnych języka JavaScript](xref:blazor/call-javascript-from-dotnet) .

Gdy użytkownik zamknie i ponownie otworzy swoją przeglądarkę lub załaduje stronę, stan użytkownika znajdujący się w pamięci przeglądarki zostanie utracony.

> [!NOTE]
> [Chroniony magazyn przeglądarki](xref:blazor/state-management?pivots=server#aspnet-core-protected-browser-storage) ( <xref:Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage?displayProperty=fullName> przestrzeń nazw) opiera się na ASP.NET Core ochrony danych i jest obsługiwany tylko w przypadku Blazor Server aplikacji.

## <a name="persist-state-across-browser-sessions"></a>Stan utrwalania w sesjach przeglądarki

Ogólnie rzecz biorąc, utrzymuje stan w sesjach przeglądarki, w których użytkownicy aktywnie tworzą dane, a nie tylko odczytujące dane, które już istnieją.

Aby zachować stan między sesjami przeglądarki, aplikacja musi przechowywać dane w innej lokalizacji magazynu niż pamięć przeglądarki. Trwałość stanu nie jest automatyczna. Podczas tworzenia aplikacji należy wykonać kroki w celu zaimplementowania stanu trwałości danych stanowych.

Trwałość danych jest zazwyczaj wymagana tylko w przypadku stanu wysokiej wartości, który użytkownicy wystawią nakłady na tworzenie. W poniższych przykładach stan utrwalania polega na zapisywaniu czasu lub pomocy w działaniach komercyjnych:

* Wieloetapowe formularze sieci Web: czasochłonne, aby użytkownik mógł ponownie wprowadzić dane dla kilku ukończonych kroków wieloetapowego formularza sieci Web, jeśli ich stan zostanie utracony. Użytkownik utraci stan w tym scenariuszu, jeśli opuszcza formularz i wróci później.
* Koszyki: każdy handlowy istotny składnik aplikacji, który reprezentuje potencjalne dochody, może być utrzymywany. Użytkownik, który straci swój stan, a tym samym koszyk, może zakupić mniejszą liczbę produktów lub usług w momencie powrotu do lokacji w przyszłości.

Aplikacja może utrzymywać tylko *stan aplikacji*. Interfejsów użytkownika nie mogą być utrwalane, takie jak wystąpienia składników i ich drzewa renderowania. Składniki i drzewa renderowania nie są generalnie serializowane. Aby zachować stan interfejsu użytkownika, na przykład rozwinięte węzły kontrolki widoku drzewa, aplikacja musi używać niestandardowego kodu do modelowania zachowania stanu interfejsu użytkownika jako możliwy do serializacji stan aplikacji.

## <a name="where-to-persist-state"></a>Gdzie będzie trwały stan

Istnieją wspólne lokalizacje dla stanu utrwalania:

* [Magazyn po stronie serwera](#server-side-storage-wasm)
* [Adres URL](#url-wasm)
* [Magazyn przeglądarki](#browser-storage-wasm)
* [Usługa kontenera stanu w pamięci](#in-memory-state-container-service-wasm)

<h2 id="server-side-storage-wasm">Magazyn po stronie serwera</h2>

W przypadku trwałej trwałości danych obejmującej wielu użytkowników i wszystkie urządzenia aplikacja może używać niezależnego magazynu po stronie serwera, dostępnego za pośrednictwem internetowego interfejsu API. Dostępne opcje:

* Blob Storage
* Magazyn kluczy i wartości
* Relacyjna baza danych
* Magazyn tabel

Po zapisaniu danych stan użytkownika jest zachowywany i dostępny w każdej nowej sesji przeglądarki.

Ponieważ Blazor WebAssembly aplikacje działają w całości w przeglądarce użytkownika, wymagają dodatkowych środków w celu uzyskania dostępu do bezpiecznych systemów zewnętrznych, takich jak usługi magazynu i bazy danych. Blazor WebAssembly aplikacje są zabezpieczone w taki sam sposób, jak aplikacje jednostronicowe (aplikacji jednostronicowych). Zazwyczaj aplikacja uwierzytelnia użytkownika za pośrednictwem protokołu [OAuth](https://oauth.net) / [OpenID Connect Connect (OIDC)](https://openid.net/connect/) , a następnie współdziała z usługami magazynu i bazami danych za pośrednictwem wywołań interfejsu API sieci Web dla aplikacji po stronie serwera. Aplikacja po stronie serwera koryguje transfer danych między Blazor WebAssembly aplikacją a usługą magazynu lub bazą danych. Blazor WebAssemblyAplikacja zachowuje tymczasowe połączenie z aplikacją po stronie serwera, podczas gdy aplikacja po stronie serwera ma trwałe połączenie z magazynem.

Więcej informacji można znaleźć w następujących zasobach:

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* Blazor*Zabezpieczenia i Identity* artykułu

Aby uzyskać więcej informacji na temat opcji usługi Azure Data Storage, zobacz następujące tematy:

* [Bazy danych platformy Azure](https://azure.microsoft.com/product-categories/databases/)
* [Dokumentacja usługi Azure Storage](/azure/storage/)

<h2 id="url-wasm">Adres URL</h2>

W przypadku danych przejściowych reprezentujących stan nawigacji należy modelować dane w ramach adresu URL. Przykłady stanu użytkownika z modelem w adresie URL obejmują:

* Identyfikator wyświetlonej jednostki.
* Numer bieżącej strony w siatce stronicowanej.

Zawartość paska adresu przeglądarki jest zachowywana, jeśli użytkownik ręcznie ponownie załaduje stronę.

Aby uzyskać informacje na temat definiowania wzorców adresów URL za pomocą [`@page`](xref:mvc/views/razor#page) dyrektywy, zobacz <xref:blazor/fundamentals/routing> .

<h2 id="browser-storage-wasm">Magazyn przeglądarki</h2>

W przypadku danych przejściowych, które użytkownik aktywnie tworzy, często używaną lokalizacją magazynu jest przeglądarka [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) i [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) Kolekcje:

* `localStorage` jest zakresem okna przeglądarki. Jeśli użytkownik ponownie załaduje stronę lub zamknie i otworzy przeglądarkę, stan będzie się utrzymywał. Jeśli użytkownik otworzy wiele kart przeglądarki, stan jest udostępniany na kartach. Dane są zachowywane `localStorage` do momentu jawnego wyczyszczenia.
* `sessionStorage` jest zakresem na karcie przeglądarki. Jeśli użytkownik ponownie załaduje kartę, stan będzie się utrzymywał. Jeśli użytkownik zamknie kartę lub przeglądarkę, stan zostanie utracony. Jeśli użytkownik otworzy wiele kart przeglądarki, każda karta ma własną niezależną wersję danych.

> [!NOTE]
> `localStorage` i `sessionStorage` mogą być używane w Blazor WebAssembly aplikacjach, ale tylko przez napisanie kodu niestandardowego lub użycie pakietu innej firmy.

Ogólnie rzecz biorąc, `sessionStorage` jest bezpiecznie używać. `sessionStorage` pozwala uniknąć ryzyka, że użytkownik otwiera wiele kart i napotyka następujące kwestie:

* Błędy w magazynie Stanów na kartach.
* Zachowanie mylące, gdy karta zastępuje stan innych kart.

`localStorage` jest lepszym rozwiązaniem, jeśli aplikacja musi utrzymywać stan w trakcie zamykania i ponownego otwierania przeglądarki.

> [!WARNING]
> Użytkownicy mogą wyświetlać lub naruszać dane przechowywane w `localStorage` i `sessionStorage` .

<h2 id="in-memory-state-container-service-wasm">Usługa kontenera stanu w pamięci</h2>

[!INCLUDE[](~/includes/blazor-state-management/state-container.md)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zapisz stan aplikacji przed operacją uwierzytelniania](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

Blazor Server jest platformą aplikacji stanowych. W większości przypadków aplikacja utrzymuje połączenie z serwerem. Stan użytkownika jest przechowywany w pamięci serwera w ramach *obwodu*. 

Przykłady stanu użytkownika przechowywanego w obwodzie:

* Hierarchia wystąpień składników i ich najnowsze dane wyjściowe renderowania w renderowanym interfejsie użytkownika.
* Wartości pól i właściwości w wystąpieniach składników.
* Dane przechowywane w wystąpieniach usługi [wtrysku zależności (di)](xref:fundamentals/dependency-injection) , które są objęte zakresem obwodu.

Stan użytkownika może również znajdować się w zmiennych JavaScript w zestawie pamięci w przeglądarce za pośrednictwem wywołań [międzyoperacyjnych języka JavaScript](xref:blazor/call-javascript-from-dotnet) .

Jeśli użytkownik napotyka chwilową utratę połączenia sieciowego, program Blazor spróbuje ponownie połączyć użytkownika z oryginalnym obwodem. Jednak ponowne połączenie użytkownika z oryginalnym obwodem w pamięci serwera nie zawsze jest możliwe:

* Serwer nie może całkowicie zachować rozłączonego obwodu. Serwer musi zwolnić obwód rozłączony po upływie limitu czasu lub w przypadku, gdy na serwerze znajduje się pamięć.
* W środowiskach wdrażania z obsługą równoważenia obciążenia poszczególne serwery mogą kończyć się niepowodzeniem lub być automatycznie usuwane, gdy nie są już wymagane do obsługi ogólnej liczby żądań. Oryginalne żądania przetwarzania serwera dla użytkownika mogą stać się niedostępne, gdy użytkownik spróbuje ponownie nawiązać połączenie.
* Użytkownik może zamknąć i ponownie otworzyć przeglądarkę lub ponownie załadować stronę, co spowoduje usunięcie wszystkich stanów przechowywanych w pamięci przeglądarki. Na przykład wartości zmiennych JavaScript ustawione za poorednictwem wywołań międzyoperacyjnych języka JavaScript są tracone.

Gdy nie można ponownie nawiązać połączenia użytkownika z oryginalnym obwodem, użytkownik otrzymuje nowy obwód z pustym stanem. Jest to równoznaczne z zamknięciem i ponownym otwarciem aplikacji klasycznej.

## <a name="persist-state-across-circuits"></a>Stan utrwalania w obwodach

Ogólnie rzecz biorąc, utrzymuje stan w obwodach, w których użytkownicy aktywnie tworzą dane, a nie tylko odczytujące dane, które już istnieją.

Aby zachować stan w obwodach, aplikacja musi utrzymywać dane w innej lokalizacji magazynu niż pamięć serwera. Trwałość stanu nie jest automatyczna. Podczas tworzenia aplikacji należy wykonać kroki w celu zaimplementowania stanu trwałości danych stanowych.

Trwałość danych jest zazwyczaj wymagana tylko w przypadku stanu wysokiej wartości, który użytkownicy wystawią nakłady na tworzenie. W poniższych przykładach stan utrwalania polega na zapisywaniu czasu lub pomocy w działaniach komercyjnych:

* Wieloetapowe formularze sieci Web: czasochłonne, aby użytkownik mógł ponownie wprowadzić dane dla kilku ukończonych kroków wieloetapowego formularza sieci Web, jeśli ich stan zostanie utracony. Użytkownik utraci stan w tym scenariuszu, jeśli opuszcza formularz i wróci później.
* Koszyki: każdy handlowy istotny składnik aplikacji, który reprezentuje potencjalne dochody, może być utrzymywany. Użytkownik, który straci swój stan, a tym samym koszyk, może zakupić mniejszą liczbę produktów lub usług w momencie powrotu do lokacji w przyszłości.

Aplikacja może utrzymywać tylko *stan aplikacji*. Interfejsów użytkownika nie mogą być utrwalane, takie jak wystąpienia składników i ich drzewa renderowania. Składniki i drzewa renderowania nie są generalnie serializowane. Aby zachować stan interfejsu użytkownika, na przykład rozwinięte węzły kontrolki widoku drzewa, aplikacja musi używać niestandardowego kodu do modelowania zachowania stanu interfejsu użytkownika jako możliwy do serializacji stan aplikacji.

## <a name="where-to-persist-state"></a>Gdzie będzie trwały stan

Istnieją wspólne lokalizacje dla stanu utrwalania:

* [Magazyn po stronie serwera](#server-side-storage-server)
* [Adres URL](#url-server)
* [Magazyn przeglądarki](#browser-storage-server)
* [Usługa kontenera stanu w pamięci](#in-memory-state-container-service-server)

<h2 id="server-side-storage-server">Magazyn po stronie serwera</h2>

W przypadku trwałej trwałości danych obejmującej wielu użytkowników i urządzenia aplikacja może korzystać z magazynu po stronie serwera. Dostępne opcje:

* Blob Storage
* Magazyn kluczy i wartości
* Relacyjna baza danych
* Magazyn tabel

Po zapisaniu danych stan użytkownika jest zachowywany i dostępny w dowolnym nowym obwodie.

Aby uzyskać więcej informacji na temat opcji usługi Azure Data Storage, zobacz następujące tematy:

* [Bazy danych platformy Azure](https://azure.microsoft.com/product-categories/databases/)
* [Dokumentacja usługi Azure Storage](/azure/storage/)

<h2 id="url-server">Adres URL</h2>

W przypadku danych przejściowych reprezentujących stan nawigacji należy modelować dane w ramach adresu URL. Przykłady stanu użytkownika z modelem w adresie URL obejmują:

* Identyfikator wyświetlonej jednostki.
* Numer bieżącej strony w siatce stronicowanej.

Zawartość paska adresu przeglądarki jest zachowywana:

* Jeśli użytkownik ręcznie ponownie załaduje stronę.
* Jeśli serwer sieci Web stał się niedostępny, a użytkownik jest zmuszony do ponownego załadowania strony, aby można było połączyć się z innym serwerem.

Aby uzyskać informacje na temat definiowania wzorców adresów URL za pomocą [`@page`](xref:mvc/views/razor#page) dyrektywy, zobacz <xref:blazor/fundamentals/routing> .

<h2 id="browser-storage-server">Magazyn przeglądarki</h2>

W przypadku danych przejściowych, które użytkownik aktywnie tworzy, często używaną lokalizacją magazynu jest przeglądarka [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) i [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) Kolekcje:

* `localStorage` jest zakresem okna przeglądarki. Jeśli użytkownik ponownie załaduje stronę lub zamknie i otworzy przeglądarkę, stan będzie się utrzymywał. Jeśli użytkownik otworzy wiele kart przeglądarki, stan jest udostępniany na kartach. Dane są zachowywane `localStorage` do momentu jawnego wyczyszczenia.
* `sessionStorage` jest zakresem na karcie przeglądarki. Jeśli użytkownik ponownie załaduje kartę, stan będzie się utrzymywał. Jeśli użytkownik zamknie kartę lub przeglądarkę, stan zostanie utracony. Jeśli użytkownik otworzy wiele kart przeglądarki, każda karta ma własną niezależną wersję danych.

Ogólnie rzecz biorąc, `sessionStorage` jest bezpiecznie używać. `sessionStorage` pozwala uniknąć ryzyka, że użytkownik otwiera wiele kart i napotyka następujące kwestie:

* Błędy w magazynie Stanów na kartach.
* Zachowanie mylące, gdy karta zastępuje stan innych kart.

`localStorage` jest lepszym rozwiązaniem, jeśli aplikacja musi utrzymywać stan w trakcie zamykania i ponownego otwierania przeglądarki.

Ostrzeżenia dotyczące korzystania z magazynu przeglądarki:

* Podobnie jak w przypadku korzystania z bazy danych po stronie serwera, ładowanie i zapisywanie danych są asynchroniczne.
* W przeciwieństwie do bazy danych po stronie serwera, magazyn nie jest dostępny podczas renderowania wstępnego, ponieważ żądana strona nie istnieje w przeglądarce na etapie renderowania.
* Przechowywanie kilku kilobajtów danych jest rozsądne dla Blazor Server aplikacji. Po kilku kilobajtach należy wziąć pod uwagę wpływ na wydajność, ponieważ dane są ładowane i zapisywane w sieci.
* Użytkownicy mogą wyświetlać i modyfikować dane. [Ochrona danych ASP.NET Core](xref:security/data-protection/introduction) może ograniczyć ryzyko. Na przykład [ASP.NET Core chronionej przeglądarki](#aspnet-core-protected-browser-storage) używa ASP.NET Core ochrony danych.

Pakiety NuGet innych firm zapewniają interfejsy API do pracy z `localStorage` i `sessionStorage` . Warto rozważać wybór pakietu, który w sposób przezroczysty używa [ASP.NET Core ochrony danych](xref:security/data-protection/introduction). Ochrona danych szyfruje przechowywane dane i zmniejsza potencjalne ryzyko naruszenia przechowywanych danych. Jeśli dane serializowane w formacie JSON są przechowywane w postaci zwykłego tekstu, użytkownicy mogą zobaczyć dane przy użyciu narzędzi deweloperskich przeglądarki, a także zmodyfikować przechowywane dane. Zabezpieczanie danych nie zawsze jest problemem, ponieważ dane mogą być proste. Na przykład odczytywanie lub modyfikowanie zapisanego koloru elementu interfejsu użytkownika nie jest istotnym zagrożeniem bezpieczeństwa użytkownika lub organizacji. Unikaj zezwalania użytkownikom na inspekcję i manipulowanie *danymi poufnymi*.

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a>ASP.NET Core chronionego magazynu przeglądarki

ASP.NET Core magazynu chronionej przeglądarki wykorzystuje [ochronę danych ASP.NET Core](xref:security/data-protection/introduction) dla [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) i [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .

> [!NOTE]
> Magazyn chronionej przeglądarki opiera się na ASP.NET Core ochrony danych i jest obsługiwany tylko w przypadku Blazor Server aplikacji.

### <a name="save-and-load-data-within-a-component"></a>Zapisz i Załaduj dane w składniku

W dowolnym składniku wymagającym ładowania lub zapisywania danych w magazynie przeglądarki Użyj [`@inject`](xref:mvc/views/razor#inject) dyrektywy, aby wstrzyknąć wystąpienie jednego z następujących elementów:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

Wybór zależy od lokalizacji magazynu przeglądarki, która ma być używana. W poniższym przykładzie `sessionStorage` jest używany:

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

`@using`Dyrektywa może zostać umieszczona w `_Imports.razor` pliku aplikacji zamiast w składniku. Użycie `_Imports.razor` pliku sprawia, że przestrzeń nazw jest dostępna dla większych segmentów aplikacji lub całej aplikacji.

Aby zachować `currentCount` wartość w `Counter` składniku aplikacji na podstawie Blazor Server szablonu projektu, zmodyfikuj `IncrementCount` metodę, która ma być używana `ProtectedSessionStore.SetAsync` :

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

W większych, bardziej realistycznych aplikacjach przechowywanie pojedynczych pól jest mało prawdopodobne. Aplikacje są bardziej podobne do przechowywania całych obiektów modelu, które zawierają złożone Stany. `ProtectedSessionStore` automatycznie deserializacji i deserializacji dane JSON do przechowywania obiektów stanu złożonego.

W poprzednim przykładzie kodu `currentCount` dane są przechowywane jako `sessionStorage['count']` w przeglądarce użytkownika. Dane nie są przechowywane w postaci zwykłego tekstu, ale nie są chronione przy użyciu ASP.NET Core ochrony danych. Zaszyfrowane dane można sprawdzić, jeśli `sessionStorage['count']` są oceniane w konsoli dewelopera w przeglądarce.

Aby odzyskać `currentCount` dane, jeśli użytkownik powróci do `Counter` składnika później, w tym, jeśli użytkownik znajduje się w nowym obwodzie, użyj `ProtectedSessionStore.GetAsync` :

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

Jeśli parametry składnika obejmują stan nawigacji, wywołanie `ProtectedSessionStore.GetAsync` i przypisanie `null` niewyniku do <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> , nie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> . <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> jest wywoływana tylko raz podczas pierwszego wystąpienia składnika. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> nie zostanie wywołana ponownie później, jeśli użytkownik przejdzie do innego adresu URL, a pozostałe na tej samej stronie. Aby uzyskać więcej informacji, zobacz <xref:blazor/components/lifecycle>.

> [!WARNING]
> Przykłady w tej sekcji działają tylko wtedy, gdy serwer nie ma włączonej obsługi przed renderowaniem. Po włączeniu obsługi przed renderowaniem zostanie wygenerowany błąd wyjaśniający, że nie można wystawić wywołań międzyoperacyjnych języka JavaScript, ponieważ składnik jest wstępnie renderowany.
>
> Wyłącz renderowanie lub Dodaj dodatkowy kod, aby współpracował z renderowaniem. Aby dowiedzieć się więcej na temat pisania kodu, który działa z renderowaniem, zobacz sekcję [Obsługa przed renderowaniem](#handle-prerendering) .

### <a name="handle-the-loading-state"></a>Obsłuż stan ładowania

Ponieważ dostęp do magazynu przeglądarki odbywa się asynchronicznie za pośrednictwem połączenia sieciowego, zawsze istnieje okres, po upływie którego dane są ładowane i dostępne dla składnika. Aby uzyskać najlepsze wyniki, renderowanie komunikatu o stanie ładowania podczas ładowania jest w toku zamiast wyświetlania danych pustych lub domyślnych.

Jednym z rozwiązań jest śledzenie, czy dane są `null` , co oznacza, że dane są nadal ładowane. W `Counter` składniku domyślnym liczba jest przechowywana w `int` . [Wprowadź `currentCount` wartość null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) , dodając znak zapytania ( `?` ) do typu ( `int` ):

```csharp
private int? currentCount;
```

Zamiast bezwarunkowo wyświetlać liczbę i **`Increment`** przycisk, Wyświetl te elementy tylko wtedy, gdy dane są ładowane przez sprawdzenie <xref:System.Nullable%601.HasValue%2A> :

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Obsługa przed renderowaniem

Podczas renderowania:

* Połączenie interaktywne z przeglądarką użytkownika nie istnieje.
* Przeglądarka nie zawiera jeszcze strony, w której można uruchomić kod JavaScript.

`localStorage` lub `sessionStorage` nie są dostępne podczas renderowania. Jeśli składnik próbuje współdziałać z magazynem, zostanie wygenerowany błąd z wyjaśnieniem, że nie można wystawić wywołań międzyoperacyjnych języka JavaScript, ponieważ składnik jest wstępnie renderowany.

Jednym ze sposobów na rozwiązanie błędu jest wyłączenie renderowania. Jest to zazwyczaj najlepszym wyborem, jeśli aplikacja znacznie korzysta z magazynu opartego na przeglądarce. Renderowanie zwiększa złożoność i nie korzysta z aplikacji, ponieważ aplikacja nie może przeprowadzić renderowania żadnej przydatnej zawartości do momentu, gdy nie `localStorage` `sessionStorage` jest dostępna.

Aby wyłączyć renderowanie, Otwórz `Pages/_Host.cshtml` plik i Zmień `render-mode` atrybut [pomocnika znacznika składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) , aby <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

Renderowanie może być przydatne w przypadku innych stron, które nie używają `localStorage` ani `sessionStorage` . Aby zachować renderowanie, odłóż operację ładowania do momentu podłączenia przeglądarki do obwodu. Poniżej przedstawiono przykład przechowywania wartości licznika:

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int currentCount;
    private bool isConnected;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        var result = await ProtectedLocalStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Wyrównać zachowywanie stanu do wspólnej lokalizacji

Jeśli wiele składników korzysta z magazynu opartego na przeglądarce, ponowne implementowanie kodu dostawcy stanu wiele razy powoduje utworzenie duplikacji kodu. Jedną z opcji unikania duplikowania kodu jest utworzenie *składnika nadrzędnego dostawcy stanu* , który hermetyzuje logikę dostawcy stanu. Składniki podrzędne mogą współpracować z trwałymi danymi bez względu na mechanizm trwałości stanu.

W poniższym przykładzie `CounterStateProvider` składnika dane licznika są utrwalane `sessionStorage` :

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
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
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        var result = await ProtectedSessionStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

`CounterStateProvider`Składnik obsługuje fazę ładowania, przez co nie renderuje jej zawartości podrzędnej do momentu ukończenia ładowania.

Aby użyć `CounterStateProvider` składnika, zawiń wystąpienie składnika wokół dowolnego innego składnika, który wymaga dostępu do stanu licznika. Aby zapewnić dostępność stanu dla wszystkich składników w aplikacji, zawiń `CounterStateProvider` składnik wokół składnika <xref:Microsoft.AspNetCore.Components.Routing.Router> w `App` składniku ( `App.razor` ):

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Zapakowane składniki są odbierane i mogą modyfikować stan trwałych liczników. Poniższy `Counter` składnik implementuje wzorzec:

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

Poprzedni składnik nie jest wymagany do współpracy z programem `ProtectedBrowserStorage` ani nie zajmuje się fazą "Ładowanie".

Aby można było zaradzić sobie z instrukcją prerenderingu zgodnie z wcześniejszym opisem, `CounterStateProvider` może zostać zmieniona, aby wszystkie składniki korzystające z danych licznika automatycznie działały z użyciem prerenderowania. Aby uzyskać więcej informacji, zobacz sekcję [Obsługa przed renderowaniem](#handle-prerendering) .

Ogólnie rzecz biorąc, zalecany jest wzorzec *składnika nadrzędnego dostawcy stanu* :

* Aby korzystać z stanu w wielu składnikach.
* Jeśli istnieje tylko jeden obiekt stanu najwyższego poziomu, który ma być trwały.

Aby zachować wiele różnych obiektów stanu i korzystać z różnych podzbiorów obiektów w różnych miejscach, lepiej jest unikać utrwalania stanu w całości.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a>Eksperymentalny pakiet NuGet magazynu chronionej przeglądarki

ASP.NET Core magazynu chronionej przeglądarki wykorzystuje [ochronę danych ASP.NET Core](xref:security/data-protection/introduction) dla [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) i [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage` jest nieobsługiwanym, eksperymentalnym pakietem nieodpowiedninym do użycia w środowisku produkcyjnym.
>
> Pakiet jest dostępny tylko do użytku w aplikacjach ASP.NET Core 3,1 Blazor Server .

### <a name="configuration"></a>Konfigurowanie

1. Dodaj odwołanie do pakietu do [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) .
1. W `Pages/_Host.cshtml` pliku Dodaj następujący skrypt wewnątrz tagu zamykającego `</body>` :

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. W programie `Startup.ConfigureServices` Wywołaj `AddProtectedBrowserStorage` `localStorage` kolekcję usługi i Dodaj do `sessionStorage` niej usługi:

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Zapisz i Załaduj dane w składniku

W dowolnym składniku wymagającym ładowania lub zapisywania danych w magazynie przeglądarki Użyj [`@inject`](xref:mvc/views/razor#inject) dyrektywy, aby wstrzyknąć wystąpienie jednego z następujących elementów:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

Wybór zależy od lokalizacji magazynu przeglądarki, która ma być używana. W poniższym przykładzie `sessionStorage` jest używany:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

`@using`Instrukcja może zostać umieszczona w `_Imports.razor` pliku, a nie w składniku. Użycie `_Imports.razor` pliku sprawia, że przestrzeń nazw jest dostępna dla większych segmentów aplikacji lub całej aplikacji.

Aby zachować `currentCount` wartość w `Counter` składniku aplikacji na podstawie Blazor Server szablonu projektu, zmodyfikuj `IncrementCount` metodę, która ma być używana `ProtectedSessionStore.SetAsync` :

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

W większych, bardziej realistycznych aplikacjach przechowywanie pojedynczych pól jest mało prawdopodobne. Aplikacje są bardziej podobne do przechowywania całych obiektów modelu, które zawierają złożone Stany. `ProtectedSessionStore` automatycznie serializować i deserializacji danych JSON.

W poprzednim przykładzie kodu `currentCount` dane są przechowywane jako `sessionStorage['count']` w przeglądarce użytkownika. Dane nie są przechowywane w postaci zwykłego tekstu, ale nie są chronione przy użyciu ASP.NET Core ochrony danych. Zaszyfrowane dane można sprawdzić, jeśli `sessionStorage['count']` są oceniane w konsoli dewelopera w przeglądarce.

Aby odzyskać `currentCount` dane, jeśli użytkownik powróci do `Counter` składnika później, w tym, jeśli znajdują się w całkowicie nowym obwodzie, użyj `ProtectedSessionStore.GetAsync` :

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

Jeśli parametry składnika obejmują stan nawigacji, wywołaj `ProtectedSessionStore.GetAsync` i przypisz wynik w <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> , nie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> . <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> jest wywoływana tylko raz podczas pierwszego wystąpienia składnika. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> nie zostanie wywołana ponownie później, jeśli użytkownik przejdzie do innego adresu URL, a pozostałe na tej samej stronie. Aby uzyskać więcej informacji, zobacz <xref:blazor/components/lifecycle>.

> [!WARNING]
> Przykłady w tej sekcji działają tylko wtedy, gdy serwer nie ma włączonej obsługi przed renderowaniem. Po włączeniu obsługi przed renderowaniem zostanie wygenerowany błąd wyjaśniający, że nie można wystawić wywołań międzyoperacyjnych języka JavaScript, ponieważ składnik jest wstępnie renderowany.
>
> Wyłącz renderowanie lub Dodaj dodatkowy kod, aby współpracował z renderowaniem. Aby dowiedzieć się więcej na temat pisania kodu, który działa z renderowaniem, zobacz sekcję [Obsługa przed renderowaniem](#handle-prerendering) .

### <a name="handle-the-loading-state"></a>Obsłuż stan ładowania

Ponieważ dostęp do magazynu przeglądarki odbywa się asynchronicznie za pośrednictwem połączenia sieciowego, zawsze istnieje okres, po upływie którego dane są ładowane i dostępne dla składnika. Aby uzyskać najlepsze wyniki, renderowanie komunikatu o stanie ładowania podczas ładowania jest w toku zamiast wyświetlania danych pustych lub domyślnych.

Jednym z rozwiązań jest śledzenie, czy dane są `null` , co oznacza, że dane są nadal ładowane. W `Counter` składniku domyślnym liczba jest przechowywana w `int` . [Wprowadź `currentCount` wartość null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) , dodając znak zapytania ( `?` ) do typu ( `int` ):

```csharp
private int? currentCount;
```

Zamiast bezwarunkowo wyświetlić liczbę i **`Increment`** przycisk, wybierz, aby wyświetlić te elementy tylko wtedy, gdy dane są ładowane:

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Obsługa przed renderowaniem

Podczas renderowania:

* Połączenie interaktywne z przeglądarką użytkownika nie istnieje.
* Przeglądarka nie zawiera jeszcze strony, w której można uruchomić kod JavaScript.

`localStorage` lub `sessionStorage` nie są dostępne podczas renderowania. Jeśli składnik próbuje współdziałać z magazynem, zostanie wygenerowany błąd z wyjaśnieniem, że nie można wystawić wywołań międzyoperacyjnych języka JavaScript, ponieważ składnik jest wstępnie renderowany.

Jednym ze sposobów na rozwiązanie błędu jest wyłączenie renderowania. Jest to zazwyczaj najlepszym wyborem, jeśli aplikacja znacznie korzysta z magazynu opartego na przeglądarce. Renderowanie zwiększa złożoność i nie korzysta z aplikacji, ponieważ aplikacja nie może przeprowadzić renderowania żadnej przydatnej zawartości do momentu, gdy nie `localStorage` `sessionStorage` jest dostępna.

Aby wyłączyć renderowanie, Otwórz `Pages/_Host.cshtml` plik i Zmień `render-mode` atrybut [pomocnika znacznika składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) , aby <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

Renderowanie może być przydatne w przypadku innych stron, które nie używają `localStorage` ani `sessionStorage` . Aby zachować renderowanie, odłóż operację ładowania do momentu podłączenia przeglądarki do obwodu. Poniżej przedstawiono przykład przechowywania wartości licznika:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("count");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Wyrównać zachowywanie stanu do wspólnej lokalizacji

Jeśli wiele składników korzysta z magazynu opartego na przeglądarce, ponowne implementowanie kodu dostawcy stanu wiele razy powoduje utworzenie duplikacji kodu. Jedną z opcji unikania duplikowania kodu jest utworzenie *składnika nadrzędnego dostawcy stanu* , który hermetyzuje logikę dostawcy stanu. Składniki podrzędne mogą współpracować z trwałymi danymi bez względu na mechanizm trwałości stanu.

W poniższym przykładzie `CounterStateProvider` składnika dane licznika są utrwalane `sessionStorage` :

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
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
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

`CounterStateProvider`Składnik obsługuje fazę ładowania, przez co nie renderuje jej zawartości podrzędnej do momentu ukończenia ładowania.

Aby użyć `CounterStateProvider` składnika, zawiń wystąpienie składnika wokół dowolnego innego składnika, który wymaga dostępu do stanu licznika. Aby zapewnić dostępność stanu dla wszystkich składników w aplikacji, zawiń `CounterStateProvider` składnik wokół składnika <xref:Microsoft.AspNetCore.Components.Routing.Router> w `App` składniku ( `App.razor` ):

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Zapakowane składniki są odbierane i mogą modyfikować stan trwałych liczników. Poniższy `Counter` składnik implementuje wzorzec:

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

Poprzedni składnik nie jest wymagany do współpracy z programem `ProtectedBrowserStorage` ani nie zajmuje się fazą "Ładowanie".

Aby można było zaradzić sobie z instrukcją prerenderingu zgodnie z wcześniejszym opisem, `CounterStateProvider` może zostać zmieniona, aby wszystkie składniki korzystające z danych licznika automatycznie działały z użyciem prerenderowania. Aby uzyskać więcej informacji, zobacz sekcję [Obsługa przed renderowaniem](#handle-prerendering) .

Ogólnie rzecz biorąc, zalecany jest wzorzec *składnika nadrzędnego dostawcy stanu* :

* Aby korzystać z stanu w wielu składnikach.
* Jeśli istnieje tylko jeden obiekt stanu najwyższego poziomu, który ma być trwały.

Aby zachować wiele różnych obiektów stanu i korzystać z różnych podzbiorów obiektów w różnych miejscach, lepiej jest unikać utrwalania stanu w całości.

::: moniker-end

<h2 id="in-memory-state-container-service-server">Usługa kontenera stanu w pamięci</h2>

[!INCLUDE[](~/includes/blazor-state-management/state-container.md)]

::: zone-end
