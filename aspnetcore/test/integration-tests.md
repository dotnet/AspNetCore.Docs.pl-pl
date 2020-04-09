---
title: Testy integracji w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak testy integracji zapewniają poprawne działanie składników aplikacji na poziomie infrastruktury, w tym bazy danych, systemu plików i sieci.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/06/2019
uid: test/integration-tests
ms.openlocfilehash: 414e47b9c5a1c843755bd79d313f503b554945db
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664697"
---
# <a name="integration-tests-in-aspnet-core"></a>Testy integracji w ASP.NET Core

[Javier Calvarro Nelson](https://github.com/javiercn), Steve [Smith](https://ardalis.com/)i Jos van [der Til](https://jvandertil.nl)

::: moniker range=">= aspnetcore-3.0"

Testy integracji upewnij się, że składniki aplikacji działają poprawnie na poziomie, który zawiera infrastrukturę pomocniczą aplikacji, takich jak baza danych, system plików i sieci. ASP.NET Core obsługuje testy integracji przy użyciu struktury testów jednostkowych z testowym hostem internetowym i serwerem testowym w pamięci.

W tym temacie przyjęto podstawową wiedzę na temat testów jednostkowych. Jeśli nie znasz pojęć testowych, zobacz [testowanie jednostek w temacie .NET Core i .NET Standard](/dotnet/core/testing/) i jego połączonej zawartości.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykładowa aplikacja to aplikacja Razor Pages i zakłada podstawową wiedzę na temat stron Razor. Jeśli nie znasz stron Razor, zobacz następujące tematy:

* [Wprowadzenie do produktu Razor Pages](xref:razor-pages/index)
* [Wprowadzenie do korzystania ze stron Razor](xref:tutorials/razor-pages/razor-pages-start)
* [Testy jednostkowe stron Razor](xref:test/razor-pages-tests)

> [!NOTE]
> Do testowania OSO, poleciliśmy narzędzie, takie jak [Selen](https://www.seleniumhq.org/), które można zautomatyzować przeglądarki.

## <a name="introduction-to-integration-tests"></a>Wprowadzenie do testów integracyjnych

Testy integracji oceniają składniki aplikacji na szerszym poziomie niż [testy jednostkowe.](/dotnet/core/testing/) Testy jednostkowe są używane do testowania izolowanych składników oprogramowania, takich jak metody poszczególnych klas. Testy integracji potwierdzają, że dwa lub więcej składników aplikacji współpracują ze sobą, aby uzyskać oczekiwany wynik, ewentualnie w tym każdy składnik wymagany do pełnego przetworzenia żądania.

Te szersze testy są używane do testowania infrastruktury aplikacji i całej struktury, często w tym następujących składników:

* baza danych
* System plików
* Urządzenia sieciowe
* Potok żądania odpowiedzi

Testy jednostkowe używają sfabrykowanych komponentów, znanych jako *podróbki* lub *makiety obiektów,* zamiast składników infrastruktury.

W przeciwieństwie do testów jednostkowych, testy integracyjne:

* Użyj rzeczywistych składników, które aplikacja używa w produkcji.
* Wymagaj więcej kodu i przetwarzania danych.
* Trwać dłużej, aby uruchomić.

W związku z tym ograniczyć użycie testów integracji do najważniejszych scenariuszy infrastruktury. Jeśli zachowanie można przetestować przy użyciu testu jednostkowego lub testu integracji, wybierz test jednostkowy.

> [!TIP]
> Nie zapisuj testów integracji dla każdej możliwej permutacji danych i dostępu do plików za pomocą baz danych i systemów plików. Niezależnie od tego, ile miejsc w aplikacji wchodzi w interakcję z bazami danych i systemami plików, skoncentrowany zestaw testów integracji odczytu, zapisu, aktualizacji i usuwania jest zwykle w stanie odpowiednio przetestować składniki bazy danych i systemu plików. Użyj testów jednostkowych dla rutynowych testów logiki metody, które współdziałają z tymi składnikami. W testach jednostkowych użycie podróbek/makiet infrastruktury powoduje szybsze wykonywanie testów.

> [!NOTE]
> W dyskusjach na temat testów integracyjnych testowany projekt jest często nazywany *testowanym systemem*lub w skrócie "SUT".
>
> *"SUT" jest używany w tym temacie, aby odwołać się do testowanych aplikacji ASP.NET Core.*

## <a name="aspnet-core-integration-tests"></a>ASP.NET Podstawowe testy integracji

Testy integracji w ASP.NET Core wymagają następujących czynności:

* Projekt testowy jest używany do przechowywania i wykonywania testów. Projekt testowy ma odniesienie do SUT.
* Projekt testowy tworzy testowy host internetowy dla SUT i używa klienta serwera testowego do obsługi żądań i odpowiedzi z SUT.
* Próbkowania jest używany do wykonywania testów i raport wyników testów.

Testy integracji wykonaj sekwencję zdarzeń, które obejmują zwykłe kroki testu *Arrange*, *Act*i *Assert:*

1. Host internetowy SUT jest skonfigurowany.
1. Klient serwera testowego jest tworzony do przesyłania żądań do aplikacji.
1. Krok testu *Rozmieszczanie* jest wykonywany: aplikacja testowa przygotowuje żądanie.
1. Krok testu *act* jest wykonywany: klient przesyła żądanie i odbiera odpowiedź.
1. Krok testu *Assert* jest wykonywany: *rzeczywista* odpowiedź jest sprawdzana jako *przebieg lub* *niepowodzenie* na podstawie *oczekiwanej* odpowiedzi.
1. Proces jest kontynuowany do czasu wykonania wszystkich testów.
1. Wyniki badań są zgłaszane.

Zazwyczaj testowy host internetowy jest skonfigurowany inaczej niż normalny host internetowy aplikacji dla przebiegów testowych. Na przykład do testów może być używana inna baza danych lub inne ustawienia aplikacji.

Składniki infrastruktury, takie jak testowy host sieci web i serwer testowy w pamięci[(TestServer),](/dotnet/api/microsoft.aspnetcore.testhost.testserver)są dostarczane lub zarządzane przez pakiet [Microsoft.AspNetCore.Mvc.Testing.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) Użycie tego pakietu usprawnia tworzenie i wykonywanie testów.

Pakiet `Microsoft.AspNetCore.Mvc.Testing` obsługuje następujące zadania:

* Kopiuje plik zależności (*.deps*) z SUT do katalogu *bin* projektu testowego.
* Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) w katalogu głównym projektu SUT, tak aby pliki statyczne i strony/widoki były znajdowy podczas wykonywania testów.
* Zapewnia [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) klasy usprawnić boottrapping `TestServer`SUT z .

Dokumentacja [testów jednostkowych](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) opisuje sposób konfigurowania projektu testowego i programu testowego, a także szczegółowe instrukcje dotyczące uruchamiania testów i zaleceń dotyczących sposobu nazywania testów i klas testowych.

> [!NOTE]
> Podczas tworzenia projektu testowego dla aplikacji, oddzielić testy jednostkowe od testów integracji do różnych projektów. Pomaga to zapewnić, że składniki testowania infrastruktury nie są przypadkowo uwzględnione w testach jednostkowych. Separacja jednostek i testy integracji umożliwia również kontrolę nad tym, który zestaw testów są uruchamiane.

Praktycznie nie ma różnicy między konfiguracją testów aplikacji Razor Pages i aplikacji MVC. Jedyną różnicą jest w jaki sposób testy są nazwane. W aplikacji Razor Pages testy punktów końcowych strony są zwykle nazwane po `IndexPageTests` klasie modelu strony (na przykład w celu przetestowania integracji składników dla strony Indeks). W aplikacji MVC testy są zwykle organizowane przez klasy kontrolerów i `HomeControllerTests` nazwane po testach kontrolerów (na przykład w celu przetestowania integracji składników dla kontrolera macierzystego).

## <a name="test-app-prerequisites"></a>Wymagania wstępne aplikacji testowej

Projekt testowy musi:

* Odwołanie się do pakietu [Microsoft.AspNetCore.Mvc.Testing.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)
* Określ składnik Web SDK`<Project Sdk="Microsoft.NET.Sdk.Web">`w pliku projektu ( ).

Te wymagania wstępne można zobaczyć w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/). Sprawdź *plik tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj.* Przykładowa aplikacja używa struktury testów [xUnit](https://xunit.github.io/) i biblioteki analizatora [AngleSharp,](https://anglesharp.github.io/) więc przykładowa aplikacja odwołuje się również do:

* [Xunit](https://www.nuget.org/packages/xunit)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [AngleSharp (Kształt kątowy)](https://www.nuget.org/packages/AngleSharp)

Entity Framework Core jest również używany w testach. Aplikacja odwołuje się do:

* [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [Microsoft.AspNetCore.Identity.EntityFrameCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [Microsoft.EntityFrameCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [Microsoft.EntityFrameWorkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [Narzędzie Microsoft.EntityFrameCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a>Środowisko SUT

Jeśli [środowisko](xref:fundamentals/environments) SUT nie jest ustawiona, środowisko domyślnie rozwoju.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Podstawowe testy z domyślną usługą WebApplicationFactory

[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) służy do tworzenia [Serwera TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) dla testów integracji. `TEntryPoint`jest klasą punktu wejścia SUT, `Startup` zwykle klasą.

Klasy testowe implementują interfejs *urządzenia klasy* [(IClassFixture),](https://xunit.github.io/docs/shared-context#class-fixture)aby wskazać, że klasa zawiera testy i zapewnić wystąpienia obiektów udostępnionych w testach w klasie.

Następująca klasa `BasicTests`testu, , `WebApplicationFactory` używa do bootstrap SUT i zapewnić [HttpClient](/dotnet/api/system.net.http.httpclient) do metody testowej, `Get_EndpointsReturnSuccessAndCorrectContentType`. Metoda sprawdza, czy kod stanu odpowiedzi jest pomyślny (kody stanu w zakresie `Content-Type` 200-299) i nagłówek jest `text/html; charset=utf-8` dla kilku stron aplikacji.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) tworzy wystąpienie, `HttpClient` które automatycznie następuje przekierowuje i obsługuje pliki cookie.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Domyślnie pliki cookie inne niż podstawowe nie są zachowywane w przypadku żądań po włączeniu [zasad zgody RODO.](xref:security/gdpr) Aby zachować nieistotne pliki cookie, takie jak te używane przez dostawcę TempData, oznacz je jako niezbędne w testach. Aby uzyskać instrukcje dotyczące oznaczania pliku cookie jako niezbędnego, zobacz [Niezbędne pliki cookie](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Dostosowywanie witryny WebApplicationFactory

Konfigurację hosta sieci Web można tworzyć niezależnie `WebApplicationFactory` od klas testowych, dziedzicząc po utworzeniu jednej lub więcej niestandardowych fabryk:

1. Dziedzicz `WebApplicationFactory` i [zastępuj configureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) umożliwia konfigurację kolekcji usług z [ConfigureServices:](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   Wysiew bazy danych w `InitializeDbForTests` [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) jest wykonywana przez metodę. Metoda jest opisana w [przykładzie testy integracji: Test organizacji aplikacji](#test-app-organization) sekcji.

   Kontekst bazy danych SUT jest `Startup.ConfigureServices` zarejestrowany w swojej metodzie. Wywołania zwrotnego `builder.ConfigureServices` aplikacji testowej jest wykonywany `Startup.ConfigureServices` *po* wykonaniu kodu aplikacji. Zlecenie wykonania jest przełomową zmianą dla [hosta ogólnego](xref:fundamentals/host/generic-host) wraz z wydaniem ASP.NET Core 3.0. Aby użyć innej bazy danych do testów niż baza danych aplikacji, kontekst `builder.ConfigureServices`bazy danych aplikacji musi zostać zastąpiony w pliku .

   Przykładowa aplikacja znajduje deskryptor usługi dla kontekstu bazy danych i używa deskryptora do usunięcia rejestracji usługi. Następnie fabryka dodaje `ApplicationDbContext` nowy, który używa bazy danych w pamięci do testów.

   Aby połączyć się z inną bazą danych `UseInMemoryDatabase` niż baza danych w pamięci, zmień wywołanie, aby połączyć kontekst z inną bazą danych. Aby użyć bazy danych testów programu SQL Server:

   * Odwołanie się do pakietu [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet w pliku projektu.
   * Wywołanie `UseSqlServer` z ciągiem połączenia do bazy danych.

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. Użyj niestandardowe `CustomWebApplicationFactory` w klasach testowych. W poniższym przykładzie użyto fabryki w `IndexPageTests` klasie:

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Klient przykładowej aplikacji jest skonfigurowany `HttpClient` tak, aby zapobiec następującym przekierowywaniom. Jak wyjaśniono w dalszej części [mock uwierzytelniania](#mock-authentication) sekcji, to pozwala testów, aby sprawdzić wynik pierwszej odpowiedzi aplikacji. Pierwsza odpowiedź jest przekierowanie w wielu `Location` z tych testów z nagłówkiem.

3. Typowy test używa `HttpClient` i pomocnika metody do przetwarzania żądania i odpowiedzi:

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Każde żądanie POST do SUT musi spełniać kontrolę antyforgery, która jest automatycznie dokonywana przez [system ochrony danych aplikacji.](xref:security/data-protection/introduction) Aby przygotować żądanie POST testu, aplikacja testowa musi:

1. Złożyć wniosek o stronę.
1. Przesiemaj plik cookie antyforgery i żądaj tokenu sprawdzania poprawności z odpowiedzi.
1. Złóż żądanie POST za pomocą pliku cookie antyforgery i zażądaj tokenu sprawdzania poprawności.

`SendAsync` Metody rozszerzenia pomocnika *(Helpers/HttpClientExtensions.cs)* `GetDocumentAsync` i metoda pomocnika *(Helpers/HtmlHelpers.cs)* w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) używają analizatora [AngleSharp](https://anglesharp.github.io/) do obsługi sprawdzania antyforgery za pomocą następujących metod:

* `GetDocumentAsync`&ndash; Odbiera [httpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) i `IHtmlDocument`zwraca . `GetDocumentAsync`wykorzystuje fabrykę, która przygotowuje *wirtualną* odpowiedź `HttpResponseMessage`na podstawie oryginalnego pliku . Aby uzyskać więcej informacji, zobacz [dokumentację AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync`metody rozszerzenia `HttpClient` do komponowania [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) i wywołać [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) do przesyłania wniosków do SUT. Przeciążenia `SendAsync` dla zaakceptowania`IHtmlFormElement`formularza HTML ( ) i następujące:
  * Przycisk Prześlij`IHtmlElement`formularz ( )
  * Zbieranie wartości`IEnumerable<KeyValuePair<string, string>>`formularza ( )
  * Przycisk Prześlij`IHtmlElement`(`IEnumerable<KeyValuePair<string, string>>`) i wartości formularza ( )

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) to biblioteka analizowania innej firmy używana do celów demonstracyjnych w tym temacie i przykładowej aplikacji. AngleSharp nie jest obsługiwany lub wymagane do testowania integracji aplikacji ASP.NET Core. Można użyć innych analizatorów, takich jak [Pakiet Agility Pack (HAP) html.](https://html-agility-pack.net/) Innym podejściem jest pisanie kodu do obsługi tokenu weryfikacji żądania systemu antyforgery i pliku cookie antyforgery bezpośrednio.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Dostosowywanie klienta za pomocą usługi WithWebHostBuilder

Gdy wymagana jest dodatkowa konfiguracja w ramach metody testowej, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) tworzy nowy `WebApplicationFactory` z [IWebHostBuilder,](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) który jest dalej dostosowywany przez konfigurację.

Metoda `Post_DeleteMessageHandler_ReturnsRedirectToRoot` testowa [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) pokazuje `WithWebHostBuilder`użycie . Ten test wykonuje usunięcie rekordu w bazie danych przez wyzwolenie przesyłania formularza w SUT.

Ponieważ inny test `IndexPageTests` w klasie wykonuje operację, która usuwa wszystkie rekordy w `Post_DeleteMessageHandler_ReturnsRedirectToRoot` bazie danych i może działać przed metodą, baza danych jest ponownie wysiewane w tej metodzie testowej, aby upewnić się, że rekord jest obecny dla SUT do usunięcia. Wybranie pierwszego przycisku `messages` usuwania formularza w SUT jest symulowane w żądaniu do SUT:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Opcje klienta

W poniższej tabeli przedstawiono domyślne [opcje WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) dostępne podczas tworzenia `HttpClient` wystąpień.

| Opcja | Opis | Domyślne |
| ------ | ----------- | ------- |
| [Allowautoredirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Pobiera lub ustawia, `HttpClient` czy wystąpienia powinny automatycznie śledzić odpowiedzi przekierowania. | `true` |
| [Baseaddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Pobiera lub ustawia adres `HttpClient` podstawowy wystąpień. | `http://localhost` |
| [HandleCookies (Uchwyty)](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Pobiera lub `HttpClient` ustawia, czy wystąpienia powinny obsługiwać pliki cookie. | `true` |
| [MaxAutomaticRedirections (MaxAutomaticRedirections)](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Pobiera lub ustawia maksymalną liczbę `HttpClient` odpowiedzi przekierowania, które powinny być obserwowane przez wystąpienia. | 7 |

Utwórz `WebApplicationFactoryClientOptions` klasę i przekaż ją do [metody CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (wartości domyślne są wyświetlane w przykładzie kodu):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Wstrzyknąć makiety usług

Usługi można zastąpić w teście z wywołaniem [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) w konstruktorze hosta. **Aby wstrzyknąć makiety usług, SUT musi mieć `Startup` klasę z `Startup.ConfigureServices` metodą.**

Przykładowy SUT zawiera usługę o określonym zakresie, która zwraca ofertę. Oferta jest osadzona w ukrytym polu na stronie Indeks, gdy żądana jest strona Indeks.

*Usługi/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Usługi/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs:*

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Strony/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Strony/Index.cs*:

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

Podczas uruchamiania aplikacji SUT jest generowany następujący znacznik:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Aby przetestować wtrysku usługi i cytatu w teście integracji, makieta usługi jest wstrzykiwany do SUT przez test. Makieta usługi zastępuje aplikację `QuoteService` usługą świadczoną przez aplikację `TestQuoteService`testową o nazwie:

*IntegrationTests.IndexPageTests.cs:*

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices`jest wywoływana, a usługa o określonym zakresie jest zarejestrowana:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Znaczników wyprodukowanych podczas wykonywania testu odzwierciedla tekst cytatu dostarczony przez `TestQuoteService`, w ten sposób twierdzenie przechodzi:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Makieta uwierzytelniania

Testy w `AuthTests` klasie sprawdzić, czy bezpieczny punkt końcowy:

* Przekierowuje nieuwierzytytego użytkownika do strony logowania aplikacji.
* Zwraca zawartość uwierzytelnionego użytkownika.

W SUT `/SecurePage` strona używa [authorizepage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) konwencji do zastosowania [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) do strony. Aby uzyskać więcej informacji, zobacz [Konwencje autoryzacji stron razor](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

W `Get_SecurePageRedirectsAnUnauthenticatedUser` teście [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) jest ustawiona na niezezwolenie na `false`przekierowania, ustawiając [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) na:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

Nie zezwalając klientowi na podążanie za przekierowaniem, można dokonać następujących kontroli:

* Kod stanu zwrócony przez SUT można sprawdzić względem oczekiwanego wyniku [HttpStatusCode.Redirect,](/dotnet/api/system.net.httpstatuscode) a nie ostatecznego kodu stanu po przekierowaniu do strony logowania, która byłaby [httpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).
* Wartość `Location` nagłówka w nagłówkach odpowiedzi jest sprawdzana, `http://localhost/Identity/Account/Login`aby potwierdzić, że zaczyna `Location` się od , a nie ostatecznej odpowiedzi strony logowania, gdzie nagłówek nie będzie obecny.

Aplikacja testowa może <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> nawlec w [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) w celu przetestowania aspektów uwierzytelniania i autoryzacji. Minimalny scenariusz zwraca [AuthenticateResult.Success:](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

Wywoływana `TestAuthHandler` jest uwierzytelnianie użytkownika, gdy schemat `Test` uwierzytelniania jest ustawiony na miejsce, w którym `AddAuthentication` jest zarejestrowana dla: `ConfigureTestServices`

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Aby uzyskać `WebApplicationFactoryClientOptions`więcej informacji na temat , zobacz opcje [klienta](#client-options) sekcji.

## <a name="set-the-environment"></a>Ustawianie środowiska

Domyślnie środowisko hosta i aplikacji SUT jest skonfigurowane do używania środowiska programistycznego. Aby zastąpić środowisko SUT:

* Ustaw `ASPNETCORE_ENVIRONMENT` zmienną środowiskową `Staging`(na przykład , `Production`lub `Testing`inną wartość niestandardową, taką jak ).
* Zastąd w `CreateHostBuilder` aplikacji testowej należy odczytać `ASPNETCORE`zmienne środowiskowe poprzedzone programem .

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Jak infrastruktura testowa wywnioskować ścieżkę katalogu głównego zawartości aplikacji

`WebApplicationFactory` Konstruktor wnioskuje ścieżkę [katalogu głównego zawartości](xref:fundamentals/index#content-root) aplikacji, wyszukując [webapplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) w zestawie `TEntryPoint` zawierającym testy integracji z kluczem równym zestawowi `System.Reflection.Assembly.FullName`. W przypadku, gdy atrybut z poprawnym `WebApplicationFactory` kluczem nie zostanie znaleziony, powraca do wyszukiwania pliku rozwiązania `TEntryPoint` (*.sln*) i dołącza nazwę zestawu do katalogu rozwiązania. Katalog główny aplikacji (ścieżka główna zawartości) służy do odnajdowania widoków i plików zawartości.

## <a name="disable-shadow-copying"></a>Wyłącz kopiowanie w tle

Kopiowanie w tle powoduje, że testy są wykonywane w innym katalogu niż katalog wyjściowy. Aby testy działały poprawnie, kopiowanie w tle musi być wyłączone. [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) używa xUnit i wyłącza kopiowanie w tle dla xUnit przez dołączenie pliku *xunit.runner.json* z poprawnym ustawieniem konfiguracji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie jednostki xUnit z JSON](https://xunit.github.io/docs/configuring-with-json.html).

Dodaj plik *xunit.runner.json* do katalogu głównego projektu testowego z następującą zawartością:

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>Utylizacja przedmiotów

Po wykonaniu testów `IClassFixture` implementacji, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) i [HttpClient](/dotnet/api/system.net.http.httpclient) są usuwane, gdy xUnit usuwa [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1). Jeśli obiekty tworzone przez dewelopera wymagają utylizacji, `IClassFixture` zutylizuj je w implementacji. Aby uzyskać więcej informacji, zobacz [Implementowanie dispose metody](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Przykład testów integracyjnych

[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) składa się z dwóch aplikacji:

| Aplikacja | Katalog projektu | Opis |
| --- | ----------------- | ----------- |
| Aplikacja wiadomości (SUT) | *src/RazorPagesProject* | Umożliwia użytkownikowi dodawanie, usuwanie jednego, usuwanie wszystkich i analizowanie wiadomości. |
| Aplikacja testowa | *testy/RazorPagesProject.Testy* | Służy do integracji test SUT. |

Testy można uruchomić przy użyciu wbudowanych funkcji testowych IDE, takich jak [Visual Studio.](https://visualstudio.microsoft.com) Jeśli używasz [programu Visual Studio Code](https://code.visualstudio.com/) lub wiersza polecenia, wykonaj następujące polecenie w wierszu polecenia w katalogu *tests/RazorPagesProject.Tests:*

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>Organizacja aplikacji wiadomości (SUT)

SUT to system wiadomości Razor Pages o następujących cechach:

* Strona Indeks aplikacji *(Pages/Index.cshtml* i *Pages/Index.cshtml.cs)* udostępnia metody interfejsu użytkownika i modelu strony do kontrolowania dodawania, usuwania i analizy wiadomości (średnie słowa na wiadomość).
* Komunikat jest opisany przez `Message` klasę *(Data/Message.cs)* z `Id` dwiema właściwościami: (key) i `Text` (message). Obiekt `Text` jest wymagany i ograniczony do 200 znaków.
* Wiadomości są przechowywane przy użyciu [bazy danych w pamięci encji](/ef/core/providers/in-memory/)&#8224;.
* Aplikacja zawiera warstwę dostępu do danych (DAL) `AppDbContext` w klasie kontekstu bazy danych (*Data/AppDbContext.cs*).
* Jeśli baza danych jest pusta podczas uruchamiania aplikacji, magazyn wiadomości jest inicjowany z trzema wiadomościami.
* Aplikacja `/SecurePage` zawiera, który jest dostępny tylko przez uwierzytelnionego użytkownika.

&#8224;temat EF, [Test z InMemory](/ef/core/miscellaneous/testing/in-memory), wyjaśnia, jak używać bazy danych w pamięci do testów z MSTest. W tym temacie użyto struktury testów [xUnit.](https://xunit.github.io/) Pojęcia testowe i implementacje testów w różnych strukturach testów są podobne, ale nie identyczne.

Chociaż aplikacja nie używa wzorca repozytorium i nie jest skutecznym przykładem [wzorca Jednostki Pracy (UoW),](https://martinfowler.com/eaaCatalog/unitOfWork.html)Razor Pages obsługuje te wzorce rozwoju. Aby uzyskać więcej informacji, zobacz [Projektowanie warstwy trwałości infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) i [logiki kontrolera testów](/aspnet/core/mvc/controllers/testing) (przykład implementuje wzorzec repozytorium).

### <a name="test-app-organization"></a>Testowanie organizacji aplikacji

Aplikacja testowa jest aplikacją konsoli wewnątrz *katalogu tests/RazorPagesProject.Tests.*

| Katalog aplikacji testowych | Opis |
| ------------------ | ----------- |
| *Testy Auth* | Zawiera metody badań dla:<ul><li>Uzyskiwanie dostępu do bezpiecznej strony przez nieuwierzytynego użytkownika.</li><li>Uzyskiwanie dostępu do bezpiecznej strony przez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>uwierzytelnionego użytkownika za pomocą makiety .</li><li>Uzyskanie profilu użytkownika Usługi GitHub i sprawdzenie logowania użytkownika profilu.</li></ul> |
| *Testy podstawowe* | Zawiera metodę testową dla routingu i typu zawartości. |
| *Testy integracji* | Zawiera testy integracji dla strony `WebApplicationFactory` Indeks przy użyciu klasy niestandardowej. |
| *Pomocnicy/narzędzia* | <ul><li>*Utilities.cs* zawiera `InitializeDbForTests` metodę używaną do wysiewu bazy danych z danymi testowymi.</li><li>*HtmlHelpers.cs* zapewnia metodę zwracania AngleSharp `IHtmlDocument` do użycia przez metody testowe.</li><li>*HttpClientExtensions.cs* zapewniają przeciążenia w `SendAsync` celu składania wniosków do SUT.</li></ul> |

Struktura testowa to [xUnit](https://xunit.github.io/). Testy integracji są przeprowadzane przy użyciu [pliku Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), który zawiera [serwer testowy.](/dotnet/api/microsoft.aspnetcore.testhost.testserver) Ponieważ pakiet [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) służy do konfigurowania hosta testowego i serwera testowego, `TestHost` pakiety i `TestServer` pakiety nie wymagają bezpośrednich odwołań do pakietu w pliku projektu aplikacji testowej lub konfiguracji dewelopera w aplikacji testowej.

**Wysiew bazy danych do testowania**

Testy integracji zwykle wymagają małego zestawu danych w bazie danych przed wykonaniem testu. Na przykład test usuwania wymaga usunięcia rekordu bazy danych, więc baza danych musi mieć co najmniej jeden rekord dla żądania usunięcia, aby zakończyć się pomyślnie.

Przykładowa aplikacja nasion bazy danych z trzech komunikatów w *Utilities.cs,* które testy mogą używać podczas wykonywania:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

Kontekst bazy danych SUT jest `Startup.ConfigureServices` zarejestrowany w swojej metodzie. Wywołania zwrotnego `builder.ConfigureServices` aplikacji testowej jest wykonywany `Startup.ConfigureServices` *po* wykonaniu kodu aplikacji. Aby użyć innej bazy danych do testów, kontekst bazy `builder.ConfigureServices`danych aplikacji musi zostać zastąpiony w pliku . Aby uzyskać więcej informacji, zobacz [dostosowywanie WebApplicationFactory](#customize-webapplicationfactory) sekcji.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Testy integracji upewnij się, że składniki aplikacji działają poprawnie na poziomie, który zawiera infrastrukturę pomocniczą aplikacji, takich jak baza danych, system plików i sieci. ASP.NET Core obsługuje testy integracji przy użyciu struktury testów jednostkowych z testowym hostem internetowym i serwerem testowym w pamięci.

W tym temacie przyjęto podstawową wiedzę na temat testów jednostkowych. Jeśli nie znasz pojęć testowych, zobacz [testowanie jednostek w temacie .NET Core i .NET Standard](/dotnet/core/testing/) i jego połączonej zawartości.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykładowa aplikacja to aplikacja Razor Pages i zakłada podstawową wiedzę na temat stron Razor. Jeśli nie znasz stron Razor, zobacz następujące tematy:

* [Wprowadzenie do produktu Razor Pages](xref:razor-pages/index)
* [Wprowadzenie do korzystania ze stron Razor](xref:tutorials/razor-pages/razor-pages-start)
* [Testy jednostkowe stron Razor](xref:test/razor-pages-tests)

> [!NOTE]
> Do testowania OSO, poleciliśmy narzędzie, takie jak [Selen](https://www.seleniumhq.org/), które można zautomatyzować przeglądarki.

## <a name="introduction-to-integration-tests"></a>Wprowadzenie do testów integracyjnych

Testy integracji oceniają składniki aplikacji na szerszym poziomie niż [testy jednostkowe.](/dotnet/core/testing/) Testy jednostkowe są używane do testowania izolowanych składników oprogramowania, takich jak metody poszczególnych klas. Testy integracji potwierdzają, że dwa lub więcej składników aplikacji współpracują ze sobą, aby uzyskać oczekiwany wynik, ewentualnie w tym każdy składnik wymagany do pełnego przetworzenia żądania.

Te szersze testy są używane do testowania infrastruktury aplikacji i całej struktury, często w tym następujących składników:

* baza danych
* System plików
* Urządzenia sieciowe
* Potok żądania odpowiedzi

Testy jednostkowe używają sfabrykowanych komponentów, znanych jako *podróbki* lub *makiety obiektów,* zamiast składników infrastruktury.

W przeciwieństwie do testów jednostkowych, testy integracyjne:

* Użyj rzeczywistych składników, które aplikacja używa w produkcji.
* Wymagaj więcej kodu i przetwarzania danych.
* Trwać dłużej, aby uruchomić.

W związku z tym ograniczyć użycie testów integracji do najważniejszych scenariuszy infrastruktury. Jeśli zachowanie można przetestować przy użyciu testu jednostkowego lub testu integracji, wybierz test jednostkowy.

> [!TIP]
> Nie zapisuj testów integracji dla każdej możliwej permutacji danych i dostępu do plików za pomocą baz danych i systemów plików. Niezależnie od tego, ile miejsc w aplikacji wchodzi w interakcję z bazami danych i systemami plików, skoncentrowany zestaw testów integracji odczytu, zapisu, aktualizacji i usuwania jest zwykle w stanie odpowiednio przetestować składniki bazy danych i systemu plików. Użyj testów jednostkowych dla rutynowych testów logiki metody, które współdziałają z tymi składnikami. W testach jednostkowych użycie podróbek/makiet infrastruktury powoduje szybsze wykonywanie testów.

> [!NOTE]
> W dyskusjach na temat testów integracyjnych testowany projekt jest często nazywany *testowanym systemem*lub w skrócie "SUT".
>
> *"SUT" jest używany w tym temacie, aby odwołać się do testowanych aplikacji ASP.NET Core.*

## <a name="aspnet-core-integration-tests"></a>ASP.NET Podstawowe testy integracji

Testy integracji w ASP.NET Core wymagają następujących czynności:

* Projekt testowy jest używany do przechowywania i wykonywania testów. Projekt testowy ma odniesienie do SUT.
* Projekt testowy tworzy testowy host internetowy dla SUT i używa klienta serwera testowego do obsługi żądań i odpowiedzi z SUT.
* Próbkowania jest używany do wykonywania testów i raport wyników testów.

Testy integracji wykonaj sekwencję zdarzeń, które obejmują zwykłe kroki testu *Arrange*, *Act*i *Assert:*

1. Host internetowy SUT jest skonfigurowany.
1. Klient serwera testowego jest tworzony do przesyłania żądań do aplikacji.
1. Krok testu *Rozmieszczanie* jest wykonywany: aplikacja testowa przygotowuje żądanie.
1. Krok testu *act* jest wykonywany: klient przesyła żądanie i odbiera odpowiedź.
1. Krok testu *Assert* jest wykonywany: *rzeczywista* odpowiedź jest sprawdzana jako *przebieg lub* *niepowodzenie* na podstawie *oczekiwanej* odpowiedzi.
1. Proces jest kontynuowany do czasu wykonania wszystkich testów.
1. Wyniki badań są zgłaszane.

Zazwyczaj testowy host internetowy jest skonfigurowany inaczej niż normalny host internetowy aplikacji dla przebiegów testowych. Na przykład do testów może być używana inna baza danych lub inne ustawienia aplikacji.

Składniki infrastruktury, takie jak testowy host sieci web i serwer testowy w pamięci[(TestServer),](/dotnet/api/microsoft.aspnetcore.testhost.testserver)są dostarczane lub zarządzane przez pakiet [Microsoft.AspNetCore.Mvc.Testing.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) Użycie tego pakietu usprawnia tworzenie i wykonywanie testów.

Pakiet `Microsoft.AspNetCore.Mvc.Testing` obsługuje następujące zadania:

* Kopiuje plik zależności (*.deps*) z SUT do katalogu *bin* projektu testowego.
* Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) w katalogu głównym projektu SUT, tak aby pliki statyczne i strony/widoki były znajdowy podczas wykonywania testów.
* Zapewnia [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) klasy usprawnić boottrapping `TestServer`SUT z .

Dokumentacja [testów jednostkowych](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) opisuje sposób konfigurowania projektu testowego i programu testowego, a także szczegółowe instrukcje dotyczące uruchamiania testów i zaleceń dotyczących sposobu nazywania testów i klas testowych.

> [!NOTE]
> Podczas tworzenia projektu testowego dla aplikacji, oddzielić testy jednostkowe od testów integracji do różnych projektów. Pomaga to zapewnić, że składniki testowania infrastruktury nie są przypadkowo uwzględnione w testach jednostkowych. Separacja jednostek i testy integracji umożliwia również kontrolę nad tym, który zestaw testów są uruchamiane.

Praktycznie nie ma różnicy między konfiguracją testów aplikacji Razor Pages i aplikacji MVC. Jedyną różnicą jest w jaki sposób testy są nazwane. W aplikacji Razor Pages testy punktów końcowych strony są zwykle nazwane po `IndexPageTests` klasie modelu strony (na przykład w celu przetestowania integracji składników dla strony Indeks). W aplikacji MVC testy są zwykle organizowane przez klasy kontrolerów i `HomeControllerTests` nazwane po testach kontrolerów (na przykład w celu przetestowania integracji składników dla kontrolera macierzystego).

## <a name="test-app-prerequisites"></a>Wymagania wstępne aplikacji testowej

Projekt testowy musi:

* Odwołaj się do następujących pakietów:
  * [Aplikacja Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft.AspNetCore.Mvc.Testowanie](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* Określ składnik Web SDK`<Project Sdk="Microsoft.NET.Sdk.Web">`w pliku projektu ( ). Pakiet Web SDK jest wymagany podczas odwoływania się do [metapakietu Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Te wymagania wstępne można zobaczyć w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/). Sprawdź *plik tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj.* Przykładowa aplikacja używa struktury testów [xUnit](https://xunit.github.io/) i biblioteki analizatora [AngleSharp,](https://anglesharp.github.io/) więc przykładowa aplikacja odwołuje się również do:

* [Xunit](https://www.nuget.org/packages/xunit/)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp (Kształt kątowy)](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>Środowisko SUT

Jeśli [środowisko](xref:fundamentals/environments) SUT nie jest ustawiona, środowisko domyślnie rozwoju.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Podstawowe testy z domyślną usługą WebApplicationFactory

[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) służy do tworzenia [Serwera TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) dla testów integracji. `TEntryPoint`jest klasą punktu wejścia SUT, `Startup` zwykle klasą.

Klasy testowe implementują interfejs *urządzenia klasy* [(IClassFixture),](https://xunit.github.io/docs/shared-context#class-fixture)aby wskazać, że klasa zawiera testy i zapewnić wystąpienia obiektów udostępnionych w testach w klasie.

Następująca klasa `BasicTests`testu, , `WebApplicationFactory` używa do bootstrap SUT i zapewnić [HttpClient](/dotnet/api/system.net.http.httpclient) do metody testowej, `Get_EndpointsReturnSuccessAndCorrectContentType`. Metoda sprawdza, czy kod stanu odpowiedzi jest pomyślny (kody stanu w zakresie `Content-Type` 200-299) i nagłówek jest `text/html; charset=utf-8` dla kilku stron aplikacji.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) tworzy wystąpienie, `HttpClient` które automatycznie następuje przekierowuje i obsługuje pliki cookie.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Domyślnie pliki cookie inne niż podstawowe nie są zachowywane w przypadku żądań po włączeniu [zasad zgody RODO.](xref:security/gdpr) Aby zachować nieistotne pliki cookie, takie jak te używane przez dostawcę TempData, oznacz je jako niezbędne w testach. Aby uzyskać instrukcje dotyczące oznaczania pliku cookie jako niezbędnego, zobacz [Niezbędne pliki cookie](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Dostosowywanie witryny WebApplicationFactory

Konfigurację hosta sieci Web można tworzyć niezależnie `WebApplicationFactory` od klas testowych, dziedzicząc po utworzeniu jednej lub więcej niestandardowych fabryk:

1. Dziedzicz `WebApplicationFactory` i [zastępuj configureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) umożliwia konfigurację kolekcji usług z [ConfigureServices:](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   Wysiew bazy danych w `InitializeDbForTests` [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) jest wykonywana przez metodę. Metoda jest opisana w [przykładzie testy integracji: Test organizacji aplikacji](#test-app-organization) sekcji.

2. Użyj niestandardowe `CustomWebApplicationFactory` w klasach testowych. W poniższym przykładzie użyto fabryki w `IndexPageTests` klasie:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Klient przykładowej aplikacji jest skonfigurowany `HttpClient` tak, aby zapobiec następującym przekierowywaniom. Jak wyjaśniono w dalszej części [mock uwierzytelniania](#mock-authentication) sekcji, to pozwala testów, aby sprawdzić wynik pierwszej odpowiedzi aplikacji. Pierwsza odpowiedź jest przekierowanie w wielu `Location` z tych testów z nagłówkiem.

3. Typowy test używa `HttpClient` i pomocnika metody do przetwarzania żądania i odpowiedzi:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Każde żądanie POST do SUT musi spełniać kontrolę antyforgery, która jest automatycznie dokonywana przez [system ochrony danych aplikacji.](xref:security/data-protection/introduction) Aby przygotować żądanie POST testu, aplikacja testowa musi:

1. Złożyć wniosek o stronę.
1. Przesiemaj plik cookie antyforgery i żądaj tokenu sprawdzania poprawności z odpowiedzi.
1. Złóż żądanie POST za pomocą pliku cookie antyforgery i zażądaj tokenu sprawdzania poprawności.

`SendAsync` Metody rozszerzenia pomocnika *(Helpers/HttpClientExtensions.cs)* `GetDocumentAsync` i metoda pomocnika *(Helpers/HtmlHelpers.cs)* w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) używają analizatora [AngleSharp](https://anglesharp.github.io/) do obsługi sprawdzania antyforgery za pomocą następujących metod:

* `GetDocumentAsync`&ndash; Odbiera [httpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) i `IHtmlDocument`zwraca . `GetDocumentAsync`wykorzystuje fabrykę, która przygotowuje *wirtualną* odpowiedź `HttpResponseMessage`na podstawie oryginalnego pliku . Aby uzyskać więcej informacji, zobacz [dokumentację AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync`metody rozszerzenia `HttpClient` do komponowania [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) i wywołać [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) do przesyłania wniosków do SUT. Przeciążenia `SendAsync` dla zaakceptowania`IHtmlFormElement`formularza HTML ( ) i następujące:
  * Przycisk Prześlij`IHtmlElement`formularz ( )
  * Zbieranie wartości`IEnumerable<KeyValuePair<string, string>>`formularza ( )
  * Przycisk Prześlij`IHtmlElement`(`IEnumerable<KeyValuePair<string, string>>`) i wartości formularza ( )

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) to biblioteka analizowania innej firmy używana do celów demonstracyjnych w tym temacie i przykładowej aplikacji. AngleSharp nie jest obsługiwany lub wymagane do testowania integracji aplikacji ASP.NET Core. Można użyć innych analizatorów, takich jak [Pakiet Agility Pack (HAP) html.](https://html-agility-pack.net/) Innym podejściem jest pisanie kodu do obsługi tokenu weryfikacji żądania systemu antyforgery i pliku cookie antyforgery bezpośrednio.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Dostosowywanie klienta za pomocą usługi WithWebHostBuilder

Gdy wymagana jest dodatkowa konfiguracja w ramach metody testowej, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) tworzy nowy `WebApplicationFactory` z [IWebHostBuilder,](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) który jest dalej dostosowywany przez konfigurację.

Metoda `Post_DeleteMessageHandler_ReturnsRedirectToRoot` testowa [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) pokazuje `WithWebHostBuilder`użycie . Ten test wykonuje usunięcie rekordu w bazie danych przez wyzwolenie przesyłania formularza w SUT.

Ponieważ inny test `IndexPageTests` w klasie wykonuje operację, która usuwa wszystkie rekordy w `Post_DeleteMessageHandler_ReturnsRedirectToRoot` bazie danych i może działać przed metodą, baza danych jest ponownie wysiewane w tej metodzie testowej, aby upewnić się, że rekord jest obecny dla SUT do usunięcia. Wybranie pierwszego przycisku `messages` usuwania formularza w SUT jest symulowane w żądaniu do SUT:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Opcje klienta

W poniższej tabeli przedstawiono domyślne [opcje WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) dostępne podczas tworzenia `HttpClient` wystąpień.

| Opcja | Opis | Domyślne |
| ------ | ----------- | ------- |
| [Allowautoredirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Pobiera lub ustawia, `HttpClient` czy wystąpienia powinny automatycznie śledzić odpowiedzi przekierowania. | `true` |
| [Baseaddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Pobiera lub ustawia adres `HttpClient` podstawowy wystąpień. | `http://localhost` |
| [HandleCookies (Uchwyty)](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Pobiera lub `HttpClient` ustawia, czy wystąpienia powinny obsługiwać pliki cookie. | `true` |
| [MaxAutomaticRedirections (MaxAutomaticRedirections)](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Pobiera lub ustawia maksymalną liczbę `HttpClient` odpowiedzi przekierowania, które powinny być obserwowane przez wystąpienia. | 7 |

Utwórz `WebApplicationFactoryClientOptions` klasę i przekaż ją do [metody CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (wartości domyślne są wyświetlane w przykładzie kodu):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Wstrzyknąć makiety usług

Usługi można zastąpić w teście z wywołaniem [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) w konstruktorze hosta. **Aby wstrzyknąć makiety usług, SUT musi mieć `Startup` klasę z `Startup.ConfigureServices` metodą.**

Przykładowy SUT zawiera usługę o określonym zakresie, która zwraca ofertę. Oferta jest osadzona w ukrytym polu na stronie Indeks, gdy żądana jest strona Indeks.

*Usługi/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Usługi/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs:*

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Strony/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Strony/Index.cs*:

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

Podczas uruchamiania aplikacji SUT jest generowany następujący znacznik:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Aby przetestować wtrysku usługi i cytatu w teście integracji, makieta usługi jest wstrzykiwany do SUT przez test. Makieta usługi zastępuje aplikację `QuoteService` usługą świadczoną przez aplikację `TestQuoteService`testową o nazwie:

*IntegrationTests.IndexPageTests.cs:*

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices`jest wywoływana, a usługa o określonym zakresie jest zarejestrowana:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Znaczników wyprodukowanych podczas wykonywania testu odzwierciedla tekst cytatu dostarczony przez `TestQuoteService`, w ten sposób twierdzenie przechodzi:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Makieta uwierzytelniania

Testy w `AuthTests` klasie sprawdzić, czy bezpieczny punkt końcowy:

* Przekierowuje nieuwierzytytego użytkownika do strony logowania aplikacji.
* Zwraca zawartość uwierzytelnionego użytkownika.

W SUT `/SecurePage` strona używa [authorizepage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) konwencji do zastosowania [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) do strony. Aby uzyskać więcej informacji, zobacz [Konwencje autoryzacji stron razor](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

W `Get_SecurePageRedirectsAnUnauthenticatedUser` teście [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) jest ustawiona na niezezwolenie na `false`przekierowania, ustawiając [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) na:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

Nie zezwalając klientowi na podążanie za przekierowaniem, można dokonać następujących kontroli:

* Kod stanu zwrócony przez SUT można sprawdzić względem oczekiwanego wyniku [HttpStatusCode.Redirect,](/dotnet/api/system.net.httpstatuscode) a nie ostatecznego kodu stanu po przekierowaniu do strony logowania, która byłaby [httpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).
* Wartość `Location` nagłówka w nagłówkach odpowiedzi jest sprawdzana, `http://localhost/Identity/Account/Login`aby potwierdzić, że zaczyna `Location` się od , a nie ostatecznej odpowiedzi strony logowania, gdzie nagłówek nie będzie obecny.

Aplikacja testowa może <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> nawlec w [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) w celu przetestowania aspektów uwierzytelniania i autoryzacji. Minimalny scenariusz zwraca [AuthenticateResult.Success:](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

Wywoływana `TestAuthHandler` jest uwierzytelnianie użytkownika, gdy schemat `Test` uwierzytelniania jest ustawiony na miejsce, w którym `AddAuthentication` jest zarejestrowana dla: `ConfigureTestServices`

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Aby uzyskać `WebApplicationFactoryClientOptions`więcej informacji na temat , zobacz opcje [klienta](#client-options) sekcji.

## <a name="set-the-environment"></a>Ustawianie środowiska

Domyślnie środowisko hosta i aplikacji SUT jest skonfigurowane do używania środowiska programistycznego. Aby zastąpić środowisko SUT:

* Ustaw `ASPNETCORE_ENVIRONMENT` zmienną środowiskową `Staging`(na przykład , `Production`lub `Testing`inną wartość niestandardową, taką jak ).
* Zastąd w `CreateHostBuilder` aplikacji testowej należy odczytać `ASPNETCORE`zmienne środowiskowe poprzedzone programem .

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Jak infrastruktura testowa wywnioskować ścieżkę katalogu głównego zawartości aplikacji

`WebApplicationFactory` Konstruktor wnioskuje ścieżkę [katalogu głównego zawartości](xref:fundamentals/index#content-root) aplikacji, wyszukując [webapplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) w zestawie `TEntryPoint` zawierającym testy integracji z kluczem równym zestawowi `System.Reflection.Assembly.FullName`. W przypadku, gdy atrybut z poprawnym `WebApplicationFactory` kluczem nie zostanie znaleziony, powraca do wyszukiwania pliku rozwiązania `TEntryPoint` (*.sln*) i dołącza nazwę zestawu do katalogu rozwiązania. Katalog główny aplikacji (ścieżka główna zawartości) służy do odnajdowania widoków i plików zawartości.

## <a name="disable-shadow-copying"></a>Wyłącz kopiowanie w tle

Kopiowanie w tle powoduje, że testy są wykonywane w innym katalogu niż katalog wyjściowy. Aby testy działały poprawnie, kopiowanie w tle musi być wyłączone. [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) używa xUnit i wyłącza kopiowanie w tle dla xUnit przez dołączenie pliku *xunit.runner.json* z poprawnym ustawieniem konfiguracji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie jednostki xUnit z JSON](https://xunit.github.io/docs/configuring-with-json.html).

Dodaj plik *xunit.runner.json* do katalogu głównego projektu testowego z następującą zawartością:

```json
{
  "shadowCopy": false
}
```

Jeśli używasz programu Visual Studio, ustaw właściwość **Kopiuj do katalogu wyjściowego** na **Kopiuj zawsze**. Jeśli nie używasz programu `Content` Visual Studio, dodaj obiekt docelowy do pliku projektu aplikacji testowej:

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a>Utylizacja przedmiotów

Po wykonaniu testów `IClassFixture` implementacji, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) i [HttpClient](/dotnet/api/system.net.http.httpclient) są usuwane, gdy xUnit usuwa [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1). Jeśli obiekty tworzone przez dewelopera wymagają utylizacji, `IClassFixture` zutylizuj je w implementacji. Aby uzyskać więcej informacji, zobacz [Implementowanie dispose metody](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Przykład testów integracyjnych

[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) składa się z dwóch aplikacji:

| Aplikacja | Katalog projektu | Opis |
| --- | ----------------- | ----------- |
| Aplikacja wiadomości (SUT) | *src/RazorPagesProject* | Umożliwia użytkownikowi dodawanie, usuwanie jednego, usuwanie wszystkich i analizowanie wiadomości. |
| Aplikacja testowa | *testy/RazorPagesProject.Testy* | Służy do integracji test SUT. |

Testy można uruchomić przy użyciu wbudowanych funkcji testowych IDE, takich jak [Visual Studio.](https://visualstudio.microsoft.com) Jeśli używasz [programu Visual Studio Code](https://code.visualstudio.com/) lub wiersza polecenia, wykonaj następujące polecenie w wierszu polecenia w katalogu *tests/RazorPagesProject.Tests:*

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a>Organizacja aplikacji wiadomości (SUT)

SUT to system wiadomości Razor Pages o następujących cechach:

* Strona Indeks aplikacji *(Pages/Index.cshtml* i *Pages/Index.cshtml.cs)* udostępnia metody interfejsu użytkownika i modelu strony do kontrolowania dodawania, usuwania i analizy wiadomości (średnie słowa na wiadomość).
* Komunikat jest opisany przez `Message` klasę *(Data/Message.cs)* z `Id` dwiema właściwościami: (key) i `Text` (message). Obiekt `Text` jest wymagany i ograniczony do 200 znaków.
* Wiadomości są przechowywane przy użyciu [bazy danych w pamięci encji](/ef/core/providers/in-memory/)&#8224;.
* Aplikacja zawiera warstwę dostępu do danych (DAL) `AppDbContext` w klasie kontekstu bazy danych (*Data/AppDbContext.cs*).
* Jeśli baza danych jest pusta podczas uruchamiania aplikacji, magazyn wiadomości jest inicjowany z trzema wiadomościami.
* Aplikacja `/SecurePage` zawiera, który jest dostępny tylko przez uwierzytelnionego użytkownika.

&#8224;temat EF, [Test z InMemory](/ef/core/miscellaneous/testing/in-memory), wyjaśnia, jak używać bazy danych w pamięci do testów z MSTest. W tym temacie użyto struktury testów [xUnit.](https://xunit.github.io/) Pojęcia testowe i implementacje testów w różnych strukturach testów są podobne, ale nie identyczne.

Chociaż aplikacja nie używa wzorca repozytorium i nie jest skutecznym przykładem [wzorca Jednostki Pracy (UoW),](https://martinfowler.com/eaaCatalog/unitOfWork.html)Razor Pages obsługuje te wzorce rozwoju. Aby uzyskać więcej informacji, zobacz [Projektowanie warstwy trwałości infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) i [logiki kontrolera testów](/aspnet/core/mvc/controllers/testing) (przykład implementuje wzorzec repozytorium).

### <a name="test-app-organization"></a>Testowanie organizacji aplikacji

Aplikacja testowa jest aplikacją konsoli wewnątrz *katalogu tests/RazorPagesProject.Tests.*

| Katalog aplikacji testowych | Opis |
| ------------------ | ----------- |
| *Testy Auth* | Zawiera metody badań dla:<ul><li>Uzyskiwanie dostępu do bezpiecznej strony przez nieuwierzytynego użytkownika.</li><li>Uzyskiwanie dostępu do bezpiecznej strony przez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>uwierzytelnionego użytkownika za pomocą makiety .</li><li>Uzyskanie profilu użytkownika Usługi GitHub i sprawdzenie logowania użytkownika profilu.</li></ul> |
| *Testy podstawowe* | Zawiera metodę testową dla routingu i typu zawartości. |
| *Testy integracji* | Zawiera testy integracji dla strony `WebApplicationFactory` Indeks przy użyciu klasy niestandardowej. |
| *Pomocnicy/narzędzia* | <ul><li>*Utilities.cs* zawiera `InitializeDbForTests` metodę używaną do wysiewu bazy danych z danymi testowymi.</li><li>*HtmlHelpers.cs* zapewnia metodę zwracania AngleSharp `IHtmlDocument` do użycia przez metody testowe.</li><li>*HttpClientExtensions.cs* zapewniają przeciążenia w `SendAsync` celu składania wniosków do SUT.</li></ul> |

Struktura testowa to [xUnit](https://xunit.github.io/). Testy integracji są przeprowadzane przy użyciu [pliku Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), który zawiera [serwer testowy.](/dotnet/api/microsoft.aspnetcore.testhost.testserver) Ponieważ pakiet [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) służy do konfigurowania hosta testowego i serwera testowego, `TestHost` pakiety i `TestServer` pakiety nie wymagają bezpośrednich odwołań do pakietu w pliku projektu aplikacji testowej lub konfiguracji dewelopera w aplikacji testowej.

**Wysiew bazy danych do testowania**

Testy integracji zwykle wymagają małego zestawu danych w bazie danych przed wykonaniem testu. Na przykład test usuwania wymaga usunięcia rekordu bazy danych, więc baza danych musi mieć co najmniej jeden rekord dla żądania usunięcia, aby zakończyć się pomyślnie.

Przykładowa aplikacja nasion bazy danych z trzech komunikatów w *Utilities.cs,* które testy mogą używać podczas wykonywania:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Testy jednostkowe](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
