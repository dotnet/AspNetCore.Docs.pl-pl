---
title: Hostowanie i wdrażanie ASP.NET CoreBlazor WebAssembly
author: guardrex
description: Dowiedz się, jak hostować i wdrażać Blazor aplikację przy użyciu ASP.NET Core, sieci dostarczania zawartości (CDN), serwerów plików i stron usługi GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 2a2b0dabc26c14624144ce7eceb5861fe56f1054
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445141"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a>Hostowanie i wdrażanie ASP.NET CoreBlazor WebAssembly

[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams)i [Safia Abdalla](https://safia.rocks)

Z [ Blazor WebAssembly modelem hostingu](xref:blazor/hosting-models#blazor-webassembly):

* BlazorAplikacja, jej zależności i środowisko uruchomieniowe platformy .NET są pobierane równolegle do przeglądarki.
* Aplikacja jest wykonywana bezpośrednio w wątku interfejsu użytkownika przeglądarki.

Obsługiwane są następujące strategie wdrażania:

* BlazorAplikacja jest obsługiwana przez aplikację ASP.NET Core. Ta strategia jest objęta [wdrożeniem hostowanym za pomocą ASP.NET Core](#hosted-deployment-with-aspnet-core) sekcji.
* BlazorAplikacja jest umieszczana na statycznym, hostingowym serwerze sieci Web lub usłudze, w której program .NET nie jest używany do obsługi Blazor aplikacji. Ta strategia została omówiona w sekcji [wdrażanie autonomiczne](#standalone-deployment) , która obejmuje informacje dotyczące hostingu Blazor WebAssembly aplikacji jako PODRZĘDNEJ aplikacji usług IIS.

## <a name="compression"></a>Kompresja

Po Blazor WebAssembly opublikowaniu aplikacji dane wyjściowe są kompresowane statycznie podczas publikowania, aby zmniejszyć rozmiar aplikacji i usunąć obciążenie dla kompresji w czasie wykonywania. Używane są następujące algorytmy kompresji:

* [Brotli](https://tools.ietf.org/html/rfc7932) (najwyższy poziom)
* [Gzip](https://tools.ietf.org/html/rfc1952)

Blazorkorzysta z hosta, aby zapewnić odpowiednie skompresowane pliki. W przypadku korzystania z ASP.NET Core hostowanego projektu host jest w stanie wykonywać negocjacje zawartości i obsługiwać statycznie skompresowane pliki. W przypadku udostępniania Blazor WebAssembly aplikacji autonomicznej może być wymagane dodatkowe działanie, aby zapewnić obsługę plików skompresowanych statycznie:

* Aby uzyskać `web.config` konfigurację kompresji usług IIS, zobacz sekcję [rekompresji usług IIS: Brotli i gzip](#brotli-and-gzip-compression) . 
* Podczas hostingu w rozwiązaniach hostingu statycznego, które nie obsługują negocjowanej statycznie negocjacji zawartości plików, na przykład stron usługi GitHub, należy rozważyć skonfigurowanie aplikacji do pobierania i dekodowania skompresowanych plików Brotli:

  * Odwołuje się do dekodera Brotli z [repozytorium GitHub/Brotli](https://github.com/google/brotli/) w aplikacji.
  * Zaktualizuj aplikację, aby użyć dekodera. Zmień adiustację wewnątrz tagu zamykającego `<body>` w `wwwroot/index.html` następujący sposób:
  
    ```html
    <script src="brotli.decode.min.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
          return (async function () {
            const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
            if (!response.ok) {
              throw new Error(response.statusText);
            }
            const originalResponseBuffer = await response.arrayBuffer();
            const originalResponseArray = new Int8Array(originalResponseBuffer);
            const decompressedResponseArray = BrotliDecode(originalResponseArray);
            const contentType = type === 
              'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
            return new Response(decompressedResponseArray, 
              { headers: { 'content-type': contentType } });
          })();
        }
      }
    });
    </script>
    ```
 
Aby wyłączyć kompresję, należy dodać `BlazorEnableCompression` Właściwość programu MSBuild do pliku projektu aplikacji i ustawić wartość na `false` :

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

## <a name="rewrite-urls-for-correct-routing"></a>Ponownie Napisz adresy URL pod kątem prawidłowego routingu

Żądania routingu dla składników strony w Blazor WebAssembly aplikacji nie są tak proste jak żądania routingu w Blazor Server hostowanej aplikacji. Weź pod uwagę Blazor WebAssembly aplikację z dwoma składnikami:

* `Main.razor`: Ładuje się w katalogu głównym aplikacji i zawiera link do `About` składnika ( `href="About"` ).
* `About.razor`: `About` składnik.

Gdy zażądano dokumentu domyślnego aplikacji przy użyciu paska adresu przeglądarki (na przykład `https://www.contoso.com/` ):

1. Przeglądarka wykonuje żądanie.
1. Zostanie zwrócona domyślna strona, która zwykle `index.html` .
1. `index.html`wyładowania aplikacji.
1. Blazorładuje router, a Razor `Main` składnik jest renderowany.

Na stronie głównej wybranie linku do `About` składnika działa na kliencie, ponieważ Blazor router uniemożliwia przeglądarce wykonywanie żądania w Internecie do `www.contoso.com` programu `About` i obsługuje wyrenderowany `About` składnik. Wszystkie żądania dotyczące wewnętrznych punktów końcowych *w Blazor WebAssembly aplikacji* działają w taki sam sposób: żądania nie wyzwalają żądań przeglądarki do zasobów hostowanych przez serwer w Internecie. Router obsługuje wewnętrznie żądania.

Żądanie kończy się niepowodzeniem, jeśli żądanie zostanie wykonane przy użyciu paska adresu przeglądarki `www.contoso.com/About` . Ten zasób nie istnieje na hoście internetowym aplikacji, więc zwracana jest odpowiedź *404 — nie znaleziono* .

Ponieważ przeglądarki wysyłają żądania do hostów internetowych dla stron po stronie klienta, serwery sieci Web i usługi hostingu muszą ponownie zapisywać wszystkie żądania dotyczące zasobów, które nie znajdują się fizycznie na serwerze na `index.html` stronie. Gdy `index.html` jest zwracany, Blazor router aplikacji przejmuje i reaguje na prawidłowy zasób.

Podczas wdrażania na serwerze usług IIS można użyć modułu ponownego zapisywania adresu URL z opublikowanym `web.config` plikiem aplikacji. Aby uzyskać więcej informacji, zobacz sekcję [usług IIS](#iis) .

## <a name="hosted-deployment-with-aspnet-core"></a>Hostowane wdrożenie z ASP.NET Core

*Wdrożenie hostowane* Blazor WebAssembly umożliwia aplikacji przeglądarki z poziomu [aplikacji ASP.NET Core](xref:index) działającej na serwerze sieci Web.

Aplikacja kliencka Blazor WebAssembly jest publikowana w `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folderze aplikacji serwerowej wraz z wszelkimi innymi statycznymi zasobami sieci Web aplikacji serwera. Te dwie aplikacje są wdrażane razem. Wymagany jest serwer sieci Web, który umożliwia hostowanie aplikacji ASP.NET Core. W przypadku wdrożenia hostowanego program Visual Studio zawiera szablon projektu ** Blazor WebAssembly aplikacji** ( `blazorwasm` szablon przy użyciu [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenia) z **`Hosted`** wybraną opcją ( `-ho|--hosted` przy użyciu `dotnet new` polecenia).

Aby uzyskać więcej informacji na temat ASP.NET Core hostingu i wdrażania aplikacji, zobacz <xref:host-and-deploy/index> .

Aby uzyskać informacje na temat wdrażania do Azure App Service, zobacz <xref:tutorials/publish-to-azure-webapp-using-vs> .

## <a name="standalone-deployment"></a>Wdrożenie autonomiczne

*Wdrożenie samodzielne* służy Blazor WebAssembly jako zestaw plików statycznych, które są żądane bezpośrednio przez klientów. Każdy statyczny serwer plików jest w stanie obsłużyć Blazor aplikację.

Zasoby wdrażania autonomicznego są publikowane w `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folderze.

### <a name="azure-app-service"></a>Azure App Service

Blazor WebAssemblyAplikacje można wdrażać w usłudze Azure App Services w systemie Windows, który jest hostem aplikacji w [usługach IIS](#iis).

Wdrażanie autonomicznej Blazor WebAssembly aplikacji do Azure App Service dla systemu Linux nie jest obecnie obsługiwane. Obraz serwera z systemem Linux do hostowania aplikacji nie jest obecnie dostępny. Trwa wykonywanie pracy, aby włączyć ten scenariusz.

### <a name="iis"></a>IIS

Program IIS jest obsługującym statycznego serwera plików dla Blazor aplikacji. Aby skonfigurować usługi IIS do hostowania Blazor , zobacz [Tworzenie statycznej witryny sieci Web w usługach IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Opublikowane zasoby są tworzone w `/bin/Release/{TARGET FRAMEWORK}/publish` folderze. Hostowanie zawartości `publish` folderu na serwerze sieci Web lub w usłudze hostingu.

#### <a name="webconfig"></a>web.config

Po Blazor opublikowaniu projektu `web.config` tworzony jest plik z następującą konfiguracją usług IIS:

* Typy MIME są ustawiane dla następujących rozszerzeń plików:
  * `.dll`: `application/octet-stream`
  * `.json`: `application/json`
  * `.wasm`: `application/wasm`
  * `.woff`: `application/font-woff`
  * `.woff2`: `application/font-woff`
* Kompresja HTTP jest włączona dla następujących typów MIME:
  * `application/octet-stream`
  * `application/wasm`
* Reguły modułu ponownego zapisywania adresu URL zostały ustanowione:
  * Obsługuj podkatalog, w którym znajdują się zasoby statyczne aplikacji ( `wwwroot/{PATH REQUESTED}` ).
  * Utwórz Routing awaryjny SPA, aby żądania dotyczące zasobów nienależących do pliku zostały przekierowane do domyślnego dokumentu aplikacji w folderze zasobów statycznych ( `wwwroot/index.html` ).
  
#### <a name="use-a-custom-webconfig"></a>Użyj niestandardowego web.config

Aby użyć pliku niestandardowego `web.config` , umieść `web.config` plik niestandardowy w folderze głównym folderu projektu i Opublikuj projekt.

#### <a name="install-the-url-rewrite-module"></a>Zainstaluj moduł ponownego zapisywania adresów URL

[Moduł ponownego zapisywania adresu URL](https://www.iis.net/downloads/microsoft/url-rewrite) jest wymagany do ponownego zapisywania adresów URL. Moduł nie jest instalowany domyślnie i nie jest dostępny do zainstalowania jako funkcja usługi roli Serwer sieci Web (IIS). Moduł musi zostać pobrany z witryny sieci Web usług IIS. Zainstaluj moduł przy użyciu Instalatora platformy sieci Web:

1. Lokalnie przejdź do [strony pobierania modułu ponowne zapisywanie adresów URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). W przypadku wersji angielskiej wybierz pozycję **Instalatora WebPI** , aby pobrać Instalatora Instalatora WebPI. W przypadku innych języków wybierz odpowiednią architekturę dla serwera (x86/x64), aby pobrać Instalatora.
1. Skopiuj Instalatora na serwer. Uruchom instalatora. Wybierz przycisk **Zainstaluj** i zaakceptuj postanowienia licencyjne. Po zakończeniu instalacji nie jest wymagane ponowne uruchomienie serwera.

#### <a name="configure-the-website"></a>Skonfiguruj witrynę sieci Web

Ustaw **ścieżkę fizyczną** witryny sieci Web do folderu aplikacji. Folder zawiera:

* Plik, za `web.config` pomocą którego usługi IIS konfiguruje witrynę sieci Web, w tym wymagane reguły przekierowań i typy zawartości plików.
* Folder elementu zawartości statycznej aplikacji.

#### <a name="host-as-an-iis-sub-app"></a>Host jako podrzędną aplikację usług IIS

Jeśli aplikacja autonomiczna jest hostowana jako podaplikacja usług IIS, wykonaj jedną z następujących czynności:

* Wyłącz procedurę obsługi ASP.NET Core dziedziczonego modułu.

  Usuń program obsługi w Blazor opublikowanym pliku aplikacji `web.config` , dodając `<handlers>` sekcję do pliku:

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* Wyłącz dziedziczenie sekcji głównej (nadrzędnej) aplikacji `<system.webServer>` przy użyciu `<location>` elementu z `inheritInChildApplications` ustawioną opcją `false` :

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

Usuwanie procedury obsługi lub wyłączanie dziedziczenia jest wykonywane poza [konfiguracją ścieżki podstawowej aplikacji](xref:blazor/host-and-deploy/index#app-base-path). Ustaw ścieżkę bazową aplikacji w `index.html` pliku aplikacji na alias IIS używany podczas konfigurowania aplikacji podrzędnej w usługach IIS.

#### <a name="brotli-and-gzip-compression"></a>Brotli i Kompresja gzip

Usługi IIS można skonfigurować `web.config` w taki sposób, aby obsługiwały zasoby Brotli lub Gzip skompresowane Blazor . Aby zapoznać się z przykładową konfiguracją, zobacz [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) .

#### <a name="troubleshooting"></a>Rozwiązywanie problemów

W przypadku odebrania *500 — wewnętrzny błąd serwera* , a Menedżer usług IIS zgłasza błędy przy próbie uzyskania dostępu do konfiguracji witryny sieci Web, upewnij się, że zainstalowano moduł ponownego zapisywania adresu URL. Gdy moduł nie jest zainstalowany, `web.config` nie można przeanalizować pliku przez usługi IIS. Zapobiega to załadowaniu przez Menedżera usług IIS konfiguracji witryny sieci Web i witryny sieci Web na podstawie Blazor plików statycznych.

Aby uzyskać więcej informacji na temat rozwiązywania problemów z wdrożeniami w usługach IIS, zobacz <xref:test/troubleshoot-azure-iis> .

### <a name="azure-storage"></a>Azure Storage

Hosting pliku statycznego [usługi Azure Storage](/azure/storage/) umożliwia Blazor hosting aplikacji bezserwerowych. Obsługiwane są niestandardowe nazwy domen, usługa Azure Content Delivery Network (CDN) i protokół HTTPS.

Gdy usługa BLOB jest włączona dla hostingu statycznej witryny sieci Web na koncie magazynu:

* Ustaw **nazwę dokumentu indeksu** na `index.html` .
* Ustaw **ścieżkę do dokumentu błędu** `index.html` . Razorskładniki i inne punkty końcowe inne niż pliki nie znajdują się w ścieżkach fizycznych w zawartości statycznej przechowywanej przez usługę BLOB. Po otrzymaniu żądania dla jednego z tych zasobów, który Blazor powinien zostać obsłużony przez router, błąd *404-nie znaleziono* przez usługę BLOB Service kieruje żądanie do **ścieżki dokumentu błędu**. `index.html`Obiekt BLOB jest zwracany, a Blazor router ładuje i przetwarza ścieżkę.

Jeśli pliki nie są ładowane w czasie wykonywania ze względu na nieodpowiednie typy MIME w `Content-Type` nagłówkach plików, wykonaj jedną z następujących czynności:

* Skonfiguruj narzędzia do ustawiania prawidłowych typów MIME ( `Content-Type` nagłówkami) podczas wdrażania plików.
* Zmień typy MIME ( `Content-Type` nagłówki) dla plików po wdrożeniu aplikacji.

  W Eksplorator usługi Storage (Azure Portal) dla każdego pliku:
  
  1. Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości**.
  1. Ustaw wartość **ContentType** i wybierz przycisk **Zapisz** .

Aby uzyskać więcej informacji, zobacz [Obsługa statycznej witryny sieci Web w usłudze Azure Storage](/azure/storage/blobs/storage-blob-static-website).

### <a name="nginx"></a>Nginx

Następujący `nginx.conf` plik jest uproszczony, aby pokazać, jak skonfigurować Nginx do wysyłania `index.html` pliku za każdym razem, gdy nie można znaleźć odpowiedniego pliku na dysku.

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

W przypadku ustawienia [limitu szybkości serii Nginx](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) w [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) programie Blazor WebAssembly aplikacje mogą wymagać dużej `burst` wartości parametru, aby pomieścić stosunkowo dużą liczbę żądań wykonywanych przez aplikację. Początkowo ustaw wartość na co najmniej 60:

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

Zwiększ wartość, jeśli narzędzia deweloperskie przeglądarki lub ruch sieciowy wskazuje, że żądania odbierają kod stanu *niedostępny dla usługi 503* .

Aby uzyskać więcej informacji na temat konfiguracji serwera sieci Web w środowisku produkcyjnym, zobacz [Tworzenie plików konfiguracji Nginx Plus i Nginx](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).

### <a name="nginx-in-docker"></a>Nginx w Docker

Aby hostować Blazor w Docker przy użyciu Nginx, skonfiguruj pliku dockerfile do korzystania z obrazu Nginx opartego na Alpine. Zaktualizuj pliku dockerfile, aby skopiować `nginx.config` plik do kontenera.

Dodaj jeden wiersz do pliku dockerfile, jak pokazano w następującym przykładzie:

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

Aby wdrożyć Blazor WebAssembly aplikację w programie CentOS 7 lub nowszym:

1. Utwórz plik konfiguracji Apache. Poniższy przykład to uproszczony plik konfiguracji ( `blazorapp.config` ):

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

Aby uzyskać więcej informacji, zobacz [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) i [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html) .

### <a name="github-pages"></a>Usługa GitHub Pages

Aby obsłużyć ponowne zapisywanie adresów URL, Dodaj `404.html` plik ze skryptem, który obsługuje przekierowywanie żądania do `index.html` strony. Aby zapoznać się z przykładową implementacją dostarczoną przez społeczność, zobacz [aplikacje jednostronicowe dla stron usługi GitHub](https://spa-github-pages.rafrex.com/) ([rafrex/Spa-GitHub-Pages w witrynie GitHub](https://github.com/rafrex/spa-github-pages#readme)). Przykład użycia podejścia społecznościowego można znaleźć[w witrynie](https://blazor-demo.github.io/) [GitHub (blazor — Demonstracja/blazor-Demonstracja](https://github.com/blazor-demo/blazor-demo.github.io) ).

W przypadku korzystania z witryny projektu zamiast witryny organizacji Dodaj lub zaktualizuj `<base>` tag w `index.html` . Ustaw `href` wartość atrybutu na nazwę repozytorium GitHub z końcowym ukośnikiem (na przykład `my-repository/` .

## <a name="host-configuration-values"></a>Wartości konfiguracji hosta

[ Blazor WebAssembly aplikacje](xref:blazor/hosting-models#blazor-webassembly) mogą akceptować następujące wartości konfiguracji hosta jako argumenty wiersza polecenia w czasie wykonywania w środowisku programistycznym.

### <a name="content-root"></a>Katalog główny zawartości

`--contentroot`Argument ustawia ścieżkę bezwzględną do katalogu, który zawiera pliki zawartości aplikacji ([katalog główny zawartości](xref:fundamentals/index#content-root)). W poniższych przykładach `/content-root-path` jest ścieżką katalogu głównego zawartości aplikacji.

* Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia. W katalogu aplikacji wykonaj następujące polecenie:

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* Dodaj wpis do `launchSettings.json` pliku aplikacji w profilu **IIS Express** . To ustawienie jest używane, gdy aplikacja jest uruchamiana z debugerem programu Visual Studio i z wiersza polecenia z `dotnet run` .

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji**. Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do `launchSettings.json` pliku.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Baza ścieżki

`--pathbase`Argument ustawia ścieżkę bazową aplikacji dla aplikacji uruchamianej lokalnie z niegłówną względną ścieżką URL ( `<base>` tag `href` jest ustawiony na ścieżkę inną niż `/` w przypadku przemieszczania i produkcji). W poniższych przykładach `/relative-URL-path` jest podstawą ścieżki aplikacji. Aby uzyskać więcej informacji, zobacz [Ścieżka podstawowa aplikacji](xref:blazor/host-and-deploy/index#app-base-path).

> [!IMPORTANT]
> W przeciwieństwie do ścieżki przekazanej do `href` `<base>` tagu, nie dodawaj końcowego ukośnika ( `/` ) podczas przekazywania `--pathbase` wartości argumentu. Jeśli ścieżka podstawowa aplikacji jest podana w `<base>` tagu jako `<base href="/CoolApp/">` (zawiera końcowy ukośnik), należy przekazać wartość argumentu wiersza polecenia jako `--pathbase=/CoolApp` (bez ukośnika na końcu).

* Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia. W katalogu aplikacji wykonaj następujące polecenie:

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* Dodaj wpis do `launchSettings.json` pliku aplikacji w profilu **IIS Express** . To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i z wiersza polecenia w programie `dotnet run` .

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji**. Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do `launchSettings.json` pliku.

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>Adresy URL

`--urls`Argument ustawia adresy IP lub adresy hosta z portami i protokołami, aby nasłuchiwać żądań.

* Przekaż argument podczas lokalnego uruchamiania aplikacji w wierszu polecenia. W katalogu aplikacji wykonaj następujące polecenie:

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* Dodaj wpis do `launchSettings.json` pliku aplikacji w profilu **IIS Express** . To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i z wiersza polecenia w programie `dotnet run` .

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* W programie Visual Studio Określ argument w **właściwościach**  >  **Debuguj**  >  **argumenty aplikacji**. Ustawienie argumentu na stronie właściwości programu Visual Studio powoduje dodanie argumentu do `launchSettings.json` pliku.

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>Konfigurowanie konsolidatora

Blazorwykonuje konsolidację języka pośredniego (IL) dla każdej kompilacji wydania, aby usunąć niepotrzebny kod IL z zestawów wyjściowych. Aby uzyskać więcej informacji, zobacz <xref:blazor/host-and-deploy/configure-linker>.

## <a name="custom-boot-resource-loading"></a>Ładowanie niestandardowego zasobu rozruchowego

Blazor WebAssemblyAplikacja może zostać zainicjowana przy użyciu `loadBootResource` funkcji w celu zastąpienia wbudowanego mechanizmu ładowania zasobów rozruchowego. Należy używać `loadBootResource` w następujących scenariuszach:

* Zezwalaj użytkownikom na ładowanie zasobów statycznych, takich jak dane dotyczące strefy czasowej lub `dotnet.wasm` z sieci CDN.
* Załaduj skompresowane zestawy za pomocą żądania HTTP i zdekompresuj je na kliencie dla hostów, które nie obsługują pobierania skompresowanej zawartości z serwera.
* Aliasowanie zasobów do innej nazwy przez przekierowanie każdego `fetch` żądania do nowej nazwy.

`loadBootResource`Parametry znajdują się w poniższej tabeli.

| Parametr    | Opis |
| ------------ | ----------- |
| `type`       | Typ zasobu. Typy Permissable: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` ,`timezonedata` |
| `name`       | Nazwa zasobu. |
| `defaultUri` | Względny lub bezwzględny identyfikator URI zasobu. |
| `integrity`  | Ciąg integralności reprezentujący oczekiwaną zawartość w odpowiedzi. |

`loadBootResource`zwraca jedną z następujących wartości, aby zastąpić proces ładowania:

* Ciąg identyfikatora URI. W poniższym przykładzie ( `wwwroot/index.html` ) następujące pliki są obsługiwane z sieci CDN w `https://my-awesome-cdn.com/` :

  * `dotnet.*.js`
  * `dotnet.wasm`
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

  Poniższy przykład ( `wwwroot/index.html` ) dodaje niestandardowy nagłówek HTTP do żądań wychodzących i przekazuje `integrity` parametr do `fetch` wywołania:
  
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

Wystarczy określić typy zachowań niestandardowych. Typy, które nie zostały określone do, `loadBootResource` są ładowane przez platformę na ich domyślne zachowania ładowania.

## <a name="change-the-filename-extension-of-dll-files"></a>Zmień rozszerzenie nazwy pliku DLL

Jeśli potrzebujesz zmienić rozszerzenia nazw plików opublikowanych w aplikacji `.dll` , postępuj zgodnie ze wskazówkami w tej sekcji.

Po opublikowaniu aplikacji Użyj skryptu powłoki lub potoku kompilacji DevOps, aby zmienić nazwy `.dll` plików, aby użyć innego rozszerzenia pliku. Docelowo `.dll` pliki w `wwwroot` katalogu opublikowanych danych wyjściowych aplikacji (na przykład `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).

W poniższych przykładach `.dll` nazwy plików są zmieniane, aby użyć `.bin` rozszerzenia pliku.

W systemie Windows:

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

W systemie Linux lub macOS:

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
Aby użyć innego rozszerzenia pliku niż `.bin` , Zastąp `.bin` w poprzednich poleceniach.

Aby rozwiązać skompresowane `blazor.boot.json.gz` i `blazor.boot.json.br` pliki, należy zastosować jedną z następujących metod:

* Usuń skompresowane `blazor.boot.json.gz` i `blazor.boot.json.br` pliki. Kompresja jest wyłączona w tym podejściu.
* Rekompresuj zaktualizowany `blazor.boot.json` plik.

Powyższe wskazówki stosuje się również w przypadku używania zasobów roboczych usługi. Usuń lub przekompresuj `wwwroot/service-worker-assets.js.br` i `wwwroot/service-worker-assets.js.gz` . W przeciwnym razie sprawdzanie integralności plików nie powiedzie się w przeglądarce.

Poniższy przykład systemu Windows używa skryptu programu PowerShell znajdującego się w katalogu głównym projektu.

`ChangeDLLExtensions.ps1:`:

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

Jeśli zasoby procesu roboczego usługi są również używane, Dodaj następujące polecenie:

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

W pliku projektu skrypt jest uruchamiany po opublikowaniu aplikacji:

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

Aby przekazać opinię, odwiedź stronę [aspnetcore/problemy #5477](https://github.com/dotnet/aspnetcore/issues/5477).
 
