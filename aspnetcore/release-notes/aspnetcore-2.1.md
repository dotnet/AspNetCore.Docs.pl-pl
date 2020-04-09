---
title: Co nowego w ASP.NET Core 2.1
author: isaac2004
description: Dowiedz się więcej o nowych funkcjach w ASP.NET Core 2.1.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: aspnetcore-2.1
ms.openlocfilehash: af5807b782d4acec8c7d40111dc508dfa6127057
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667546"
---
# <a name="whats-new-in-aspnet-core-21"></a>Co nowego w ASP.NET Core 2.1

W tym artykule przedstawiono najważniejsze zmiany w ASP.NET Core 2.1, wraz z łączami do odpowiedniej dokumentacji.

## SignalR

SignalRzostał przepisany dla ASP.NET Core 2.1. ASP.NET Core SignalR zawiera szereg ulepszeń:

* Uproszczony model skalowania w poziomie.
* Nowy klient JavaScript bez zależności jQuery.
* Nowy kompaktowy protokół binarny oparty na MessagePack.
* Obsługa protokołów niestandardowych.
* Nowy model odpowiedzi przesyłania strumieniowego.
* Obsługa klientów na podstawie gołych WebSockets.

Aby uzyskać więcej informacji, zobacz [ASP.NET Core SignalR ](xref:signalr/introduction).

## <a name="razor-class-libraries"></a>Biblioteki klas Razor

ASP.NET Core 2.1 ułatwia tworzenie i dołączanie interfejsu użytkownika opartego na maszynie Razor w bibliotece i udostępnianie go w wielu projektach. Nowy zestawu Razor SDK umożliwia tworzenie plików Razor do projektu biblioteki klas, które mogą być pakowane w pakiecie NuGet. Widoki i strony w bibliotekach są automatycznie odnajdywane i mogą być zastępowane przez aplikację. Poprzez zintegrowanie kompilacji Razor do kompilacji:

* Czas uruchamiania aplikacji jest znacznie szybszy.
* Szybkie aktualizacje widoków razor i stron w czasie wykonywania są nadal dostępne w ramach iteracyjnego przepływu pracy rozwoju.

Aby uzyskać więcej informacji, zobacz [Tworzenie interfejsu użytkownika wielokrotnego użytku przy użyciu projektu Biblioteka klas Razor](xref:razor-pages/ui-class).

## <a name="identity-ui-library--scaffolding"></a>Biblioteka interfejsu użytkownika tożsamości & rusztowania

ASP.NET Core 2.1 zapewnia [ASP.NET podstawową tożsamość](xref:security/authentication/identity) jako [bibliotekę klas Razor.](xref:razor-pages/ui-class) Aplikacje, które zawierają tożsamość można zastosować nowy szkielet usztrzacz tożsamości selektywnie dodać kod źródłowy zawarty w bibliotece klas razor tożsamości (RCL). Można wygenerować kod źródłowy, dzięki czemu można zmodyfikować kod i zmienić zachowanie. Na przykład można poinstruować szkieletu do generowania kodu używanego w rejestracji. Wygenerowany kod ma pierwszeństwo przed tym samym kodem w podstawie listy RCL tożsamości.

Aplikacje, które **nie** zawierają uwierzytelniania można zastosować szkieletu tożsamości, aby dodać pakiet tożsamości RCL. Masz możliwość wybrania kodu tożsamości, który ma zostać wygenerowany.

Aby uzyskać więcej informacji, zobacz [Tożsamość szkieletu w ASP.NET podstawowych projektach](xref:security/authentication/scaffold-identity).

## <a name="https"></a>HTTPS

Przy zwiększonym skupieniu się na bezpieczeństwie i prywatności ważne jest włączenie protokołu HTTPS dla aplikacji internetowych. Egzekwowanie protokołu HTTPS staje się coraz bardziej rygorystyczne w internecie. Witryny, które nie korzystają z protokołu HTTPS, są uważane za niezabezpieczone. Przeglądarki (Chromium, Mozilla) zaczynają wymuszać, że funkcje internetowe muszą być używane z bezpiecznego kontekstu. [RODO](xref:security/gdpr) wymaga użycia protokołu HTTPS w celu ochrony prywatności użytkowników. Podczas korzystania z protokołu HTTPS w środowisku produkcyjnym jest krytyczna, przy użyciu protokołu HTTPS w rozwoju może pomóc w zapobieganiu problemom we wdrożeniu (na przykład niezabezpieczone łącza). ASP.NET Core 2.1 zawiera szereg ulepszeń, które ułatwiają korzystanie z protokołu HTTPS w programowaniu i konfigurowanie protokołu HTTPS w produkcji. Aby uzyskać więcej informacji, zobacz [Wymuszanie protokołu HTTPS](xref:security/enforcing-ssl).

