---
title: Platforma ASP.NET Core — podstawy
author: rick-anderson
description: Poznaj podstawowe pojęcia dotyczące tworzenia aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
uid: fundamentals/index
ms.openlocfilehash: da2b42a7cf5d116a36d1dd9fa586d40ab31fc52d
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417642"
---
# <a name="aspnet-core-fundamentals"></a>Platforma ASP.NET Core — podstawy

::: moniker range=">= aspnetcore-3.0"

Ten artykuł zawiera omówienie kluczowych tematów do zrozumienia, jak tworzyć ASP.NET aplikacje Core.

## <a name="the-startup-class"></a>Klasa Startup

Klasa `Startup` jest, gdzie:

* Usługi wymagane przez aplikację są skonfigurowane.
* Potok obsługi żądań aplikacji jest zdefiniowany jako seria składników oprogramowania pośredniczącego.

Oto przykładowa `Startup` klasa:

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Wstrzykiwanie zależności (usługi)

ASP.NET Core zawiera wbudowaną strukturę iniekcji zależności (DI), która udostępnia skonfigurowane usługi w całej aplikacji. Na przykład składnik rejestrowania jest usługą.

Kod do konfigurowania (lub *rejestracji)* `Startup.ConfigureServices` usług jest dodawany do metody. Przykład:

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

Usługi są zazwyczaj rozpoznawane z DI przy użyciu iniekcji konstruktora. Wtrysku konstruktora klasy deklaruje parametr konstruktora wymaganego typu lub interfejsu. Struktura DI zapewnia wystąpienie tej usługi w czasie wykonywania.

W poniższym przykładzie użyto `RazorPagesMovieContext` iniekcji konstruktora w celu rozwiązania a z DI:

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

Jeśli wbudowany kontener inwersji kontroli (IoC) nie spełnia wszystkich potrzeb aplikacji, zamiast tego można użyć kontenera IoC innej firmy.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Oprogramowanie pośredniczące

Potok obsługi żądań składa się jako seria składników oprogramowania pośredniczącego. Każdy składnik wykonuje operacje `HttpContext` na i albo wywołuje następne oprogramowanie pośredniczące w potoku lub kończy żądanie.

Zgodnie z konwencją składnik oprogramowania pośredniczącego `Use...` jest dodawany `Startup.Configure` do potoku przez wywołanie metody rozszerzenia w metodzie. Na przykład, aby włączyć renderowanie plików `UseStaticFiles`statycznych, wywołaj .

Poniższy przykład konfiguruje potok obsługi żądań:

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

ASP.NET Core zawiera bogaty zestaw wbudowanego oprogramowania pośredniczącego. Można również zapisywać niestandardowe składniki oprogramowania pośredniczącego.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index>.

## <a name="host"></a>Host

Podczas uruchamiania aplikacja ASP.NET Core tworzy *hosta.* Host hermetyzuje wszystkie zasoby aplikacji, takie jak:

* Implementacja serwera HTTP
* Składniki oprogramowania pośredniczącego
* Rejestrowanie
* Usługi iniekcji zależności (DI)
* Konfigurowanie

Istnieją dwa różne hosty: 

* Host ogólny platformy .NET
* ASP.NET Core Web Host

Zaleca się host ogólny platformy .NET. ASP.NET Core Web Host jest dostępny tylko dla zgodności z przeszywnie.

Poniższy przykład tworzy hosta ogólnego platformy .NET:

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

Metody `CreateDefaultBuilder` `ConfigureWebHostDefaults` i metody konfigurują hosta z zestawem opcji domyślnych, takich jak:

