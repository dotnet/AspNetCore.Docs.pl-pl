---
title: ASP.NET modele hostingowe Core Blazor
author: guardrex
description: Zrozumieć Blazor WebAssembly Blazor i serwer hostingu modeli.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: 0dfc991f76acb227ce9ea27a07fbae50571f0117
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80471831"
---
# <a name="aspnet-core-opno-locblazor-hosting-models"></a>ASP.NET modele hostingowe Core Blazor

Przez [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazorto struktura sieci web przeznaczona do uruchamiania po stronie klienta w przeglądarce w czasie wykonywania .NET opartym na [webassembly](https://webassembly.org/)* Blazor (WebAssembly)* lub po stronie serwera w ASP.NET Core* Blazor (Server).* Niezależnie od modelu hostingu, modele aplikacji i komponentów *są takie same*.

Aby utworzyć projekt dla modeli hostingu opisanych <xref:blazor/get-started>w tym artykule, zobacz .

Aby uzyskać konfigurację zaawansowaną, zobacz <xref:blazor/hosting-model-configuration>.

## <a name="opno-locblazor-webassembly"></a>BlazorWebAssembly (WebAssembly)

Główny model hostingu dla Blazor jest uruchomiony po stronie klienta w przeglądarce na WebAssembly. Aplikacja, Blazor jej zależności i środowisko uruchomieniowe platformy .NET są pobierane do przeglądarki. Aplikacja jest wykonywana bezpośrednio w wątku interfejsu użytkownika przeglądarki. Aktualizacje interfejsu użytkownika i obsługa zdarzeń występują w ramach tego samego procesu. Zasoby aplikacji są wdrażane jako pliki statyczne na serwerze sieci web lub usłudze zdolnej do obsługi zawartości statycznej klientom.

![BlazorWebAssembly: Blazor aplikacja działa w wątku interfejsu użytkownika wewnątrz przeglądarki.](hosting-models/_static/blazor-webassembly.png)

Aby utworzyć Blazor aplikację przy użyciu modelu hostingu po stronie klienta, użyj szablonu ** Blazor aplikacji WebAssembly** ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).

Po wybraniu szablonu ** Blazor aplikacji WebAssembly** możesz skonfigurować aplikację do używania zaplecza ASP.NET Core, zaznaczając pole wyboru **hostowane ASP.NET Core** ([dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)). Aplikacja ASP.NET Core obsługuje aplikację Blazor dla klientów. Aplikacja Blazor WebAssembly może wchodzić w interakcje z serwerem za pośrednictwem sieci za pomocą wywołań internetowego interfejsu API lub [SignalR](xref:signalr/introduction) (<xref:tutorials/signalr-blazor-webassembly>).

Szablony obejmują `blazor.webassembly.js` skrypt, który obsługuje:

* Pobieranie środowiska uruchomieniowego platformy .NET, aplikacji i zależności aplikacji.
* Inicjowanie środowiska wykonawczego w celu uruchomienia aplikacji.

Model Blazor hostingu WebAssembly oferuje kilka korzyści:

* Nie ma zależności po stronie serwera .NET. Aplikacja działa w pełni po pobraniu do klienta.
* Zasoby i możliwości klienta są w pełni wykorzystywane.
* Praca jest odciążona z serwera do klienta.
* Serwer sieci web ASP.NET Core nie jest wymagany do hosta aplikacji. Możliwe są scenariusze wdrażania bez serwera (na przykład obsługa aplikacji z sieci CDN).

Istnieją wady hostingu Blazor WebAssembly:

* Aplikacja jest ograniczona do możliwości przeglądarki.
* Wymagany jest sprzęt i oprogramowanie klienta (na przykład obsługa webassembly).
* Rozmiar pobierania jest większy, a ładowanie aplikacji trwa dłużej.
* Obsługa środowiska uruchomieniowego i narzędzi .NET jest mniej dojrzała. Na przykład ograniczenia istnieją w [.NET Standardowa](/dotnet/standard/net-standard) obsługa i debugowanie.

Model Blazor aplikacji Hosted obsługuje [kontenery platformy Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index). Kliknij prawym przyciskiem myszy projekt Serwer w programie Visual Studio i wybierz pozycję **Dodaj** > **obsługę platformy Docker**.

## <a name="opno-locblazor-server"></a>BlazorSerwera

W Blazor modelu hostingu serwera aplikacja jest wykonywana na serwerze z poziomu aplikacji ASP.NET Core. Aktualizacje interfejsu użytkownika, obsługa zdarzeń i wywołania [SignalR](xref:signalr/introduction) JavaScript są obsługiwane przez połączenie.

![Przeglądarka współdziała z aplikacją (hostowana wewnątrz aplikacji ASP.NET Core) SignalR na serwerze za pośrednictwem połączenia.](hosting-models/_static/blazor-server.png)

Aby utworzyć Blazor aplikację Blazor przy użyciu modelu hostingu serwera, użyj szablonu ** Blazor aplikacji** ASP.NET Core Server ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)). Aplikacja ASP.NET Core obsługuje aplikację Blazor Serwer i SignalR tworzy punkt końcowy, z którym łączą się klienci.

