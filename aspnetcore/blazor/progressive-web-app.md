---
title: Twórz progresywne aplikacje sieci Web za pomocą ASP.NET Core Blazor WebAssembly
author: guardrex
description: Dowiedz się, jak utworzyć Blazor progresywną aplikację sieci Web (PWA), która korzysta z nowoczesnych funkcji przeglądarki, aby zachować takie działanie jak aplikacja klasyczna.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/11/2020
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
uid: blazor/progressive-web-app
ms.openlocfilehash: 1706d3502dc68f1c25e0c35ba8f5dd44b55ce690
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658654"
---
# <a name="build-progressive-web-applications-with-aspnet-core-no-locblazor-webassembly"></a>Twórz progresywne aplikacje sieci Web za pomocą ASP.NET Core Blazor WebAssembly

[Steve Sanderson](https://github.com/SteveSandersonMS)

Progresywna aplikacja sieci Web (PWA) jest zazwyczaj aplikacją jednostronicową, która używa nowoczesnych interfejsów API i funkcji w celu zachowania aplikacji klasycznych. Blazor WebAssembly to oparta na standardach platforma aplikacji sieci Web po stronie klienta, która może korzystać z dowolnego interfejsu API przeglądarki, w tym interfejsów API programu PWA, wymaganych w przypadku następujących możliwości:

* Praca w trybie offline i ładowanie błyskawiczne niezależnie od szybkości sieci.
* Działa w osobnym oknie aplikacji, a nie tylko w oknie przeglądarki.
* Jest uruchamiany z menu Start systemu operacyjnego hosta, zadokowanego lub ekranu głównego.
* Otrzymywanie powiadomień wypychanych z serwera wewnętrznej bazy danych, nawet jeśli użytkownik nie korzysta z aplikacji.
* Automatyczna aktualizacja w tle.

Słowo *progresywne* służy do opisywania takich aplikacji, ponieważ:

* Użytkownik może najpierw odnaleźć i korzystać z aplikacji w swojej przeglądarce internetowej, podobnie jak inne SPA.
* Później użytkownik postępuje nad instalowaniem go w systemie operacyjnym i włączaniem powiadomień wypychanych.

## <a name="create-a-project-from-the-pwa-template"></a>Tworzenie projektu na podstawie szablonu PWA

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Gdy tworzysz nową **Blazor WebAssembly aplikację** w oknie dialogowym **Tworzenie nowego projektu** , zaznacz pole wyboru **aplikacja sieci Web progresywna** :

![W oknie dialogowym Nowy projekt programu Visual Studio jest zaznaczone pole wyboru "progresywna aplikacja sieci Web".](progressive-web-app/_static/image1.png)

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

-->

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code/interfejs wiersza polecenia platformy .NET Core](#tab/visual-studio-code+netcore-cli)

Użyj następującego polecenia, aby utworzyć projekt PWA w powłoce poleceń z `--pwa` przełącznikiem:

```dotnetcli
dotnet new blazorwasm -o MyBlazorPwa --pwa
```

W poprzednim poleceniu `-o|--output` opcja powoduje utworzenie nowego folderu dla aplikacji o nazwie `MyBlazorPwa` .

---

Opcjonalnie można skonfigurować PWA dla aplikacji utworzonej przy użyciu szablonu hostowanego ASP.NET Core. Scenariusz PWA jest niezależny od modelu hostingu.

## <a name="convert-an-existing-no-locblazor-webassembly-app-into-a-pwa"></a>Konwertowanie istniejącej Blazor WebAssembly aplikacji do PWA

Przekonwertuj istniejącą Blazor WebAssembly aplikację do PWA zgodnie ze wskazówkami w tej sekcji.

W pliku projektu aplikacji:

* Dodaj następującą `ServiceWorkerAssetsManifest` Właściwość do `PropertyGroup` :

  ```xml
    ...
    <ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
  </PropertyGroup>
   ```

* Dodaj następujący `ServiceWorker` element do `ItemGroup` :

  ```xml
  <ItemGroup>
    <ServiceWorker Include="wwwroot\service-worker.js" 
      PublishedContent="wwwroot\service-worker.published.js" />
  </ItemGroup>
  ```

Aby uzyskać statyczne zasoby, należy użyć **jednej** z następujących metod:

::: moniker range=">= aspnetcore-5.0"

* Utwórz oddzielny, nowy projekt PWA przy użyciu [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenia w powłoce poleceń:

  ```dotnetcli
  dotnet new blazorwasm -o MyBlazorPwa --pwa
  ```
  
  W poprzednim poleceniu `-o|--output` opcja powoduje utworzenie nowego folderu dla aplikacji o nazwie `MyBlazorPwa` .
  
  Jeśli nie chcesz konwertować aplikacji dla najnowszej wersji, przekaż tę `-f|--framework` opcję. W poniższym przykładzie jest tworzona aplikacja dla ASP.NET Core w wersji 3,1:
  
  ```dotnetcli
  dotnet new blazorwasm -o MyBlazorPwa --pwa -f netcoreapp3.1
  ```

* Przejdź do repozytorium ASP.NET Core GitHub pod następującym adresem URL, który łączy się ze źródłem i zasobami odwołania do 5,0. Jeśli nie przekonwertujesz aplikacji na wersję 5,0, wybierz wersję, z którą pracujesz, z listy rozwijanej **Przełącz gałęzie lub Tagi** , która ma zastosowanie do Twojej aplikacji.

  [Blazor WebAssemblyfolder szablonu projektu dotnet/aspnetcore (wersja 5,0 `wwwroot` )](https://github.com/dotnet/aspnetcore/tree/release/5.0/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/wwwroot)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Utwórz oddzielny, nowy projekt PWA za pomocą [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenia w powłoce poleceń. Przekaż `-f|--framework` opcję, aby wybrać wersję. W poniższym przykładzie jest tworzona aplikacja dla ASP.NET Core w wersji 3,1:
  
  ```dotnetcli
  dotnet new blazorwasm -o MyBlazorPwa --pwa -f netcoreapp3.1
  ```
  
  W poprzednim poleceniu `-o|--output` opcja powoduje utworzenie nowego folderu dla aplikacji o nazwie `MyBlazorPwa` .

* Przejdź do repozytorium ASP.NET Core GitHub pod następującym adresem URL, który zawiera linki do źródła i zasobów odwołania do 3,1:

  [Blazor WebAssemblyfolder szablonu projektu dotnet/aspnetcore (wersja 3,1 `wwwroot` )](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/ProjectTemplates/ComponentsWebAssembly.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/wwwroot)

  > [!NOTE]
  > Adres URL Blazor WebAssembly szablonu projektu został zmieniony po wydaniu ASP.NET Core 3,1. Zasoby referencyjne dla 5,0 lub nowszych są dostępne pod następującym adresem URL:
  >
  > [Blazor WebAssemblyfolder szablonu projektu dotnet/aspnetcore (wersja 5,0 `wwwroot` )](https://github.com/dotnet/aspnetcore/tree/release/5.0/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/wwwroot)

::: moniker-end

Z folderu źródłowego `wwwroot` w aplikacji utworzonej lub z zasobów referencyjnych w `dotnet/aspnetcore` repozytorium GitHub Skopiuj następujące pliki do `wwwroot` folderu aplikacji:

* `icon-512.png`
* `manifest.json`
* `service-worker.js`
* `service-worker.published.js`

W `wwwroot/index.html` pliku aplikacji:

* Dodaj `<link>` elementy dla ikony manifestu i aplikacji:

  ```html
  <link href="manifest.json" rel="manifest" />
  <link rel="apple-touch-icon" sizes="512x512" href="icon-512.png" />
  ```

* Dodaj następujący `<script>` tag w tagu zamykającym `</body>` bezpośrednio po `blazor.webassembly.js` tagu skryptu:

  ```html
      ...
      <script>navigator.serviceWorker.register('service-worker.js');</script>
  </body>
  ```

## <a name="installation-and-app-manifest"></a>Manifest instalacji i aplikacji

Podczas odwiedzania aplikacji utworzonej przy użyciu szablonu PWA użytkownicy mają możliwość instalowania aplikacji w menu Start, zadokowanym lub na ekranie głównym systemu operacyjnego. Sposób przedstawiania tej opcji zależy od przeglądarki użytkownika. W przypadku korzystania z przeglądarek programu Desktop w formacie chromu, takich jak Edge lub Chrome, na pasku adresu URL pojawia się przycisk **Dodaj** . Gdy użytkownik wybierze przycisk **Dodaj** , pojawi się okno dialogowe potwierdzenia:

![Okno dialogowe potwierdzenia w usłudze Google Chrome prezentuje przycisk Zainstaluj użytkownikowi dla aplikacji "my::: No-Loc (Blazor):::P wa".](progressive-web-app/_static/image2.png)

W systemie iOS osoby odwiedzające mogą zainstalować program PWA przy użyciu przycisku **udostępniania** Safari i jego opcji **Dodaj do homescreen** . W programie Chrome dla systemu Android użytkownicy powinni wybrać przycisk **menu** w prawym górnym rogu, a następnie **dodać do ekranu głównego**.

Po zainstalowaniu aplikacja zostanie wyświetlona w osobnym oknie bez paska adresu:

![Aplikacja "my::: No-Loc (Blazor):::P wa jest uruchamiana w przeglądarce Google Chrome bez paska adresu.](progressive-web-app/_static/image3.png)

Aby dostosować tytuł okna, schemat kolorów, ikonę lub inne szczegóły, zobacz `manifest.json` plik w `wwwroot` katalogu projektu. Schemat tego pliku jest definiowany przez standardy sieci Web. Aby uzyskać więcej informacji, zobacz [powiadomienia MDN Web docs: manifest aplikacji sieci Web](https://developer.mozilla.org/docs/Web/Manifest).

## <a name="offline-support"></a>Obsługa offline

Domyślnie aplikacje utworzone przy użyciu opcji szablonu PWA obsługują uruchamianie w trybie offline. Użytkownik musi najpierw odwiedzić aplikację, gdy jest w trybie online. Przeglądarka automatycznie pobiera i buforuje wszystkie zasoby wymagane do działania w trybie offline.

> [!IMPORTANT]
> Wsparcie programistyczne może kolidować z typowym cyklem programistycznym wprowadzania zmian i testowania. W związku z tym obsługa w trybie offline jest włączona tylko dla *opublikowanych* aplikacji. 

> [!WARNING]
> Jeśli zamierzasz rozpowszechnić aplikację PWA z obsługą trybu offline, istnieje [kilka ważnych ostrzeżeń i](#caveats-for-offline-pwas)ich zamiar. Te scenariusze są związane z PWAs w trybie offline i nie są specyficzne dla programu Blazor . Pamiętaj, aby przeczytać i zrozumieć te zastrzeżenia przed wprowadzeniem założeń dotyczących sposobu działania aplikacji z obsługą trybu offline.

Aby zobaczyć, jak działa pomoc techniczna w trybie offline:

1. Opublikuj aplikację. Aby uzyskać więcej informacji, zobacz <xref:blazor/host-and-deploy/index#publish-the-app>.
1. Wdróż aplikację na serwerze obsługującym protokół HTTPS i uzyskaj dostęp do aplikacji w przeglądarce przy użyciu bezpiecznego adresu HTTPS.
1. Otwórz narzędzia deweloperskie przeglądarki i sprawdź, czy *proces roboczy usługi* jest zarejestrowany dla hosta na karcie **aplikacja** :

   ![Karta "aplikacja" narzędzia deweloperskiego dla deweloperów Google Chrome pokazuje, że proces roboczy usługi został aktywowany i uruchomiony.](progressive-web-app/_static/image4.png)

1. Załaduj ponownie stronę i sprawdź kartę **Sieć** . **proces roboczy usługi** lub pamięć **podręczna pamięci** są wyświetlane jako źródła dla wszystkich zasobów strony:

   ![Karta Sieć w narzędziu deweloperskim firmy Google Chrome przedstawia źródła dla wszystkich elementów zawartości strony.](progressive-web-app/_static/image5.png)

1. Aby sprawdzić, czy przeglądarka nie jest zależna od dostępu do sieci w celu załadowania aplikacji, należy wykonać jedną z:

   * Zamknij serwer sieci Web i zobacz, jak aplikacja nadal działa normalnie, co obejmuje ponowne ładowanie stron. Podobnie aplikacja nadal działa normalnie, gdy istnieje wolne połączenie sieciowe.
   * Poinstruuj przeglądarkę, aby zasymulować tryb offline na karcie **Sieć** :

   ![Karta "Sieć" programu Google Chrome Developer Tools z menu rozwijanego trybu przeglądarki zmienia się z "online" na "offline".](progressive-web-app/_static/image6.png)

Obsługa w trybie offline przy użyciu procesu roboczego usługi jest standardem internetowym, który nie jest specyficzny dla programu Blazor . Aby uzyskać więcej informacji na temat procesów roboczych usługi, zobacz [powiadomienia MDN Web docs: interfejs API procesu roboczego usługi](https://developer.mozilla.org/docs/Web/API/Service_Worker_API). Aby dowiedzieć się więcej o typowych wzorcach użycia dla pracowników usług, zobacz [Google Web: cykl życia procesu roboczego usługi](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle).

Blazorszablon aplikacji PWA tworzy dwa pliki procesów roboczych usługi:

* `wwwroot/service-worker.js`, który jest używany podczas opracowywania.
* `wwwroot/service-worker.published.js`, który jest używany po opublikowaniu aplikacji.

Aby współużytkować logikę między dwoma plikami roboczymi usługi, należy wziąć pod uwagę następujące podejście:

* Dodaj trzeci plik JavaScript do przechowywania typowej logiki.
* Służy [`self.importScripts`](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) do ładowania wspólnej logiki do obu plików procesów roboczych usługi.

### <a name="cache-first-fetch-strategy"></a>Strategia pobierania w pamięci podręcznej

Wbudowany `service-worker.published.js` proces roboczy usługi rozpoznaje żądania przy użyciu strategii *pierwszej pamięci podręcznej* . Oznacza to, że pracownik usługi woli zwrócić zawartość buforowaną, bez względu na to, czy użytkownik ma dostęp do sieci czy na serwerze jest dostępna nowsza zawartość.

Strategia pierwszej pamięci podręcznej jest cenna, ponieważ:

* **Zapewnia niezawodność.** Dostęp do sieci nie jest stanem logicznym. Użytkownik nie jest po prostu w trybie online lub offline:

  * Urządzenie użytkownika może założyć, że jest w trybie online, ale sieć może być zbyt mała, aby można było oczekiwać.
  * Sieć może zwrócić nieprawidłowe wyniki dla niektórych adresów URL, na przykład gdy istnieje Portal sieci Wi-Fi, który aktualnie blokuje lub przekierowuje określone żądania.
  
  Jest to dlatego, że `navigator.onLine` interfejs API przeglądarki nie jest niezawodny i nie powinien być zależny od tego, czy nie.

* **Zapewnia to poprawność.** Podczas kompilowania pamięci podręcznej zasobów w trybie offline, proces roboczy usługi używa tworzenia skrótów zawartości w celu zagwarantowania, że pobrano kompletną i spójną migawkę zasobów w jednym czasie. Ta pamięć podręczna jest następnie używana jako jednostka niepodzielna. Nie ma żadnego punktu, aby uzyskać więcej zasobów w sieci, ponieważ jedyne wymagane wersje to te, które są już w pamięci podręcznej. Wszelkie inne zagrożenia niespójnością i niezgodnością (na przykład próba użycia wersji zestawów .NET, które nie zostały skompilowane razem).

### <a name="background-updates"></a>Aktualizacje w tle

Jako model psychiczny Możesz pomyśleć o tym, że w trybie offline — w przypadku aplikacji mobilnej, która może być zainstalowana. Aplikacja zostanie uruchomiona natychmiast niezależnie od łączności sieciowej, ale zainstalowana logika aplikacji pochodzi z migawki do punktu w czasie, która może nie być najnowszą wersją.

BlazorSzablon PWA tworzy aplikacje, które automatycznie próbują zaktualizować się w tle zawsze, gdy użytkownik odwiedza i ma działające połączenie sieciowe. Oto jak to działa:

* Podczas kompilacji projekt generuje *manifest zasobów roboczych usługi*. Domyślnie jest to nazywane `service-worker-assets.js` . Manifest zawiera listę wszystkich zasobów statycznych wymaganych przez aplikację do działania w trybie offline, takich jak zestawy .NET, pliki JavaScript i CSS, w tym ich skróty zawartości. Lista zasobów jest załadowana przez proces roboczy usługi, aby uzyskać informację o tym, które zasoby mają być buforowane.
* Za każdym razem, gdy użytkownik odwiedzi aplikację, ponowne żądania przeglądarki `service-worker.js` i `service-worker-assets.js` w tle. Pliki są porównywane bajtowo z istniejącym zainstalowanym pracownikiem usługi. Jeśli serwer zwróci zmianę zawartości dla dowolnego z tych plików, proces roboczy usługi próbuje zainstalować nową wersję programu.
* Podczas instalowania nowej wersji, proces roboczy usługi tworzy nową, oddzielną pamięć podręczną dla zasobów w trybie offline i rozpoczyna zapełnianie pamięci podręcznej zasobami wymienionymi w temacie `service-worker-assets.js` . Ta logika jest implementowana w `onInstall` funkcji wewnątrz `service-worker.published.js` .
* Proces zostanie zakończony pomyślnie, gdy wszystkie zasoby są ładowane bez błędu, a wszystkie skróty zawartości są zgodne. Jeśli to się powiedzie, nowy proces roboczy usługi przechodzi w stan *oczekiwania na aktywację* . Gdy tylko użytkownik zamknie aplikację (żadne pozostałe karty lub okna aplikacji), nowy proces roboczy usługi zostanie *uaktywniony* i będzie używany do kolejnych odwiedzin aplikacji. Stary proces roboczy usługi i jego pamięć podręczna są usuwane.
* Jeśli proces nie zakończy się pomyślnie, nowe wystąpienie procesu roboczego usługi zostanie odrzucone. Proces aktualizacji jest podejmowany ponownie na następnym odwiedzeniu użytkownika, gdy miejmy nadzieję klient ma lepsze połączenie sieciowe, które może zakończyć żądania.

Dostosuj ten proces, edytując logikę procesu roboczego usługi. Żadne z powyższych zachowań nie jest specyficzne dla programu Blazor , ale jest tylko domyślnym środowiskoem udostępnionym przez opcję szablonu PWA. Aby uzyskać więcej informacji, zobacz [powiadomienia MDN Web docs: interfejs API procesu roboczego usługi](https://developer.mozilla.org/docs/Web/API/Service_Worker_API).

### <a name="how-requests-are-resolved"></a>Jak są rozwiązywane żądania

Zgodnie z opisem w sekcji [Strategia pobierania pamięci podręcznej](#cache-first-fetch-strategy) , domyślny proces roboczy usługi korzysta z strategii dotyczącej *pamięci podręcznej* , co oznacza, że próbuje obsłużć zawartość buforowaną, jeśli jest dostępna. W przypadku braku zawartości przechowywanej w pamięci podręcznej dla określonego adresu URL, na przykład podczas żądania danych z interfejsu API zaplecza, proces roboczy usługi powraca do zwykłego żądania sieci. Żądanie sieciowe powiedzie się, jeśli serwer jest osiągalny. Ta logika jest implementowana wewnątrz `onFetch` funkcji w ramach programu `service-worker.published.js` .

Jeśli Razor składniki aplikacji korzystają z żądania danych z interfejsów API zaplecza i chcesz zapewnić przyjazne środowisko użytkownika dla żądań zakończonych niepowodzeniem ze względu na niedostępność sieci, zaimplementuj logikę w składnikach aplikacji. Na przykład użyj `try/catch` dookoła <xref:System.Net.Http.HttpClient> żądań.

### <a name="support-server-rendered-pages"></a>Obsługa stron renderowanych na serwerze

Zastanów się, co się stanie, gdy użytkownik po raz pierwszy przejdzie do adresu URL, takiego jak `/counter` lub dowolnego innego linku bezpośredniego w aplikacji. W takich przypadkach nie chcesz zwracać zawartości w pamięci podręcznej jako `/counter` , ale potrzebna jest przeglądarka do załadowania zawartości w pamięci podręcznej w `/index.html` celu uruchomienia Blazor WebAssembly aplikacji. Te początkowe żądania są znane jako żądania *nawigacji* , w przeciwieństwie do:

* `subresource` żądania obrazów, arkuszy stylów lub innych plików.
* `fetch/XHR` żądania dotyczące danych interfejsu API.

Domyślny proces roboczy usługi zawiera logikę przypadków specjalnych dla żądań nawigacji. Proces roboczy usługi rozwiązuje żądania, zwracając zawartość z pamięci podręcznej dla `/index.html` , niezależnie od żądanego adresu URL. Ta logika jest implementowana w `onFetch` funkcji wewnątrz `service-worker.published.js` .

Jeśli aplikacja ma określone adresy URL, które muszą zwracać kod HTML renderowany przez serwer, a nie `/index.html` z pamięci podręcznej, należy edytować logikę w procesie roboczym usługi. Jeśli wszystkie adresy URL zawierające `/Identity/` muszą być obsługiwane jako zwykłe żądania tylko online do serwera, należy zmodyfikować `service-worker.published.js` `onFetch` logikę. Znajdź następujący kod:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate';
```

Zmień kod na następujący:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
  && !event.request.url.includes('/Identity/');
```

W przeciwnym razie niezależnie od łączności sieciowej proces roboczy usługi przechwytuje żądania dotyczące takich adresów URL i rozwiązuje je za pomocą polecenia `/index.html` .

Dodaj dodatkowe punkty końcowe dla zewnętrznych dostawców uwierzytelniania do sprawdzenia. W poniższym przykładzie w `/signin-google` przypadku uwierzytelniania Google do sprawdzenia zostanie dodany:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
  && !event.request.url.includes('/Identity/')
  && !event.request.url.includes('/signin-google');
```

Nie jest wymagana żadna akcja dla środowiska programistycznego, w którym zawartość jest zawsze pobierana z sieci.

### <a name="control-asset-caching"></a>Sterowanie buforowaniem zasobów

Jeśli projekt definiuje Właściwość programu `ServiceWorkerAssetsManifest` MSBuild, Blazor narzędzia kompilacji generują manifest zasobów procesów roboczych usługi o określonej nazwie. Domyślny szablon PWA tworzy plik projektu zawierający następującą właściwość:

```xml
<ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
```

Plik zostanie umieszczony w `wwwroot` katalogu wyjściowym, dzięki czemu przeglądarka może pobrać ten plik przez żądanie `/service-worker-assets.js` . Aby wyświetlić zawartość tego pliku, Otwórz `/bin/Debug/{TARGET FRAMEWORK}/wwwroot/service-worker-assets.js` w edytorze tekstu. Jednak nie należy edytować pliku, ponieważ jest on ponownie generowany dla każdej kompilacji.

Domyślnie ten manifest zawiera następujące listę:

* Wszelkie Blazor zarządzane zasoby, takie jak zestawy .NET i pliki środowiska uruchomieniowego Webassembly programu .NET, są wymagane do działania w trybie offline.
* Wszystkie zasoby do publikowania w `wwwroot` katalogu aplikacji, takie jak obrazy, arkusze stylów i pliki JavaScript, w tym statyczne zasoby sieci Web dostarczane przez zewnętrzne projekty i pakiety NuGet.

Można kontrolować, które z tych zasobów są pobierane i buforowane przez proces roboczy usługi przez edytowanie logiki w `onInstall` programie w ramach programu `service-worker.published.js` . Domyślnie proces roboczy usługi pobiera i buforuje pliki zgodne z typowymi rozszerzeniami nazw sieci Web, takimi jak `.html` ,, `.css` `.js` , i oraz `.wasm` typami plików specyficznymi dla Blazor WebAssembly ( `.dll` , `.pdb` ).

Aby uwzględnić dodatkowe zasoby, które nie znajdują się w `wwwroot` katalogu aplikacji, Zdefiniuj dodatkowe `ItemGroup` wpisy programu MSBuild, jak pokazano w następującym przykładzie:

```xml
<ItemGroup>
  <ServiceWorkerAssetsManifestItem Include="MyDirectory\AnotherFile.json"
    RelativePath="MyDirectory\AnotherFile.json" AssetUrl="files/AnotherFile.json" />
</ItemGroup>
```

`AssetUrl`Metadane określają podstawowy adres URL, który powinien być używany przez przeglądarkę podczas pobierania zasobu do pamięci podręcznej. Może to być niezależna od oryginalnej nazwy pliku źródłowego na dysku.

> [!IMPORTANT]
> Dodanie elementu `ServiceWorkerAssetsManifestItem` nie powoduje opublikowania pliku w `wwwroot` katalogu aplikacji. Publikowanie danych wyjściowych musi być kontrolowane osobno. `ServiceWorkerAssetsManifestItem`Powoduje to jedynie wprowadzenie dodatkowego wpisu w manifeście zasobów procesu roboczego usługi.

## <a name="push-notifications"></a>Powiadomienia wypychane

Podobnie jak w przypadku innych aplikacji PWA, Blazor WebAssembly PWA może odbierać powiadomienia wypychane z serwera wewnętrznej bazy danych. Serwer może wysyłać powiadomienia wypychane w dowolnym momencie, nawet jeśli użytkownik nie korzysta aktywnie z aplikacji. Na przykład powiadomienia wypychane mogą być wysyłane, gdy inny użytkownik wykonuje odpowiednią akcję.

Mechanizm wysyłania powiadomień wypychanych jest całkowicie niezależny od Blazor WebAssembly , ponieważ jest zaimplementowany przez serwer wewnętrznej bazy danych, który może korzystać z dowolnej technologii. Jeśli chcesz wysyłać powiadomienia wypychane z serwera ASP.NET Core, rozważ [użycie techniki podobnej do podejścia wykonywanego w warsztatie niezwykle Pizza](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications).

Mechanizm otrzymywania i wyświetlania powiadomień wypychanych na kliencie jest również niezależny od Blazor WebAssembly , ponieważ jest zaimplementowany w pliku JavaScript procesu roboczego usługi. Aby zapoznać się z przykładem, zapoznaj [się z podejściem używanym w warsztatie niezwykle Pizza](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications).

## <a name="caveats-for-offline-pwas"></a>Ostrzeżenia dotyczące PWAs w trybie offline

Nie wszystkie aplikacje powinny próbować obsługiwać użycie w trybie offline. Obsługa offline zwiększa znaczącą złożoność, ale nie zawsze jest istotna dla wymaganych przypadków użycia.

Obsługa w trybie offline jest zwykle istotna:

* Jeśli podstawowy magazyn danych jest lokalny dla przeglądarki. Na przykład podejście jest istotne w aplikacji z interfejsem użytkownika dla urządzenia [IoT](https://en.wikipedia.org/wiki/Internet_of_things) , które przechowuje dane w programie `localStorage` lub [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API).
* Jeśli aplikacja wykonuje znaczną ilość pracy, aby pobrać i buforować dane interfejsu API zaplecza odpowiednie dla każdego użytkownika, aby mogły nawigować przez dane w trybie offline. Jeśli aplikacja musi obsługiwać edytowanie, należy skompilować system do śledzenia zmian i synchronizowania danych z zapleczem.
* Jeśli celem jest zagwarantowanie, że aplikacja zostanie załadowana natychmiast niezależnie od warunków sieci. Zaimplementuj odpowiednie środowisko użytkownika wokół żądań interfejsu API zaplecza, aby pokazać postęp żądań i zachowywać się bezproblemowo, gdy żądania nie powiodą się z powodu niedostępności sieci.

Ponadto PWAs z obsługą trybu offline muszą odnosić się do szeregu dodatkowych komplikacji. Deweloperzy powinni uważnie zaznajomić się z zastrzeżeniami w poniższych sekcjach.

### <a name="offline-support-only-when-published"></a>Obsługa offline tylko po opublikowaniu

Podczas opracowywania zwykle chcesz zobaczyć, że wszystkie zmiany zostaną odzwierciedlone bezpośrednio w przeglądarce bez przechodzenia przez proces aktualizacji w tle. W związku z tym Blazor szablon PWA umożliwia obsługę offline tylko po opublikowaniu.

Podczas kompilowania aplikacji z obsługą trybu offline nie wystarczy przetestować aplikacji w środowisku deweloperskim. Aby zrozumieć, jak reagują na inne warunki sieci, należy przetestować aplikację w stanie opublikowanym.

### <a name="update-completion-after-user-navigation-away-from-app"></a>Aktualizowanie uzupełniania po nawigacji użytkownika z aplikacji

Aktualizacje nie zostaną ukończone, dopóki użytkownik nie wyjdzie z aplikacji na wszystkich kartach. Zgodnie z opisem w sekcji [aktualizacje w tle](#background-updates) po wdrożeniu aktualizacji aplikacji przeglądarka pobiera zaktualizowane pliki procesów roboczych usługi, aby rozpocząć proces aktualizacji.

Co się stało z wieloma deweloperami, nawet po zakończeniu tej aktualizacji **nie zacznie obowiązywać** , dopóki użytkownik nie przejdzie na wszystkie karty. Odświeżenie karty wyświetlającej aplikację **nie** jest wystarczające, nawet jeśli jest to jedyna karta wyświetlająca aplikację. Dopóki aplikacja nie zostanie całkowicie ZAMKNIĘTA, nowy proces roboczy usługi pozostaje w stanie *oczekiwania na aktywację* . **Nie jest to specyficzne dla Blazor , ale raczej jest standardowe zachowanie platformy sieci Web.**

Jest to często spowodowane przez deweloperów, którzy próbują przetestować aktualizacje do swoich procesów roboczych lub zasobów w pamięci podręcznej w trybie offline. W przypadku zaewidencjonowania narzędzi deweloperskich w przeglądarce można zobaczyć podobne do następujących:

![Karta Google Chrome "aplikacja" pokazuje, że proces roboczy usługi aplikacji to "Oczekiwanie na aktywację".](progressive-web-app/_static/image7.png)

Tak długo, jak lista "klientów", które są kartami lub oknami wyświetlanymi w aplikacji, nie jest pusta, proces roboczy kontynuuje oczekiwanie. Przyczyną tego jest zagwarantowanie spójności przez pracowników. Spójność oznacza, że wszystkie zasoby są pobierane z tej samej niepodzielnej pamięci podręcznej.

Podczas testowania zmian może być przydatne kliknięcie linku "skipWaiting", jak pokazano na poprzednim zrzucie ekranu, a następnie ponowne załadowanie strony. Możesz zautomatyzować ten proces dla wszystkich użytkowników, napisanie przez siebie procesu roboczego usługi w celu [pominięcia fazy "oczekiwanie" i natychmiastowej aktywacji przy aktualizacji](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase). W przypadku pominięcia fazy oczekiwania zostanie nadana gwarancja, że zasoby są zawsze pobierane spójnie z tego samego wystąpienia pamięci podręcznej.

### <a name="users-may-run-any-historical-version-of-the-app"></a>Użytkownicy mogą uruchamiać dowolną wersję historyczną aplikacji

Deweloperzy sieci Web zwykle oczekują, że użytkownicy uruchamiają tylko najnowszą wdrożoną wersję aplikacji sieci Web, ponieważ jest ona normalna w ramach tradycyjnego modelu dystrybucji sieci Web. Jednak w trybie offline — w pierwszej kolejności program PWA jest bardziej zbliżone w natywnej aplikacji mobilnej, w której użytkownicy nie muszą uruchamiać najnowszej wersji.

Zgodnie z opisem w sekcji [aktualizacje w tle](#background-updates) , po wdrożeniu aktualizacji aplikacji, **każdy istniejący użytkownik nadal używa poprzedniej wersji dla co najmniej jednej kolejnej odwiedzania** , ponieważ aktualizacja występuje w tle i nie jest aktywowana, dopóki użytkownik nie wyjdzie dalej. Ponadto użyta Poprzednia wersja nie jest konieczna od poprzedniej wdrożonej wersji. Poprzednią wersją może być *dowolna* wersja historyczna, w zależności od tego, kiedy użytkownik ostatnio ukończył aktualizację.

Może to być problemem, jeśli składniki frontonu i zaplecza aplikacji wymagają umowy dotyczącej schematu dla żądań interfejsu API. Nie należy wdrażać zmian schematu interfejsu API bez zgodności z poprzednimi wersjami, dopóki nie będzie można upewnić się, że wszyscy użytkownicy zostali zaktualizowani. Alternatywnie Zablokuj użytkownikom możliwość korzystania ze starszych wersji aplikacji. To wymaganie jest takie samo jak w przypadku natywnych aplikacji mobilnych. W przypadku wdrożenia istotnej zmiany w interfejsach API serwera aplikacja kliencka jest uszkodzona dla użytkowników, którzy jeszcze nie zostali zaktualizowani.

Jeśli to możliwe, nie Wdrażaj istotnych zmian w interfejsach API zaplecza. Jeśli to konieczne, należy rozważyć użycie [standardowych interfejsów API procesów roboczych, takich jak ServiceWorkerRegistration](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration) , aby określić, czy aplikacja jest aktualna, a jeśli nie, aby uniknąć użycia.

### <a name="interference-with-server-rendered-pages"></a>Zakłócenia przy użyciu stron renderowanych na serwerze

Zgodnie z opisem w sekcji [strony obsługi renderowanej przez serwer](#support-server-rendered-pages) , jeśli chcesz ominąć zachowanie funkcji przez proces roboczy usługi zwracające `/index.html` zawartość dla wszystkich żądań nawigacji, Edytuj logikę w procesie roboczym usługi.

### <a name="all-service-worker-asset-manifest-contents-are-cached-by-default"></a>Zawartość manifestu wszystkich zasobów procesu roboczego usługi jest domyślnie buforowana

Zgodnie z opisem w sekcji [buforowanie zasobów kontroli](#control-asset-caching) plik `service-worker-assets.js` jest generowany podczas kompilacji i zawiera listę wszystkich zasobów, które pracownik usług powinien pobrać i buforować.

Ponieważ ta lista domyślnie zawiera wszystkie informacje, które są emitowane do `wwwroot` , w tym zawartość dostarczaną przez zewnętrzne pakiety i projekty, należy zachować ostrożność, aby nie umieścić zbyt dużej ilości zawartości. Jeśli `wwwroot` katalog zawiera miliony obrazów, proces roboczy usługi próbuje pobrać i buforować je wszystkie, zużywać nadmierną przepustowość i prawdopodobnie nie zostanie pomyślnie zakończony.

Implementowanie dowolnej logiki w celu kontrolowania, który podzbiór zawartości manifestu powinien być pobierany i buforowany przez edycję `onInstall` funkcji w `service-worker.published.js` .

### <a name="interaction-with-authentication"></a>Interakcja z uwierzytelnianiem

Szablon PWA może być używany w połączeniu z uwierzytelnianiem. PWA obsługujący tryb offline może również obsługiwać uwierzytelnianie, gdy użytkownik ma początkową łączność sieciową.

Jeśli użytkownik nie ma łączności sieciowej, nie może uwierzytelnić ani uzyskać tokenów dostępu. Domyślnie przy próbie odwiedzenia strony logowania bez dostępu do sieci następuje komunikat "błąd sieci". Musisz zaprojektować przepływ interfejsu użytkownika, który umożliwia użytkownikowi wykonywanie użytecznych zadań w trybie offline bez próby uwierzytelnienia użytkownika lub uzyskania tokenów dostępu. Alternatywnie możesz zaprojektować aplikację, aby bezpiecznie kończyć się niepowodzeniem, gdy sieć jest niedostępna. Jeśli aplikacja nie może być zaprojektowana do obsługi tych scenariuszy, możesz nie włączyć obsługi w trybie offline.

Gdy aplikacja przeznaczona do użycia w trybie online i offline jest ponownie dostępna w trybie online:

* Aplikacja może wymagać udostępnienia nowego tokenu dostępu.
* Aplikacja musi wykryć, czy inny użytkownik jest zalogowany w usłudze, aby mógł zastosować operacje do konta użytkownika, które zostały wprowadzone w trybie offline.

Aby utworzyć aplikację w usłudze PWA w trybie offline, która współdziała z uwierzytelnianiem:

* Zastąp wartość <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> fabryką, która przechowuje ostatniego zalogowanego użytkownika i używa przechowywanego użytkownika, gdy aplikacja jest w trybie offline.
* Operacje w kolejce, gdy aplikacja jest w trybie offline i stosuje je, gdy aplikacja wraca do trybu online.
* Podczas wylogowywania Wyczyść przechowywanego użytkownika.

[`CarChecker`](https://github.com/SteveSandersonMS/CarChecker)Przykładowa aplikacja pokazuje poprzednie metody. Zobacz następujące części aplikacji:

* `OfflineAccountClaimsPrincipalFactory` (`Client/Data/OfflineAccountClaimsPrincipalFactory.cs`)
* `LocalVehiclesStore` (`Client/Data/LocalVehiclesStore.cs`)
* `LoginStatus` składnik ( `Client/Shared/LoginStatus.razor` )

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Rozwiązywanie problemów z integralnością skryptu programu PowerShell](xref:blazor/host-and-deploy/webassembly#troubleshoot-integrity-powershell-script)
* [SignalR negocjowanie między źródłami na potrzeby uwierzytelniania](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
