---
title: Hostowanie i wdrażanie Blazor ASP.NET Core WebAssembly
author: guardrex
description: Dowiedz się, jak Blazor hostować i wdrażać aplikację przy użyciu ASP.NET Core, Content Delivery Networks (CDN), serwerów plików i stron GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: f364d94085d175fde5596c222ef21852c0106ec1
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751130"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a>Hostowanie i wdrażanie Blazor ASP.NET Core WebAssembly

[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)i [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Z [ Blazor webassembly hosting modelu:](xref:blazor/hosting-models#blazor-webassembly)

* Aplikacja, Blazor jej zależności i środowisko uruchomieniowe platformy .NET są pobierane do przeglądarki.
* Aplikacja jest wykonywana bezpośrednio w wątku interfejsu użytkownika przeglądarki.

Obsługiwane są następujące strategie wdrażania:

* Aplikacja Blazor jest obsługiwana przez aplikację ASP.NET Core. Ta strategia jest omówiona w [hostowane wdrożenie z ASP.NET Core](#hosted-deployment-with-aspnet-core) sekcji.
* Aplikacja Blazor jest umieszczana na statycznym serwerze www hostingu, gdzie .NET nie jest używana do obsługi Blazor aplikacji. Ta strategia jest omówiona w sekcji [samodzielne wdrażanie,](#standalone-deployment) która zawiera informacje na temat hostingu Blazor aplikacji WebAssembly jako podkoszuli IIS.

## <a name="rewrite-urls-for-correct-routing"></a>Przepisz adresy URL w celu prawidłowego routingu

Żądania routingu składników Blazor strony w aplikacji WebAssembly nie jest tak Blazor proste, jak żądania routingu w aplikacji serwerowej, hostowane. Należy Blazor wziąć pod uwagę webassembly aplikacji z dwóch składników:

* *Main.brzytwa* &ndash; Ładuje się w katalogu głównym `About` aplikacji`href="About"`i zawiera link do składnika ( ).
* *Składnik About.brzytwa.* &ndash; `About`

Gdy wymagany jest domyślny dokument aplikacji przy użyciu paska adresu `https://www.contoso.com/`przeglądarki (na przykład):

1. Przeglądarka składa żądanie.
1. Zwracana jest strona domyślna, która jest zwykle *index.html*.
1. *index.html* bootstraps aplikacji.
1. Blazor's ładuje się, a `Main` komponent Razor jest renderowany.

Na stronie głównej wybranie łącza `About` do składnika działa Blazor na kliencie, ponieważ router uniemożliwia `www.contoso.com` przeglądarce składanie żądania w Internecie do i `About` służy renderowane składnika. `About` Wszystkie żądania dotyczące wewnętrznych punktów końcowych *w aplikacji Blazor WebAssembly* działają w ten sam sposób: żądania nie wyzwalają żądań opartych na przeglądarce do zasobów hostowanych na serwerze w Internecie. Router obsługuje żądania wewnętrznie.

Jeśli żądanie zostanie złożone przy użyciu paska adresu przeglądarki dla `www.contoso.com/About`, żądanie nie powiedzie się. Nie istnieje taki zasób na hoście internetowym aplikacji, więc zwracana jest odpowiedź *404 — nie znaleziono.*

Ponieważ przeglądarki żądają hostów internetowych dla stron po stronie klienta, serwery sieci web i usługi hostingowe muszą przepisać wszystkie żądania dotyczące zasobów, które nie są fizycznie na serwerze, na stronie *index.html.* Po powrocie *pliku index.html* Blazor router aplikacji przejmuje kontrolę nad poprawnym zasobem i odpowiada za pomocą odpowiedniego zasobu.

Podczas wdrażania na serwerze usług IIS można użyć modułu ponownego zapisu adresu URL z opublikowanym *plikiem web.config* aplikacji. Aby uzyskać więcej informacji, zobacz sekcję [IIS.](#iis)

## <a name="hosted-deployment-with-aspnet-core"></a>Hostowane wdrażanie z ASP.NET Core

*Wdrożenie hostowane* obsługuje Blazor aplikację WebAssembly do przeglądarek z [aplikacji ASP.NET Core,](xref:index) która działa na serwerze sieci web.

Aplikacja Blazor WebAssembly klienta jest publikowana w folderze */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* aplikacji serwera wraz z innymi statycznymi zasobami sieci Web aplikacji serwera. Dwie aplikacje są wdrażane razem. Wymagany jest serwer sieci web, który jest w stanie hostowania aplikacji ASP.NET Core. W przypadku wdrożenia hostowanego program Visual Studio zawiera szablon`blazorwasm` projektu ** Blazor aplikacji WebAssembly** (szablon podczas korzystania`-ho|--hosted` z nowego `dotnet new` polecenia [dotnet)](/dotnet/core/tools/dotnet-new) z wybraną opcją **Hosted** ( podczas korzystania z polecenia).

Aby uzyskać więcej informacji na temat hostingu <xref:host-and-deploy/index>i wdrażania aplikacji core ASP.NET, zobacz .

Aby uzyskać informacje na temat wdrażania <xref:tutorials/publish-to-azure-webapp-using-vs>w usłudze Azure App Service, zobacz .

## <a name="standalone-deployment"></a>Wdrożenie autonomiczne

*Wdrożenie autonomiczne* służy Blazor aplikacji WebAssembly jako zestaw plików statycznych, które są wymagane bezpośrednio przez klientów. Każdy statyczny serwer plików jest Blazor w stanie obsługiwać aplikację.

Autonomiczne zasoby wdrażania są publikowane w folderze */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot.*

### <a name="iis"></a>IIS

Usługi IIS to zdolny statyczny serwer plików dla Blazor aplikacji. Aby skonfigurować programy IIS do hosta, Blazorzobacz Tworzenie [statycznej witryny sieci Web w serwisie IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Opublikowane zasoby są tworzone w folderze */bin/Release/{TARGET FRAMEWORK}/publish.* Hostuj zawartość folderu *publikowania* na serwerze www lub usłudze hostingowej.

#### <a name="webconfig"></a>Web.config

Po Blazor opublikowaniu projektu tworzony jest plik *web.config* z następującą konfiguracją usług IIS:

* Typy MIME są ustawiane dla następujących rozszerzeń plików:
  * *.dll (dll)* &ndash;`application/octet-stream`
  * *.json (json)* &ndash;`application/json`
  * *.wasm* &ndash;`application/wasm`
  * *.woff (woff)* &ndash;`application/font-woff`
  * *.woff2* &ndash;`application/font-woff`
* Kompresja HTTP jest włączona dla następujących typów MIME:
  * `application/octet-stream`
  * `application/wasm`
* Reguły modułu zapisywania adresów URL są ustanawiane:
  * Służy podkatarenie, w którym znajdują się zasoby statyczne aplikacji (*wwwroot/{PATH REQUESTED}*).
  * Utwórz routing rezerwowy SPA, aby żądania dotyczące zasobów innych niż pliki były przekierowywane do domyślnego dokumentu aplikacji w folderze zasobów statycznych (*wwwroot/index.html*).
  
#### <a name="use-a-custom-webconfig"></a>Korzystanie z niestandardowego pliku web.config

Aby użyć niestandardowego pliku *web.config:*

1. Umieść niestandardowy plik *web.config* w katalogu głównym folderu projektu.
1. Dodaj następujący obiekt docelowy do pliku projektu (*.csproj*):

   ```xml
   <Target Name="CopyWebConfigOnPublish" AfterTargets="Publish">
     <Copy SourceFiles="web.config" DestinationFolder="$(PublishDir)" />
   </Target>
   ```
   
> [!NOTE]
> `<IsWebConfigTransformDisabled>` Użycie właściwości MSBuild ustawionej `true` na nie Blazor jest obsługiwane w aplikacjach WebAssembly, [tak jak w przypadku aplikacji ASP.NET Core wdrożonych w serwisach IIS](xref:host-and-deploy/iis/index#webconfig-file). Aby uzyskać więcej informacji, zobacz [Kopiowanie obiektu docelowego Blazor wymaganego do podania niestandardowego pliku WEB.config (dotnet/aspnetcore #20569)](https://github.com/dotnet/aspnetcore/issues/20569).

#### <a name="install-the-url-rewrite-module"></a>Instalowanie modułu przepisywania adresów URL

[Moduł przepisywania adresu URL](https://www.iis.net/downloads/microsoft/url-rewrite) jest wymagany do ponownego zapisu adresów URL. Moduł nie jest zainstalowany domyślnie i nie jest dostępny do zainstalowania jako funkcja usługi roli serwera sieci Web (IIS). Moduł należy pobrać ze strony internetowej IIS. Użyj Instalatora platformy sieci Web, aby zainstalować moduł:

1. Lokalnie przejdź do [strony Pliki do pobrania modułu ponownego zapisu adresu URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). W przypadku wersji angielskiej wybierz pozycję **WebPI,** aby pobrać instalatora webpi. W przypadku innych języków wybierz odpowiednią architekturę serwera (x86/x64), aby pobrać instalator.
1. Skopiuj instalatora na serwer. Uruchom instalatora. Wybierz przycisk **Zainstaluj** i zaakceptuj postanowienia licencyjne. Ponowne uruchomienie serwera nie jest wymagane po zakończeniu instalacji.

#### <a name="configure-the-website"></a>Konfigurowanie witryny sieci Web

Ustaw **ścieżkę fizyczną** witryny sieci Web do folderu aplikacji. Folder zawiera:

* Plik *web.config* używany przez usługi IIS do konfigurowania witryny sieci Web, w tym wymagane reguły przekierowania i typy zawartości plików.
* Statyczny folder zasobów aplikacji.

#### <a name="host-as-an-iis-sub-app"></a>Host jako podnauka IIS

Jeśli autonomiczna aplikacja jest hostowana jako podnauka IIS, wykonaj jedną z następujących czynności:

* Wyłącz program obsługi dziedziczonego modułu ASP.NET core.

  Usuń program obsługi Blazor w opublikowanym pliku *web.config* aplikacji, dodając sekcję `<handlers>` do pliku:

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* Wyłącz dziedziczenie sekcji głównej `<system.webServer>` (nadrzędnej) `<location>` aplikacji `inheritInChildApplications` przy `false`użyciu elementu z ustawionym na:

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

Usunięcie programu obsługi lub wyłączenie dziedziczenia jest wykonywane oprócz [konfigurowania ścieżki podstawowej aplikacji](xref:host-and-deploy/blazor/index#app-base-path). Ustaw ścieżkę podstawową aplikacji w pliku *index.html* aplikacji na alias IIS używany podczas konfigurowania poddołty w programach IIS.

#### <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli zostanie odebrany *błąd serwera 500 — wewnętrzny,* a menedżer usług IIS zgłasza błędy podczas próby uzyskania dostępu do konfiguracji witryny sieci Web, upewnij się, że jest zainstalowany moduł przepisywania adresu URL. Gdy moduł nie jest zainstalowany, plik *web.config* nie może być analizowany przez usługi IIS. Uniemożliwia to menedżerowi usług IIS ładowanie konfiguracji witryny sieci BlazorWeb, a witryny sieci Web wyświetlania plików statycznych.

Aby uzyskać więcej informacji na temat rozwiązywania <xref:test/troubleshoot-azure-iis>problemów z wdrożeniami w usługach IIS, zobacz .

### <a name="azure-storage"></a>Azure Storage

[Hosting](/azure/storage/) plików statycznych usługi Blazor Azure Storage umożliwia hosting aplikacji bezserwerowych. Obsługiwane są niestandardowe nazwy domen, sieć dostarczania zawartości platformy Azure (CDN) i https.

Gdy usługa obiektu blob jest włączona dla statycznego hostingu witryny sieci Web na koncie magazynu:

* Ustaw **nazwę dokumentu** `index.html`Indeks na .
* Ustaw **ścieżkę** dokumentu `index.html`Błąd na . Składniki maszynki do golenia i inne punkty końcowe niebędące plikami nie znajdują się w ścieżkach fizycznych w zawartości statycznej przechowywanej przez usługę obiektu blob. Po odebraniu żądania jednego z Blazor tych zasobów, które router powinien obsłużyć, błąd *404 - Nie znaleziono* wygenerowany przez usługę obiektu blob kieruje żądanie do **ścieżki dokumentu błąd**. Obiekt blob *index.html* jest Blazor zwracany, a router ładuje i przetwarza ścieżkę.

Aby uzyskać więcej informacji, zobacz [Hosting statycznej witryny sieci Web w usłudze Azure Storage](/azure/storage/blobs/storage-blob-static-website).

### <a name="nginx"></a>Nginx

Następujący plik *nginx.conf* jest uproszczony, aby pokazać, jak skonfigurować Nginx do wysyłania pliku *index.html,* gdy nie można znaleźć odpowiedniego pliku na dysku.

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

Aby uzyskać więcej informacji na temat produkcji konfiguracji serwera sieci Web Nginx, zobacz [Tworzenie plików konfiguracyjnych NGINX Plus i NGINX](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).

### <a name="nginx-in-docker"></a>Nginx w mieście Docker

Aby Blazor hostować w utoke za pomocą platformy Nginx, skonfiguruj plik Dockerfile, aby użyć obrazu Nginx opartego na masie. Zaktualizuj plik Dockerfile, aby skopiować plik *nginx.config* do kontenera.

Dodaj jeden wiersz do pliku dockerfile, jak pokazano w poniższym przykładzie:

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

Aby wdrożyć aplikację Blazor WebAssembly w centos 7 lub nowszym:

1. Utwórz plik konfiguracyjny Apache. Poniższy przykład to uproszczony plik konfiguracyjny (*blazorapp.config*):

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

1. Umieść plik konfiguracyjny `/etc/httpd/conf.d/` Apache w katalogu, który jest domyślnym katalogiem konfiguracyjnym Apache w CentOS 7.

1. Umieść pliki aplikacji w `/var/www/blazorapp` katalogu (lokalizacja określona `DocumentRoot` w pliku konfiguracyjnym).

1. Uruchom ponownie usługę Apache.

Aby uzyskać więcej informacji, zobacz [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) i [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).

### <a name="github-pages"></a>Strony GitHub

Aby obsłużyć przepisywanie adresów URL, dodaj plik *404.html* ze skryptem obsługującym przekierowanie żądania do strony *index.html.* Aby uzyskać przykładową implementację udostępnianą przez społeczność, zobacz [Aplikacje jednostronicowe dla stron GitHub](https://spa-github-pages.rafrex.com/) [(rafrex/spa-github-pages w usłudze GitHub).](https://github.com/rafrex/spa-github-pages#readme) Przykład przy użyciu podejścia społeczności można zobaczyć na [blazor-demo/blazor-demo.github.io na GitHub](https://github.com/blazor-demo/blazor-demo.github.io) [(live site).](https://blazor-demo.github.io/)

W przypadku korzystania z witryny projektu zamiast witryny organizacji dodaj lub zaktualizuj `<base>` znacznik w pliku *index.html*. Ustaw `href` wartość atrybutu na nazwę repozytorium GitHub za pomocą `my-repository/`końcowego ukośnika (na przykład .

## <a name="host-configuration-values"></a>Wartości konfiguracji hosta

Aplikacje WebAssembly mogą akceptować następujące wartości konfiguracji hosta jako argumenty wiersza polecenia w czasie wykonywania w środowisku programistycznym. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)

### <a name="content-root"></a>Katalog główny zawartości

Argument `--contentroot` ustawia ścieżkę bezwzględną do katalogu zawierającego pliki zawartości aplikacji[(katalog główny zawartości](xref:fundamentals/index#content-root)). W poniższych przykładach `/content-root-path` jest ścieżka katalogu głównego zawartości aplikacji.

* Przekaż argument podczas uruchamiania aplikacji lokalnie w wierszu polecenia. Z katalogu aplikacji wykonaj następujące wykonanie:

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* Dodaj wpis do pliku *launchSettings.json* aplikacji w profilu **IIS Express.** To ustawienie jest używane, gdy aplikacja jest uruchamiana za pomocą `dotnet run`debugera programu Visual Studio i z wiersza polecenia z programem .

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* W programie Visual Studio określ argument w **argumentach** > Właściwości**debugowania** > **aplikacji**. Ustawienie argumentu na stronie właściwości programu Visual Studio dodaje argument do pliku *launchSettings.json.*

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Podstawa ścieżki

Argument `--pathbase` ustawia ścieżkę podstawową aplikacji dla aplikacji uruchamianej lokalnie ze `<base>` `href` ścieżką względnego adresu `/` URL niebędącego głównym (tag jest ustawiony na ścieżkę inną niż dla przemieszczania i produkcji). W poniższych przykładach `/relative-URL-path` jest baza ścieżki aplikacji. Aby uzyskać więcej informacji, zobacz [Ścieżka podstawowa aplikacji](xref:host-and-deploy/blazor/index#app-base-path).

> [!IMPORTANT]
> W przeciwieństwie do `href` ścieżki `<base>` podanej do znacznika, nie`/`należy dołączać końcowego ukośnika ( ) podczas przekazywania wartości argumentu. `--pathbase` Jeśli ścieżka podstawowa aplikacji `<base>` znajduje `<base href="/CoolApp/">` się w tagu jako (zawiera ukośnik końcowy), przekaż wartość argumentu wiersza polecenia jako `--pathbase=/CoolApp` (bez końcowego ukośnika).

* Przekaż argument podczas uruchamiania aplikacji lokalnie w wierszu polecenia. Z katalogu aplikacji wykonaj następujące wykonanie:

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* Dodaj wpis do pliku *launchSettings.json* aplikacji w profilu **IIS Express.** To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i wiersza polecenia z programem `dotnet run`.

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* W programie Visual Studio określ argument w **argumentach** > Właściwości**debugowania** > **aplikacji**. Ustawienie argumentu na stronie właściwości programu Visual Studio dodaje argument do pliku *launchSettings.json.*

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>Adresy URL

Argument `--urls` ustawia adresy IP lub adresy hostów z portami i protokołami do nasłuchiwania żądań.

* Przekaż argument podczas uruchamiania aplikacji lokalnie w wierszu polecenia. Z katalogu aplikacji wykonaj następujące wykonanie:

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* Dodaj wpis do pliku *launchSettings.json* aplikacji w profilu **IIS Express.** To ustawienie jest używane podczas uruchamiania aplikacji za pomocą debugera programu Visual Studio i wiersza polecenia z programem `dotnet run`.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* W programie Visual Studio określ argument w **argumentach** > Właściwości**debugowania** > **aplikacji**. Ustawienie argumentu na stronie właściwości programu Visual Studio dodaje argument do pliku *launchSettings.json.*

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>Konfigurowanie konsolidatora

Blazorwykonuje łącze języka pośredniego (IL) na każdej kompilacji release, aby usunąć niepotrzebne IL z zestawów wyjściowych. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/blazor/configure-linker>.
