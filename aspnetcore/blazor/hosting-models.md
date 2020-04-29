---
title: ASP.NET Core Blazor modele hostingu
author: guardrex
description: Poznaj Blazor modele hostingu i Blazor serwera webassembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: 48f5b09199091b2b55974010a2b0715c28eb1bae
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205972"
---
# <a name="aspnet-core-blazor-hosting-models"></a>ASP.NET Core Blazor modele hostingu

Autor [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazorjest platformą internetową, która umożliwia uruchamianie po stronie klienta w przeglądarce na podstawie [zestawu](https://webassembly.org/)weburuchomieniowego platformy .NET (* Blazor webassembly*) lub po stronie serwera w ASP.NET Core (* Blazor serwer*). Niezależnie od modelu hostingu modele aplikacji i składników *są takie same*.

Aby utworzyć projekt dla modeli hostingu opisanych w tym artykule, zobacz <xref:blazor/get-started>.

Aby uzyskać konfigurację zaawansowaną, zobacz <xref:blazor/hosting-model-configuration>.

## <a name="blazor-webassembly"></a>BlazorZestaw webassembly

Główny model hostingu dla programu Blazor działa po stronie klienta w przeglądarce w zestawie webassembly. Blazor Aplikacja, jej zależności i środowisko uruchomieniowe platformy .NET są pobierane do przeglądarki. Aplikacja jest wykonywana bezpośrednio w wątku interfejsu użytkownika przeglądarki. Aktualizacje interfejsu użytkownika i obsługa zdarzeń są wykonywane w ramach tego samego procesu. Zasoby aplikacji są wdrażane jako pliki statyczne na serwerze sieci Web lub usłudze obsługującej zawartość statyczną dla klientów.

![BlazorWebassembly: Blazor aplikacja jest uruchamiana w wątku interfejsu użytkownika w przeglądarce.](hosting-models/_static/blazor-webassembly.png)

Aby utworzyć Blazor aplikację przy użyciu modelu hostingu po stronie klienta, użyj ** Blazor szablonu aplikacji webassembly** ([dotnet New blazorwasm](/dotnet/core/tools/dotnet-new)).

Po wybraniu ** Blazor szablonu aplikacji webassembly** można skonfigurować aplikację do korzystania z zaplecza ASP.NET Core, zaznaczając pole wyboru **hostowane ASP.NET Core** ([Nowy blazorwasm--Hosted](/dotnet/core/tools/dotnet-new)). Aplikacja ASP.NET Core obsługuje Blazor klientów. Aplikacja webassembly może współdziałać z serwerem za pośrednictwem sieci przy użyciu wywołań interfejsu [SignalR](xref:signalr/introduction) API<xref:tutorials/signalr-blazor-webassembly>sieci Web lub (). Blazor

Szablony obejmują `blazor.webassembly.js` skrypt, który obsługuje:

* Pobieranie środowiska uruchomieniowego platformy .NET, aplikacji i zależności aplikacji.
* Inicjowanie środowiska uruchomieniowego w celu uruchomienia aplikacji.

Blazor Model hostingu zestawu webassembly oferuje kilka korzyści:

* Nie istnieje zależność po stronie serwera .NET. Aplikacja jest w pełni funkcjonalna po pobraniu jej do klienta.
* Zasoby i możliwości klienta są w pełni wykorzystywane.
* Zadania są Odciążone z serwera do klienta programu.
* Serwer sieci Web ASP.NET Core nie jest wymagany do hostowania aplikacji. Możliwe są scenariusze wdrażania bezserwerowego (na przykład obsługujące aplikację z sieci CDN).

Istnieją Downsides do Blazor hostingu zestawu webassembly:

* Aplikacja jest ograniczona do możliwości przeglądarki.
* Wymagany jest sprzęt i oprogramowanie klienta (na przykład obsługa zestawu webassembly).
* Rozmiar pobieranych plików jest większy i ładowanie aplikacji trwa dłużej.
* Środowisko uruchomieniowe platformy .NET i obsługa narzędzi są mniej dojrzałe. Na przykład istnieją ograniczenia dotyczące obsługi [.NET Standard](/dotnet/standard/net-standard) i debugowania.

Model Blazor hostowanej aplikacji obsługuje [kontenery platformy Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index). Kliknij prawym przyciskiem myszy projekt serwera w programie Visual Studio i wybierz polecenie **Dodaj** > **obsługę platformy Docker**.

## <a name="blazor-server"></a>BlazorServer

W modelu Blazor hostingu serwera aplikacja jest wykonywana na serwerze z poziomu aplikacji ASP.NET Core. Aktualizacje interfejsu użytkownika, obsługa zdarzeń i wywołania języka JavaScript są obsługiwane przez [SignalR](xref:signalr/introduction) połączenie.

![Przeglądarka współdziała z aplikacją (hostowaną wewnątrz aplikacji ASP.NET Core) na serwerze za pośrednictwem SignalR połączenia.](hosting-models/_static/blazor-server.png)

Aby utworzyć Blazor aplikację przy użyciu modelu Blazor hostingu serwera, użyj szablonu aplikacji ASP.NET Core ** Blazor Server** ([dotnet New blazorserver](/dotnet/core/tools/dotnet-new)). Aplikacja ASP.NET Core obsługuje aplikację Blazor serwera i tworzy punkt końcowy, SignalR w którym klienci nawiązują połączenie.

Aplikacja ASP.NET Core odwołuje się do `Startup` klasy aplikacji do dodania:

* Usługi po stronie serwera.
* Aplikacja do potoku obsługi żądania.

`blazor.server.js` Skrypt ustanawia połączenie z klientem. Jest on odpowiedzialny za utrzymanie i przywrócenie stanu aplikacji zgodnie z wymaganiami (na przykład w przypadku utraconego połączenia sieciowego). `blazor.server.js` Skrypt jest obsługiwany z zasobów osadzonych w ASP.NET Core udostępnionej platformie.

Model Blazor hostingu serwera oferuje kilka korzyści:

* Rozmiar pobieranych plików jest znacznie mniejszy Blazor niż aplikacja webassembly, a aplikacja jest znacznie szybsza.
* Aplikacja w pełni wykorzystuje możliwości serwera, w tym używanie dowolnego zgodnego z platformą .NET Core interfejsów API.
* Program .NET Core na serwerze jest używany do uruchamiania aplikacji, więc istniejące narzędzia platformy .NET, takie jak debugowanie, działają zgodnie z oczekiwaniami.
* Klienci zubożoni są obsługiwani. Na przykład aplikacje Blazor serwerowe współpracują z przeglądarkami, które nie obsługują zestawów webassembly i na urządzeniach z ograniczeniami zasobów.
* Baza kodu platformy .NET/C#, w tym kod składnika aplikacji, nie jest obsługiwana dla klientów.

Downsides do Blazor hostingu serwera:

* Wyższe opóźnienia zwykle istnieją. Każda interakcja użytkownika obejmuje przeskok sieci.
* Brak obsługi offline. Jeśli połączenie z klientem zakończy się niepowodzeniem, aplikacja przestanie działać.
* Skalowalność jest wyzwaniem dla aplikacji z wieloma użytkownikami. Serwer musi zarządzać wieloma połączeniami klientów i obsługiwać stan klienta.
* Do obsłużynia aplikacji wymagany jest serwer ASP.NET Core. Scenariusze wdrażania bez użycia serwera nie są możliwe (na przykład w celu obsługi aplikacji z sieci CDN).

Model Blazor aplikacji serwera obsługuje [kontenery platformy Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index). Kliknij prawym przyciskiem myszy projekt w programie Visual Studio i wybierz polecenie **Dodaj** > **obsługę platformy Docker**.

### <a name="comparison-to-server-rendered-ui"></a>Porównanie z renderowanym przez serwer interfejsem użytkownika

Jednym ze sposobów zrozumienia Blazor aplikacji serwerowych jest zrozumienie, jak różni się od tradycyjnych modeli służących do renderowania interfejsu użytkownika w aplikacjach ASP.NET Core przy użyciu widoków Razor lub Razor Pages. Oba modele używają języka Razor do opisywania zawartości HTML, ale znacząco różnią się sposobem renderowania znaczników.

Gdy strona Razor lub widok jest renderowany, każdy wiersz kodu Razor emituje kod HTML w postaci tekstowej. Po wyrenderowaniu serwer usuwa wystąpienie strony lub widoku, w tym dowolny utworzony stan. Gdy występuje inne żądanie dotyczące strony, na przykład w przypadku niepowodzenia walidacji serwera i wyświetlenia podsumowania walidacji:

* Cała strona zostanie ponownie przerenderowana na tekst HTML.
* Strona jest wysyłana do klienta.

Blazor Aplikacja składa się z elementów wielokrotnego użytku interfejsu użytkownika o nazwie *Components*. Składnik zawiera kod C#, znacznik i inne składniki. Gdy składnik jest renderowany, program Blazor tworzy wykres dołączonych składników podobny do Document Object Model HTML lub XML (dom). Ten wykres zawiera stan składnika przechowywany w właściwościach i polach. Blazoroblicza wykres składnika, aby utworzyć binarną reprezentację znacznika. Format binarny może:

* Włączono tekst HTML (podczas renderowania prerenderingu&dagger;).
* Służy do wydajnej aktualizacji znaczników podczas normalnego renderowania.

&dagger;Przed *renderowaniem* &ndash; żądany składnik Razor jest kompilowany na serwerze do statycznego kodu HTML i wysyłany do klienta, gdzie jest renderowany dla użytkownika. Po nawiązaniu połączenia między klientem a serwerem, statycznie renderowane elementy składnika są zastępowane elementami interaktywnymi. Renderowanie w czasie konwersji sprawia, że aplikacja będzie bardziej odpowiadać użytkownikowi.

Aktualizacja interfejsu użytkownika w Blazor programie jest wyzwalana przez:

* Interakcja z użytkownikiem, na przykład wybranie przycisku.
* Wyzwalacze aplikacji, takie jak czasomierz.

Wykres jest ponownie renderowany i obliczana *jest różnica między interfejsami* użytkownika. Różnica ta jest najmniejszym zestawem zmian modelu DOM wymaganym do zaktualizowania interfejsu użytkownika na kliencie. Różnica jest wysyłana do klienta w formacie binarnym i stosowana przez przeglądarkę.

Składnik jest usuwany po przejściu przez użytkownika na klienta. Gdy użytkownik korzysta ze składnika, stan składnika (usługi, zasoby) musi być przechowywany w pamięci serwera. Ponieważ stan wielu składników może być obsługiwany przez serwer współbieżnie, wyczerpanie pamięci jest problemem, który należy rozwiązać. Aby uzyskać wskazówki dotyczące sposobu tworzenia aplikacji Blazor serwera w celu zapewnienia optymalnego wykorzystania pamięci serwera, zobacz <xref:security/blazor/server/threat-mitigation>.

### <a name="circuits"></a>Elektrycznych

Aplikacja Blazor serwera jest oparta na [ASP.NET Core SignalR ](xref:signalr/introduction). Każdy klient komunikuje się z serwerem za pośrednictwem co SignalR najmniej jednego połączenia nazywanego *obwodem*. Obwód jest Blazorabstrakcją dla połączeń, SignalR które mogą tolerować tymczasowe przerwy w sieci. Gdy Blazor klient zobaczy, że SignalR połączenie jest rozłączone, próbuje ponownie nawiązać połączenie z serwerem przy użyciu nowego SignalR połączenia.

Każdy ekran przeglądarki (karta przeglądarki lub iframe), który jest połączony z Blazor aplikacją serwera, używa SignalR połączenia. Jest to jeszcze inna ważna różnica w porównaniu z typowymi aplikacjami renderowanymi przez serwer. W aplikacji renderowanej na serwerze otwieranie tej samej aplikacji na wielu ekranach przeglądarki zazwyczaj nie jest przeważnie uwzględniane w dodatkowych wymaganiach dotyczących zasobów na serwerze. W aplikacji Blazor serwera każdy ekran przeglądarki wymaga oddzielnego obwodu i oddzielnych wystąpień stanu składnika, które mają być zarządzane przez serwer.

Blazoruważa *, że zamyka* kartę przeglądarki lub przechodzenie do zewnętrznego adresu URL. W przypadku bezpiecznego zakończenia obwód i skojarzone zasoby są natychmiast uwalniane. Klient może również odłączyć się niebezpiecznie, na przykład z powodu przerwy w działaniu sieci. BlazorSerwer przechowuje rozłączone obwody przez konfigurowalny interwał, aby umożliwić klientowi ponowne nawiązanie połączenia.

BlazorSerwer umożliwia kodowi Definiowanie *procedury obsługi obwodu*, która umożliwia uruchamianie kodu na zmiany stanu obwodu użytkownika. Aby uzyskać więcej informacji, zobacz <xref:blazor/advanced-scenarios#blazor-server-circuit-handler>.

### <a name="ui-latency"></a>Opóźnienie interfejsu użytkownika

Opóźnienie interfejsu użytkownika to czas od zainicjowanej akcji do momentu zaktualizowania interfejsu użytkownika. Mniejsze wartości opóźnienia interfejsu użytkownika są bezwzględnie konieczne, aby aplikacja mogła reagować na użytkownika. W aplikacji Blazor serwera każda akcja jest wysyłana do serwera, przetwarzane i różnic interfejsu użytkownika jest wysyłana z powrotem. W związku z tym opóźnienia interfejsu użytkownika to suma opóźnień sieci i opóźnienia serwera w przetwarzaniu akcji.

W przypadku aplikacji biznesowych, która jest ograniczona do prywatnej sieci firmowej, wpływ na postrzeganie opóźnień przez użytkownika z powodu opóźnienia sieci jest zwykle niezauważalny. W przypadku aplikacji wdrożonej za pośrednictwem Internetu opóźnienie może być zauważalne dla użytkowników, szczególnie w przypadku, gdy użytkownicy są szeroko rozproszona geograficznie.

Użycie pamięci może również przyczynić się do opóźnienia aplikacji. Zwiększone użycie pamięci powoduje częste zbieranie elementów bezużytecznych lub stronicowanie pamięci na dysku, co zmniejsza wydajność aplikacji i w związku z tym zwiększa opóźnienia interfejsu użytkownika.

BlazorAplikacje serwera powinny być zoptymalizowane pod kątem zminimalizowania opóźnień interfejsu użytkownika przez zmniejszenie opóźnienia sieci i użycie pamięci. Aby uzyskać podejście do mierzenia opóźnień sieci, <xref:host-and-deploy/blazor/server#measure-network-latency>Zobacz. Aby uzyskać więcej informacji SignalR na Blazortemat i, zobacz:

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server/threat-mitigation>

### <a name="connection-to-the-server"></a>Połączenie z serwerem

BlazorAplikacje serwera wymagają aktywnego SignalR połączenia z serwerem. Jeśli połączenie zostanie utracone, aplikacja spróbuje ponownie nawiązać połączenie z serwerem. O ile stan klienta nadal znajduje się w pamięci, sesja klienta zostaje wznowiona bez utraty stanu.

Aplikacja Blazor serwera jest przedstawiona w odpowiedzi na pierwsze żądanie klienta, która konfiguruje stan interfejsu użytkownika na serwerze. Gdy klient próbuje utworzyć SignalR połączenie, klient musi ponownie nawiązać połączenie z tym samym serwerem. BlazorAplikacje serwera, które używają więcej niż jednego serwera wewnętrznej bazy danych, powinny SignalR implementować *sesje usługi Sticky Notes* dla połączeń.

Zalecamy korzystanie z [usługi platformy SignalR Azure](/azure/azure-signalr) dla Blazor aplikacji serwerowych. Usługa umożliwia skalowanie aplikacji Blazor serwera do dużej liczby jednoczesnych SignalR połączeń. Sesje programu Sticky Notes są włączone SignalR dla usługi platformy Azure, ustawiając `ServerStickyMode` opcję usługi lub wartość konfiguracji `Required`na. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/blazor/server#signalr-configuration>.

W przypadku korzystania z usług IIS sesje programu Sticky są włączane przy użyciu routingu żądań aplikacji. Aby uzyskać więcej informacji, zobacz [równoważenie obciążenia HTTP przy użyciu routingu żądań aplikacji](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:blazor/get-started>
* <xref:signalr/introduction>
* <xref:blazor/hosting-model-configuration>
* <xref:tutorials/signalr-blazor-webassembly>
