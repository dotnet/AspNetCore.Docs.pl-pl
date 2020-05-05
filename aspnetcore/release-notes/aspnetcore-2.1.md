---
title: Co nowego w ASP.NET Core 2,1
author: isaac2004
description: Dowiedz się więcej o nowych funkcjach w ASP.NET Core 2,1.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-2.1
ms.openlocfilehash: 1f68bd5347ba1f67e56b7a2fe8914ffdaef8010c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774083"
---
# <a name="whats-new-in-aspnet-core-21"></a>Co nowego w ASP.NET Core 2,1

W tym artykule przedstawiono najbardziej znaczące zmiany w ASP.NET Core 2,1 z linkami do odpowiedniej dokumentacji.

## SignalR

SignalRzostał ponownie zapisany dla ASP.NET Core 2,1. ASP.NET Core SignalR obejmuje kilka ulepszeń:

* Uproszczony model skalowalny w poziomie.
* Nowy klient JavaScript bez zależności jQuery.
* Nowy, kompaktowy protokół binarny oparty na MessagePack.
* Obsługa niestandardowych protokołów.
* Nowy model odpowiedzi przesyłania strumieniowego.
* Obsługa klientów w oparciu o usługi WebSockets.

Aby uzyskać więcej informacji, [zobacz SignalRASP.NET Core ](xref:signalr/introduction).

## <a name="razor-class-libraries"></a>Razorbiblioteki klas

ASP.NET Core 2,1 ułatwia tworzenie i dołączanie Razorinterfejsu użytkownika opartego na interfejsie w bibliotece i udostępnianie go w wielu projektach. Nowy Razor zestaw SDK umożliwia tworzenie Razor plików w projekcie biblioteki klas, który można spakować do pakietu NuGet. Widoki i strony w bibliotekach są automatycznie odnajdywane i mogą zostać zastąpione przez aplikację. Przez integrację Razor kompilacji z kompilacją:

* Czas uruchamiania aplikacji jest znacznie szybszy.
* Szybkie aktualizacje Razor widoków i stron w środowisku uruchomieniowym są nadal dostępne jako część iteracyjnego przepływu pracy programistycznej.

Aby uzyskać więcej informacji, zobacz [Tworzenie interfejsu użytkownika wielokrotnego Razor użytku przy użyciu biblioteki klas](xref:razor-pages/ui-class).

## <a name="identity-ui-library--scaffolding"></a>IdentityTworzenie szkieletu & biblioteki interfejsu użytkownika

ASP.NET Core 2,1 zapewnia [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor bibliotekę klas](xref:razor-pages/ui-class). Aplikacje, które Identity obejmują, mogą zastosować Identity nowy szkielet, aby selektywnie dodać kod źródłowy znajdujący Identity Razor się w bibliotece klas (RCL). Może być konieczne wygenerowanie kodu źródłowego, aby można było zmodyfikować kod i zmienić zachowanie. Na przykład możesz poinstruować szkielet, aby wygenerował kod używany w rejestracji. Wygenerowany kod ma pierwszeństwo przed tym samym kodem w Identity RCL.

Aplikacje, które **nie** obejmują uwierzytelniania, mogą zastosować Identity szkieleter w celu dodania pakietu Identity RCL. Dostępna jest opcja wybierania Identity kodu do wygenerowania.

Aby uzyskać więcej informacji, [Zobacz Identity szkielet w ASP.NET Core projekty](xref:security/authentication/scaffold-identity).

## <a name="https"></a>HTTPS

Dzięki zwiększeniu poziomu zabezpieczeń i ochrony prywatności włączenie protokołu HTTPS dla aplikacji sieci Web jest ważne. Wymuszanie protokołu HTTPS staje się coraz bardziej rygorystyczne w sieci Web. Lokacje, które nie używają protokołu HTTPS, są uznawane za niezabezpieczone. Przeglądarki (chrom, Mozilla) zaczynają wymuszać, że funkcje sieci Web muszą być używane z bezpiecznego kontekstu. [Rodo](xref:security/gdpr) wymaga użycia protokołu HTTPS do ochrony prywatności użytkowników. Korzystanie z protokołu HTTPS w środowisku produkcyjnym ma krytyczne znaczenie, dzięki czemu korzystanie z protokołu HTTPS podczas programowania może pomóc w zapobieganiu problemom z wdrażaniem (na przykład niezabezpieczonych łączy). ASP.NET Core 2,1 zawiera szereg ulepszeń, które ułatwiają korzystanie z protokołu HTTPS w programowaniu i Konfigurowanie protokołu HTTPS w środowisku produkcyjnym. Aby uzyskać więcej informacji, zobacz [Wymuszanie protokołu HTTPS](xref:security/enforcing-ssl).