Aplikacja ASP.NET Core odwołuje się do `Startup` klasy aplikacji, aby dodać:

* Usługi po stronie serwera.
* Aplikacja do potoku obsługi żądań.

Skrypt `blazor.server.js` ustanawia połączenie klienta. Jest to odpowiedzialność aplikacji do utrwalania i przywracania stanu aplikacji zgodnie z wymaganiami (na przykład w przypadku utraty połączenia sieciowego). Skrypt `blazor.server.js` jest obsługiwany z osadzonego zasobu w ASP.NET Core udostępnionej ramach.

Model Blazor hostingu serwera oferuje kilka korzyści:

* Rozmiar pobierania jest znacznie Blazor mniejszy niż aplikacja WebAssembly, a aplikacja ładuje się znacznie szybciej.
* Aplikacja w pełni wykorzystuje możliwości serwera, w tym korzystanie z dowolnych interfejsów API zgodnych z rdzeniem .NET Core.
* .NET Core na serwerze jest używany do uruchamiania aplikacji, więc istniejące narzędzia .NET, takie jak debugowanie, działa zgodnie z oczekiwaniami.
* Obsługiwane są cienkie klienty. Na przykład Blazor aplikacje serwera działają z przeglądarkami, które nie obsługują webassembly i na urządzeniach o ograniczonym zasobach.
* Baza kodu aplikacji .NET/C#, w tym kod składnika aplikacji, nie jest obsługiwana klientom.

Istnieją wady hostingu Blazor serwera:

* Zwykle występuje większe opóźnienie. Każda interakcja użytkownika obejmuje przeskok sieciowy.
* Nie ma obsługi w trybie offline. Jeśli połączenie klienta nie powiedzie się, aplikacja przestanie działać.
* Skalowalność jest wyzwaniem dla aplikacji z wieloma użytkownikami. Serwer musi zarządzać wieloma połączeniami klientów i obsługiwać stan klienta.
* Do obsługi aplikacji wymagany jest serwer ASP.NET Core. Scenariusze wdrażania bez serwera nie są możliwe (na przykład obsługujących aplikację z sieci CDN).

Model Blazor aplikacji Serwer obsługuje [kontenery platformy Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index). Kliknij prawym przyciskiem myszy projekt w programie Visual Studio i wybierz pozycję **Dodaj** > **obsługę platformy Docker**.

### <a name="comparison-to-server-rendered-ui"></a>Porównanie z interfejsem użytkownika renderowanym przez serwer

Jednym ze Blazor sposobów zrozumienia aplikacji serwera jest zrozumienie, czym różni się od tradycyjnych modeli renderowania interfejsu użytkownika w aplikacjach ASP.NET Core przy użyciu widoków Razor lub stron Razor. Oba modele używają języka Razor do opisywania zawartości HTML, ale znacznie różnią się one w sposobie renderowania znaczników.

