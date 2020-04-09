---
title: Implementacja serwera www HTTP.sys w ASP.NET Core
author: rick-anderson
description: Dowiedz się więcej o HTTP.sys, serwerze sieci web dla ASP.NET Core w systemie Windows. Oparty na sterowniku w trybie jądra HTTP.sys, HTTP.sys jest alternatywą dla Kestrel, który może być używany do bezpośredniego połączenia z Internetem bez usług IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 3e858a974d6a5c008969c3c51a507880cc25a7ff
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666923"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a>Implementacja serwera www HTTP.sys w ASP.NET Core

Przez [Tom Dykstra](https://github.com/tdykstra) i [Chris Ross](https://github.com/Tratcher)

::: moniker range=">= aspnetcore-3.1"

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) to [serwer sieci web dla ASP.NET Core,](xref:fundamentals/servers/index) który działa tylko w systemie Windows. HTTP.sys jest alternatywą dla serwera [Kestrel](xref:fundamentals/servers/kestrel) i oferuje pewne funkcje, których Kestrel nie zapewnia.

> [!IMPORTANT]
> Protokół HTTP.sys nie jest zgodny z [modułem ASP.NET Core](xref:host-and-deploy/aspnet-core-module) module i nie może być używany z programami IIS lub IIS Express.

Http.sys obsługuje następujące funkcje:

* [Uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth)
* Udostępnianie portów
* HTTPS z SNI
* Protokół HTTP/2 przez protokół TLS (Windows 10 lub nowszy)
* Bezpośrednia transmisja plików
* Buforowanie odpowiedzi
* WebSockets (Windows 8 lub nowsze)

Obsługiwane wersje systemu Windows:

* Windows 7 lub nowsze
* Windows Server 2008 R2 lub nowszy

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>Kiedy używać protokołu HTTP.sys

HTTP.sys jest przydatne w przypadku wdrożeń, w których:

* Istnieje potrzeba udostępnienia serwera bezpośrednio do Internetu bez korzystania z usług IIS.

  ![HTTP.sys komunikuje się bezpośrednio z Internetem](httpsys/_static/httpsys-to-internet.png)

* Wdrożenie wewnętrzne wymaga funkcji niedostępnej w Kestrel, takiej jak [Uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth).

  ![HTTP.sys komunikuje się bezpośrednio z siecią wewnętrzną](httpsys/_static/httpsys-to-internal.png)

HTTP.sys to dojrzała technologia, która chroni przed wieloma typami ataków i zapewnia niezawodność, bezpieczeństwo i skalowalność w pełni funkcjonowego serwera sieci Web. Usługi IIS działa jako odbiornik HTTP na górze HTTP.sys.

## <a name="http2-support"></a>Obsługa protokołu HTTP/2

