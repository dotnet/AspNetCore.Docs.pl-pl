---
title: Host ASP.NET Core na Linuksie z Nginx
author: rick-anderson
description: Dowiedz się, jak skonfigurować Nginx jako odwrotny serwer proxy na Ubuntu 16.04, aby przekazywać ruch HTTP do aplikacji sieci web ASP.NET Core działającej na Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/05/2020
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 320a5364efe85b06028d8e80000e3455bb8ebd18
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657914"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a>Host ASP.NET Core na Linuksie z Nginx

Przez [Sourabh Shirhatti](https://twitter.com/sshirhatti)

W tym przewodniku wyjaśniono konfigurowanie środowiska ASP.NET Core gotowych do produkcji na serwerze Ubuntu 16.04. Te instrukcje prawdopodobnie działają z nowszymi wersjami Ubuntu, ale instrukcje nie zostały przetestowane w nowszych wersjach.

Aby uzyskać informacje na temat innych dystrybucji linuksa obsługiwanych przez ASP.NET Core, zobacz [Wymagania wstępne dla .NET Core w systemie Linux](/dotnet/core/linux-prerequisites).

> [!NOTE]
> Dla Ubuntu 14.04, *nadzorca* jest zalecane jako rozwiązanie do monitorowania procesu Kestrel. *systemd* nie jest dostępny na Ubuntu 14.04. Instrukcje Ubuntu 14.04 można znaleźć w [poprzedniej wersji tego tematu](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).

Ten przewodnik:

* Umieszcza istniejącą aplikację ASP.NET Core za odwrotnym serwerem proxy.
* Konfiguruje odwrotny serwer proxy do przesyłania dalej żądań do serwera sieci Web Kestrel.
* Zapewnia, że aplikacja internetowa działa podczas uruchamiania jako demon.
* Konfiguruje narzędzie do zarządzania procesami, aby ułatwić ponowne uruchomienie aplikacji sieci web.

## <a name="prerequisites"></a>Wymagania wstępne

1. Dostęp do serwera Ubuntu 16.04 ze standardowym kontem użytkownika z uprawnieniami sudo.
1. Zainstaluj środowisko uruchomieniowe .NET Core na serwerze.
   1. Odwiedź [stronę Pobierz .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
   1. Wybierz najnowszą wersję .NET Core bez podglądu.
   1. Pobierz najnowsze środowisko wykonawcze bez podglądu w tabeli w obszarze **Uruchamianie aplikacji - Środowisko wykonawcze**.
   1. Wybierz link **do instrukcji menedżera pakietów** Linuksa i postępuj zgodnie z instrukcjami Ubuntu dla swojej wersji Ubuntu.
1. Istniejąca aplikacja ASP.NET Core.

W dowolnym momencie w przyszłości po uaktualnieniu udostępnionej struktury uruchom ponownie aplikacje ASP.NET Core hostowane przez serwer.

## <a name="publish-and-copy-over-the-app"></a>Publikowanie i kopiowanie za pomocą aplikacji

Skonfiguruj aplikację dla [wdrożenia zależnego od struktury](/dotnet/core/deploying/#framework-dependent-deployments-fdd).

Jeśli aplikacja jest uruchamiana lokalnie i nie jest skonfigurowana do nawiązywać bezpieczne połączenia (HTTPS), należy przyjąć jedną z następujących metod:

* Skonfiguruj aplikację do obsługi bezpiecznych połączeń lokalnych. Aby uzyskać więcej informacji, zobacz sekcję [konfiguracji HTTPS.](#https-configuration)
* Usuń `https://localhost:5001` (jeśli jest `applicationUrl` obecny) z właściwości w *pliku Properties/launchSettings.json.*

Uruchom [dotnet publikowania](/dotnet/core/tools/dotnet-publish) ze środowiska programistycznego, aby spakować aplikację do katalogu (na przykład *&lt;bin/Release/ target_framework_moniker&gt;/publish),* który można uruchomić na serwerze:

```dotnetcli
dotnet publish --configuration Release
```

Aplikacja może być również opublikowana jako [samodzielne wdrożenie,](/dotnet/core/deploying/#self-contained-deployments-scd) jeśli wolisz nie utrzymywać środowiska uruchomieniowego .NET Core na serwerze.

Skopiuj aplikację ASP.NET Core na serwer przy użyciu narzędzia integrujące się z przepływem pracy organizacji (na przykład SCP, SFTP). Często lokalizuje się aplikacje internetowe w katalogu *var* (na przykład *var/www/helloapp).*

> [!NOTE]
> W scenariuszu wdrażania produkcyjnego przepływ pracy ciągłej integracji wykonuje pracę publikowania aplikacji i kopiowania zasobów na serwerze.

Przetestuj aplikację:

1. W wierszu polecenia uruchom `dotnet <app_assembly>.dll`aplikację: .
1. W przeglądarce przejdź `http://<serveraddress>:<port>` do, aby sprawdzić, czy aplikacja działa w systemie Linux lokalnie.

## <a name="configure-a-reverse-proxy-server"></a>Konfigurowanie odwrotnego serwera proxy

Odwrócony serwer proxy to powszechna konfiguracja do wyświetlania dynamicznych aplikacji internetowych. Odwrócony serwer proxy kończy żądanie HTTP i przekazuje go do aplikacji ASP.NET Core.

### <a name="use-a-reverse-proxy-server"></a>Używanie odwrotnego serwera proxy

Pustułka doskonale nadaje się do wyświetlania dynamicznych treści z ASP.NET Core. Jednak możliwości obsługi sieci Web nie są tak bogate w funkcje jak serwery takie jak IIS, Apache lub Nginx. Serwer odwrotnego serwera proxy może odciążać pracę, taką jak obsługa zawartości statycznej, żądania buforowania, kompresowanie żądań i zakończenie protokołu HTTPS z serwera HTTPS. Serwer odwrotnego serwera proxy może znajdować się na dedykowanym komputerze lub może zostać wdrożony wraz z serwerem HTTP.

Na potrzeby tego przewodnika używane jest pojedyncze wystąpienie Nginx. Działa na tym samym serwerze, obok serwera HTTP. W zależności od wymagań można wybrać inną konfigurację.

Ponieważ żądania są przekazywane za pośrednictwem odwrotnego serwera proxy, użyj [oprogramowania pośredniczącego nagłówków przesyłanych dalej](xref:host-and-deploy/proxy-load-balancer) z pakietu [Microsoft.AspNetCore.HttpOverrides.](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) Oprogramowanie pośredniczące `Request.Scheme`aktualizuje `X-Forwarded-Proto` , używając nagłówka, dzięki czemu przekierowanie identyfikatorów URI i innych zasad zabezpieczeń działa poprawnie.

Każdy składnik, który zależy od schematu, takich jak uwierzytelnianie, generowanie łączy, przekierowania i geolokalizacja, musi być umieszczony po wywołaniu oprogramowania pośredniczącego nagłówków przesyłanych dalej. Zgodnie z ogólną zasadą oprogramowanie pośredniczące nagłówków przesyłanych dalej powinno być uruchamiane przed innym oprogramowaniem pośredniczącym, z wyjątkiem diagnostyki i obsługi błędów oprogramowania pośredniczącego. Ta kolejność gwarantuje, że oprogramowanie pośredniczące korzystające z informacji o nagłówkach przesyłanych dalej może zużywać wartości nagłówka do przetwarzania.

Wywołaj <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodę `Startup.Configure` przed <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> wywołaniem lub podobnym oprogramowaniem pośredniczącym schematu uwierzytelniania. Skonfiguruj oprogramowanie `X-Forwarded-For` pośredniczące do przekazania nagłówków i `X-Forwarded-Proto` nagłówków:

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

Jeśli <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> oprogramowanie pośredniczące nie jest określone, domyślnymi nagłówkami do przekazania są `None`.

Serwery proxy uruchomione na adresach sprzężenia zwrotnego (127.0.0.0/8, [::1]), w tym standardowy adres localhost (127.0.0.1), są domyślnie zaufane. Jeśli inne zaufane serwery proxy lub sieci w organizacji obsługują żądania między Internetem <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>serwerem sieci web, dodaj je do listy lub z programem . Poniższy przykład dodaje zaufany serwer proxy pod adresem IP 10.0.0.100 `KnownProxies` `Startup.ConfigureServices`do oprogramowania pośredniczącego Nagłówków przesyłanych dalej w:

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/proxy-load-balancer>.

### <a name="install-nginx"></a>Instalowanie Nginx

Służy `apt-get` do instalowania Nginx. Instalator tworzy skrypt *init systemd,* który uruchamia Nginx jako demon podczas uruchamiania systemu. Postępuj zgodnie z instrukcjami instalacji Ubuntu w [Nginx: Official Debian / Pakiety Ubuntu](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).

> [!NOTE]
> Jeśli wymagane są opcjonalne moduły Nginx, może być wymagane tworzenie Nginx ze źródła.

Ponieważ Nginx został zainstalowany po raz pierwszy, jawnie uruchom go, uruchamiając:

```bash
sudo service nginx start
```

Sprawdź, czy przeglądarka wyświetla domyślną stronę docelową Nginx. Strona docelowa jest `http://<server_IP_address>/index.nginx-debian.html`osiągalna pod adresem .

### <a name="configure-nginx"></a>Konfigurowanie serwera Nginx

Aby skonfigurować Nginx jako odwrotny serwer proxy do przesyłania dalej żądań do aplikacji Core ASP.NET, zmodyfikuj */etc/nginx/sites-available/default*. Otwórz go w edytorze tekstu i zastąp zawartość następującymi:

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

Jeśli aplikacja jest aplikacją Blazor Server, która opiera się <xref:host-and-deploy/blazor/server#linux-with-nginx> na SignalR WebSockets, zobacz informacje na temat ustawiania nagłówka. `Connection`

Gdy `server_name` nie pasuje, Nginx używa serwera domyślnego. Jeśli nie zdefiniowano serwera domyślnego, pierwszym serwerem w pliku konfiguracyjnym jest serwer domyślny. Najlepszym rozwiązaniem jest dodanie określonego serwera domyślnego, który zwraca kod stanu 444 w pliku konfiguracyjnym. Domyślny przykład konfiguracji serwera to:

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

W poprzednim pliku konfiguracyjnym i serwerze domyślnym Nginx akceptuje `example.com` `*.example.com`ruch publiczny na porcie 80 z nagłówkiem hosta lub . Żądania, które nie pasują do tych hostów, nie zostaną przekazane do Kestrel. Nginx przekazuje pasujące żądania do `http://localhost:5000`Kestrel w . Zobacz [Jak nginx przetwarza żądanie, aby](https://nginx.org/docs/http/request_processing.html) uzyskać więcej informacji. Aby zmienić adres IP/port Kestrel, zobacz [Kestrel: Konfiguracja punktu końcowego](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!WARNING]
> Niepodkreślenie odpowiedniej [dyrektywy server_name](https://nginx.org/docs/http/server_names.html) naraża aplikację na luki w zabezpieczeniach. Powiązanie symboli wieloznacznych `*.example.com`poddomeny (na przykład) nie stanowi tego zagrożenia bezpieczeństwa, jeśli kontrolujesz całą domenę nadrzędną (w przeciwieństwie do `*.com`, która jest narażona). Zobacz [rfc7230 section-5.4,](https://tools.ietf.org/html/rfc7230#section-5.4) aby uzyskać więcej informacji.

Po ustanowieniu konfiguracji Nginx `sudo nginx -t` uruchom, aby sprawdzić składnię plików konfiguracyjnych. Jeśli test pliku konfiguracyjnego zakończy się pomyślnie, zmusić Nginx do pobrania zmian przez uruchomienie `sudo nginx -s reload`.

Aby bezpośrednio uruchomić aplikację na serwerze:

1. Przejdź do katalogu aplikacji.
1. Uruchom aplikację: `dotnet <app_assembly.dll>`, `app_assembly.dll` gdzie jest nazwa pliku zestawu aplikacji.

Jeśli aplikacja działa na serwerze, ale nie odpowiada przez Internet, sprawdź zaporę serwera i upewnij się, że port 80 jest otwarty. Jeśli używasz maszyny Wirtualnej Ubuntu platformy Azure, dodaj regułę sieciowej grupy zabezpieczeń (NSG), która umożliwia ruch przychodzący port 80. Nie ma potrzeby włączania reguły portu wychodzącego 80, ponieważ ruch wychodzący jest automatycznie przyznawany, gdy reguła ruchu przychodzącego jest włączona.

Po zakończeniu testowania aplikacji zamknij `Ctrl+C` aplikację w wierszu polecenia.

## <a name="monitor-the-app"></a>Monitorowanie aplikacji

Serwer jest skonfigurowany do `http://<serveraddress>:80` przekazywania żądań kierowanych do aplikacji ASP.NET `http://127.0.0.1:5000`Core uruchomionej na Kestrel w punkcie . Jednak Nginx nie jest skonfigurowany do zarządzania procesem Kestrel. *systemd* może służyć do tworzenia pliku usługi, aby uruchomić i monitorować podstawową aplikację internetową. *systemd* to system init, który zapewnia wiele zaawansowanych funkcji uruchamiania, zatrzymywania i zarządzania procesami. 

### <a name="create-the-service-file"></a>Tworzenie pliku usługi

Utwórz plik definicji usługi:

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

Poniżej przedstawiono przykładowy plik usługi dla aplikacji:

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

W poprzednim przykładzie użytkownik, który zarządza usługą `User` jest określony przez opcję. Użytkownik (`www-data`) musi istnieć i mieć odpowiednie prawo własności do plików aplikacji.

Służy `TimeoutStopSec` do konfigurowania czasu oczekiwania na zamknięcie aplikacji po odebraniu początkowego sygnału przerwania. Jeśli aplikacja nie zostanie zamknięta w tym okresie, SIGKILL zostanie wystawiony w celu zakończenia aplikacji. Podaj wartość jako sekundy `150`bez jednostek (na przykład), wartość przedziału czasu (na `2min 30s`przykład) lub `infinity` wyłącz limit czasu. `TimeoutStopSec`domyślnie wartość w `DefaultTimeoutStopSec` pliku konfiguracyjnym menedżera (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*). Domyślny limit czasu dla większości dystrybucji wynosi 90 sekund.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Linux ma system plików z uwzględnieniem wielkości liter. Ustawienie ASPNETCORE_ENVIRONMENT na "Produkcja" powoduje wyszukanie pliku konfiguracji *appsettings. Production.json*, nie *appsettings.production.json*.

Niektóre wartości (na przykład parametry połączenia SQL) muszą zostać zmienione dla dostawców konfiguracji, aby odczytać zmienne środowiskowe. Użyj następującego polecenia, aby wygenerować poprawnie odsunącą wartość do użycia w pliku konfiguracyjnym:

```console
systemd-escape "<value-to-escape>"
```

Separatory`:`dwukropek ( ) nie są obsługiwane w nazwach zmiennych środowiskowych. Użyj podwójnego podkreślenia (`__`) zamiast jelita grubego. [Dostawca konfiguracji zmiennych środowiskowych](xref:fundamentals/configuration/index#environment-variables-configuration-provider) konwertuje podwójne podkreślenia na dwukropki, gdy zmienne środowiskowe są odczytywane do konfiguracji. W poniższym przykładzie klucz `ConnectionStrings:DefaultConnection` parametry połączenia jest ustawiony `ConnectionStrings__DefaultConnection`w pliku definicji usługi jako:

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

Zapisz plik i włącz usługę.

```bash
sudo systemctl enable kestrel-helloapp.service
```

Uruchom usługę i sprawdź, czy jest uruchomiona.

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

Dzięki skonfigurowanego odwrotnego serwera proxy i kestrelowi zarządzanej przez systemd, aplikacja internetowa jest w `http://localhost`pełni skonfigurowana i można uzyskać do niej dostęp z przeglądarki na komputerze lokalnym pod adresem . Jest również dostępny z komputera zdalnego, blokując każdą zaporę, która może blokować. Sprawdzając nagłówki odpowiedzi, `Server` nagłówek pokazuje ASP.NET Core aplikacji obsługiwanych przez Kestrel.

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Wyświetlanie dzienników

Ponieważ aplikacja internetowa przy użyciu Kestrel jest zarządzana za pomocą `systemd`, wszystkie zdarzenia i procesy są rejestrowane w scentralizowanym dzienniku. Jednak ten arkusz zawiera wszystkie wpisy dla `systemd`wszystkich usług i procesów zarządzanych przez program . Aby wyświetlić elementy określone w `kestrel-helloapp.service`poszczególnych, użyj następującego polecenia:

```bash
sudo journalctl -fu kestrel-helloapp.service
```

W przypadku dalszego filtrowania `--since today`opcje `--until 1 hour ago` czasu, takie jak , lub ich kombinacja, mogą zmniejszyć liczbę zwróconych wpisów.

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a>Ochrona danych

[Stos ASP.NET Core Data Protection](xref:security/data-protection/introduction) jest używany przez kilka ASP.NET core middleware , w tym oprogramowanie [pośredniczące](xref:fundamentals/middleware/index)uwierzytelniania (na przykład oprogramowanie pośredniczące plików cookie) i zabezpieczenia fałszowania żądań między witrynami (CSRF). Nawet jeśli interfejsy API ochrony danych nie są wywoływane przez kod użytkownika, ochrona danych powinna być skonfigurowana do tworzenia trwałego [magazynu kluczy](xref:security/data-protection/implementation/key-management)kryptograficznych . Jeśli ochrona danych nie jest skonfigurowana, klucze są przechowywane w pamięci i odrzucane po ponownym uruchomieniu aplikacji.

Jeśli pierścień klucza jest przechowywany w pamięci po ponownym uruchomieniu aplikacji:

* Wszystkie tokeny uwierzytelniania oparte na plikach cookie są unieważnione.
* Użytkownicy muszą zalogować się ponownie przy następnym żądaniu.
* Nie można już odszyfrować żadnych danych chronionych za pomocą pierścienia kluczy. Może to obejmować [tokeny CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) i [ASP.NET podstawowe pliki cookie MVC TempData](xref:fundamentals/app-state#tempdata).

Aby skonfigurować ochronę danych do utrwalania i szyfrowania pierścienia kluczy, zobacz:

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a>Pola nagłówka żądania długiego

Domyślne ustawienia serwera proxy zazwyczaj ograniczają pola nagłówka żądania do 4 K lub 8 K w zależności od platformy. Aplikacja może wymagać pól dłuższych niż domyślne (na przykład aplikacji korzystających z [usługi Azure Active Directory).](https://azure.microsoft.com/services/active-directory/) Jeśli wymagane są dłuższe pola, domyślne ustawienia serwera proxy wymagają dostosowania. Wartości, które należy zastosować, zależą od scenariusza. Aby uzyskać więcej informacji, zapoznaj się z dokumentacją serwera.

* [proxy_buffer_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [proxy_buffers](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [proxy_busy_buffers_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [large_client_header_buffers](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> Nie należy zwiększać wartości domyślnych buforów serwera proxy, chyba że jest to konieczne. Zwiększenie tych wartości zwiększa ryzyko przepełnienia buforu (przepełnienie) i ataków typu "odmowa usługi" (DoS) przez złośliwych użytkowników.

## <a name="secure-the-app"></a>Zabezpiecz aplikację

### <a name="enable-apparmor"></a>Włącz AppArmor

Linux Security Modules (LSM) to framework, który jest częścią jądra Linuksa od Linuksa 2.6. LSM obsługuje różne implementacje modułów zabezpieczeń. [AppArmor](https://wiki.ubuntu.com/AppArmor) to LSM, który implementuje system obowiązkowej kontroli dostępu, który pozwala na ograniczenie programu do ograniczonego zestawu zasobów. Upewnij się, że AppArmor jest włączony i poprawnie skonfigurowany.

### <a name="configure-the-firewall"></a>Konfigurowanie zapory

Zamknij wszystkie porty zewnętrzne, które nie są używane. Nieskomplikowana zapora (ufw) `iptables` zapewnia front-end, zapewniając interfejsu wiersza polecenia do konfigurowania zapory.

> [!WARNING]
> Zapora uniemożliwi dostęp do całego systemu, jeśli nie zostanie poprawnie skonfigurowany. Niepodjęcie prawidłowego portu SSH skutecznie zablokuje Cię z systemu, jeśli używasz SSH do łączenia się z nim. Domyślny port to 22. Aby uzyskać więcej informacji, zobacz [wprowadzenie do ufw](https://help.ubuntu.com/community/UFW) i [instrukcja](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).

Zainstaluj `ufw` i skonfiguruj go tak, aby zezwalał na ruch na wszystkich potrzebnych portach.

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a>Bezpieczny Nginx

#### <a name="change-the-nginx-response-name"></a>Zmienianie nazwy odpowiedzi Nginx

Edycja *src/http/ngx_http_header_filter_module.c*:

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a>Konfigurowanie opcji

Skonfiguruj serwer za pomocą dodatkowych wymaganych modułów. Należy rozważyć użycie zapory aplikacji sieci web, takich jak [ModSecurity](https://www.modsecurity.org/), aby wzmocnić aplikację.

#### <a name="https-configuration"></a>Konfiguracja PROTOKOŁU HTTPS

**Konfigurowanie aplikacji do bezpiecznych połączeń lokalnych (HTTPS)**

Polecenie [dotnet run](/dotnet/core/tools/dotnet-run) używa pliku *Properties/launchSettings.json* aplikacji, który konfiguruje aplikację do nasłuchiwania na adresach URL dostarczonych przez `applicationUrl` właściwość (na przykład `https://localhost:5001;http://localhost:5000`).

Skonfiguruj aplikację do używania `dotnet run` certyfikatu w środowisku deweloperskim (F5 lub Ctrl+F5 w programie Visual Studio Code) przy użyciu jednego z następujących metod:

* [Zastąp domyślny certyfikat z konfiguracji](xref:fundamentals/servers/kestrel#configuration) (*Zalecane*)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

**Konfigurowanie odwrotnego serwera proxy dla bezpiecznych połączeń klientów (HTTPS)**

* Skonfiguruj serwer do nasłuchiwania ruchu HTTPS na porcie, `443` określając prawidłowy certyfikat wystawiony przez zaufany urząd certyfikacji (CA).

* Wzmocnij bezpieczeństwo, stosując niektóre praktyki przedstawione w następującym pliku */etc/nginx/nginx.conf.* Przykłady obejmują wybranie silniejszego szyfru i przekierowanie całego ruchu za pośrednictwem protokołu HTTP do https.

* Dodanie `HTTP Strict-Transport-Security` nagłówka (HSTS) gwarantuje, że wszystkie kolejne żądania wykonane przez klienta są za pośrednictwem protokołu HTTPS.

* Nie dodawaj nagłówka HSTS ani `max-age` nie wybieraj odpowiedniego, jeśli protokół HTTPS zostanie wyłączony w przyszłości.

Dodaj plik konfiguracyjny */etc/nginx/proxy.conf:*

[!code-nginx[](linux-nginx/proxy.conf)]

Edytuj plik konfiguracyjny */etc/nginx/nginx.conf.* Przykład zawiera `http` zarówno `server` i sekcje w jednym pliku konfiguracyjnym.

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a>Zabezpiecz Nginx przed kliknięciem

[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), znany również jako *atak zadośćuczynienia interfejsu*użytkownika , to złośliwy atak, w którym użytkownik witryny zostaje oszukany, klikając link lub przycisk na innej stronie niż obecnie odwiedza. Służy `X-FRAME-OPTIONS` do zabezpieczania witryny.

Aby ograniczyć ataki typu clickjacking:

1. Edytuj plik *nginx.conf:*

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   Dodaj wiersz `add_header X-Frame-Options "SAMEORIGIN";`.
1. Zapisz plik.
1. Uruchom ponownie nginx.

#### <a name="mime-type-sniffing"></a>Wąchanie typu MIME

Ten nagłówek uniemożliwia większości przeglądarek z MIME wąchania odpowiedzi z dala od zadeklarowanego typu zawartości, jak nagłówek instruuje przeglądarkę, aby nie zastępować typu zawartości odpowiedzi. Z `nosniff` opcją, jeśli serwer mówi, że zawartość jest "text/html", przeglądarka renderuje go jako "text/html".

Edytuj plik *nginx.conf:*

```bash
sudo nano /etc/nginx/nginx.conf
```

Dodaj wiersz `add_header X-Content-Type-Options "nosniff";` i zapisz plik, a następnie uruchom ponownie Nginx.

## <a name="additional-nginx-suggestions"></a>Dodatkowe sugestie Nginx

Po uaktualnieniu udostępnionej struktury na serwerze uruchom ponownie aplikacje ASP.NET Core hostowane przez serwer.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wymagania wstępne dla .NET Core w systemie Linux](/dotnet/core/linux-prerequisites)
* [Nginx: Publikacje binarne: Oficjalne pakiety Debian/Ubuntu](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [NGINX: Korzystanie z nagłówka przesyłania dalej](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
