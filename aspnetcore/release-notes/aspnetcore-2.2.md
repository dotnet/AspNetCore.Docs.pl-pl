---
title: Co nowego w ASP.NET Core 2.2
author: rick-anderson
description: Dowiedz się więcej o nowych funkcjach w ASP.NET Core 2.2.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: aspnetcore-2.2
ms.openlocfilehash: 54d3f1e7b0c94d69781c052694305a389a675019
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977174"
---
# <a name="whats-new-in-aspnet-core-22"></a>Co nowego w ASP.NET Core 2.2

W tym artykule przedstawiono najważniejsze zmiany w ASP.NET Core 2.2, wraz z łączami do odpowiedniej dokumentacji.

## <a name="openapi-analyzers--conventions"></a>Analizatory OpenAPI & konwencje

OpenAPI (dawniej znany jako Swagger) jest specyfikacją niezależną od języka do opisywania interfejsów API REST. Ekosystem OpenAPI zawiera narzędzia, które umożliwiają odnajdowanie, testowanie i tworzenie kodu klienta przy użyciu specyfikacji. Obsługa generowania i wizualizacji dokumentów OpenAPI w ASP.NET Core MVC jest świadczona za pośrednictwem projektów opartych na społeczności, takich jak [NSwag](https://github.com/RicoSuter/NSwag) i [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore). ASP.NET Core 2.2 zapewnia ulepszone narzędzia i środowisko uruchomieniowe do tworzenia dokumentów OpenAPI.

Więcej informacji zawierają następujące zasoby:

* <xref:web-api/advanced/analyzers>
* <xref:web-api/advanced/conventions>
* [ASP.NET Core 2.2.0-preview1: Analizatory OpenAPI & konwencje](https://blogs.msdn.microsoft.com/webdev/2018/08/23/asp-net-core-2-20-preview1-open-api-analyzers-conventions/)

## <a name="problem-details-support"></a>Obsługa szczegółów problemu

ASP.NET wprowadzono `ProblemDetails`Core 2.1, opartą na specyfikacji [RFC 7807](https://tools.ietf.org/html/rfc7807) do przenoszenia szczegółów błędu z odpowiedzią HTTP. W 2.2, `ProblemDetails` jest standardową odpowiedzią dla kodów błędów klienta w kontrolerach przypisanych z `ApiControllerAttribute`. Zwracany `IActionResult` kod stanu błędu klienta (4xx) `ProblemDetails` zwraca teraz treść. Wynik zawiera również identyfikator korelacji, który może służyć do skorelowania błędu przy użyciu dzienników żądań. W przypadku błędów klienta `ProducesResponseType` `ProblemDetails` domyślnie używa się jako typu odpowiedzi. Jest to udokumentowane w openapi / swagger danych wyjściowych generowanych przy użyciu NSwag lub Swashbuckle.AspNetCore.

## <a name="endpoint-routing"></a>Routing punktu końcowego

ASP.NET Core 2.2 używa nowego systemu *routingu punktów końcowych* do poprawy wysyłania żądań. Zmiany obejmują nowe elementy członkowskie interfejsu API generowania łączy i transformatory parametrów trasy.

Więcej informacji zawierają następujące zasoby:

* [Routing punktu końcowego w 2.2](https://blogs.msdn.microsoft.com/webdev/2018/08/27/asp-net-core-2-2-0-preview1-endpoint-routing/)
* [Transformatory parametrów trasy](https://www.hanselman.com/blog/ASPNETCore22ParameterTransformersForCleanURLGenerationAndSlugsInRazorPagesOrMVC.aspx) (patrz sekcja **Routing)**
* [Różnice między routingu IRouter- i oparte na punkcie końcowym](xref:fundamentals/routing?view=aspnetcore-2.2#differences-from-earlier-versions-of-routing)

## <a name="health-checks"></a>Kontrole kondycji

Nowa usługa kontroli kondycji ułatwia korzystanie z ASP.NET Core w środowiskach, które wymagają kontroli kondycji, takich jak Kubernetes. Kontrole kondycji obejmują oprogramowanie pośredniczące i `IHealthCheck` zestaw bibliotek definiujących abstrakcję i usługę.

Kontrole kondycji są używane przez koordynatora kontenera lub moduł równoważenia obciążenia, aby szybko określić, czy system odpowiada na żądania normalnie. Koordynator kontenera może odpowiedzieć na sprawdzanie kondycji, zatrzymując wdrożenie stopniowe lub ponowne uruchomienie kontenera. Moduł równoważenia obciążenia może odpowiedzieć na sprawdzenie kondycji, kierując ruch z dala od nieudanego wystąpienia usługi.

Kontrole kondycji są udostępniane przez aplikację jako punkt końcowy HTTP używany przez systemy monitorowania. Kontrole kondycji można skonfigurować dla różnych scenariuszy monitorowania w czasie rzeczywistym i systemów monitorowania. Służba kontroli zdrowia integruje się z [projektem BeatPulse.](https://github.com/Xabaril/BeatPulse) co ułatwia dodawanie kontroli dla dziesiątek popularnych systemów i zależności.

Aby uzyskać więcej informacji, zobacz [Kontrole kondycji w ASP.NET Core](xref:host-and-deploy/health-checks).

## <a name="http2-in-kestrel"></a>HTTP/2 w Pustułce

ASP.NET Core 2.2 dodaje obsługę protokołu HTTP/2.

HTTP/2 jest główną wersją protokołu HTTP. Godne uwagi funkcje HTTP/2 obejmują:

* Obsługa kompresji nagłówka.
* W pełni multipleksowane strumienie za pośrednictwem jednego połączenia.

Podczas gdy protokół HTTP/2 zachowuje semantykę HTTP (na przykład nagłówki i metody HTTP), jest to przełomowa zmiana z protokołu HTTP/1.x na temat sposobu oprawiania i wysyłania danych między klientem a serwerem.

W konsekwencji tej zmiany w kadrowaniu serwery i klienci muszą negocjować używaną wersję protokołu. Negocjacja protokołu warstwy aplikacji (ALPN) to rozszerzenie TLS, które umożliwia serwerowi i klientowi negocjowanie wersji protokołu używanej jako część uzgadniania TLS. Chociaż istnieje możliwość wcześniejszej wiedzy między serwerem a klientem na temat protokołu, wszystkie główne przeglądarki obsługują ALPN jako jedyny sposób na nawiązania połączenia HTTP/2.

Aby uzyskać więcej informacji, zobacz [http/2 support](xref:fundamentals/servers/index?view=aspnetcore-2.2#http2-support).

## <a name="kestrel-configuration"></a>Konfiguracja pustułki

We wcześniejszych wersjach ASP.NET Core opcje Kestrel są `UseKestrel`konfigurowane przez wywołanie . W 2.2 opcje Kestrel są `ConfigureKestrel` konfigurowane przez wywołanie konstruktora hosta. Ta zmiana rozwiązuje problem z `IServer` kolejnością rejestracji dla hostingu w trakcie. Więcej informacji zawierają następujące zasoby:

* [Ograniczanie konfliktów UseIIS](https://github.com/aspnet/KestrelHttpServer/issues/2760)
* [Konfigurowanie opcji serwera Pustułka za pomocą narzędzia Konfiguruj atrybut](xref:fundamentals/servers/kestrel?view=aspnetcore-2.2#how-to-use-kestrel-in-aspnet-core-apps)

## <a name="iis-in-process-hosting"></a>Hosting w trakcie usługi IIS

We wcześniejszych wersjach ASP.NET Core usługi IIS służą jako odwrotny serwer proxy. W 2.2 moduł ASP.NET Core może uruchomić CoreCLR i hostować aplikację wewnątrz procesu roboczego IIS (*w3wp.exe*). Hosting w trakcie zapewnia korzyści wydajności i diagnostyki podczas uruchamiania z usługi IIS.

Aby uzyskać więcej informacji, zobacz [hosting w trakcie usługi IIS](xref:host-and-deploy/aspnet-core-module?view=aspnetcore-2.2#in-process-hosting-model).

## <a name="opno-locsignalr-java-client"></a>SignalRKlient Java

ASP.NET Core 2.2 wprowadza klienta Java SignalRdla . Ten klient obsługuje łączenie się z SignalR ASP.NET Core Server z kodu Java, w tym aplikacji na Androida.

Aby uzyskać więcej informacji, zobacz [ASP.NET Core SignalR Java client](https://docs.microsoft.com/aspnet/core/signalr/java-client?view=aspnetcore-2.2).

## <a name="cors-improvements"></a>Ulepszenia CORS

We wcześniejszych wersjach ASP.NET Core oprogramowanie pośredniczące `Accept-Language` `Content-Language`CORS `Origin` umożliwia `Accept`przesyłanie nagłówków i nagłówków `CorsPolicy.Headers`niezależnie od wartości skonfigurowanych w pliku . W 2.2 dopasowanie zasad oprogramowania pośredniczącego CORS jest `Access-Control-Request-Headers` możliwe tylko wtedy, `WithHeaders`gdy nagłówki wysyłane dokładnie odpowiadają nagłówkom podanym w .

Aby uzyskać więcej informacji, zobacz [oprogramowanie pośredniczące CORS](xref:security/cors?view=aspnetcore-2.2#set-the-allowed-request-headers).

## <a name="response-compression"></a>Kompresja odpowiedzi

ASP.NET Core 2.2 może kompresować odpowiedzi za pomocą [formatu kompresji Brotli.](https://tools.ietf.org/html/rfc7932)

Aby uzyskać więcej informacji, zobacz [Oprogramowanie pośredniczące kompresji odpowiedzi obsługuje kompresję Brotli](xref:performance/response-compression?view=aspnetcore-2.2#brotli-compression-provider).

## <a name="project-templates"></a>Szablony projektów

ASP.NET Core szablony projektów internetowych zostały zaktualizowane do [Bootstrap 4](https://getbootstrap.com/docs/4.1/migration/) i [Angular 6](https://blog.angular.io/version-6-of-angular-now-available-cc56b0efa7a4). Nowy wygląd jest wizualnie prostszy i ułatwia zobaczenie ważnych struktur aplikacji.

![Strona główna lub Indeks](~/tutorials/razor-pages/razor-pages-start/_static/home2.2.png)

## <a name="validation-performance"></a>Wydajność sprawdzania poprawności

System sprawdzania poprawności MVC jest zaprojektowany tak, aby był rozszerzalny i elastyczny, co pozwala określić na podstawie żądania, które walidatory mają zastosowanie do danego modelu. Jest to idealne rozwiązanie dla autorów złożonych dostawców sprawdzania poprawności. Jednak w najczęstszym przypadku aplikacja używa tylko wbudowanych modułów sprawdzania poprawności i nie wymagają tej dodatkowej elastyczności. Wbudowane walidatory zawierają adnotacje danych, takie jak [Wymagane] `IValidatableObject`i [StringLength] oraz .

W ASP.NET Core 2.2 MVC może zwarcie sprawdzania poprawności, jeśli określa, że wykres danego modelu nie wymaga sprawdzania poprawności. Pomijanie sprawdzania poprawności powoduje znaczne ulepszenia podczas sprawdzania poprawności modeli, które nie mogą lub nie mają żadnych modułów sprawdzania poprawności. Obejmuje to obiekty, takie jak kolekcje pierwotnych `string[]` `Dictionary<string, string>`(takich jak `byte[]`, , ), lub złożone wykresy obiektów bez wielu modułów sprawdzania poprawności.

## <a name="http-client-performance"></a>Wydajność klienta HTTP

W ASP.NET Core 2.2 wydajność `SocketsHttpHandler` została zwiększona poprzez zmniejszenie rywalizacji blokowania puli połączeń. W przypadku aplikacji, które tworzą wiele wychodzących żądań HTTP, takich jak niektóre architektury mikrousług, przepustowość jest lepsza. Pod obciążeniem `HttpClient` przepustowość można poprawić nawet o 60% w systemie Linux i o 20% w systemie Windows.

Aby uzyskać więcej informacji, zobacz [żądanie ściągnięcia, które dokonało tej poprawy](https://github.com/dotnet/corefx/pull/32568).

## <a name="additional-information"></a>Dodatkowe informacje

Aby uzyskać pełną listę zmian, zobacz [ASP.NET Informacje o wersji Core 2.2](https://github.com/dotnet/aspnetcore/releases/tag/2.2.0).