Gdy strona lub widok Razor jest renderowany, każdy wiersz kodu Razor emituje HTML w formie tekstowej. Po renderowaniu serwer usuwa stronę lub wystąpienie widoku, w tym dowolny stan, który został wyprodukowany. Gdy wystąpi inne żądanie strony, na przykład gdy sprawdzanie poprawności serwera zakończy się niepowodzeniem i zostanie wyświetlone podsumowanie sprawdzania poprawności:

* Cała strona jest ponownie przekierowana do tekstu HTML.
* Strona jest wysyłana do klienta.

Aplikacja Blazor składa się z elementów wielokrotnego zastosowania interfejsu użytkownika o nazwie *components*. Składnik zawiera kod C#, znaczniki i inne składniki. Podczas renderowania składnika Blazor tworzy wykres dołączonych składników podobnych do modelu obiektu dokumentu HTML lub XML (DOM). Ten wykres zawiera stan składnika przechowywany we właściwościach i polach. Blazorocenia wykres komponentu w celu uzyskania binarnej reprezentacji znaczników. Format binarny może być:

* Przekształcony w tekst HTML (podczas prerenderingu).&dagger;
* Służy do efektywnej aktualizacji znaczników podczas regularnego renderowania.

&dagger;*Prerendering* &ndash; Żądany składnik Razor jest kompilowany na serwerze do statycznego kodu HTML i wysyłany do klienta, gdzie jest renderowany dla użytkownika. Po nawiązyniu połączenia między klientem a serwerem statyczne elementy prerendered składnika są zastępowane elementami interaktywnymi. Prerendering sprawia, że aplikacja czuje się bardziej elastyczna dla użytkownika.

Aktualizacja interfejsu użytkownika Blazor jest wyzwalana przez:

* Interakcja z użytkownikiem, na przykład wybranie przycisku.
* Wyzwalacze aplikacji, takie jak czasomierz.

Wykres jest rerendered i interfejsu *użytkownika różnicy* (różnica) jest obliczany. Ten diff jest najmniejszyzestaw zmian DOM wymagane do aktualizacji interfejsu użytkownika na kliencie. Różnice są wysyłane do klienta w formacie binarnym i stosowane przez przeglądarkę.

Składnik jest usuwany po użytkownik przechodzi od niego na kliencie. Podczas gdy użytkownik wchodzi w interakcję ze składnikiem, stan składnika (usługi, zasoby) musi być przechowywany w pamięci serwera. Ponieważ stan wielu składników może być obsługiwany przez serwer jednocześnie, wyczerpanie pamięci jest problemem, który należy rozwiązać. Aby uzyskać wskazówki Blazor dotyczące tworzenia aplikacji Server w celu <xref:security/blazor/server>zapewnienia najlepszego wykorzystania pamięci serwera, zobacz .

### <a name="circuits"></a>Układy scalone

Aplikacja Blazor Serwer jest zbudowana na [ASP.NET SignalRCore ](xref:signalr/introduction). Każdy klient komunikuje się z SignalR serwerem za każdym lub kilkoma połączeniami zwanymi *obwodem*. Obwód Blazorjest abstrakcja SignalR przez połączenia, które mogą tolerować tymczasowe przerwy w sieci. Gdy Blazor klient zobaczy, że SignalR połączenie jest rozłączone, próbuje ponownie SignalR połączyć się z serwerem przy użyciu nowego połączenia.

Każdy ekran przeglądarki (karta przeglądarki lub iframe), który jest połączony z aplikacją Blazor Serwer, korzysta z SignalR połączenia. Jest to kolejne ważne rozróżnienie w porównaniu do typowych aplikacji renderowanych na serwerze. W aplikacji renderowane na serwerze, otwarcie tej samej aplikacji na wielu ekranach przeglądarki zazwyczaj nie przekłada się na dodatkowe zapotrzebowanie na zasoby na serwerze. W Blazor aplikacji Serwer każdy ekran przeglądarki wymaga oddzielnego obwodu i oddzielnych wystąpień stanu składnika, które mają być zarządzane przez serwer.