* Użyj [Kestrel](#servers) jako serwera sieci web i włącz integrację usług IIS.
* Załaduj konfigurację z *pliku appsettings.json*, *appsettings.{ Nazwa środowiska}.json*, zmienne środowiskowe, argumenty wiersza polecenia i inne źródła konfiguracji.
* Wyślij dane wyjściowe rejestrowania do dostawców konsoli i debugowania.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.

### <a name="non-web-scenarios"></a>Scenariusze inne niż internetowe

Host ogólny umożliwia innym typom aplikacji używanie rozszerzeń struktury przekrojowych, takich jak rejestrowanie, iniekcja zależności (DI), konfiguracja i zarządzanie okresem istnienia aplikacji. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host> i <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Serwery

Aplikacja ASP.NET Core używa implementacji serwera HTTP do nasłuchiwać żądań HTTP. Serwer powierzchnie żądań do aplikacji jako zestaw funkcji `HttpContext` [żądania](xref:fundamentals/request-features) składa się w .

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core udostępnia następujące implementacje serwera:

* *Pustułka* to wieloplatformowy serwer www. Pustułka jest często uruchamiana w odwrotnej konfiguracji serwera proxy przy użyciu [usług IIS](https://www.iis.net/). W ASP.NET Core 2.0 lub nowszym Kestrel może być uruchamiany jako publiczny serwer brzegowy udostępniane bezpośrednio do Internetu.
* *Serwer HTTP usług IIS* to serwer dla systemu Windows, który korzysta z usług IIS. Z tego serwera ASP.NET Core aplikacji i usług IIS uruchomić w tym samym procesie.
* *HTTP.sys* to serwer dla systemu Windows, który nie jest używany z usługami IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core zapewnia implementację serwera międzyplatformowego *Kestrel.* W ASP.NET Core 2.0 lub nowszym Kestrel może działać jako publiczny serwer brzegowy udostępniane bezpośrednio do Internetu. Pustułka jest często uruchamiana w odwrotnej konfiguracji proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core zapewnia implementację serwera międzyplatformowego *Kestrel.* W ASP.NET Core 2.0 lub nowszym Kestrel może działać jako publiczny serwer brzegowy udostępniane bezpośrednio do Internetu. Pustułka jest często uruchamiana w odwrotnej konfiguracji proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).

---

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Konfigurowanie

ASP.NET Core zapewnia strukturę konfiguracji, która pobiera ustawienia jako pary nazwa-wartość z uporządkowanego zestawu dostawców konfiguracji. Wbudowane dostawcy konfiguracji są dostępni dla różnych źródeł, takich jak pliki *json,* pliki *xml,* zmienne środowiskowe i argumenty wiersza polecenia. Napisz dostawców konfiguracji niestandardowych do obsługi innych źródeł.

[Domyślnie](xref:fundamentals/configuration/index#default)aplikacje ASP.NET Core są skonfigurowane do odczytu z *pliku appsettings.json*, zmiennych środowiskowych, wiersza polecenia i innych. Po załadowaniu konfiguracji aplikacji wartości ze zmiennych środowiskowych zastępują wartości z *pliku appsettings.json*.

Preferowanym sposobem odczytywania powiązanych wartości konfiguracyjnych jest użycie [wzorca opcji](xref:fundamentals/configuration/options). Aby uzyskać więcej informacji, zobacz [Powiązanie danych konfiguracji hierarchicznej przy użyciu wzorca opcji](xref:fundamentals/configuration/index#optpat).

Do zarządzania poufnymi danymi konfiguracyjnymi, takimi jak hasła, ASP.NET Core udostępnia [Tajnego Menedżera](xref:security/app-secrets#secret-manager). W przypadku wpisów tajnych produkcji zalecamy [usługę Azure Key Vault](xref:security/key-vault-configuration).

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.

## <a name="environments"></a>Środowiska

Środowiska wykonywania, takie `Development` `Staging`jak `Production`, i , są pojęciem pierwszej klasy w ASP.NET Core. Określ środowisko, w które działa `ASPNETCORE_ENVIRONMENT` aplikacja, ustawiając zmienną środowiskową. ASP.NET Core odczytuje tę zmienną środowiskową `IWebHostEnvironment` podczas uruchamiania aplikacji i przechowuje wartość w implementacji. Ta implementacja jest dostępna w dowolnym miejscu w aplikacji za pośrednictwem iniekcji zależności (DI).

Poniższy przykład konfiguruje aplikację, aby zapewnić `Development` szczegółowe informacje o błędzie podczas uruchamiania w środowisku:

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.

## <a name="logging"></a>Rejestrowanie

ASP.NET Core obsługuje interfejs API rejestrowania, który współpracuje z różnymi wbudowanymi i zewnętrznymi dostawcami rejestrowania. Do dostępnych dostawców należą:

* Konsola
* Debugowanie
* Śledzenie zdarzeń w systemie Windows
* Dziennik zdarzeń systemu Windows
* TraceSource
* Azure App Service
* Azure Application Insights

Aby utworzyć dzienniki, <xref:Microsoft.Extensions.Logging.ILogger%601> rozwiąż problem usługi z iniekcji <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>zależności (DI) i wywołania metod rejestrowania, takich jak . Przykład:

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

Metody rejestrowania, `LogInformation` takie jak obsługa dowolnej liczby pól. Te pola są często używane `string`do konstruowania wiadomości, ale niektórzy dostawcy rejestrowania wysyłają je do magazynu danych jako oddzielne pola. Ta funkcja umożliwia dostawcom rejestrowania zaimplementowanie [rejestrowania semantycznego, znanego również jako rejestrowanie strukturalne.](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/logging/index>.

## <a name="routing"></a>Routing

*Trasa* jest wzorcem adresu URL, który jest mapowany do programu obsługi. Program obsługi jest zazwyczaj razor strony, metody akcji w kontrolerze MVC lub oprogramowania pośredniczącego. ASP.NET Routing podstawowy daje ci kontrolę nad adresami URL używanymi przez aplikację.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing>.

## <a name="error-handling"></a>Obsługa błędów

ASP.NET Core ma wbudowane funkcje obsługi błędów, takie jak:

* Strona wyjątku dla deweloperów
* Niestandardowe strony błędów
* Statyczne strony kodowe stanu
* Obsługa wyjątków uruchamiania

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Zgłaszanie żądań HTTP

Implementacja `IHttpClientFactory` jest dostępna do `HttpClient` tworzenia wystąpień. Fabryka:

* Zapewnia centralną lokalizację nazewnictwa `HttpClient` i konfigurowania wystąpień logicznych. Na przykład zarejestruj i skonfiguruj klienta *github,* aby uzyskać dostęp do usługi GitHub. Zarejestruj i skonfiguruj klienta domyślnego do innych celów.
* Obsługuje rejestrację i tworzenie łańcucha wielu programów obsługi delegujących do tworzenia potoku oprogramowania pośredniczącego żądania wychodzącego. Ten wzorzec jest podobny do potoku przychodzącego oprogramowania pośredniczącego ASP.NET Core. Wzorzec zapewnia mechanizm zarządzania przekrojowe problemy dla żądań HTTP, w tym buforowania, obsługi błędów, serializacji i rejestrowania.
* Integruje się z *Polly*, popularną biblioteką innych firm do obsługi błędów przejściowych.
* Zarządza buforowanie i okres `HttpClientHandler` istnienia wystąpienia podstawowe, aby uniknąć `HttpClient` typowych problemów z systemem DNS, które występują podczas ręcznego zarządzania okresami istnienia.
* Dodaje konfigurowalne środowisko <xref:Microsoft.Extensions.Logging.ILogger> rejestrowania za pośrednictwem dla wszystkich żądań wysyłanych za pośrednictwem klientów utworzonych przez fabrykę.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Katalog główny zawartości

Katalog główny zawartości jest podstawową ścieżką dla:

* Plik wykonywalny hosting aplikacji (*.exe*).
* Skompilowane zestawy, które tworzą aplikację (*.dll*).
* Pliki zawartości używane przez aplikację, takie jak:
  * Pliki razor (*.cshtml*, *.brzytwa*)
  * Pliki konfiguracyjne (*.json*, *.xml*)
  * Pliki danych (*.db*)
* [Katalog główny sieci Web](#web-root), zazwyczaj folder *wwwroot.*

Podczas tworzenia katalogu głównego zawartości domyślnie katalogu głównego projektu. Ten katalog jest również podstawową ścieżką zarówno dla plików zawartości aplikacji, jak i [katalogu głównego sieci Web.](#web-root) Określ inny katalog główny zawartości, ustawiając jego ścieżkę podczas [tworzenia hosta](#host). Aby uzyskać więcej informacji, zobacz [Katalog główny zawartości](xref:fundamentals/host/generic-host#contentrootpath-1).

## <a name="web-root"></a>Katalog główny sieci Web

Katalog główny sieci Web jest ścieżką bazową dla publicznych, statycznych plików zasobów, takich jak:

* Arkusze stylów (*.css*)
* JavaScript (*.js*)
* Zdjęcia (*.png*, *.jpg*)

Domyślnie pliki statyczne są obsługiwane tylko z katalogu głównego sieci Web i jego podkatastów. Ścieżka główna sieci Web domyślnie *{zawartość root}/wwwroot*. Określ inny katalog główny sieci Web, ustawiając jego ścieżkę podczas [tworzenia hosta](#host). Aby uzyskać więcej informacji, zobacz [Katalog główny sieci Web](xref:fundamentals/host/generic-host#webroot-1).

Zapobiegaj publikowaniu plików w [ \<](/visualstudio/msbuild/common-msbuild-project-items#content) *witrynie wwwroot* za pomocą elementu projektu> zawartości w pliku projektu. Poniższy przykład zapobiega publikowaniu zawartości w *wwwroot/local* i jego podkatastronach:

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

W plikach Razor *.cshtml,* `~/`tylda-ukośnik ( ) wskazuje na katalog główny sieci Web. Ścieżka rozpoczynająca `~/` się od ścieżki jest określana jako *ścieżka wirtualna*.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Ten artykuł zawiera omówienie kluczowych tematów do zrozumienia, jak tworzyć ASP.NET aplikacje Core.

## <a name="the-startup-class"></a>Klasa Startup

Klasa `Startup` jest, gdzie:

* Usługi wymagane przez aplikację są skonfigurowane.
* Potok obsługi żądań jest zdefiniowany.

*Usługi* są składnikami, które są używane przez aplikację. Na przykład składnik rejestrowania jest usługą. Kod do konfigurowania (lub *rejestracji)* `Startup.ConfigureServices` usług jest dodawany do metody.

Potok obsługi żądań składa się jako seria składników *oprogramowania pośredniczącego.* Na przykład oprogramowanie pośredniczące może obsługiwać żądania plików statycznych lub przekierowywać żądania HTTP do protokołu HTTPS. Każde oprogramowanie pośredniczące wykonuje operacje asynchroniczne na a `HttpContext` następnie wywołuje następne oprogramowanie pośredniczące w potoku lub kończy żądanie. Kod, aby skonfigurować potok obsługi `Startup.Configure` żądań jest dodawany do metody.

Oto przykładowa `Startup` klasa:

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Wstrzykiwanie zależności (usługi)

ASP.NET Core ma wbudowaną strukturę iniekcji zależności (DI), która udostępnia skonfigurowane usługi klasom aplikacji. Jednym ze sposobów uzyskania wystąpienia usługi w klasie jest utworzenie konstruktora z parametrem wymaganego typu. Parametrem może być typ usługi lub interfejs. System DI zapewnia usługę w czasie wykonywania.

Oto klasa, która używa DI, aby uzyskać obiekt kontekstu Entity Framework Core. Podświetlona linia jest przykładem iniekcji konstruktora:

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

Gdy di jest wbudowany, został zaprojektowany, aby umożliwić podłączenie kontenera inwersji sterowania (IoC) innej firmy, jeśli wolisz.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Oprogramowanie pośredniczące

Potok obsługi żądań składa się jako seria składników oprogramowania pośredniczącego. Każdy składnik wykonuje operacje asynchroniczne na a `HttpContext` następnie wywołuje następne oprogramowanie pośredniczące w potoku lub kończy żądanie.

Zgodnie z konwencją składnik oprogramowania pośredniczącego `Use...` jest dodawany `Startup.Configure` do potoku przez wywołanie jego metody rozszerzenia w metodzie. Na przykład, aby włączyć renderowanie plików `UseStaticFiles`statycznych, wywołaj .

Wyróżniony kod w poniższym przykładzie konfiguruje potok obsługi żądań:

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

ASP.NET Core zawiera bogaty zestaw wbudowanego oprogramowania pośredniczącego i można pisać niestandardowe oprogramowanie pośredniczące.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index>.

## <a name="host"></a>Host

Aplikacja ASP.NET Core tworzy *hosta* podczas uruchamiania. Host jest obiektem, który hermetyzuje wszystkie zasoby aplikacji, takie jak:

* Implementacja serwera HTTP
* Składniki oprogramowania pośredniczącego
* Rejestrowanie
* Di
* Konfigurowanie

Głównym powodem włączenia wszystkich współzależnych zasobów aplikacji w jednym obiekcie jest zarządzanie okresem istnienia: kontrola nad uruchamianiem aplikacji i wdzięczne zamykanie.

Dostępne są dwa hosty: Host internetowy i Host ogólny. W ASP.NET Core 2.x host ogólny jest przeznaczony tylko dla scenariuszy innych niż web.

Kod do utworzenia hosta znajduje się w `Program.Main`:

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

Metoda `CreateDefaultBuilder` konfiguruje hosta z powszechnie używanymi opcjami, takimi jak:

* Użyj [Kestrel](#servers) jako serwera sieci web i włącz integrację usług IIS.
* Załaduj konfigurację z *pliku appsettings.json*, *appsettings.{ Nazwa środowiska}.json*, zmienne środowiskowe, argumenty wiersza polecenia i inne źródła konfiguracji.
* Wyślij dane wyjściowe rejestrowania do dostawców konsoli i debugowania.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host>.

### <a name="non-web-scenarios"></a>Scenariusze inne niż internetowe

Host ogólny umożliwia innym typom aplikacji używanie rozszerzeń struktury przekrojowych, takich jak rejestrowanie, iniekcja zależności (DI), konfiguracja i zarządzanie okresem istnienia aplikacji. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host> i <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Serwery

Aplikacja ASP.NET Core używa implementacji serwera HTTP do nasłuchiwać żądań HTTP. Serwer powierzchnie żądań do aplikacji jako zestaw funkcji `HttpContext` [żądania](xref:fundamentals/request-features) składa się w .

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core udostępnia następujące implementacje serwera:

* *Pustułka* to wieloplatformowy serwer www. Pustułka jest często uruchamiana w odwrotnej konfiguracji serwera proxy przy użyciu [usług IIS](https://www.iis.net/). Pustułka może być uruchamiana jako publiczny serwer brzegowy narażony bezpośrednio do Internetu.
* *Serwer HTTP usług IIS* to serwer dla systemu Windows, który korzysta z usług IIS. Z tego serwera ASP.NET Core aplikacji i usług IIS uruchomić w tym samym procesie.
* *HTTP.sys* to serwer dla systemu Windows, który nie jest używany z usługami IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core zapewnia implementację serwera międzyplatformowego *Kestrel.* Pustułka może być uruchamiana jako publiczny serwer brzegowy narażony bezpośrednio do Internetu. Pustułka jest często uruchamiana w odwrotnej konfiguracji proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core zapewnia implementację serwera międzyplatformowego *Kestrel.* Pustułka może być uruchamiana jako publiczny serwer brzegowy narażony bezpośrednio do Internetu. Pustułka jest często uruchamiana w odwrotnej konfiguracji proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core udostępnia następujące implementacje serwera:

* *Pustułka* to wieloplatformowy serwer www. Pustułka jest często uruchamiana w odwrotnej konfiguracji serwera proxy przy użyciu [usług IIS](https://www.iis.net/). Pustułka może być uruchamiana jako publiczny serwer brzegowy narażony bezpośrednio do Internetu.
* *HTTP.sys* to serwer dla systemu Windows, który nie jest używany z usługami IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core zapewnia implementację serwera międzyplatformowego *Kestrel.* Pustułka może być uruchamiana jako publiczny serwer brzegowy narażony bezpośrednio do Internetu. Pustułka jest często uruchamiana w odwrotnej konfiguracji proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core zapewnia implementację serwera międzyplatformowego *Kestrel.* Pustułka może być uruchamiana jako publiczny serwer brzegowy narażony bezpośrednio do Internetu. Pustułka jest często uruchamiana w odwrotnej konfiguracji proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Konfigurowanie

ASP.NET Core zapewnia strukturę konfiguracji, która pobiera ustawienia jako pary nazwa-wartość z uporządkowanego zestawu dostawców konfiguracji. Istnieją wbudowane dostawcy konfiguracji dla różnych źródeł, takich jak pliki *json,* pliki *xml,* zmienne środowiskowe i argumenty wiersza polecenia. Można również napisać dostawców konfiguracji niestandardowych.

Na przykład można określić, że konfiguracja pochodzi z *appsettings.json* i zmiennych środowiskowych. Następnie, gdy żądana jest wartość *ConnectionString,* struktura wygląda najpierw w pliku *appsettings.json.* Jeśli wartość zostanie tam znaleziona, ale również w zmiennej środowiskowej, wartość ze zmiennej środowiskowej będzie miała pierwszeństwo.

Do zarządzania poufnymi danymi konfiguracyjnymi, takimi jak hasła, ASP.NET Core udostępnia [narzędzie Secret Manager](xref:security/app-secrets). W przypadku wpisów tajnych produkcji zalecamy [usługę Azure Key Vault](xref:security/key-vault-configuration).

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.

## <a name="options"></a>Opcje

Jeśli to możliwe, ASP.NET Core następuje *wzorzec opcji* do przechowywania i pobierania wartości konfiguracji. Wzorzec opcji używa klas do reprezentowania grup powiązanych ustawień.

Na przykład następujący kod ustawia opcje WebSockets:

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/options>.

## <a name="environments"></a>Środowiska

Środowiska wykonywania, takie jak *Development,* *Staging*i *Production*, są pierwszorzędnym pojęciem w ASP.NET Core. Można określić środowisko, w które działa `ASPNETCORE_ENVIRONMENT` aplikacja, ustawiając zmienną środowiskową. ASP.NET Core odczytuje tę zmienną środowiskową `IHostingEnvironment` podczas uruchamiania aplikacji i przechowuje wartość w implementacji. Obiekt środowiska jest dostępny w dowolnym miejscu w aplikacji za pośrednictwem DI.

Poniższy przykładowy `Startup` kod z klasy konfiguruje aplikację, aby zapewnić szczegółowe informacje o błędzie tylko wtedy, gdy jest uruchamiana w rozwoju:

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.

## <a name="logging"></a>Rejestrowanie

ASP.NET Core obsługuje interfejs API rejestrowania, który współpracuje z różnymi wbudowanymi i zewnętrznymi dostawcami rejestrowania. Dostępni dostawcy są następujący:

* Konsola
* Debugowanie
* Śledzenie zdarzeń w systemie Windows
* Dziennik zdarzeń systemu Windows
* TraceSource
* Azure App Service
* Azure Application Insights

Zapisuj dzienniki z dowolnego miejsca w `ILogger` kodzie aplikacji, uzyskując obiekt z DI i wywołując metody dziennika.

Oto przykładowy kod, który `ILogger` używa obiektu, z iniekcji konstruktora i wywołanie metody rejestrowania wyróżnione.

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

Interfejs `ILogger` umożliwia przekazywanie dowolnej liczby pól do dostawcy rejestrowania. Pola są często używane do konstruowania ciągu wiadomości, ale dostawca może również wysłać je jako oddzielne pola do magazynu danych. Ta funkcja umożliwia dostawcom rejestrowania zaimplementowanie [rejestrowania semantycznego, znanego również jako rejestrowanie strukturalne.](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/logging/index>.

## <a name="routing"></a>Routing

*Trasa* jest wzorcem adresu URL, który jest mapowany do programu obsługi. Program obsługi jest zazwyczaj razor strony, metody akcji w kontrolerze MVC lub oprogramowania pośredniczącego. ASP.NET Routing podstawowy daje ci kontrolę nad adresami URL używanymi przez aplikację.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing>.

## <a name="error-handling"></a>Obsługa błędów

ASP.NET Core ma wbudowane funkcje obsługi błędów, takie jak:

* Strona wyjątku dla deweloperów
* Niestandardowe strony błędów
* Statyczne strony kodowe stanu
* Obsługa wyjątków uruchamiania

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Zgłaszanie żądań HTTP

Implementacja `IHttpClientFactory` jest dostępna do `HttpClient` tworzenia wystąpień. Fabryka:

* Zapewnia centralną lokalizację nazewnictwa `HttpClient` i konfigurowania wystąpień logicznych. Na przykład klient *github* może być zarejestrowany i skonfigurowany do uzyskiwania dostępu do usługi GitHub. Domyślny klient może być zarejestrowany do innych celów.
* Obsługuje rejestrację i tworzenie łańcucha wielu programów obsługi delegujących do tworzenia potoku oprogramowania pośredniczącego żądania wychodzącego. Ten wzorzec jest podobny do potoku przychodzącego oprogramowania pośredniczącego w ASP.NET Core. Wzorzec zapewnia mechanizm zarządzania przekrojowe problemy wokół żądań HTTP, w tym buforowania, obsługi błędów, serializacji i rejestrowania.
* Integruje się z *Polly*, popularną biblioteką innych firm do obsługi błędów przejściowych.
* Zarządza buforowanie i okres `HttpClientHandler` istnienia wystąpienia podstawowe, aby uniknąć typowych `HttpClient` problemów z systemem DNS, które występują podczas ręcznego zarządzania okresami istnienia.
* Dodaje konfigurowalne środowisko rejestrowania (za pośrednictwem) `ILogger`dla wszystkich żądań wysyłanych za pośrednictwem klientów utworzonych przez fabrykę.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Katalog główny zawartości

Katalog główny zawartości jest podstawową ścieżką do:

* Plik wykonywalny hosting aplikacji (*.exe*).
* Skompilowane zestawy, które tworzą aplikację (*.dll*).
* Pliki zawartości niekodowej używane przez aplikację, takie jak:
  * Pliki razor (*.cshtml*, *.brzytwa*)
  * Pliki konfiguracyjne (*.json*, *.xml*)
  * Pliki danych (*.db*)
* [Katalog główny sieci Web](#web-root), zazwyczaj opublikowany folder *wwwroot.*

Podczas rozwoju:

* Katalog główny zawartości jest domyślnie wyświetlany w katalogu głównym projektu.
* Katalog główny projektu jest używany do tworzenia:
  * Ścieżka do plików zawartości niekodowej aplikacji w katalogu głównym projektu.
  * [Katalog główny sieci Web](#web-root), zazwyczaj folder *wwwroot* w katalogu głównym projektu.

Alternatywna ścieżka główna zawartości może być określona podczas [tworzenia hosta](#host). Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#content-root>.

## <a name="web-root"></a>Katalog główny sieci Web

Katalog główny sieci Web jest podstawową ścieżką do publicznych, niekodowych, statycznych plików zasobów, takich jak:

* Arkusze stylów (*.css*)
* JavaScript (*.js*)
* Zdjęcia (*.png*, *.jpg*)

Pliki statyczne są domyślnie obsługiwane tylko z katalogu głównego sieci Web (i podkatastów).

Ścieżka główna sieci Web domyślnie *{zawartość root}/wwwroot*, ale podczas [tworzenia hosta](#host)można określić inny katalog główny sieci Web. Aby uzyskać więcej informacji, zobacz [Katalog główny sieci Web](xref:fundamentals/host/web-host#web-root).

Zapobiegaj publikowaniu plików w [ \<](/visualstudio/msbuild/common-msbuild-project-items#content) *witrynie wwwroot* za pomocą elementu projektu> zawartości w pliku projektu. Poniższy przykład zapobiega publikowaniu zawartości w *katalogu wwwroot/local* i podkatastronach:

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

W razor (*.cshtml*) plików,`~/`tylda-ukośnik ( ) wskazuje na katalog główny sieci Web. Ścieżka rozpoczynająca `~/` się od ścieżki jest określana jako *ścieżka wirtualna*.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.

::: moniker-end
