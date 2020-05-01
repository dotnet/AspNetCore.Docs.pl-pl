---
title: Hostowanie i wdrażanie Blazor ASP.NET Core webassembly
author: guardrex
description: Dowiedz się, jak hostować Blazor i wdrażać aplikację przy użyciu ASP.NET Core, sieci dostarczania zawartości (CDN), serwerów plików i stron usługi GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: 2472fd499128a8807b76a3cc031d466140e180f5
ms.sourcegitcommit: 23243f6d6a3100303802e4310b0634860cc0b268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82619372"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a>Hostowanie i wdrażanie ASP.NET Core Blazor webassembly

[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams)i [Safia Abdalla](https://safia.rocks)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Z [modelem hostingu Blazor webassembly](xref:blazor/hosting-models#blazor-webassembly):

* Aplikacja Blazor, jej zależności i środowisko uruchomieniowe platformy .NET są pobierane równolegle do przeglądarki.
* Aplikacja jest wykonywana bezpośrednio w wątku interfejsu użytkownika przeglądarki.

Obsługiwane są następujące strategie wdrażania:

* Aplikacja Blazor jest obsługiwana przez aplikację ASP.NET Core. Ta strategia jest objęta [wdrożeniem hostowanym za pomocą ASP.NET Core](#hosted-deployment-with-aspnet-core) sekcji.
* Aplikacja Blazor jest umieszczana na statycznym, hostingowym serwerze sieci Web lub usłudze, w której program .NET nie jest używany do obsługi aplikacji Blazor. Ta strategia została omówiona w sekcji [wdrażanie autonomiczne](#standalone-deployment) , która obejmuje informacje dotyczące hostingu aplikacji Blazor webassembly jako aplikacji PODRZĘDNEJ usług IIS.

## <a name="brotli-precompression"></a>Precompression Brotli

Po opublikowaniu aplikacji Blazor webassembly dane wyjściowe są dekompresowane przy użyciu [algorytmu kompresji Brotli](https://tools.ietf.org/html/rfc7932) na najwyższym poziomie w celu zmniejszenia rozmiaru aplikacji i usunięcia potrzeby kompresji w czasie wykonywania.

Aby poznać konfigurację kompresji *Web. config* usług IIS, zobacz sekcję [rekompresji usług IIS: Brotli i gzip](#brotli-and-gzip-compression) .

## <a name="rewrite-urls-for-correct-routing"></a>Ponownie Napisz adresy URL pod kątem prawidłowego routingu

Żądania routingu dla składników strony w aplikacji Blazor webassembly nie są tak proste jak żądania routingu na serwerze Blazor, hostowanej aplikacji. Rozważmy aplikację webassembly Blazor z dwoma składnikami:

* *Główny. Razor* &ndash; ładuje się w katalogu głównym aplikacji i zawiera link do `About` składnika (`href="About"`).
* *Informacje o* &ndash; `About` składniku Razor.

Gdy zażądano dokumentu domyślnego aplikacji przy użyciu paska adresu przeglądarki (na przykład `https://www.contoso.com/`):

1. Przeglądarka wykonuje żądanie.
1. Zostanie zwrócona strona domyślna, która jest zwykle *index. html*.
1. *index. html* Bootstrap aplikację.
1. Ładowanie routera Blazor, a składnik Razor `Main` jest renderowany.

Na stronie głównej `About` wybranie linku do składnika działa na kliencie, ponieważ router Blazor zatrzyma w przeglądarce żądanie połączenia z Internetem `www.contoso.com` `About` i obsługuje wyrenderowany `About` składnik. Wszystkie żądania dotyczące wewnętrznych punktów końcowych *w aplikacji Blazor webassembly* działają w taki sam sposób: żądania nie wyzwalają żądań przeglądarki do zasobów hostowanych przez serwer w Internecie. Router obsługuje wewnętrznie żądania.

Żądanie kończy się niepowodzeniem `www.contoso.com/About`, jeśli żądanie zostanie wykonane przy użyciu paska adresu przeglądarki. Ten zasób nie istnieje na hoście internetowym aplikacji, więc zwracana jest odpowiedź *404 — nie znaleziono* .

Ponieważ przeglądarki wysyłają żądania do hostów internetowych dla stron po stronie klienta, serwery sieci Web i usługi hostingu muszą ponownie zapisywać wszystkie żądania dotyczące zasobów, które nie znajdują się fizycznie na serwerze, na stronie *index. html* . Po zwróceniu elementu *index. html* router Blazor aplikacji przejmuje i reaguje na prawidłowy zasób.

Podczas wdrażania na serwerze IIS można użyć modułu ponownego zapisywania adresu URL z opublikowanym plikiem *Web. config* aplikacji. Aby uzyskać więcej informacji, zobacz sekcję [usług IIS](#iis) .

## <a name="hosted-deployment-with-aspnet-core"></a>Hostowane wdrożenie z ASP.NET Core

*Wdrożenie hostowane* służy do obsługi aplikacji Blazor webassembly w przeglądarkach z poziomu [aplikacji ASP.NET Core](xref:index) działającej na serwerze sieci Web.

Aplikacja webassembly Blazor klienta jest publikowana w folderze */bin/Release/{Target Framework}/Publish/wwwroot* aplikacji serwerowej wraz ze wszystkimi innymi statycznymi zasobami sieci Web aplikacji serwera. Te dwie aplikacje są wdrażane razem. Wymagany jest serwer sieci Web, który umożliwia hostowanie aplikacji ASP.NET Core. W przypadku wdrożenia hostowanego program Visual Studio zawiera szablon projektu **aplikacji Webassembly Blazor** (`blazorwasm` szablon używany przez polecenie [dotnet New](/dotnet/core/tools/dotnet-new) ) z wybraną opcją **hostowaną** (`-ho|--hosted` przy użyciu `dotnet new` polecenia).

Aby uzyskać więcej informacji na temat ASP.NET Core hostingu i wdrażania aplikacji <xref:host-and-deploy/index>, zobacz.

Aby uzyskać informacje na temat wdrażania do Azure App Service <xref:tutorials/publish-to-azure-webapp-using-vs>, zobacz.

## <a name="standalone-deployment"></a>Wdrożenie autonomiczne

*Wdrożenie autonomiczne* obsługuje aplikację webassembly Blazor jako zestaw plików statycznych, które są żądane bezpośrednio przez klientów. Każdy statyczny serwer plików jest w stanie obsłużyć aplikację Blazor.

Zasoby wdrażania autonomicznego są publikowane w folderze */bin/Release/{Target Framework}/Publish/wwwroot* .

### <a name="iis"></a>IIS

Usługi IIS to obsługujący statyczny serwer plików dla aplikacji Blazor. Aby skonfigurować usługi IIS do hostowania Blazor, zobacz [Tworzenie statycznej witryny sieci Web w usługach IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Opublikowane zasoby są tworzone w folderze */bin/Release/{Target Framework}/Publish* . Hostowanie zawartości folderu *publikowania* na serwerze sieci Web lub w usłudze hostingu.

#### <a name="webconfig"></a>plik Web. config

Po opublikowaniu projektu Blazor zostanie utworzony plik *Web. config* z następującą konfiguracją usług IIS:

* Typy MIME są ustawiane dla następujących rozszerzeń plików:
  * *. dll* &ndash;`application/octet-stream`
  * *. JSON* &ndash;`application/json`
  * *. wasm* &ndash;`application/wasm`
  * *. WOFF* &ndash;`application/font-woff`
  * *. woff2* &ndash;`application/font-woff`
* Kompresja HTTP jest włączona dla następujących typów MIME:
  * `application/octet-stream`
  * `application/wasm`
* Reguły modułu ponownego zapisywania adresu URL zostały ustanowione:
  * Obsługuj podkatalog, w którym znajdują się zasoby statyczne aplikacji (*wwwroot/{ŻĄDANA ścieżka}*).
  * Utwórz Routing awaryjny SPA, aby żądania dotyczące zasobów nienależących do pliku zostały przekierowane do domyślnego dokumentu aplikacji w folderze zasobów statycznych (*wwwroot/index.html*).
  
#### <a name="use-a-custom-webconfig"></a>Użyj niestandardowego pliku Web. config

Aby użyć niestandardowego pliku *Web. config* , umieść niestandardowy plik *Web. config* w katalogu głównym folderu projektu i Opublikuj projekt.

#### <a name="install-the-url-rewrite-module"></a>Zainstaluj moduł ponownego zapisywania adresów URL

[Moduł ponownego zapisywania adresu URL](https://www.iis.net/downloads/microsoft/url-rewrite) jest wymagany do ponownego zapisywania adresów URL. Moduł nie jest instalowany domyślnie i nie jest dostępny do zainstalowania jako funkcja usługi roli Serwer sieci Web (IIS). Moduł musi zostać pobrany z witryny sieci Web usług IIS. Zainstaluj moduł przy użyciu Instalatora platformy sieci Web:

1. Lokalnie przejdź do [strony pobierania modułu ponowne zapisywanie adresów URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). W przypadku wersji angielskiej wybierz pozycję **Instalatora WebPI** , aby pobrać Instalatora Instalatora WebPI. W przypadku innych języków wybierz odpowiednią architekturę dla serwera (x86/x64), aby pobrać Instalatora.
1. Skopiuj Instalatora na serwer. Uruchom instalatora. Wybierz przycisk **Zainstaluj** i zaakceptuj postanowienia licencyjne. Po zakończeniu instalacji nie jest wymagane ponowne uruchomienie serwera.

#### <a name="configure-the-website"></a>Skonfiguruj witrynę sieci Web

Ustaw **ścieżkę fizyczną** witryny sieci Web do folderu aplikacji. Folder zawiera:

* Plik *Web. config* , za pomocą którego usługi IIS konfigurują witrynę sieci Web, w tym wymagane reguły przekierowań i typy zawartości plików.
* Folder elementu zawartości statycznej aplikacji.

#### <a name="host-as-an-iis-sub-app"></a>Host jako podrzędną aplikację usług IIS

Jeśli aplikacja autonomiczna jest hostowana jako podaplikacja usług IIS, wykonaj jedną z następujących czynności:

* Wyłącz procedurę obsługi ASP.NET Core dziedziczonego modułu.

  Usuń program obsługi w opublikowanym pliku *Web. config* aplikacji Blazor przez dodanie `<handlers>` sekcji do pliku:

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* Wyłącz dziedziczenie `<system.webServer>` sekcji głównej (nadrzędnej) aplikacji przy użyciu `<location>` elementu z ustawioną opcją: `inheritInChildApplications` `false`

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

Usuwanie procedury obsługi lub wyłączanie dziedziczenia jest wykonywane poza [konfiguracją ścieżki podstawowej aplikacji](xref:host-and-deploy/blazor/index#app-base-path). Ustaw ścieżkę bazową aplikacji w pliku *index. html* aplikacji na alias IIS używany podczas konfigurowania aplikacji podrzędnej w usługach IIS.

#### <a name="brotli-and-gzip-compression"></a>Brotli i Kompresja gzip

Usługi IIS można konfigurować za pomocą *pliku Web. config* w celu obsłużenia Brotli lub skompresowanych zasobów Blazor. Aby zapoznać się z przykładową konfiguracją, zobacz [plik Web. config](webassembly/_samples/web.config?raw=true).

#### <a name="troubleshooting"></a>Rozwiązywanie problemów

W przypadku odebrania *500 — wewnętrzny błąd serwera* , a Menedżer usług IIS zgłasza błędy przy próbie uzyskania dostępu do konfiguracji witryny sieci Web, upewnij się, że zainstalowano moduł ponownego zapisywania adresu URL. Gdy moduł nie jest zainstalowany, nie można przeanalizować pliku *Web. config* przez usługi IIS. Zapobiega to załadowaniu przez Menedżera usług IIS konfiguracji witryny sieci Web i witryny sieci Web do obsługi plików statycznych Blazor.

Aby uzyskać więcej informacji na temat rozwiązywania problemów z <xref:test/troubleshoot-azure-iis>wdrożeniami w usługach IIS, zobacz.

### <a name="azure-storage"></a>Azure Storage

Hosting pliku statycznego [usługi Azure Storage](/azure/storage/) umożliwia hosting aplikacji bezserwerowych Blazor. Obsługiwane są niestandardowe nazwy domen, usługa Azure Content Delivery Network (CDN) i protokół HTTPS.

Gdy usługa BLOB jest włączona dla hostingu statycznej witryny sieci Web na koncie magazynu:

* Ustaw **nazwę dokumentu indeksu** na `index.html`.
* Ustaw ścieżkę do `index.html` **dokumentu błędu** . Składniki Razor i inne punkty końcowe inne niż pliki nie znajdują się w ścieżkach fizycznych w zawartości statycznej przechowywanej przez usługę BLOB. Po otrzymaniu żądania dla jednego z tych zasobów, który powinien zostać obsłużony przez router Blazor, błąd *404-nie znaleziono* przez usługę BLOB Service kieruje żądanie do **ścieżki dokumentu błędu**. Zwracany jest obiekt BLOB *index. html* , a router Blazor ładuje i przetwarza ścieżkę.

Aby uzyskać więcej informacji, zobacz [Obsługa statycznej witryny sieci Web w usłudze Azure Storage](/azure/storage/blobs/storage-blob-static-website).

### <a name="nginx"></a>Nginx

Następujący plik *Nginx. conf* został uproszczony, aby pokazać, jak skonfigurować Nginx do wysyłania pliku *index. html* za każdym razem, gdy nie można znaleźć odpowiedniego pliku na dysku.

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

Aby uzyskać więcej informacji na temat konfiguracji serwera sieci Web w środowisku produkcyjnym, zobacz [Tworzenie plików konfiguracji Nginx Plus i Nginx](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).

### <a name="nginx-in-docker"></a>Nginx w Docker

Aby hostować Blazor w platformie Docker przy użyciu Nginx, skonfiguruj pliku dockerfile do korzystania z obrazu Nginx opartego na Alpine. Zaktualizuj pliku dockerfile, aby skopiować plik *Nginx. config* do kontenera.

Dodaj jeden wiersz do pliku dockerfile, jak pokazano w następującym przykładzie:

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

Aby wdrożyć aplikację webassembly Blazor w programie CentOS 7 lub nowszym:

1. Utwórz plik konfiguracji Apache. Poniższy przykład to uproszczony plik konfiguracji (*blazorapp. config*):

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. Umieść plik konfiguracji Apache w `/etc/httpd/conf.d/` katalogu, który jest domyślnym katalogiem konfiguracji Apache w CentOS 7.

1. Umieść pliki aplikacji w `/var/www/blazorapp` katalogu (lokalizacja określona `DocumentRoot` w pliku konfiguracyjnym).

1. Uruchom ponownie usługę Apache.

Aby uzyskać więcej informacji, zobacz [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) i [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).

### <a name="github-pages"></a>Strony serwisu GitHub

Aby obsłużyć ponowne zapisywanie adresów URL, Dodaj plik *404. html* ze skryptem, który obsługuje przekierowywanie żądania do strony *index. html* . Aby zapoznać się z przykładową implementacją dostarczoną przez społeczność, zobacz [aplikacje jednostronicowe dla stron usługi GitHub](https://spa-github-pages.rafrex.com/) ([rafrex/Spa-GitHub-Pages w witrynie GitHub](https://github.com/rafrex/spa-github-pages#readme)). Przykład użycia podejścia społecznościowego można znaleźć[w witrynie](https://blazor-demo.github.io/) [GitHub (blazor — Demonstracja/blazor-Demonstracja](https://github.com/blazor-demo/blazor-demo.github.io) ).

W przypadku korzystania z witryny projektu zamiast witryny organizacji Dodaj lub zaktualizuj `<base>` tag w *pliku index. html*. Ustaw wartość `href` atrybutu na nazwę repozytorium GitHub z końcowym ukośnikiem (na przykład `my-repository/`.

## <a name="host-configuration-values"></a>Wartości konfiguracji hosta

[Blazor aplikacje webassembly](xref:blazor/hosting-models#blazor-webassembly) mogą akceptować następujące wartości konfiguracji hosta jako argumenty wiersza polecenia w czasie wykonywania w środowisku programistycznym.

### <a name="content-root"></a>Katalog główny zawartości

`--contentroot` Argument ustawia ścieżkę bezwzględną do katalogu, który zawiera pliki zawartości aplikacji ([katalog główny zawartości](xref:fundamentals/index#content-root)). W poniższych przykładach `/content-root-path` jest ścieżką katalogu głównego zawartości aplikacji.

* Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia. W katalogu aplikacji wykonaj następujące polecenie:

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* Dodaj wpis do pliku *profilu launchsettings. JSON* aplikacji w profilu **IIS Express** . To ustawienie jest używane, gdy aplikacja jest uruchamiana z debugerem programu Visual Studio i z wiersza polecenia `dotnet run`z.

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* W programie Visual Studio Określ argument w **właściwościach** > **Debuguj** > **argumenty aplikacji**. Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do pliku *profilu launchsettings. JSON* .

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Baza ścieżki

`--pathbase` Argument ustawia ścieżkę bazową aplikacji dla aplikacji uruchamianej lokalnie z niegłówną względną ścieżką URL ( `<base>` tag `href` jest ustawiony na ścieżkę inną niż `/` w przypadku przemieszczania i produkcji). W poniższych przykładach `/relative-URL-path` jest podstawą ścieżki aplikacji. Aby uzyskać więcej informacji, zobacz [Ścieżka podstawowa aplikacji](xref:host-and-deploy/blazor/index#app-base-path).

> [!IMPORTANT]
> W przeciwieństwie do ścieżki przekazanej `href` do `<base>` tagu, nie dodawaj końcowego ukośnika (`/`) podczas przekazywania wartości `--pathbase` argumentu. Jeśli ścieżka podstawowa aplikacji jest podana w `<base>` tagu jako `<base href="/CoolApp/">` (zawiera końcowy ukośnik), należy przekazać wartość argumentu wiersza polecenia jako `--pathbase=/CoolApp` (bez ukośnika na końcu).

* Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia. W katalogu aplikacji wykonaj następujące polecenie:

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* Dodaj wpis do pliku *profilu launchsettings. JSON* aplikacji w profilu **IIS Express** . To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i z wiersza polecenia w programie `dotnet run`.

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* W programie Visual Studio Określ argument w **właściwościach** > **Debuguj** > **argumenty aplikacji**. Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do pliku *profilu launchsettings. JSON* .

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>Adresy URL

`--urls` Argument ustawia adresy IP lub adresy hosta z portami i protokołami, aby nasłuchiwać żądań.

* Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia. W katalogu aplikacji wykonaj następujące polecenie:

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* Dodaj wpis do pliku *profilu launchsettings. JSON* aplikacji w profilu **IIS Express** . To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i z wiersza polecenia w programie `dotnet run`.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* W programie Visual Studio Określ argument w **właściwościach** > **Debuguj** > **argumenty aplikacji**. Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do pliku *profilu launchsettings. JSON* .

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>Konfigurowanie konsolidatora

Blazor wykonuje konsolidację języka pośredniego (IL) dla każdej kompilacji wydania, aby usunąć niepotrzebny kod IL z zestawów wyjściowych. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/blazor/configure-linker>.

## <a name="custom-boot-resource-loading"></a>Ładowanie niestandardowego zasobu rozruchowego

Aplikację webassembly Blazor można zainicjować przy użyciu `loadBootResource` funkcji w celu zastąpienia wbudowanego mechanizmu ładowania zasobów rozruchowego. Należy `loadBootResource` używać w następujących scenariuszach:

* Zezwalaj użytkownikom na ładowanie zasobów statycznych, takich jak dane strefy czasowej lub *dotnet. wasm* z sieci CDN.
* Załaduj skompresowane zestawy za pomocą żądania HTTP i zdekompresuj je na kliencie dla hostów, które nie obsługują pobierania skompresowanej zawartości z serwera.
* Aliasowanie zasobów do innej nazwy przez przekierowanie każdego `fetch` żądania do nowej nazwy.

`loadBootResource`Parametry znajdują się w poniższej tabeli.

| Parametr    | Opis |
| ------------ | ----------- |
| `type`       | Typ zasobu. Typy Permissable: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`,`timezonedata` |
| `name`       | Nazwa zasobu. |
| `defaultUri` | Względny lub bezwzględny identyfikator URI zasobu. |
| `integrity`  | Ciąg integralności reprezentujący oczekiwaną zawartość w odpowiedzi. |

`loadBootResource`zwraca jedną z następujących wartości, aby zastąpić proces ładowania:

* Ciąg identyfikatora URI. W poniższym przykładzie (*wwwroot/index.html*) następujące pliki są obsługiwane z sieci CDN w `https://my-awesome-cdn.com/`:

  * *dotnet. \*. js*
  * *dotnet. wasm*
  * Dane strefy czasowej

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* `Promise<Response>`. Przekaż `integrity` parametr w nagłówku, aby zachować domyślne zachowanie sprawdzania integralności.

  Poniższy przykład (*wwwroot/index.html*) dodaje niestandardowy nagłówek HTTP do żądań wychodzących i przekazuje `integrity` parametr do `fetch` wywołania:
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* `null`/`undefined`, które powoduje domyślne zachowanie podczas ładowania.

Źródła zewnętrzne muszą zwracać wymagane nagłówki CORS dla przeglądarek, aby umożliwić ładowanie zasobów między źródłami. Sieci CDN zwykle domyślnie udostępnia wymagane nagłówki.

Wystarczy określić typy zachowań niestandardowych. Typy, które nie `loadBootResource` zostały określone do, są ładowane przez platformę na ich domyślne zachowania ładowania.
