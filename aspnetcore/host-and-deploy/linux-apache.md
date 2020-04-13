---
title: Host ASP.NET Core na Linuksie z Apache
author: rick-anderson
description: Dowiedz się, jak skonfigurować Apache jako odwrotny serwer proxy w CentOS, aby przekierować ruch HTTP do aplikacji sieci web ASP.NET Core działającej na Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 04/10/2020
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 1256f6d21f94ef6c4baad7aae4bd0e751af5c675
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81224040"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a>Host ASP.NET Core na Linuksie z Apache

Przez [Shayne Boyer](https://github.com/spboyer)

Korzystając z tego przewodnika, dowiedz się, jak skonfigurować [Apache](https://httpd.apache.org/) jako odwrotny serwer proxy w [systemie CentOS 7,](https://www.centos.org/) aby przekierować ruch HTTP do aplikacji sieci web ASP.NET Core działającej na serwerze [Kestrel.](xref:fundamentals/servers/kestrel) Rozszerzenie [mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) i powiązane moduły tworzą odwrotny serwer proxy serwera.

## <a name="prerequisites"></a>Wymagania wstępne

* Serwer z systemem CentOS 7 ze standardowym kontem użytkownika z uprawnieniami sudo.
* Zainstaluj środowisko uruchomieniowe .NET Core na serwerze.
   1. Odwiedź [stronę Pobierz .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
   1. Wybierz najnowszą wersję .NET Core bez podglądu.
   1. Pobierz najnowsze środowisko wykonawcze bez podglądu w tabeli w obszarze **Uruchamianie aplikacji - Środowisko wykonawcze**.
   1. Wybierz **łącze z instrukcjami menedżera pakietów** systemu Linux i postępuj zgodnie z instrukcjami CentOS.
* Istniejąca aplikacja ASP.NET Core.

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

## <a name="configure-a-proxy-server"></a>Konfigurowanie serwera proxy

Odwrócony serwer proxy to powszechna konfiguracja do wyświetlania dynamicznych aplikacji internetowych. Odwrócony serwer proxy kończy żądanie HTTP i przekazuje go do aplikacji ASP.NET.

Serwer proxy to serwer, który przekazuje żądania klientów do innego serwera zamiast samych żądań. Odwrócony serwer proxy przekazuje do stałego miejsca docelowego, zazwyczaj w imieniu dowolnych klientów. W tym przewodniku Apache jest skonfigurowany jako odwrotny serwer proxy uruchomiony na tym samym serwerze, który Kestrel obsługuje aplikację ASP.NET Core.

Ponieważ żądania są przekazywane za pośrednictwem odwrotnego serwera proxy, użyj [oprogramowania pośredniczącego nagłówków przesyłanych dalej](xref:host-and-deploy/proxy-load-balancer) z pakietu [Microsoft.AspNetCore.HttpOverrides.](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) Oprogramowanie pośredniczące `Request.Scheme`aktualizuje `X-Forwarded-Proto` , używając nagłówka, dzięki czemu przekierowanie identyfikatorów URI i innych zasad zabezpieczeń działa poprawnie.

Każdy składnik, który zależy od schematu, takich jak uwierzytelnianie, generowanie łączy, przekierowania i geolokalizacja, musi być umieszczony po wywołaniu oprogramowania pośredniczącego nagłówków przesyłanych dalej. Zgodnie z ogólną zasadą oprogramowanie pośredniczące nagłówków przesyłanych dalej powinno być uruchamiane przed innym oprogramowaniem pośredniczącym, z wyjątkiem diagnostyki i obsługi błędów oprogramowania pośredniczącego. Ta kolejność gwarantuje, że oprogramowanie pośredniczące korzystające z informacji o nagłówkach przesyłanych dalej może zużywać wartości nagłówka do przetwarzania.

Wywołać <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodę w górnej `Startup.Configure` części przed wywołaniem innych oprogramowania pośredniczącego. Skonfiguruj oprogramowanie `X-Forwarded-For` pośredniczące do przekazania nagłówków i `X-Forwarded-Proto` nagłówków:

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

### <a name="install-apache"></a>Instalowanie apache

Aktualizuj pakiety CentOS do ich najnowszych stabilnych wersji:

```bash
sudo yum update -y
```

Zainstaluj serwer www Apache na CentOS za pomocą jednego `yum` polecenia:

```bash
sudo yum -y install httpd mod_ssl
```

Przykładowe dane wyjściowe po uruchomieniu polecenia:

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> W tym przykładzie dane wyjściowe odzwierciedla httpd.86_64 ponieważ wersja CentOS 7 jest 64 bit. Aby sprawdzić, gdzie jest zainstalowany `whereis httpd` apache, uruchom z wiersza polecenia.

### <a name="configure-apache"></a>Konfigurowanie apache

Pliki konfiguracyjne apache `/etc/httpd/conf.d/` znajdują się w katalogu. Każdy plik z rozszerzeniem *.conf* jest przetwarzany w kolejności alfabetycznej oprócz plików konfiguracyjnych modułu w `/etc/httpd/conf.modules.d/`, który zawiera wszelkie pliki konfiguracyjne niezbędne do załadowania modułów.

Utwórz plik konfiguracyjny o nazwie *helloapp.conf*dla aplikacji:

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

Blok `VirtualHost` może pojawić się wiele razy, w jednym lub kilku plikach na serwerze. W poprzednim pliku konfiguracyjnym Apache akceptuje ruch publiczny na porcie 80. Domena `www.example.com` jest obsługiwana, `*.example.com` a alias jest rozpoznawany w tej samej witrynie sieci Web. Aby uzyskać więcej informacji, zobacz [obsługa hosta wirtualnego opartego](https://httpd.apache.org/docs/current/vhosts/name-based.html) na nazwach. Żądania są przenoszone przez katalog główny do portu 5000 serwera o godzinie 127.0.0.1. Do komunikacji dwukierunkowej `ProxyPass` `ProxyPassReverse` i są wymagane. Aby zmienić adres IP/port Kestrel, zobacz [Kestrel: Konfiguracja punktu końcowego](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!WARNING]
> Niepodjęcie odpowiedniej [dyrektywy ServerName](https://httpd.apache.org/docs/current/mod/core.html#servername) w bloku **VirtualHost** udostępnia aplikację na luki w zabezpieczeniach. Powiązanie symboli wieloznacznych `*.example.com`poddomeny (na przykład) nie stanowi tego zagrożenia bezpieczeństwa, jeśli kontrolujesz całą domenę nadrzędną (w przeciwieństwie do `*.com`, która jest narażona). Zobacz [rfc7230 section-5.4,](https://tools.ietf.org/html/rfc7230#section-5.4) aby uzyskać więcej informacji.

Rejestrowanie można skonfigurować `VirtualHost` `ErrorLog` na `CustomLog` using i dyrektyw. `ErrorLog`to lokalizacja, w której serwer rejestruje `CustomLog` błędy i ustawia nazwę pliku i format pliku dziennika. W takim przypadku jest to miejsce, w którym rejestrowane są informacje o żądaniu. Istnieje jeden wiersz dla każdego żądania.

Zapisz plik i przetestuj konfigurację. Jeśli wszystko przejdzie, odpowiedź `Syntax [OK]`powinna być .

```bash
sudo service httpd configtest
```

Uruchom ponownie apache:

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a>Monitorowanie aplikacji

Apache jest teraz skonfigurowany `http://localhost:80` do przekazywania żądań do aplikacji ASP.NET `http://127.0.0.1:5000`Core uruchomionej na Kestrel w . Jednak Apache nie jest skonfigurowany do zarządzania procesem Kestrel. Użyj *systemd* i utworzyć plik usługi, aby uruchomić i monitorować podstawowej aplikacji sieci web. *systemd* to system init, który zapewnia wiele zaawansowanych funkcji uruchamiania, zatrzymywania i zarządzania procesami.

### <a name="create-the-service-file"></a>Tworzenie pliku usługi

Utwórz plik definicji usługi:

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

Przykładowy plik usługi dla aplikacji:

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

W poprzednim przykładzie użytkownik, który zarządza usługą `User` jest określony przez opcję. Użytkownik (`apache`) musi istnieć i mieć odpowiednie prawo własności do plików aplikacji.

Służy `TimeoutStopSec` do konfigurowania czasu oczekiwania na zamknięcie aplikacji po odebraniu początkowego sygnału przerwania. Jeśli aplikacja nie zostanie zamknięta w tym okresie, SIGKILL zostanie wystawiony w celu zakończenia aplikacji. Podaj wartość jako sekundy `150`bez jednostek (na przykład), wartość przedziału czasu (na `2min 30s`przykład) lub `infinity` wyłącz limit czasu. `TimeoutStopSec`domyślnie wartość w `DefaultTimeoutStopSec` pliku konfiguracyjnym menedżera (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*). Domyślny limit czasu dla większości dystrybucji wynosi 90 sekund.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Niektóre wartości (na przykład parametry połączenia SQL) muszą zostać zmienione dla dostawców konfiguracji, aby odczytać zmienne środowiskowe. Użyj następującego polecenia, aby wygenerować poprawnie odsunącą wartość do użycia w pliku konfiguracyjnym:

```console
systemd-escape "<value-to-escape>"
```

Separatory`:`dwukropek ( ) nie są obsługiwane w nazwach zmiennych środowiskowych. Użyj podwójnego podkreślenia (`__`) zamiast jelita grubego. [Dostawca konfiguracji zmiennych środowiskowych](xref:fundamentals/configuration/index#environment-variables-configuration-provider) konwertuje podwójne podkreślenia na dwukropki, gdy zmienne środowiskowe są odczytywane do konfiguracji. W poniższym przykładzie klucz `ConnectionStrings:DefaultConnection` parametry połączenia jest ustawiony `ConnectionStrings__DefaultConnection`w pliku definicji usługi jako:

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

Zapisz plik i włącz usługę:

```bash
sudo systemctl enable kestrel-helloapp.service
```

Uruchom usługę i sprawdź, czy jest uruchomiona:

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

Dzięki skonfigurowanego odwrotnego serwera proxy i Kestrel zarządzanej przez *systemd,* aplikacja internetowa jest w `http://localhost`pełni skonfigurowana i można uzyskać do niej dostęp z przeglądarki na komputerze lokalnym pod adresem . Sprawdzając nagłówki odpowiedzi, nagłówek **serwera** wskazuje, że aplikacja ASP.NET Core jest obsługiwana przez Kestrel:

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Wyświetlanie dzienników

Ponieważ aplikacja internetowa przy użyciu Kestrel jest zarządzana za pomocą *systemd,* zdarzenia i procesy są rejestrowane w scentralizowanym dzienniku. Jednak ten arkusz zawiera wpisy dla wszystkich usług i procesów zarządzanych przez *systemd*. Aby wyświetlić elementy określone w `kestrel-helloapp.service`poszczególnych, użyj następującego polecenia:

```bash
sudo journalctl -fu kestrel-helloapp.service
```

W przypadku filtrowania czasu należy określić opcje czasu za pomocą polecenia. Na przykład `--since today` służy do filtrowania `--until 1 hour ago` dla bieżącego dnia lub do wyświetlenia wpisów z poprzedniej godziny. Aby uzyskać więcej informacji, zobacz [stronę man dla journalctl](https://www.unix.com/man-page/centos/1/journalctl/).

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

## <a name="secure-the-app"></a>Zabezpiecz aplikację

### <a name="configure-firewall"></a>Konfigurowanie zapory

*Firewalld* to dynamiczny demon do zarządzania zaporą z obsługą stref sieciowych. Portami i filtrowania pakietów nadal mogą być zarządzane przez iptables. *Zapórka* powinna być zainstalowana domyślnie. `yum`można użyć do zainstalowania pakietu lub sprawdzenia, czy jest zainstalowany.

```bash
sudo yum install firewalld -y
```

Służy `firewalld` do otwierania tylko portów potrzebnych do aplikacji. W takim przypadku używane są porty 80 i 443. Następujące polecenia na stałe ustawiają porty 80 i 443, aby otworzyć:

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

Ponownie załaduj ustawienia zapory. Sprawdź dostępne usługi i porty w strefie domyślnej. Opcje są dostępne `firewall-cmd -h`po sprawdzeniu .

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="https-configuration"></a>Konfiguracja PROTOKOŁU HTTPS

**Konfigurowanie aplikacji do bezpiecznych połączeń lokalnych (HTTPS)**

Polecenie [dotnet run](/dotnet/core/tools/dotnet-run) używa pliku *Properties/launchSettings.json* aplikacji, który konfiguruje aplikację do nasłuchiwania na adresach URL dostarczonych przez `applicationUrl` właściwość (na przykład `https://localhost:5001;http://localhost:5000`).

Skonfiguruj aplikację do używania `dotnet run` certyfikatu w środowisku deweloperskim (F5 lub Ctrl+F5 w programie Visual Studio Code) przy użyciu jednego z następujących metod:

* [Zastąp domyślny certyfikat z konfiguracji](xref:fundamentals/servers/kestrel#configuration) (*Zalecane*)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

**Konfigurowanie odwrotnego serwera proxy dla bezpiecznych połączeń klientów (HTTPS)**

Aby skonfigurować Apache dla protokołu HTTPS, używany jest moduł *mod_ssl.* Po zainstalowaniu modułu *httpd* zainstalowano również moduł *mod_ssl.* Jeśli nie został zainstalowany, `yum` użyj, aby dodać go do konfiguracji.

```bash
sudo yum install mod_ssl
```

Aby wymusić https, należy zainstalować moduł, `mod_rewrite` aby włączyć przepisywanie adresów URL:

```bash
sudo yum install mod_rewrite
```

Zmodyfikuj plik *helloapp.conf,* aby włączyć przepisywanie adresów URL i bezpieczną komunikację na porcie 443:

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> W tym przykładzie jest przy użyciu certyfikatu generowane lokalnie. **SSLCertificateFile** powinien być podstawowym plikiem certyfikatu dla nazwy domeny. **SSLCertificateKeyFile** powinien być plikiem klucza generowanym podczas tworzenia csr. **SSLCertificateChainFile** powinien być pośrednim plikiem certyfikatu (jeśli istnieje), który został dostarczony przez urząd certyfikacji.

Zapisz plik i przetestuj konfigurację:

```bash
sudo service httpd configtest
```

Uruchom ponownie apache:

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a>Dodatkowe sugestie Apache

### <a name="restart-apps-with-shared-framework-updates"></a>Ponowne uruchamianie aplikacji z udostępnionymi aktualizacjami struktury

Po uaktualnieniu udostępnionej struktury na serwerze uruchom ponownie aplikacje ASP.NET Core hostowane przez serwer.

### <a name="additional-headers"></a>Dodatkowe nagłówki

Aby zabezpieczyć się przed złośliwymi atakami, istnieje kilka nagłówków, które powinny zostać zmodyfikowane lub dodane. Upewnij się, że `mod_headers` moduł jest zainstalowany:

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a>Bezpieczne Apache przed atakami typu clickjacking

[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), znany również jako *atak zadośćuczynienia interfejsu*użytkownika , to złośliwy atak, w którym użytkownik witryny zostaje oszukany, klikając link lub przycisk na innej stronie niż obecnie odwiedza. Służy `X-FRAME-OPTIONS` do zabezpieczania witryny.

Aby ograniczyć ataki typu clickjacking:

1. Edytuj plik *httpd.conf:*

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   Dodaj wiersz `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.
1. Zapisz plik.
1. Uruchom ponownie apache.

#### <a name="mime-type-sniffing"></a>Wąchanie typu MIME

Nagłówek `X-Content-Type-Options` zapobiega *wąchaniu programu* Internet Explorer do mime `Content-Type` (określanie pliku z zawartości pliku). Jeśli serwer ustawi `Content-Type` `text/html` nagłówek `nosniff` z zestawem opcji, program `text/html` Internet Explorer renderuje zawartość jako niezależnie od zawartości pliku.

Edytuj plik *httpd.conf:*

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

Dodaj wiersz `Header set X-Content-Type-Options "nosniff"`. Zapisz plik. Uruchom ponownie apache.

### <a name="load-balancing"></a>Równoważenie obciążenia

W tym przykładzie pokazano, jak skonfigurować apache na CentOS 7 i Kestrel na tym samym komputerze wystąpienia. Aby nie mieć jednego punktu awarii; korzystanie *z mod_proxy_balancer* i modyfikowanie **VirtualHost** pozwoliłoby na zarządzanie wieloma wystąpieniami aplikacji internetowych za serwerem proxy Apache.

```bash
sudo yum install mod_proxy_balancer
```

W pliku konfiguracyjnym pokazanym `helloapp` poniżej dodatkowe wystąpienie jest skonfigurowane do uruchamiania na porcie 5001. Sekcja *Proxy* jest ustawiona z konfiguracją balansu z dwoma członkami, aby równoważyć obciążenie *byrequests*.

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a>Limity stawek

Korzystając z *mod_ratelimit*, który jest zawarty w module *httpd,* przepustowość klientów może być ograniczona:

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

Przykładowy plik ogranicza przepustowość jako 600 KB/s w lokalizacji głównej:

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a>Pola nagłówka żądania długiego

Domyślne ustawienia serwera proxy zazwyczaj ograniczają pola nagłówka żądania do 8190 bajtów. Aplikacja może wymagać pól dłuższych niż domyślne (na przykład aplikacji korzystających z [usługi Azure Active Directory).](https://azure.microsoft.com/services/active-directory/) Jeśli wymagane są dłuższe pola, dyrektywa [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) serwera proxy wymaga dostosowania. Wartość do zastosowania zależy od scenariusza. Aby uzyskać więcej informacji, zapoznaj się z dokumentacją serwera.

> [!WARNING]
> Nie zwiększaj wartości domyślnej, `LimitRequestFieldSize` chyba że jest to konieczne. Zwiększenie wartości zwiększa ryzyko przepełnienia buforu (przepełnienie) i ataków typu "odmowa usługi" (DoS) przez złośliwych użytkowników.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wymagania wstępne dla .NET Core w systemie Linux](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