### <a name="on-by-default"></a>Domyślnie włączony

Aby ułatwić bezpieczne tworzenie witryn sieci Web, protokół HTTPS jest teraz domyślnie włączony. Począwszy od 2.1, Kestrel nasłuchuje, `https://localhost:5001` kiedy certyfikat rozwoju lokalnego jest obecny. Tworzony jest certyfikat dewelopera:

* W ramach pierwszego uruchomienia sdk .NET Core, gdy używasz SDK po raz pierwszy.
* Ręcznie za pomocą `dev-certs` nowego narzędzia.

Uruchom, `dotnet dev-certs https --trust` aby zaufać certyfikatowi.

### <a name="https-redirection-and-enforcement"></a>Przekierowanie i egzekwowanie protokołu HTTPS

Aplikacje sieci Web zazwyczaj muszą słuchać zarówno na HTTP, jak i HTTPS, ale następnie przekierowywać cały ruch HTTP do protokołu HTTPS. W wersji 2.1 wprowadzono specjalistyczne oprogramowanie pośredniczące przekierowania HTTPS, które inteligentnie przekierowuje na podstawie obecności konfiguracji lub powiązanych portów serwera.

Korzystanie z protokołu HTTPS można dodatkowo wymusić za pomocą [protokołu HTTP Strict Transport Security Protocol (HSTS).](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) HSTS instruuje przeglądarki, aby zawsze uzyskiwały dostęp do witryny za pośrednictwem protokołu HTTPS. ASP.NET Core 2.1 dodaje oprogramowanie pośredniczące HSTS, które obsługuje opcje dla maksymalnego wieku, subdomen i listy preload HSTS.

### <a name="configuration-for-production"></a>Konfiguracja do produkcji

W produkcji protokół HTTPS musi być jawnie skonfigurowany. W 2.1 dodano domyślny schemat konfiguracji konfiguracji protokołu HTTPS dla Kestrel. Aplikacje można skonfigurować do używania:

* Wiele punktów końcowych, w tym adresy URL. Aby uzyskać więcej informacji, zobacz [Implementacja serwera sieci Web Kestrel: Konfiguracja punktu końcowego](xref:fundamentals/servers/kestrel#endpoint-configuration).
* Certyfikat do użycia w pliku HTTPS z pliku na dysku lub z magazynu certyfikatów.

## <a name="gdpr"></a>RODO

ASP.NET Core udostępnia interfejsy API i szablony, które pomagają spełnić niektóre wymogi ogólnego rozporządzenia o [ochronie danych (RODO) UE.](https://www.eugdpr.org/) Aby uzyskać więcej informacji, zobacz [pomoc techniczna GDPR w ASP.NET Core](xref:security/gdpr). [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) pokazuje, jak używać i umożliwia testowanie większości punktów rozszerzenia RODO i interfejsów API dodanych do szablonów ASP.NET Core 2.1.

## <a name="integration-tests"></a>Testy integracji

Wprowadzono nowy pakiet, który usprawnia tworzenie i wykonywanie testów. Pakiet [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) obsługuje następujące zadania:

* Kopiuje plik zależności (*\*deps*) z testowanej aplikacji do folderu *bin* projektu testowego.
* Ustawia katalog główny zawartości w katalogu głównym projektu testowanego aplikacji, tak aby pliki statyczne i strony/widoki były znajdowane podczas wykonywania testów.
* Udostępnia [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) klasy usprawnić boottrapping testowane aplikacji z [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).

Następujący test używa [xUnit,](https://xunit.github.io/) aby sprawdzić, czy strona Indeks jest wczytyty z kodem stanu sukcesu i z poprawnym nagłówkiem typu zawartości:

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

Aby uzyskać więcej informacji, zobacz temat [testy integracji.](xref:test/integration-tests)

## <a name="apicontroller-actionresultt"></a>[ApiController], ActionResult\<T>

ASP.NET Core 2.1 dodaje nowe konwencje programowania, które ułatwiają tworzenie czystych i opisowych interfejsów API sieci Web. `ActionResult<T>`jest nowy typ dodany, aby umożliwić aplikacji do zwrócenia typu odpowiedzi lub inny wynik akcji (podobne do IActionResult), przy jednoczesnym pokazaniu typu odpowiedzi. Atrybut `[ApiController]` został również dodany jako sposób, aby zdecydować się na konwencje i zachowania specyficzne dla interfejsu API sieci Web.

Aby uzyskać więcej informacji, zobacz [Tworzenie interfejsów API sieci Web z ASP.NET Core](xref:web-api/index).

## <a name="ihttpclientfactory"></a>IHttpClientFactory

ASP.NET Core 2.1 zawiera nową `IHttpClientFactory` usługę, która ułatwia konfigurowanie i korzystanie `HttpClient` z wystąpień w aplikacjach. `HttpClient`już ma koncepcję delegowanie programów obsługi, które mogą być połączone ze sobą dla wychodzących żądań HTTP. Fabryka:

* Sprawia, że rejestrowanie `HttpClient` wystąpień na nazwanego klienta jest bardziej intuicyjne.
* Implementuje program obsługi Polly, który umożliwia zasady Polly, które mają być używane do ponowiania prób, CircuitBreakers, itp.

Aby uzyskać więcej informacji, zobacz [Inicjowanie żądań HTTP](xref:fundamentals/http-requests).

## <a name="kestrel-transport-configuration"></a>Konfiguracja transportu pustułki

Wraz z wydaniem ASP.NET Core 2.1 domyślny transport Kestrel nie jest już oparty na Libuv, ale zamiast tego opiera się na zarządzanych gniazdach. Aby uzyskać więcej informacji, zobacz [Kestrel web server implementation: Transport configuration](xref:fundamentals/servers/kestrel#transport-configuration).

## <a name="generic-host-builder"></a>Ogólny konstruktor hostów

Wprowadzono generic host`HostBuilder`builder ( ). Tego konstruktora można używać w przypadku aplikacji, które nie przetwarzają żądań HTTP (wiadomości, zadania w tle itp.).

Aby uzyskać więcej informacji, zobacz [Host ogólny platformy .NET](xref:fundamentals/host/generic-host).

## <a name="updated-spa-templates"></a>Zaktualizowane szablony SPA

Szablony aplikacji jednostronicowych dla angular, React i React with Redux są aktualizowane w celu użycia standardowych struktur projektu i systemów kompilacji dla każdej struktury.

Szablon kątowy jest oparty na angular CLI, a szablony React są oparte na create-react-app.

Aby uzyskać więcej informacji, zobacz:

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="razor-pages-search-for-razor-assets"></a>Strony razor wyszukiwania zasobów Razor

W 2.1 Strony Razor wyszukują zasoby Razor (takie jak układy i części) w następujących katalogach w podanej kolejności:

1. Folder Bieżące strony.
1. */Strony/Udostępnione/*
1. */Widoki/Udostępnione/*

## <a name="razor-pages-in-an-area"></a>Strony brzytwy w obszarze

Razor Pages obsługuje teraz [obszary](xref:mvc/controllers/areas). Aby wyświetlić przykład obszarów, utwórz nową aplikację internetową Razor Pages z indywidualnymi kontami użytkowników. Aplikacja internetowa Razor Pages z indywidualnymi kontami użytkowników zawiera */Obszary/Tożsamość/Strony*.

## <a name="mvc-compatibility-version"></a>Wersja kompatybilności MVC

Metoda <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> umożliwia aplikacji do wyrażenia zgody lub rezygnacji z potencjalnie przełomowych zmian zachowania wprowadzonych w ASP.NET Core MVC 2.1 lub nowszej.

Aby uzyskać więcej informacji, zobacz <xref:mvc/compatibility-version>.

## <a name="migrate-from-20-to-21"></a>Migracja z 2,0 do 2,1

Zobacz [Migrowanie z ASP.NET Core 2.0 do 2.1](xref:migration/20_21).

## <a name="additional-information"></a>Dodatkowe informacje

Aby uzyskać pełną listę zmian, zobacz [ASP.NET Informacje o wersji Core 2.1](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0).