Blazoruważa zamknięcie karty przeglądarki lub przejście do zewnętrznego adresu URL z *wdziękiem* zakończenia. W przypadku wdzięcznego zakończenia, obwód i skojarzone zasoby są natychmiast zwalniane. Klient może również odłączyć się bez wdzięku, na przykład z powodu przerwy w sieci. BlazorSerwer przechowuje odłączone obwody dla konfigurowalny interwał, aby umożliwić klientowi ponowne połączenie.

BlazorSerwer umożliwia kod do definiowania *programu obsługi obwodu*, który umożliwia uruchamianie kodu na zmiany stanu obwodu użytkownika. Aby uzyskać więcej informacji, zobacz <xref:blazor/advanced-scenarios#blazor-server-circuit-handler>.

### <a name="ui-latency"></a>Opóźnienie interfejsu użytkownika

Opóźnienie interfejsu użytkownika to czas potrzebny od zainicjowanej akcji do czasu aktualizacji interfejsu użytkownika. Mniejsze wartości opóźnienia interfejsu użytkownika są niezbędne dla aplikacji czuć reaguje na użytkownika. W Blazor aplikacji Server każda akcja jest wysyłana do serwera, przetwarzana, a różnice interfejsu użytkownika są odsyłane. W związku z tym opóźnienie interfejsu użytkownika jest sumą opóźnienia sieci i opóźnienia serwera w przetwarzaniu akcji.

W przypadku aplikacji biznesowej, która jest ograniczona do prywatnej sieci firmowej, wpływ na postrzeganie opóźnienia przez użytkowników z powodu opóźnienia sieci jest zwykle niezauważalny. W przypadku aplikacji wdrożonej przez Internet opóźnienie może stać się zauważalne dla użytkowników, szczególnie jeśli użytkownicy są szeroko rozpowszechnione geograficznie.

Użycie pamięci może również przyczynić się do opóźnienia aplikacji. Zwiększone użycie pamięci powoduje częste wyrzucanie elementów bezużytecznych lub stronicowanie pamięci na dysku, z których oba obniżają wydajność aplikacji i w konsekwencji zwiększają opóźnienie interfejsu użytkownika. Aby uzyskać więcej informacji, zobacz <xref:security/blazor/server>.

BlazorAplikacje serwera powinny być zoptymalizowane, aby zminimalizować opóźnienia interfejsu użytkownika przez zmniejszenie opóźnienia sieci i użycia pamięci. Aby uzyskać podejście do pomiaru <xref:host-and-deploy/blazor/server#measure-network-latency>opóźnienia sieci, zobacz . Aby uzyskać SignalR więcej Blazorinformacji na temat i , zobacz:

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a>Połączenie z serwerem

BlazorAplikacje serwera SignalR wymagają aktywnego połączenia z serwerem. Jeśli połączenie zostanie utracone, aplikacja próbuje ponownie połączyć się z serwerem. Tak długo, jak stan klienta jest nadal w pamięci, sesja klienta wznawia bez utraty stanu.

Aplikacja Blazor serwera prerenders w odpowiedzi na pierwsze żądanie klienta, który konfiguruje stan interfejsu użytkownika na serwerze. Gdy klient próbuje utworzyć SignalR połączenie, klient musi ponownie połączyć się z tym samym serwerem. BlazorAplikacje serwera, które używają więcej niż jednego SignalR serwera wewnętrznej bazy danych, powinny implementować *sesje przyklejone* dla połączeń.

Zalecamy korzystanie z Blazor [usługi Azure SignalR ](/azure/azure-signalr) dla aplikacji serwera. Usługa umożliwia skalowanie aplikacji Blazor serwera do dużej liczby równoczesnych SignalR połączeń. Sesje przyklejone są włączone SignalR dla usługi Azure, `ServerStickyMode` ustawiając wartość `Required`opcji lub konfiguracji usługi na . Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/blazor/server#signalr-configuration>.

Podczas korzystania z usług IIS sesje przyklejone są włączone z routingiem żądań aplikacji. Aby uzyskać więcej informacji, zobacz [Równoważenie obciążenia HTTP przy użyciu routingu żądań aplikacji](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:blazor/get-started>
* <xref:signalr/introduction>
* <xref:blazor/hosting-model-configuration>
* <xref:tutorials/signalr-blazor-webassembly>