[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest włączony dla aplikacji ASP.NET Core, jeśli spełnione są następujące wymagania podstawowe:

* Windows Server 2016/Windows 10 lub nowsze
* Połączenie [negocjacji protokołu warstwy aplikacji (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)
* Połączenie TLS 1.2 lub nowsze

Jeśli zostanie nawiązane połączenie HTTP/2, [httpRequest.protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) zgłasza `HTTP/2`.

Protokół HTTP/2 jest domyślnie włączony. Jeśli połączenie HTTP/2 nie zostanie nawiązane, połączenie powróci do protokołu HTTP/1.1. W przyszłej wersji systemu Windows dostępne będą flagi konfiguracji HTTP/2, w tym możliwość wyłączenia protokołu HTTP/2 za pomocą protokołu HTTP.sys.

## <a name="kernel-mode-authentication-with-kerberos"></a>Uwierzytelnianie w trybie jądra za pomocą protokołu Kerberos

Http.sys deleguje do uwierzytelniania w trybie jądra za pomocą protokołu uwierzytelniania Kerberos. Uwierzytelnianie w trybie użytkownika nie jest obsługiwane w protokołach Kerberos i HTTP.sys. Konto komputera musi służyć do odszyfrowania tokenu/biletu Protokołu Kerberos uzyskanego z usługi Active Directory i przekazanego przez klienta do serwera w celu uwierzytelnienia użytkownika. Zarejestruj nazwę główną usługi (SPN) dla hosta, a nie użytkownika aplikacji.

## <a name="how-to-use-httpsys"></a>Jak korzystać z protokołu HTTP.sys

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Skonfiguruj aplikację core ASP.NET do używania protokołu HTTP.sys

Wywołanie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> metody rozszerzenia podczas tworzenia hosta, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>określając wszelkie wymagane . W poniższym przykładzie ustawia się opcje wartości domyślnych:

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

Dodatkowa konfiguracja HTTP.sys jest obsługiwana przez [ustawienia rejestru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

**Opcje HTTP.sys**

| Właściwość | Opis | Domyślne |
| -------- | ----------- | :-----: |
| [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Kontrolować, czy synchroniczne wejście/wyjście `HttpContext.Request.Body` jest `HttpContext.Response.Body`dozwolone dla i . | `false` |
| [Uwierzytelnianie.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Zezwalaj na żądania anonimowe. | `true` |
| [Uwierzytelnianie.Schematy](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Określ dozwolone schematy uwierzytelniania. Może być modyfikowany w dowolnym momencie przed utylizowanym odbiornikiem. Wartości są dostarczane przez [authenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate` `None`, i `NTLM`. | `None` |
| [Włącz Buforowanie reakcji](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Spróbuj buforować [w trybie jądra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) dla odpowiedzi z kwalifikującymi się nagłówkami. Odpowiedź może nie `Set-Cookie` `Vary`zawierać `Pragma` , lub nagłówki. Musi zawierać `Cache-Control` nagłówek, który `public` jest i `shared-max-age` `max-age` albo lub `Expires` wartość lub nagłówka. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Maksymalna liczba równoczesnych akceptuje. | 5 &times; [Środowisko.<br> Liczba procesorów](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Maksymalna liczba równoczesnych połączeń do zaakceptowania. Użyj `-1` dla nieskończonych. Służy `null` do używania ustawienia całego rejestru. | `null`<br>(w całej maszynie<br>ustawienia) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Zobacz <a href="#maxrequestbodysize">MaxRequestBodySize</a> sekcji. | 3000000 bajtów<br>(~28,6 MB) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Maksymalna liczba żądań, które mogą być umieszczane w kolejce. | 1000 |
| `RequestQueueMode` | Wskazuje to, czy serwer jest odpowiedzialny za tworzenie i konfigurowanie kolejki żądań, czy powinien dołączyć do istniejącej kolejki.<br>Większość istniejących opcji konfiguracji nie ma zastosowania podczas dołączania do istniejącej kolejki. | `RequestQueueMode.Create` |
| `RequestQueueName` | Nazwa kolejki żądań HTTP.sys. | `null`(Kolejka anonimowa) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Wskaż, czy treść odpowiedzi zapisuje, że nie z powodu rozłączenia klienta należy zgłaszać wyjątki lub zakończyć normalnie. | `false`<br>(normalnie) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Uwidacznianie konfiguracji <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> HTTP.sys, która może być również skonfigurowana w rejestrze. Postępuj zgodnie z łączami interfejsu API, aby dowiedzieć się więcej o każdym ustawieniu, w tym wartościach domyślnych:<ul><li>[TimeoutManager.DrainEntityCzasciał dozwolony](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; dla interfejsu API serwera HTTP do opróżnienia treści jednostki w połączeniu Keep-Alive.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Czas dozwolony dla treści jednostki żądania do przybycia.</li><li>[Czas timeoutmanager.headerWit](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; czas dozwolony dla interfejsu API serwera HTTP do analizować nagłówek żądania.</li><li>[Czas połączenia timeoutmanager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; dozwolony dla połączenia bezczynnego.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; Minimalna szybkość wysyłania odpowiedzi.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Czas dozwolony dla żądania, aby pozostać w kolejce żądań, zanim aplikacja go odbierze.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> Określ, aby zarejestrować się w http.sys. Najbardziej przydatne jest [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), który jest używany do dodawania prefiksu do kolekcji. Mogą one zostać zmodyfikowane w dowolnym momencie przed utylizowanym odbiornikiem. |  |

<a name="maxrequestbodysize"></a>

**Rozmiar MaxRequestBodySize**

Maksymalny dozwolony rozmiar dowolnego treści żądania w bajtach. Po `null`ustawieniu, maksymalny rozmiar treści żądania jest nieograniczony. Ten limit nie ma wpływu na uaktualnione połączenia, które są zawsze nieograniczone.

Zalecaną metodą zastąpienia limitu w ASP.NET podstawowej aplikacji MVC `IActionResult` dla pojedynczej <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> jest użycie atrybutu w metodzie akcji:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Wyjątek jest zgłaszany, jeśli aplikacja próbuje skonfigurować limit żądania po aplikacji rozpoczął odczytywanie żądania. Właściwości `IsReadOnly` można użyć do wskazania, czy `MaxRequestBodySize` właściwość jest w stanie tylko do odczytu, co oznacza, że jest za późno, aby skonfigurować limit.

Jeśli aplikacja powinna zastąpić <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> na żądanie, <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>użyj:

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Jeśli używasz programu Visual Studio, upewnij się, że aplikacja nie jest skonfigurowana do uruchamiania usługi IIS lub IIS Express.

W programie Visual Studio domyślny profil uruchamiania jest dla usługi IIS Express. Aby uruchomić projekt jako aplikację konsoli, ręcznie zmień wybrany profil, jak pokazano na poniższym zstrząśnięciu z ekranu:

![Wybieranie profilu aplikacji konsoli](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Konfigurowanie systemu Windows Server

1. Określ porty do otwarcia aplikacji i użyj [Zapory systemu Windows](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) lub polecenia cmdlet [Programu New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell, aby otworzyć porty zapory, aby umożliwić ruchowi dostęp do pliku HTTP.sys. W następujących poleceniach i konfiguracji aplikacji jest używany port 443.

1. Podczas wdrażania na maszynie wirtualnej platformy Azure otwórz porty w [sieciowej grupie zabezpieczeń](/azure/virtual-machines/windows/nsg-quickstart-portal). W następujących poleceniach i konfiguracji aplikacji jest używany port 443.

1. W razie potrzeby uzyskaj i zainstaluj certyfikaty X.509.

   W systemie Windows utwórz certyfikaty z podpisem własnym przy użyciu [polecenia cmdlet Programu PowerShell new-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). Aby zapoznać się z nieobsługiwionym przykładem, zobacz [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Zainstaluj certyfikaty z podpisem własnym lub certyfikatem urzędu certyfikacji w magazynie **Local Machine** > **Personal** serwera.

1. Jeśli aplikacja jest [wdrożeniem zależnym od struktury,](/dotnet/core/deploying/#framework-dependent-deployments-fdd)zainstaluj .NET Core, .NET Framework lub oba (jeśli aplikacja jest aplikacją .NET Core kierowaną na platformę .NET Framework).

   * **.NET Core** &ndash; Jeśli aplikacja wymaga programu .NET Core, należy uzyskać i uruchomić instalator **.NET Core Runtime** z programu [.NET Core Downloads](https://dotnet.microsoft.com/download). Nie instaluj pełnego pakietu SDK na serwerze.
   * **Program .NET Framework** &ndash; Jeśli aplikacja wymaga programu .NET Framework, zobacz [przewodnik po instalacji programu .NET Framework](/dotnet/framework/install/). Zainstaluj wymaganą platformę .NET Framework. Instalator najnowszej platformy .NET Framework jest dostępny na stronie [Pliki do pobrania .NET Core.](https://dotnet.microsoft.com/download)

   Jeśli aplikacja jest [samodzielnym wdrożeniem,](/dotnet/core/deploying/#self-contained-deployments-scd)aplikacja zawiera środowisko wykonawcze w swoim wdrożeniu. Na serwerze nie jest wymagana żadna instalacja struktury.

1. Skonfiguruj adresy URL i porty w aplikacji.

   Domyślnie ASP.NET Core wiąże się `http://localhost:5000`z programem . Aby skonfigurować prefiksy i porty adresów URL, dostępne są następujące opcje:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * `urls`argument wiersza polecenia
   * `ASPNETCORE_URLS`zmienna środowiskowa
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   Poniższy przykład kodu pokazuje, jak używać <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> z `10.0.0.4` lokalnym adresem IP serwera na porcie 443:

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   Zaletą `UrlPrefixes` jest to, że komunikat o błędzie jest generowany natychmiast dla nieprawidłowo sformatowanych prefiksów.

   Ustawienia w `UrlPrefixes` ustawieniach `UseUrls` / `urls` / `ASPNETCORE_URLS` zastępowania. W związku z `UseUrls`tym `urls`zaletą `ASPNETCORE_URLS` , i zmienną środowiskową jest to, że łatwiej jest przełączać się między Kestrel i HTTP.sys.

   Http.sys używa [formatów ciągów URLPrefix interfejsu API serwera HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).

   > [!WARNING]
   > Wiązania wieloznaczne najwyższego`http://*:80/` `http://+:80`poziomu ( i ) **nie** powinny być używane. Powiązania symboli wieloznacznych najwyższego poziomu tworzą luki w zabezpieczeniach aplikacji. Dotyczy to zarówno silnych, jak i słabych symboli wieloznacznych. Użyj jawnych nazw hostów lub adresów IP, a nie symboli wieloznacznych. Powiązanie symboli wieloznacznych `*.mysub.com`poddomeny (na przykład) nie stanowi zagrożenia dla `*.com`bezpieczeństwa, jeśli kontrolujesz całą domenę nadrzędną (w przeciwieństwie do , która jest narażona). Aby uzyskać więcej informacji, zobacz [RFC 7230: Sekcja 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Preregister prefiksy adresów URL na serwerze.

   Wbudowanym narzędziem do konfigurowania pliku HTTP.sys jest *plik netsh.exe*. *netsh.exe* służy do rezerwowania prefiksów adresów URL i przypisywania certyfikatów X.509. Narzędzie wymaga uprawnień administratora.

   Użyj narzędzia *netsh.exe,* aby zarejestrować adresy URL aplikacji:

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`&ndash; W pełni kwalifikowany jednolity lokalizator zasobów (URL). Nie używaj powiązania z symbolami wieloznacznych. Użyj prawidłowej nazwy hosta lub lokalnego adresu IP. *Adres URL musi zawierać końcowe ukośnik.*
   * `<USER>`&ndash; Określa nazwę użytkownika lub grupy użytkowników.

   W poniższym przykładzie lokalny adres IP `10.0.0.4`serwera to:

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Gdy adres URL jest zarejestrowany, `URL reservation successfully added`narzędzie odpowiada za pomocą pliku .

   Aby usunąć zarejestrowany adres `delete urlacl` URL, użyj polecenia:

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. Zarejestruj certyfikaty X.509 na serwerze.

   Użyj narzędzia *netsh.exe,* aby zarejestrować certyfikaty dla aplikacji:

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`&ndash; Określa lokalny adres IP powiązania. Nie używaj powiązania z symbolami wieloznacznych. Użyj prawidłowego adresu IP.
   * `<PORT>`&ndash; Określa port powiązania.
   * `<THUMBPRINT>`&ndash; Odcisk palca certyfikatu X.509.
   * `<GUID>`&ndash; Identyfikator GUID wygenerowany przez dewelopera do reprezentowania aplikacji w celach informacyjnych.

   W celach informacyjnych należy przechowywać identyfikator GUID w aplikacji jako tag pakietu:

   * W programie Visual Studio:
     * Otwórz właściwości projektu aplikacji, klikając prawym przyciskiem myszy aplikację w **Eksploratorze rozwiązań** i wybierając pozycję **Właściwości**.
     * Wybierz kartę **Pakiet.**
     * Wprowadź identyfikator GUID utworzony w polu **Znaczniki.**
   * Jeśli nie jest używany program Visual Studio:
     * Otwórz plik projektu aplikacji.
     * Dodaj `<PackageTags>` właściwość do nowej `<PropertyGroup>` lub istniejącej z utworzonym identyfikatorem GUID:

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   W poniższym przykładzie:

   * Lokalny adres IP serwera `10.0.0.4`to .
   * Generator identyfikatorów GUID `appid` online zapewnia wartość.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Gdy certyfikat jest zarejestrowany, narzędzie `SSL Certificate successfully added`odpowiada za pomocą pliku .

   Aby usunąć rejestrację certyfikatu, użyj `delete sslcert` polecenia:

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Dokumentacja referencyjna dla *netsh.exe:*

   * [Polecenia Netsh dla protokołu transferu hipertekstowego (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)
   * [Ciągi UrlPrefix](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. Uruchom aplikację.

   Uprawnienia administratora nie są wymagane do uruchamiania aplikacji podczas powiązania z localhost przy użyciu protokołu HTTP (nie HTTPS) z numerem portu większym niż 1024. W przypadku innych konfiguracji (na przykład przy użyciu lokalnego adresu IP lub powiązania z portem 443) uruchom aplikację z uprawnieniami administratora.

   Aplikacja odpowiada na publiczny adres IP serwera. W tym przykładzie serwer jest osiągany z Internetu `104.214.79.47`pod jego publicznym adresem IP .

   Certyfikat dewelopera jest używany w tym przykładzie. Strona ładuje się bezpiecznie po pominięciu ostrzeżenia o niezaufanym certyfikacie przeglądarki.

   ![Okno przeglądarki z załadowaną stroną Indeks aplikacji](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scenariusze serwera proxy i modułu równoważenia obciążenia

W przypadku aplikacji obsługiwanych przez http.sys, które współdziałają z żądaniami z Internetu lub sieci firmowej, dodatkowa konfiguracja może być wymagana podczas hostowania za serwerami proxy i modułami równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ASP.NET Core do pracy z serwerami proxy i modułami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Włączanie uwierzytelniania systemu Windows za pomocą protokołu HTTP.sys](xref:security/authentication/windowsauth#httpsys)
* [Interfejs API serwera HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [repozytorium aspnet/HttpSysServer GitHub (kod źródłowy)](https://github.com/aspnet/HttpSysServer/)
* [Host](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) to [serwer sieci web dla ASP.NET Core,](xref:fundamentals/servers/index) który działa tylko w systemie Windows. HTTP.sys jest alternatywą dla serwera [Kestrel](xref:fundamentals/servers/kestrel) i oferuje pewne funkcje, których Kestrel nie zapewnia.

> [!IMPORTANT]
> Protokół HTTP.sys nie jest zgodny z [modułem ASP.NET Core](xref:host-and-deploy/aspnet-core-module) module i nie może być używany z programami IIS lub IIS Express.

Http.sys obsługuje następujące funkcje:

* [Uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth)
* Udostępnianie portów
* HTTPS z SNI
* Protokół HTTP/2 przez protokół TLS (Windows 10 lub nowszy)
* Bezpośrednia transmisja plików
* Buforowanie odpowiedzi
* WebSockets (Windows 8 lub nowsze)

Obsługiwane wersje systemu Windows:

* Windows 7 lub nowsze
* Windows Server 2008 R2 lub nowszy

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>Kiedy używać protokołu HTTP.sys

HTTP.sys jest przydatne w przypadku wdrożeń, w których:

* Istnieje potrzeba udostępnienia serwera bezpośrednio do Internetu bez korzystania z usług IIS.

  ![HTTP.sys komunikuje się bezpośrednio z Internetem](httpsys/_static/httpsys-to-internet.png)

* Wdrożenie wewnętrzne wymaga funkcji niedostępnej w Kestrel, takiej jak [Uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth).

  ![HTTP.sys komunikuje się bezpośrednio z siecią wewnętrzną](httpsys/_static/httpsys-to-internal.png)

HTTP.sys to dojrzała technologia, która chroni przed wieloma typami ataków i zapewnia niezawodność, bezpieczeństwo i skalowalność w pełni funkcjonowego serwera sieci Web. Usługi IIS działa jako odbiornik HTTP na górze HTTP.sys.

## <a name="http2-support"></a>Obsługa protokołu HTTP/2

[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest włączony dla aplikacji ASP.NET Core, jeśli spełnione są następujące wymagania podstawowe:

* Windows Server 2016/Windows 10 lub nowsze
* Połączenie [negocjacji protokołu warstwy aplikacji (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)
* Połączenie TLS 1.2 lub nowsze

Jeśli zostanie nawiązane połączenie HTTP/2, [httpRequest.protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) zgłasza `HTTP/2`.

Protokół HTTP/2 jest domyślnie włączony. Jeśli połączenie HTTP/2 nie zostanie nawiązane, połączenie powróci do protokołu HTTP/1.1. W przyszłej wersji systemu Windows dostępne będą flagi konfiguracji HTTP/2, w tym możliwość wyłączenia protokołu HTTP/2 za pomocą protokołu HTTP.sys.

## <a name="kernel-mode-authentication-with-kerberos"></a>Uwierzytelnianie w trybie jądra za pomocą protokołu Kerberos

Http.sys deleguje do uwierzytelniania w trybie jądra za pomocą protokołu uwierzytelniania Kerberos. Uwierzytelnianie w trybie użytkownika nie jest obsługiwane w protokołach Kerberos i HTTP.sys. Konto komputera musi służyć do odszyfrowania tokenu/biletu Protokołu Kerberos uzyskanego z usługi Active Directory i przekazanego przez klienta do serwera w celu uwierzytelnienia użytkownika. Zarejestruj nazwę główną usługi (SPN) dla hosta, a nie użytkownika aplikacji.

## <a name="how-to-use-httpsys"></a>Jak korzystać z protokołu HTTP.sys

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Skonfiguruj aplikację core ASP.NET do używania protokołu HTTP.sys

Wywołanie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> metody rozszerzenia podczas tworzenia hosta, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>określając wszelkie wymagane . W poniższym przykładzie ustawia się opcje wartości domyślnych:

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

Dodatkowa konfiguracja HTTP.sys jest obsługiwana przez [ustawienia rejestru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

**Opcje HTTP.sys**

| Właściwość | Opis | Domyślne |
| -------- | ----------- | :-----: |
| [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Kontrolować, czy synchroniczne wejście/wyjście `HttpContext.Request.Body` jest `HttpContext.Response.Body`dozwolone dla i . | `false` |
| [Uwierzytelnianie.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Zezwalaj na żądania anonimowe. | `true` |
| [Uwierzytelnianie.Schematy](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Określ dozwolone schematy uwierzytelniania. Może być modyfikowany w dowolnym momencie przed utylizowanym odbiornikiem. Wartości są dostarczane przez [authenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate` `None`, i `NTLM`. | `None` |
| [Włącz Buforowanie reakcji](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Spróbuj buforować [w trybie jądra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) dla odpowiedzi z kwalifikującymi się nagłówkami. Odpowiedź może nie `Set-Cookie` `Vary`zawierać `Pragma` , lub nagłówki. Musi zawierać `Cache-Control` nagłówek, który `public` jest i `shared-max-age` `max-age` albo lub `Expires` wartość lub nagłówka. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Maksymalna liczba równoczesnych akceptuje. | 5 &times; [Środowisko.<br> Liczba procesorów](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Maksymalna liczba równoczesnych połączeń do zaakceptowania. Użyj `-1` dla nieskończonych. Służy `null` do używania ustawienia całego rejestru. | `null`<br>(w całej maszynie<br>ustawienia) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Zobacz <a href="#maxrequestbodysize">MaxRequestBodySize</a> sekcji. | 3000000 bajtów<br>(~28,6 MB) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Maksymalna liczba żądań, które mogą być umieszczane w kolejce. | 1000 |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Wskaż, czy treść odpowiedzi zapisuje, że nie z powodu rozłączenia klienta należy zgłaszać wyjątki lub zakończyć normalnie. | `false`<br>(normalnie) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Uwidacznianie konfiguracji <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> HTTP.sys, która może być również skonfigurowana w rejestrze. Postępuj zgodnie z łączami interfejsu API, aby dowiedzieć się więcej o każdym ustawieniu, w tym wartościach domyślnych:<ul><li>[TimeoutManager.DrainEntityCzasciał dozwolony](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; dla interfejsu API serwera HTTP do opróżnienia treści jednostki w połączeniu Keep-Alive.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Czas dozwolony dla treści jednostki żądania do przybycia.</li><li>[Czas timeoutmanager.headerWit](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; czas dozwolony dla interfejsu API serwera HTTP do analizować nagłówek żądania.</li><li>[Czas połączenia timeoutmanager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; dozwolony dla połączenia bezczynnego.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; Minimalna szybkość wysyłania odpowiedzi.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Czas dozwolony dla żądania, aby pozostać w kolejce żądań, zanim aplikacja go odbierze.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> Określ, aby zarejestrować się w http.sys. Najbardziej przydatne jest [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), który jest używany do dodawania prefiksu do kolekcji. Mogą one zostać zmodyfikowane w dowolnym momencie przed utylizowanym odbiornikiem. |  |

<a name="maxrequestbodysize"></a>

**Rozmiar MaxRequestBodySize**

Maksymalny dozwolony rozmiar dowolnego treści żądania w bajtach. Po `null`ustawieniu, maksymalny rozmiar treści żądania jest nieograniczony. Ten limit nie ma wpływu na uaktualnione połączenia, które są zawsze nieograniczone.

Zalecaną metodą zastąpienia limitu w ASP.NET podstawowej aplikacji MVC `IActionResult` dla pojedynczej <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> jest użycie atrybutu w metodzie akcji:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Wyjątek jest zgłaszany, jeśli aplikacja próbuje skonfigurować limit żądania po aplikacji rozpoczął odczytywanie żądania. Właściwości `IsReadOnly` można użyć do wskazania, czy `MaxRequestBodySize` właściwość jest w stanie tylko do odczytu, co oznacza, że jest za późno, aby skonfigurować limit.

Jeśli aplikacja powinna zastąpić <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> na żądanie, <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>użyj:

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Jeśli używasz programu Visual Studio, upewnij się, że aplikacja nie jest skonfigurowana do uruchamiania usługi IIS lub IIS Express.

W programie Visual Studio domyślny profil uruchamiania jest dla usługi IIS Express. Aby uruchomić projekt jako aplikację konsoli, ręcznie zmień wybrany profil, jak pokazano na poniższym zstrząśnięciu z ekranu:

![Wybieranie profilu aplikacji konsoli](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Konfigurowanie systemu Windows Server

1. Określ porty do otwarcia aplikacji i użyj [Zapory systemu Windows](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) lub polecenia cmdlet [Programu New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell, aby otworzyć porty zapory, aby umożliwić ruchowi dostęp do pliku HTTP.sys. W następujących poleceniach i konfiguracji aplikacji jest używany port 443.

1. Podczas wdrażania na maszynie wirtualnej platformy Azure otwórz porty w [sieciowej grupie zabezpieczeń](/azure/virtual-machines/windows/nsg-quickstart-portal). W następujących poleceniach i konfiguracji aplikacji jest używany port 443.

1. W razie potrzeby uzyskaj i zainstaluj certyfikaty X.509.

   W systemie Windows utwórz certyfikaty z podpisem własnym przy użyciu [polecenia cmdlet Programu PowerShell new-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). Aby zapoznać się z nieobsługiwionym przykładem, zobacz [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Zainstaluj certyfikaty z podpisem własnym lub certyfikatem urzędu certyfikacji w magazynie **Local Machine** > **Personal** serwera.

1. Jeśli aplikacja jest [wdrożeniem zależnym od struktury,](/dotnet/core/deploying/#framework-dependent-deployments-fdd)zainstaluj .NET Core, .NET Framework lub oba (jeśli aplikacja jest aplikacją .NET Core kierowaną na platformę .NET Framework).

   * **.NET Core** &ndash; Jeśli aplikacja wymaga programu .NET Core, należy uzyskać i uruchomić instalator **.NET Core Runtime** z programu [.NET Core Downloads](https://dotnet.microsoft.com/download). Nie instaluj pełnego pakietu SDK na serwerze.
   * **Program .NET Framework** &ndash; Jeśli aplikacja wymaga programu .NET Framework, zobacz [przewodnik po instalacji programu .NET Framework](/dotnet/framework/install/). Zainstaluj wymaganą platformę .NET Framework. Instalator najnowszej platformy .NET Framework jest dostępny na stronie [Pliki do pobrania .NET Core.](https://dotnet.microsoft.com/download)

   Jeśli aplikacja jest [samodzielnym wdrożeniem,](/dotnet/core/deploying/#self-contained-deployments-scd)aplikacja zawiera środowisko wykonawcze w swoim wdrożeniu. Na serwerze nie jest wymagana żadna instalacja struktury.

1. Skonfiguruj adresy URL i porty w aplikacji.

   Domyślnie ASP.NET Core wiąże się `http://localhost:5000`z programem . Aby skonfigurować prefiksy i porty adresów URL, dostępne są następujące opcje:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * `urls`argument wiersza polecenia
   * `ASPNETCORE_URLS`zmienna środowiskowa
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   Poniższy przykład kodu pokazuje, jak używać <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> z `10.0.0.4` lokalnym adresem IP serwera na porcie 443:

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   Zaletą `UrlPrefixes` jest to, że komunikat o błędzie jest generowany natychmiast dla nieprawidłowo sformatowanych prefiksów.

   Ustawienia w `UrlPrefixes` ustawieniach `UseUrls` / `urls` / `ASPNETCORE_URLS` zastępowania. W związku z `UseUrls`tym `urls`zaletą `ASPNETCORE_URLS` , i zmienną środowiskową jest to, że łatwiej jest przełączać się między Kestrel i HTTP.sys.

   Http.sys używa [formatów ciągów URLPrefix interfejsu API serwera HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).

   > [!WARNING]
   > Wiązania wieloznaczne najwyższego`http://*:80/` `http://+:80`poziomu ( i ) **nie** powinny być używane. Powiązania symboli wieloznacznych najwyższego poziomu tworzą luki w zabezpieczeniach aplikacji. Dotyczy to zarówno silnych, jak i słabych symboli wieloznacznych. Użyj jawnych nazw hostów lub adresów IP, a nie symboli wieloznacznych. Powiązanie symboli wieloznacznych `*.mysub.com`poddomeny (na przykład) nie stanowi zagrożenia dla `*.com`bezpieczeństwa, jeśli kontrolujesz całą domenę nadrzędną (w przeciwieństwie do , która jest narażona). Aby uzyskać więcej informacji, zobacz [RFC 7230: Sekcja 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Preregister prefiksy adresów URL na serwerze.

   Wbudowanym narzędziem do konfigurowania pliku HTTP.sys jest *plik netsh.exe*. *netsh.exe* służy do rezerwowania prefiksów adresów URL i przypisywania certyfikatów X.509. Narzędzie wymaga uprawnień administratora.

   Użyj narzędzia *netsh.exe,* aby zarejestrować adresy URL aplikacji:

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`&ndash; W pełni kwalifikowany jednolity lokalizator zasobów (URL). Nie używaj powiązania z symbolami wieloznacznych. Użyj prawidłowej nazwy hosta lub lokalnego adresu IP. *Adres URL musi zawierać końcowe ukośnik.*
   * `<USER>`&ndash; Określa nazwę użytkownika lub grupy użytkowników.

   W poniższym przykładzie lokalny adres IP `10.0.0.4`serwera to:

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Gdy adres URL jest zarejestrowany, `URL reservation successfully added`narzędzie odpowiada za pomocą pliku .

   Aby usunąć zarejestrowany adres `delete urlacl` URL, użyj polecenia:

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. Zarejestruj certyfikaty X.509 na serwerze.

   Użyj narzędzia *netsh.exe,* aby zarejestrować certyfikaty dla aplikacji:

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`&ndash; Określa lokalny adres IP powiązania. Nie używaj powiązania z symbolami wieloznacznych. Użyj prawidłowego adresu IP.
   * `<PORT>`&ndash; Określa port powiązania.
   * `<THUMBPRINT>`&ndash; Odcisk palca certyfikatu X.509.
   * `<GUID>`&ndash; Identyfikator GUID wygenerowany przez dewelopera do reprezentowania aplikacji w celach informacyjnych.

   W celach informacyjnych należy przechowywać identyfikator GUID w aplikacji jako tag pakietu:

   * W programie Visual Studio:
     * Otwórz właściwości projektu aplikacji, klikając prawym przyciskiem myszy aplikację w **Eksploratorze rozwiązań** i wybierając pozycję **Właściwości**.
     * Wybierz kartę **Pakiet.**
     * Wprowadź identyfikator GUID utworzony w polu **Znaczniki.**
   * Jeśli nie jest używany program Visual Studio:
     * Otwórz plik projektu aplikacji.
     * Dodaj `<PackageTags>` właściwość do nowej `<PropertyGroup>` lub istniejącej z utworzonym identyfikatorem GUID:

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   W poniższym przykładzie:

   * Lokalny adres IP serwera `10.0.0.4`to .
   * Generator identyfikatorów GUID `appid` online zapewnia wartość.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Gdy certyfikat jest zarejestrowany, narzędzie `SSL Certificate successfully added`odpowiada za pomocą pliku .

   Aby usunąć rejestrację certyfikatu, użyj `delete sslcert` polecenia:

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Dokumentacja referencyjna dla *netsh.exe:*

   * [Polecenia Netsh dla protokołu transferu hipertekstowego (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)
   * [Ciągi UrlPrefix](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. Uruchom aplikację.

   Uprawnienia administratora nie są wymagane do uruchamiania aplikacji podczas powiązania z localhost przy użyciu protokołu HTTP (nie HTTPS) z numerem portu większym niż 1024. W przypadku innych konfiguracji (na przykład przy użyciu lokalnego adresu IP lub powiązania z portem 443) uruchom aplikację z uprawnieniami administratora.

   Aplikacja odpowiada na publiczny adres IP serwera. W tym przykładzie serwer jest osiągany z Internetu `104.214.79.47`pod jego publicznym adresem IP .

   Certyfikat dewelopera jest używany w tym przykładzie. Strona ładuje się bezpiecznie po pominięciu ostrzeżenia o niezaufanym certyfikacie przeglądarki.

   ![Okno przeglądarki z załadowaną stroną Indeks aplikacji](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scenariusze serwera proxy i modułu równoważenia obciążenia

W przypadku aplikacji obsługiwanych przez http.sys, które współdziałają z żądaniami z Internetu lub sieci firmowej, dodatkowa konfiguracja może być wymagana podczas hostowania za serwerami proxy i modułami równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ASP.NET Core do pracy z serwerami proxy i modułami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Włączanie uwierzytelniania systemu Windows za pomocą protokołu HTTP.sys](xref:security/authentication/windowsauth#httpsys)
* [Interfejs API serwera HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [repozytorium aspnet/HttpSysServer GitHub (kod źródłowy)](https://github.com/aspnet/HttpSysServer/)
* [Host](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) to [serwer sieci web dla ASP.NET Core,](xref:fundamentals/servers/index) który działa tylko w systemie Windows. HTTP.sys jest alternatywą dla serwera [Kestrel](xref:fundamentals/servers/kestrel) i oferuje pewne funkcje, których Kestrel nie zapewnia.

> [!IMPORTANT]
> Protokół HTTP.sys nie jest zgodny z [modułem ASP.NET Core](xref:host-and-deploy/aspnet-core-module) module i nie może być używany z programami IIS lub IIS Express.

Http.sys obsługuje następujące funkcje:

* [Uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth)
* Udostępnianie portów
* HTTPS z SNI
* Protokół HTTP/2 przez protokół TLS (Windows 10 lub nowszy)
* Bezpośrednia transmisja plików
* Buforowanie odpowiedzi
* WebSockets (Windows 8 lub nowsze)

Obsługiwane wersje systemu Windows:

* Windows 7 lub nowsze
* Windows Server 2008 R2 lub nowszy

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>Kiedy używać protokołu HTTP.sys

HTTP.sys jest przydatne w przypadku wdrożeń, w których:

* Istnieje potrzeba udostępnienia serwera bezpośrednio do Internetu bez korzystania z usług IIS.

  ![HTTP.sys komunikuje się bezpośrednio z Internetem](httpsys/_static/httpsys-to-internet.png)

* Wdrożenie wewnętrzne wymaga funkcji niedostępnej w Kestrel, takiej jak [Uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth).

  ![HTTP.sys komunikuje się bezpośrednio z siecią wewnętrzną](httpsys/_static/httpsys-to-internal.png)

HTTP.sys to dojrzała technologia, która chroni przed wieloma typami ataków i zapewnia niezawodność, bezpieczeństwo i skalowalność w pełni funkcjonowego serwera sieci Web. Usługi IIS działa jako odbiornik HTTP na górze HTTP.sys.

## <a name="http2-support"></a>Obsługa protokołu HTTP/2

[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest włączony dla aplikacji ASP.NET Core, jeśli spełnione są następujące wymagania podstawowe:

* Windows Server 2016/Windows 10 lub nowsze
* Połączenie [negocjacji protokołu warstwy aplikacji (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)
* Połączenie TLS 1.2 lub nowsze

Jeśli zostanie nawiązane połączenie HTTP/2, [httpRequest.protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) zgłasza `HTTP/2`.

Protokół HTTP/2 jest domyślnie włączony. Jeśli połączenie HTTP/2 nie zostanie nawiązane, połączenie powróci do protokołu HTTP/1.1. W przyszłej wersji systemu Windows dostępne będą flagi konfiguracji HTTP/2, w tym możliwość wyłączenia protokołu HTTP/2 za pomocą protokołu HTTP.sys.

## <a name="kernel-mode-authentication-with-kerberos"></a>Uwierzytelnianie w trybie jądra za pomocą protokołu Kerberos

Http.sys deleguje do uwierzytelniania w trybie jądra za pomocą protokołu uwierzytelniania Kerberos. Uwierzytelnianie w trybie użytkownika nie jest obsługiwane w protokołach Kerberos i HTTP.sys. Konto komputera musi służyć do odszyfrowania tokenu/biletu Protokołu Kerberos uzyskanego z usługi Active Directory i przekazanego przez klienta do serwera w celu uwierzytelnienia użytkownika. Zarejestruj nazwę główną usługi (SPN) dla hosta, a nie użytkownika aplikacji.

## <a name="how-to-use-httpsys"></a>Jak korzystać z protokołu HTTP.sys

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Skonfiguruj aplikację core ASP.NET do używania protokołu HTTP.sys

Odwołanie do pakietu w pliku projektu nie jest wymagane podczas korzystania z [metapakiety Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)). Jeśli nie `Microsoft.AspNetCore.App` używasz metapakietu, dodaj odwołanie do pakietu [microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).

Wywołanie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> metody rozszerzenia podczas tworzenia hosta, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>określając wszelkie wymagane . W poniższym przykładzie ustawia się opcje wartości domyślnych:

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

Dodatkowa konfiguracja HTTP.sys jest obsługiwana przez [ustawienia rejestru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

**Opcje HTTP.sys**

| Właściwość | Opis | Domyślne |
| -------- | ----------- | :-----: |
| [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Kontrolować, czy synchroniczne wejście/wyjście `HttpContext.Request.Body` jest `HttpContext.Response.Body`dozwolone dla i . | `true` |
| [Uwierzytelnianie.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Zezwalaj na żądania anonimowe. | `true` |
| [Uwierzytelnianie.Schematy](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Określ dozwolone schematy uwierzytelniania. Może być modyfikowany w dowolnym momencie przed utylizowanym odbiornikiem. Wartości są dostarczane przez [authenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate` `None`, i `NTLM`. | `None` |
| [Włącz Buforowanie reakcji](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Spróbuj buforować [w trybie jądra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) dla odpowiedzi z kwalifikującymi się nagłówkami. Odpowiedź może nie `Set-Cookie` `Vary`zawierać `Pragma` , lub nagłówki. Musi zawierać `Cache-Control` nagłówek, który `public` jest i `shared-max-age` `max-age` albo lub `Expires` wartość lub nagłówka. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Maksymalna liczba równoczesnych akceptuje. | 5 &times; [Środowisko.<br> Liczba procesorów](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Maksymalna liczba równoczesnych połączeń do zaakceptowania. Użyj `-1` dla nieskończonych. Służy `null` do używania ustawienia całego rejestru. | `null`<br>(w całej maszynie<br>ustawienia) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Zobacz <a href="#maxrequestbodysize">MaxRequestBodySize</a> sekcji. | 3000000 bajtów<br>(~28,6 MB) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Maksymalna liczba żądań, które mogą być umieszczane w kolejce. | 1000 |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Wskaż, czy treść odpowiedzi zapisuje, że nie z powodu rozłączenia klienta należy zgłaszać wyjątki lub zakończyć normalnie. | `false`<br>(normalnie) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Uwidacznianie konfiguracji <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> HTTP.sys, która może być również skonfigurowana w rejestrze. Postępuj zgodnie z łączami interfejsu API, aby dowiedzieć się więcej o każdym ustawieniu, w tym wartościach domyślnych:<ul><li>[TimeoutManager.DrainEntityCzasciał dozwolony](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; dla interfejsu API serwera HTTP do opróżnienia treści jednostki w połączeniu Keep-Alive.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Czas dozwolony dla treści jednostki żądania do przybycia.</li><li>[Czas timeoutmanager.headerWit](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; czas dozwolony dla interfejsu API serwera HTTP do analizować nagłówek żądania.</li><li>[Czas połączenia timeoutmanager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; dozwolony dla połączenia bezczynnego.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; Minimalna szybkość wysyłania odpowiedzi.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Czas dozwolony dla żądania, aby pozostać w kolejce żądań, zanim aplikacja go odbierze.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> Określ, aby zarejestrować się w http.sys. Najbardziej przydatne jest [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), który jest używany do dodawania prefiksu do kolekcji. Mogą one zostać zmodyfikowane w dowolnym momencie przed utylizowanym odbiornikiem. |  |

<a name="maxrequestbodysize"></a>

**Rozmiar MaxRequestBodySize**

Maksymalny dozwolony rozmiar dowolnego treści żądania w bajtach. Po `null`ustawieniu, maksymalny rozmiar treści żądania jest nieograniczony. Ten limit nie ma wpływu na uaktualnione połączenia, które są zawsze nieograniczone.

Zalecaną metodą zastąpienia limitu w ASP.NET podstawowej aplikacji MVC `IActionResult` dla pojedynczej <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> jest użycie atrybutu w metodzie akcji:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Wyjątek jest zgłaszany, jeśli aplikacja próbuje skonfigurować limit żądania po aplikacji rozpoczął odczytywanie żądania. Właściwości `IsReadOnly` można użyć do wskazania, czy `MaxRequestBodySize` właściwość jest w stanie tylko do odczytu, co oznacza, że jest za późno, aby skonfigurować limit.

Jeśli aplikacja powinna zastąpić <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> na żądanie, <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>użyj:

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Jeśli używasz programu Visual Studio, upewnij się, że aplikacja nie jest skonfigurowana do uruchamiania usługi IIS lub IIS Express.

W programie Visual Studio domyślny profil uruchamiania jest dla usługi IIS Express. Aby uruchomić projekt jako aplikację konsoli, ręcznie zmień wybrany profil, jak pokazano na poniższym zstrząśnięciu z ekranu:

![Wybieranie profilu aplikacji konsoli](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Konfigurowanie systemu Windows Server

1. Określ porty do otwarcia aplikacji i użyj [Zapory systemu Windows](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) lub polecenia cmdlet [Programu New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell, aby otworzyć porty zapory, aby umożliwić ruchowi dostęp do pliku HTTP.sys. W następujących poleceniach i konfiguracji aplikacji jest używany port 443.

1. Podczas wdrażania na maszynie wirtualnej platformy Azure otwórz porty w [sieciowej grupie zabezpieczeń](/azure/virtual-machines/windows/nsg-quickstart-portal). W następujących poleceniach i konfiguracji aplikacji jest używany port 443.

1. W razie potrzeby uzyskaj i zainstaluj certyfikaty X.509.

   W systemie Windows utwórz certyfikaty z podpisem własnym przy użyciu [polecenia cmdlet Programu PowerShell new-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). Aby zapoznać się z nieobsługiwionym przykładem, zobacz [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Zainstaluj certyfikaty z podpisem własnym lub certyfikatem urzędu certyfikacji w magazynie **Local Machine** > **Personal** serwera.

1. Jeśli aplikacja jest [wdrożeniem zależnym od struktury,](/dotnet/core/deploying/#framework-dependent-deployments-fdd)zainstaluj .NET Core, .NET Framework lub oba (jeśli aplikacja jest aplikacją .NET Core kierowaną na platformę .NET Framework).

   * **.NET Core** &ndash; Jeśli aplikacja wymaga programu .NET Core, należy uzyskać i uruchomić instalator **.NET Core Runtime** z programu [.NET Core Downloads](https://dotnet.microsoft.com/download). Nie instaluj pełnego pakietu SDK na serwerze.
   * **Program .NET Framework** &ndash; Jeśli aplikacja wymaga programu .NET Framework, zobacz [przewodnik po instalacji programu .NET Framework](/dotnet/framework/install/). Zainstaluj wymaganą platformę .NET Framework. Instalator najnowszej platformy .NET Framework jest dostępny na stronie [Pliki do pobrania .NET Core.](https://dotnet.microsoft.com/download)

   Jeśli aplikacja jest [samodzielnym wdrożeniem,](/dotnet/core/deploying/#self-contained-deployments-scd)aplikacja zawiera środowisko wykonawcze w swoim wdrożeniu. Na serwerze nie jest wymagana żadna instalacja struktury.

1. Skonfiguruj adresy URL i porty w aplikacji.

   Domyślnie ASP.NET Core wiąże się `http://localhost:5000`z programem . Aby skonfigurować prefiksy i porty adresów URL, dostępne są następujące opcje:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * `urls`argument wiersza polecenia
   * `ASPNETCORE_URLS`zmienna środowiskowa
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   Poniższy przykład kodu pokazuje, jak używać <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> z `10.0.0.4` lokalnym adresem IP serwera na porcie 443:

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   Zaletą `UrlPrefixes` jest to, że komunikat o błędzie jest generowany natychmiast dla nieprawidłowo sformatowanych prefiksów.

   Ustawienia w `UrlPrefixes` ustawieniach `UseUrls` / `urls` / `ASPNETCORE_URLS` zastępowania. W związku z `UseUrls`tym `urls`zaletą `ASPNETCORE_URLS` , i zmienną środowiskową jest to, że łatwiej jest przełączać się między Kestrel i HTTP.sys.

   Http.sys używa [formatów ciągów URLPrefix interfejsu API serwera HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).

   > [!WARNING]
   > Wiązania wieloznaczne najwyższego`http://*:80/` `http://+:80`poziomu ( i ) **nie** powinny być używane. Powiązania symboli wieloznacznych najwyższego poziomu tworzą luki w zabezpieczeniach aplikacji. Dotyczy to zarówno silnych, jak i słabych symboli wieloznacznych. Użyj jawnych nazw hostów lub adresów IP, a nie symboli wieloznacznych. Powiązanie symboli wieloznacznych `*.mysub.com`poddomeny (na przykład) nie stanowi zagrożenia dla `*.com`bezpieczeństwa, jeśli kontrolujesz całą domenę nadrzędną (w przeciwieństwie do , która jest narażona). Aby uzyskać więcej informacji, zobacz [RFC 7230: Sekcja 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Preregister prefiksy adresów URL na serwerze.

   Wbudowanym narzędziem do konfigurowania pliku HTTP.sys jest *plik netsh.exe*. *netsh.exe* służy do rezerwowania prefiksów adresów URL i przypisywania certyfikatów X.509. Narzędzie wymaga uprawnień administratora.

   Użyj narzędzia *netsh.exe,* aby zarejestrować adresy URL aplikacji:

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`&ndash; W pełni kwalifikowany jednolity lokalizator zasobów (URL). Nie używaj powiązania z symbolami wieloznacznych. Użyj prawidłowej nazwy hosta lub lokalnego adresu IP. *Adres URL musi zawierać końcowe ukośnik.*
   * `<USER>`&ndash; Określa nazwę użytkownika lub grupy użytkowników.

   W poniższym przykładzie lokalny adres IP `10.0.0.4`serwera to:

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Gdy adres URL jest zarejestrowany, `URL reservation successfully added`narzędzie odpowiada za pomocą pliku .

   Aby usunąć zarejestrowany adres `delete urlacl` URL, użyj polecenia:

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. Zarejestruj certyfikaty X.509 na serwerze.

   Użyj narzędzia *netsh.exe,* aby zarejestrować certyfikaty dla aplikacji:

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`&ndash; Określa lokalny adres IP powiązania. Nie używaj powiązania z symbolami wieloznacznych. Użyj prawidłowego adresu IP.
   * `<PORT>`&ndash; Określa port powiązania.
   * `<THUMBPRINT>`&ndash; Odcisk palca certyfikatu X.509.
   * `<GUID>`&ndash; Identyfikator GUID wygenerowany przez dewelopera do reprezentowania aplikacji w celach informacyjnych.

   W celach informacyjnych należy przechowywać identyfikator GUID w aplikacji jako tag pakietu:

   * W programie Visual Studio:
     * Otwórz właściwości projektu aplikacji, klikając prawym przyciskiem myszy aplikację w **Eksploratorze rozwiązań** i wybierając pozycję **Właściwości**.
     * Wybierz kartę **Pakiet.**
     * Wprowadź identyfikator GUID utworzony w polu **Znaczniki.**
   * Jeśli nie jest używany program Visual Studio:
     * Otwórz plik projektu aplikacji.
     * Dodaj `<PackageTags>` właściwość do nowej `<PropertyGroup>` lub istniejącej z utworzonym identyfikatorem GUID:

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   W poniższym przykładzie:

   * Lokalny adres IP serwera `10.0.0.4`to .
   * Generator identyfikatorów GUID `appid` online zapewnia wartość.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Gdy certyfikat jest zarejestrowany, narzędzie `SSL Certificate successfully added`odpowiada za pomocą pliku .

   Aby usunąć rejestrację certyfikatu, użyj `delete sslcert` polecenia:

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Dokumentacja referencyjna dla *netsh.exe:*

   * [Polecenia Netsh dla protokołu transferu hipertekstowego (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)
   * [Ciągi UrlPrefix](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. Uruchom aplikację.

   Uprawnienia administratora nie są wymagane do uruchamiania aplikacji podczas powiązania z localhost przy użyciu protokołu HTTP (nie HTTPS) z numerem portu większym niż 1024. W przypadku innych konfiguracji (na przykład przy użyciu lokalnego adresu IP lub powiązania z portem 443) uruchom aplikację z uprawnieniami administratora.

   Aplikacja odpowiada na publiczny adres IP serwera. W tym przykładzie serwer jest osiągany z Internetu `104.214.79.47`pod jego publicznym adresem IP .

   Certyfikat dewelopera jest używany w tym przykładzie. Strona ładuje się bezpiecznie po pominięciu ostrzeżenia o niezaufanym certyfikacie przeglądarki.

   ![Okno przeglądarki z załadowaną stroną Indeks aplikacji](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scenariusze serwera proxy i modułu równoważenia obciążenia

W przypadku aplikacji obsługiwanych przez http.sys, które współdziałają z żądaniami z Internetu lub sieci firmowej, dodatkowa konfiguracja może być wymagana podczas hostowania za serwerami proxy i modułami równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ASP.NET Core do pracy z serwerami proxy i modułami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Włączanie uwierzytelniania systemu Windows za pomocą protokołu HTTP.sys](xref:security/authentication/windowsauth#httpsys)
* [Interfejs API serwera HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [repozytorium aspnet/HttpSysServer GitHub (kod źródłowy)](https://github.com/aspnet/HttpSysServer/)
* [Host](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) to [serwer sieci web dla ASP.NET Core,](xref:fundamentals/servers/index) który działa tylko w systemie Windows. HTTP.sys jest alternatywą dla serwera [Kestrel](xref:fundamentals/servers/kestrel) i oferuje pewne funkcje, których Kestrel nie zapewnia.

> [!IMPORTANT]
> Protokół HTTP.sys nie jest zgodny z [modułem ASP.NET Core](xref:host-and-deploy/aspnet-core-module) module i nie może być używany z programami IIS lub IIS Express.

Http.sys obsługuje następujące funkcje:

* [Uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth)
* Udostępnianie portów
* HTTPS z SNI
* Protokół HTTP/2 przez protokół TLS (Windows 10 lub nowszy)
* Bezpośrednia transmisja plików
* Buforowanie odpowiedzi
* WebSockets (Windows 8 lub nowsze)

Obsługiwane wersje systemu Windows:

* Windows 7 lub nowsze
* Windows Server 2008 R2 lub nowszy

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>Kiedy używać protokołu HTTP.sys

HTTP.sys jest przydatne w przypadku wdrożeń, w których:

* Istnieje potrzeba udostępnienia serwera bezpośrednio do Internetu bez korzystania z usług IIS.

  ![HTTP.sys komunikuje się bezpośrednio z Internetem](httpsys/_static/httpsys-to-internet.png)

* Wdrożenie wewnętrzne wymaga funkcji niedostępnej w Kestrel, takiej jak [Uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth).

  ![HTTP.sys komunikuje się bezpośrednio z siecią wewnętrzną](httpsys/_static/httpsys-to-internal.png)

HTTP.sys to dojrzała technologia, która chroni przed wieloma typami ataków i zapewnia niezawodność, bezpieczeństwo i skalowalność w pełni funkcjonowego serwera sieci Web. Usługi IIS działa jako odbiornik HTTP na górze HTTP.sys.

## <a name="http2-support"></a>Obsługa protokołu HTTP/2

[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest włączony dla aplikacji ASP.NET Core, jeśli spełnione są następujące wymagania podstawowe:

* Windows Server 2016/Windows 10 lub nowsze
* Połączenie [negocjacji protokołu warstwy aplikacji (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)
* Połączenie TLS 1.2 lub nowsze

Jeśli zostanie nawiązane połączenie HTTP/2, [httpRequest.protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) zgłasza `HTTP/1.1`.

Protokół HTTP/2 jest domyślnie włączony. Jeśli połączenie HTTP/2 nie zostanie nawiązane, połączenie powróci do protokołu HTTP/1.1. W przyszłej wersji systemu Windows dostępne będą flagi konfiguracji HTTP/2, w tym możliwość wyłączenia protokołu HTTP/2 za pomocą protokołu HTTP.sys.

## <a name="kernel-mode-authentication-with-kerberos"></a>Uwierzytelnianie w trybie jądra za pomocą protokołu Kerberos

Http.sys deleguje do uwierzytelniania w trybie jądra za pomocą protokołu uwierzytelniania Kerberos. Uwierzytelnianie w trybie użytkownika nie jest obsługiwane w protokołach Kerberos i HTTP.sys. Konto komputera musi służyć do odszyfrowania tokenu/biletu Protokołu Kerberos uzyskanego z usługi Active Directory i przekazanego przez klienta do serwera w celu uwierzytelnienia użytkownika. Zarejestruj nazwę główną usługi (SPN) dla hosta, a nie użytkownika aplikacji.

## <a name="how-to-use-httpsys"></a>Jak korzystać z protokołu HTTP.sys

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Skonfiguruj aplikację core ASP.NET do używania protokołu HTTP.sys

Odwołanie do pakietu w pliku projektu nie jest wymagane podczas korzystania z [metapakiety Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)). Jeśli nie `Microsoft.AspNetCore.App` używasz metapakietu, dodaj odwołanie do pakietu [microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).

Wywołanie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> metody rozszerzenia podczas tworzenia hosta, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>określając wszelkie wymagane . W poniższym przykładzie ustawia się opcje wartości domyślnych:

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

Dodatkowa konfiguracja HTTP.sys jest obsługiwana przez [ustawienia rejestru](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

**Opcje HTTP.sys**

| Właściwość | Opis | Domyślne |
| -------- | ----------- | :-----: |
| [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Kontrolować, czy synchroniczne wejście/wyjście `HttpContext.Request.Body` jest `HttpContext.Response.Body`dozwolone dla i . | `true` |
| [Uwierzytelnianie.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Zezwalaj na żądania anonimowe. | `true` |
| [Uwierzytelnianie.Schematy](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Określ dozwolone schematy uwierzytelniania. Może być modyfikowany w dowolnym momencie przed utylizowanym odbiornikiem. Wartości są dostarczane przez [authenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate` `None`, i `NTLM`. | `None` |
| [Włącz Buforowanie reakcji](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Spróbuj buforować [w trybie jądra](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) dla odpowiedzi z kwalifikującymi się nagłówkami. Odpowiedź może nie `Set-Cookie` `Vary`zawierać `Pragma` , lub nagłówki. Musi zawierać `Cache-Control` nagłówek, który `public` jest i `shared-max-age` `max-age` albo lub `Expires` wartość lub nagłówka. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Maksymalna liczba równoczesnych akceptuje. | 5 &times; [Środowisko.<br> Liczba procesorów](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Maksymalna liczba równoczesnych połączeń do zaakceptowania. Użyj `-1` dla nieskończonych. Służy `null` do używania ustawienia całego rejestru. | `null`<br>(w całej maszynie<br>ustawienia) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Zobacz <a href="#maxrequestbodysize">MaxRequestBodySize</a> sekcji. | 3000000 bajtów<br>(~28,6 MB) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Maksymalna liczba żądań, które mogą być umieszczane w kolejce. | 1000 |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Wskaż, czy treść odpowiedzi zapisuje, że nie z powodu rozłączenia klienta należy zgłaszać wyjątki lub zakończyć normalnie. | `false`<br>(normalnie) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Uwidacznianie konfiguracji <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> HTTP.sys, która może być również skonfigurowana w rejestrze. Postępuj zgodnie z łączami interfejsu API, aby dowiedzieć się więcej o każdym ustawieniu, w tym wartościach domyślnych:<ul><li>[TimeoutManager.DrainEntityCzasciał dozwolony](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; dla interfejsu API serwera HTTP do opróżnienia treści jednostki w połączeniu Keep-Alive.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Czas dozwolony dla treści jednostki żądania do przybycia.</li><li>[Czas timeoutmanager.headerWit](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; czas dozwolony dla interfejsu API serwera HTTP do analizować nagłówek żądania.</li><li>[Czas połączenia timeoutmanager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; dozwolony dla połączenia bezczynnego.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; Minimalna szybkość wysyłania odpowiedzi.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Czas dozwolony dla żądania, aby pozostać w kolejce żądań, zanim aplikacja go odbierze.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> Określ, aby zarejestrować się w http.sys. Najbardziej przydatne jest [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), który jest używany do dodawania prefiksu do kolekcji. Mogą one zostać zmodyfikowane w dowolnym momencie przed utylizowanym odbiornikiem. |  |

<a name="maxrequestbodysize"></a>

**Rozmiar MaxRequestBodySize**

Maksymalny dozwolony rozmiar dowolnego treści żądania w bajtach. Po `null`ustawieniu, maksymalny rozmiar treści żądania jest nieograniczony. Ten limit nie ma wpływu na uaktualnione połączenia, które są zawsze nieograniczone.

Zalecaną metodą zastąpienia limitu w ASP.NET podstawowej aplikacji MVC `IActionResult` dla pojedynczej <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> jest użycie atrybutu w metodzie akcji:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Wyjątek jest zgłaszany, jeśli aplikacja próbuje skonfigurować limit żądania po aplikacji rozpoczął odczytywanie żądania. Właściwości `IsReadOnly` można użyć do wskazania, czy `MaxRequestBodySize` właściwość jest w stanie tylko do odczytu, co oznacza, że jest za późno, aby skonfigurować limit.

Jeśli aplikacja powinna zastąpić <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> na żądanie, <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>użyj:

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Jeśli używasz programu Visual Studio, upewnij się, że aplikacja nie jest skonfigurowana do uruchamiania usługi IIS lub IIS Express.

W programie Visual Studio domyślny profil uruchamiania jest dla usługi IIS Express. Aby uruchomić projekt jako aplikację konsoli, ręcznie zmień wybrany profil, jak pokazano na poniższym zstrząśnięciu z ekranu:

![Wybieranie profilu aplikacji konsoli](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Konfigurowanie systemu Windows Server

1. Określ porty do otwarcia aplikacji i użyj [Zapory systemu Windows](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) lub polecenia cmdlet [Programu New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell, aby otworzyć porty zapory, aby umożliwić ruchowi dostęp do pliku HTTP.sys. W następujących poleceniach i konfiguracji aplikacji jest używany port 443.

1. Podczas wdrażania na maszynie wirtualnej platformy Azure otwórz porty w [sieciowej grupie zabezpieczeń](/azure/virtual-machines/windows/nsg-quickstart-portal). W następujących poleceniach i konfiguracji aplikacji jest używany port 443.

1. W razie potrzeby uzyskaj i zainstaluj certyfikaty X.509.

   W systemie Windows utwórz certyfikaty z podpisem własnym przy użyciu [polecenia cmdlet Programu PowerShell new-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). Aby zapoznać się z nieobsługiwionym przykładem, zobacz [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Zainstaluj certyfikaty z podpisem własnym lub certyfikatem urzędu certyfikacji w magazynie **Local Machine** > **Personal** serwera.

1. Jeśli aplikacja jest [wdrożeniem zależnym od struktury,](/dotnet/core/deploying/#framework-dependent-deployments-fdd)zainstaluj .NET Core, .NET Framework lub oba (jeśli aplikacja jest aplikacją .NET Core kierowaną na platformę .NET Framework).

   * **.NET Core** &ndash; Jeśli aplikacja wymaga programu .NET Core, należy uzyskać i uruchomić instalator **.NET Core Runtime** z programu [.NET Core Downloads](https://dotnet.microsoft.com/download). Nie instaluj pełnego pakietu SDK na serwerze.
   * **Program .NET Framework** &ndash; Jeśli aplikacja wymaga programu .NET Framework, zobacz [przewodnik po instalacji programu .NET Framework](/dotnet/framework/install/). Zainstaluj wymaganą platformę .NET Framework. Instalator najnowszej platformy .NET Framework jest dostępny na stronie [Pliki do pobrania .NET Core.](https://dotnet.microsoft.com/download)

   Jeśli aplikacja jest [samodzielnym wdrożeniem,](/dotnet/core/deploying/#self-contained-deployments-scd)aplikacja zawiera środowisko wykonawcze w swoim wdrożeniu. Na serwerze nie jest wymagana żadna instalacja struktury.

1. Skonfiguruj adresy URL i porty w aplikacji.

   Domyślnie ASP.NET Core wiąże się `http://localhost:5000`z programem . Aby skonfigurować prefiksy i porty adresów URL, dostępne są następujące opcje:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * `urls`argument wiersza polecenia
   * `ASPNETCORE_URLS`zmienna środowiskowa
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   Poniższy przykład kodu pokazuje, jak używać <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> z `10.0.0.4` lokalnym adresem IP serwera na porcie 443:

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   Zaletą `UrlPrefixes` jest to, że komunikat o błędzie jest generowany natychmiast dla nieprawidłowo sformatowanych prefiksów.

   Ustawienia w `UrlPrefixes` ustawieniach `UseUrls` / `urls` / `ASPNETCORE_URLS` zastępowania. W związku z `UseUrls`tym `urls`zaletą `ASPNETCORE_URLS` , i zmienną środowiskową jest to, że łatwiej jest przełączać się między Kestrel i HTTP.sys.

   Http.sys używa [formatów ciągów URLPrefix interfejsu API serwera HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).

   > [!WARNING]
   > Wiązania wieloznaczne najwyższego`http://*:80/` `http://+:80`poziomu ( i ) **nie** powinny być używane. Powiązania symboli wieloznacznych najwyższego poziomu tworzą luki w zabezpieczeniach aplikacji. Dotyczy to zarówno silnych, jak i słabych symboli wieloznacznych. Użyj jawnych nazw hostów lub adresów IP, a nie symboli wieloznacznych. Powiązanie symboli wieloznacznych `*.mysub.com`poddomeny (na przykład) nie stanowi zagrożenia dla `*.com`bezpieczeństwa, jeśli kontrolujesz całą domenę nadrzędną (w przeciwieństwie do , która jest narażona). Aby uzyskać więcej informacji, zobacz [RFC 7230: Sekcja 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Preregister prefiksy adresów URL na serwerze.

   Wbudowanym narzędziem do konfigurowania pliku HTTP.sys jest *plik netsh.exe*. *netsh.exe* służy do rezerwowania prefiksów adresów URL i przypisywania certyfikatów X.509. Narzędzie wymaga uprawnień administratora.

   Użyj narzędzia *netsh.exe,* aby zarejestrować adresy URL aplikacji:

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`&ndash; W pełni kwalifikowany jednolity lokalizator zasobów (URL). Nie używaj powiązania z symbolami wieloznacznych. Użyj prawidłowej nazwy hosta lub lokalnego adresu IP. *Adres URL musi zawierać końcowe ukośnik.*
   * `<USER>`&ndash; Określa nazwę użytkownika lub grupy użytkowników.

   W poniższym przykładzie lokalny adres IP `10.0.0.4`serwera to:

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Gdy adres URL jest zarejestrowany, `URL reservation successfully added`narzędzie odpowiada za pomocą pliku .

   Aby usunąć zarejestrowany adres `delete urlacl` URL, użyj polecenia:

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. Zarejestruj certyfikaty X.509 na serwerze.

   Użyj narzędzia *netsh.exe,* aby zarejestrować certyfikaty dla aplikacji:

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`&ndash; Określa lokalny adres IP powiązania. Nie używaj powiązania z symbolami wieloznacznych. Użyj prawidłowego adresu IP.
   * `<PORT>`&ndash; Określa port powiązania.
   * `<THUMBPRINT>`&ndash; Odcisk palca certyfikatu X.509.
   * `<GUID>`&ndash; Identyfikator GUID wygenerowany przez dewelopera do reprezentowania aplikacji w celach informacyjnych.

   W celach informacyjnych należy przechowywać identyfikator GUID w aplikacji jako tag pakietu:

   * W programie Visual Studio:
     * Otwórz właściwości projektu aplikacji, klikając prawym przyciskiem myszy aplikację w **Eksploratorze rozwiązań** i wybierając pozycję **Właściwości**.
     * Wybierz kartę **Pakiet.**
     * Wprowadź identyfikator GUID utworzony w polu **Znaczniki.**
   * Jeśli nie jest używany program Visual Studio:
     * Otwórz plik projektu aplikacji.
     * Dodaj `<PackageTags>` właściwość do nowej `<PropertyGroup>` lub istniejącej z utworzonym identyfikatorem GUID:

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   W poniższym przykładzie:

   * Lokalny adres IP serwera `10.0.0.4`to .
   * Generator identyfikatorów GUID `appid` online zapewnia wartość.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Gdy certyfikat jest zarejestrowany, narzędzie `SSL Certificate successfully added`odpowiada za pomocą pliku .

   Aby usunąć rejestrację certyfikatu, użyj `delete sslcert` polecenia:

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Dokumentacja referencyjna dla *netsh.exe:*

   * [Polecenia Netsh dla protokołu transferu hipertekstowego (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)
   * [Ciągi UrlPrefix](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. Uruchom aplikację.

   Uprawnienia administratora nie są wymagane do uruchamiania aplikacji podczas powiązania z localhost przy użyciu protokołu HTTP (nie HTTPS) z numerem portu większym niż 1024. W przypadku innych konfiguracji (na przykład przy użyciu lokalnego adresu IP lub powiązania z portem 443) uruchom aplikację z uprawnieniami administratora.

   Aplikacja odpowiada na publiczny adres IP serwera. W tym przykładzie serwer jest osiągany z Internetu `104.214.79.47`pod jego publicznym adresem IP .

   Certyfikat dewelopera jest używany w tym przykładzie. Strona ładuje się bezpiecznie po pominięciu ostrzeżenia o niezaufanym certyfikacie przeglądarki.

   ![Okno przeglądarki z załadowaną stroną Indeks aplikacji](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scenariusze serwera proxy i modułu równoważenia obciążenia

W przypadku aplikacji obsługiwanych przez http.sys, które współdziałają z żądaniami z Internetu lub sieci firmowej, dodatkowa konfiguracja może być wymagana podczas hostowania za serwerami proxy i modułami równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ASP.NET Core do pracy z serwerami proxy i modułami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Włączanie uwierzytelniania systemu Windows za pomocą protokołu HTTP.sys](xref:security/authentication/windowsauth#httpsys)
* [Interfejs API serwera HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [repozytorium aspnet/HttpSysServer GitHub (kod źródłowy)](https://github.com/aspnet/HttpSysServer/)
* [Host](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
