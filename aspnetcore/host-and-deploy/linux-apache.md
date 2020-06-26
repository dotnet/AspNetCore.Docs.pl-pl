---
title: Hostowanie ASP.NET Core w systemie Linux przy użyciu oprogramowania Apache
author: rick-anderson
description: Dowiedz się, jak skonfigurować Apache jako zwrotny serwer proxy w usłudze CentOS, aby przekierować ruch HTTP do aplikacji internetowej ASP.NET Core działającej w systemie Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 04/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 5efd84cc4b54c2ad21a7c038137fe68ee3a40f55
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403992"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a>Hostowanie ASP.NET Core w systemie Linux przy użyciu oprogramowania Apache

Autor [Shayne Boyer](https://github.com/spboyer)

Korzystając z tego przewodnika, Dowiedz się, jak skonfigurować [Apache](https://httpd.apache.org/) jako zwrotny serwer proxy w [CentOS 7](https://www.centos.org/) , aby przekierować ruch HTTP do aplikacji internetowej ASP.NET Core działającej na serwerze [Kestrel](xref:fundamentals/servers/kestrel) . [Rozszerzenie mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) i powiązane moduły tworzą zwrotny serwer proxy serwera.

## <a name="prerequisites"></a>Wymagania wstępne

* Serwer z systemem CentOS 7 z kontem użytkownika standardowego z uprawnieniami sudo.
* Zainstaluj środowisko uruchomieniowe platformy .NET Core na serwerze.
   1. Odwiedź [stronę pobieranie platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
   1. Wybierz najnowszą wersję programu .NET Core niebędącą podglądem.
   1. Pobierz najnowszą wersję środowiska uruchomieniowego bez podglądu w tabeli w obszarze **Uruchamianie aplikacji — środowisko uruchomieniowe**.
   1. Wybierz łącze **instrukcje dotyczące Menedżera pakietów** systemu Linux i postępuj zgodnie z instrukcjami CentOS.
* Istniejąca aplikacja ASP.NET Core.

W dowolnym momencie w przyszłości po uaktualnieniu struktury udostępnionej ponownie uruchom ASP.NET Core aplikacje hostowane przez serwer.

## <a name="publish-and-copy-over-the-app"></a>Publikowanie i kopiowanie aplikacji

Skonfiguruj aplikację dla [wdrożenia zależnego od platformy](/dotnet/core/deploying/#framework-dependent-deployments-fdd).

Jeśli aplikacja jest uruchamiana lokalnie i nie jest skonfigurowana do nawiązywania bezpiecznych połączeń (HTTPS), należy zastosować jedną z następujących metod:

* Skonfiguruj aplikację do obsługi bezpiecznych połączeń lokalnych. Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja protokołu HTTPS](#https-configuration) .
* Usuń `https://localhost:5001` (jeśli istnieje) z `applicationUrl` Właściwości we *właściwościach/launchSettings.jsw* pliku.

Uruchom [dotnet Publish](/dotnet/core/tools/dotnet-publish) ze środowiska programistycznego, aby spakować aplikację do katalogu (na przykład *bin/Release/ &lt; target_framework_moniker &gt; /Publish*), które można uruchomić na serwerze:

```dotnetcli
dotnet publish --configuration Release
```

Aplikację można również opublikować jako [samodzielne wdrożenie](/dotnet/core/deploying/#self-contained-deployments-scd) , jeśli wolisz, aby nie obsługiwać środowiska uruchomieniowego .NET Core na serwerze.

Skopiuj aplikację ASP.NET Core na serwer przy użyciu narzędzia, które integruje się z przepływem pracy organizacji (na przykład SCP, SFTP). Często można zlokalizować aplikacje sieci Web w katalogu *var* (na przykład *var/www/helloapp*).

> [!NOTE]
> W obszarze scenariusza wdrożenia produkcyjnego przepływ pracy ciągłej integracji wykonuje zadania publikowania aplikacji i kopiowania zasobów na serwer.

## <a name="configure-a-proxy-server"></a>Konfigurowanie serwera proxy

Zwrotny serwer proxy to typowa konfiguracja służąca do obsługi dynamicznych aplikacji sieci Web. Zwrotny serwer proxy przerywa żądanie HTTP i przekazuje go do aplikacji ASP.NET.

Serwer proxy, który przekazuje żądania klientów na inny serwer zamiast zaspokajać same żądania. Zwrotny serwer proxy przesyła do stałego miejsca docelowego, zazwyczaj w imieniu dowolnych klientów. W tym przewodniku program Apache jest skonfigurowany jako zwrotny serwer proxy uruchomiony na tym samym serwerze, na którym Kestrel obsługuje aplikację ASP.NET Core.

Ze względu na to, że żądania są przekazywane przez zwrotny serwer proxy, należy użyć [oprogramowania pośredniczącego "przesłane nagłówki](xref:host-and-deploy/proxy-load-balancer) " z pakietu [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) . Oprogramowanie pośredniczące aktualizuje `Request.Scheme` , używając `X-Forwarded-Proto` nagłówka, tak aby identyfikatory URI przekierowania i inne zasady zabezpieczeń działały prawidłowo.

Wszelkie składniki, które są zależne od schematu, takie jak uwierzytelnianie, generowanie linków, przekierowania i geolokalizacja, muszą być umieszczone po wywołaniu bezpośrednich nagłówków.

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

Wywołaj <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodę w górnej części `Startup.Configure` przed wywołaniem innego oprogramowania pośredniczącego. Skonfiguruj oprogramowanie pośredniczące do przesyłania dalej `X-Forwarded-For` `X-Forwarded-Proto` nagłówków i:

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

Jeśli wartość nie <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> jest określona dla oprogramowania pośredniczącego, domyślne nagłówki są do przodu `None` .

Serwery proxy uruchomione na adresach sprzężenia zwrotnego (127.0.0.0/8, [:: 1]), w tym standardowy adres localhost (127.0.0.1), są domyślnie zaufane. Jeśli inne zaufane serwery proxy lub sieci w organizacji obsługują żądania między Internetem a serwerem sieci Web, należy dodać je do listy <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> lub <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> z <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> . Poniższy przykład dodaje zaufany serwer proxy pod adresem IP 10.0.0.100 do przesyłanych nagłówków pośredniczących `KnownProxies` w programie `Startup.ConfigureServices` :

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/proxy-load-balancer>.

### <a name="install-apache"></a>Instalowanie oprogramowania Apache

Aktualizowanie pakietów CentOS do najnowszych stabilnych wersji:

```bash
sudo yum update -y
```

Zainstaluj serwer Apache Web Server w systemie CentOS za pomocą jednego `yum` polecenia:

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
> W tym przykładzie dane wyjściowe odzwierciedlają http. 86_64, ponieważ wersja CentOS 7 jest 64 bit. Aby sprawdzić, gdzie jest zainstalowany program Apache, uruchom `whereis httpd` polecenie w wierszu polecenia.

### <a name="configure-apache"></a>Konfiguruj Apache

Pliki konfiguracji dla oprogramowania Apache znajdują się w `/etc/httpd/conf.d/` katalogu. Każdy plik z rozszerzeniem *. conf* jest przetwarzany w kolejności alfabetycznej oprócz plików konfiguracji modułu w programie `/etc/httpd/conf.modules.d/` , które zawierają pliki konfiguracyjne niezbędne do załadowania modułów.

Utwórz plik konfiguracji o nazwie *helloapp. conf*dla aplikacji:

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

`VirtualHost`Blok może występować wiele razy, w co najmniej jednym pliku na serwerze. W poprzednim pliku konfiguracyjnym Apache akceptuje ruch publiczny na porcie 80. Domena `www.example.com` jest obsługiwana, a `*.example.com` alias jest rozpoznawany jako ta sama witryna sieci Web. Aby uzyskać więcej informacji, zobacz [Obsługa hosta wirtualnego opartego na nazwach](https://httpd.apache.org/docs/current/vhosts/name-based.html) . Żądania są przekazywane w katalogu głównym do portu 5000 serwera o wartości 127.0.0.1. W przypadku komunikacji dwukierunkowej `ProxyPass` i `ProxyPassReverse` są wymagane. Aby zmienić adres IP/port Kestrel, zobacz [Kestrel: Konfiguracja punktu końcowego](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!WARNING]
> Niepowodzenie określenia odpowiedniej [dyrektywy ServerName](https://httpd.apache.org/docs/current/mod/core.html#servername) w bloku **VirtualHost** uwidacznia aplikację pod kątem luk w zabezpieczeniach. Powiązanie symboli wieloznacznych z poddomeną (na przykład `*.example.com` ) nie ma znaczenia dla tego zagrożenia bezpieczeństwa, jeśli kontrolujesz całą domenę nadrzędną (w przeciwieństwie do `*.com` , który jest narażony). Aby uzyskać więcej informacji, zobacz [sekcję rfc7230-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .

Rejestrowanie można skonfigurować za `VirtualHost` pomocą `ErrorLog` dyrektyw i `CustomLog` . `ErrorLog`jest lokalizacją, w której serwer rejestruje błędy i `CustomLog` ustawia nazwę pliku dziennika oraz jego format. W tym przypadku jest to miejsce, w którym rejestrowane są informacje o żądaniu. Jeden wiersz dla każdego żądania.

Zapisz plik i przetestuj konfigurację. Jeśli wszystko kończy się, odpowiedź powinna być `Syntax [OK]` .

```bash
sudo service httpd configtest
```

Uruchom ponownie Apache:

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a>Monitorowanie aplikacji

Program Apache jest teraz skonfigurowany do przesyłania dalej żądań wysyłanych do `http://localhost:80` aplikacji ASP.NET Core działającej w usłudze Kestrel pod adresem `http://127.0.0.1:5000` . Program Apache nie jest jednak skonfigurowany do zarządzania procesem Kestrel. Użyj *systemu* i Utwórz plik usługi, aby uruchomić i monitorować podstawową aplikację sieci Web. *system* to system inicjujący, który udostępnia wiele zaawansowanych funkcji uruchamiania, zatrzymywania i zarządzania procesami.

### <a name="create-the-service-file"></a>Utwórz plik usługi

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

W poprzednim przykładzie użytkownik zarządzający usługą jest określony przez `User` opcję. Użytkownik ( `apache` ) musi istnieć i mieć właściwy własność plików aplikacji.

Użyj `TimeoutStopSec` , aby skonfigurować czas oczekiwania na wyłączenie aplikacji po odebraniu początkowego sygnału przerwania. Jeśli aplikacja nie zostanie zamknięta w tym okresie, SIGKILL jest wystawiony, aby zakończyć działanie aplikacji. Podaj wartość jako bezjednostkowe sekundy (na przykład `150` ), wartość przedziału czasu (na przykład `2min 30s` ) lub `infinity` Aby wyłączyć limit czasu. `TimeoutStopSec`Wartością domyślną jest wartość `DefaultTimeoutStopSec` w pliku konfiguracji Menedżera (*systemd-system. conf*, *System. conf. d*, *systemed-User. conf*, *User. conf. d*). Domyślny limit czasu dla większości dystrybucji wynosi 90 sekund.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Niektóre wartości (na przykład parametry połączenia SQL) muszą zostać zmienione dla dostawców konfiguracji, aby odczytywać zmienne środowiskowe. Użyj poniższego polecenia, aby wygenerować poprawną wartość ucieczki do użycia w pliku konfiguracji:

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

Separatory dwukropek ( `:` ) nie są obsługiwane w nazwach zmiennych środowiskowych. Użyj podwójnego podkreślenia ( `__` ) zamiast dwukropka. [Dostawca konfiguracji zmiennych środowiskowych](xref:fundamentals/configuration/index#environment-variables-configuration-provider) konwertuje podwójne podkreślenie na dwukropek, gdy zmienne środowiskowe są odczytywane w konfiguracji. W poniższym przykładzie klucz parametrów połączenia `ConnectionStrings:DefaultConnection` jest ustawiany na plik definicji usługi jako `ConnectionStrings__DefaultConnection` :

::: moniker-end
::: moniker range="< aspnetcore-3.0"

Separatory dwukropek ( `:` ) nie są obsługiwane w nazwach zmiennych środowiskowych. Użyj podwójnego podkreślenia ( `__` ) zamiast dwukropka. [Dostawca konfiguracji zmiennych środowiskowych](xref:fundamentals/configuration/index#environment-variables) konwertuje podwójne podkreślenie na dwukropek, gdy zmienne środowiskowe są odczytywane w konfiguracji. W poniższym przykładzie klucz parametrów połączenia `ConnectionStrings:DefaultConnection` jest ustawiany na plik definicji usługi jako `ConnectionStrings__DefaultConnection` :

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

Zapisz plik i Włącz usługę:

```bash
sudo systemctl enable kestrel-helloapp.service
```

Uruchom usługę i sprawdź, czy jest uruchomiona:

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

Gdy zwrotny serwer proxy został skonfigurowany i Kestrel zarządzany za pomocą *systemu*, aplikacja sieci Web jest w pełni skonfigurowana i można uzyskać do niej dostęp z przeglądarki na komputerze lokalnym pod adresem `http://localhost` . Sprawdzanie nagłówków odpowiedzi, nagłówek **serwera** wskazuje, że aplikacja ASP.NET Core jest obsługiwana przez Kestrel:

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Wyświetlanie dzienników

Ponieważ aplikacja sieci Web używająca Kestrel jest zarządzana przy użyciu *systemu*, zdarzenia i procesy są rejestrowane w scentralizowanym dzienniku. Ten dziennik zawiera jednak wpisy dla wszystkich usług i procesów zarządzanych przez *system*. Aby wyświetlić `kestrel-helloapp.service` konkretne elementy, użyj następującego polecenia:

```bash
sudo journalctl -fu kestrel-helloapp.service
```

W przypadku filtrowania czasu określ opcje czasu za pomocą polecenia. Na przykład użyj, `--since today` Aby odfiltrować bieżący dzień lub `--until 1 hour ago` zobaczyć poprzednią godzinę. Aby uzyskać więcej informacji, zobacz [stronę Man for journalctl](https://www.unix.com/man-page/centos/1/journalctl/).

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a>Ochrona danych

[ASP.NET Core stosu ochrony danych](xref:security/data-protection/introduction) jest używany przez kilka ASP.NET Core [middlewares](xref:fundamentals/middleware/index), w tym uwierzytelnianie pośredniczące uwierzytelniania (np. Oprogramowanie pośredniczące plików cookie) i ochrona za żądania między lokacjami (CSRF). Nawet jeśli interfejsy API ochrony danych nie są wywoływane przez kod użytkownika, należy skonfigurować ochronę danych w celu utworzenia trwałego [magazynu kluczy](xref:security/data-protection/implementation/key-management)kryptograficznych. Jeśli ochrona danych nie jest skonfigurowana, klucze są przechowywane w pamięci i usuwane po ponownym uruchomieniu aplikacji.

Jeśli pierścień kluczy jest przechowywany w pamięci po ponownym uruchomieniu aplikacji:

* Wszystkie tokeny uwierzytelniania na podstawie plików cookie są unieważnione.
* Użytkownicy muszą ponownie zalogować się przy następnym żądaniu.
* Nie można już odszyfrować żadnych danych chronionych za pomocą dzwonka klucza. Może to obejmować [tokeny CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) [ASP.NET Core i pliki cookie MVC TempData](xref:fundamentals/app-state#tempdata).

Aby skonfigurować ochronę danych w celu utrwalenia i szyfrowania pierścienia kluczy, zobacz:

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a>Zabezpieczanie aplikacji

### <a name="configure-firewall"></a>Konfigurowanie zapory

*Zapora* jest demonem dynamicznym do zarządzania zaporą z obsługą stref sieciowych. Porty i filtrowanie pakietów nadal mogą być zarządzane przez dołączenie iptables. *Zapora* powinna być instalowana domyślnie. `yum`można go użyć, aby zainstalować pakiet lub sprawdzić jego instalację.

```bash
sudo yum install firewalld -y
```

Służy `firewalld` do otwierania tylko portów wymaganych dla aplikacji. W takim przypadku używane są porty 80 i 443. Następujące polecenia trwale ustawiają porty 80 i 443, aby otworzyć:

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

Załaduj ponownie ustawienia zapory. Sprawdź dostępne usługi i porty w strefie domyślnej. Opcje są dostępne przez sprawdzenie `firewall-cmd -h` .

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

### <a name="https-configuration"></a>Konfiguracja protokołu HTTPS

**Konfigurowanie aplikacji do połączeń lokalnych (HTTPS)**

Polecenie [dotnet Run](/dotnet/core/tools/dotnet-run) używa *Właściwości/launchSettings.jsaplikacji w* pliku, co umożliwia skonfigurowanie aplikacji do nasłuchiwania na adresach URL dostarczonych przez `applicationUrl` Właściwość (na przykład `https://localhost:5001;http://localhost:5000` ).

Skonfiguruj aplikację do korzystania z certyfikatu podczas opracowywania dla `dotnet run` polecenia lub środowiska programistycznego (F5 lub CTRL + F5 w Visual Studio Code), korzystając z jednej z następujących metod:

* [Zastąp domyślny certyfikat z konfiguracji](xref:fundamentals/servers/kestrel#configuration) (*zalecane*)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

**Konfigurowanie zwrotnego serwera proxy dla połączeń zabezpieczonych za pośrednictwem protokołu HTTPS**

Aby skonfigurować Apache for HTTPS, używany jest moduł *mod_ssl* . Po zainstalowaniu modułu *http* zainstalowano również moduł *mod_ssl* . Jeśli nie została zainstalowana, użyj, `yum` Aby dodać ją do konfiguracji.

```bash
sudo yum install mod_ssl
```

Aby wymusić protokół HTTPS, zainstaluj `mod_rewrite` moduł, aby włączyć ponowne zapisywanie adresów URL:

```bash
sudo yum install mod_rewrite
```

Zmodyfikuj plik *helloapp. conf* , aby umożliwić ponowne zapisywanie adresów URL i bezpieczną komunikację na porcie 443:

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
> W tym przykładzie użyto certyfikatu wygenerowanego lokalnie. **SSLCertificateFile** powinien być podstawowym plikiem certyfikatu dla nazwy domeny. **SSLCertificateKeyFile** powinien być plikiem klucza generowanym podczas tworzenia CSR. **SSLCertificateChainFile** powinien być pośrednim plikiem certyfikatu (jeśli istnieje), który został dostarczony przez urząd certyfikacji.

Zapisz plik i przetestuj konfigurację:

```bash
sudo service httpd configtest
```

Uruchom ponownie Apache:

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a>Dodatkowe sugestie Apache

### <a name="restart-apps-with-shared-framework-updates"></a>Ponowne uruchamianie aplikacji przy użyciu aktualizacji platformy udostępnionej

Po uaktualnieniu platformy udostępnionej na serwerze uruchom ponownie ASP.NET Core aplikacje hostowane przez serwer.

### <a name="additional-headers"></a>Dodatkowe nagłówki

Aby zabezpieczyć przed złośliwymi atakami, istnieje kilka nagłówków, które należy zmodyfikować lub dodać. Upewnij się, że `mod_headers` moduł jest zainstalowany:

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a>Zabezpiecz ataki Apache from clickjacking

[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), znana także jako *atak polegająca na zaskarżeniu interfejsu użytkownika*, to złośliwy atak polegający na tym, że odwiedzanie witryny sieci Web jest trudne do kliknięcia linku lub przycisku na innej stronie niż aktualnie odwiedzane. Użyj `X-FRAME-OPTIONS` , aby zabezpieczyć lokację.

Aby wyeliminować ataki clickjacking:

1. Edytuj plik *http. conf* :

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   Dodaj wiersz `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.
1. Zapisz plik.
1. Uruchom ponownie Apache.

#### <a name="mime-type-sniffing"></a>Wykrywanie typu MIME

`X-Content-Type-Options`Nagłówek uniemożliwia Internet Explorer z *wykrywania MIME* (określenie pliku `Content-Type` z zawartości pliku). Jeśli serwer ustawi `Content-Type` nagłówek `text/html` z `nosniff` zestawem opcji, program Internet Explorer renderuje zawartość `text/html` niezależnie od zawartości pliku.

Edytuj plik *http. conf* :

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

Dodaj wiersz `Header set X-Content-Type-Options "nosniff"`. Zapisz plik. Uruchom ponownie Apache.

### <a name="load-balancing"></a>Równoważenie obciążenia

W tym przykładzie przedstawiono sposób konfigurowania i konfigurowania oprogramowania Apache w systemie CentOS 7 i Kestrel na tym samym komputerze wystąpienia. Aby nie mieć single point of failure; Używanie *mod_proxy_balancer* i modyfikowanie **VirtualHost** umożliwi zarządzanie wieloma wystąpieniami aplikacji sieci Web za serwerem Apache proxy.

```bash
sudo yum install mod_proxy_balancer
```

W pliku konfiguracyjnym przedstawionym poniżej dodatkowe wystąpienie `helloapp` jest skonfigurowane do uruchamiania na porcie 5001. Sekcja *proxy* jest ustawiana z konfiguracją modułu równoważenia obciążenia z dwoma elementami członkowskimi w celu zrównoważenia *byrequests*.

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

### <a name="rate-limits"></a>Limity szybkości

Za pomocą *mod_ratelimit*, który jest uwzględniony w module *http* , przepustowość klientów może być ograniczona:

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

### <a name="long-request-header-fields"></a>Długie pola nagłówka żądania

Ustawienia domyślne serwera proxy zwykle ograniczają pola nagłówka żądania do 8 190 bajtów. Aplikacja może wymagać pól, które są dłuższe niż domyślne (na przykład aplikacje, które używają [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)). Jeśli są wymagane dłuższe pola, dyrektywa [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) serwera proxy wymaga korekty. Wartość, która ma zostać zastosowana, zależy od scenariusza. Aby uzyskać więcej informacji, zapoznaj się z dokumentacją serwera.

> [!WARNING]
> Nie należy zwiększać wartości domyślnej, `LimitRequestFieldSize` chyba że jest to konieczne. Zwiększenie wartości zwiększa ryzyko ataków przepełnienia buforu (przepełnienie) i ataki typu "odmowa usługi" (DoS) przez złośliwych użytkowników.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wymagania wstępne dotyczące programu .NET Core w systemie Linux](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