### <a name="on-by-default"></a>Domyślnie włączone

Aby ułatwić tworzenie bezpiecznych witryn internetowych, protokół HTTPS jest teraz domyślnie włączony. Począwszy od 2,1, Kestrel nasłuchuje `https://localhost:5001` , gdy obecny jest lokalny certyfikat programistyczny. Tworzony jest certyfikat programistyczny:

* W ramach pierwszego uruchomienia zestaw .NET Core SDK, gdy zestaw SDK jest używany po raz pierwszy.
* Ręczne korzystanie z nowego `dev-certs` narzędzia.

Uruchom `dotnet dev-certs https --trust` , aby zaufać certyfikatowi.

### <a name="https-redirection-and-enforcement"></a>Przekierowywanie i wymuszanie protokołu HTTPS

Aplikacje sieci Web zazwyczaj muszą nasłuchiwać zarówno protokół HTTP, jak i HTTPS, a następnie przekierować cały ruch HTTP do protokołu HTTPS. W 2,1, wyspecjalizowane oprogramowanie pośredniczące do przekierowywania HTTPS, które inteligentnie przekierowuje się w oparciu o obecność konfiguracji lub powiązanych portów serwera.

Korzystanie z protokołu HTTPS może być realizowane przy użyciu [protokołu HTTP Strict Transport Security Protocol (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts). HSTS instruuje przeglądarki, aby zawsze mogli uzyskać dostęp do witryny za pośrednictwem protokołu HTTPS. ASP.NET Core 2,1 dodaje oprogramowanie pośredniczące HSTS, które obsługuje opcje maksymalnego wieku, poddomen i listy wstępnego ładowania HSTS.

### <a name="configuration-for-production"></a>Konfiguracja dla środowiska produkcyjnego

W środowisku produkcyjnym należy jawnie skonfigurować protokół HTTPS. W 2,1 został dodany domyślny schemat konfiguracji służący do konfigurowania protokołu HTTPS dla Kestrel. Aplikacje można skonfigurować do korzystania z:

* Wiele punktów końcowych, w tym adresów URL. Aby uzyskać więcej informacji, zobacz [Implementacja serwera sieci Web Kestrel: Konfiguracja punktu końcowego](xref:fundamentals/servers/kestrel#endpoint-configuration).
* Certyfikat do użycia na potrzeby protokołu HTTPS z pliku znajdującego się na dysku lub z magazynu certyfikatów.

## <a name="gdpr"></a>RODO

ASP.NET Core udostępnia interfejsy API i szablony, które pomagają spełnić niektóre wymagania dotyczące [ogólne rozporządzenie o ochronie danych UE (Rodo)](https://www.eugdpr.org/) . Aby uzyskać więcej informacji, zobacz [Obsługa Rodo w ASP.NET Core](xref:security/gdpr). [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) pokazuje, jak używać programu i umożliwia testowanie większości punktów rozszerzenia Rodo i interfejsów API dodanych do szablonów ASP.NET Core 2,1.

## <a name="integration-tests"></a>Testy integracji

Wprowadzono nowy pakiet, który usprawnia tworzenie i wykonywanie testów. Pakiet [Microsoft. AspNetCore. MVC. test](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) obsługuje następujące zadania:

* Kopiuje plik zależności (*\*. deps*) z testowanej aplikacji do folderu *bin* projektu testowego.
* Ustawia katalog główny zawartości dla elementu głównego projektu przetestowanej aplikacji, co umożliwia znalezienie plików statycznych i stron/widoków podczas wykonywania testów.
* Udostępnia klasę [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) do usprawnienia uruchamiania przetestowanej aplikacji z [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).

Poniższy test korzysta z [xUnit](https://xunit.github.io/) , aby sprawdzić, czy strona indeksu ładuje się z kodem stanu sukcesu i z prawidłowym nagłówkiem Content-Type:

```csharp
public class BasicTests
    : IClassFixture<WebApplicationFactory<RazorPagesProject.Startup>>
{
    private readonly HttpClient _client;

    public BasicTests(WebApplicationFactory<RazorPagesProject.Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetHomePage()
    {
        // Act
        var response = await _client.GetAsync("/");

        // Assert
        response.EnsureSuccessStatusCode(); // Status Code 200-299
        Assert.Equal("text/html; charset=utf-8",
            response.Content.Headers.ContentType.ToString());
    }
}
```

Aby uzyskać więcej informacji, zobacz temat [testy integracji](xref:test/integration-tests) .

## <a name="apicontroller-actionresultt"></a>[ApiController], ActionResult\<T>

ASP.NET Core 2,1 dodaje nowe konwencje programowania, które ułatwiają tworzenie czystych i opisowych interfejsów API sieci Web. `ActionResult<T>`to nowy typ, który został dodany, aby umożliwić aplikacji zwracanie typu odpowiedzi lub dowolnego innego wyniku akcji (podobnego do IActionResult), przy czym wskazuje typ odpowiedzi. `[ApiController]` Atrybut został również dodany jako sposób, aby można było wyrazić zgodę na konwencje i zachowania specyficzne dla interfejsu API sieci Web.

Aby uzyskać więcej informacji, zobacz [Tworzenie internetowych interfejsów API za pomocą ASP.NET Core](xref:web-api/index).

## <a name="ihttpclientfactory"></a>IHttpClientFactory

ASP.NET Core 2,1 obejmuje nową `IHttpClientFactory` usługę, która ułatwia konfigurowanie i używanie wystąpień `HttpClient` w aplikacjach. `HttpClient`ma już koncepcję delegowania programów obsługi, które mogą być połączone ze sobą w przypadku wychodzących żądań HTTP. Fabryka:

* Sprawia, że rejestrowanie wystąpień `HttpClient` na nazwę klienta jest bardziej intuicyjne.
* Implementuje procedurę obsługi Polly, która umożliwia używanie zasad Polly do ponawiania, CircuitBreakers itd.

Aby uzyskać więcej informacji, zobacz [Inicjowanie żądań HTTP](xref:fundamentals/http-requests).

## <a name="kestrel-transport-configuration"></a>Konfiguracja transportu Kestrel

W wersji ASP.NET Core 2,1 Kestrel domyślny transport nie jest już oparty na Libuv, ale zamiast w oparciu o zarządzane gniazda. Aby uzyskać więcej informacji, zobacz [Kestrel Web Server implementation: transport Configuration](xref:fundamentals/servers/kestrel#transport-configuration).

## <a name="generic-host-builder"></a>Ogólny Konstruktor hosta

Wprowadzono ogólny Konstruktor hosta (`HostBuilder`). Tego konstruktora można używać w przypadku aplikacji, które nie przetwarzają żądań HTTP (wiadomości, zadań w tle itp.).

Aby uzyskać więcej informacji, zobacz [host ogólny programu .NET](xref:fundamentals/host/generic-host).

## <a name="updated-spa-templates"></a>Zaktualizowane szablony SPA

Szablony aplikacji jednostronicowych umożliwiające przeznaczenie, reagowanie i reagowanie na Redux są aktualizowane w taki sposób, aby korzystały ze standardowych struktur projektu i systemów kompilacji dla każdej struktury.

Szablon kątowy jest oparty na kątowym interfejsie wiersza polecenia, a szablony reagowania opierają się na tworzeniu aplikacji.

Aby uzyskać więcej informacji, zobacz:

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="razor-pages-search-for-razor-assets"></a>RazorStrony szukają Razor elementów zawartości

W 2,1 Razor strony szukają Razor zasobów (takich jak układy i części) w następujących katalogach w podanej kolejności:

1. Folder bieżące strony.
1. */Pages/Shared/*
1. */Views/Shared/*

## <a name="razor-pages-in-an-area"></a>RazorStrony w obszarze

RazorStrony obsługują teraz [obszary](xref:mvc/controllers/areas). Aby zobaczyć przykład obszarów, Utwórz nową Razor aplikację sieci Web dla poszczególnych kont użytkowników. Aplikacja Razor internetowa stron z pojedynczymi kontami użytkowników zawiera */Areas/Identity/Pages*.

## <a name="mvc-compatibility-version"></a>Wersja zgodności MVC

<xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> Metoda pozwala aplikacji na zgodę lub rezygnację z ewentualnych zmian w zachowaniu, wprowadzonych w ASP.NET Core MVC 2,1 lub nowszych.

Aby uzyskać więcej informacji, zobacz <xref:mvc/compatibility-version>.

## <a name="migrate-from-20-to-21"></a>Migrowanie z 2,0 do 2,1

Zobacz [Migrowanie z ASP.NET Core 2,0 do 2,1](xref:migration/20_21).

## <a name="additional-information"></a>Dodatkowe informacje

Aby uzyskać pełną listę zmian, zapoznaj się z [informacjami o wersji ASP.NET Core 2,1](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0).
