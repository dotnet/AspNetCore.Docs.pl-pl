---
title: Tworzenie progresywnych aplikacji sieci Web Blazor za pomocą ASP.NET Core WebAssembly
author: guardrex
description: Dowiedz się, Blazorjak utworzyć opartą na bazie progresywną aplikację sieci Web (PWA), która używa nowoczesnych funkcji przeglądarki, aby zachowywać się jak aplikacja klasyczna.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/progressive-web-app
ms.openlocfilehash: fe69e51aefae9c80e5bb4b78151d384ce25d41a7
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218950"
---
# <a name="build-progressive-web-applications-with-aspnet-core-blazor-webassembly"></a>Tworzenie progresywnych aplikacji sieci Web za pomocą ASP.NET Core Blazor WebAssembly

Przez [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Progressive Web Application (PWA) to aplikacja jednostronicowa (SPA), która używa nowoczesnych interfejsów API przeglądarki i możliwości zachowywania się jak aplikacja klasyczna. Blazor WebAssembly jest opartą na standardach platformą aplikacji internetowych po stronie klienta, dzięki czemu może korzystać z dowolnego interfejsu API przeglądarki, w tym interfejsów API PWA wymaganych dla następujących możliwości:

* Praca w trybie offline i ładowanie natychmiast, niezależnie od szybkości sieci.
* Uruchamianie w swoim własnym oknie aplikacji, a nie tylko w oknie przeglądarki.
* Uruchamiany z menu startowego systemu operacyjnego hosta, stacji dokującej lub ekranu głównego.
* Odbieranie powiadomień wypychanych z serwera wewnętrznej bazy danych, nawet jeśli użytkownik nie korzysta z aplikacji.
* Automatyczne aktualizowanie w tle.

Słowo *progressive* jest używane do opisywania takich aplikacji, ponieważ:

* Użytkownik może najpierw odkryć i korzystać z aplikacji w przeglądarce internetowej, jak każdy inny SPA.
* Później użytkownik przechodzi do instalowania go w swoim os i włączania powiadomień wypychanych.

## <a name="create-a-project-from-the-pwa-template"></a>Tworzenie projektu na podstawie szablonu programu PWA

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Podczas tworzenia nowej **aplikacji Blazor WebAssembly** w oknie **dialogowym Tworzenie nowego projektu** zaznacz pole wyboru Progress Web **Application:**

![Pole wyboru "Progresywna aplikacja sieci Web" jest zaznaczone w oknie dialogowym nowego projektu programu Visual Studio.](progressive-web-app/_static/image1.png)

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

-->

# <a name="visual-studio-code--net-core-cli"></a>[Kod programu Visual Studio / .NET Core CLI](#tab/visual-studio-code+netcore-cli)

Utwórz projekt programu PWA w `--pwa` powłoce poleceń za pomocą przełącznika:

```dotnetcli
dotnet new blazorwasm -o MyNewProject --pwa
```

---

Opcjonalnie program PWA można skonfigurować dla aplikacji utworzonej na podstawie szablonu hostowanego ASP.NET rdzenia. Scenariusz programu PWA jest niezależny od modelu hostingu.

## <a name="installation-and-app-manifest"></a>Instalacja i manifest aplikacji

Podczas odwiedzania aplikacji utworzonej przy użyciu szablonu PROGRAMU PWA użytkownicy mają możliwość zainstalowania aplikacji w menu start systemu operacyjnego, stacji dokującej lub ekranie głównym. Sposób prezentacji tej opcji zależy od przeglądarki użytkownika. W przypadku korzystania z przeglądarek opartych na chromie na komputerach stacjonarnych, takich jak Edge lub Chrome, na pasku adresu URL pojawia się przycisk **Dodaj.** Po wybraniu przycisku **Dodaj** użytkownik otrzymuje okno dialogowe potwierdzenia:

![Diaglog potwierdzenia w Google Chrome przedstawia przycisk Zainstaluj dla użytkownika dla aplikacji "MyBlazorPwa".](progressive-web-app/_static/image2.png)

W przypadku systemu iOS użytkownicy mogą zainstalować program PWA za pomocą przycisku **Udostępnij** w przeglądarce Safari i opcji **Dodaj do ekranu głównego.** W Chrome dla Androida użytkownicy powinni wybrać przycisk **Menu** w prawym górnym rogu, a następnie **dodaj do ekranu głównego**.

Po zainstalowaniu aplikacja pojawia się we własnym oknie bez paska adresu:

![Aplikacja "MyBlazorPwa" działa w Google Chrome bez paska adresu.](progressive-web-app/_static/image3.png)

Aby dostosować tytuł okna, schemat kolorów, ikonę lub inne szczegóły, zobacz plik *manifest.json* w katalogu *wwwroot* projektu. Schemat tego pliku jest zdefiniowany przez standardy sieci web. Aby uzyskać więcej informacji, zobacz [dokumenty sieci Web MDN: Manifest aplikacji sieci Web](https://developer.mozilla.org/docs/Web/Manifest).

## <a name="offline-support"></a>Obsługa w trybie offline

Domyślnie aplikacje utworzone przy użyciu opcji szablonu programu PWA mają obsługę uruchamiania w trybie offline. Użytkownik musi najpierw odwiedzić aplikację, gdy jest w trybie online. Przeglądarka automatycznie pobiera i buforuje wszystkie zasoby wymagane do pracy w trybie offline.

> [!IMPORTANT]
> Wsparcie rozwoju kolidowałoby ze zwykłym cyklem rozwoju wprowadzania zmian i testowania ich. W związku z tym obsługa w trybie offline jest włączona tylko dla *opublikowanych* aplikacji. 

> [!WARNING]
> Jeśli zamierzasz rozpowszechniać program PWA z obsługą trybu offline, istnieje [kilka ważnych ostrzeżeń i zastrzeżeń.](#caveats-for-offline-pwas) Te scenariusze są związane z programem BlazorPWA w trybie offline i nie są specyficzne dla . Pamiętaj, aby przeczytać i zrozumieć te zastrzeżenia przed podjęciem założeń dotyczących działania aplikacji obsługującej tryb offline.

Aby zobaczyć, jak działa pomoc techniczna w trybie offline:

1. Opublikuj aplikację. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/blazor/index#publish-the-app>.
1. Wdrażanie aplikacji na serwerze obsługującym protokół HTTPS i uzyskiwanie dostępu do aplikacji w przeglądarce pod jej bezpiecznym adresem HTTPS.
1. Otwórz narzędzia deweloperskie przeglądarki i sprawdź, czy *pracownik serwisu* jest zarejestrowany dla hosta na karcie **Aplikacja:**

   ![Karta "Aplikacja" narzędzi programistycznych Google Chrome pokazuje, że pracownik serwisu jest aktywowany i uruchomiony.](progressive-web-app/_static/image4.png)

1. Przeładuj ponownie stronę i sprawdź kartę **Sieć.** **Pracownik serwisu** lub **pamięć podręczna** są wymienione jako źródła wszystkich zasobów strony:

   ![Karta "Sieć" narzędzi programistycznych Google Chrome z pokasamy źródeł wszystkich zasobów strony.](progressive-web-app/_static/image5.png)

1. Aby sprawdzić, czy przeglądarka nie jest zależna od dostępu do sieci, aby załadować aplikację, :

   * Zamknij serwer www i zobacz, jak aplikacja nadal działa normalnie, co obejmuje ponowne ładowanie strony. Podobnie aplikacja nadal działa normalnie, gdy istnieje wolne połączenie sieciowe.
   * Poinstruuj przeglądarkę, aby symulowała tryb offline na karcie **Sieć:**

   ![Karta "Sieć" narzędzi programistycznych Google Chrome z rozwijanym trybem przeglądarki została zmieniona z "Online" na "Offline".](progressive-web-app/_static/image6.png)

Obsługa w trybie offline przy użyciu pracownika Blazorserwisu jest standardem sieci web, niespecyficznym dla . Aby uzyskać więcej informacji na temat pracowników serwisu, zobacz [dokumenty sieci Web MDN: Interfejs API procesu roboczego usługi](https://developer.mozilla.org/docs/Web/API/Service_Worker_API). Aby dowiedzieć się więcej na temat typowych wzorców użycia dla pracowników serwisu, zobacz [Google Web: Cykl życia pracownika serwisu](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle).

Blazor's SZABLON PWA tworzy dwa pliki pracownika serwisu:

* *wwwroot/service-worker.js*, który jest używany podczas rozwoju.
* *wwwroot/service-worker.published.js*, który jest używany po opublikowaniu aplikacji.

Aby udostępnić logikę między dwoma plikami procesu roboczego usługi, należy wziąć pod uwagę następujące podejście:

* Dodaj trzeci plik JavaScript, aby pomieścić wspólną logikę.
* Użyj [self.importScripts,](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) aby załadować wspólną logikę do obu plików procesu roboczego usługi.

### <a name="cache-first-fetch-strategy"></a>Strategia pobierania pamięci podręcznej

Wbudowany proces *service-worker.published.js* rozpoznaje żądania przy użyciu strategii *pamięci podręcznej.* Oznacza to, że pracownik usługi woli zwracać zawartość buforowaną, niezależnie od tego, czy użytkownik ma dostęp do sieci, czy nowsza zawartość jest dostępna na serwerze.

Strategia pamięci podręcznej jest cenna, ponieważ:

* **Zapewnia niezawodność.** &ndash;Dostęp do sieci nie jest stanem logicznym. Użytkownik nie jest po prostu w trybie online lub offline:

  * Urządzenie użytkownika może zakładać, że jest w trybie online, ale sieć może być tak powolna, że niepraktyczne czekać.
  * Sieć może zwracać nieprawidłowe wyniki dla niektórych adresów URL, na przykład gdy istnieje wbudowany portal WIFI, który obecnie blokuje lub przekierowuje określone żądania.
  
  Dlatego `navigator.onLine` interfejs API przeglądarki nie jest niezawodny i nie powinien być uzależniony od.

* **Zapewnia poprawność.** &ndash;Podczas tworzenia pamięci podręcznej zasobów w trybie offline, pracownik usługi używa mieszania zawartości, aby zagwarantować, że pobrano pełną i samowystarczajną migawkę zasobów w jednej chwili w czasie. Ta pamięć podręczna jest następnie używana jako jednostka atomowa. Nie ma sensu pytać sieci o nowsze zasoby, ponieważ tylko wymagane wersje są już buforowane. Wszystko inne ryzyko niespójności i niezgodności (na przykład próbuje użyć wersji zestawów .NET, które nie zostały skompilowane razem).

### <a name="background-updates"></a>Aktualizacje w tle

Jako model mentalny, możesz myśleć o pierwszym w trybie offline PWA jako zachowującym się jak aplikacja mobilna, którą można zainstalować. Aplikacja uruchamia się natychmiast niezależnie od łączności sieciowej, ale logika zainstalowanej aplikacji pochodzi z migawki punktu w czasie, która może nie być najnowszą wersją.

Szablon Blazor programu PWA tworzy aplikacje, które automatycznie próbują aktualizować się w tle za każdym razem, gdy użytkownik odwiedzi i ma działające połączenie sieciowe. Sposób, w jaki to działa, jest następujący:

* Podczas kompilacji projekt generuje *manifest zasobów pracownika serwisu*. Domyślnie jest to nazywane *service-worker-assets.js*. Manifest zawiera listę wszystkich zasobów statycznych, które aplikacja wymaga do działania w trybie offline, takich jak zestawy .NET, pliki JavaScript i CSS, w tym ich skróty zawartości. Lista zasobów jest ładowana przez proces roboczy usługi, dzięki czemu wie, które zasoby do pamięci podręcznej.
* Za każdym razem, gdy użytkownik odwiedza aplikację, przeglądarka ponownie żąda *service-worker.js* i *service-worker-assets.js* w tle. Pliki są porównywane bajtami za bajt z istniejącym zainstalowanym pracownikiem serwisu. Jeśli serwer zwraca zmienioną zawartość dla jednego z tych plików, pracownik serwisu próbuje zainstalować nową wersję siebie.
* Podczas instalowania nowej wersji siebie, pracownik usługi tworzy nową, oddzielną pamięć podręczną dla zasobów w trybie offline i rozpoczyna wypełnianie pamięci podręcznej zasobami wymienionymi w *service-worker-assets.js*. Ta logika jest `onInstall` implementowana w funkcji wewnątrz *service-worker.published.js*.
* Proces kończy się pomyślnie, gdy wszystkie zasoby są ładowane bez błędów i wszystkie skróty zawartości są zgodne. W przypadku powodzenia nowy pracownik serwisu wprowadza stan *oczekiwania na aktywację.* Jak tylko użytkownik zamknie aplikację (nie ma pozostałych kart aplikacji lub okien), nowy pracownik usługi staje się *aktywny* i jest używany do kolejnych wizyt w aplikacji. Stary proces roboczy usługi i jego pamięć podręczna są usuwane.
* Jeśli proces nie zakończy się pomyślnie, nowe wystąpienie procesu roboczego usługi zostanie odrzucone. Proces aktualizacji jest podejmowana ponownie przy następnej wizycie użytkownika, gdy miejmy nadzieję, że klient ma lepsze połączenie sieciowe, które może zakończyć żądania.

Dostosuj ten proces, edytując logikę procesu roboczego serwisu. Żadne z powyższych zachowań Blazor nie jest specyficzne dla, ale jest tylko domyślne środowisko dostarczone przez opcję szablonu programu PWA. Aby uzyskać więcej informacji, zobacz [dokumenty sieci Web MDN: Interfejs API pracownika serwisu](https://developer.mozilla.org/docs/Web/API/Service_Worker_API).

### <a name="how-requests-are-resolved"></a>Jak żądania są rozwiązywane

Zgodnie z opisem w [sekcji strategii pobierania pamięci podręcznej](#cache-first-fetch-strategy) domyślny proces roboczy usługi używa strategii *pamięci podręcznej,* co oznacza, że próbuje obsługiwać zawartość buforowaną, gdy jest dostępna. Jeśli nie ma zawartości buforowanej dla określonego adresu URL, na przykład podczas żądania danych z interfejsu API wewnętrznej bazy danych, proces roboczy usługi powraca do zwykłego żądania sieciowego. Żądanie sieciowe powiedzie się, jeśli serwer jest osiągalny. Ta logika jest `onFetch` implementowana wewnątrz funkcji w *serwisie service-worker.published.js*.

Jeśli składniki Razor aplikacji polegać na żądaniu danych z interfejsów API wewnętrznej bazy danych i chcesz zapewnić przyjazne środowisko użytkownika dla żądań nie powiodło się z powodu niedostępności sieci, zaimplementuj logikę w składnikach aplikacji. Na przykład `try/catch` użyj `HttpClient` wokół żądań.

### <a name="support-server-rendered-pages"></a>Obsługa stron renderowanych na serwerze

Zastanów się, co się stanie, gdy `/counter` użytkownik po raz pierwszy przejdzie do adresu URL, takiego jak lub innego głębokiego łącza w aplikacji. W takich przypadkach nie chcesz zwracać zawartości `/counter`w pamięci podręcznej jako , ale zamiast `/index.html` tego trzeba Blazor przeglądarki, aby załadować zawartość w pamięci podręcznej, aby uruchomić aplikację WebAssembly. Te początkowe żądania są znane jako żądania *nawigacji,* w przeciwieństwie do:

* *subresource* żądań obrazów, arkuszy stylów lub innych plików.
* *żądania pobierania/XHR* dla danych INTERFEJSU API.

Domyślny proces roboczy usługi zawiera logikę specjalnego przypadku dla żądań nawigacji. Pracownik serwisu rozwiązuje żądania, zwracając buforowaną zawartość `/index.html`dla , niezależnie od żądanego adresu URL. Ta logika jest `onFetch` implementowana w funkcji wewnątrz *service-worker.published.js*.

Jeśli aplikacja ma określone adresy URL, które muszą zwracać kod HTML renderowany na serwerze, a nie służyć `/index.html` z pamięci podręcznej, należy edytować logikę w usłudze worker. Jeśli wszystkie adresy `/Identity/` URL zawierające muszą być obsługiwane jako zwykłe żądania tylko online do serwera, następnie zmodyfikuj *logikę service-worker.published.js.* `onFetch` Znajdź następujący kod:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate';
```

Zmień kod na następujący:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
    && !event.request.url.includes('/Identity/');
```

Jeśli tego nie zrobisz, niezależnie od łączności sieciowej pracownik serwisu przechwytuje żądania dotyczące takich adresów URL i rozwiązuje je za pomocą programu `/index.html`.

### <a name="control-asset-caching"></a>Buforowanie zasobów kontrolnych

Jeśli projekt definiuje `ServiceWorkerAssetsManifest` właściwość MSBuild, Blazornarzędzia kompilacji 's generuje manifest zasobów zasobu procesu roboczego usługi o określonej nazwie. Domyślny szablon programu PWA tworzy plik projektu zawierający następującą właściwość:

```xml
<ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
```

Plik jest umieszczany w katalogu wyjściowym *wwwroot,* dzięki czemu przeglądarka może pobrać ten plik, żądając `/service-worker-assets.js`. Aby wyświetlić zawartość tego pliku, otwórz */bin/Debug/{TARGET FRAMEWORK}/wwwroot/service-worker-assets.js* w edytorze tekstu. Jednak nie edytować plik, ponieważ jest generowany ponownie na każdej kompilacji.

Domyślnie ten manifest zawiera listę:

* Wszystkie Blazorzasoby zarządzane przez -, takie jak zestawy .NET i pliki środowiska uruchomieniowego .NET WebAssembly wymagane do działania w trybie offline.
* Wszystkie zasoby do publikowania w katalogu *wwwroot* aplikacji, takie jak obrazy, arkusze stylów i pliki JavaScript, w tym statyczne zasoby sieci Web dostarczane przez projekty zewnętrzne i pakiety NuGet.

Można kontrolować, które z tych zasobów są pobierane i buforowane przez `onInstall` pracownika serwisu, edytując logikę w *serwisie service-worker.published.js*. Domyślnie pracownik serwisu pobiera i buforuje pliki pasujące do typowych rozszerzeń nazwy plików internetowych, takich jak *.html*, Blazor *css*, *.js*i *.wasm*, plus typy plików specyficzne dla WebAssembly (*.dll*, *.pdb*).

Aby uwzględnić dodatkowe zasoby, które nie są obecne w katalogu *wwwroot* `ItemGroup` aplikacji, należy zdefiniować dodatkowe wpisy MSBuild, jak pokazano w poniższym przykładzie:

```xml
<ItemGroup>
  <ServiceWorkerAssetsManifestItem Include="MyDirectory\AnotherFile.json"
    RelativePath="MyDirectory\AnotherFile.json" AssetUrl="files/AnotherFile.json" />
</ItemGroup>
```

Metadane `AssetUrl` określa adres URL względny podstawowy, który przeglądarka powinna używać podczas pobierania zasobu do pamięci podręcznej. Może to być niezależne od jego oryginalnej nazwy pliku źródłowego na dysku.

> [!IMPORTANT]
> Dodanie `ServiceWorkerAssetsManifestItem` pliku nie powoduje, że plik zostanie opublikowany w katalogu *wwwroot* aplikacji. Dane wyjściowe publikacji muszą być kontrolowane oddzielnie. Tylko `ServiceWorkerAssetsManifestItem` powoduje, że dodatkowy wpis pojawia się w manifeście zasobów pracownika serwisu.

## <a name="push-notifications"></a>Powiadomienia push

Jak każdy inny pwa, Blazor WebAssembly PWA może odbierać powiadomienia wypychania z serwera wewnętrznej bazy danych. Serwer może wysyłać powiadomienia wypychane w dowolnym momencie, nawet jeśli użytkownik nie korzysta aktywnie z aplikacji. Na przykład powiadomienia wypychane mogą być wysyłane, gdy inny użytkownik wykonuje odpowiednią akcję.

Mechanizm wysyłania powiadomień wypychanych Blazor jest całkowicie niezależny od webassembly, ponieważ jest zaimplementowany przez serwer wewnętrznej bazy danych, który może korzystać z dowolnej technologii. Jeśli chcesz wysyłać powiadomienia push z ASP.NET core server, rozważ [użycie techniki podobnej do podejścia przyjętego w warsztacie Blazing Pizza.](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications)

Mechanizm odbierania i wyświetlania powiadomień wypychanych Blazor na kliencie jest również niezależny od WebAssembly, ponieważ jest zaimplementowany w pliku JavaScript pracownika usługi. Na przykład, zobacz [podejście stosowane w warsztacie Blazing Pizza](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications).

## <a name="caveats-for-offline-pwas"></a>Zastrzeżenia dla programów PWA w trybie offline

Nie wszystkie aplikacje powinny próbować obsługiwać korzystanie w trybie offline. Obsługa w trybie offline zwiększa złożoność, a jednocześnie nie zawsze jest istotne dla przypadków użycia wymagane.

Obsługa w trybie offline jest zwykle tylko odpowiednia:

* Jeśli podstawowy magazyn danych jest lokalny w przeglądarce. Na przykład podejście jest istotne w aplikacji z interfejsem użytkownika dla urządzenia `localStorage` [IoT,](https://en.wikipedia.org/wiki/Internet_of_things) które przechowuje dane w lub [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API).
* Jeśli aplikacja wykonuje znaczną ilość pracy, aby pobrać i buforować dane interfejsu API wewnętrznej bazy danych istotne dla każdego użytkownika, dzięki czemu mogą poruszać się po danych w trybie offline. Jeśli aplikacja musi obsługiwać edycję, należy sbudować system śledzenia zmian i synchronizowania danych z zapleczem.
* Jeśli celem jest zagwarantowanie, że aplikacja ładuje się natychmiast, niezależnie od warunków sieciowych. Zaimplementuj odpowiednie środowisko użytkownika wokół żądań interfejsu API wewnętrznej bazy danych, aby pokazać postęp żądań i zachowywać się bezpiecznie, gdy żądania nie powiodą się z powodu niedostępności sieci.

Ponadto usługi PWA obsługujące tryb offline muszą radzić sobie z szeregiem dodatkowych komplikacji. Deweloperzy powinni dokładnie zapoznać się z zastrzeżeniami w poniższych sekcjach.

### <a name="offline-support-only-when-published"></a>Obsługa w trybie offline tylko wtedy, gdy jest opublikowana

Podczas tworzenia zazwyczaj chcesz zobaczyć każdą zmianę odzwierciedloną natychmiast w przeglądarce bez przechodzenia przez proces aktualizacji w tle. W związku Blazorz tym szablon programu PWA 's umożliwia obsługę w trybie offline tylko wtedy, gdy opublikowane.

Podczas tworzenia aplikacji w trybie offline, nie wystarczy przetestować aplikację w środowisku deweloperskim. Należy przetestować aplikację w stanie opublikowanym, aby zrozumieć, jak reaguje na różne warunki sieciowe.

### <a name="update-completion-after-user-navigation-away-from-app"></a>Aktualizacja ukończenia po nawigacji użytkownika z dala od aplikacji

Aktualizacje nie są kompletne, dopóki użytkownik nie odsunie się od aplikacji na wszystkich kartach. Jak wyjaśniono w [sekcji Aktualizacje w tle,](#background-updates) po wdrożeniu aktualizacji do aplikacji przeglądarka pobiera zaktualizowane pliki procesu roboczego usługi, aby rozpocząć proces aktualizacji.

Co zaskakuje wielu deweloperów jest to, że nawet po zakończeniu tej aktualizacji, **nie** zostanie ona obowiązuje, dopóki użytkownik nie odsunął się we wszystkich kartach. **Nie** wystarczy odświeżyć kartę wyświetlającą aplikację, nawet jeśli jest to jedyna karta wyświetlająca aplikację. Dopóki aplikacja nie zostanie całkowicie zamknięta, nowy pracownik serwisu pozostaje w *oczekiwaniu na aktywację* stanu. **Nie jest to Blazorspecyficzne dla , ale raczej jest to standardowe zachowanie platformy internetowej.**

To często kłopoty deweloperów, którzy próbują przetestować aktualizacje do ich pracy usługi lub w trybie offline buforowane zasoby. Jeśli zaewidencjonujesz narzędzia programistyczne przeglądarki, może pojawić się coś następującego:

![Karta "Aplikacja" Google Chrome pokazuje, że pracownik serwisu aplikacji "czeka na aktywację".](progressive-web-app/_static/image7.png)

Tak długo, jak lista "klientów", które są karty lub okna wyświetlania aplikacji, jest niepusty, pracownik kontynuuje oczekiwanie. Powodem, dla którego pracownicy serwisu to robią, jest zagwarantowanie spójności. Spójność oznacza, że wszystkie zasoby są pobierane z tej samej niepodzielną pamięci podręcznej.

Podczas testowania zmian, może okazać się wygodne, aby kliknąć link "skipWaiting", jak pokazano na poprzednim zrzucie ekranu, a następnie ponownie załadować stronę. Można to zautomatyzować dla wszystkich użytkowników, kodując pracownika serwisu, aby [pominąć fazę "oczekiwania" i natychmiast aktywować przy aktualizacji](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase). Jeśli pominąć fazę oczekiwania, rezygnując z gwarancji, że zasoby są zawsze pobierane konsekwentnie z tego samego wystąpienia pamięci podręcznej.

### <a name="users-may-run-any-historical-version-of-the-app"></a>Użytkownicy mogą uruchamiać dowolną historyczną wersję aplikacji

Deweloperzy sieci Web zwykle oczekują, że użytkownicy uruchamiają tylko najnowszą wdrożoną wersję swojej aplikacji sieci web, ponieważ jest to normalne w ramach tradycyjnego modelu dystrybucji sieci Web. Jednak pwa połączona w trybie offline jest bardziej zbliżona do natywnej aplikacji mobilnej, w której użytkownicy niekoniecznie uruchamiają najnowszą wersję.

Jak wyjaśniono w [sekcji Aktualizacje w tle,](#background-updates) po wdrożeniu aktualizacji do **aplikacji, każdy istniejący użytkownik nadal używa poprzedniej wersji dla co najmniej jednej kolejnej wizyty,** ponieważ aktualizacja występuje w tle i nie jest aktywowana, dopóki użytkownik nie przejdzie dalej. Ponadto poprzednia wersja używana nie jest koniecznie poprzednia wdrożona. Poprzednia wersja może być *dowolną* wersją historyczną, w zależności od tego, kiedy użytkownik ostatnio ukończył aktualizację.

Może to być problem, jeśli frontend i wewnętrznej bazy części aplikacji wymagają porozumienia dotyczące schematu dla żądań interfejsu API. Nie należy wdrażać wstecznie niezgodnych zmian schematu interfejsu API, dopóki nie można mieć pewności, że wszyscy użytkownicy uaktualnili. Alternatywnie należy zablokować użytkownikom korzystanie z niezgodnych starszych wersji aplikacji. To wymaganie scenariusza jest taka sama jak w przypadku natywnych aplikacji mobilnych. Jeśli wdrożysz przełomową zmianę w interfejsach API serwera, aplikacja kliencka zostanie przerwana dla użytkowników, którzy nie zostały jeszcze zaktualizowane.

Jeśli to możliwe, nie należy wdrażać zmian podziału do interfejsów API wewnętrznej bazy danych. Jeśli musisz to zrobić, należy rozważyć użycie [standardowych interfejsów API service worker, takich jak ServiceWorkerRegistration,](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration) aby ustalić, czy aplikacja jest aktualna, a jeśli nie, aby zapobiec użyciu.

### <a name="interference-with-server-rendered-pages"></a>Zakłócenia ze strony renderowane na serwerze

Zgodnie z opisem w [pomocy technicznej strony renderowane strony](#support-server-rendered-pages) sekcji, jeśli chcesz `/index.html` pominąć zachowanie pracownika usługi zwracania zawartości dla wszystkich żądań nawigacji, edytować logikę w pracowniku usługi.

### <a name="all-service-worker-asset-manifest-contents-are-cached-by-default"></a>Cała zawartość manifestu zasobu zasobu zasobu procesu roboczego usługi jest domyślnie buforowana

Zgodnie z opisem w [sekcji Buforowanie zasobów kontroli,](#control-asset-caching) usługa plików *worker-assets.js* jest generowany podczas kompilacji i wyświetla listę wszystkich zasobów, które pracownik usługi powinien pobrać i pamięci podręcznej.

Ponieważ ta lista domyślnie zawiera wszystko, co jest dostarczane do *wwwroot*, w tym treści dostarczane przez zewnętrzne pakiety i projekty, należy uważać, aby nie umieścić tam zbyt wiele treści. Jeśli katalog *wwwroot* zawiera miliony obrazów, pracownik serwisu próbuje pobrać i buforować je wszystkie, zużywając nadmierną przepustowość i najprawdopodobniej nie kończąc pomyślnie.

Zaimplementuj dowolną logikę, aby kontrolować, który podzbiór zawartości `onInstall` manifestu powinien być pobierany i buforowany, edytując funkcję w *serwisie service-worker.published.js*.

### <a name="interaction-with-authentication"></a>Interakcja z uwierzytelnianiem

Możliwe jest użycie opcji szablonu programu PWA w połączeniu z opcjami uwierzytelniania. Program PWA obsługujący tryb offline może również obsługiwać uwierzytelnianie, gdy użytkownik ma łączność sieciową.

Gdy użytkownik nie ma łączności sieciowej, nie może uwierzytelnić ani uzyskać tokenów dostępu. Domyślnie próba odwiedzenia strony logowania bez dostępu do sieci powoduje wyświetlenie komunikatu "błąd sieci".

Należy zaprojektować przepływ interfejsu użytkownika, który pozwala użytkownikowi wykonywać przydatne czynności w trybie offline bez próby uwierzytelnienia lub uzyskania tokenów dostępu. Alternatywnie można zaprojektować aplikację, aby zakończyć się niepowodzeniem w sposób pełen wdzięku, gdy sieć nie jest dostępna. Jeśli nie jest to możliwe w aplikacji, możesz nie chcieć włączyć obsługi w trybie offline.
