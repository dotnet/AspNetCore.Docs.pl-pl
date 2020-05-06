---
title: Co nowego w ASP.NET Core 2,2
author: rick-anderson
description: Dowiedz się więcej o nowych funkcjach w ASP.NET Core 2,2.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-2.2
ms.openlocfilehash: 3b510c7f4788a59145ef16720276fc7e4560f07e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774148"
---
# <a name="whats-new-in-aspnet-core-22"></a>Co nowego w ASP.NET Core 2,2

W tym artykule przedstawiono najbardziej znaczące zmiany w ASP.NET Core 2,2 z linkami do odpowiedniej dokumentacji.

## <a name="openapi-analyzers--conventions"></a>OpenAPI & analizatory

OpenAPI (wcześniej znany jako Swagger) to specyfikacja języka niezależny od do opisywania interfejsów API REST. Ekosystem OpenAPI zawiera narzędzia, które umożliwiają odnajdywanie, testowanie i tworzenie kodu klienta przy użyciu specyfikacji. Obsługa generowania i wizualizacji dokumentów OpenAPI w ASP.NET Core MVC jest zapewniana za pośrednictwem projektów opartych na społeczności, takich jak [NSwag](https://github.com/RicoSuter/NSwag) i [Swashbuckle. AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore). ASP.NET Core 2,2 oferuje udoskonalone narzędzia i środowisko uruchomieniowe do tworzenia dokumentów OpenAPI.

Więcej informacji zawierają następujące zasoby:

* <xref:web-api/advanced/analyzers>
* <xref:web-api/advanced/conventions>
* [ASP.NET Core 2.2.0-zestawu: OpenAPI analizatory &](https://blogs.msdn.microsoft.com/webdev/2018/08/23/asp-net-core-2-20-preview1-open-api-analyzers-conventions/)

## <a name="problem-details-support"></a>Pomoc techniczna dotycząca problemów

Wprowadzono `ProblemDetails`ASP.NET Core 2,1 na podstawie specyfikacji [RFC 7807](https://tools.ietf.org/html/rfc7807) w celu przeprowadzenia szczegółów błędu z odpowiedzi HTTP. W 2,2 `ProblemDetails` jest to standardowa odpowiedź dla kodów błędów klienta w kontrolerach przypisanych do `ApiControllerAttribute`. `IActionResult` Zwrócenie kodu stanu błędu klienta (4xx) zwróci teraz `ProblemDetails` treść. Wynik zawiera również identyfikator korelacji, który może służyć do skorelowania błędów przy użyciu dzienników żądań. W przypadku błędów klienta `ProducesResponseType` program domyślnie używa `ProblemDetails` jako typu odpowiedzi. Jest to udokumentowane w danych wyjściowych OpenAPI/Swagger wygenerowanych przy użyciu NSwag lub Swashbuckle. AspNetCore.

## <a name="endpoint-routing"></a>Routing punktów końcowych

ASP.NET Core 2,2 używa nowego systemu *routingu punktu końcowego* do ulepszonego wysyłania żądań. Zmiany obejmują nowe elementy członkowskie interfejsu API generacji linków i transformatory parametrów trasy.

Więcej informacji zawierają następujące zasoby:

* [Routing punktów końcowych w 2,2](https://blogs.msdn.microsoft.com/webdev/2018/08/27/asp-net-core-2-2-0-preview1-endpoint-routing/)
* [Transformatory parametrów trasy](https://www.hanselman.com/blog/ASPNETCore22ParameterTransformersForCleanURLGenerationAndSlugsInRazorPagesOrMVC.aspx) (zobacz sekcję **routingu** )
* [Różnice między IRouter i routingu opartego na punktach końcowych](xref:fundamentals/routing?view=aspnetcore-2.2#differences-from-earlier-versions-of-routing)

## <a name="health-checks"></a>Kontrole kondycji

Nowa usługa kontroli kondycji ułatwia korzystanie z ASP.NET Core w środowiskach wymagających kontroli kondycji, takich jak Kubernetes. Kontrole kondycji obejmują oprogramowanie pośredniczące i zestaw bibliotek, które definiują `IHealthCheck` abstrakcję i usługę.

Kontrole kondycji są używane przez koordynatora kontenerów lub moduł równoważenia obciążenia, aby szybko określić, czy system odpowiada na żądania normalnie. Koordynator kontenera może odpowiedzieć na niepowodzenie sprawdzania kondycji przez zatrzymanie wdrożenia stopniowego lub ponowne uruchomienie kontenera. Moduł równoważenia obciążenia może odpowiedzieć na kontrolę kondycji przez kierowanie ruchu z nieprawidłowego wystąpienia usługi.

Kontrole kondycji są udostępniane przez aplikację jako punkt końcowy HTTP używany przez systemy monitorowania. Kontrole kondycji można skonfigurować dla różnych scenariuszy monitorowania w czasie rzeczywistym i systemów monitorowania. Usługa sprawdzania kondycji integruje się z [projektem BeatPulse](https://github.com/Xabaril/BeatPulse). ułatwia to dodawanie czeków dla dziesiątek popularnych systemów i zależności.

Aby uzyskać więcej informacji, zobacz [kontrole kondycji w ASP.NET Core](xref:host-and-deploy/health-checks).

## <a name="http2-in-kestrel"></a>HTTP/2 w Kestrel

ASP.NET Core 2,2 dodaje obsługę protokołu HTTP/2.

HTTP/2 to główna wersja protokołu HTTP. Istotne funkcje protokołu HTTP/2 obejmują:

* Obsługa kompresji nagłówka.
* W pełni multipleksne strumienie za pośrednictwem jednego połączenia.

Chociaż protokół HTTP/2 zachowuje semantykę protokołu HTTP (na przykład nagłówki HTTP i metody), jest to istotna zmiana z protokołu HTTP/1. x na temat tego, jak dane są frameowe i wysyłane między klientem a serwerem.

W związku z tą zmianą w ramkach serwery i klienci muszą negocjować używaną wersję protokołu. Negocjowanie protokołu warstwy aplikacji (CLIENTHELLO ALPN) to rozszerzenie TLS, które umożliwia serwerowi i klientowi negocjowanie wersji protokołu używanej w ramach uzgadniania TLS. Chociaż istnieje możliwość uzyskania wcześniejszej wiedzy między serwerem a klientem programu, wszystkie główne przeglądarki obsługują CLIENTHELLO ALPN jako jedyny sposób nawiązywania połączenia HTTP/2.

Aby uzyskać więcej informacji, zobacz [Obsługa protokołu HTTP/2](xref:fundamentals/servers/index?view=aspnetcore-2.2#http2-support).

## <a name="kestrel-configuration"></a>Konfiguracja Kestrel

We wcześniejszych wersjach ASP.NET Core opcje Kestrel są konfigurowane przez wywołanie `UseKestrel`. W 2,2 opcje Kestrel są konfigurowane przez wywołanie `ConfigureKestrel` na konstruktorze hosta. Ta zmiana rozwiązuje problem z kolejnością `IServer` rejestracji na potrzeby hostingu w procesie. Więcej informacji zawierają następujące zasoby:

* [Eliminowanie konfliktu Iisurl](https://github.com/aspnet/KestrelHttpServer/issues/2760)
* [Konfigurowanie opcji serwera Kestrel za pomocą ConfigureKestrel](xref:fundamentals/servers/kestrel?view=aspnetcore-2.2#how-to-use-kestrel-in-aspnet-core-apps)

## <a name="iis-in-process-hosting"></a>Hosting w procesie usług IIS

W starszych wersjach ASP.NET Core usługi IIS pełnią funkcję zwrotnego serwera proxy. W 2,2 moduł ASP.NET Core może wykonać rozruch CoreCLR i hostowanie aplikacji w procesie roboczym usług IIS (*w3wp. exe*). Hosting w procesie zapewnia wydajność i zyski z diagnostyki podczas pracy z usługami IIS.

Aby uzyskać więcej informacji, zobacz [hosting w procesie dla usług IIS](xref:host-and-deploy/aspnet-core-module?view=aspnetcore-2.2#in-process-hosting-model).

## <a name="signalr-java-client"></a>SignalRKlient Java

ASP.NET Core 2,2 wprowadza klienta Java dla programu SignalR. Ten klient obsługuje łączenie z serwerem ASP.NET Core SignalR przy użyciu kodu Java, w tym aplikacji dla systemu Android.

Aby uzyskać więcej informacji, [zobacz SignalR ASP.NET Core klienta Java](https://docs.microsoft.com/aspnet/core/signalr/java-client?view=aspnetcore-2.2).

## <a name="cors-improvements"></a>Ulepszenia funkcji CORS

We wcześniejszych wersjach ASP.NET Core oprogramowanie CORS `Accept`umożliwia `Accept-Language` `Content-Language`wysyłanie `Origin` nagłówków niezależnie od wartości skonfigurowanych w. `CorsPolicy.Headers` W 2,2, dopasowanie zasad oprogramowania CORS jest możliwe tylko wtedy, gdy nagłówki wysyłane `Access-Control-Request-Headers` dokładnie pasują do nagłówków określonych w. `WithHeaders`

Aby uzyskać więcej informacji, zobacz [oprogramowanie pośredniczące CORS](xref:security/cors?view=aspnetcore-2.2#set-the-allowed-request-headers).

## <a name="response-compression"></a>Kompresja odpowiedzi

ASP.NET Core 2,2 może kompresować odpowiedzi przy użyciu [formatu kompresji Brotli](https://tools.ietf.org/html/rfc7932).

Aby uzyskać więcej informacji, zobacz Kompresja [oprogramowania pośredniczącego obsługuje kompresję Brotli](xref:performance/response-compression?view=aspnetcore-2.2#brotli-compression-provider).

## <a name="project-templates"></a>Szablony projektów

Szablony projektu sieci Web ASP.NET Core zostały zaktualizowane do [Bootstrap 4](https://getbootstrap.com/docs/4.1/migration/) i [kątowe 6](https://blog.angular.io/version-6-of-angular-now-available-cc56b0efa7a4). Nowy wygląd jest wizualnie prostszy i ułatwia wyświetlanie ważnych struktur aplikacji.

![Strona główna lub indeks](~/tutorials/razor-pages/razor-pages-start/_static/home2.2.png)

## <a name="validation-performance"></a>Wydajność walidacji

System sprawdzania poprawności MVC został zaprojektowany do rozszerzalności i elastyczności, co pozwala na określenie na podstawie żądania, którego moduł sprawdzania poprawności ma zastosowanie do danego modelu. Jest to doskonałe rozwiązanie w przypadku tworzenia złożonych dostawców weryfikacji. Jednak w najbardziej typowym przypadku aplikacja używa tylko wbudowanych modułów sprawdzania poprawności i nie wymaga tej dodatkowej elastyczności. Wbudowane moduły sprawdzania poprawności obejmują adnotacje, takie jak [Required] i [StringLength], i `IValidatableObject`.

W ASP.NET Core 2,2, MVC może skrócić do krótkiego obwodu, jeśli określa, że dany wykres modelu nie wymaga weryfikacji. Pomijanie sprawdzania poprawności skutkuje znaczącymi ulepszeniami podczas weryfikowania modeli, które nie mogą lub nie mają żadnych modułów walidacji. Obejmuje to takie obiekty jak kolekcje elementów pierwotnych (takich jak `byte[]`, `string[]`, `Dictionary<string, string>`) lub złożone wykresy obiektów bez wielu modułów sprawdzania poprawności.

## <a name="http-client-performance"></a>Wydajność klienta HTTP

W ASP.NET Core 2,2 wydajność `SocketsHttpHandler` została ulepszona przez zmniejszenie rywalizacji o blokadę puli połączeń. W przypadku aplikacji, które tworzą wiele wychodzących żądań HTTP, takich jak architektury mikrousług, zwiększa się przepływność. W obszarze obciążenie `HttpClient` można ulepszyć przepływność przez nawet 60% w systemie Linux i 20% systemu Windows.

Aby uzyskać więcej informacji, zobacz [żądanie ściągnięcia, które dokonało tego ulepszenia](https://github.com/dotnet/corefx/pull/32568).

## <a name="additional-information"></a>Dodatkowe informacje

Aby uzyskać pełną listę zmian, zapoznaj się z [informacjami o wersji ASP.NET Core 2,2](https://github.com/dotnet/aspnetcore/releases/tag/2.2.0).
