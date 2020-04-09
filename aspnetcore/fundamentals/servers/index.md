---
title: Implementacje serwera sieci Web w ASP.NET Core
author: rick-anderson
description: Odkryj serwery internetowe Kestrel i HTTP.sys dla ASP.NET Core. Dowiedz się, jak wybrać serwer i kiedy używać odwrotnego serwera proxy.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/servers/index
ms.openlocfilehash: d46793ef54c99fe609b5983c5a658fb7b20032fa
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666342"
---
# <a name="web-server-implementations-in-aspnet-core"></a>Implementacje serwera sieci Web w ASP.NET Core

[Przez Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), Stephen [Halter](https://twitter.com/halter73)i [Chris Ross](https://github.com/Tratcher)

Aplikacja ASP.NET Core jest uruchamiana z implementacją serwera HTTP w toku. Implementacja serwera nasłuchuje żądań HTTP i przedstawia je w aplikacji <xref:Microsoft.AspNetCore.Http.HttpContext>jako zestaw funkcji [żądania](xref:fundamentals/request-features) skomponowanych w pliku .

## <a name="kestrel"></a>Kestrel

Pustułka jest domyślnym serwerem www określonym przez szablony projektów ASP.NET Core.

Użyj Pustułki:

* Sam jako serwer brzegowy przetwarza żądania bezpośrednio z sieci, w tym z Internetu.

  ![Pustułka komunikuje się bezpośrednio z Internetem bez odwrotnego serwera proxy](kestrel/_static/kestrel-to-internet2.png)

* Z *odwrotnym serwerem proxy,* takim jak [Internetowe usługi informacyjne (IIS),](https://www.iis.net/) [Nginx](https://nginx.org)lub [Apache](https://httpd.apache.org/). Serwer odwrotnego serwera proxy odbiera żądania HTTP z Internetu i przekazuje je do Kestrel.

  ![Kestrel komunikuje się pośrednio z Internetem za pośrednictwem serwera odwrotnego serwera proxy, takiego jak IIS, Nginx lub Apache](kestrel/_static/kestrel-to-internet.png)

Obsługiwana jest&mdash;konfiguracja hostingu&mdash;z odwrotnym serwerem proxy lub bez niego.

Aby uzyskać wskazówki dotyczące konfiguracji kestrelu i informacje o <xref:fundamentals/servers/kestrel>tym, kiedy używać Kestrel w odwrotnej konfiguracji serwera proxy, zobacz .

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core jest dostarczany z następującymi elementami:

* [Serwer pustułki](xref:fundamentals/servers/kestrel) jest domyślną, wieloplatformową implementacją serwera HTTP.
* Serwer HTTP usług IIS jest [serwerem w trakcie](#hosting-models) usług IIS.
* [Serwer HTTP.sys](xref:fundamentals/servers/httpsys) jest serwerem HTTP dostępnym tylko dla systemu Windows opartym na [sterowniku jądra HTTP.sys i interfejsie API serwera HTTP.](/windows/desktop/Http/http-api-start-page)

W przypadku korzystania z [usługi IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) lub [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)aplikacja jest uruchamiana:

* W tym samym procesie co proces roboczy usług IIS [(model hostingu w trakcie)](#hosting-models)z serwerem HTTP usług IIS. *W procesie* jest zalecana konfiguracja.
* W procesie oddzielonym od procesu roboczego usług IIS [(model hostingu poza procesem)](#hosting-models)z [serwerem Kestrel](#kestrel).

[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) jest macierzystym modułem usług IIS, który obsługuje natywne żądania usług IIS między usługami IIS a serwerem HTTP usług IIS w trakcie procesu lub pustułką. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module>.

## <a name="hosting-models"></a>Modele hostingu

Korzystając z hostingu w trakcie, aplikacja ASP.NET Core działa w tym samym procesie co proces roboczy IIS. Hosting w trakcie zapewnia lepszą wydajność w zakresie hostingu poza procesem, ponieważ żądania nie są przerzaszane przez kartę sprzężenia zwrotnego, interfejs sieciowy, który zwraca wychodzący ruch sieciowy z powrotem na ten sam komputer. Usługi IIS obsługują zarządzanie procesami za pomocą [usługi aktywacji procesów systemu Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Korzystając z hostingu poza procesem, aplikacje core ASP.NET są uruchamiane w procesie odrębnym od procesu roboczego usług IIS, a moduł obsługuje zarządzanie procesami. Moduł uruchamia proces aplikacji ASP.NET Core po nadejściu pierwszego żądania i uruchamia ponownie aplikację, jeśli zostanie zamknięta lub ulegnie awarii. Jest to zasadniczo to samo zachowanie, co w aplikacjach uruchamianych w procesie, które są zarządzane przez [usługę aktywacji procesów systemu Windows (WAS).](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)

Aby uzyskać więcej informacji i wskazówek dotyczących konfiguracji, zobacz następujące tematy:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym, wieloplatformowym serwerem HTTP.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym, wieloplatformowym serwerem HTTP.

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core jest dostarczany z następującymi elementami:

* [Serwer pustułki](xref:fundamentals/servers/kestrel) jest domyślnym, wieloplatformowym serwerem HTTP.
* [Serwer HTTP.sys](xref:fundamentals/servers/httpsys) jest serwerem HTTP dostępnym tylko dla systemu Windows opartym na [sterowniku jądra HTTP.sys i interfejsie API serwera HTTP.](/windows/desktop/Http/http-api-start-page)

W przypadku korzystania z [usług IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) lub [usług IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)aplikacja jest uruchamiana w procesie odrębnym od procesu roboczego usług IIS *(poza procesem)* z [serwerem Kestrel](#kestrel).

Ponieważ ASP.NET aplikacje Core są uruchamiane w procesie odrębnym od procesu roboczego usług IIS, moduł obsługuje zarządzanie procesami. Moduł uruchamia proces aplikacji ASP.NET Core po nadejściu pierwszego żądania i uruchamia ponownie aplikację, jeśli zostanie zamknięta lub ulegnie awarii. Jest to zasadniczo to samo zachowanie, co w aplikacjach uruchamianych w procesie, które są zarządzane przez [usługę aktywacji procesów systemu Windows (WAS).](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)

Na poniższym diagramie przedstawiono relację między iis, ASP.NET Core Module i aplikacji hostowane poza procesem:

![Moduł ASP.NET Core](_static/ancm-outofprocess.png)

Żądania przychodzą z sieci Web do sterownika HTTP.sys w trybie jądra. Sterownik kieruje żądania do iIS w skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS). Moduł przekazuje żądania do Kestrel na losowym porcie dla aplikacji, który nie jest portem 80 lub 443.

Moduł określa port za pośrednictwem zmiennej środowiskowej podczas uruchamiania, a [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) konfiguruje serwer do nasłuchiwać na `http://localhost:{port}`. Dodatkowe kontrole są wykonywane, a żądania, które nie pochodzą z modułu są odrzucane. Moduł nie obsługuje przekazywania protokołu HTTPS, więc żądania są przekazywane za pośrednictwem protokołu HTTP, nawet jeśli są odbierane przez usługi IIS za pośrednictwem protokołu HTTPS.

Po Kestrel odbiera żądanie z modułu, żądanie jest wypychane do potoku oprogramowania pośredniczącego ASP.NET Core. Potok oprogramowania pośredniczącego obsługuje żądanie i `HttpContext` przekazuje go jako wystąpienie do logiki aplikacji. Oprogramowanie pośredniczące dodane przez integrację usług IIS aktualizuje schemat, zdalny adres IP i bazę ścieżek w celu przekazania żądania do Kestrel. Odpowiedź aplikacji jest przekazywana z powrotem do iIS, co wypycha ją z powrotem do klienta HTTP, który zainicjował żądanie.

W przypadku usług IIS i ASP.NET wskazówki dotyczące konfiguracji modułu podstawowego można znaleźć w następujących tematach:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym, wieloplatformowym serwerem HTTP.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), który jest domyślnym, wieloplatformowym serwerem HTTP.

---

::: moniker-end

### <a name="nginx-with-kestrel"></a>Nginx z kestrelem

Aby uzyskać informacje na temat używania Nginx w systemie Linux <xref:host-and-deploy/linux-nginx>jako odwrotnego serwera proxy dla Kestrel, zobacz .

### <a name="apache-with-kestrel"></a>Apache z Kestrel

Aby uzyskać informacje na temat używania Apache w systemie Linux <xref:host-and-deploy/linux-apache>jako odwrotnego serwera proxy dla Kestrel, zobacz .

## <a name="httpsys"></a>HTTP.sys

Jeśli aplikacje ASP.NET Core są uruchamiane w systemie Windows, http.sys jest alternatywą dla Kestrel. Pustułka jest ogólnie zalecana dla najlepszej wydajności. HTTP.sys może służyć w scenariuszach, w których aplikacja jest narażona na Internet i wymagane funkcje są obsługiwane przez HTTP.sys, ale nie Kestrel. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/httpsys>.

![HTTP.sys komunikuje się bezpośrednio z Internetem](httpsys/_static/httpsys-to-internet.png)

Http.sys może być również używany dla aplikacji, które są udostępniane tylko w sieci wewnętrznej.

![HTTP.sys komunikuje się bezpośrednio z siecią wewnętrzną](httpsys/_static/httpsys-to-internal.png)

Aby uzyskać wskazówki dotyczące konfiguracji <xref:fundamentals/servers/httpsys>http.sys, zobacz .

## <a name="aspnet-core-server-infrastructure"></a>ASP.NET Podstawowa infrastruktura serwera

Dostępne <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> w `Startup.Configure` metodzie udostępnia <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> właściwość <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>typu . Pustułka i HTTP.sys tylko uwidaczniają jedną funkcję, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>ale różne implementacje serwera mogą udostępniać dodatkowe funkcje.

`IServerAddressesFeature`można użyć, aby dowiedzieć się, który port implementacji serwera ma powiązane w czasie wykonywania.

## <a name="custom-servers"></a>Serwery niestandardowe

Jeśli wbudowane serwery nie spełniają wymagań aplikacji, można utworzyć niestandardową implementację serwera. Przewodnik [Open Web Interface for .NET (OWIN)](xref:fundamentals/owin) pokazuje, jak napisać implementację opartą na <xref:Microsoft.AspNetCore.Hosting.Server.IServer> [nowin.](https://github.com/Bobris/Nowin) Tylko interfejsy funkcji, które używa aplikacji wymaga implementacji, choć co najmniej <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> i <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> muszą być obsługiwane.

## <a name="server-startup"></a>Uruchamianie serwera

Serwer jest uruchamiany po uruchomieniu aplikacji zintegrowanego środowiska programistycznego (IDE) lub edytora:

* [Profile](https://visualstudio.microsoft.com) &ndash; uruchamiania programu Visual Studio mogą służyć do uruchamiania aplikacji i serwera za pomocą [programu IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET core module](xref:host-and-deploy/aspnet-core-module) lub konsoli.
* [Visual Studio Kod](https://code.visualstudio.com/) &ndash; Aplikacja i serwer są uruchamiane przez [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), który aktywuje debuger CoreCLR.
* [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/) &ndash; Aplikacja i serwer są uruchamiane przez [debuger mono tryb](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/)miękki .

Podczas uruchamiania aplikacji z wiersza polecenia w folderze projektu, [dotnet uruchom](/dotnet/core/tools/dotnet-run) uruchamia aplikację i serwer (Tylko Kestrel i HTTP.sys). Konfiguracja jest określona przez `-c|--configuration` opcję, która `Debug` jest ustawiona na opcję (domyślna) lub `Release`.

Plik *launchSettings.json* zapewnia konfigurację podczas `dotnet run` uruchamiania aplikacji z debugerem lub z wbudowanym debugerem, takim jak Visual Studio. Jeśli profile uruchamiania są obecne w pliku *launchSettings.json,* użyj `--launch-profile {PROFILE NAME}` opcji z `dotnet run` poleceniem lub wybierz profil w programie Visual Studio. Aby uzyskać więcej informacji, zobacz [dotnet run](/dotnet/core/tools/dotnet-run) i [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).

## <a name="http2-support"></a>Obsługa protokołu HTTP/2

[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest obsługiwany przez ASP.NET Core w następujących scenariuszach wdrażania:

::: moniker range=">= aspnetcore-2.2"

* [Kestrel](xref:fundamentals/servers/kestrel#http2-support)
  * System operacyjny
    * Windows Server 2016/Windows 10 lub nowsze&dagger;
    * Linux z OpenSSL 1.0.2 lub nowszym (na przykład Ubuntu 16.04 lub nowszym)
    * Protokół HTTP/2 będzie obsługiwany w systemie macOS w przyszłej wersji.
  * Struktura docelowa: .NET Core 2.2 lub nowsza
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 lub nowsze
  * Struktura docelowa: nie dotyczy wdrożeń HTTP.sys.
* [IIS (w toku)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 lub nowsza; IIS 10 lub nowsze
  * Struktura docelowa: .NET Core 2.2 lub nowsza
* [IIS (poza procesem)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 lub nowsza; IIS 10 lub nowsze
  * Połączenia serwerów brzegowych wychodzących na publicznej stronie używają protokołu HTTP/2, ale odwrotne połączenie proxy z Kestrelem używa protokołu HTTP/1.1.
  * Struktura docelowa: nie dotyczy wdrożeń poza procesem usług IIS.

&dagger;Pustułka ma ograniczoną obsługę protokołu HTTP/2 w systemach Windows Server 2012 R2 i Windows 8.1. Obsługa jest ograniczona, ponieważ lista obsługiwanych pakietów szyfrowania TLS dostępnych w tych systemach operacyjnych jest ograniczona. Certyfikat wygenerowany przy użyciu algorytmu podpisu cyfrowego krzywej eliptycznej (ECDSA) może być wymagany do zabezpieczenia połączeń TLS.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 lub nowsze
  * Struktura docelowa: nie dotyczy wdrożeń HTTP.sys.
* [IIS (poza procesem)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 lub nowsza; IIS 10 lub nowsze
  * Połączenia serwerów brzegowych wychodzących na publicznej stronie używają protokołu HTTP/2, ale odwrotne połączenie proxy z Kestrelem używa protokołu HTTP/1.1.
  * Struktura docelowa: nie dotyczy wdrożeń poza procesem usług IIS.

::: moniker-end

Połączenie HTTP/2 musi używać [negocjacji protokołu warstwy aplikacji (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) i protokołu TLS 1.2 lub nowszego. Aby uzyskać więcej informacji, zobacz tematy dotyczące scenariuszy wdrażania serwera.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
