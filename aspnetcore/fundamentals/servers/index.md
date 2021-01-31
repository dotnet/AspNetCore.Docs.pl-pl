---
title: Implementacje serwera sieci Web w ASP.NET Core
author: rick-anderson
description: Odkryj serwery sieci Web Kestrel i HTTP.sys ASP.NET Core. Dowiedz się, jak wybrać serwer i kiedy używać serwera zwrotnego serwera proxy.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
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
uid: fundamentals/servers/index
ms.openlocfilehash: 2acddd212639ac0a82b3c46f2225ff66d0999dd0
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217560"
---
# <a name="web-server-implementations-in-aspnet-core"></a>Implementacje serwera sieci Web w ASP.NET Core

Autorzy [Dykstra](https://github.com/tdykstra), [Steve Kowalski](https://ardalis.com/), [Stephen](https://twitter.com/halter73), i [Krzysztof Ross](https://github.com/Tratcher)

Aplikacja ASP.NET Core jest uruchamiana z użyciem implementacji serwera HTTP w procesie. Implementacja serwera nasłuchuje żądań HTTP i umieszcza je w aplikacji jako zestaw [funkcji żądania](xref:fundamentals/request-features) złożonych w <xref:Microsoft.AspNetCore.Http.HttpContext> .

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core dostarcza następujące elementy:

* [Serwer Kestrel](xref:fundamentals/servers/kestrel) jest domyślną implementacją międzyplatformowego serwera http. Kestrel zapewnia najlepszą wydajność i użycie pamięci, ale nie oferuje niektórych zaawansowanych funkcji w HTTP.sys. Aby uzyskać więcej informacji, zobacz [Kestrel a HTTP.sys](#korh) w tym dokumencie.
* Serwer HTTP usług IIS jest [serwerem w procesie](#hosting-models) dla usług IIS.
* [ SerwerHTTP.sys](xref:fundamentals/servers/httpsys) to serwer HTTP z systemem Windows oparty na [HTTP.sys sterownika jądra i interfejsu API serwera http](/windows/desktop/Http/http-api-start-page).

W przypadku korzystania z [usług IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) lub [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)uruchomiona jest aplikacja:

* W tym samym procesie co proces roboczy usług IIS ( [model hostingu w procesie](#hosting-models)) z serwerem HTTP usług IIS. *W procesie* jest zalecana konfiguracja.
* W procesie innym niż proces roboczy usług IIS ( [model hostingu poza procesem](#hosting-models)) z [serwerem Kestrel](#kestrel).

[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) jest natywnym modułem usług IIS, który obsługuje natywne żądania usług IIS między usługami IIS a serwerem HTTP lub Kestrel IIS w procesie. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module>.

<a name="korh"></a>

## <a name="kestrel-vs-httpsys"></a>Kestrel a HTTP.sys

Kestrel ma następujące zalety w stosunku do HTTP.sys:

  * Lepsza wydajność i użycie pamięci.
  * Wiele platform
  * Elastyczność, opracowana i niezależna od systemu operacyjnego.
  * Port programistyczny i konfiguracja protokołu TLS
  * Rozszerzalność umożliwiająca obsługę protokołów, takich jak [PPv2](https://github.com/aspnet/AspLabs/blob/master/src/ProxyProtocol/ProxyProtocol.Sample/ProxyProtocol.cs) i alternatywne transporty.

Http.Sys działa jako współużytkowany składnik trybu jądra z następującymi funkcjami, które nie są dostępne dla Kestrel:

  * Udostępnianie portów
  * Uwierzytelnianie systemu Windows trybu jądra. [Kestrel obsługuje tylko uwierzytelnianie w trybie użytkownika](xref:security/authentication/windowsauth#kestrel).
  * Szybki serwer proxy za pośrednictwem transferów kolejek
  * Bezpośrednia transmisja plików
  * Buforowanie odpowiedzi

## <a name="hosting-models"></a>Modele hostingu

Korzystając z hostingu w procesie, aplikacja ASP.NET Core jest uruchamiana w tym samym procesie co proces roboczy usług IIS. Hosting w procesie zapewnia lepszą wydajność w porównaniu z obsługą hostingu, ponieważ żądania nie są kierowane do serwera proxy za pośrednictwem karty sprzężenia zwrotnego, czyli interfejsu sieciowego, który zwraca wychodzący ruch sieciowy z powrotem do tego samego komputera. Usługi IIS obsługują zarządzanie procesami przy użyciu [usługi aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Korzystając z hostingu poza procesem, ASP.NET Core aplikacje są uruchamiane w procesie oddzielonym od procesu roboczego usług IIS, a moduł obsługuje zarządzanie procesami. Moduł uruchamia proces dla aplikacji ASP.NET Core, gdy pierwsze żądanie zostanie odebrane i ponownie uruchomiony, jeśli zostanie zamknięty lub ulegnie awarii. Jest to zasadniczo takie samo zachowanie jak w przypadku aplikacji uruchamianych w procesie, które są zarządzane przez [usługę aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Aby uzyskać więcej informacji i wskazówki dotyczące konfiguracji, zobacz następujące tematy:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym serwerem HTTP dla wielu platform.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym serwerem HTTP dla wielu platform.

---

::: moniker-end

## <a name="kestrel"></a>Kestrel

 [Serwer Kestrel](xref:fundamentals/servers/kestrel) jest domyślną implementacją międzyplatformowego serwera http. Kestrel zapewnia najlepszą wydajność i użycie pamięci, ale nie oferuje niektórych zaawansowanych funkcji w HTTP.sys. Aby uzyskać więcej informacji, zobacz [Kestrel a HTTP.sys](#korh) w tym dokumencie.

Użyj Kestrel:

* Jako serwer graniczny przetwarza żądania bezpośrednio z sieci, w tym z Internetu.

  ![Kestrel komunikuje się bezpośrednio z Internetem bez serwera proxy zwrotnego](kestrel/_static/kestrel-to-internet2.png)

* *Serwer proxy zwrotnego*, taki jak [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)lub [Apache](https://httpd.apache.org/). Odwrotny serwer proxy odbiera żądania HTTP z Internetu i przekazuje je do usługi Kestrel.

  ![Kestrel komunikuje się pośrednio z Internetem za pomocą odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache](kestrel/_static/kestrel-to-internet.png)

&mdash;Obsługiwana jest konfiguracja hostingu z serwerem zwrotnego serwera proxy lub bez niego &mdash; .

Aby uzyskać wskazówki dotyczące konfiguracji Kestrel i informacje o tym, kiedy używać Kestrel w konfiguracji zwrotnego serwera proxy, zobacz <xref:fundamentals/servers/kestrel> .

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core dostarcza następujące elementy:

* [Serwer Kestrel](xref:fundamentals/servers/kestrel) jest domyślnym serwerem HTTP dla wielu platform.
* [ SerwerHTTP.sys](xref:fundamentals/servers/httpsys) to serwer HTTP z systemem Windows oparty na [HTTP.sys sterownika jądra i interfejsu API serwera http](/windows/desktop/Http/http-api-start-page).

W przypadku korzystania z [usług IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) lub [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)aplikacja działa w procesie innym niż proces roboczy usług IIS (*poza procesem*) z [serwerem Kestrel](#kestrel).

Ponieważ ASP.NET Core aplikacje działają w procesie innym niż proces roboczy usług IIS, moduł obsługuje zarządzanie procesami. Moduł uruchamia proces dla aplikacji ASP.NET Core, gdy pierwsze żądanie zostanie odebrane i ponownie uruchomiony, jeśli zostanie zamknięty lub ulegnie awarii. Jest to zasadniczo takie samo zachowanie jak w przypadku aplikacji uruchamianych w procesie, które są zarządzane przez [usługę aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Na poniższym diagramie przedstawiono relację między usługami IIS, modułem ASP.NET Core i hostowanym przez aplikację aplikacją:

![Moduł ASP.NET Core](_static/ancm-outofprocess.png)

Żądania docierają z sieci Web do sterownika HTTP.sys trybu jądra. Sterownik kieruje żądania do usług IIS na skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS). Moduł przekazuje żądania do Kestrel na losowo wybranym porcie dla aplikacji, która nie jest portem 80 lub 443.

Moduł określa port za pośrednictwem zmiennej środowiskowej podczas uruchamiania, a [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) konfiguruje serwer do nasłuchiwania `http://localhost:{port}` . Dodatkowe sprawdzenia są wykonywane, a żądania, które nie pochodzą z modułu, są odrzucane. Moduł nie obsługuje przekazywania HTTPS, dlatego żądania są przekazywane przez protokół HTTP nawet wtedy, gdy są odbierane przez usługę IIS przez protokół HTTPS.

Po podaniu przez Kestrel żądania z modułu żądanie jest wypychane do potoku ASP.NET Core pośredniczącego. Potok oprogramowania pośredniczącego obsługuje żądanie i przekazuje go jako `HttpContext` wystąpienie do logiki aplikacji. Oprogramowanie pośredniczące dodane przez integrację usług IIS aktualizuje schemat, zdalny adres IP i pathbase, aby można było przesłać żądanie do Kestrel. Odpowiedź aplikacji jest przesyłana z powrotem do usług IIS, która wypycha ją z powrotem do klienta HTTP, który zainicjował żądanie.

Aby uzyskać wskazówki dotyczące konfiguracji modułu usług IIS i ASP.NET Core, zobacz następujące tematy:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym serwerem HTTP dla wielu platform.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym serwerem HTTP dla wielu platform.

---

::: moniker-end

### <a name="nginx-with-kestrel"></a>Nginx z Kestrel

Aby uzyskać informacje na temat używania Nginx w systemie Linux jako serwera zwrotnego proxy dla Kestrel, zobacz <xref:host-and-deploy/linux-nginx> .

### <a name="apache-with-kestrel"></a>Apache z Kestrel

Aby uzyskać informacje na temat korzystania z usługi Apache w systemie Linux jako serwera zwrotnego proxy dla usługi Kestrel, zobacz <xref:host-and-deploy/linux-apache> .

## <a name="httpsys"></a>HTTP.sys

Jeśli ASP.NET Core aplikacje są uruchamiane w systemie Windows, HTTP.sys jest alternatywą dla Kestrel. Kestrel jest zalecane w stosunku do HTTP.sys, chyba że aplikacja wymaga funkcji niedostępnych w Kestrel. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/httpsys>.

![HTTP.sys komunikują się bezpośrednio z Internetem](httpsys/_static/httpsys-to-internet.png)

HTTP.sys można również używać dla aplikacji, które są dostępne tylko w sieci wewnętrznej.

![HTTP.sys komunikuje się bezpośrednio z siecią wewnętrzną](httpsys/_static/httpsys-to-internal.png)

Aby uzyskać HTTP.sys wskazówki dotyczące konfiguracji, zobacz <xref:fundamentals/servers/httpsys> .

## <a name="aspnet-core-server-infrastructure"></a>Infrastruktura serwera ASP.NET Core

<xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>Dostępne w `Startup.Configure` metodzie uwidacznia <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> Właściwość typu <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection> . Kestrel i HTTP.sys uwidaczniają tylko jedną funkcję, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> ale różne implementacje serwera mogą uwidaczniać dodatkowe funkcje.

`IServerAddressesFeature` można go użyć, aby dowiedzieć się, który port implementacji serwera w czasie wykonywania.

## <a name="custom-servers"></a>Serwery niestandardowe

Jeśli wbudowane serwery nie spełniają wymagań aplikacji, można utworzyć niestandardową implementację serwera. W [przewodniku otwierania interfejsu sieci Web dla platformy .NET (Owin)](xref:fundamentals/owin) przedstawiono sposób pisania implementacji opartej na [nowin](https://github.com/Bobris/Nowin) <xref:Microsoft.AspNetCore.Hosting.Server.IServer> . Tylko interfejsy funkcji używane przez aplikację wymagają implementacji, ale <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> muszą być obsługiwane w minimalnym i <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> wymaganym czasie.

## <a name="server-startup"></a>Uruchamianie serwera

Serwer jest uruchamiany po uruchomieniu aplikacji zintegrowanego środowiska programistycznego (IDE) lub edytora:

* [Visual Studio](https://visualstudio.microsoft.com): profile uruchamiania mogą służyć do uruchamiania aplikacji i serwera za pomocą [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) / [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) lub konsoli programu.
* [Visual Studio Code](https://code.visualstudio.com/): aplikacja i serwer są uruchamiane przez [omnisharp](https://github.com/OmniSharp/omnisharp-vscode), która aktywuje debuger CoreCLR.
* [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/): aplikacja i serwer są uruchamiane za pomocą [debugera Soft-Mode mono](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).

Podczas uruchamiania aplikacji z poziomu wiersza polecenia w folderze projektu, [uruchomienie dotnet](/dotnet/core/tools/dotnet-run) uruchamia aplikację i serwer (tylko Kestrel i HTTP.sys). Konfiguracja jest określana przez `-c|--configuration` opcję, która jest ustawiona na wartość `Debug` (domyślnie) lub `Release` .

*launchSettings.jsw* pliku zapewnia konfigurację podczas uruchamiania aplikacji przy użyciu `dotnet run` debugera wbudowanego w narzędzia, takiego jak Visual Studio. Jeśli profile uruchamiania są obecne w *launchSettings.jsw* pliku, użyj `--launch-profile {PROFILE NAME}` opcji z `dotnet run` poleceniem lub wybierz profil w programie Visual Studio. Aby uzyskać więcej informacji, [](/dotnet/core/tools/dotnet-run) zobacz [pakietem rozkładu dotnet i .NET Core](/dotnet/core/build/distribution-packaging).

## <a name="http2-support"></a>Obsługa protokołu HTTP/2

[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest obsługiwany z ASP.NET Core w następujących scenariuszach wdrażania:

::: moniker range=">= aspnetcore-5.0"

* [Kestrel](xref:fundamentals/servers/kestrel/http2)
  * System operacyjny
    * Windows Server 2016/Windows 10 lub nowszy&dagger;
    * Linux z OpenSSL 1.0.2 lub nowszym (na przykład Ubuntu 16,04 lub nowszy)
    * Protokół HTTP/2 będzie obsługiwany w przypadku macOS w przyszłej wersji.
  * Platforma docelowa: .NET Core 2,2 lub nowszy
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 lub nowszy
  * Struktura docelowa: nie dotyczy wdrożeń HTTP.sys.
* [Usługi IIS (w procesie)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy
  * Platforma docelowa: .NET Core 2,2 lub nowszy
* [Usługi IIS (pozaprocesowe)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy
  * Połączenia z serwerem granicznym dostępnym publicznie korzystają z protokołu HTTP/2, ale połączenie zwrotne serwera proxy z Kestrel korzysta z protokołu HTTP/1.1.
  * Struktura docelowa: nie dotyczy wdrożeń pozaprocesowych usług IIS.

&dagger;Kestrel ma ograniczoną obsługę protokołu HTTP/2 w systemie Windows Server 2012 R2 i Windows 8.1. Obsługa jest ograniczona, ponieważ lista obsługiwanych mechanizmów szyfrowania TLS dostępnych w tych systemach operacyjnych jest ograniczona. Do zabezpieczenia połączeń TLS może być wymagany certyfikat wygenerowany przy użyciu algorytmu podpisu cyfrowego (ECDSA) krzywej eliptycznej.

::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"

* [Kestrel](xref:fundamentals/servers/kestrel#http2-support)
  * System operacyjny
    * Windows Server 2016/Windows 10 lub nowszy&dagger;
    * Linux z OpenSSL 1.0.2 lub nowszym (na przykład Ubuntu 16,04 lub nowszy)
    * Protokół HTTP/2 będzie obsługiwany w przypadku macOS w przyszłej wersji.
  * Platforma docelowa: .NET Core 2,2 lub nowszy
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 lub nowszy
  * Struktura docelowa: nie dotyczy wdrożeń HTTP.sys.
* [Usługi IIS (w procesie)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy
  * Platforma docelowa: .NET Core 2,2 lub nowszy
* [Usługi IIS (pozaprocesowe)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy
  * Połączenia z serwerem granicznym dostępnym publicznie korzystają z protokołu HTTP/2, ale połączenie zwrotne serwera proxy z Kestrel korzysta z protokołu HTTP/1.1.
  * Struktura docelowa: nie dotyczy wdrożeń pozaprocesowych usług IIS.

&dagger;Kestrel ma ograniczoną obsługę protokołu HTTP/2 w systemie Windows Server 2012 R2 i Windows 8.1. Obsługa jest ograniczona, ponieważ lista obsługiwanych mechanizmów szyfrowania TLS dostępnych w tych systemach operacyjnych jest ograniczona. Do zabezpieczenia połączeń TLS może być wymagany certyfikat wygenerowany przy użyciu algorytmu podpisu cyfrowego (ECDSA) krzywej eliptycznej.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 lub nowszy
  * Struktura docelowa: nie dotyczy wdrożeń HTTP.sys.
* [Usługi IIS (pozaprocesowe)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy
  * Połączenia z serwerem granicznym dostępnym publicznie korzystają z protokołu HTTP/2, ale połączenie zwrotne serwera proxy z Kestrel korzysta z protokołu HTTP/1.1.
  * Struktura docelowa: nie dotyczy wdrożeń pozaprocesowych usług IIS.

::: moniker-end

Połączenie HTTP/2 musi korzystać z [negocjacji protokołu warstwy aplikacji (ClientHello alpn)](https://tools.ietf.org/html/rfc7301#section-3) i TLS 1,2 lub nowszej. Aby uzyskać więcej informacji, zapoznaj się z tematami dotyczącymi scenariuszy wdrażania serwera.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
