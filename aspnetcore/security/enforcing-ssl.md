---
title: Wymuszanie protokołu HTTPS w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak wymagać protokołu HTTPS/TLS w aplikacji internetowej ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/enforcing-ssl
ms.openlocfilehash: 8247d66900a0c15b3b386dca021c5c5922d26e71
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404565"
---
# <a name="enforce-https-in-aspnet-core"></a>Wymuszanie protokołu HTTPS w ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

W tym dokumencie przedstawiono sposób:

* Wymagaj protokołu HTTPS dla wszystkich żądań.
* Przekierowuj wszystkie żądania HTTP do protokołu HTTPS.

Żaden interfejs API nie może zapobiec wysyłaniu poufnych danych przez klienta do pierwszego żądania.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a>Projekty interfejsu API
>
> **Nie** należy używać [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) w interfejsach API sieci Web, które otrzymują poufne informacje. `RequireHttpsAttribute`używa kodów stanu HTTP do przekierowywania przeglądarek z protokołu HTTP do HTTPS. Klienci interfejsu API nie mogą zrozumieć ani przestrzegać przekierowania z protokołu HTTP do protokołu HTTPS. Tacy klienci mogą wysyłać informacje za pośrednictwem protokołu HTTP. Interfejsy API sieci Web powinny:
>
> * Nie nasłuchuje na protokole HTTP.
> * Zamknij połączenie z kodem stanu 400 (złe żądanie) i nie obsługuj żądania.
>
> ## <a name="hsts-and-api-projects"></a>HSTS i projekty interfejsu API
>
> Domyślne projekty interfejsów API nie obejmują [HSTS](#hsts) , ponieważ HSTS jest ogólnie jedyną instrukcją przeglądarki. Inne obiekty wywołujące, takie jak telefony lub aplikacje klasyczne, **nie przestrzegają** instrukcji. Nawet w przeglądarkach pojedyncze uwierzytelnione wywołanie interfejsu API za pośrednictwem protokołu HTTP jest niebezpieczne dla niezabezpieczonych sieci. Bezpiecznym podejściem jest skonfigurowanie projektów interfejsu API tylko do nasłuchiwania i odpowiadania za pośrednictwem protokołu HTTPS.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a>Projekty interfejsu API
>
> **Nie** należy używać [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) w interfejsach API sieci Web, które otrzymują poufne informacje. `RequireHttpsAttribute`używa kodów stanu HTTP do przekierowywania przeglądarek z protokołu HTTP do HTTPS. Klienci interfejsu API nie mogą zrozumieć ani przestrzegać przekierowania z protokołu HTTP do protokołu HTTPS. Tacy klienci mogą wysyłać informacje za pośrednictwem protokołu HTTP. Interfejsy API sieci Web powinny:
>
> * Nie nasłuchuje na protokole HTTP.
> * Zamknij połączenie z kodem stanu 400 (złe żądanie) i nie obsługuj żądania.

::: moniker-end

## <a name="require-https"></a>Wymagaj protokołu HTTPS

Zalecamy korzystanie z aplikacji sieci Web ASP.NET Core produkcyjnych:

* Oprogramowanie pośredniczące przekierowania HTTPS ( <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> ) do przekierowywania żądań HTTP do protokołu HTTPS.
* HSTSe oprogramowanie pośredniczące ([UseHsts](#http-strict-transport-security-protocol-hsts)) do wysyłania nagłówków HTTP Strict Transport Security Protocol (HSTS) do klientów.

> [!NOTE]
> Aplikacje wdrożone w konfiguracji zwrotnego serwera proxy pozwalają serwerowi proxy obsługiwać zabezpieczenia połączeń (HTTPS). Jeśli serwer proxy obsługuje również przekierowywanie przy użyciu protokołu HTTPS, nie ma potrzeby używania oprogramowania pośredniczącego do przekierowania protokołu HTTPS. Jeśli serwer proxy obsługuje również zapisywanie nagłówków HSTS (na przykład [natywnej obsługi HSTS w usługach IIS 10,0 (1709) lub nowszej](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), oprogramowanie pośredniczące HSTS nie jest wymagane przez aplikację. Aby uzyskać więcej informacji, zobacz [rezygnacja z protokołu HTTPS/HSTS podczas tworzenia projektu](#opt-out-of-httpshsts-on-project-creation).

### <a name="usehttpsredirection"></a>UseHttpsRedirection

Następujący kod wywołuje `UseHttpsRedirection` w `Startup` klasie:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

Poprzedni wyróżniony kod:

* Używa domyślnej [HttpsRedirectionOptions. RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).
* Używa domyślnego [HttpsRedirectionOptions. HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null), chyba że jest zastępowany przez `ASPNETCORE_HTTPS_PORT` zmienną środowiskową lub [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).

Zalecamy używanie tymczasowych przekierowań zamiast trwałych przekierowań. Buforowanie łączy może spowodować niestabilne zachowanie w środowiskach deweloperskich. Jeśli wolisz wysyłać kod stanu stałego przekierowania, gdy aplikacja znajduje się w środowisku innym niż programowanie, zobacz sekcję [Konfigurowanie stałych przekierowań w produkcji](#configure-permanent-redirects-in-production) . Zalecamy używanie [HSTS](#http-strict-transport-security-protocol-hsts) do sygnalizowania klientów, że tylko zabezpieczone żądania zasobów powinny być wysyłane do aplikacji (tylko w środowisku produkcyjnym).

### <a name="port-configuration"></a>Konfiguracja portu

Aby przekierować niezabezpieczone żądanie do protokołu HTTPS, Port musi być dostępny dla oprogramowania pośredniczącego. Jeśli port nie jest dostępny:

* Przekierowanie do protokołu HTTPS nie jest wykonywane.
* Oprogramowanie pośredniczące rejestruje ostrzeżenie "nie można określić portu HTTPS do przekierowania".

Określ port HTTPS przy użyciu dowolnej z następujących metod:

* Ustaw [HttpsRedirectionOptions. HttpsPort](#options).

::: moniker range=">= aspnetcore-3.0"

* Ustaw `https_port` [ustawienie hosta](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port):

  * W obszarze Konfiguracja hosta.
  * Przez ustawienie `ASPNETCORE_HTTPS_PORT` zmiennej środowiskowej.
  * Dodając wpis najwyższego poziomu w *appsettings.jsna*:

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/appsettings.json?highlight=2)]

* Wskaż port z bezpiecznym schematem przy użyciu [zmiennej środowiskowej ASPNETCORE_URLS](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls). Zmienna środowiskowa służy do konfigurowania serwera. Oprogramowanie pośredniczące pośrednio wykrywa port HTTPS za pośrednictwem <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> . Takie podejście nie działa w przypadku wdrożeń zwrotnych serwerów proxy.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* Ustaw `https_port` [ustawienie hosta](xref:fundamentals/host/web-host#https-port):

  * W obszarze Konfiguracja hosta.
  * Przez ustawienie `ASPNETCORE_HTTPS_PORT` zmiennej środowiskowej.
  * Dodając wpis najwyższego poziomu w *appsettings.jsna*:

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/appsettings.json?highlight=2)]

* Wskaż port z bezpiecznym schematem przy użyciu [zmiennej środowiskowej ASPNETCORE_URLS](xref:fundamentals/host/web-host#server-urls). Zmienna środowiskowa służy do konfigurowania serwera. Oprogramowanie pośredniczące pośrednio wykrywa port HTTPS za pośrednictwem <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> . Takie podejście nie działa w przypadku wdrożeń zwrotnych serwerów proxy.

::: moniker-end

* W obszarze programowanie Ustaw adres URL HTTPS w *launchsettings.jsna*. Włącz protokół HTTPS, gdy zostanie użyta IIS Express.

* Skonfiguruj punkt końcowy adresu URL HTTPS dla wdrożenia publicznej krawędzi serwera [Kestrel](xref:fundamentals/servers/kestrel) lub serwera [HTTP.sys](xref:fundamentals/servers/httpsys) . Aplikacja używa tylko **jednego portu HTTPS** . Oprogramowanie pośredniczące odnajduje port za pośrednictwem programu <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> .

> [!NOTE]
> Gdy aplikacja jest uruchamiana w konfiguracji zwrotnego serwera proxy, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> jest niedostępna. Ustaw port przy użyciu jednej z innych metod opisanych w tej sekcji.

### <a name="edge-deployments"></a>Wdrożenia brzegowe 

Gdy Kestrel lub HTTP.sys jest używany jako publiczny serwer graniczny, Kestrel lub HTTP.sys musi być skonfigurowany do nasłuchiwania na obu:

* Bezpieczny port do przekierowania przez klienta (zazwyczaj 443 w środowisku produkcyjnym i 5001 podczas tworzenia).
* Niezabezpieczony port (zazwyczaj 80 w środowisku produkcyjnym i 5000 podczas tworzenia).

Niezabezpieczony Port musi być dostępny dla klienta, aby aplikacja mogła odebrać niezabezpieczone żądanie i przekierować klienta do bezpiecznego portu.

Aby uzyskać więcej informacji, zobacz [Kestrel Endpoint Configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) lub <xref:fundamentals/servers/httpsys> .

### <a name="deployment-scenarios"></a>Scenariusze wdrażania

Wszystkie zapory między klientem a serwerem muszą także mieć otwarte porty komunikacyjne dla ruchu.

Jeśli żądania są przekazywane w konfiguracji zwrotnego serwera proxy, przed wywołaniem oprogramowania pośredniczącego do przekierowywania HTTPS Użyj [oprogramowania pośredniczącego](xref:host-and-deploy/proxy-load-balancer) . Przesłane nagłówki — oprogramowanie pośredniczące aktualizuje `Request.Scheme` program, używając `X-Forwarded-Proto` nagłówka. Oprogramowanie pośredniczące umożliwia poprawne działanie identyfikatorów URI przekierowania i innych zasad zabezpieczeń. Gdy przekazane nagłówki nie są używane, aplikacja zaplecza może nie otrzymać poprawnego schematu i zakończyć działania w pętli przekierowania. Typowy komunikat o błędzie użytkownika końcowego polega na tym, że wystąpiły zbyt wiele przekierowań.

Podczas wdrażania programu w celu Azure App Service postępuj zgodnie ze wskazówkami podanymi w [samouczku: Powiąż istniejący niestandardowy certyfikat SSL z usługą Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).

### <a name="options"></a>Opcje

Następujący wyróżniony kod wywołuje [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) , aby skonfigurować opcje oprogramowania pośredniczącego:


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


Wywołanie `AddHttpsRedirection` jest niezbędne tylko do zmiany wartości `HttpsPort` lub `RedirectStatusCode` .

Poprzedni wyróżniony kod:

* Ustawia [HttpsRedirectionOptions. RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) na <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect> , który jest wartością domyślną. Użyj pól <xref:Microsoft.AspNetCore.Http.StatusCodes> klasy do przypisywania do `RedirectStatusCode` .
* Ustawia port HTTPS na 5001.

#### <a name="configure-permanent-redirects-in-production"></a>Konfigurowanie stałych przekierowań w środowisku produkcyjnym

Ustawienia domyślne oprogramowania pośredniczącego do wysyłania [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) z wszystkimi przekierowaniami. Jeśli wolisz wysyłać kod stanu stałego przekierowania, gdy aplikacja znajduje się w środowisku nieprogramistycznym, Zapakuj konfigurację opcji oprogramowania pośredniczącego w ramach sprawdzania warunkowego dla środowiska nieprogramistycznego.

::: moniker range=">= aspnetcore-3.0"

Podczas konfigurowania usług w programie *Startup.cs*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Podczas konfigurowania usług w programie *Startup.cs*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a>Alternatywne podejście do przekierowania protokołu HTTPS

Alternatywą dla korzystania z oprogramowania pośredniczącego do przekierowania protokołu HTTPS `UseHttpsRedirection` jest użycie oprogramowania pośredniczącego ( `AddRedirectToHttps` ). `AddRedirectToHttps`można również ustawić kod stanu i port, gdy przekierowanie jest wykonywane. Aby uzyskać więcej informacji, zobacz Ponowne [Zapisywanie oprogramowania pośredniczącego w adresie URL](xref:fundamentals/url-rewriting).

Podczas przekierowywania do protokołu HTTPS bez wymagania dotyczącego dodatkowych reguł przekierowywania zalecamy używanie oprogramowania pośredniczącego do przekierowania protokołu HTTPS ( `UseHttpsRedirection` ) opisanego w tym temacie.

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a>Protokół HTTP Strict Transport Security Protocol (HSTS)

Na [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [http Strict Transport Security (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) jest opcjonalnym ulepszeniem zabezpieczeń, określonym przez aplikację sieci Web przy użyciu nagłówka odpowiedzi. Gdy [przeglądarka, która obsługuje HSTS,](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) otrzymuje ten nagłówek:

* Przeglądarka przechowuje konfigurację dla domeny, która uniemożliwia wysyłanie komunikacji za pośrednictwem protokołu HTTP. Przeglądarka wymusza całą komunikację za pośrednictwem protokołu HTTPS.
* Przeglądarka uniemożliwia użytkownikowi korzystanie z niezaufanych lub nieprawidłowych certyfikatów. Przeglądarka wyłącza wyświetlanie wierszy, które umożliwiają użytkownikowi tymczasowe zaufać temu certyfikatowi.

Ponieważ HSTS jest wymuszana przez klienta, ma pewne ograniczenia:

* Klient musi obsługiwać HSTS.
* HSTS wymaga co najmniej jednego pomyślnego żądania HTTPS do ustanowienia zasad HSTS.
* Aplikacja musi sprawdzić każde żądanie HTTP i przekierować lub odrzucić żądanie HTTP.

ASP.NET Core 2,1 i nowsze implementują HSTS z `UseHsts` metodą rozszerzenia. Następujący kod wywołuje, `UseHsts` gdy aplikacja nie jest w [trybie deweloperskim](xref:fundamentals/environments):

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

`UseHsts`nie jest zalecane w programowaniu, ponieważ ustawienia HSTS mają wysoką pamięć podręczną przez przeglądarki. Domyślnie program `UseHsts` wyklucza adres lokalnego sprzężenia zwrotnego.

W przypadku środowisk produkcyjnych, które wdrażają protokół HTTPS po raz pierwszy, należy ustawić początkowy [HstsOptions. maxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) na małą wartość przy użyciu jednej z <xref:System.TimeSpan> metod. Ustaw wartość od godziny na nie więcej niż jeden dzień w przypadku konieczności przywrócenia infrastruktury HTTPS do protokołu HTTP. Po upewnieniu się, że trwałość konfiguracji protokołu HTTPS jest stabilna, zwiększ `max-age` wartość HSTS. najczęściej używaną wartością jest jeden rok.

Następujący kod:


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* Ustawia parametr wstępnego ładowania `Strict-Transport-Security` nagłówka. Wstępne ładowanie nie jest częścią [specyfikacji RFC HSTS](https://tools.ietf.org/html/rfc6797), ale jest obsługiwane przez przeglądarki sieci Web do wstępnego ładowania witryn HSTS w przypadku instalacji nowej. Aby uzyskać więcej informacji, zobacz [https://hstspreload.org/](https://hstspreload.org/).
* Włącza [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), która stosuje zasady HSTS do hostowania poddomen.
* Jawnie ustawia `max-age` parametr `Strict-Transport-Security` nagłówka na 60 dni. Jeśli nie zostanie ustawiona, wartość domyślna to 30 dni. Aby uzyskać więcej informacji, zobacz [dyrektywa max-age](https://tools.ietf.org/html/rfc6797#section-6.1.1).
* Dodaje `example.com` do listy hostów, które mają zostać wykluczone.

`UseHsts`wyklucza następujące hosty sprzężenia zwrotnego:

* `localhost`: Adres sprzężenia zwrotnego IPv4.
* `127.0.0.1`: Adres sprzężenia zwrotnego IPv4.
* `[::1]`: Adres sprzężenia zwrotnego IPv6.

## <a name="opt-out-of-httpshsts-on-project-creation"></a>Zrezygnuj z protokołu HTTPS/HSTS podczas tworzenia projektu

W niektórych scenariuszach usługi zaplecza, w których zabezpieczenia połączeń są obsługiwane na publicznej krawędzi sieci, skonfigurowanie zabezpieczeń połączeń w każdym węźle nie jest wymagane. Aplikacje sieci Web, które są generowane na podstawie szablonów w programie Visual Studio lub za pomocą polecenia [dotnet New](/dotnet/core/tools/dotnet-new) , umożliwiają [przekierowania https](#require-https) i [HSTS](#http-strict-transport-security-protocol-hsts). W przypadku wdrożeń, które nie wymagają tych scenariuszy, można zrezygnować z protokołu HTTPS/HSTS podczas tworzenia aplikacji na podstawie szablonu.

Aby zrezygnować z protokołu HTTPS/HSTS:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio) 

Usuń zaznaczenie pola wyboru **Konfiguruj dla protokołu HTTPS** .

::: moniker range=">= aspnetcore-3.0"

![Okno dialogowe nowe ASP.NET Core aplikacji sieci Web z niezaznaczonym polem wyboru Konfiguruj dla protokołu HTTPS.](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![Okno dialogowe nowe ASP.NET Core aplikacji sieci Web z niezaznaczonym polem wyboru Konfiguruj dla protokołu HTTPS.](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli) 

Użyj `--no-https` opcji. Na przykład:

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a>Ufaj certyfikatowi Deweloperskiemu protokołu HTTPS ASP.NET Core w systemach Windows i macOS

Zestaw .NET Core SDK zawiera certyfikat programistyczny HTTPS. Certyfikat jest instalowany w ramach pierwszego uruchomienia. Na przykład program `dotnet --info` tworzy odmianę następujących danych wyjściowych:

```
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

Zainstalowanie zestaw .NET Core SDK instaluje certyfikat deweloperski ASP.NET Core HTTPS do magazynu certyfikatów użytkownika lokalnego. Certyfikat został zainstalowany, ale nie jest zaufany. Aby zaufać certyfikatowi, wykonaj jednorazowy krok w celu uruchomienia narzędzia dotnet `dev-certs` :

```dotnetcli
dotnet dev-certs https --trust
```

Następujące polecenie zapewnia pomoc dotyczącą `dev-certs` Narzędzia:

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a>Jak skonfigurować certyfikat dewelopera dla platformy Docker

Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/6199)w serwisie GitHub.

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a>Ufaj certyfikatowi HTTPS z podsystemu Windows dla systemu Linux

Podsystem Windows dla systemu Linux (WSL) generuje certyfikat z podpisem własnym HTTPS. Aby skonfigurować magazyn certyfikatów systemu Windows w celu zaufać certyfikatowi WSL:

* Uruchom następujące polecenie, aby wyeksportować certyfikat wygenerowany przez WSL:

  ```
  dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>
  ```
* W oknie WSL Uruchom następujące polecenie:

  ```
    ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" 
    ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx
    dotnet watch run
  ```

  Poprzednie polecenie ustawia zmienne środowiskowe, aby system Linux używał zaufanego certyfikatu systemu Windows.

## <a name="troubleshoot-certificate-problems"></a>Rozwiązywanie problemów z certyfikatami

Ta sekcja zawiera informacje ułatwiające [zainstalowanie i zaufanie](#trust)certyfikatu deweloperskiego https ASP.NET Core, ale nadal masz ostrzeżenia przeglądarki, że certyfikat nie jest zaufany. ASP.NET Core certyfikat programistyczny HTTPS jest używany przez [Kestrel](xref:fundamentals/servers/kestrel).

### <a name="all-platforms---certificate-not-trusted"></a>Wszystkie platformy — certyfikat niezaufany

Uruchom następujące polecenia:

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

Zamknij wszystkie otwarte wystąpienia przeglądarki. Otwórz nowe okno przeglądarki do aplikacji. Zaufanie certyfikatu jest buforowane przez przeglądarki.

Powyższe polecenia rozwiązują większość problemów z zaufaniem do przeglądarki. Jeśli przeglądarka nadal nie ufa certyfikatowi, postępuj zgodnie z sugestiami specyficznymi dla danej platformy.

### <a name="docker---certificate-not-trusted"></a>Zaufać certyfikatowi platformy Docker

* Usuń folder *C:\Users \{ User} \AppData\Roaming\ASP.NET\Https* .
* Wyczyść rozwiązanie. Usuń foldery *bin* i *obj* .
* Uruchom ponownie narzędzie programistyczne. Na przykład Visual Studio, Visual Studio Code lub Visual Studio dla komputerów Mac.

### <a name="windows---certificate-not-trusted"></a>Windows-certyfikat niezaufany

* Sprawdź certyfikaty w magazynie certyfikatów. Powinien istnieć `localhost` certyfikat o `ASP.NET Core HTTPS development certificate` przyjaznej nazwie w obszarze `Current User > Personal > Certificates` i`Current User > Trusted root certification authorities > Certificates`
* Usuń wszystkie znalezione certyfikaty zarówno z prywatnych, jak i zaufanych głównych urzędów certyfikacji. **Nie** usuwaj IIS Express certyfikatu localhost.
* Uruchom następujące polecenia:

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

Zamknij wszystkie otwarte wystąpienia przeglądarki. Otwórz nowe okno przeglądarki do aplikacji.

### <a name="os-x---certificate-not-trusted"></a>OS X — certyfikat niezaufany

* Otwórz dostęp do łańcucha kluczy.
* Wybierz system łańcucha kluczy.
* Sprawdź obecność certyfikatu localhost.
* Sprawdź, czy zawiera `+` symbol na ikonie, aby wskazać, że jest on zaufany dla wszystkich użytkowników.
* Usuń certyfikat z łańcucha kluczy systemu.
* Uruchom następujące polecenia:

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

Zamknij wszystkie otwarte wystąpienia przeglądarki. Otwórz nowe okno przeglądarki do aplikacji.

Aby rozwiązać problemy z certyfikatami w programie Visual Studio, zobacz [błąd protokołu HTTPS przy użyciu IIS Express (#16892 dotnet/AspNetCore)](https://github.com/dotnet/AspNetCore/issues/16892) .

### <a name="iis-express-ssl-certificate-used-with-visual-studio"></a>IIS Express certyfikat SSL używany z programem Visual Studio

Aby rozwiązać problemy z certyfikatem IIS Express, wybierz pozycję **napraw** w Instalatorze programu Visual Studio. Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/aspnetcore/issues/16892)w serwisie GitHub.

## <a name="additional-information"></a>Dodatkowe informacje

* <xref:host-and-deploy/proxy-load-balancer>
* [Hostowanie ASP.NET Core w systemie Linux przy użyciu programu Apache: Konfiguracja protokołu HTTPS](xref:host-and-deploy/linux-apache#https-configuration)
* [ASP.NET Core hosta w systemie Linux z Nginx: Konfiguracja HTTPS](xref:host-and-deploy/linux-nginx#https-configuration)
* [Jak skonfigurować protokół SSL w usługach IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [Obsługa przeglądarki OWASP HSTS](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
