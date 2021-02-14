---
title: ASP.NET Core Blazor modele hostingu
author: guardrex
description: Zrozumienie Blazor WebAssembly i Blazor Server hosting modeli.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/07/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: 780161090d376f08b849fadb47127810d6661d0c
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279754"
---
# <a name="aspnet-core-blazor-hosting-models"></a>ASP.NET Core Blazor modele hostingu

Blazorto platforma internetowa służąca do uruchamiania programu w przeglądarce w programie .NET Runtime [](https://webassembly.org/)( *Blazor WebAssembly* ) lub po stronie serwera w ASP.NET Core ( *Blazor Server* ). Niezależnie od modelu hostingu modele aplikacji i składników *są takie same*.

## Blazor WebAssembly

Podstawowy Blazor model hostingu działa po stronie klienta w przeglądarce w zestawie webassembly. BlazorAplikacja, jej zależności i środowisko uruchomieniowe platformy .NET są pobierane do przeglądarki. Aplikacja jest wykonywana bezpośrednio w wątku interfejsu użytkownika przeglądarki. Aktualizacje interfejsu użytkownika i obsługa zdarzeń są wykonywane w ramach tego samego procesu. Zasoby aplikacji są wdrażane jako pliki statyczne na serwerze sieci Web lub usłudze obsługującej zawartość statyczną dla klientów.

![::: No-Loc (Blazor webassembly):::: w wątku interfejsu użytkownika w przeglądarce zostanie uruchomiona aplikacja No-Loc (Blazor)::: App.](hosting-models/_static/blazor-webassembly.png)

Gdy Blazor WebAssembly aplikacja zostanie utworzona do wdrożenia bez zaplecza ASP.NET Core aplikacji do obsłużynia swoich plików, aplikacja jest nazywana aplikacją *autonomiczną* Blazor WebAssembly . Gdy aplikacja zostanie utworzona na potrzeby wdrożenia z aplikacją zaplecza do obsługi swoich plików, aplikacja jest nazywana *hostowaną* Blazor WebAssembly aplikacją. Aplikacja hostowana Blazor WebAssembly zwykle współdziała z serwerem za pośrednictwem sieci przy użyciu wywołań interfejsu API sieci Web lub [SignalR](xref:signalr/introduction) ( <xref:tutorials/signalr-blazor> ).

`blazor.webassembly.js`Skrypt jest dostarczany przez strukturę i dojścia:

* Pobieranie środowiska uruchomieniowego platformy .NET, aplikacji i zależności aplikacji.
* Inicjowanie środowiska uruchomieniowego w celu uruchomienia aplikacji.

Blazor WebAssemblyModel hostingu oferuje kilka korzyści:

* Nie istnieje zależność po stronie serwera .NET. Aplikacja jest w pełni funkcjonalna po pobraniu jej do klienta.
* Zasoby i możliwości klienta są w pełni wykorzystywane.
* Zadania są Odciążone z serwera do klienta programu.
* Serwer sieci Web ASP.NET Core nie jest wymagany do hostowania aplikacji. Możliwe są scenariusze wdrażania bezserwerowego, takie jak obsługa aplikacji z Content Delivery Network (CDN).

Blazor WebAssemblyModel hostingu ma następujące ograniczenia:

* Aplikacja jest ograniczona do możliwości przeglądarki.
* Wymagany jest sprzęt i oprogramowanie klienta (na przykład obsługa zestawu webassembly).
* Rozmiar pobieranych plików jest większy i ładowanie aplikacji trwa dłużej.
* Środowisko uruchomieniowe platformy .NET i obsługa narzędzi są mniej dojrzałe. Na przykład istnieją ograniczenia dotyczące obsługi [.NET Standard](/dotnet/standard/net-standard) i debugowania.

Aby utworzyć Blazor WebAssembly aplikację, zobacz <xref:blazor/tooling> .

Model hostowanej Blazor aplikacji obsługuje [kontenery platformy Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index). W przypadku obsługi platformy Docker w programie Visual Studio kliknij prawym przyciskiem myszy `Server` projekt rozwiązania hostowanego, Blazor WebAssembly a następnie wybierz polecenie **Dodaj**  >  **obsługę platformy Docker**.

## Blazor Server

W Blazor Server modelu hostingu aplikacja jest wykonywana na serwerze z poziomu aplikacji ASP.NET Core. Aktualizacje interfejsu użytkownika, obsługa zdarzeń i wywołania języka JavaScript są obsługiwane przez [SignalR](xref:signalr/introduction) połączenie.

![Przeglądarka współdziała z aplikacją (hostowaną wewnątrz aplikacji ASP.NET Core) na serwerze za pośrednictwem::: No-Loc (Sygnalizującer):: Connection.](hosting-models/_static/blazor-server.png)

Aplikacja ASP.NET Core odwołuje się `Startup` do klasy aplikacji do dodania:

* Usługi po stronie serwera.
* Aplikacja do potoku obsługi żądania.

Na kliencie `blazor.server.js` skrypt nawiązuje SignalR połączenie z serwerem. Skrypt jest obsługiwany przez aplikację po stronie klienta z osadzonego zasobu w ASP.NET Core udostępnionej platformie. Aplikacja po stronie klienta jest odpowiedzialna za utrwalanie i przywracanie stanu aplikacji zgodnie z potrzebami. 

Blazor ServerModel hostingu oferuje kilka korzyści:

* Rozmiar pobieranych plików jest znacznie mniejszy niż Blazor WebAssembly aplikacja, a aplikacja jest znacznie szybsza.
* Aplikacja w pełni wykorzystuje możliwości serwera, w tym używanie dowolnego zgodnego z platformą .NET Core interfejsów API.
* Program .NET Core na serwerze jest używany do uruchamiania aplikacji, więc istniejące narzędzia platformy .NET, takie jak debugowanie, działają zgodnie z oczekiwaniami.
* Klienci zubożoni są obsługiwani. Na przykład Blazor Server aplikacje pracują z przeglądarkami, które nie obsługują zestawów webassembly i na urządzeniach z ograniczeniami zasobów.
* Baza kodu platformy .NET/C#, w tym kod składnika aplikacji, nie jest obsługiwana dla klientów.

> [!IMPORTANT]
> Blazor ServerAplikacja jest przedrenderowana w odpowiedzi na pierwsze żądanie klienta, co powoduje utworzenie stanu interfejsu użytkownika na serwerze. Gdy klient próbuje utworzyć SignalR połączenie, **Klient musi ponownie nawiązać połączenie z tym samym serwerem**. Blazor Server aplikacje korzystające z więcej niż jednego serwera wewnętrznej bazy danych powinny implementować *sesje usługi Sticky Notes* dla SignalR połączeń. Aby uzyskać więcej informacji, zobacz sekcję [połączenie z serwerem](#connection-to-the-server) .

Blazor ServerModel hostingu ma następujące ograniczenia:

* Wyższe opóźnienia zwykle istnieją. Każda interakcja użytkownika obejmuje przeskok sieci.
* Brak obsługi offline. Jeśli połączenie z klientem zakończy się niepowodzeniem, aplikacja przestanie działać.
* Skalowalność jest wyzwaniem dla aplikacji z wieloma użytkownikami. Serwer musi zarządzać wieloma połączeniami klientów i obsługiwać stan klienta.
* Do obsłużynia aplikacji wymagany jest serwer ASP.NET Core. Scenariusze wdrażania bez użycia serwera nie są możliwe, na przykład w celu obsługi aplikacji z Content Delivery Network (CDN).

Aby utworzyć Blazor Server aplikację, zobacz <xref:blazor/tooling> .

Blazor ServerModel aplikacji obsługuje [kontenery platformy Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index). W przypadku obsługi platformy Docker w programie Visual Studio kliknij prawym przyciskiem myszy projekt w programie Visual Studio i wybierz polecenie **Dodaj**  >  **obsługę platformy Docker**.

### <a name="comparison-to-server-rendered-ui"></a>Porównanie z renderowanym przez serwer interfejsem użytkownika

Jednym ze sposobów zrozumienia Blazor Server aplikacji jest zrozumienie, jak różni się od tradycyjnych modeli na potrzeby renderowania interfejsu użytkownika w aplikacjach ASP.NET Core przy użyciu Razor widoków lub Razor stron. Oba modele używają [ Razor języka](xref:mvc/views/razor) do opisywania zawartości HTML do renderowania, ale znacząco różnią się w *sposobie* renderowania znaczników.

Gdy Razor Strona lub widok jest renderowany, każdy wiersz Razor kodu EMITUJE kod HTML w postaci tekstu. Po wyrenderowaniu serwer usuwa wystąpienie strony lub widoku, w tym dowolny utworzony stan. Gdy występuje inne żądanie dotyczące strony, na przykład w przypadku niepowodzenia walidacji serwera i wyświetlenia podsumowania walidacji:

* Cała strona zostanie ponownie przerenderowana na tekst HTML.
* Strona jest wysyłana do klienta.

BlazorAplikacja składa się z elementów wielokrotnego użytku interfejsu użytkownika o nazwie *Components*. Składnik zawiera kod C#, znacznik i inne składniki. Gdy składnik jest renderowany, program Blazor tworzy wykres dołączonych składników podobny do Document Object Model HTML lub XML (dom). Ten wykres zawiera stan składnika przechowywany w właściwościach i polach. Blazor oblicza wykres składnika, aby utworzyć binarną reprezentację znacznika. Format binarny może:

* Włączono tekst HTML (podczas renderowania prerenderingu &dagger; ).
* Służy do wydajnej aktualizacji znaczników podczas normalnego renderowania.

&dagger;*Renderowanie* wstępnie: żądany Razor składnik jest kompilowany na serwerze do statycznego kodu HTML i wysyłany do klienta, gdzie jest renderowany dla użytkownika. Po nawiązaniu połączenia między klientem a serwerem, statycznie renderowane elementy składnika są zastępowane elementami interaktywnymi. Renderowanie w czasie konwersji sprawia, że aplikacja będzie bardziej odpowiadać użytkownikowi.

Aktualizacja interfejsu użytkownika w programie Blazor jest wyzwalana przez:

* Interakcja z użytkownikiem, na przykład wybranie przycisku.
* Wyzwalacze aplikacji, takie jak czasomierz.

Wykres składnika jest ponownie renderowany i obliczana *jest różnica między interfejsami* użytkownika. Różnica ta jest najmniejszym zestawem zmian modelu DOM wymaganym do zaktualizowania interfejsu użytkownika na kliencie. Różnica jest wysyłana do klienta w formacie binarnym i stosowana przez przeglądarkę.

Składnik jest usuwany po przejściu przez użytkownika na klienta. Gdy użytkownik korzysta ze składnika, stan składnika (usługi, zasoby) musi być przechowywany w pamięci serwera. Ponieważ stan wielu składników może być obsługiwany przez serwer współbieżnie, wyczerpanie pamięci jest problemem, który należy rozwiązać. Aby uzyskać wskazówki dotyczące sposobu tworzenia Blazor Server aplikacji w celu zapewnienia optymalnego wykorzystania pamięci serwera, zobacz <xref:blazor/security/server/threat-mitigation> .

### <a name="circuits"></a>Elektrycznych

Blazor ServerAplikacja została utworzona na podstawie [ASP.NET Core SignalR ](xref:signalr/introduction). Każdy klient komunikuje się z serwerem za pośrednictwem co najmniej jednego SignalR połączenia nazywanego *obwodem*. Obwód jest Blazor abstrakcją dla SignalR połączeń, które mogą tolerować tymczasowe przerwy w sieci. Gdy Blazor klient zobaczy, że SignalR połączenie jest rozłączone, próbuje ponownie nawiązać połączenie z serwerem przy użyciu nowego SignalR połączenia.

Każdy ekran przeglądarki (karta przeglądarki lub iframe), który jest połączony z Blazor Server aplikacją, używa SignalR połączenia. Jest to jeszcze inna ważna różnica w porównaniu z typowymi aplikacjami renderowanymi przez serwer. W aplikacji renderowanej na serwerze otwieranie tej samej aplikacji na wielu ekranach przeglądarki zazwyczaj nie jest przeważnie uwzględniane w dodatkowych wymaganiach dotyczących zasobów na serwerze. W Blazor Server aplikacji każdy ekran przeglądarki wymaga oddzielnego obwodu i oddzielnych wystąpień stanu składnika, które mają być zarządzane przez serwer programu.

Blazor uważa *, że zamyka* kartę przeglądarki lub przechodzenie do zewnętrznego adresu URL. W przypadku bezpiecznego zakończenia obwód i skojarzone zasoby są natychmiast uwalniane. Klient może również odłączyć się niebezpiecznie, na przykład z powodu przerwy w działaniu sieci. Blazor Server przechowuje połączone obwody przez konfigurowalne interwały, aby umożliwić klientowi ponowne nawiązanie połączenia.

Blazor Server umożliwia kodowi Definiowanie *procedury obsługi obwodu*, która umożliwia uruchamianie kodu na zmiany stanu obwodu użytkownika. Aby uzyskać więcej informacji, zobacz <xref:blazor/advanced-scenarios#blazor-server-circuit-handler>.

### <a name="ui-latency"></a>Opóźnienie interfejsu użytkownika

Opóźnienie interfejsu użytkownika to czas od zainicjowanej akcji do momentu zaktualizowania interfejsu użytkownika. Mniejsze wartości opóźnienia interfejsu użytkownika są bezwzględnie konieczne, aby aplikacja mogła reagować na użytkownika. W Blazor Server aplikacji każda akcja jest wysyłana do serwera, przetwarzane i różnic interfejsu użytkownika jest wysyłana z powrotem. W związku z tym opóźnienia interfejsu użytkownika to suma opóźnień sieci i opóźnienia serwera w przetwarzaniu akcji.

W przypadku aplikacji biznesowej ograniczonej do prywatnej sieci firmowej wpływ na postrzeganie opóźnień przez użytkownika z powodu opóźnienia sieci jest zwykle niezauważalny. W przypadku aplikacji wdrożonej za pośrednictwem Internetu opóźnienie może być zauważalne dla użytkowników, szczególnie w przypadku, gdy użytkownicy są szeroko rozproszona geograficznie.

Użycie pamięci może również przyczynić się do opóźnienia aplikacji. Zwiększone użycie pamięci powoduje częste zbieranie elementów bezużytecznych lub stronicowanie pamięci na dysku, co zmniejsza wydajność aplikacji i w związku z tym zwiększa opóźnienia interfejsu użytkownika.

Blazor Server aplikacje powinny być zoptymalizowane w celu zminimalizowania opóźnień interfejsu użytkownika przez zmniejszenie opóźnienia sieci i użycie pamięci. Aby uzyskać podejście do mierzenia opóźnień sieci, zobacz <xref:blazor/host-and-deploy/server#measure-network-latency> . Aby uzyskać więcej informacji na temat SignalR i Blazor , zobacz:

* <xref:blazor/host-and-deploy/server>
* <xref:blazor/security/server/threat-mitigation>

### <a name="connection-to-the-server"></a>Połączenie z serwerem

Blazor Server aplikacje wymagają aktywnego SignalR połączenia z serwerem. Jeśli połączenie zostanie utracone, aplikacja spróbuje ponownie nawiązać połączenie z serwerem. O ile stan klienta pozostaje w pamięci serwera, sesja klienta zostaje wznowiona bez utraty stanu.

Blazor ServerAplikacja jest przedrenderowana w odpowiedzi na pierwsze żądanie klienta, co powoduje utworzenie stanu interfejsu użytkownika na serwerze. Gdy klient próbuje utworzyć SignalR połączenie, klient musi ponownie nawiązać połączenie z tym samym serwerem. Blazor Server aplikacje korzystające z więcej niż jednego serwera wewnętrznej bazy danych powinny implementować *sesje usługi Sticky Notes* dla SignalR połączeń.

Zalecamy korzystanie z [ SignalR usługi platformy Azure](/azure/azure-signalr) dla Blazor Server aplikacji. Usługa umożliwia skalowanie Blazor Server aplikacji w górę do dużej liczby jednoczesnych SignalR połączeń. Sesje programu Sticky Notes są włączone dla usługi platformy Azure SignalR , ustawiając `ServerStickyMode` opcję usługi lub wartość konfiguracji na `Required` . Aby uzyskać więcej informacji, zobacz <xref:blazor/host-and-deploy/server#signalr-configuration>.

W przypadku korzystania z usług IIS sesje programu Sticky są włączane przy użyciu *routingu żądań aplikacji*. Aby uzyskać więcej informacji, zobacz [równoważenie obciążenia HTTP przy użyciu routingu żądań aplikacji](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:blazor/tooling>
* <xref:signalr/introduction>
* <xref:blazor/fundamentals/signalr>
* <xref:tutorials/signalr-blazor>
