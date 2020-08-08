---
title: Platforma ASP.NET Core — podstawy
author: rick-anderson
description: Poznaj podstawowe koncepcje tworzenia aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/index
ms.openlocfilehash: f141e9248a702ad9a1d9737f82543a0ccc8fb573
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017210"
---
# <a name="aspnet-core-fundamentals"></a>Platforma ASP.NET Core — podstawy

::: moniker range=">= aspnetcore-3.0"

Ten artykuł zawiera omówienie najważniejszych tematów dotyczących sposobu tworzenia aplikacji ASP.NET Core.

## <a name="the-startup-class"></a>Klasa Startup

`Startup`Klasa jest:

* Usługi wymagane przez aplikację są skonfigurowane.
* Potok obsługi żądań aplikacji jest zdefiniowany jako seria części oprogramowania pośredniczącego.

Oto przykładowa `Startup` Klasa:

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Wstrzykiwanie zależności (usługi)

ASP.NET Core zawiera wbudowaną platformę wstrzykiwania zależności (DI), która udostępnia skonfigurowane usługi w aplikacji. Na przykład składnik rejestrowania to usługa.

Kod do konfigurowania (lub *rejestrowania*) usług jest dodawany do `Startup.ConfigureServices` metody. Przykład:

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

Usługi są zwykle rozwiązywane przez iniekcję konstruktora przy użyciu funkcji DI. Z iniekcją konstruktora Klasa deklaruje parametr konstruktora dla wymaganego typu lub interfejsu. Program DI Framework oferuje wystąpienie tej usługi w czasie wykonywania.

W poniższym przykładzie zastosowano iniekcję konstruktora do rozpoznania elementu `RazorPagesMovieContext` z typu di:

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

Jeśli wbudowana wersja kontenera kontroli (IoC) nie spełnia wszystkich potrzeb aplikacji, zamiast tego można użyć kontenera IoC innej firmy.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Oprogramowanie pośredniczące

Potok obsługi żądań składa się z serii komponentów oprogramowania pośredniczącego. Każdy składnik wykonuje operacje na `HttpContext` i wywołuje następne oprogramowanie pośredniczące w potoku lub kończy żądanie.

Zgodnie z Konwencją składnik pośredniczący jest dodawany do potoku przez wywołanie `Use...` metody rozszerzenia w `Startup.Configure` metodzie. Na przykład, aby włączyć renderowanie plików statycznych, wywołaj `UseStaticFiles` .

W poniższym przykładzie zostanie skonfigurowany potok obsługi żądania:

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

ASP.NET Core obejmuje bogaty zestaw wbudowanych programów pośredniczących. Można również pisać niestandardowe składniki pośredniczące.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index>.

## <a name="host"></a>Host

Podczas uruchamiania aplikacja ASP.NET Core kompiluje *hosta*. Host hermetyzuje wszystkie zasoby aplikacji, takie jak:

* Implementacja serwera HTTP
* Składniki oprogramowania pośredniczącego
* Rejestrowanie
* Usługi iniekcji zależności (DI)
* Konfiguracja

Istnieją dwa różne hosty: 

* Host ogólny .NET
* ASP.NET Core hosta sieci Web

Zalecany jest host ogólny platformy .NET. ASP.NET Core host sieci Web jest dostępny tylko w celu zapewnienia zgodności z poprzednimi wersjami.

Poniższy przykład tworzy hosta ogólnego platformy .NET:

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

`CreateDefaultBuilder`Metody i `ConfigureWebHostDefaults` konfigurują hosta z zestawem opcji domyślnych, takich jak:

* Użyj [Kestrel](#servers) jako serwera sieci Web i Włącz INTEGRACJĘ usług IIS.
* Załaduj konfigurację z *appsettings.jsna*, *appSettings. { Nazwa środowiska}. JSON*, zmienne środowiskowe, argumenty wiersza polecenia i inne źródła konfiguracji.
* Wyślij dane wyjściowe rejestrowania do konsoli programu i dostawców debugowania.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.

### <a name="non-web-scenarios"></a>Scenariusze inne niż internetowe

Host ogólny umożliwia innym typom aplikacji korzystanie z rozszerzeń struktury wycinania, takich jak rejestrowanie, iniekcja zależności (DI), konfiguracja i zarządzanie okresem istnienia aplikacji. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host> i <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Serwery

Aplikacja ASP.NET Core używa implementacji serwera HTTP do nasłuchiwania żądań HTTP. Serwer wyświetla żądania do aplikacji jako zestaw [funkcji żądania](xref:fundamentals/request-features) złożonych w `HttpContext` .

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core udostępnia następujące implementacje serwera:

* *Kestrel* to Międzyplatformowy serwer sieci Web. Kestrel jest często uruchamiana w odwrotnej konfiguracji serwera proxy za pomocą [usług IIS](https://www.iis.net/). W ASP.NET Core 2,0 lub nowszej Kestrel może być uruchamiany jako publiczny serwer graniczny uwidoczniony bezpośrednio w Internecie.
* *Serwer http IIS* jest serwerem dla systemu Windows, który korzysta z usług IIS. Na tym serwerze aplikacja ASP.NET Core i usługi IIS działają w tym samym procesie.
* *HTTP.sys* to serwer dla systemu Windows, który nie jest używany z usługami IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core udostępnia międzyplatformową implementację serwera *Kestrel* . W ASP.NET Core 2,0 lub nowszej Kestrel może działać jako publiczny serwer graniczny uwidoczniony bezpośrednio w Internecie. Kestrel jest często uruchamiana w odwrotnej konfiguracji serwera proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core udostępnia międzyplatformową implementację serwera *Kestrel* . W ASP.NET Core 2,0 lub nowszej Kestrel może działać jako publiczny serwer graniczny uwidoczniony bezpośrednio w Internecie. Kestrel jest często uruchamiana w odwrotnej konfiguracji serwera proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).

---

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Konfiguracja

ASP.NET Core udostępnia platformę konfiguracji, która pobiera ustawienia jako pary nazwa-wartość z uporządkowanego zestawu dostawców konfiguracji. Wbudowani dostawcy konfiguracji są dostępni dla różnych źródeł, takich jak pliki *. JSON* , pliki *. XML* , zmienne środowiskowe i argumenty wiersza polecenia. Napisz niestandardowych dostawców konfiguracji, aby obsługiwać inne źródła.

[Domyślnie](xref:fundamentals/configuration/index#default)aplikacje ASP.NET Core są skonfigurowane do odczytywania z *appsettings.jsna*, zmiennych środowiskowych, wiersza polecenia i nie tylko. Po załadowaniu konfiguracji aplikacji wartości z zmiennych środowiskowych zastępują wartości z *appsettings.jsna*.

Preferowanym sposobem odczytywania pokrewnych wartości konfiguracji jest użycie [wzorca opcji](xref:fundamentals/configuration/options). Aby uzyskać więcej informacji, zobacz [Powiązywanie hierarchicznych danych konfiguracyjnych przy użyciu wzorca opcji](xref:fundamentals/configuration/index#optpat).

Do zarządzania poufnymi danymi konfiguracyjnymi, takimi jak hasła, ASP.NET Core zapewnia [Menedżera wpisów tajnych](xref:security/app-secrets#secret-manager). W przypadku wpisów tajnych produkcji zalecamy [Azure Key Vault](xref:security/key-vault-configuration).

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.

## <a name="environments"></a>Środowiska

Środowiska wykonawcze, takie jak `Development` , `Staging` i `Production` , są pojęciem pierwszej klasy w ASP.NET Core. Określ środowisko, w którym aplikacja działa, przez ustawienie `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej. ASP.NET Core odczytuje tę zmienną środowiskową przy uruchamianiu aplikacji i zapisuje wartość w `IWebHostEnvironment` implementacji. Ta implementacja jest dostępna w dowolnym miejscu w aplikacji za pomocą iniekcji zależności (DI).

Poniższy przykład służy do konfigurowania aplikacji w celu zapewnienia szczegółowych informacji o błędzie w przypadku uruchamiania w `Development` środowisku:

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.

## <a name="logging"></a>Rejestrowanie

ASP.NET Core obsługuje interfejs API rejestrowania, który współpracuje z różnymi dostawcami rejestrowania wbudowanych i innych firm. Dostępni dostawcy obejmują:

* Konsola
* Debugowanie
* Śledzenie zdarzeń w systemie Windows
* Dziennik zdarzeń systemu Windows
* TraceSource
* Azure App Service
* Azure Application Insights

Aby utworzyć dzienniki, należy rozwiązać <xref:Microsoft.Extensions.Logging.ILogger%601> usługę od iniekcji zależności (di) i wywoływać metody rejestrowania, takie jak <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> . Przykład:

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

Metody rejestrowania, takie jak `LogInformation` Obsługa dowolnej liczby pól. Te pola są często używane do konstruowania komunikatu `string` , ale niektórzy dostawcy rejestrowania wysyłają je do magazynu danych jako osobne pola. Ta funkcja umożliwia dostawcom rejestrowania implementowanie [rejestrowania semantycznego, znanego również jako rejestrowanie strukturalne](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/logging/index>.

## <a name="routing"></a>Routing

*Trasa* jest WZORCEM adresu URL, który jest mapowany do procedury obsługi. Procedura obsługi jest zazwyczaj Razor stroną, metodą akcji w KONTROLERZE MVC lub w oprogramowaniu pośredniczącym. Routing ASP.NET Core zapewnia kontrolę nad adresami URL używanymi przez aplikację.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing>.

## <a name="error-handling"></a>Obsługa błędów

ASP.NET Core ma wbudowane funkcje do obsługi błędów, takie jak:

* Strona wyjątków dla deweloperów
* Niestandardowe strony błędów
* Statyczne strony kodów stanu
* Obsługa wyjątków uruchamiania

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Zgłaszanie żądań HTTP

Implementacja programu `IHttpClientFactory` jest dostępna do tworzenia `HttpClient` wystąpień. Fabryka:

* Zapewnia centralną lokalizację do nazywania i konfigurowania `HttpClient` wystąpień logicznych. Na przykład zarejestruj i Skonfiguruj klienta usługi *GitHub* do uzyskiwania dostępu do serwisu GitHub. Zarejestruj i skonfiguruj domyślnego klienta do innych celów.
* Obsługuje rejestrację i łańcuch wielu procedur delegowania, aby utworzyć potok pośredniczący żądania wychodzącego. Ten wzorzec jest podobny do potoku przychodzącego oprogramowania pośredniczącego ASP.NET Core. Wzorzec zapewnia mechanizm zarządzania założeniami krzyżowymi dla żądań HTTP, takich jak buforowanie, obsługa błędów, serializacja i rejestrowanie.
* Integruje się z usługą *Polly*, popularną biblioteką innej firmy na potrzeby obsługi błędów przejściowych.
* Zarządza buforowaniem i okresem istnienia podstawowych `HttpClientHandler` wystąpień, aby uniknąć typowych problemów z usługą DNS, które występują podczas `HttpClient` ręcznego zarządzania okresami istnienia.
* Dodaje konfigurowalne środowisko rejestrowania za pośrednictwem <xref:Microsoft.Extensions.Logging.ILogger> programu dla wszystkich żądań wysyłanych za pośrednictwem klientów utworzonych przez fabrykę.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Katalog główny zawartości

Katalog główny zawartości jest ścieżką podstawową dla:

* Plik wykonywalny obsługujący aplikację (*. exe*).
* Skompilowane zestawy wchodzące w skład aplikacji (*. dll*).
* Pliki zawartości używane przez aplikację, takie jak:
  * Razorpliki (*. cshtml*, *. Razor*)
  * Pliki konfiguracji (*. JSON*, *. XML*)
  * Pliki danych (*. DB*)
* [Katalog główny sieci Web](#web-root), zazwyczaj folder *wwwroot* .

Podczas opracowywania, główny element zawartości domyślnie jest katalogiem głównym projektu. Ten katalog jest również ścieżką podstawową dla plików zawartości aplikacji i [katalogu głównego sieci Web](#web-root). Określ inny katalog główny zawartości, ustawiając jego ścieżkę podczas [kompilowania hosta](#host). Aby uzyskać więcej informacji, zobacz [katalog główny zawartości](xref:fundamentals/host/generic-host#contentroot).

## <a name="web-root"></a>Katalog główny sieci Web

Katalog główny sieci Web jest ścieżką podstawową dla publicznych, statycznych plików zasobów, takich jak:

* Arkusze stylów (*. css*)
* JavaScript (*. js*)
* Obrazy (*. png*, *. jpg*)

Domyślnie pliki statyczne są obsługiwane tylko z katalogu głównego sieci Web i jego podkatalogów. Domyślna ścieżka katalogu głównego sieci Web to *{Content root}/wwwroot*. Określ inny katalog główny sieci Web przez ustawienie jego ścieżki podczas [kompilowania hosta](#host). Aby uzyskać więcej informacji, zobacz [katalog główny sieci Web](xref:fundamentals/host/generic-host#webroot).

Zapobiegaj publikowaniu plików w pliku *wwwroot* przy użyciu [ \<Content> elementu projektu](/visualstudio/msbuild/common-msbuild-project-items#content) w projekcie. Poniższy przykład uniemożliwia opublikowanie zawartości w katalogu *wwwroot/lokalnym* i jego podkatalogach:

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

W Razor plikach *. cshtml* , ukośnika ( `~/` ) wskazuje na katalog główny sieci Web. Ścieżka rozpoczynająca `~/` się od jest nazywana *ścieżką wirtualną*.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Ten artykuł zawiera omówienie najważniejszych tematów dotyczących sposobu tworzenia aplikacji ASP.NET Core.

## <a name="the-startup-class"></a>Klasa Startup

`Startup`Klasa jest:

* Usługi wymagane przez aplikację są skonfigurowane.
* Zdefiniowano potok obsługi żądań.

*Usługi* są składnikami, które są używane przez aplikację. Na przykład składnik rejestrowania to usługa. Kod do konfigurowania (lub *rejestrowania*) usług jest dodawany do `Startup.ConfigureServices` metody.

Potok obsługi żądań składa się z serii komponentów *oprogramowania pośredniczącego* . Na przykład oprogramowanie pośredniczące może obsługiwać żądania dotyczące plików statycznych lub przekierowywać żądania HTTP do protokołu HTTPS. Każde oprogramowanie pośredniczące wykonuje operacje asynchroniczne na serwerze `HttpContext` , a następnie wywołuje następne oprogramowanie pośredniczące w potoku lub kończy żądanie. Kod do konfigurowania potoku obsługi żądań został dodany do `Startup.Configure` metody.

Oto przykładowa `Startup` Klasa:

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Wstrzykiwanie zależności (usługi)

ASP.NET Core ma wbudowaną platformę wstrzykiwania zależności (DI), która udostępnia skonfigurowane usługi dla klas aplikacji. Jednym ze sposobów uzyskania wystąpienia usługi w klasie jest utworzenie konstruktora z parametrem wymaganego typu. Parametr może być typem usługi lub interfejsem. System DI zapewnia usługę w czasie wykonywania.

Oto Klasa, która używa funkcji DI do pobrania obiektu kontekstu Entity Framework Core. Wyróżniony wiersz jest przykładem iniekcji konstruktora:

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

Podczas gdy program jest wbudowany, został zaprojektowany z myślą o umożliwieniu podłączenia kontenera kontroli (IoC) innej firmy.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Oprogramowanie pośredniczące

Potok obsługi żądań składa się z serii komponentów oprogramowania pośredniczącego. Każdy składnik wykonuje operacje asynchroniczne na serwerze `HttpContext` , a następnie wywołuje następne oprogramowanie pośredniczące w potoku lub kończy żądanie.

Zgodnie z Konwencją składnik pośredniczący jest dodawany do potoku przez wywołanie jego `Use...` metody rozszerzenia w `Startup.Configure` metodzie. Na przykład, aby włączyć renderowanie plików statycznych, wywołaj `UseStaticFiles` .

Wyróżniony kod w poniższym przykładzie konfiguruje potok obsługi żądań:

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

ASP.NET Core zawiera rozbudowany zestaw wbudowanych programów pośredniczących i można napisać niestandardowe oprogramowanie pośredniczące.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index>.

## <a name="host"></a>Host

Aplikacja ASP.NET Core kompiluje *hosta* podczas uruchamiania. Host jest obiektem, który hermetyzuje wszystkie zasoby aplikacji, takie jak:

* Implementacja serwera HTTP
* Składniki oprogramowania pośredniczącego
* Rejestrowanie
* FOSFORAN
* Konfiguracja

Główną przyczyną uwzględnienia wszystkich zasobów zależnych od aplikacji w jednym obiekcie jest zarządzanie okresem istnienia: Kontrola uruchamiania aplikacji i bezpieczne zamykanie.

Dostępne są dwa hosty: host sieci Web i Host ogólny. W ASP.NET Core 2. x Host generyczny jest przeznaczony tylko dla scenariuszy innych niż sieci Web.

Kod służący do tworzenia hosta `Program.Main` :

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

`CreateDefaultBuilder`Metoda konfiguruje hosta z najczęściej używanymi opcjami, takimi jak:

* Użyj [Kestrel](#servers) jako serwera sieci Web i Włącz INTEGRACJĘ usług IIS.
* Załaduj konfigurację z *appsettings.jsna*, *appSettings. { Nazwa środowiska}. JSON*, zmienne środowiskowe, argumenty wiersza polecenia i inne źródła konfiguracji.
* Wyślij dane wyjściowe rejestrowania do konsoli programu i dostawców debugowania.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host>.

### <a name="non-web-scenarios"></a>Scenariusze inne niż internetowe

Host ogólny umożliwia innym typom aplikacji korzystanie z rozszerzeń struktury wycinania, takich jak rejestrowanie, iniekcja zależności (DI), konfiguracja i zarządzanie okresem istnienia aplikacji. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host> i <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Serwery

Aplikacja ASP.NET Core używa implementacji serwera HTTP do nasłuchiwania żądań HTTP. Serwer wyświetla żądania do aplikacji jako zestaw [funkcji żądania](xref:fundamentals/request-features) złożonych w `HttpContext` .

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core udostępnia następujące implementacje serwera:

* *Kestrel* to Międzyplatformowy serwer sieci Web. Kestrel jest często uruchamiana w odwrotnej konfiguracji serwera proxy za pomocą [usług IIS](https://www.iis.net/). Kestrel może być uruchamiany jako publiczny serwer graniczny uwidoczniony bezpośrednio w Internecie.
* *Serwer http IIS* jest serwerem dla systemu Windows, który korzysta z usług IIS. Na tym serwerze aplikacja ASP.NET Core i usługi IIS działają w tym samym procesie.
* *HTTP.sys* to serwer dla systemu Windows, który nie jest używany z usługami IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core udostępnia międzyplatformową implementację serwera *Kestrel* . Kestrel może być uruchamiany jako publiczny serwer graniczny uwidoczniony bezpośrednio w Internecie. Kestrel jest często uruchamiana w odwrotnej konfiguracji serwera proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core udostępnia międzyplatformową implementację serwera *Kestrel* . Kestrel może być uruchamiany jako publiczny serwer graniczny uwidoczniony bezpośrednio w Internecie. Kestrel jest często uruchamiana w odwrotnej konfiguracji serwera proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core udostępnia następujące implementacje serwera:

* *Kestrel* to Międzyplatformowy serwer sieci Web. Kestrel jest często uruchamiana w odwrotnej konfiguracji serwera proxy za pomocą [usług IIS](https://www.iis.net/). Kestrel może być uruchamiany jako publiczny serwer graniczny uwidoczniony bezpośrednio w Internecie.
* *HTTP.sys* to serwer dla systemu Windows, który nie jest używany z usługami IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core udostępnia międzyplatformową implementację serwera *Kestrel* . Kestrel może być uruchamiany jako publiczny serwer graniczny uwidoczniony bezpośrednio w Internecie. Kestrel jest często uruchamiana w odwrotnej konfiguracji serwera proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core udostępnia międzyplatformową implementację serwera *Kestrel* . Kestrel może być uruchamiany jako publiczny serwer graniczny uwidoczniony bezpośrednio w Internecie. Kestrel jest często uruchamiana w odwrotnej konfiguracji serwera proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Konfiguracja

ASP.NET Core udostępnia platformę konfiguracji, która pobiera ustawienia jako pary nazwa-wartość z uporządkowanego zestawu dostawców konfiguracji. Istnieją Wbudowani dostawcy konfiguracji dla różnych źródeł, takich jak pliki *. JSON* , pliki *. XML* , zmienne środowiskowe i argumenty wiersza polecenia. Możesz również napisać niestandardowych dostawców konfiguracji.

Można na przykład określić, że konfiguracja pochodzi z *appsettings.js* i zmiennych środowiskowych. Następnie po zażądaniu wartości parametru *ConnectionString* struktura najpierw będzie wyglądać w *appsettings.js* pliku. Jeśli wartość jest tam znaleziona, ale również w zmiennej środowiskowej, pierwszeństwo ma wartość ze zmiennej środowiskowej.

Aby zarządzać poufnymi danymi konfiguracyjnymi, takimi jak hasła, ASP.NET Core zapewnia [Narzędzie tajnego Menedżera](xref:security/app-secrets). W przypadku wpisów tajnych produkcji zalecamy [Azure Key Vault](xref:security/key-vault-configuration).

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.

## <a name="options"></a>Opcje

Jeśli to możliwe, ASP.NET Core są zgodne ze *wzorcem opcji* przechowywania i pobierania wartości konfiguracyjnych. Wzorzec opcji używa klas do reprezentowania grup powiązanych ustawień.

Na przykład poniższy kod ustawia opcje obiektów WebSockets:

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/options>.

## <a name="environments"></a>Środowiska

Środowiska wykonawcze, takie jak *programowanie*, *przemieszczanie*i *produkcja*, są pierwszą klasą koncepcji w ASP.NET Core. Aby określić środowisko, w którym działa aplikacja, należy ustawić `ASPNETCORE_ENVIRONMENT` zmienną środowiskową. ASP.NET Core odczytuje tę zmienną środowiskową przy uruchamianiu aplikacji i zapisuje wartość w `IHostingEnvironment` implementacji. Obiekt środowiska jest dostępny w dowolnym miejscu w aplikacji za pomocą funkcji DI.

Następujący przykładowy kod z klasy służy `Startup` do konfigurowania aplikacji w celu zapewnienia szczegółowych informacji o błędzie tylko wtedy, gdy jest ona uruchamiana w programie Development:

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.

## <a name="logging"></a>Rejestrowanie

ASP.NET Core obsługuje interfejs API rejestrowania, który współpracuje z różnymi dostawcami rejestrowania wbudowanych i innych firm. Dostępne są następujące dostawcy:

* Konsola
* Debugowanie
* Śledzenie zdarzeń w systemie Windows
* Dziennik zdarzeń systemu Windows
* TraceSource
* Azure App Service
* Azure Application Insights

Zapisuj dzienniki z dowolnego miejsca w kodzie aplikacji, pobierając `ILogger` obiekt z metod rejestrowania i wywoływania.

Oto przykładowy kod, który używa `ILogger` obiektu, z iniekcją konstruktora i wyróżniania wywołań metody rejestrowania.

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

`ILogger`Interfejs umożliwia przekazanie dowolnej liczby pól dostawcy rejestrowania. Pola są często używane do konstruowania ciągu komunikatu, ale dostawcy mogą również wysyłać je jako oddzielne pola do magazynu danych. Ta funkcja umożliwia dostawcom rejestrowania implementowanie [rejestrowania semantycznego, znanego również jako rejestrowanie strukturalne](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/logging/index>.

## <a name="routing"></a>Routing

*Trasa* jest WZORCEM adresu URL, który jest mapowany do procedury obsługi. Procedura obsługi jest zazwyczaj Razor stroną, metodą akcji w KONTROLERZE MVC lub w oprogramowaniu pośredniczącym. Routing ASP.NET Core zapewnia kontrolę nad adresami URL używanymi przez aplikację.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing>.

## <a name="error-handling"></a>Obsługa błędów

ASP.NET Core ma wbudowane funkcje do obsługi błędów, takie jak:

* Strona wyjątków dla deweloperów
* Niestandardowe strony błędów
* Statyczne strony kodów stanu
* Obsługa wyjątków uruchamiania

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Zgłaszanie żądań HTTP

Implementacja programu `IHttpClientFactory` jest dostępna do tworzenia `HttpClient` wystąpień. Fabryka:

* Zapewnia centralną lokalizację do nazywania i konfigurowania `HttpClient` wystąpień logicznych. Na przykład klient usługi *GitHub* można zarejestrować i skonfigurować do uzyskiwania dostępu do usługi GitHub. Domyślny klient można zarejestrować do innych celów.
* Obsługuje rejestrację i łańcuch wielu procedur delegowania, aby utworzyć potok pośredniczący żądania wychodzącego. Ten wzorzec jest podobny do przychodzącego potoku oprogramowania pośredniczącego w ASP.NET Core. Wzorzec zapewnia mechanizm zarządzania problemami z wycinaniem między żądaniami HTTP, takimi jak buforowanie, obsługa błędów, serializacja i rejestrowanie.
* Integruje się z usługą *Polly*, popularną biblioteką innej firmy na potrzeby obsługi błędów przejściowych.
* Zarządza buforowaniem i okresem istnienia podstawowych `HttpClientHandler` wystąpień, aby uniknąć typowych problemów z usługą DNS występujących podczas ręcznego zarządzania `HttpClient` okresami istnienia.
* Dodaje konfigurowalne środowisko rejestrowania (za pośrednictwem programu `ILogger` ) dla wszystkich żądań wysyłanych przez klientów utworzonych przez fabrykę.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Katalog główny zawartości

Katalog główny zawartości jest ścieżką podstawową do:

* Plik wykonywalny obsługujący aplikację (*. exe*).
* Skompilowane zestawy wchodzące w skład aplikacji (*. dll*).
* Pliki zawartości inne niż kod używane przez aplikację, takie jak:
  * Razorpliki (*. cshtml*, *. Razor*)
  * Pliki konfiguracji (*. JSON*, *. XML*)
  * Pliki danych (*. DB*)
* [Katalog główny sieci Web](#web-root), zazwyczaj opublikowany folder *wwwroot* .

Podczas tworzenia:

* Katalog główny zawartości domyślnie jest katalogiem głównym projektu.
* Katalog główny projektu służy do tworzenia:
  * Ścieżka do plików zawartości nienależących do kodu aplikacji w katalogu głównym projektu.
  * [Katalog główny sieci Web](#web-root), zazwyczaj folder *wwwroot* w katalogu głównym projektu.

Alternatywna ścieżka katalogu głównego zawartości może być określona podczas [kompilowania hosta](#host). Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#content-root>.

## <a name="web-root"></a>Katalog główny sieci Web

Katalog główny sieci Web jest ścieżką podstawową do publicznych, niekodowych, statycznych plików zasobów, takich jak:

* Arkusze stylów (*. css*)
* JavaScript (*. js*)
* Obrazy (*. png*, *. jpg*)

Pliki statyczne są obsługiwane domyślnie tylko z katalogu głównego (i katalogów podrzędnych) sieci Web.

Ścieżka katalogu głównego sieci Web jest domyślnie ustawiona na *{Content root}/wwwroot*, ale podczas [kompilowania hosta](#host)można określić inny katalog internetowy w sieci Web. Aby uzyskać więcej informacji, zobacz [katalog główny sieci Web](xref:fundamentals/host/web-host#web-root).

Zapobiegaj publikowaniu plików w pliku *wwwroot* przy użyciu [ \<Content> elementu projektu](/visualstudio/msbuild/common-msbuild-project-items#content) w projekcie. Poniższy przykład uniemożliwia opublikowanie zawartości w katalogu *wwwroot/lokalnym* i podkatalogach:

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

W Razor plikach (*. cshtml*), ukośnik ( `~/` ) wskazuje na katalog główny sieci Web. Ścieżka rozpoczynająca `~/` się od jest nazywana *ścieżką wirtualną*.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.

::: moniker-end
